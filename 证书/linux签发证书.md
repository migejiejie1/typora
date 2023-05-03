**Linux签发https证书**

```css
样例:
使用者1
CN = baidu.com 
O = Beijing Baidu Netcom Science Technology Co. (北京百度网通科技有限公司)
OU = service operation department (服务操作部门)
L = beijing
S = beijing
C = CN

使用者2
CN = *.163.com
O = NetEase (Hangzhou) Network Co., Ltd (网易(杭州)网络有限公司)
L = hangzhou
S = zhejiang
C = CN

颁发者1
CN = GlobalSign Organization Validation CA - SHA256 - G2 (全球签名组织验证CA)
O = GlobalSign nv-sa
C = BE

颁发者2
CN = GeoTrust RSA CN CA G2
O = DigiCert Inc
C = US

自定义:
颁发者
CN=China Sign Organization Validation CA (中国签名组织验证CA) 公用名称 (Common Name)| 简称：CN 字段, 一般为网站域名
O=ChinaSign 单位名称 (Organization Name)| 简称：O 字段
C=CN 所在国家 (Country)| 简称：C 字段

使用者
CN=*.mige.com 公用名称 (Common Name)| 简称：CN 字段, 一般为网站域名
OU=service operation department 显示其他内容| 简称:OU 字段
O=Beijing Mige Science Technology Co. 单位名称 (Organization Name)|	简称：O 字段
L=Beijing 所在城市 (Locality)| 简称：L 字段 
ST=Beijing 所在省份 (State/Provice)| 简称：S 字段 
C=CN 所在国家 (Country)| 简称：C 字段
```

1. 生成根证书私钥和根证书 (证书颁发者或者签发机构) 

```shell
使用指定 -subj "/C=CN/ST=MyProvince/L=MyCity/O=MyOrganization", 生成根证书私钥和根证书 -keyout CA-private.key -out CA-certificate.crt
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -subj "/C=CN/O=ChinaSign/CN=China Sign Organization Validation CA" -keyout CA.key -out CA.crt -reqexts v3_req -extensions v3_ca
```

2. 生成自签名证书私钥 -out private.key

```shell
openssl genrsa -out private.key 2048
```

3. 根据自签名证书私钥生成自签名证书申请文件 -out private.csr

```shell
openssl req -new -key private.key -subj "/C=CN/ST=Beijing/L=Beijing/O=Beijing Mige Science Technology Co./OU=service operation department/CN=*.mige.com" -sha256 -out private.csr
```

4. 定义自签名证书扩展文件(解决chrome安全告警)。

在默认情况下生成的证书一旦选择信任，在 Edge, Firefox 等浏览器都显示为安全，但是Chrome仍然会标记为不安全并警告拦截，
这是因为 Chrome 需要证书支持扩展 Subject Alternative Name(主体别名), 因此生成时需要特别指定 SAN 扩展并添加相关参数, 将下述内容放到一个文件中, 命名为 private.ext

```shell
vim private.ext
##############################private.ext##############################
[ req ]
default_bits        = 1024
distinguished_name  = req_distinguished_name
req_extensions      = san
extensions          = san
[ req_distinguished_name ]
countryName         = CN
stateOrProvinceName = Beijing
localityName        = Beijing
organizationName    = Beijing Mige Science Technology Co.
[SAN]
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names
[alt_names]
DNS.1 = *.mige.com
IP.1 = 1.1.1.1
##############################private.ext##############################
```

5. 根据根证书私钥及根证书 -CA CA.crt -CAkey CA.key、自签名证书申请文件 -in private.csr、自签名证书扩展文件 -extfile private.ext, 生成自签名证书 -out private.crt

```shell
openssl x509 -req -days 3650 -in private.csr -CA CA.crt -CAkey CA.key -CAcreateserial -sha256 -out private.crt -extfile private.ext -extensions SAN
```

6. 将证书配置到nginx配置文件的http-server模块里，类似：

```css
server {
    listen 443 ssl; 
    server_name localhost; 
    root /usr/share/nginx/html; 
    index index.html;
    ssl_certificate  /etc/nginx/cert/server.crt;
    ssl_certificate_key /etc/nginx/cert/server.key;
	ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    location / {
	    root   html;
        index index.html index.htm;
    }
}
server {
    listen 80;
    server_name localhost;
    rewrite ^(.*)$ https://$host:443$1 permanent;
}
```

7. 把CA证书 CA.crt 安装到受信任的根证书颁发机构下，即可从谷歌浏览器正常访问 https 的对应网址且不会报不安全警告。
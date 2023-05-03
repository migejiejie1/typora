```shell
集群安全性
官方文档:
https://www.elastic.co/guide/en/elasticsearch/reference/current/configuring-stack-security.html

SSL：（Secure Socket Layer，安全套接字层），为Netscape所研发，用以保障在Internet上数据传输之安全，利用数据加密(Encryption)技术，可确保数据在网络上之传输过程中不会被截取。当前版本为3.0。它已被广泛地用于Web浏览器与服务器之间的身份认证和加密数据传输。
SSL协议位于TCP/IP协议与各种应用层协议之间，为数据通讯提供安全支持。SSL协议可分为两层： SSL记录协议（SSL Record Protocol）：它建立在可靠的传输协议（如TCP）之上，为高层协议提供数据封装、压缩、加密等基本功能的支持。 SSL握手协议（SSL Handshake Protocol）：它建立在SSL记录协议之上，用于在实际的数据传输开始前，通讯双方进行身份认证、协商加密算法、交换加密密钥等。
TLS：(Transport Layer Security，传输层安全协议)，用于两个应用程序之间提供保密性和数据完整性。
TLS 1.0是IETF（Internet Engineering Task Force，Internet工程任务组）制定的一种新的协议，它建立在SSL 3.0协议规范之上，是SSL 3.0的后续版本，可以理解为SSL 3.1，它是写入了 RFC 的。该协议由两层组成： TLS 记录协议（TLS Record）和 TLS 握手协议（TLS Handshake）。较低的层为 TLS 记录协议，位于某个可靠的传输协议（例如 TCP）上面。

为Elastic stack配置安全性
安全需求各不相同，具体取决于您是在笔记本电脑上进行本地开发，还是在生产环境中保护所有通信。

1 最低安全性（Elasticsearch Development）
如果您想在笔记本电脑上设置 Elasticsearch 并开始开发，此方案适合您。此配置通过为内置用户设置密码来防止对本地群集进行未经授权的访问。您还可以为 Kibana 配置密码身份验证。

最低安全方案对于生产模式群集是不够的。如果群集具有多个节点，则必须启用最低安全性，然后在节点之间配置传输层安全性 （TLS）。
设置最低安全性:
	为 Elasticsearch 设置最低安全性编辑
您可以启用 Elasticsearch 安全功能，然后为内置用户创建密码。您可以稍后添加更多用户，但使用内置用户可简化为群集启用安全性的过程。

先决条件:
安装并配置 Elasticsearch 和 Kibana。

基本许可证包括 Elastic Stack 的最小安全设置，因此您只需下载发行版即可开始工作。您还可以启用免费试用许可证以访问 Elastic Stack 的所有功能。

启用 Elasticsearch 安全功能
当您使用基本许可证时，默认情况下会禁用 Elasticsearch 安全功能。启用 Elasticsearch 安全功能可启用基本身份验证，以便您可以使用用户名和密码身份验证运行本地集群。

1.1. 在集群中的每个节点上，如果 Kibana 和 Elasticsearch 正在运行，请同时停止它们。
1.2. 在群集中的每个节点上，将设置添加到文件中，并将值设置为 ：

xpack.security.enabled: true

1.3. 如果群集具有单个节点，请在文件中添加设置并将值设置为 single-node。此设置可确保节点不会无意中连接到可能在网络上运行的其他群集。

discovery.type: single-node

为内置用户创建密码
要与群集通信，您必须为内置用户配置用户名。除非启用匿名访问，否则将拒绝所有不包含用户名和密码的请求。

您只需要在启用最低或基本安全性时为 elastic 和 kibana_system用户设置密码。
1.1. 在集群中的每个节点上，启动 Elasticsearch。
./bin/elasticsearch
1.2. 在另一个终端窗口中，通过运行 elasticsearch-setup-passwords 实用程序为内置用户设置密码。

您可以针对群集中的任何节点运行该实用程序。但是，您只应为整个集群运行一次此实用程序。
elasticsearch-setup-passwords auto 使用该参数将随机生成的密码输出到控制台，您可以稍后根据需要更改这些密码：

./bin/elasticsearch-setup-passwords auto
如果要使用自己的密码，请使用参数而不是参数运行命令。使用此模式将逐步完成所有内置用户的密码配置。interactive

./bin/elasticsearch-setup-passwords interactive

1.3. 保存生成的密码。您需要它们来将内置用户添加到 Kibana。

为用户设置密码后，无法再次运行该命令。elasticelasticsearch-setup-passwords

配置 Kibana 以使用密码连接到 Elasticsearch
启用 Elasticsearch 安全功能后，用户必须使用有效的用户名和密码登录Kibana。
您将配置 Kibana 以使用您之前创建的内置用户和密码。Kibana 执行一些需要用户使用的后台任务。kibana_system 
kibana_system 此帐户不适用于个人用户，并且没有从浏览器登录 Kibana 的权限。相反，您将以超级用户 elastic 身份登录到 Kibana。 elastic
1.1 将 elasticsearch.username 设置添加到文件中，并将值设置为用户：kibana_system  KIB_PATH_CONF/kibana.yml 
elasticsearch.username: "kibana_system"

# 由于 kibana5.* 6.* 官方并没有支持中文，需要另外下载补丁包: https://github.com/anbai-inc/Kibana_Hanization
# kibana 7 中官方加入了中文的选项, 只需要在配置文件 kibana.yml 中加入配置
i18n.locale: "zh-CN"

1.2 在安装 Kibana 的目录中，运行以下命令以创建 Kibana 密钥库并添加安全设置：
1.2.1 创建 Kibana 密钥库：

./bin/kibana-keystore create
1.2.2 将用户的密码添加到 Kibana 密钥库：kibana_system

./bin/kibana-keystore add elasticsearch.password
出现提示时，输入用户的密码。kibana_system

1.3 重新启动 Kibana。

./bin/kibana
1.4 以 elastic 用户身份登录到 Kibana。使用此超级用户帐户可以管理空间、创建新用户和分配角色。如果您在本地运行 Kibana，请转到 http://localhost:5601 查看登录页面。

下一步是什么？
祝贺！您为本地群集启用了密码保护，以防止未经授权的访问。您可以以 elastic用户身份安全地登录到 Kibana，并创建其他用户和角色。如果您正在运行单节点群集，则可以在此处停止。elastic

如果群集具有多个节点，则必须在节点之间配置传输层安全性 （TLS）。如果不启用 TLS，则生产模式群集将不会启动。

为Elastic stack设置基本安全性，以保护集群中节点之间的所有内部通信。


2. 基本安全性（Elasticsearch Production）
此方案基于最低安全要求构建，为节点之间的通信添加传输层安全性 （TLS）。此附加层要求节点验证安全证书，以防止未经授权的节点加入您的 Elasticsearch 集群。

Elasticsearch 和 Kibana 之间的外部 HTTP 流量不会加密，但节点间通信将受到保护。

为Elastic stack设置基本安全性编辑
在最小安全配置中添加密码保护后，您需要配置传输层安全性 （TLS）。传输层处理群集中节点之间的所有内部通信。

如果群集具有多个节点，则必须在节点之间配置 TLS。如果不启用 TLS，则生产模式群集将不会启动。

传输层依赖于相互 TLS 对节点进行加密和身份验证。正确应用 TLS 可确保恶意节点无法加入群集并与其他节点交换数据。虽然在 HTTP 层实现用户名和密码身份验证对于保护本地群集很有用，但节点之间通信的安全性需要 TLS。
在节点之间配置 TLS 是防止未经授权的节点访问群集的基本安全设置。

了解传输上下文

传输层安全性 （TLS） 是用于将安全控制（如加密）应用于网络通信的行业标准协议的名称。TLS 是过去称为安全套接字层 （SSL） 的现代名称。Elasticsearch 文档可互换使用术语 TLS 和 SSL。

传输协议是 Elasticsearch 节点用于相互通信的协议的名称。此名称特定于 Elasticsearch，并将传输端口（默认9300）与 HTTP 端口（默认9200）区分开来。节点使用传输端口相互通信，REST 客户端使用 HTTP 端口与 Elasticsearch 通信。9300 9200

虽然"运输"这个词出现在两个上下文中，但它们的含义不同。可以将 TLS 同时应用于 Elasticsearch 传输端口和 HTTP 端口。我们知道这些重叠的术语可能会令人困惑，因此为了澄清，在这种情况下，我们将TLS应用于Elasticsearch传输端口。在下一个场景中，我们将 TLS 应用于 Elasticsearch HTTP 端口。

先决条件
完成 Elastic Stack 的最小安全性中的步骤，以便在集群中的每个节点上启用 Elasticsearch 安全功能。然后，您可以使用 TLS 加密节点之间的通信。

您只需为整个群集的内置用户创建一次密码。

生成证书颁发机构
您可以在群集中添加任意数量的节点，但它们必须能够相互通信。群集中节点之间的通信由传输模块处理。若要保护群集，必须确保对节点间通信进行加密和验证，这是通过相互 TLS 实现的。

在安全集群中，Elasticsearch 节点在与其他节点通信时使用证书来标识自己。

群集必须验证这些证书的真实性。建议的方法是信任特定的证书颁发机构 （CA）。将节点添加到群集时，它们必须使用由同一 CA 签名的证书。
对于传输层，我们建议使用单独的专用 CA，而不是现有的、可能共享的 CA，以便严格控制节点成员身份。使用该工具为您的集群生成 CA。elasticsearch-certutil

2.1 在任何单个节点上，使用该工具为群集生成 CA。elasticsearch-certutil

./bin/elasticsearch-certutil ca
2.1.1 出现提示时，接受缺省文件名，即 elastic-stack-ca.p12。此文件包含 CA 的公共证书和用于签署每个节点的证书的私钥。elastic-stack-ca.p12
2.1.2 输入 CA 的密码。如果未部署到生产环境，则可以选择将密码留空。

2.2 在任何单个节点上，为群集中的节点生成证书和私钥。包括您在上一步中生成的输出文件。elastic-stack-ca.p12
./bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12

--ca <ca_file>
用于对证书进行签名的 CA 文件的名称。该工具的缺省文件名为 。elasticsearch-certutil elastic-stack-ca.p12
2.2.1 输入 CA 的密码，如果未在上一步中配置密码，请按Enter。
2.2.2 为证书创建密码并接受默认文件名。
输出文件是名为 elastic-certificates.p12的密钥库。此文件包含节点证书、节点密钥和 CA 证书。elastic-certificates.p12

2.3 在群集中的每个节点上，将文件复制到目录中。elastic-certificates.p12 $ES_PATH_CONF


使用 TLS 加密节点间通信
传输网络层用于群集中节点之间的内部通信。启用安全功能后，必须使用 TLS 来确保节点之间的通信已加密。

现在，您已经生成了证书颁发机构和证书，接下来您将更新集群以使用这些文件。

Elasticsearch 会监控配置为 TLS 相关节点设置值的所有文件，例如证书、密钥库、密钥库或信任库。
如果您更新了这些文件中的任何一个，例如当您的主机名更改或证书即将过期时，Elasticsearch 会重新加载它们。
这些文件以全局 Elasticsearch 设置确定的频率轮询更改，该设置默认为 5 秒。resource.reload.interval.high

为群集中的每个节点完成以下步骤。若要加入同一群集，所有节点必须共享相同的值。cluster.name
2.1 打开该文件并进行以下更改：$ES_PATH_CONF/elasticsearch.yml
2.1.1 添加群集名称设置并输入群集的名称：

cluster.name: my-cluster

2.1.2 添加node.name设置并输入节点的名称。当 Elasticsearch 启动时，节点名称默认为计算机的主机名。

node.name: node-1

2.1.3 添加以下设置以启用节点间通信并提供对节点证书的访问权限。

由于您在群集中的每个节点上使用相同的文件，因此请将验证模式设置为 ：elastic-certificates.p12 certificate

xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate 
xpack.security.transport.ssl.client_authentication: required
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12

	
如果要使用主机名验证，请将验证模式设置为 full。您应该为每个主机生成与 DNS 或 IP 地址匹配的不同证书。请参阅TLS 设置中的参数。
full xpack.security.transport.ssl.verification_mode

2.2 如果您在创建节点证书时输入了密码，请运行以下命令将密码存储在 Elasticsearch 密钥库中：
./bin/elasticsearch-keystore add xpack.security.transport.ssl.keystore.secure_password
./bin/elasticsearch-keystore add xpack.security.transport.ssl.truststore.secure_password

2.3 为群集中的每个节点完成上述步骤。
2.4 在集群中的每个节点上，重新启动 Elasticsearch。

您必须执行完整的群集重新启动。配置为使用 TLS 进行传输的节点无法与使用未加密传输连接的节点进行通信（反之亦然）。

下一步是什么？
祝贺！您已加密群集中节点之间的通信，可以通过TLS 引导检查。

要添加另一层安全性，请为 Elastic Stack 设置基本安全性以及受保护的 HTTPS 流量。除了在 Elasticsearch 集群的传输接口上配置 TLS 之外，您还可以在 HTTP 接口上为 Elasticsearch 和 Kibana 配置 TLS。


3. 基本安全性加安全 HTTPS 流量（Elastic stack）
此方案建立在基本安全性的基础上，并使用 TLS 保护所有 HTTP 流量。除了在 Elasticsearch 集群的传输接口上配置 TLS 之外，您还可以在 HTTP 接口上为 Elasticsearch 和 Kibana 配置 TLS。

如果需要在 HTTP 层上使用相互（双向）TLS，则需要配置相互身份验证的加密。
然后，您将 Kibana 和 Beats 配置为使用 TLS 与 Elasticsearch 进行通信，以便对所有通信进行加密。此安全级别很强，可确保进出群集的任何通信都是安全的。

为Elastic stack设置基本安全性以及受保护的 HTTPS 流量
在生产环境中，某些 Elasticsearch 功能（如令牌和 API 密钥）将被禁用，除非您在 HTTP 层上启用 TLS。此附加安全层可确保与群集之间的所有通信都受到保护。

在模式下运行该工具时，该工具会询问几个有关您希望如何生成证书的问题。虽然有许多选项，但以下选择会产生适用于大多数环境的证书。elasticsearch-certutil http

1) 签署证书

该工具提示您的第一个问题是是否要生成证书签名请求 （CSR）。如果要对自己的证书进行签名 n，或者要使用中央 CA 对证书进行签名 y，请回答。elasticsearch-certutil n y

2) 签署您自己的证书
如果要使用在生成证书颁发机构时创建的 CA，请在询问您是否要生成 CSR 时回答 n 。然后，指定 CA 的位置 .p12，该工具使用该位置对证书进行签名和生成。此过程中的步骤遵循此工作流。 n .p12

3) 使用中央 CA 对证书进行签名
如果您在与中央安全团队合作的环境中工作，他们可能会为您生成证书。您组织内的基础架构可能已经配置为信任现有 CA，因此，如果您使用 CSR 并将该请求发送给控制您的 CA 的团队，则客户端可能会更轻松地连接到 Elasticsearch。要使用中央 CA，请回答第一个问题。 y

先决条件
完成为Elastic stack设置基本安全性中的所有步骤。

为 Elasticsearch 加密 HTTP 客户端
3.1 在集群中的每个节点上，停止 Elasticsearch 和 Kibana（如果它们正在运行）。
3.2 在任何单个节点上，从您安装 Elasticsearch 的目录中，运行 Elasticsearch HTTP 证书工具以生成证书签名请求 （CSR）。

./bin/elasticsearch-certutil http

此命令生成一个文件，其中包含要与 Elasticsearch 和 Kibana 一起使用的证书和密钥。每个文件夹都包含如何使用这些文件的说明。.zip README.txt
3.2.1 当系统询问您是否要生成 CSR 时，请输入 。n
3.2.2 当系统询问您是否要使用现有 CA 时，请输入 。y
3.2.3 输入您的 CA 的路径。这是您为群集生成的文件的绝对路径。elastic-stack-ca.p12
3.2.4 输入您的 CA 的密码。
3.2.5 输入证书的过期值。您可以输入有效期（以年、月或天为单位）。例如，输入 90 天。90D
3.2.6 当系统询问您是否要为每个节点生成一个证书时，请输入 。y
每个证书都有自己的私钥，并将为特定的主机名或 IP 地址颁发。
3.2.7 出现提示时，输入群集中第一个节点的名称。使用生成节点证书时使用的相同节点名称。
3.2.8 输入用于连接到第一个节点的所有主机名。这些主机名将作为 DNS 名称添加到证书的"使用者备用名称 （SAN）"字段中。
列出用于通过 HTTPS 连接到群集的每个主机名和变体。
3.2.9 输入客户端可用于连接到节点的 IP 地址。
3.2.10 对群集中的每个附加节点重复这些步骤。

3.3 为每个节点生成证书后，在出现提示时输入私钥的密码。
3.4 解压缩生成的文件。这个压缩文件包含一个 Elasticsearch 和 Kibana 的目录。elasticsearch-ssl-http.zip
/elasticsearch
|_ README.txt
|_ http.p12
|_ sample-elasticsearch.yml 
/kibana
|_ README.txt
|_ elasticsearch-ca.pem
|_ sample-kibana.yml

3.5 在群集中的每个节点上，完成以下步骤：
3.5.1 将相关证书复制到目录中。http.p12 $ES_PATH_CONF
3.5.2 编辑文件以启用 HTTPS 安全性并指定安全证书的位置。elasticsearch.yml http.p12
xpack.security.http.ssl.enabled: true
xpack.security.http.ssl.keystore.path: http.p12
3.5.3 将私钥的密码添加到 Elasticsearch 中的安全设置中。
./bin/elasticsearch-keystore add xpack.security.http.ssl.keystore.secure_password
3.5.4 启动 Elasticsearch。


4. 加密 Kibana 的 HTTP 客户端通信
浏览器向 Kibana 发送流量，Kibana 向 Elasticsearch 发送流量。这些通信通道单独配置为使用 TLS。您可以加密浏览器和 Kibana 之间的流量，然后加密 Kibana 和 Elasticsearch 之间的流量。

4.1 加密 Kibana 和 Elasticsearch 之间的流量
使用该选项运行该工具时 bin/elasticsearch-certutil http  ，它会创建一个包含文件的目录 ./kibana 。
您可以使用此文件将 Kibana 配置为信任 HTTP 层的 Elasticsearch CA。 
./bin/elasticsearch-certutil http 
./kibana/elasticsearch-ca.pem

4.1.1 将文件复制到由路径定义的 Kibana 配置目录。elasticsearch-ca.pem $KBN_PATH_CONF
4.1.2 打开并添加以下行以指定 HTTP 层的安全证书的位置。kibana.yml

elasticsearch.ssl.certificateAuthorities: $KBN_PATH_CONF/elasticsearch-ca.pem
4.1.3 添加以下行以指定 Elasticsearch 集群的 HTTPS URL。
elasticsearch.hosts: https://<your_elasticsearch_host>:9200

4.1.4 重新启动 Kibana。

连接到安全监控群集

如果启用了弹性监控功能，并且您配置了单独的 Elasticsearch 监控集群，您还可以将 Kibana 配置为通过 HTTPS 连接到监控集群。步骤相同，但每个设置都以 monitoring 为前缀。例如，monitoring.ui.elasticsearch.hosts 和  monitoring.ui.elasticsearch.ssl.truststore.path。

您必须为监控集群创建单独的安全文件。elasticsearch-ca.pem

4.2 加密浏览器和 Kibana 之间的流量
您可以为 Kibana 创建服务器证书和私钥。Kibana 在接收来自 Web 浏览器的连接时使用此服务器证书和相应的私钥。
获取服务器证书时，必须正确设置其使用者备用名称 （SAN），以确保浏览器信任它。您可以将一个或多个 SAN 设置为 Kibana 服务器的完全限定域名 （FQDN）、主机名或 IP 地址。选择 SAN 时，请选择您将用于在浏览器中连接到 Kibana 的任何属性，该属性可能是 FQDN。

以下说明为 Kibana 创建证书签名请求 （CSR）。CSR 包含 CA 用于生成和签署安全证书的信息。证书可以是受信任的（由受信任的公共 CA 签名）或不受信任的证书（由内部 CA 签名）。自签名或内部签名证书对于开发环境和生成概念证明是可以接受的，但不应在生产环境中使用。

在进入生产环境之前，请使用受信任的 CA（如Let's Encrypt）或组织的内部 CA 对证书进行签名。使用签名证书可建立浏览器对连接到 Kibana 以进行内部访问或公共互联网的信任。

4.2.1 为 Kibana 生成服务器证书和私钥。
./bin/elasticsearch-certutil csr -name kibana-server -dns example.com,www.example.com
CSR 的公用名 （CN） 为 kibana-server ，一个 SAN 为 example.com ，另一个 SAN 为 www.example.com。

默认情况下，此命令生成一个包含以下内容的文件：csr-bundle.zip
/kibana-server
|_ kibana-server.csr - 证书
|_ kibana-server.key - 私钥
4.2.2 解压缩文件以获取未签名的安全证书和未加密的私钥。
csr-bundle.zip 
kibana-server.csr 
kibana-server.key 
4.2.3 将证书 kibana-server.csr 签名请求发送到内部 CA 或受信任的 CA 进行签名以获取签名证书。签名文件可以采用不同的格式，例如 像 .crt  kibana-server.crt

Linux 生成CRT、KEY、CSR证书:
	1.key的生成 
	openssl genrsa -des3 -out server.key 2048 
	这样是生成rsa私钥，des3算法，openssl格式，2048位强度。server.key是密钥文件名。为了生成这样的密钥，需要一个至少四位的密码。可以通过以下方法生成没有密码的key。
	openssl rsa -in server.key -out server.key 
	2.生成CA的crt
	openssl req -new -x509 -key server.key -out ca.crt -days 3650 
	生成的ca.crt文件是用来签署下面的server.csr文件。 
	3. csr的生成方法
	openssl req -new -key server.key -out server.csr 
	需要依次输入国家，地区，组织，email。生成的csr文件交给CA签名后形成服务端自己的证书。 
	4. crt生成方法
	CSR文件必须有CA的签名才可形成证书，可将此文件发送到verisign等地方由它验证，要交一大笔钱，何不自己做CA呢。
	openssl x509 -req -days 3650 -in server.csr -CA ca.crt -CAkey server.key -CAcreateserial -out server.crt
	输入key的密钥后，完成证书生成。-CA选项指明用于被签名的csr证书，-CAkey选项指明用于签名的密钥，-CAserial指明序列号文件，而-CAcreateserial指明文件不存在时自动生成。
	最后生成了私用密钥：server.key和自己认证的SSL证书：server.crt

	证书合并：
	cat server.key server.crt > server.pem


4.2.4 打开并添加以下行，以配置 Kibana 以访问服务器证书和未加密的私钥。kibana.yml

server.ssl.certificate: $KBN_PATH_CONF/kibana-server.crt
server.ssl.key: $KBN_PATH_CONF/kibana-server.key

4.2.5 添加以下行以为入站连接启用 TLS。kibana.yml
server.ssl.enabled: true
4.2.6 启动 Kibana。

进行这些更改后，您必须始终通过 HTTPS 访问 Kibana。例如。https://<your_kibana_host>.com


5. JDK 版本支持的 SSL/TLS 版本编辑
Elasticsearch 依赖于 JDK 对 SSL 和 TLS 的实现。

不同的JDK版本支持不同版本的SSL，这可能会影响Elasticsearch的运行方式。

当在 JDK 中的缺省 JSSE 提供程序上运行时，此支持适用。配置为使用FIPS 140-2安全提供程序的 JVM 可能具有自定义 TLS 实现，该实现可能支持与此列表不同的 TLS 协议版本。

有关 TLS 支持的信息，请查看安全提供商的发行说明。

SSLv3
SSL v3 在所有与 Elasticsearch 兼容的JDK上都受支持，但默认情况下处于禁用状态。
TLSv1
TLS v1.0 在所有Elasticsearch 兼容的 JDK上均受支持。一些较新的JDK，包括与Elasticsearch捆绑在一起的JDK，默认情况下禁用TLS v1.0。
TLSv1.1
TLS v1.1 在所有Elasticsearch 兼容的 JDK上均受支持。一些较新的JDK，包括与Elasticsearch捆绑在一起的JDK，默认情况下禁用TLS v1.1。
TLSv1.2
TLS v1.2 在所有Elasticsearch 兼容的 JDK上均受支持。默认情况下，它在 Elasticsearch 支持的所有 JDK 上都处于启用状态，包括捆绑的 JDK。
TLSv1.3
JDK11 及更高版本支持 TLS v1.3，JDK8 构建的版本比 8u261 更新（包括 Elasticsearch 支持的每个 JDK8 发行版的最新版本）。默认情况下，TLS v1.3 在与 Elasticsearch 捆绑在一起的 JDK 上受支持并启用。

尽管 Elasticsearch 支持在没有 TLS v1.3 的较旧 JDK8 版本上运行，但我们建议升级到包含 TLS v1.3 的 JDK 版本，以获得更好的支持和更新。

在 JDK 上启用其他 SSL/TLS 版本
JDK 支持的 SSL/TLS 版本集由作为 JDK 的一部分安装的 java 安全性属性文件控制。

此配置文件列出了在该 JDK 中禁用的 SSL/TLS 算法。完成这些步骤可从该列表中删除 TLS 版本，并在 JDK 中使用它。

找到 JDK 的配置文件。
从该文件复制设置，并将其添加到 Elasticsearch 配置目录中的自定义配置文件中。jdk.tls.disabledAlgorithms
在自定义配置文件中，从 中删除要使用的 TLS 版本的值。jdk.tls.disabledAlgorithms
配置 Elasticsearch 以将自定义系统属性传递给 JDK，以便使用您的自定义配置文件。
找到 JDK 的配置文件
对于 Elasticsearch捆绑的 JDK，配置文件位于 Elasticsearch 主目录的子目录中（）：$ES_HOME

Linux：$ES_HOME/jdk/conf/security/java.security
窗户：$ES_HOME/jdk/conf/security/java.security
macOS：$ES_HOME/jdk.app/Contents/Home/conf/security/java.security
对于JDK8，配置文件位于 Java 安装的目录中。如果指向您用于运行 Elasticsearch 的 JDK 的主目录，则配置文件将位于：

$JAVA_HOME/jre/lib/security/java.security
对于JDK11 或更高版本，配置文件位于 Java 安装的目录中。如果指向您用于运行 Elasticsearch 的 JDK 的主目录，则配置文件将位于：

$JAVA_HOME/conf/security/java.security
复制已禁用的算法设置
在 JDK 配置文件中，有一行以 jdk.tls.disabledAlgorithms= 开头。此设置控制在 JDK 中禁用哪些协议和算法。该设置的值通常跨越多行。

例如，在 OpenJDK 16 中，设置为：

jdk.tls.disabledAlgorithms=SSLv3, TLSv1, TLSv1.1, RC4, DES, MD5withRSA, \
    DH keySize < 1024, EC keySize < 224, 3DES_EDE_CBC, anon, NULL
在 Elasticsearch 配置目录中创建一个名为 es.java.security 的新文件。将设置从 JDK 的缺省配置文件复制到 es.java.security。您无需复制任何其他设置。 

启用所需的 TLS 版本
编辑 Elasticsearch 配置目录中的文件 es.java.security，并修改设置，以便不再列出您希望使用的任何 SSL 或 TLS 版本。 jdk.tls.disabledAlgorithms

例如，要在 OpenJDK 16 上启用 TLSv1.1（使用前面显示的设置），该文件将包含以前禁用的 TLS 算法，但以下算法除外 TLSv1.1：

jdk.tls.disabledAlgorithms=SSLv3, TLSv1, RC4, DES, MD5withRSA, \
    DH keySize < 1024, EC keySize < 224, 3DES_EDE_CBC, anon, NULL

启用自定义安全配置
要启用自定义安全策略，请在 Elasticsearch 配置目录的jvm.options.d目录中创建一个名为 java.security.options 的文件，其中包含以下内容：

-Djava.security.properties=/path/to/your/es.java.security

在 Elasticsearch 中启用 TLS 版本
SSL/TLS 版本可以通过ssl.supported_protocols设置在 Elasticsearch 中启用和禁用。

Elasticsearch 将仅支持由底层 JDK启用的 TLS 版本。如果 ssl.supported_protocols 配置为包含 JDK 中未启用的 TLS 版本，则该版本将被静默忽略。

同样，在 JDK 中启用的 TLS 版本将不会被使用，除非将其配置为 Elasticsearch 中的一个。ssl.supported_protocols

```


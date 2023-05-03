**jenkinsfile文件详解**

```css
pipeline {
    agent any

    environment {
       name = "ecloud-app-zlswfw"
       namespace = "ecloud-app"
       repo = "10.160.242.21:7728"
       tag = "${BRANCH_NAME}.${BUILD_NUMBER.padLeft(8, '0')}"
       imageName = "${namespace}/${name}:${tag}"
    }

    options {
        skipDefaultCheckout(true) # 跳过默认的检出
        disableConcurrentBuilds() # 禁用并发构建
    }

    stages {
        stage('CheckOut') { # 检出|签出 代码
            steps {
                git credentialsId: 'gitlab.passwd', url: "https://gitlab.cpms.tech/ecloud-config/deployment-config.git" # 下载Dockerfile, Jenkinsfile等自动部署文件
                sh "cp -rf ${namespace}/${name}/${BRANCH_NAME}/* ."  # 复制服务的自动部署文件到当前目录下
                git branch: "${BRANCH_NAME}", credentialsId: 'gitlab.passwd', url: "https://gitlab.cpms.tech/${namespace}/${name}.git"  #下载源码文件
																					https://gitlab.cpms.tech/ecloud-app/ecloud-app-zlswfw.git
																					https://prod.gitlab.cpms.tech/i-prod-deployment/i-prod-deployment-template.git
            }
        }
        stage('Compile') {  # 源码编译
            steps {
                sh 'mvn clean compile package dependency:copy-dependencies -U -Dmaven.test.skip=true'  # 源码文件编译打包
                sh "cp ${name}-core/target/${name}-core.jar app.jar" # 修改打包后得文件名为 app.jar
            }
        }

# clean 用来清除编译后的文件**(target文件夹里面的)【一般清缓存】**
# 
# compile  编译只会编译main里面的内容
# 
# test       执行单元测试，先将main、test中的内容进行编译，然后执行test中的测试方法
# 
# package  打包 (javaSe-->jar, javaweb-->war)，其实执行打包之前先执行test，然后对项目进行打包
# 
# install  把项目打包之后安装到本地仓库，其实执行install之前先执行了打包，然后对项目进行安装到本地仓库
# 
# 生命周期
# 
# 当我们执行了install 也会执行compile  test  package


        stage('Nexus') {
            steps{
                sh "mvn deploy -pl ${name}-api -am"  # 将最终版本的包拷贝到远程的nexus私服
            }
        }
        stage('SonarQube') {   # 代码扫描
           steps{
               // sh "sonar-scanner"
                build wait: false, propagate: false, job: 'ecloud-config/ecloud-config%2Fsonar/master', parameters: [string(name: 'namespace', value: "${namespace}"), string(name: 'name', value: "${name}")]
           }
        }
        stage('Image') {
            steps{
                script {
                    docker.withRegistry("http://${repo}","nexus-admin") {   # 远程docker仓库 https://nexus.cpms.tech/repository/docker-ecloud-snapshots/
                       docker.build("${imageName}",'.').push()   # 使用docker build 构建镜像, 并上传至仓库
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                sh "sed -e 's#{IMAGE_URL}#${imageName}#g;s#{APP_NAME}#${name}#g;s#{NAMESPACE}#${namespace}#g' deploy.yml > deploy-temp.yml"  # 替换文件中的变量
				
				# s#{IMAGE_URL}#${imageName}#
				# ecloud-app/ecloud-app-ajfl:develop.00002769 ${imageName}
				# ecloud-app/ecloud-app-ajfl:develop.00002769 ${namespace}/${name}:${tag}
				# ecloud-app/ecloud-app-ajfl:develop.00002769 ecloud-app/ecloud-app-zlswfw:dev|master.00000000
				
				# IMAGE_URL: ecloud-app/ecloud-app-ajfl:develop.00002769 # Jenkins 输入的变量值
				# imageName = "${namespace}/${name}:${tag}"
				# namespace = "ecloud-app"	
				# name = "ecloud-app-zlswfw"
				# tag = "${BRANCH_NAME}.${BUILD_NUMBER.padLeft(8, '0')}"
				# repo = "10.160.242.21:7728"
				
				
                sh "kubectl --kubeconfig /jenkins/.kube/config apply -f deploy-temp.yml --namespace=${namespace}"  # 发布
            }
        }
    }
}


   workload.user.cattle.io/workloadselector: deployment-{NAMESPACE}-{APP_NAME}
  name: {APP_NAME}
  namespace: {NAMESPACE}
      workload.user.cattle.io/workloadselector: deployment-{NAMESPACE}-{APP_NAME}
        workload.user.cattle.io/workloadselector: deployment-{NAMESPACE}-{APP_NAME}
          image: {IMAGE_URL}
          name: {APP_NAME}
          name: filebeat-for-{APP_NAME}
              value: {APP_NAME}
          emptyDir: {}
    field.cattle.io/targetWorkloadIds: '["deployment:{NAMESPACE}:{APP_NAME}"]'
  name: {APP_NAME}
  namespace: {NAMESPACE}
    - name: {APP_NAME}-port
    workload.user.cattle.io/workloadselector: deployment-{NAMESPACE}-{APP_NAME}

{IMAGE_URL}

{NAMESPACE}	

{APP_NAME}
```


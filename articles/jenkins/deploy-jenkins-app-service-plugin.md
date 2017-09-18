---
title: "Jenkins 플러그 인을 사용해 Azure App Service에 배포 | Microsoft Docs"
description: "Azure App Service Jenkins 플러그 인을 사용하여 Jenkins에서 Azure에 Java 웹앱을 배포하는 방법을 알아봅니다."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: b2035d6bc0d323f2497a1db9b88d3ed015235b16
ms.contentlocale: ko-kr
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-to-azure-app-service-with-jenkins-plugin"></a>Jenkins 플러그 인으로 Azure App Service에 배포 
Azure에 Java 웹앱을 배포하려면 [Jenkins 파이프라인](/azure/jenkins/execute-cli-jenkins-pipeline)의 Azure CLI를 사용하거나 [Azure App Service Jenkins 플러그 인](https://plugins.jenkins.io/azure-app-service)을 사용할 수 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * FTP를 통해 Azure App Service에 배포하도록 Jenkins 구성 
> * Docker를 통해 Linux의 Azure App Service에 배포하도록 Jenkins 구성 

## <a name="create-and-configure-jenkins-instance"></a>Jenkins 인스턴스 만들기 및 구성
Jenkins 마스터가 아직 없는 경우 [솔루션 템플릿](install-jenkins-solution-template.md)으로 시작합니다. 이 템플릿에는 JDK8 및 다음 필수 플러그 인이 포함되어 있습니다.

* [Jenkins Git 클라이언트 플러그 인](https://plugins.jenkins.io/git-client) v.2.4.6 
* [Docker Commons 플러그 인](https://plugins.jenkins.io/docker-commons) v.1.4.0
* [Azure 자격 증명](https://plugins.jenkins.io/azure-credentials) v.1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-server) v.0.1

App Service 플러그 인을 사용하여 Azure App Service에서 지원되는 모든 언어(예: C#, PHP, Java 및 node.js 등)로 웹앱을 배포할 수 있습니다. 이 자습서에서는 샘플 Java 앱인 [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample)를 사용합니다. 사용자 고유의 GitHub 계정에 리포지토리를 분기하려면 오른쪽 위 모서리에 있는 **분기** 단추를 클릭합니다.  

Java 프로젝트 빌드를 위해 Java JDK 및 Maven이 필요합니다. 지속적인 통합을 위해 사용하는 경우 Jenkins 마스터 또는 VM 에이전트에 구성 요소를 설치해야 합니다. 

설치하려면 SSH를 사용하여 Jenkins 인스턴스에 로그인하고 다음 명령을 실행합니다.

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Linux에 App Service를 배포하는 경우 빌드에 사용되는 VM 에이전트 또는 Jenkins 마스터에도 Docker를 설치해야 합니다. 도커를 설치하려면 다음 문서를 참조하십시오. https://docs.docker.com/engine/installation/linux/ubuntu/.

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Jenkins 자격 증명에 Azure 서비스 주체 추가

Azure에 배포하려면 Azure 서비스 주체가 필요합니다. 

<ol>
<li>[Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) 또는 [Azure Portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal)을 사용하여 Azure 서비스 주체를 만듭니다.</li>
<li>Jenkins 대시보드 내에서 **자격 증명->시스템->**을 클릭합니다. **전역 자격 증명(제한 없음)**을 클릭합니다.</li>
<li>**자격 증명 추가**를 클릭한 다음 구독 ID, 클라이언트 ID, 클라이언트 암호 및 OAuth 2.0 토큰 끝점을 입력하여 Microsoft Azure 서비스 주체를 추가합니다. 이후 단계에서 사용할 ID **mySp**를 제공합니다.</li>
</ol>

## <a name="azure-app-service-plugin"></a>Azure App Service 플러그 인

Azure App Service 플러그 인 v1.0은 다음을 통해 Azure 웹앱으로의 지속적인 배포를 지원합니다.

* Git 및 FTP
* Linux의 웹앱용 Docker

## <a name="configure-jenkins-to-deploy-web-app-through-ftp-using-the-jenkins-dashboard"></a>Jenkins 대시보드를 사용하여 FTP를 통해 웹앱을 배포하도록 Jenkins 구성

Azure 웹앱에 프로젝트를 배포하려면 Git 또는 FTP를 사용하여 빌드 아티팩트(예:.Java의 .war 파일)를 업로드할 수 있습니다.

Jenkins에서 작업을 설정하기 전에 Azure App Service 계획과 Java 앱에서 실행하기 위한 웹앱이 필요합니다.


1. [az appservice plan create](/cli/azure/appservice/plan#create) CLI 명령을 사용하여 **무료** 가격 책정 계층과 함께 Azure App Service 계획을 만듭니다. App Service 계획은 앱을 호스트하는 데 사용되는 실제 리소스를 정의합니다. App Service 계획에 할당된 모든 응용 프로그램은 이들 리소스를 공유하므로 여러 앱을 호스팅할 때 비용을 절감할 수 있습니다.
2. 웹앱을 만듭니다. [Azure Portal](/azure/app-service-web/web-sites-configure)을 사용하거나 다음 Az CLI 명령을 사용할 수 있습니다.
```azurecli-interactive 
az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
```

3. 앱에 필요한 Java 런타임 구성을 설정해야 합니다. 다음 Azure CLI 명령은 최근 Java 8 JDK 및 [Apache Tomcat](http://tomcat.apache.org/) 8.0에서 실행되도록 웹앱을 구성합니다.
```azurecli-interactive
az webapp config set \
--name <myAppName> \
--resource-group <myResourceGroup> \
--java-version 1.8 \
--java-container Tomcat \
--java-container-version 8.0
```

### <a name="set-up-the-jenkins-job"></a>Jenkins 작업 설정


1. Jenkins 대시보드에 새 **프리스타일** 프로젝트를 만듭니다.
2. **리포지토리 URL**을 제공하여 [Azure용 간단한 Java 웹앱](https://github.com/azure-devops/javawebappsample)의 로컬 분기를 사용하도록 **소스 코드 관리**를 구성합니다. 예: http://github.com/&lt;yourID>/javawebappsample.
3. 빌드 단계를 추가하여 Maven을 사용하여 프로젝트를 빌드합니다. 이를 위해 **셸 실행**을 추가합니다. 이 예제의 경우 대상 폴더의 *.war 파일 이름을 ROOT.war로 바꾸기 위한 추가 단계가 필요합니다.   
```bash
mvn clean package
mv target/*.war target/ROOT.war
```

4. **Azure 웹앱 게시**를 선택하여 빌드 후 작업을 추가합니다.
5. 이전 단계에서 저장한 Azure 서비스 주체 "mySp"를 제공합니다.
6. **앱 구성** 섹션에서 구독의 리소스 그룹 및 웹앱을 선택합니다. 플러그 인은 웹앱이 Windows 기반인지 또는 Linux 기반인지를 자동으로 탐지합니다. Windows 기반 웹앱의 경우 "파일 게시" 옵션이 표시됩니다.
7. 배포할 파일을 입력합니다(예: Java를 사용 중인 경우 war 패키지). 소스 디렉터리와 대상 디렉터리는 선택 사항입니다. 파일을 업로드할 때 매개 변수를 사용하여 소스 및 대상 폴더를 지정할 수 있습니다. Azure의 Java 웹앱은 Tomcat 서버에서 실행됩니다. 따라서 webapps 폴더에 war 패키지를 업로드합니다. 이 예제에서는 **소스 디렉터리**를 "target"으로 설정하고 **대상 디렉터리**에 "webapps"를 입력합니다.
8. 프로덕션 이외의 슬롯에 배포하려는 경우 **슬롯** 이름도 설정할 수 있습니다.
9. 프로젝트를 저장하고 빌드합니다. 빌드가 완료되면 웹앱이 Azure에 배포됩니다.

### <a name="deploy-web-app-through-ftp-using-jenkins-pipeline"></a>Jenkins 파이프라인을 사용하여 FTP를 통해 웹앱 배포

플러그 인에서 파이프라인을 사용할 수 있습니다. GitHub 리포지토리의 샘플을 참조할 수 있습니다.

1. GitHub 웹 UI에서 **Jenkinsfile_ftp_plugin** 파일을 엽니다. 연필 아이콘을 클릭하여 이 파일을 편집하고 줄 11과 12에서 각각 웹앱의 리소스 그룹 및 이름을 업데이트합니다.    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. 줄 14를 변경하여 Jenkins 인스턴스의 자격 증명 ID를 업데이트합니다.    
```java
withCredentials([azureServicePrincipal('<mySp>')]) {
```

### <a name="create-a-jenkins-pipeline"></a>Jenkins 파이프라인 만들기

1. 웹 브라우저에서 Jenkins를 열고 **새 항목**을 클릭합니다.
2. 작업 이름을 제공하고 **파이프라인**을 선택합니다. **확인**을 클릭합니다.
3. **파이프라인** 탭을 클릭합니다.
4. **정의**에서 **SCM의 파이프라인 스크립트**를 선택합니다.
5. **SCM**에서 **Git**을 선택합니다. 분기 리포지토리의 GitHub URL을 입력합니다(https:&lt;분기 리포지토리>.git).
6. **스크립트 경로**를 "Jenkinsfile_ftp_plugin"으로 업데이트합니다.
7. **저장**을 클릭하고 작업을 실행합니다.

## <a name="configure-jenkins-to-deploy-web-app-on-linux-through-docker"></a>Jenkins를 구성하여 Docker를 통해 Linux에 웹앱 배포

Git/FTP 외에 Linux의 웹앱은 Docker를 사용한 배포를 지원합니다. Docker를 사용하여 배포하려면 서비스 런타임에 웹앱을 Docker 이미지로 패키지화하는 Dockerfile을 제공해야 합니다. 그러면 플러그 인이 이미지를 빌드하고 Docker 레지스트리에 푸시하고 이미지를 웹앱에 배포합니다.

Linux의 웹앱은 Git 및 FTP와 같은 일반적인 방법도 지원하지만 기본 제공되는 언어에 한합니다(.NET Core, Node.js, PHP 및 Ruby). 다른 언어의 경우 응용 프로그램 코드와 서비스 런타임을 함께 Docker 이미지로 패키징하고 Docker를 사용하여 배포해야 합니다.

Jenkins에서 작업을 설정하기 전에 Linux에 Azure App Service가 필요합니다. 개인 Docker 컨테이너 이미지를 저장하고 관리하기 위해 컨테이너 레지스트리도 필요합니다. DockerHub를 사용할 수 있습니다. 이 예제에서는 Azure Container Registry를 사용합니다.

* [여기](../app-service/containers/quickstart-nodejs.md)에 나온 단계에 따라 Linux에서 웹앱을 만들 수 있습니다. 
* Azure Container Registry는 오픈 소스 Docker Registry 2.0에 기반한 관리되는 [Docker 레지스트리](https://docs.docker.com/registry/) 서비스입니다. 방법에 대한 추가 지침이 필요한 경우 [여기](/azure/container-registry/container-registry-get-started-azure-cli)에 나온 단계에 따릅니다. DockerHub를 사용할 수도 있습니다.

### <a name="to-deploy-using-docker"></a>Docker를 사용하여 배포하려면 다음을 수행합니다.

1. Jenkins 대시보드에 새 프리스타일 프로젝트를 만듭니다.
2. **리포지토리 URL**을 제공하여 [Azure용 간단한 Java 웹앱](https://github.com/azure-devops/javawebappsample)의 로컬 분기를 사용하도록 **소스 코드 관리**를 구성합니다. 예: http://github.com/&lt;yourid>/javawebappsample.
빌드 단계를 추가하여 Maven을 사용하여 프로젝트를 빌드합니다. 이를 위해 **셸 실행**을 추가하고 **명령**에 다음 줄을 추가합니다.    
```bash
mvn clean package
```

3. **Azure 웹앱 게시**를 선택하여 빌드 후 작업을 추가합니다.
4. 이전 단계에서 저장한 Azure 서비스 주체 **mySp**를 Azure 자격 증명으로 제공합니다.
5. **앱 구성** 섹션에서 구독의 리소스 그룹 및 Linux 웹앱을 선택합니다.
6. Docker를 통해 게시를 선택합니다.
7. **Dockerfile** 경로를 입력합니다. 기본값 "/Dockerfile"을 유지할 수 있습니다. Azure Container Registry를 사용하는 경우 **Docker 레지스트리 URL**에 https://&lt;myRegistry>.azurecr.io 형식으로 입력합니다. DockerHub를 사용하는 경우 비워 둡니다.
8. **레지스트리 자격 증명**에는 Azure Container Registry에 대한 자격 증명을 추가합니다. Azure CLI에서 다음 명령을 실행하여 사용자 ID와 암호를 가져올 수 있습니다. 첫 번째 명령은 관리자 계정을 활성화합니다.    
```azurecli-interactive
az acr update -n <yourRegistry> --admin-enabled true
az acr credential show -n <yourRegistry>
```

9. **고급** 탭의 Docker 이미지 이름 및 태그는 선택 사항입니다. 기본적으로 이미지 이름은 Azure Portal에서(Docker 컨테이너 설정에) 구성한 이미지 이름에서 가져옵니다. 태그는 $BUILD_NUMBER에서 생성됩니다. Azure Portal에 이미지 이름을 지정하거나 **고급** 탭의 **Docker 이미지**에 대한 값을 제공해야 합니다. 이 예제의 경우 **Docker 이미지**에 "&lt;yourRegistry>.azurecr.io/calculator"를 입력하고 **Docker 이미지 태그**는 비워 둡니다.
10. 기본 제공 Docker 이미지 설정을 사용할 경우 배포가 실패합니다. Azure Portal의 Docker 컨테이너 설정에서 사용자 지정 이미지를 사용하도록 Docker 구성을 변경해야 합니다. 기본 제공 이미지의 경우 파일 업로드 방법을 사용하여 배포합니다.
11. 파일 업로드 방법과 비슷한 방법으로, 프로덕션 이외의 다른 슬롯을 선택할 수 있습니다.
12. 프로젝트를 만들고 빌드합니다. 컨테이너 이미지가 레지스트리로 푸시되고 웹앱이 배포되는 것을 볼 수 있습니다.

### <a name="deploy-to-web-app-on-linux-through-docker-using-jenkins-pipeline"></a>Jenkins 파이프라인을 사용하여 Docker를 통해 Linux에 웹앱 배포

1. GitHub 웹 UI에서 **Jenkinsfile_container_plugin** 파일을 엽니다. 연필 아이콘을 클릭하여 이 파일을 편집하고 줄 11과 12에서 각각 웹앱의 리소스 그룹 및 이름을 업데이트합니다.    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. 줄 13을 컨테이너 레지스트리 서버로 변경합니다.    
```java
def registryServer = '<registryURL>'
```    

3. 줄 16을 변경하여 Jenkins 인스턴스의 자격 증명 ID를 업데이트합니다.    
```java
azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
```    
### <a name="create-jenkins-pipeline"></a>Jenkins 파이프라인 만들기    

1. 웹 브라우저에서 Jenkins를 열고 **새 항목**을 클릭합니다.
2. 작업 이름을 제공하고 **파이프라인**을 선택합니다. **확인**을 클릭합니다.
3. **파이프라인** 탭을 클릭합니다.
4. **정의**에서 **SCM의 파이프라인 스크립트**를 선택합니다.
5. **SCM**에서 **Git**을 선택합니다.
6. 분기 리포지토리의 GitHub URL을 입력합니다(https:&lt;분기 리포지토리>.git).</li>
7, **스크립트 경로**를 "Jenkinsfile_container_plugin"으로 업데이트합니다.
8. **저장**을 클릭하고 작업을 실행합니다.

## <a name="verify-your-web-app"></a>웹앱 확인

1. WAR 파일이 웹앱에 성공적으로 배포되었는지 확인하려면 웹 브라우저를 엽니다.
2. http://&lt;app_name>.azurewebsites.net/api/calculator/ping으로 이동하면 다음이 표시됩니다.    
     Java 웹앱 시작!!! 업데이트되었습니다!
   2017년 6월 17일 일요일 16:39:10 UTC
3. http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>(&lt;x> 및 &lt;y>를 임의의 숫자로 대체)로 이동하여 x와 y의 합계를 구합니다.        
    ![계산기: 추가](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-app-service-on-linux"></a>Linux의 앱 서비스

* 확인하려면 Azure CLI에서 다음을 실행합니다.

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    다음 결과를 얻을 수 있습니다.
    
    ```
    [
    "calculator"
    ]
    ```
    
    http://&lt;app_name>.azurewebsites.net/api/calculator/ping으로 이동합니다. 메시지가 표시됩니다. 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>(&lt;x> 및 &lt;y>를 임의의 숫자로 대체)로 이동하여 x와 y의 합계를 구합니다.
    
## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure App Service 플러그 인을 사용하여 Azure에 배포합니다.

다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * FTP를 통해 Azure App Service를 배포하도록 Jenkins 구성 
> * Docker를 통해 Linux의 Azure App Service에 배포하도록 Jenkins 구성 

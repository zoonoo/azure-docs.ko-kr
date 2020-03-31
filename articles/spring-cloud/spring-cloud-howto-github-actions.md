---
title: GitHub 작업을 가진 Azure 스프링 클라우드 CI/CD
description: GitHub 작업을 사용하여 Azure 스프링 클라우드에 대한 CI/CD 워크플로를 빌드하는 방법
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 559c894a2212466761de820de7486ae203337802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538467"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>GitHub 작업을 가진 Azure 스프링 클라우드 CI/CD

GitHub 작업은 자동화된 소프트웨어 개발 수명 주기 워크플로를 지원합니다. Azure Spring 클라우드에 대한 GitHub 작업을 사용하면 리포지토리에서 워크플로를 만들어 Azure에 빌드, 테스트, 패키지, 릴리스 및 배포할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
이 예제에서는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="set-up-github-repository-and-authenticate"></a>GitHub 리포지토리 설정 및 인증
Azure 로그인 작업을 승인하려면 Azure 서비스 원칙 자격 증명이 필요합니다. Azure 자격 증명을 받으려면 로컬 컴퓨터에서 다음 명령을 실행합니다.
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
특정 리소스 그룹에 액세스하려면 범위를 줄일 수 있습니다.
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
명령은 JSON 개체를 출력해야 합니다.
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

이 예제에서는 GitHub에서 [돼지 메트릭](https://github.com/Azure-Samples/piggymetrics) 샘플을 사용합니다.  샘플을 포크하고 GitHub 리포지토리 페이지를 열고 **설정** 탭을 클릭합니다. **Add a new secret** **Secrets**

 ![새 비밀 추가](./media/github-actions/actions1.png)

비밀 이름과 `AZURE_CREDENTIALS` 해당 값을 제목 아래에 있는 JSON 문자열로 *설정하여 GitHub 리포지토리를 설정하고 인증합니다.*

 ![비밀 데이터 설정](./media/github-actions/actions2.png)

GitHub 작업의 키 볼트에서 Azure 로그인 자격 증명을 [GitHub 작업의 키 볼트로 인증 Azure 스프링에](./spring-cloud-github-actions-key-vault.md)설명된 대로 받을 수도 있습니다.

## <a name="provision-service-instance"></a>서비스 인스턴스 프로비저닝
Azure Spring 클라우드 서비스 인스턴스를 프로비전하려면 Azure CLI를 사용하여 다음 명령을 실행합니다.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>워크플로 구축
워크플로는 다음 옵션을 사용하여 정의됩니다.

### <a name="prepare-for-deployment-with-azure-cli"></a>Azure CLI로 배포 준비
명령은 `az spring-cloud app create` 현재 idempotent가 아닙니다.  기존 Azure Spring 클라우드 앱 및 인스턴스에서 이 워크플로를 권장합니다.

준비를 위해 다음 Azure CLI 명령을 사용합니다.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Azure CLI를 직접 배포
리포지토리에서 `.github/workflow/main.yml` 파일 만들기:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Azure CLI 작업으로 배포
az `run` 명령은 Azure CLI의 최신 버전을 사용합니다. 주요 변경 내용이 있는 경우 Azure/CLI를 사용하여 특정 버전의 Azure CLI를 사용할 수도 `action`있습니다. 

> [!Note] 
> 이 명령은 새 컨테이너에서 실행되므로 `env` 작동하지 않으며 교차 작업 파일 액세스에는 추가 제한이 있을 수 있습니다.

리포지토리에서 .github/워크플로/main.yml 파일을 만듭니다.
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>메이븐 플러그인으로 배포
또 다른 옵션은 항아리를 배포하고 응용 프로그램 설정을 업데이트하는 [메이븐 플러그인을](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) 사용하는 것입니다. 이 `mvn azure-spring-cloud:deploy` 명령은 idempotent이며 필요한 경우 자동으로 앱을 만듭니다. 해당 앱을 미리 만들 필요가 없습니다.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>워크플로 실행
GitHub로 푸시한 `.github/workflow/main.yml` 후 GitHub **작업을** 자동으로 활성화해야 합니다. 새 커밋을 푸시하면 작업이 트리거됩니다. 브라우저에서 이 파일을 만드는 경우 작업이 이미 실행되어 있어야 합니다.

작업이 활성화되었는지 확인하려면 GitHub 리포지토리 페이지에서 **작업** 탭을 클릭합니다.

 ![작업 활성화 확인](./media/github-actions/actions3.png)

예를 들어 Azure 자격 증명을 설정하지 않은 경우 작업을 오류로 실행하는 경우 오류를 수정한 후 검사를 다시 실행할 수 있습니다. GitHub 리포지토리 페이지에서 **작업을**클릭하고 특정 워크플로 작업을 선택한 다음 **다시 실행 확인** 단추를 클릭하여 검사를 다시 실행합니다.

 ![검사 다시 실행](./media/github-actions/actions4.png)

## <a name="next-steps"></a>다음 단계
* [스프링 클라우드 GitHub 작업에 대한 키 볼트](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory 서비스 보안 주체](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [Azure에 대한 GitHub 작업](https://github.com/Azure/actions/)

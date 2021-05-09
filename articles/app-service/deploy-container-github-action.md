---
title: GitHub Actions의 사용자 지정 컨테이너 CI/CD
description: GitHub Actions를 사용하여 CI/CD 파이프라인에서 App Service에 사용자 지정 Linux 컨테이너를 배포하는 방법을 알아봅니다.
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: bf9fba9142de82c6e8518198d54b5e74f1807838
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789432"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>GitHub Actions를 사용하여 App Service에 사용자 지정 컨테이너 배포

[GitHub Actions](https://docs.github.com/en/actions)를 사용하면 자동화된 소프트웨어 개발 워크플로를 유연하게 빌드할 수 있습니다. [Azure 웹 배포 작업](https://github.com/Azure/webapps-deploy)을 사용하면 GitHub Actions를 통해 [App Service](overview.md)에 사용자 지정 컨테이너를 배포하는 워크플로를 자동화할 수 있습니다.

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로의 다양한 단계와 매개 변수를 포함합니다.

Azure App Service 컨테이너 워크플로의 경우 파일에는 다음 세 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체 또는 게시 프로필을 검색합니다. <br /> 2. GitHub 비밀을 만듭니다. |
|**빌드** | 1. 환경을 만듭니다. <br /> 2. 컨테이너 이미지를 빌드합니다. |
|**배포** | 1. 컨테이너 이미지를 배포합니다. |

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요. Azure App Service에 배포하려면 GitHub 리포지토리에 코드가 있어야 합니다. 
- 작동하는 컨테이너 레지스트리 및 컨테이너용 Azure App Service 앱입니다. 이 예에서는 Azure Container Registry를 사용합니다. 컨테이너용 Azure App Service에 대한 전체 배포를 완료해야 합니다. 일반 웹앱과 달리 컨테이너용 웹앱에는 기본 방문 페이지가 없습니다. 작업 예를 포함하도록 컨테이너를 게시합니다.
    - [Docker를 사용하여 컨테이너화된 Node.js 애플리케이션을 만들고 레지스트리에 컨테이너 이미지를 푸시한 다음, Azure App Service에 이미지를 배포하는 방법 알아보기](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성

GitHub Actions용 Azure App Service를 사용하여 인증하려면 게시 프로필을 사용하는 것이 좋습니다. 서비스 주체를 사용하여 인증할 수도 있지만 이 프로세스에는 추가 단계가 필요합니다. 

Azure 인증을 할 수 있도록 게시 프로필 자격 증명이나 서비스 주체를 [GitHub 비밀](https://docs.github.com/en/actions/reference/encrypted-secrets)로 저장합니다. 워크플로 내의 비밀에 액세스합니다. 

# <a name="publish-profile"></a>[게시 프로필](#tab/publish-profile)

게시 프로필은 앱 수준의 자격 증명입니다. 게시 프로필을 GitHub 비밀로 설정합니다. 

1. Azure Portal에서 해당 앱 서비스로 이동합니다. 

1. **개요** 페이지에서 **게시 프로필 가져오기** 를 선택합니다.

    > [!NOTE]
    > 2020년 10월부터 Linux 웹앱에서는 앱 설정 `WEBSITE_WEBDEPLOY_USE_SCM`을 `true`로 설정해야 **파일을 다운로드** 할 수 있습니다. 이 요구 사항은 나중에 제거됩니다. 일반적인 웹앱 설정을 구성하는 방법을 알아보려면 [Azure Portal에서 App Service 앱 구성](./configure-common.md)을 참조하세요.  

1. 다운로드한 파일을 저장합니다. 파일의 내용을 사용하여 GitHub 비밀을 만듭니다.

# <a name="service-principal"></a>[서비스 주체](#tab/service-principal)

[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 사용하여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. 이 명령은 Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 실행합니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

예에서 자리 표시자를 구독 ID, 리소스 그룹 이름 및 앱 이름으로 바꿉니다. 출력은 App Service 앱에 대한 액세스를 제공하는 역할 할당 자격 증명이 있는 JSON 개체입니다. 나중에 사용할 수 있도록 이 JSON 개체를 복사합니다.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 항상 최소한의 액세스 권한을 부여하는 것이 좋습니다. 이전 예의 범위는 전체 리소스 그룹이 아닌 특정 App Service 앱으로 제한되어 있습니다.

---
## <a name="configure-the-github-secret-for-authentication"></a>인증을 위한 GitHub 비밀 구성

# <a name="publish-profile"></a>[게시 프로필](#tab/publish-profile)

[GitHub](https://github.com/)에서 리포지토리를 검색하고 **설정 > 비밀 > 새 비밀 추가** 를 선택합니다.

[앱 수준 자격 증명](#generate-deployment-credentials)을 사용하려면 다운로드한 게시 프로필 파일의 내용을 비밀의 값 필드에 붙여넣습니다. 비밀 `AZURE_WEBAPP_PUBLISH_PROFILE`의 이름을 지정합니다.

GitHub 워크플로를 구성하는 경우에는 Azure 웹앱 배포 작업에서 `AZURE_WEBAPP_PUBLISH_PROFILE`을 사용합니다. 예를 들면 다음과 같습니다.
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[서비스 주체](#tab/service-principal)

[GitHub](https://github.com/)에서 리포지토리를 검색하고 **설정 > 비밀 > 새 비밀 추가** 를 선택합니다.

[사용자 수준 자격 증명](#generate-deployment-credentials)을 사용하려면 Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 붙여넣습니다. `AZURE_CREDENTIALS`와 같은 이름으로 비밀을 지정합니다.

나중에 워크플로 파일을 구성할 때 이 비밀을 Azure 로그인 작업의 `creds` 입력에 사용합니다. 예를 들면 다음과 같습니다.

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>레지스트리에 대한 GitHub 비밀 구성

Docker 로그인 작업에 사용할 비밀을 정의합니다. 이 문서의 예는 컨테이너 레지스트리로 Azure Container Registry를 사용합니다. 

1. Azure Portal 또는 Docker의 컨테이너로 이동하여 사용자 이름 및 비밀을 복사합니다. 레지스트리에 대한 **설정** > **액세스 키** 아래의 Azure Portal에서 Azure Container Registry의 사용자 이름 및 암호를 찾을 수 있습니다. 

2. `REGISTRY_USERNAME`이라는 레지스트리 사용자 이름에 대해 새 비밀을 정의합니다. 

3. `REGISTRY_PASSWORD`이라는 레지스트리 암호에 대해 새 비밀을 정의합니다. 

## <a name="build-the-container-image"></a>컨테이너 이미지 빌드

다음 예에서는 Node.JS Docker 이미지를 작성하는 워크플로의 일부를 보여 줍니다. [Docker 로그인](https://github.com/azure/docker-login)을 사용하여 프라이빗 컨테이너 레지스트리에 로그인합니다. 이 예에서는 Azure Container Registry를 사용하지만 다른 레지스트리를 사용해도 됩니다. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

[Docker 로그인](https://github.com/azure/docker-login)을 사용하여 동시에 여러 컨테이너 레지스트리에 로그인할 수도 있습니다. 이 예에는 docker.io 인증을 위한 두 가지 새로운 GitHub 비밀이 포함되어 있습니다. 이 예에서는 레지스트리의 루트 수준에 Dockerfile이 있다고 가정합니다. 

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>Azure App Service 컨테이너에 배포

App Service의 사용자 지정 컨테이너에 이미지를 배포하려면 `azure/webapps-deploy@v2` 작업을 사용합니다. 이 작업에는 다음 7개의 매개 변수가 있습니다.

| **매개 변수**  | **설명**  |
|---------|---------|
| **app-name** | (필수) App Service 앱의 이름 | 
| **publish-profile** | (선택 사항) 웹앱(Windows 및 Linux) 및 웹앱 컨테이너(Linux)에 적용됩니다. 다중 컨테이너 시나리오는 지원되지 않습니다. 웹 배포 비밀을 포함하는 프로필(\*.publishsettings) 파일 콘텐츠 게시 | 
| **slot-name** | (선택 사항) 프로덕션 슬롯이 아닌 기존 슬롯 입력 |
| **package** | (선택 사항) 웹앱에만 적용: 패키지 또는 폴더에 대한 경로입니다. \*.zip, \*.war, \*.jar 또는 배포할 폴더 |
| **images** | (필수) 웹앱 컨테이너에만 적용: 정규화된 컨테이너 이미지 이름을 지정합니다. 예: 'myregistry.azurecr.io/nginx:latest' or 'python:3.7.2-alpine/'. 다중 컨테이너 앱의 경우 여러 개의 컨테이너 이미지 이름이 제공될 수 있습니다(여러 줄로 구분). |
| **configuration-file** | (선택 사항) 필드 웹앱 컨테이너에만 적용: Docker-Compose 파일 경로입니다. 이는 정규화된 경로이거나 기본 작업 디렉터리에 대한 상대적인 경로여야 합니다. 다중 컨테이너 앱에 필요합니다. |
| **startup-command** | (선택 사항) 필드 시작 명령을 입력합니다. 예: dotnet run 또는 dotnet filename.dll |

# <a name="publish-profile"></a>[게시 프로필](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[서비스 주체](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>다음 단계

GitHub에서 다양한 리포지토리로 그룹화된 일련의 작업을 찾을 수 있습니다. 각 리포지토리에는 CI/CD에 GitHub를 사용하고 Azure에 앱을 배포하는 데 도움이 되는 문서 및 예제가 포함됩니다.

- [Azure에 배포할 작업 워크플로](https://github.com/Azure/actions-workflow-samples)

- [Azure 로그인](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker 로그인/로그아웃](https://github.com/Azure/docker-login)

- [워크플로를 트리거하는 이벤트](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s 배포](https://github.com/Azure/k8s-deploy)

- [시작 워크플로](https://github.com/actions/starter-workflows)
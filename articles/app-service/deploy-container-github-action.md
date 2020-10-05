---
title: GitHub 작업의 사용자 지정 컨테이너 CI/CD
description: GitHub 작업을 사용 하 여 CI/CD 파이프라인에서 App Service 하는 사용자 지정 Linux 컨테이너를 배포 하는 방법을 알아봅니다.
ms.devlang: na
ms.topic: article
ms.date: 10/03/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: dc8b5e75b4feed886f843e7a516cc18429afec11
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91728491"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>GitHub 작업을 사용 하 여 App Service 사용자 지정 컨테이너 배포

[GitHub 작업](https://help.github.com/en/articles/about-github-actions) 을 통해 자동화 된 소프트웨어 개발 워크플로를 유연 하 게 빌드할 수 있습니다. [Azure 웹 배포 작업](https://github.com/Azure/webapps-deploy)을 사용 하면 GitHub 작업을 사용 하 여 사용자 지정 컨테이너를 [App Service](overview.md) 에 배포 하는 워크플로를 자동화할 수 있습니다.

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의에는 워크플로에 포함 된 다양 한 단계 및 매개 변수가 포함 되어 있습니다.

Azure App Service 컨테이너 워크플로의 경우 파일에는 다음과 같은 세 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체 또는 게시 프로필을 검색 합니다. <br /> 2. GitHub 비밀을 만듭니다. |
|**빌드** | 1. 환경을 만듭니다. <br /> 2. 컨테이너 이미지를 빌드합니다. |
|**배포** | 1. 컨테이너 이미지를 배포 합니다. |

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [무료 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- GitHub 계정. 없는 경우 [무료로](https://github.com/join)등록 하세요.  
- 컨테이너에 대 한 작업 컨테이너 레지스트리 및 Azure App Service 앱입니다. 이 예제에서는 Azure Container Registry를 사용 합니다. 
    - [Docker를 사용 하 여 컨테이너 화 된 Node.js 응용 프로그램을 만들고 컨테이너 이미지를 레지스트리에 푸시한 다음에 이미지를 배포 하는 방법을 알아봅니다 Azure App Service](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01)

## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성

GitHub 작업에 대해 Azure 앱 서비스를 사용 하 여 인증 하는 권장 방법은 게시 프로필을 사용 하는 것입니다. 서비스 주체를 사용 하 여 인증할 수도 있지만이 프로세스에는 추가 단계가 필요 합니다. 

Azure를 인증 하기 위해 게시 프로필 자격 증명 또는 서비스 주체를 [GitHub 암호로](https://docs.github.com/en/actions/reference/encrypted-secrets) 저장 합니다. 워크플로 내에서 비밀에 액세스 합니다. 

# <a name="publish-profile"></a>[프로필 게시](#tab/publish-profile)

게시 프로필은 앱 수준 자격 증명입니다. 게시 프로필을 GitHub 암호로 설정 합니다. 

1. Azure Portal에서 app service로 이동 합니다. 

1. **개요** 페이지에서 **게시 프로필 가져오기**를 선택 합니다.

1. 다운로드한 파일을 저장합니다. 파일의 내용을 사용 하 여 GitHub 비밀을 만듭니다.

# <a name="service-principal"></a>[서비스 주체](#tab/service-principal)

[Azure CLI](/cli/azure/)에서 [az ad sp create-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) 명령을 사용 하 여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 를 만들 수 있습니다. Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/) 또는 **사용해 보기** 단추를 선택 하 여이 명령을 실행 합니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

이 예제에서는 자리 표시자를 구독 ID, 리소스 그룹 이름 및 앱 이름으로 바꿉니다. 출력은 App Service 앱에 대 한 액세스를 제공 하는 역할 할당 자격 증명을 포함 하는 JSON 개체입니다. 나중에이 JSON 개체를 복사 합니다.

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
> 항상 최소한의 액세스 권한을 부여하는 것이 좋습니다. 이전 예제의 범위는 전체 리소스 그룹이 아니라 특정 App Service 앱으로 제한 됩니다.

---

## <a name="configure-the-github-secret"></a>GitHub 비밀 구성

[GitHub](https://github.com/)에서 리포지토리를 찾아보고 **설정 > 비밀을 선택 하 > 새 비밀을 추가**합니다.

JSON 출력의 내용을 비밀 변수의 값으로 붙여넣습니다. 암호에와 같은 이름을 지정 합니다 `AZURE_CREDENTIALS` .

나중에 워크플로 파일을 구성 하는 경우 `creds` Azure 로그인 동작의 입력에 대 한 암호를 사용 합니다. 예를 들면 다음과 같습니다.

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="configure-the-github-secret-for-authentication"></a>인증을 위한 GitHub 암호 구성

# <a name="publish-profile"></a>[프로필 게시](#tab/publish-profile)

[GitHub](https://github.com/)에서 리포지토리를 찾아보고 **설정 > 비밀을 선택 하 > 새 비밀을 추가**합니다.

[앱 수준 자격 증명](#generate-deployment-credentials)을 사용 하려면 다운로드 한 게시 프로필 파일의 내용을 비밀의 값 필드에 붙여넣습니다. 비밀의 이름을로 `AZURE_WEBAPP_PUBLISH_PROFILE` 합니다.

GitHub 워크플로를 구성 하는 경우 `AZURE_WEBAPP_PUBLISH_PROFILE` Azure 웹 앱 배포 작업에서를 사용 합니다. 예를 들면 다음과 같습니다.
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[서비스 주체](#tab/service-principal)

[GitHub](https://github.com/)에서 리포지토리를 찾아보고 **설정 > 비밀을 선택 하 > 새 비밀을 추가**합니다.

[사용자 수준 자격 증명](#generate-deployment-credentials)을 사용 하려면 Azure CLI 명령의 전체 JSON 출력을 암호의 값 필드에 붙여넣습니다. 암호에와 같은 이름을 지정 합니다 `AZURE_CREDENTIALS` .

나중에 워크플로 파일을 구성 하는 경우 `creds` Azure 로그인 동작의 입력에 대 한 암호를 사용 합니다. 예를 들면 다음과 같습니다.

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>레지스트리에 대 한 GitHub 암호 구성

Docker 로그인 작업에 사용할 암호를 정의 합니다. 

1. Azure Portal 또는 Docker의 컨테이너로 이동 하 여 사용자 이름 및 암호를 복사 합니다. 

2. 라는 레지스트리 사용자 이름에 대해 새 암호를 정의 `REGISTRY_USERNAME` 합니다. 

3. 이라는 레지스트리 암호에 대 한 새 암호를 정의 `REGISTRY_PASSWORD` 합니다. 

## <a name="build-the-container-image"></a>컨테이너 이미지 빌드

다음 예제에서는 Node.JS Docker 이미지를 작성 하는 워크플로의 일부를 보여 줍니다. [Docker 로그인](https://github.com/azure/docker-login) 을 사용 하 여 개인 컨테이너 레지스트리에 로그인 합니다. 이 예제에서는 Azure Container Registry를 사용 하지만 다른 레지스트리에서 동일한 작업이 작동 합니다. 

# <a name="publish-profile"></a>[프로필 게시](#tab/publish-profile)

이 예제에서는 인증을 위해 게시 프로필을 사용 하 여 Node.JS Docker 이미지를 빌드하는 방법을 보여 줍니다.


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

[Docker 로그인](https://github.com/azure/docker-login) 을 사용 하 여 동시에 여러 컨테이너 레지스트리에 로그인 할 수도 있습니다. 이 예에는 docker.io 인증을 위한 두 가지 새로운 GitHub 비밀이 포함 되어 있습니다.

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
# <a name="service-principal"></a>[서비스 주체](#tab/service-principal)

이 예제에서는 인증을 위해 서비스 주체를 사용 하 여 Node.JS Docker 이미지를 빌드하는 방법을 보여 줍니다. 

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master

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
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="deploy-to-an-app-service-container"></a>App Service 컨테이너에 배포

App Service의 사용자 지정 컨테이너에 이미지를 배포 하려면 작업을 사용 `azure/webapps-deploy@v2` 합니다. 이 작업에는 5 개의 매개 변수가 있습니다.

| **매개 변수**  | **설명**  |
|---------|---------|
| **app-name** | (필수) App Service 앱의 이름 | 
| **publish-profile** | (선택 사항) 웹 배포 비밀을 포함하는 게시 프로필 파일 콘텐츠 |
| **images** | 정규화 된 컨테이너 이미지 이름입니다. 예: ' myregistry.azurecr.io/nginx:latest ' 또는 ' python: 3.7.2-알파인/'. 다중 컨테이너 시나리오의 경우 여러 컨테이너 이미지 이름을 제공할 수 있습니다 (여러 줄로 구분). |
| **slot-name** | (선택 사항) 프로덕션 슬롯이 아닌 기존 슬롯 입력 |
| **구성-파일** | 필드 Docker 작성 파일의 경로 |

# <a name="publish-profile"></a>[프로필 게시](#tab/publish-profile)

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
      uses: actions/checkout@master
    
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

- [워크플로를 트리거하는 이벤트](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 배포](https://github.com/Azure/k8s-deploy)

- [시작 워크플로](https://github.com/actions/starter-workflows)
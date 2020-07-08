---
title: GitHub 작업의 사용자 지정 컨테이너 CI/CD
description: GitHub 작업을 사용 하 여 CI/CD 파이프라인에서 App Service 하는 사용자 지정 Linux 컨테이너를 배포 하는 방법을 알아봅니다.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78246964"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>GitHub 작업을 사용 하 여 App Service 사용자 지정 컨테이너 배포

[GitHub Actions](https://help.github.com/en/articles/about-github-actions)를 사용하면 자동화된 소프트웨어 개발 수명 주기 워크플로를 유연성 있게 빌드할 수 있습니다. [컨테이너에 대 한 Azure App Service 작업](https://github.com/Azure/webapps-container-deploy)을 사용 하면 GitHub 작업을 사용 하 여 [App Service에 앱을 사용자 지정 컨테이너로](https://azure.microsoft.com/services/app-service/containers/) 배포 하도록 워크플로를 자동화할 수 있습니다.

> [!IMPORTANT]
> GitHub Actions는 현재 베타 버전입니다. 먼저 GitHub 계정을 사용하여 [미리 보기에 조인하려면 가입](https://github.com/features/actions)해야 합니다.
> 

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다.

Azure App Service 컨테이너 워크플로의 경우 파일에는 다음과 같은 세 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체를 정의 합니다. <br /> 2. GitHub 비밀을 만듭니다. |
|**빌드** | 1. 환경을 설정 합니다. <br /> 2. 컨테이너 이미지를 빌드합니다. |
|**배포** | 1. 컨테이너 이미지를 배포 합니다. |

## <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI](https://docs.microsoft.com/cli/azure/)에서 [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용하여 [서비스 주체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)를 만들 수 있습니다. Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 이 명령을 실행할 수 있습니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

출력은 아래와 같이 App Service 앱에 대 한 액세스를 제공 하는 역할 할당 자격 증명을 포함 하는 JSON 개체입니다. GitHub에서 인증 하려면이 JSON 개체를 복사 합니다.

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
> 항상 최소한의 액세스 권한을 부여하는 것이 좋습니다. 위의 Az CLI 명령에서 범위를 특정 App Service 앱 및 컨테이너 이미지가 푸시되는 Azure Container Registry으로 제한할 수 있습니다.

## <a name="configure-the-github-secret"></a>GitHub 비밀 구성

아래 예제에서는 배포에 대 한 Azure 서비스 주체와 같은 사용자 수준 자격 증명을 사용 합니다. 비밀을 구성하는 단계를 수행합니다.

1. [GitHub](https://github.com/)에서 리포지토리를 검색하고 **설정 > 비밀 > 새 비밀 추가**를 선택합니다.

2. 아래 `az cli` 명령의 내용을 비밀 변수 값으로 붙여넣습니다. 예: `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. 이제 분기의 워크플로 파일에서 `.github/workflows/workflow.yml` Azure 로그인 동작의 암호를 비밀로 바꿉니다.

4. 마찬가지로 컨테이너 레지스트리 자격 증명에 대해 다음과 같은 추가 암호를 정의 하 고 Docker 로그인 작업에서 설정 합니다. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. 정의 되 면 아래와 같이 비밀이 표시 됩니다.

    ![컨테이너 비밀](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>컨테이너 이미지 빌드

다음 예제에서는 docker 이미지를 작성 하는 워크플로의 일부를 보여 줍니다.

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>App Service 컨테이너에 배포

App Service의 사용자 지정 컨테이너에 이미지를 배포 하려면 작업을 사용 `azure/webapps-container-deploy@v1` 합니다. 이 작업에는 5 개의 매개 변수가 있습니다.

| **매개 변수**  | **설명**  |
|---------|---------|
| **app-name** | (필수) App Service 앱의 이름 | 
| **slot-name** | (선택 사항) 프로덕션 슬롯이 아닌 기존 슬롯 입력 |
| **images** | 하다 정규화 된 컨테이너 이미지 이름을 지정 합니다. 예: ' myregistry.azurecr.io/nginx:latest ' 또는 ' python: 3.7.2-알파인/'. 다중 컨테이너 앱의 경우 여러 개의 컨테이너 이미지 이름을 제공할 수 있습니다 (여러 줄로 구분). |
| **구성-파일** | 필드 Docker 작성 파일의 경로입니다. 는 정규화 된 경로 이거나 기본 작업 디렉터리에 대 한 상대 경로 여야 합니다. 다중 컨테이너 앱에 필요 합니다. |
| **컨테이너-명령** | 필드 시작 명령을 입력 합니다. 예: dotnet 실행 또는 dotnet filename.dll |

App Service의 사용자 지정 컨테이너에 Node.js 앱을 빌드하고 배포 하는 샘플 워크플로는 다음과 같습니다.

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>다음 단계

GitHub에서 다양한 리포지토리로 그룹화된 일련의 작업을 찾을 수 있습니다. 각 리포지토리에는 CI/CD에 GitHub를 사용하고 Azure에 앱을 배포하는 데 도움이 되는 문서 및 예제가 포함됩니다.

- [Azure 로그인](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [컨테이너용 Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker 로그인/로그아웃](https://github.com/Azure/docker-login)

- [워크플로를 트리거하는 이벤트](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 배포](https://github.com/Azure/k8s-deploy)

- [스타터 CI 워크플로](https://github.com/actions/starter-workflows)

- [Azure에 배포할 시작 워크플로](https://github.com/Azure/actions-workflow-samples)

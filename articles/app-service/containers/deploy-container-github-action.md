---
title: GitHub 작업의 사용자 지정 컨테이너 CI/CD
description: GitHub 작업을 사용하여 CI/CD 파이프라인에서 사용자 지정 Linux 컨테이너를 앱 서비스에 배포하는 방법을 알아봅니다.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246964"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>GitHub 작업을 사용하여 앱 서비스에 사용자 지정 컨테이너 배포

[GitHub 작업은](https://help.github.com/en/articles/about-github-actions) 자동화된 소프트웨어 개발 수명 주기 워크플로를 구축할 수 있는 유연성을 제공합니다. [컨테이너에 대한 Azure 앱 서비스 작업을](https://github.com/Azure/webapps-container-deploy)사용하면 GitHub 작업을 사용하여 앱 서비스에 사용자 지정 컨테이너로 앱을 배포하도록 워크플로를 [자동화할](https://azure.microsoft.com/services/app-service/containers/) 수 있습니다.

> [!IMPORTANT]
> GitHub 작업은 현재 베타 버전입니다. 먼저 GitHub 계정을 사용하여 [미리 보기에 가입해야](https://github.com/features/actions) 합니다.
> 

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에 의해 정의됩니다. 이 정의에는 워크플로를 구성하는 다양한 단계와 매개 변수가 포함되어 있습니다.

Azure App Service 컨테이너 워크플로의 경우 파일에는 세 가지 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체를 정의합니다. <br /> 2. GitHub 비밀을 만듭니다. |
|**빌드** | 1. 환경을 설정합니다. <br /> 2. 컨테이너 이미지를 빌드합니다. |
|**배포** | 1. 컨테이너 이미지를 배포합니다. |

## <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI에서](https://docs.microsoft.com/cli/azure/) [az 광고 sp 만들기-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용 하 여 [서비스 주체를](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) 만들 수 있습니다. Azure 포털에서 [Azure 클라우드 셸을](https://shell.azure.com/) 사용하거나 **시도** 단추를 선택하여 이 명령을 실행할 수 있습니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

출력은 아래와 유사한 앱 서비스 앱에 대한 액세스를 제공하는 역할 할당 자격 증명이 있는 JSON 개체입니다. 이 JSON 개체를 복사하여 GitHub에서 인증합니다.

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
> 항상 최소 액세스 권한을 부여하는 것이 좋습니다. 위의 Az CLI 명령의 범위를 특정 앱 서비스 앱및 컨테이너 이미지가 푸시되는 Azure 컨테이너 레지스트리로 제한할 수 있습니다.

## <a name="configure-the-github-secret"></a>GitHub 비밀 구성

아래 예제에서는 배포에 대 한 Azure 서비스 보안 주체와 같은 사용자 수준 자격 증명을 사용합니다. 단계를 수행하여 비밀을 구성합니다.

1. [GitHub에서](https://github.com/)리포지토리를 탐색하고 **비밀 > > 새 비밀 추가를** 선택합니다.

2. 아래 `az cli` 명령의 내용을 비밀 변수의 값으로 붙여 넣습니다. `AZURE_CREDENTIALS`)을 입력합니다.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. 이제 분기의 워크플로 파일에서 Azure 로그인 작업의 비밀을 비밀로 `.github/workflows/workflow.yml` 바꿉습니다.

4. 마찬가지로 컨테이너 레지스트리 자격 증명에 대한 다음과 같은 추가 암호를 정의하고 Docker 로그인 작업에서 설정합니다. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. 한 번 정의 된 아래와 같이 비밀을 볼 수 있습니다.

    ![컨테이너 비밀](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>컨테이너 이미지 빌드

다음 예제는 docker 이미지를 빌드하는 워크플로의 일부를 보여 주습니다.

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

## <a name="deploy-to-an-app-service-container"></a>앱 서비스 컨테이너에 배포

App Service의 사용자 지정 컨테이너에 이미지를 `azure/webapps-container-deploy@v1` 배포하려면 작업을 사용합니다. 이 작업에는 다섯 가지 매개 변수가 있습니다.

| **매개 변수**  | **설명**  |
|---------|---------|
| **앱 이름** | (필수) 앱 서비스 앱 이름 | 
| **슬롯 이름** | (선택 사항) 프로덕션 슬롯 이외의 기존 슬롯 입력 |
| **이미지** | (필수) 정규화된 컨테이너 이미지를 지정합니다. 예를 들어 'myregistry.azurecr.io/nginx:latest' 또는 '파이썬:3.7.2-알파인/'. 다중 컨테이너 앱의 경우 여러 컨테이너 이미지 이름을 제공할 수 있습니다(다중 줄 구분). |
| **구성 파일** | (선택 사항) 도커-컴포지션 파일의 경로입니다. 정규화된 경로이거나 기본 작업 디렉터리와 관련이 있어야 합니다. 다중 컨테이너 앱에 필요합니다. |
| **컨테이너 명령** | (선택 사항) 시작 명령을 입력합니다. 예의. 닷컴 런 또는 닷넷 파일name.dll |

다음은 Node.js 앱을 빌드하고 앱 서비스의 사용자 지정 컨테이너에 배포하는 샘플 워크플로입니다.

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

Ci/CD에 GitHub를 사용하고 Azure에 앱을 배포하는 데 도움이 되는 설명서와 예제가 포함된 각 작업 집합은 GitHub의 다른 리포지토리로 그룹화되어 있습니다.

- [Azure 로그인](https://github.com/Azure/login)

- [Azure 웹앱](https://github.com/Azure/webapps-deploy)

- [컨테이너용 Azure 웹앱](https://github.com/Azure/webapps-container-deploy)

- [도커 로그인/로그아웃](https://github.com/Azure/docker-login)

- [워크플로를 트리거하는 이벤트](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 배포](https://github.com/Azure/k8s-deploy)

- [시작 CI 워크플로우](https://github.com/actions/starter-workflows)

- [Azure에 배포할 시작 워크플로](https://github.com/Azure/actions-workflow-samples)

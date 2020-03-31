---
title: GitHub 작업을 사용하여 Azure Kubernetes 서비스에 컨테이너 빌드, 테스트 및 배포
description: GitHub 작업을 사용하여 Kubernetes에 컨테이너를 배포하는 방법 알아보기
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595368"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Kubernetes 서비스에 배포하기 위한 GitHub 작업

[GitHub 작업은](https://help.github.com/en/articles/about-github-actions) 자동화된 소프트웨어 개발 수명 주기 워크플로를 구축할 수 있는 유연성을 제공합니다. Kubernetes 작업은 [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) Azure Kubernetes 서비스 클러스터에 대한 배포를 용이하게 합니다. 이 작업은 [azure/k8s 배포,](https://github.com/Azure/k8s-deploy/tree/master) [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) 등과 같은 다른 작업에서 사용하거나 kubectl 명령을 실행할 수 있는 대상 AKS 클러스터 컨텍스트를 설정합니다.

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에 의해 정의됩니다. 이 정의에는 워크플로를 구성하는 다양한 단계와 매개 변수가 포함되어 있습니다.

AKS를 대상으로 하는 워크플로의 경우 파일에는 세 가지 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 개인 컨테이너 레지스트리(ACR)에 로그인 |
|**빌드** | 컨테이너 이미지를 푸시하기 & 빌드  |
|**배포** | 1. 대상 AKS 클러스터 설정 |
| |2. Kubernetes 클러스터에서 일반/도커 레지스트리 비밀 만들기  |
||3. Kubernetes 클러스터에 배포|

## <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI에서](https://docs.microsoft.com/cli/azure/) [az 광고 sp 만들기-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용 하 여 [서비스 주체를](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) 만들 수 있습니다. Azure 포털에서 [Azure 클라우드 셸을](https://shell.azure.com/) 사용하거나 **시도** 단추를 선택하여 이 명령을 실행할 수 있습니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

위의 명령에서 자리 표시자를 구독 ID 및 리소스 그룹으로 바꿉습니다. 출력은 리소스에 대한 액세스를 제공하는 역할 할당 자격 증명입니다. 명령은 이와 유사한 JSON 개체를 출력해야 합니다.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
GitHub에서 인증하는 데 사용할 수 있는 이 JSON 개체를 복사합니다.

## <a name="configure-the-github-secrets"></a>GitHub 비밀 구성

단계에 따라 비밀을 구성합니다.

1. [GitHub에서](https://github.com/)리포지토리로 검색하여 > 비밀 설정을 > **새 비밀 추가를**선택합니다.

    ![secrets](media/kubernetes-action/secrets.png)

2. 위의 `az cli` 명령의 내용을 비밀 변수의 값으로 붙여 넣습니다. `AZURE_CREDENTIALS`)을 입력합니다.

3. 마찬가지로 컨테이너 레지스트리 자격 증명에 대한 다음과 같은 추가 암호를 정의하고 Docker 로그인 작업에서 설정합니다. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 아래와 같이 한 번 정의된 대로 비밀이 표시됩니다.

    ![쿠버네츠-비밀](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>컨테이너 이미지 빌드 및 Azure Kubernetes 서비스 클러스터에 배포

컨테이너 이미지의 빌드 및 푸시는 `Azure/docker-login@v1` 작업을 사용하여 수행됩니다. 컨테이너 이미지를 AKS에 배포하려면 `Azure/k8s-deploy@v1` 작업을 사용해야 합니다. 이 작업에는 다섯 가지 매개 변수가 있습니다.

| **매개 변수**  | **설명**  |
|---------|---------|
| **namespace** | (선택 사항) 대상 Kubernetes 네임스페이스를 선택합니다. 네임스페이스가 제공되지 않으면 명령이 기본 네임스페이스에서 실행됩니다. | 
| **매니페스트** |  (필수) 배포에 사용되는 매니페스트 파일에 대한 경로 |
| **이미지** | (선택 사항) 매니페스트 파일의 대체에 사용할 이미지의 정규화된 리소스 URL |
| **이미지 풀 시크릿** | (선택 사항) 클러스터 내에 이미 설정된 도커 레지스트리 보안 정보의 이름입니다. 이러한 각 비밀 이름은 입력 매니페스트 파일에 있는 워크로드에 대한 imagePullSecrets 필드 아래에 추가됩니다. |
| **쿠베틀 버전** | (선택 사항) kubectl 바이너리의 특정 버전을 설치 |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Azure Kubernetes 서비스 클러스터에 배포

컨테이너 이미지를 빌드하고 Azure Kubernetes 서비스 클러스터에 배포하기 위한 종단 간 워크플로우입니다.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>다음 단계

Ci/CD에 GitHub를 사용하고 Azure에 앱을 배포하는 데 도움이 되는 설명서와 예제가 포함된 각 작업 집합은 GitHub의 다른 리포지토리에서 찾을 수 있습니다.

- [설정-쿠베틀](https://github.com/Azure/setup-kubectl)

- [k8s 세트 컨텍스트](https://github.com/Azure/k8s-set-context)

- [aks 세트 컨텍스트](https://github.com/Azure/aks-set-context)

- [k8s-create-secret](https://github.com/Azure/k8s-create-secret)

- [k8s 배포](https://github.com/Azure/k8s-deploy)

- [웹앱-컨테이너 배포](https://github.com/Azure/webapps-container-deploy)

- [작업 워크플로- 샘플](https://github.com/Azure/actions-workflow-samples)

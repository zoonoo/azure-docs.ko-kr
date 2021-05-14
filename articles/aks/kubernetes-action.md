---
title: GitHub Actions를 사용하여 Azure Kubernetes Service에 컨테이너 빌드, 테스트 및 배포
description: GitHub Actions를 사용하여 Kubernetes에 컨테이너를 배포하는 방법을 알아봅니다.
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 3a8e91f74fe3c862a814d7660e64748df9553f1d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779762"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Kubernetes 서비스에 배포하기 위한 GitHub Actions

[GitHub Actions](https://docs.github.com/en/actions)를 사용하면 자동화된 소프트웨어 개발 수명 주기 워크플로를 유연성 있게 빌드할 수 있습니다. GitHub Actions를 사용하여 Azure Container Registry에서 컨테이너를 Azure Kubernetes Service에 배포하는 여러 Kubernetes 작업을 사용할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항 

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요.  
- 작동하는 Kubernetes 클러스터
    - [자습서: Azure Kubernetes Service에 대한 애플리케이션 준비](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>워크플로 파일 개요

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다.

AKS를 대상으로 하는 워크플로에 대한 파일에는 다음 세 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | ACR(프라이빗 컨테이너 레지스트리)에 로그인 |
|**빌드** | 컨테이너 이미지 빌드 및 푸시  |
|**배포** | 1. 대상 AKS 클러스터 설정 |
| |2. Kubernetes 클러스터에서 generic/docker-registry 비밀 만들기  |
||3. Kubernetes 클러스터에 배포|

## <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 사용하여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 이 명령을 실행할 수 있습니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

위의 명령에서 자리 표시자를 구독 ID 및 리소스 그룹으로 바꿉니다. 리소스에 대한 액세스 권한을 제공하는 역할 할당 자격 증명이 출력됩니다. 명령은 다음과 유사한 JSON 개체를 출력해야 합니다.

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

다음 단계에 따라 비밀을 구성합니다.

1. [GitHub](https://github.com/)에서 리포지토리로 이동하고 **설정 > 비밀 > 새 비밀 추가** 를 선택합니다.

    ![리포지토리에 대한 새 비밀 추가 링크를 보여 주는 스크린샷](media/kubernetes-action/secrets.png)

2. 위 `az cli` 명령의 내용을 비밀 변수의 값으로 붙여넣습니다. 예: `AZURE_CREDENTIALS`.

3. 마찬가지로 컨테이너 레지스트리 자격 증명에 대해 다음과 같은 추가 비밀을 정의하고 Docker 로그인 작업에서 설정합니다. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 비밀은 정의되면 다음과 같이 표시됩니다.

    ![리포지토리에 대한 기존 비밀을 보여 주는 스크린샷](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>컨테이너 이미지를 빌드하고 Azure Kubernetes Service 클러스터에 배포합니다.

컨테이너 이미지의 빌드 및 푸시는 `Azure/docker-login@v1` 작업을 사용하여 수행됩니다. 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터에 배포

AKS에 컨테이너 이미지를 배포하려면 `Azure/k8s-deploy@v1` 작업을 사용해야 합니다. 이 작업에는 다음 5가지 매개 변수가 있습니다.

| **매개 변수**  | **설명**  |
|---------|---------|
| **namespace** | (선택 사항) 대상 Kubernetes 네임스페이스를 선택합니다. 네임스페이스를 제공하지 않으면 명령은 기본 네임스페이스에서 실행됩니다. | 
| **manifests** |  (필수) 배포에 사용될 매니페스트 파일의 경로입니다. |
| **images** | (선택 사항) 매니페스트 파일에서 대체에 사용할 이미지의 정규화된 리소스 URL입니다. |
| **imagepullsecrets** | (선택 사항) 클러스터 내에 이미 설정된 docker-registry 비밀의 이름입니다. 이러한 각 비밀 이름은 입력 매니페스트 파일에 있는 워크로드에 대한 imagePullSecrets 필드 아래에 추가됩니다. |
| **kubectl-version** | (선택 사항) 특정 버전의 kubectl 이진을 설치합니다. |


AKS에 배포하려면 먼저 대상 Kubernetes 네임스페이스를 설정하고 이미지 풀(pull)하기 비밀을 만들어야 합니다. 이미지 풀의 작동 방식에 대해 자세히 알아보려면 [Azure 컨테이너 레지스트리에서 Kubernetes 클러스터로 이미지 풀(pull)하기](../container-registry/container-registry-auth-kubernetes.md)를 참조하세요. 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```


`k8s-deploy` 작업으로 배포를 완료합니다. 환경 변수를 애플리케이션에 대한 값으로 바꿉니다. 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>리소스 정리

Kubernetes 클러스터, 컨테이너 레지스트리, 리포지토리가 더 이상 필요하지 않은 경우 리소스 그룹과 GitHub 리포지토리를 삭제하여 배포한 리소스를 정리합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Kubernetes Service에 대해 알아보기](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>추가 Kubernetes GitHub Actions

* [Kubectl 도구 설치 관리자](https://github.com/Azure/setup-kubectl)(`azure/setup-kubectl`): 러너에 특정 버전의 kubectl을 설치합니다.
* [Kubernetes set context](https://github.com/Azure/k8s-set-context)(`azure/k8s-set-context`): 다른 작업에서 사용되거나 kubectl 명령을 실행할 대상 Kubernetes 클러스터 컨텍스트를 설정합니다.
* [AKS set context](https://github.com/Azure/aks-set-context)(`azure/aks-set-context`): 대상 Azure Kubernetes Service 클러스터 컨텍스트를 설정합니다.
* [Kubernetes create secret](https://github.com/Azure/k8s-create-secret)(`azure/k8s-create-secret`): Kubernetes 클러스터에 generic 비밀 또는 docker-registry 비밀을 만듭니다.
* [Kubernetes deploy](https://github.com/Azure/k8s-deploy)(`azure/k8s-deploy`): 매니페스트를 베이킹하고 Kubernetes 클러스터에 배포합니다.
* [Setup Helm](https://github.com/Azure/setup-helm)(`azure/setup-helm`): 러너에 특정 버전의 Helm 이진을 설치합니다.
* [Kubernetes bake](https://github.com/Azure/k8s-bake)(`azure/k8s-bake`): helm2, kustomize 또는 kompose를 사용하여 배포에 사용할 매니페스트 파일을 베이킹합니다.
* [Kubernetes lint](https://github.com/azure/k8s-lint)(`azure/k8s-lint`): 매니페스트 파일을 유효성 검사/린트합니다.

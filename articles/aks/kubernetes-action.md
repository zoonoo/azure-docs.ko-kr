---
title: GitHub 작업을 사용 하 여 Azure Kubernetes Service에 컨테이너 빌드, 테스트 및 배포
description: GitHub 작업을 사용 하 여 컨테이너를 Kubernetes에 배포 하는 방법을 알아봅니다.
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: a0f64b0d19dd3f65d883237e9ead2c9f1303adaf
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95794782"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Kubernetes service에 배포 하는 GitHub 작업

[GitHub Actions](https://help.github.com/en/articles/about-github-actions)를 사용하면 자동화된 소프트웨어 개발 수명 주기 워크플로를 유연성 있게 빌드할 수 있습니다. 여러 Kubernetes 작업을 사용 하 여 GitHub 작업을 통해 Azure Container Registry에서 Azure Kubernetes Service로 컨테이너에 배포할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소 

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요.  
- 작동 하는 Kubernetes 클러스터
    - [자습서: Azure Kubernetes Service에 대 한 응용 프로그램 준비](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>워크플로 파일 개요

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다.

AKS를 대상으로 하는 워크플로의 경우 파일에는 다음 세 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | ACR (개인 컨테이너 레지스트리)에 로그인 합니다. |
|**빌드** | 컨테이너 이미지를 빌드 & 푸시합니다.  |
|**배포** | 1. 대상 AKS 클러스터 설정 |
| |2. Kubernetes 클러스터에서 generic/docker 레지스트리 암호 만들기  |
||3. Kubernetes 클러스터에 배포|

## <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) 명령을 사용하여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 이 명령을 실행할 수 있습니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

위의 명령에서 자리 표시자를 구독 ID 및 리소스 그룹으로 바꿉니다. 출력은 리소스에 대 한 액세스를 제공 하는 역할 할당 자격 증명입니다. 명령은 다음과 유사한 JSON 개체를 출력 해야 합니다.

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

암호를 구성 하는 단계를 수행 합니다.

1. [GitHub](https://github.com/)에서 리포지토리로 이동 하 고, **설정 > 비밀 > 선택 하 여 새 비밀을 추가** 합니다.

    ![리포지토리에 대 한 새 비밀 추가 링크를 보여 주는 스크린샷](media/kubernetes-action/secrets.png)

2. 위의 `az cli` 명령 내용을 secret 변수의 값으로 붙여넣습니다. `AZURE_CREDENTIALS`)을 입력합니다.

3. 마찬가지로 컨테이너 레지스트리 자격 증명에 대해 다음과 같은 추가 암호를 정의 하 고 Docker 로그인 작업에서 설정 합니다. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 정의 되 면 아래와 같이 비밀이 표시 됩니다.

    ![리포지토리에 대 한 기존 비밀을 보여 주는 스크린샷](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>컨테이너 이미지를 빌드하고 Azure Kubernetes Service 클러스터에 배포

컨테이너 이미지의 빌드 및 푸시는 작업을 사용 하 여 수행 됩니다 `Azure/docker-login@v1` . 


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
    - uses: actions/checkout@master
    
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

컨테이너 이미지를 AKS에 배포 하려면 작업을 사용 해야 `Azure/k8s-deploy@v1` 합니다. 이 작업에는 5 개의 매개 변수가 있습니다.

| **매개 변수**  | **설명**  |
|---------|---------|
| **namespace** | 필드 대상 Kubernetes 네임 스페이스를 선택 합니다. 네임 스페이스를 제공 하지 않으면 명령은 기본 네임 스페이스에서 실행 됩니다. | 
| **매니페스트** |  하다 배포에 사용 되는 매니페스트 파일의 경로 |
| **images** | 필드 매니페스트 파일에서 대체 하는 데 사용할 이미지의 정규화 된 리소스 URL입니다. |
| **imagepullsecrets** | 필드 클러스터 내에서 이미 설정 된 docker 레지스트리 암호의 이름입니다. 이러한 각 암호 이름은 입력 매니페스트 파일에 있는 작업에 대 한 imagePullSecrets 필드 아래에 추가 됩니다. |
| **kubectl-버전** | 필드 Kubectl binary의 특정 버전을 설치 합니다. |


AKS에 배포 하려면 먼저 대상 Kubernetes 네임 스페이스를 설정 하 고 이미지 끌어오기 암호를 만들어야 합니다. 이미지 끌어오기의 작동 방식에 대 한 자세한 내용은 [Azure container registry에서 Kubernetes 클러스터로 이미지 끌어오기](../container-registry/container-registry-auth-kubernetes.md)를 참조 하세요. 

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


작업으로 배포를 완료 `k8s-deploy` 합니다. 환경 변수를 응용 프로그램에 대 한 값으로 바꿉니다. 

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
    - uses: actions/checkout@master
    
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

Kubernetes 클러스터, 컨테이너 레지스트리 및 리포지토리가 더 이상 필요 하지 않은 경우 리소스 그룹 및 GitHub 리포지토리를 삭제 하 여 배포한 리소스를 정리 합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Kubernetes Service에 대 한 자세한 정보](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>더 많은 Kubernetes GitHub 작업

* [Kubectl tool installer](https://github.com/Azure/setup-kubectl) ( `azure/setup-kubectl` ): runner에 특정 버전의 Kubectl를 설치 합니다.
* [Kubernetes set context](https://github.com/Azure/k8s-set-context) ( `azure/k8s-set-context` ): 다른 작업에서 사용 하거나 kubectl 명령을 실행 하는 대상 Kubernetes 클러스터 컨텍스트를 설정 합니다.
* [AKS set context](https://github.com/Azure/aks-set-context) ( `azure/aks-set-context` ): 대상 Azure Kubernetes Service 클러스터 컨텍스트를 설정 합니다.
* [Kubernetes create secret](https://github.com/Azure/k8s-create-secret) ( `azure/k8s-create-secret` ): Kubernetes 클러스터에서 일반 암호 또는 docker 레지스트리 암호를 만듭니다.
* [Kubernetes deploy](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): 굽기 및 Kubernetes 클러스터에 매니페스트를 배포 합니다.
* [설치 투구](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): runner에 특정 버전의 투구 이진을 설치 합니다.
* [Kubernetes 굽기](https://github.com/Azure/k8s-bake) ( `azure/k8s-bake` ): helm2, kustomize 또는 kompose를 사용 하는 배포에 사용할 굽기 매니페스트 파일입니다.
* [Kubernetes 보푸라기가](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): 매니페스트 파일의 유효성을 검사/보풀이 없습니다.

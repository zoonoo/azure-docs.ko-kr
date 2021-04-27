---
title: GitHub 작업으로 컨테이너 인스턴스 배포
description: Azure Container Instances에 컨테이너 이미지를 빌드, 푸시 및 배포하는 단계를 자동화하는 GitHub 작업을 구성합니다.
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 1409d8fc1430cd9bf67bd735d9826a74979d495b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98762971"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>GitHub 작업을 구성하여 컨테이너 인스턴스 만들기

[GitHub Actions](https://docs.github.com/en/actions)는 코드를 저장하고 풀 요청 및 문제에 대해 공동 작업하는 것과 동일한 위치에서 소프트웨어 개발 워크플로를 자동화하는 GitHub의 기능 모음입니다.

[Azure Container Instances에 배포](https://github.com/azure/aci-deploy) GitHub 작업을 사용하여 단일 컨테이너를 Azure 컨테이너 인스턴스에 자동으로 배포합니다. 이 작업을 사용하면 [az container create][az-container-create] 명령의 속성과 유사한 컨테이너 인스턴스의 속성을 설정할 수 있습니다.

이 문서에서는 다음 작업을 수행하는 GitHub 리포지토리에서 워크플로를 설정하는 방법을 보여 줍니다.

* Dockerfile에서 이미지 빌드
* Azure 컨테이너 레지스트리에 이미지 푸시
* Azure Container Instance에 컨테이너 이미지 배포

이 문서에서는 워크플로를 설정하는 두 가지 방법을 보여 줍니다.

* [GitHub 워크플로 구성](#configure-github-workflow) - Azure Container Instances에 배포 작업 및 기타 작업을 사용하여 GitHub 리포지토리에서 워크플로를 만듭니다.  
* [CLI 확장 사용](#use-deploy-to-azure-extension) - Azure CLI의 [Azure에 배포](https://github.com/Azure/deploy-to-azure-cli-extension) 확장에서 `az container app up` 명령을 사용합니다. 이 명령은 GitHub 워크플로 만들기 및 배포 단계를 간소화합니다.

> [!IMPORTANT]
> Azure Container Instances에 대한 GitHub 작업은 현재 미리 보기 상태입니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* **GitHub 계정** - 아직 계정이 없는 경우 https://github.com 에서 계정을 만듭니다.
* **Azure CLI** - Azure Cloud Shell 또는 로컬에 설치된 Azure CLI를 사용하여 Azure CLI 단계를 완료할 수 있습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.
* **Azure 컨테이너 레지스트리** - 없는 경우 [Azure CLI](../container-registry/container-registry-get-started-azure-cli.md), [Azure Portal](../container-registry/container-registry-get-started-portal.md) 또는 기타 방법을 사용하여 기본 계층에서 Azure 컨테이너 레지스트리를 만듭니다. 배포에 사용되는 리소스 그룹을 기록해 둡니다. 이는 GitHub 워크플로에 사용됩니다.

## <a name="set-up-repo"></a>리포지토리 설정

* 이 문서의 예를 보려면 GitHub를 사용하여 https://github.com/Azure-Samples/acr-build-helloworld-node 리포지토리를 포크하세요.

  이 리포지토리에는 작은 웹앱의 컨테이너 이미지를 만드는 Dockerfile 및 원본 파일이 포함되어 있습니다.

  ![GitHub에 있는 포크 단추(강조 표시됨)의 스크린샷](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* 리포지토리에 대해 작업을 사용할 수 있는지 확인합니다. 포크된 리포지토리로 이동하여 **설정** > **작업** 을 선택합니다. **작업 권한** 에서 **이 리포지토리에 대한 로컬 및 타사 작업 사용** 이 선택되어 있는지 확인합니다.

## <a name="configure-github-workflow"></a>GitHub 워크플로 구성

### <a name="create-service-principal-for-azure-authentication"></a>Azure 인증을 위한 서비스 주체 만들기

GitHub 워크플로에서 Azure CLI에 인증하려면 Azure 자격 증명을 제공해야 합니다. 다음 예에서는 컨테이너 레지스트리의 리소스 그룹으로 범위가 지정된 기여자 역할의 서비스 주체를 만듭니다.

먼저 리소스 그룹의 리소스 ID를 가져옵니다. 다음 [az group show][az-group-show] 명령에서 그룹 이름을 대체합니다.

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

[az ad sp create-for-rbac][az-ad-sp-create-for-rbac]를 사용하여 서비스 주체를 만듭니다.

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

출력은 다음과 비슷합니다.

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

이후 단계에서 사용되므로 JSON 출력을 저장합니다. 또한 `clientId`를 기록해 둡니다. 다음 섹션에서 서비스 주체를 업데이트하는 데 필요합니다.

### <a name="update-service-principal-for-registry-authentication"></a>레지스트리 인증을 위해 서비스 주체 업데이트

컨테이너 레지스트리에 대한 푸시 및 풀 액세스를 허용하도록 Azure 서비스 주체 자격 증명을 업데이트합니다. 이 단계를 통해 GitHub 워크플로는 서비스 주체를 사용하여 [컨테이너 레지스트리로 인증](../container-registry/container-registry-auth-service-principal.md)하고 Docker 이미지를 푸시 및 풀할 수 있습니다. 

컨테이너 레지스트리의 리소스 ID를 가져옵니다. 다음 [az acr show][az-acr-show] 명령에서 레지스트리 이름을 대체합니다.

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

[az role assignment create][az-role-assignment-create]를 사용하여 레지스트리에 대한 푸시 및 풀 액세스를 제공하는 AcrPush 역할을 할당합니다. 서비스 주체의 클라이언트 ID를 대체합니다.

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>GitHub 리포지토리에 자격 증명 저장

1. GitHub UI에서 포크된 리포지토리로 이동하여 **설정** > **비밀** 을 선택합니다. 

1. 다음 비밀을 추가하려면 **새 비밀 추가** 를 선택합니다.

|비밀  |값  |
|---------|---------|
|`AZURE_CREDENTIALS`     | 서비스 주체 만들기 단계의 전체 JSON 출력 |
|`REGISTRY_LOGIN_SERVER`   | 레지스트리의 로그인 서버 이름(모두 소문자). 예: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  서비스 주체 만들기에서 JSON 출력의 `clientId`       |
|`REGISTRY_PASSWORD`     |  서비스 주체 만들기에서 JSON 출력의 `clientSecret` |
| `RESOURCE_GROUP` | 서비스 주체의 범위를 지정하는 데 사용한 리소스 그룹의 이름입니다. |

### <a name="create-workflow-file"></a>워크플로 파일 만들기

1. GitHub UI에서 **작업** > **새 워크플로** 를 선택합니다.
1. **워크플로 직접 설정** 을 선택합니다.
1. **새 파일 편집** 에서 다음 YAML 콘텐츠를 붙여넣어 샘플 코드를 덮어 씁니다. 기본 파일 이름 `main.yml`을 적용하거나 선택한 파일 이름을 제공합니다.
1. **커밋 시작** 을 선택하고 선택적으로 커밋에 대한 짧고 확장된 설명을 제공한 다음 **새 파일 커밋** 을 선택합니다.

```yml
on: [push]
name: Linux_Container_Workflow

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
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>워크플로 유효성 검사

워크플로 파일을 커밋한 후 워크플로가 트리거됩니다. 워크플로 진행 상황을 검토하려면 **작업** > **워크플로** 로 이동합니다. 

![워크플로 진행률 보기](./media/container-instances-github-action/github-action-progress.png)

워크플로에서 각 단계의 상태 및 결과를 보는 방법에 대한 자세한 내용은 [워크플로 실행 기록 보기](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history)를 참조하세요. 워크플로가 완료되지 않으면 [실패 진단을 위한 로그 보기](https://docs.github.com/en/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures)를 참조하세요.

워크플로가 성공적으로 완료되면 [az container show][az-container-show] 명령을 실행하여 *aci-sampleapp* 이라는 컨테이너 인스턴스에 대한 정보를 가져옵니다. 리소스 그룹의 이름을 대체합니다. 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

출력은 다음과 비슷합니다.

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

인스턴스가 프로비저닝된 후 브라우저에서 컨테이너의 FQDN으로 이동하여 실행 중인 웹앱을 확인합니다.

![브라우저에서 웹앱 실행](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Azure에 배포 확장 사용

또는 Azure CLI에서 [Azure에 배포 확장](https://github.com/Azure/deploy-to-azure-cli-extension)을 사용하여 워크플로를 구성합니다. 확장의 `az container app up` 명령은 Azure Container Instances에 배포할 워크플로를 설정하는 입력 매개 변수를 사용합니다. 

Azure CLI로 만들어진 워크플로는 [GitHub를 사용하여 수동으로 만들](#configure-github-workflow) 수 있는 워크플로와 유사합니다.

### <a name="additional-prerequisite"></a>추가 필수 구성 요소

이 시나리오의 [필수 구성 요소](#prerequisites) 및 [리포지토리 설정](#set-up-repo) 외에도 Azure CLI의 경우 **Azure에 배포 확장** 을 설치해야 합니다.

[az extension add][az-extension-add] 명령을 실행하여 확장을 설치합니다.

```azurecli
az extension add \
  --name deploy-to-azure
```

확장 찾기, 설치 및 관리에 대한 자세한 내용은 [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조하세요.

### <a name="run-az-container-app-up"></a>`az container app up`을 실행합니다.

[az container app up][az-container-app-up] 명령을 실행하려면 최소한 다음을 제공합니다.

* Azure 컨테이너 레지스트리의 이름(예: *myregistry*)
* GitHub 리포지토리의 URL(예: `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`)

명령 샘플:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>명령 진행률

* 메시지가 표시되면 GitHub 자격 증명을 제공하거나 GitHub 계정으로 인증할 *리포지토리* 및 *사용자* 범위가 있는 [GitHub PAT(개인용 액세스 토큰)](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token)를 제공합니다. GitHub 자격 증명을 제공하는 경우 이 명령이 PAT를 만듭니다. 추가 프롬프트에 따라 워크플로를 구성합니다.

* 이 명령은 워크플로에 대한 리포지토리 비밀을 만듭니다.

  * Azure CLI에 대한 서비스 주체 자격 증명
  * Azure 컨테이너 레지스트리에 액세스하기 위한 자격 증명

* 명령이 워크플로 파일을 리포지토리에 커밋한 후 워크플로가 트리거됩니다. 

출력은 다음과 비슷합니다.

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

GitHub UI에서 각 단계의 워크플로 상태 및 결과를 보려면 [워크플로 실행 기록 보기](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history)를 참조하세요.

### <a name="validate-workflow"></a>워크플로 유효성 검사

워크플로는 GitHub 리포지토리의 기본 이름(이 경우 *acr-build-helloworld-node*)을 사용하여 Azure 컨테이너 인스턴스를 배포합니다. 워크플로가 성공적으로 완료되면 [az container show][az-container-show] 명령을 실행하여 *cr-build-helloworld-node* 라는 컨테이너 인스턴스에 대한 정보를 가져옵니다. 리소스 그룹의 이름을 대체합니다. 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

출력은 다음과 비슷합니다.

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

인스턴스가 프로비저닝된 후 브라우저에서 컨테이너의 FQDN으로 이동하여 실행 중인 웹앱을 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

[az container delete][az-container-delete] 명령을 사용하여 컨테이너 인스턴스를 중지합니다.

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

리소스 그룹과 그 안의 모든 리소스를 삭제하려면 [az group delete][az-group-delete] 명령을 실행합니다.

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>다음 단계

개발 워크플로를 자동화하기 위한 추가 작업을 보려면 [GitHub Marketplace](https://github.com/marketplace?type=actions)에서 확인합니다.


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up

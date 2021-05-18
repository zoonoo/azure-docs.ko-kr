---
title: GitHub Actions 및 Azure Kubernetes Service(미리 보기)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: GitHub Actions 및 Azure Dev Spaces를 사용하여 Azure Kubernetes Service의 직접 끌어오기 요청에 대한 변경 내용을 검토하고 테스트합니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, GitHub Actions, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 84d4f94cdb756b0bc11026baaa3acf065604c421
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777548"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub Actions 및 Azure Kubernetes Service(미리 보기)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces는 리포지토리의 기본 분기에 끌어오기 요청을 병합하기 전에 AKS에서 직접 끌어오기 요청의 변경을 테스트할 수 있는 GitHub Actions을 사용하여 워크플로를 제공합니다. 끌어오기 요청에 대한 변경 내용을 검토하는 실행 중인 애플리케이션이 있는 경우 개발자와 팀 멤버가 모두 더 확신할 수 있습니다. 이 실행 중인 애플리케이션은 제품 관리자 및 디자이너와 같은 팀 멤버가 개발 초기 단계를 진행하면서 검토 프로세스의 일부가 될 수도 있습니다.

이 가이드에서는 다음을 수행하는 방법을 배우게 됩니다.

* Azure Dev Spaces를 Azure의 관리형 Kubernetes 클러스터에 설정합니다.
* 여러 마이크로서비스가 포함된 대규모 애플리케이션을 개발 공간에 배포합니다.
* GitHub Actions을 사용하는 CI/CD를 설정합니다.
* 전체 애플리케이션의 컨텍스트 내에서 격리된 개발 공간에서 단일 마이크로서비스를 테스트합니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치][azure-cli-installed]
* [Helm 3이 설치되었습니다][helm-installed].
* [GitHub Actions를 사용][github-actions-beta-signup]하는 GitHub 계정.
* AKS 클러스터에서 실행되는 [Azure Dev Spaces 자전거 공유 샘플 애플리케이션](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md).

## <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기

ACR(Azure Container Registry) 만들기:

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> ACR 이름은 Azure 내에서 고유해야 하며, 5~50자의 영숫자만 포함해야 합니다. 사용하는 모든 문자는 소문자여야 합니다.

이후 단계에서 사용되므로 출력에서 *loginServer* 값을 저장합니다.

## <a name="create-a-service-principal-for-authentication"></a>인증을 위한 서비스 주체 만들기

[az ad sp create-for-rbac][az-ad-sp-create-for-rbac]를 사용하여 서비스 주체를 만듭니다. 예를 들면 다음과 같습니다.

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

이후 단계에서 사용되므로 JSON 출력을 저장합니다.

[az aks show][az-aks-show]를 사용하여 AKS 클러스터의 *ID* 를 표시합니다.

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

[az acr show][az-acr-show]를 사용하여 ACR의 *ID* 를 표시합니다.

```azurecli
az acr show --name <acrName> --query id
```

[az role assignment create][az-role-assignment-create]를 사용하여 AKS 클러스터에 대한 *참가자* 액세스 및 ACR에 *AcrPush* 액세스를 제공합니다.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> 서비스 주체에게 해당 리소스에 대한 액세스 권한을 부여하려면 AKS 클러스터와 ACR 모두의 소유자여야 합니다.

## <a name="configure-your-github-action"></a>GitHub 작업 구성

> [!IMPORTANT]
> 리포지토리에 대해 GitHub 동작을 사용하도록 설정해야 합니다. 리포지토리에 대한 GitHub 동작을 사용하도록 설정하려면 GitHub에서 리포지토리로 이동하고 작업 탭을 클릭한 다음 이 리포지토리에 대한 작업을 사용하도록 설정합니다.

포크 리포지토리로 이동하고 *설정* 을 클릭합니다. 왼쪽 세로 막대에서 *비밀* 을 클릭합니다. *새 암호 추가* 를 클릭하여 아래에 새 암호를 각각 추가합니다.

1. *AZURE_CREDENTIALS*: 서비스 주체 만들기의 전체 출력입니다.
1. *RESOURCE_GROUP*: AKS 클러스터에 대한 리소스 그룹입니다(이 예제에서는 *MyResourceGroup*).
1. *CLUSTER_NAME*: AKS 클러스터의 이름(이 예제에서는 *MyAKS*)입니다.
1. *CONTAINER_REGISTRY*: ACR에 대한 *loginServer* 입니다.
1. *HOST*: 개발 공간의 호스트로 *<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>* 의 양식을 사용합니다(이 예제에서는 *dev.bikesharingweb.fedcab0987.eus.azds.io*).
1. *IMAGE_PULL_SECRET*: 사용하려는 비밀의 이름(예: *demo-secret*)입니다.
1. *MASTER_SPACE*: 부모 개발 공간의 이름으로,이 예제에서는 *dev* 입니다.
1. *REGISTRY_USERNAME*: 서비스 주체에서 만든 JSON 출력의 *clientId* 입니다.
1. *REGISTRY_PASSWORD*: 서비스 주체에서 만든 JSON 출력의 *clientSecret* 입니다.

> [!NOTE]
> 이러한 비밀은 모두 GitHub 작업에서 사용되며 [.github/workflows/bikes.yml][github-action-yaml]에서 구성됩니다.

필요에 따라 PR을 병합한 후 마스터 공간을 업데이트하려는 경우 *<MASTER_SPACE>.gateway.<HOST_SUFFIX>* 양식을 사용하는 *GATEWAY_HOST* 비밀을 추가합니다(이 예제에서는 *dev.gateway.fedcab0987.eus.azds.io*). 변경 내용을 포크의 기본 분기에 병합하면 다른 작업을 실행하여 마스터 개발 공간에서 전체 애플리케이션을 다시 빌드하고 실행합니다. 이 예제에서 마스터 공간은 *dev* 입니다. 이 작업은 [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml]에서 구성됩니다.

또한 PR의 변경 내용이 손자 공간에서 실행되도록 하려면 *MASTER_SPACE* 및 *HOST* 암호를 업데이트합니다. 예를 들어 애플리케이션이 자식 공간 *dev/azureuser1* 로 *dev* 중에 실행되는 경우 PR이 *dev/azureuser1* 의 자식 공간에서 실행되도록 하려면 다음과 같이 합니다.

* 부모 공간으로 사용할 자식 공간으로 *MASTER_SPACE* 를 업데이트합니다. 이 예제에서는 *azureuser1* 입니다.
* *HOST* 를 *<GRANDPARENT_SPACE>.<APP_NAME>.<HOST_SUFFIX>* 로 업데이트합니다. 이 예제에서는 *dev.bikesharingweb.fedcab0987.eus.azds.io* 입니다.

## <a name="create-a-new-branch-for-code-changes"></a>코드 변경에 대한 새 분기 만들기

`BikeSharingApp/`으로 이동하여 *bike-images* 라는 새 분기를 만듭니다.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

[Bikes/server.js][bikes-server-js]를 편집하여 줄 232와 233을 제거합니다.

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

이제 섹션이 다음과 같이 표시됩니다.

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

파일을 저장한 다음 `git add` 및 `git commit`을 사용하여 변경 내용을 준비합니다.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>변경 내용 푸시

`git push`를 사용하여 새 분기를 포크 리포지토리로 푸시합니다.

```cmd
git push origin bike-images
```

푸시가 완료되면 GitHub의 포크 리포지토리로 이동하여 포크 리포지토리의 *main* 분기가 *bike-images* 분기와 비교하여 기본 분기로 포함된 끌어오기 요청을 만듭니다.

끌어오기 요청이 열리면 *작업* 탭으로 이동합니다. 새 작업이 시작되었으며 *Bikes* 서비스를 구축하고 있는지 확인합니다.

## <a name="view-the-child-space-with-your-changes"></a>변경 내용이 적용된 자식 공간 보기

작업이 완료되면 끌어오기 요청의 변경 사항을 기반으로 새 자식 공간에 대한 URL이 포함된 주석이 표시됩니다.

> [!div class="mx-imgBorder"]
> ![GitHub 작업 URL](../media/github-actions/github-action-url.png)

주석에서 URL을 열어 *bikesharingweb* 서비스로 이동합니다. 사용자로 ‘Aurelia Briggs(고객)’를 선택하고 대여할 자전거를 선택합니다. 자전거에 대한 자리 표시자 이미지가 더 이상 표시되지 않는지 확인합니다.

포크의 *기본* 분기에 변경 내용을 병합하는 경우 다른 작업을 실행하여 부모 개발 공간에서 전체 애플리케이션을 다시 빌드하고 실행합니다. 이 예제에서 부모 공간은 *dev* 입니다. 이 작업은 [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml]에서 구성됩니다.

## <a name="clean-up-your-azure-resources"></a>Azure 리소스 정리

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces 작동 방식에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces의 작동 원리](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md

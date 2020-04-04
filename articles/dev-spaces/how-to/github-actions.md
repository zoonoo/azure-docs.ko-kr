---
title: GitHub 작업 & Azure Kubernetes 서비스(미리 보기)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: GitHub 작업 및 Azure 개발자 공간을 사용하여 Azure Kubernetes 서비스에서 직접 끌어오기 요청의 변경 내용 검토 및 테스트
keywords: 도커, Kubernetes, Azure, AKS, Azure Kubernetes 서비스, 컨테이너, GitHub 작업, 헬름, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
manager: gwallace
ms.openlocfilehash: a83da0ef3958748831eb0eeda1aa5e91efa7ef2e
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637938"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub 작업 & Azure Kubernetes 서비스(미리 보기)

Azure 개발자 공간은 당기기 요청이 리포지토리의 주 분기로 병합되기 전에 AKS에서 직접 끌어오기 요청의 변경 내용을 테스트할 수 있는 GitHub 작업을 사용하는 워크플로를 제공합니다. 끌어오기 요청의 변경 내용을 검토하기 위해 실행 중인 응용 프로그램을 사용하면 개발자와 팀 구성원 모두의 신뢰도를 높일 수 있습니다. 이 실행 중인 응용 프로그램은 개발 초기 단계에서 제품 관리자 및 디자이너와 같은 팀 구성원이 검토 프로세스의 일부가 되는 데도 도움이 될 수 있습니다.

이 가이드에서는 다음을 수행하는 방법을 배우게 됩니다.

* Azure Dev Spaces를 Azure의 관리형 Kubernetes 클러스터에 설정합니다.
* 여러 마이크로서비스가 포함된 대규모 애플리케이션을 개발 공간에 배포합니다.
* GitHub 작업으로 CI/CD를 설정합니다.
* 전체 애플리케이션의 컨텍스트 내에서 격리된 개발 공간에서 단일 마이크로서비스를 테스트합니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치][azure-cli-installed]
* [Helm 3이 설치되었습니다][helm-installed].
* GitHub 작업을 [사용하도록 설정한 GitHub 계정입니다.][github-actions-beta-signup]
* AKS 클러스터에서 실행되는 [Azure 개발자 공간 자전거 공유 샘플 응용 프로그램입니다.](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md)

## <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기

ACR(Azure Container Registry) 만들기:

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> ACR 이름은 Azure 내에서 고유해야 하며 5-50개의 영숫자 문자를 포함해야 합니다. 사용하는 모든 문자는 소문자여야 합니다.

*loginServer* 값은 이후 단계에서 사용되므로 출력에서 저장합니다.

## <a name="create-a-service-principal-for-authentication"></a>인증을 위한 서비스 주체 만들기

[az 광고 sp create for-rbac를][az-ad-sp-create-for-rbac] 사용하여 서비스 주체를 만듭니다. 다음은 그 예입니다.

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

JSON 출력은 이후 단계에서 사용되므로 저장합니다.

[AZ aks 표시를][az-aks-show] 사용하여 AKS 클러스터의 *ID를* 표시합니다.

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

[az acr 쇼를][az-acr-show] 사용하여 ACR의 *ID를* 표시합니다.

```azurecli
az acr show --name <acrName> --query id
```

[az 역할 할당 만들기를][az-role-assignment-create] 사용하여 *기여자에게* AKS 클러스터에 대한 액세스 권한 과 *ACR에 대한 AcrPush* 액세스 권한을 부여합니다.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> 서비스 주체가 해당 리소스에 액세스할 수 있도록 하려면 AKS 클러스터와 ACR모두의 소유자여야 합니다.

## <a name="configure-your-github-action"></a>GitHub 작업 구성

> [!IMPORTANT]
> 리포지토리에 대해 GitHub 작업을 사용하도록 설정해야 합니다. 리포지토리에 대한 GitHub 작업을 사용하려면 GitHub의 리포지토리로 이동하여 작업 탭을 클릭하고 이 리포지토리에 대한 작업을 사용하도록 선택합니다.

포크된 리포지토리로 이동하여 *설정을*클릭합니다. 왼쪽 사이드바에서 *비밀을* 클릭합니다. 아래에서 *새 비밀 추가를* 클릭합니다.

1. *AZURE_CREDENTIALS*: 서비스 주체 생성의 전체 출력입니다.
1. *RESOURCE_GROUP*: 이 예제에서 *MyResourceGroup인*AKS 클러스터의 리소스 그룹입니다.
1. *CLUSTER_NAME*: 이 예제에서 *MyAKS*입니다 AKS 클러스터의 이름입니다.
1. *CONTAINER_REGISTRY*: ACR의 *로그인 서버입니다.*
1. *HOST*: *>.<APP_NAME>.HOST_SUFFIX <>* MASTER_SPACE<양식을<개발자 공간의 호스트는 이 예제에서 *dev.bikesharingweb.fedcab0987.eus.azds.io.*
1. *IMAGE_PULL_SECRET*: *데모 비밀과*같은 사용하려는 비밀의 이름입니다.
1. *MASTER_SPACE*: 이 예제에서 *개발자인*부모 개발자 공간의 이름입니다.
1. *REGISTRY_USERNAME*: 서비스 주체 생성에서 JSON 출력의 *clientId입니다.*
1. *REGISTRY_PASSWORD*: 서비스 주체 생성에서 JSON 출력의 *클라이언트를* 보금합니다.

> [!NOTE]
> 이러한 모든 암호는 GitHub 작업에 의해 사용되며 [.github/워크플로/bikes.yml로][github-action-yaml]구성됩니다.

선택적으로 PR을 병합한 후 마스터 공간을 업데이트하려면 이 예제에서 *dev.gateway.fedcab0987.eus.azds.io* *>.gateway.<HOST_SUFFIX>MASTER_SPACE<* 양식을<*GATEWAY_HOST* 비밀을 추가합니다. 변경 내용을 포크의 마스터 분기에 병합하면 마스터 개발 공간에서 전체 응용 프로그램을 다시 빌드하고 실행하기 위한 다른 작업이 실행됩니다. 이 예제에서는 마스터 공간이 *개발됩니다.* 이 작업은 [.github/워크플로/bikesharing.yml로][github-action-bikesharing-yaml]구성됩니다.

또한 PR의 변경 내용이 손자 공간에서 실행되도록 하려면 *MASTER_SPACE* 및 *HOST* 비밀을 업데이트합니다. 예를 들어 응용 프로그램이 자식 공간 *dev/azureuser1을*사용하여 *개발에서* 실행중인 경우 *dev/azureuser1의*자식 공간에서 PR을 실행하도록 합니다.

* 이 예제 *azureuser1에서*부모 공간으로 원하는 자식 공간에 *MASTER_SPACE* 업데이트합니다.
* 이 *예제에서는* *호스트를* *GRANDPARENT_SPACE>.<APP_NAME>.<HOST_SUFFIX><* dev.bikesharingweb.fedcab0987.eus.azds.io 업데이트합니다.

## <a name="create-a-new-branch-for-code-changes"></a>코드 변경을 위한 새 분기 만들기

로 `BikeSharingApp/` 이동하여 *자전거 이미지라는*새 분기를 만듭니다.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

[자전거/server.js][bikes-server-js] 편집하여 줄 232 및 233을 제거합니다.

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

이제 섹션은 다음과 같습니다.

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

파일을 저장한 `git add` 다음 `git commit` 사용하고 변경 내용을 단계적으로 합니다.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>변경 사항 푸시

새 `git push` 분기를 분기 저장소로 푸시하는 데 사용합니다.

```cmd
git push origin bike-images
```

푸시가 완료되면 GitHub의 분기 된 리포지토리로 이동하여 *자전거 이미지* 분기와 비교하여 분기 된 리포지토리의 *마스터* 분기를 기본 분기로 끌어오기 요청을 만듭니다.

끌어오기 요청이 열리면 *작업* 탭으로 이동합니다. *Bikes*

## <a name="view-the-child-space-with-your-changes"></a>변경 내용으로 자식 공간 보기

작업이 완료되면 끌어오기 요청의 변경 내용을 기반으로 새 자식 공간에 대한 URL이 있는 주석이 표시됩니다.

> [!div class="mx-imgBorder"]
> ![GitHub 작업 URL](../media/github-actions/github-action-url.png)

주석에서 URL을 열어 *자전거 공유 웹* 서비스로 이동합니다. *사용자로 Aurelia Briggs(고객)를* 선택한 다음 대여할 자전거를 선택합니다. 자전거의 자리 표시자 이미지가 더 이상 표시되지 않는지 확인합니다.

변경 내용을 포크의 *마스터* 분기에 병합하면 부모 개발 공간에서 전체 응용 프로그램을 다시 빌드하고 실행하기 위한 다른 작업이 실행됩니다. 이 예제에서는 상위 공간이 *개발입니다.* 이 작업은 [.github/워크플로/bikesharing.yml로][github-action-bikesharing-yaml]구성됩니다.

## <a name="clean-up-your-azure-resources"></a>Azure 리소스 정리

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces를 통해 여러 컨테이너에서 더 복잡한 애플리케이션을 개발할 수 있는 방법 및 사용자가 다양한 환경에서 다양한 코드 버전이나 분기로 작업하여 공동 개발을 간소화하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces에서 팀 개발][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
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
[team-quickstart]: ../quickstart-team-development.md

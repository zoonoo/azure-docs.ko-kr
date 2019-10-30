---
title: Azure Kubernetes Service & GitHub 작업
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: GitHub 작업 및 Azure Dev Spaces를 사용 하 여 Azure Kubernetes Service에서 직접 끌어오기 요청에서 변경 내용을 검토 하 고 테스트 합니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, GitHub 작업, 투구, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 2638fe2cd12407e43d3b3b698cdfca5231362db4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065939"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Azure Kubernetes Service & GitHub 작업 (미리 보기)

Azure Dev Spaces는 리포지토리의 주 분기에 끌어오기 요청을 병합 하기 전에 AKS에서 직접 끌어오기 요청의 변경을 테스트할 수 있는 GitHub 작업을 사용 하 여 워크플로를 제공 합니다. 끌어오기 요청에 대 한 변경 내용을 검토 하는 실행 중인 응용 프로그램이 있는 경우 개발자와 팀 멤버 모두의 확신도를 높일 수 있습니다. 이 실행 중인 응용 프로그램은 제품 관리자 및 디자이너와 같은 팀 멤버가 개발 초기 단계를 진행 하는 동안 검토 프로세스의 일부가 될 수도 있습니다.

이 가이드에서는 다음을 수행하는 방법을 알아봅니다.

- Azure Dev Spaces를 Azure의 관리형 Kubernetes 클러스터에 설정합니다.
- 여러 마이크로서비스가 포함된 대규모 애플리케이션을 개발 공간에 배포합니다.
- GitHub 작업을 사용 하 여 CI/CD를 설정 합니다.
- 전체 애플리케이션의 컨텍스트 내에서 격리된 개발 공간에서 단일 마이크로서비스를 테스트합니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="prerequisites"></a>전제 조건

- Azure 구독. Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
- [Azure CLI 설치][azure-cli-installed]
- [Helm 2.13 이상 설치][helm-installed]
- [Github 작업을 사용][github-actions-beta-signup]하는 github 계정.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터 만들기

[지원되는 지역][supported-regions]에서 AKS 클러스터를 만들어야 합니다. 아래 명령은 *MyResourceGroup*이라는 리소스 그룹과 *MyAKS*라는 AKS 클러스터를 만듭니다.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>AKS 클러스터에서 Azure Dev Spaces를 사용하도록 설정

`use-dev-spaces` 명령을 사용하여 AKS 클러스터에서 Dev Spaces를 사용하도록 설정하고 프롬프트의 지시를 따릅니다. 아래 명령은 *MyResourceGroup* 그룹의 *MyAKS* 클러스터에서 Dev Spaces를 사용하도록 설정하고 *dev*라는 개발 공간을 만듭니다.

> [!NOTE]
> `use-dev-spaces` 명령은 Azure Dev Spaces CLI가 아직 설치되지 않은 경우에도 설치합니다. Azure Cloud Shell에는 Azure Dev Spaces CLI를 설치할 수 없습니다.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기

ACR (Azure Container Registry) 만들기:

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> ACR 이름은 Azure 내에서 고유 해야 하 고 5-50 영숫자 문자를 포함 해야 합니다. 사용 하는 모든 문자는 소문자 여야 합니다.

이후 단계에서 사용 되므로 출력에서 *loginServer* 값을 저장 합니다.

## <a name="create-a-service-principal-for-authentication"></a>인증을 위한 서비스 주체 만들기

[Az ad sp create-rbac][az-ad-sp-create-for-rbac] 를 사용 하 여 서비스 주체를 만듭니다. 다음은 그 예입니다.

```cmd
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

이후 단계에서 사용 되므로 JSON 출력을 저장 합니다.


[Az aks show][az-aks-show] 를 사용 하 여 aks 클러스터의 *id* 를 표시 합니다.

```cmd
az aks show -g MyResourceGroup -n MyAKS  --query id
```

[Az acr show][az-acr-show] 를 사용 하 여 acr의 *id* 를 표시 합니다.

```cmd
az acr show --name <acrName> --query id
```

[Az role 할당 create][az-role-assignment-create] 를 사용 하 여 AKS 클러스터에 대 한 *참가자* 액세스 및 ACR에 *acrpush* 액세스를 제공 합니다.

```cmd
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> 서비스 사용자에 게 해당 리소스에 대 한 액세스 권한을 부여 하려면 AKS 클러스터와 ACR의 소유자 여야 합니다.

## <a name="get-sample-application-code"></a>샘플 애플리케이션 코드 받기

이 문서에서는 [Azure Dev Spaces 자전거 공유 샘플 응용 프로그램][bike-sharing-gh] 을 사용 하 여 GitHub 작업에 Azure Dev Spaces를 사용 하는 방법을 보여 줍니다.

Azure Dev Spaces 샘플 리포지토리를 포크 한 후 분기 리포지토리로 이동 합니다. *작업* 탭을 클릭 하 고이 리포지토리에 대 한 동작을 사용 하도록 설정 합니다.

분기 리포지토리를 복제 하 고 해당 디렉터리로 이동 합니다.

```cmd
git clone https://github.com/USERNAME/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>*dev*에 대한 HostSuffix 검색

`azds show-context` 명령을 사용하여 *dev*에 대한 HostSuffix를 표시합니다.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>HostSuffix를 사용하여 Helm 차트 업데이트

[charts/values.yaml][bike-sharing-values-yaml]을 열고, 모든 `<REPLACE_ME_WITH_HOST_SUFFIX>` 인스턴스를 앞에서 검색한 HostSuffix 값으로 바꿉니다. 변경 내용을 저장하고 파일을 닫습니다.

## <a name="run-the-sample-application-in-kubernetes"></a>Kubernetes에서 샘플 애플리케이션 실행

Kubernetes에서 샘플 애플리케이션을 실행하는 명령은 기존 프로세스의 일부이며 Azure Dev Spaces 도구에 종속되지 않습니다. 이 경우 Helm은 이 샘플 애플리케이션을 실행하는 데 사용되는 도구이지만, 클러스터 내에서 네임스페이스의 전체 애플리케이션을 실행하는 경우 다른 도구를 사용할 수 있습니다. Helm 명령은 앞에서 만든 *dev*라는 개발 공간을 대상으로 하지만 이 개발 공간도 Kubernetes 네임스페이스입니다. 따라서 개발 공간은 다른 네임스페이스와 동일한 다른 도구에서 대상이 될 수 있습니다.

응용 프로그램을 배포 하는 데 사용 되는 도구에 관계 없이 클러스터에서 응용 프로그램을 실행 한 후 개발에 Azure Dev Spaces를 사용할 수 있습니다.

`helm init` 및 `helm install` 명령을 사용하여 클러스터에 샘플 애플리케이션을 설정하고 설치합니다.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

> [!Note]
> **RBAC 사용 클러스터를 사용하는 경우**에는 [Tiller에 대한 서비스 계정][tiller-rbac]을 구성해야 합니다. 그렇지 않으면, `helm` 명령이 실패합니다.

`helm install` 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 명령 출력은 완료되었을 때 클러스터에 배포된 모든 서비스의 상태를 보여 줍니다.

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

샘플 애플리케이션이 클러스터에 설치되고 클러스터에서 Dev Spaces를 사용하도록 설정되었으므로 `azds list-uris` 명령을 사용하여 현재 선택된 *dev*에 샘플 애플리케이션에 대한 URL을 표시합니다.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

`azds list-uris` 명령에서 공용 URL을 열어 *bikesharingweb* 서비스로 이동합니다. 위의 예제에서 *bikesharingweb* 서비스에 대한 공용 URL은 `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`입니다. 사용자로 *Aurelia Briggs (고객)* 를 선택 하 고 임대에 대 한 자전거를 선택 합니다. 자전거에 대 한 자리 표시자 이미지가 표시 되는지 확인 합니다.

## <a name="configure-your-github-action"></a>GitHub 작업 구성

분기 리포지토리로 이동 하 고 *설정*을 클릭 합니다. 왼쪽 세로 막대에서 *비밀* 을 클릭 합니다. *새 암호 추가* 를 클릭 하 여 아래에 새 암호를 각각 추가 합니다.

1. *AZURE_CREDENTIALS*: 서비스 사용자 만들기의 전체 출력입니다.
1. *RESOURCE_GROUP*: AKS 클러스터에 대 한 리소스 그룹 (이 예제에서는 *myresourcegroup*).
1. *CLUSTER_NAME*: AKS 클러스터의 이름 (이 예제에서는 *MyAKS*).
1. *CONTAINER_REGISTRY*: ACR의 *loginServer* 입니다.
1. *HOST*: < MASTER_SPACE > 형식으로 사용 되는 개발 공간에 대 한 호스트입니다. *< APP_NAME >. < HOST_SUFFIX >* (이 예제에서는 *dev.bikesharingweb.fedcab0987.eus.azds.io*입니다.
1. *IMAGE_PULL_SECRET*: 사용 하려는 비밀의 이름 (예: *데모 암호)* 입니다.
1. *MASTER_SPACE*:이 예제에서 *Dev*인 부모 개발 공간의 이름입니다.
1. *REGISTRY_USERNAME*: 서비스 주체 만들기의 JSON 출력에서 *clientId* 입니다.
1. *REGISTRY_PASSWORD*: 서비스 주체를 만들 때 JSON 출력의 *clientSecret* 입니다.

> [!NOTE]
> 이러한 암호는 모두 GitHub 작업에서 사용 되며 [github/워크플로/자전거 .yml][github-action-yaml]에서 구성 됩니다.

## <a name="create-a-new-branch-for-code-changes"></a>코드 변경에 대 한 새 분기 만들기

`BikeSharingApp/`으로 다시 이동 하 고 *자전거 이미지*라는 새 분기를 만듭니다.

```cmd
cd ..
git checkout -b bike-images
```

[Bikes/node.js][bikes-server-js] 를 편집 하 여 줄 232 및 233을 제거 합니다.

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

파일을 저장 한 다음 `git add` 및 `git commit`를 사용 하 여 변경 내용을 준비 합니다.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>변경 내용 푸시

`git push`를 사용 하 여 새 분기를 분기 리포지토리로 푸시합니다.

```cmd
git push origin bike-images
```

푸시가 완료 되 면 GitHub의 분기 리포지토리로 이동 하 여 분기 리포지토리의 *마스터가* *자전거 이미지* 분기와 비교 하 여 기본 분기로 설정 된 끌어오기 요청을 만듭니다.

끌어오기 요청이 열리면 *작업* 탭으로 이동 합니다. 새 작업이 시작 되었으며 *자전거* 서비스를 빌드하고 있는지 확인 합니다.

## <a name="view-the-child-space-with-your-changes"></a>변경 내용으로 자식 공간 보기

작업이 완료 되 면 끌어오기 요청의 변경 사항을 기반으로 새 하위 공간에 대 한 URL이 포함 된 주석이 표시 됩니다.

> [!div class="mx-imgBorder"]
> ![GitHub 동작 Url](../media/github-actions/github-action-url.png)

주석에서 URL을 열어 *bikesharingweb* 서비스로 이동 합니다. 사용자로 *Aurelia Briggs (고객)* 를 선택 하 고 임대에 대 한 자전거를 선택 합니다. 자전거에 대 한 자리 표시자 이미지가 더 이상 표시 되지 않는지 확인 합니다.

## <a name="clean-up-your-azure-resources"></a>Azure 리소스 정리

```cmd
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
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[tiller-rbac]: https://helm.sh/docs/using_helm/#role-based-access-control
[supported-regions]: ../about.md#supported-regions-and-configurations
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md
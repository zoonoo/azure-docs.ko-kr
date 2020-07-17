---
title: Visual Studio에서 Kubernetes와 함께 로컬 프로세스 사용(미리 보기)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Kubernetes를 사용 하 여 Visual Studio에서 로컬 프로세스를 사용 하 여 개발 컴퓨터를 Kubernetes 클러스터에 연결 하는 방법을 알아봅니다 Azure Dev Spaces
keywords: Kubernetes, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너를 사용 하는 로컬 프로세스
ms.openlocfilehash: 8e89baeac8d9042a87685ed1268ed694db8d9455
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316554"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-preview"></a>Visual Studio에서 Kubernetes와 함께 로컬 프로세스 사용(미리 보기)

Kubernetes를 사용 하는 로컬 프로세스를 사용 하면 응용 프로그램의 나머지 부분을 사용 하 여 Kubernetes 클러스터에 연결 하면서도 개발 컴퓨터에서 코드를 실행 하 고 디버그할 수 있습니다. 예를 들어 상호 의존적인 많은 서비스 및 데이터베이스를 포함 하는 많은 마이크로 서비스 아키텍처가 있는 경우 개발 컴퓨터에 이러한 종속성을 복제 하는 것은 어려울 수 있습니다. 또한 내부 루프 개발 중에 각 코드 변경에 대해 코드를 작성 하 고 Kubernetes 클러스터에 배포 하는 작업은 속도가 느리고 시간이 오래 걸리고 디버거에서 사용 하기 어려울 수 있습니다.

Kubernetes를 사용 하는 로컬 프로세스에서는 개발 컴퓨터와 클러스터 간에 직접 연결을 만들어 클러스터에 코드를 빌드하고 배포할 필요가 없습니다. 디버깅 하는 동안 개발 컴퓨터를 클러스터에 연결 하면 Docker 또는 Kubernetes 구성을 만들지 않고도 전체 응용 프로그램의 컨텍스트에서 서비스를 신속 하 게 테스트 하 고 개발할 수 있습니다.

Kubernetes를 사용 하는 로컬 프로세스는 연결 된 Kubernetes 클러스터와 개발 컴퓨터 간에 트래픽을 리디렉션합니다. 이러한 트래픽 리디렉션을 통해 Kubernetes 클러스터에서 실행 되는 서비스와 개발 컴퓨터의 코드가 동일한 Kubernetes 클러스터에 있는 것 처럼 통신할 수 있습니다. Kubernetes를 사용 하는 로컬 프로세스도 개발 컴퓨터의 Kubernetes 클러스터에서 pod에 사용할 수 있는 환경 변수 및 탑재 된 볼륨을 복제 하는 방법을 제공 합니다. 개발 컴퓨터에서 환경 변수 및 탑재 된 볼륨에 대 한 액세스를 제공 하면 해당 종속성을 수동으로 복제 하지 않고도 코드를 빠르게 처리할 수 있습니다.

이 가이드에서는 Kubernetes를 사용 하 여 로컬 프로세스를 사용 하 여 Kubernetes 클러스터와 개발 컴퓨터에서 실행 되는 코드 간에 트래픽을 리디렉션하는 방법에 대해 설명 합니다. 또한이 가이드에서는 Kubernetes 클러스터에서 여러 마이크로 서비스가 포함 된 규모가 많은 샘플 응용 프로그램을 배포 하는 스크립트를 제공 합니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관][preview-terms]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 가이드에서는 [Azure Dev Spaces 자전거 공유 샘플 응용 프로그램][bike-sharing-github] 을 사용 하 여 개발 컴퓨터를 Kubernetes 클러스터에 연결 하는 방법을 보여 줍니다. Kubernetes 클러스터에서 실행 중인 응용 프로그램이 이미 있는 경우 아래 단계를 계속 수행 하 여 고유한 서비스 이름을 사용할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치][azure-cli]
* *ASP.NET 및 웹 개발* 및 *Azure 개발* 워크 로드가 설치 된 Windows 10에서 실행 되는 [Visual Studio 2019][visual-studio] 버전 16.7 Preview 2 이상
* [CLI가 설치 Azure Dev Spaces][azds-cli].

### <a name="enable-the-local-process-with-kubernetes-preview-feature-in-visual-studio"></a>Visual Studio에서 Kubernetes 미리 보기 기능을 사용 하 여 로컬 프로세스 사용

Visual Studio에서 Kubernetes를 사용 하 여 로컬 프로세스를 사용 하도록 설정 하려면 *도구*  >  *옵션*  >  *환경*  >  *미리 보기 기능*을 클릭 합니다. *Kubernetes services에 대해 로컬 디버깅 사용을*선택 합니다.

또한 .NET 콘솔 응용 프로그램의 경우 *VisualStudio* NuGet 패키지를 설치 합니다.

## <a name="create-a-kubernetes-cluster"></a>Kubernetes 클러스터 만들기

[지원 되는 지역][supported-regions]에 AKS 클러스터를 만듭니다. 아래 명령은 *MyResourceGroup*이라는 리소스 그룹과 *MyAKS*라는 AKS 클러스터를 만듭니다.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>샘플 애플리케이션 설치

제공 된 스크립트를 사용 하 여 클러스터에 샘플 응용 프로그램을 설치 합니다. 개발 컴퓨터에서 또는 [Azure Cloud Shell][azure-cloud-shell]를 사용 하 여이 스크립트를 실행할 수 있습니다.

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

설치 스크립트의 출력에 표시 되는 공용 URL을 열어 클러스터를 실행 하는 샘플 응용 프로그램으로 이동 합니다.

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

위의 샘플에서 공용 URL은 `dev.bikesharingweb.EXTERNAL_IP.nip.io` 입니다.

## <a name="connect-to-your-cluster-and-debug-a-service"></a>클러스터에 연결 하 고 서비스 디버깅

개발 컴퓨터에서 [az Aks][az-aks-get-credentials]Kubernetes을 사용 하 여 Kubernetes 클러스터에 연결 하도록 CLI를 다운로드 하 고 구성 합니다.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Visual Studio의 [Azure Dev Spaces 자전거 공유 샘플 응용 프로그램][bike-sharing-github] 에서 *dev-spaces/samples/BikeSharingApp/ReservationEngine/app .csproj* 를 엽니다.

프로젝트의 시작 설정 드롭다운에서 아래와 같이 *Kubernetes를 사용 하 여 로컬 프로세스* 를 선택 합니다.

![Kubernetes를 사용 하 여 로컬 프로세스 선택](../media/local-process-kubernetes-visual-studio/choose-local-process.png)

*Kubernetes를 사용 하 여 로컬 프로세스*옆의 시작 단추를 클릭 합니다. Kubernetes를 *사용 하는 로컬 프로세스* 대화 상자:

* 구독을 선택합니다.
* 클러스터에 대해 *MyAKS* 를 선택 합니다.
* 네임 스페이스에 대해 *dev* 를 선택 합니다.
* *Reservationengine* 서비스를 선택 합니다.
* 시작 프로필에 대해 *앱* 을 선택 합니다.
* URL을 선택 `http://dev.bikesharingweb.EXTERNAL_IP.nip.io` 하 여 브라우저를 시작 합니다.

![Kubernetes 클러스터를 사용 하 여 로컬 프로세스 선택](../media/local-process-kubernetes-visual-studio/choose-local-process-cluster.png)

> [!IMPORTANT]
> 단일 pod가 있는 서비스만 리디렉션할 수 있습니다.

*저장 후 디버깅 시작을*클릭 합니다.

Kubernetes 클러스터의 모든 트래픽이 개발 컴퓨터에서 실행 중인 응용 프로그램의 버전으로 *reservationengine* 서비스에 대해 리디렉션됩니다. 또한 Kubernetes를 사용 하는 로컬 프로세스는 응용 프로그램의 모든 아웃 바운드 트래픽을 Kubernetes 클러스터로 다시 라우팅합니다.

> [!NOTE]
> *KubernetesDNSManager* 를 관리자 권한으로 실행 하 고 호스트 파일을 수정할 수 있도록 허용 하 라는 메시지가 표시 됩니다.

상태 표시줄에 *reservationengine* 서비스에 연결 됨이 표시 되 면 개발 컴퓨터가 연결 됩니다.

![개발 컴퓨터 연결 됨](../media/local-process-kubernetes-visual-studio/development-computer-connected.png)

> [!NOTE]
> Kubernetes 대화 상자에서 *로컬 프로세스* 를 사용 하 여 메시지가 표시 되지 않습니다. 이러한 설정은 프로젝트 속성의 *디버그* 창에서 업데이트 합니다.

개발 컴퓨터가 연결 되 면 트래픽이 대체 중인 서비스에 대 한 개발 컴퓨터로 리디렉션하기 시작 합니다.

## <a name="set-a-break-point"></a>중단점 설정

[BikesHelper.cs][bikeshelper-cs-breakpoint] 를 열고 26 줄에서 원하는 위치를 클릭 하 여 커서를 놓습니다. *F9* 또는 *디버그* 를 클릭 한 다음 *중단점 설정/해제*를 클릭 하 여 중단점을 설정 합니다.

공용 URL을 열어 샘플 응용 프로그램으로 이동 합니다. 사용자로 *Aurelia Briggs (고객)* 를 선택 하 고 임대에 대 한 자전거를 선택 합니다. *임대 자전거*를 클릭 합니다. Visual Studio로 돌아가서 26 줄이 강조 표시 됩니다. 설정 하는 중단점에서 26 줄의 서비스를 일시 중지 했습니다. 서비스를 다시 시작하려면 *F5* 키를 누르거나 *디버그*, *계속*을 차례로 클릭합니다. 브라우저로 돌아가서 자전거를 임대 하는 페이지가 표시 되는지 확인 합니다.

에서 26 줄에 커서를 놓고 F9를 눌러 중단점을 제거 `BikesHelper.cs` 합니다. *F9*

> [!NOTE]
> 기본적으로 디버깅 작업을 중지 하면 Kubernetes 클러스터에서 개발 컴퓨터의 연결이 끊어집니다. 디버깅 옵션의 *Kubernetes 디버깅 도구* 섹션에서 *디버깅 후 연결 끊기* 를 변경 하 여이 *동작을 변경할* 수 있습니다. 이 설정을 업데이트 한 후에는 디버깅을 중지 하 고 시작할 때 개발 컴퓨터가 연결 된 상태를 유지 합니다. 클러스터에서 개발 컴퓨터의 연결을 끊으려면 도구 모음에서 *연결 끊기* 단추를 클릭 합니다.
>
> Visual Studio가 클러스터에 대 한 연결을 갑자기 종료 하거나 종료 하는 경우 Kubernetes를 사용 하 여 로컬 프로세스에 연결 하기 전에 리디렉션하는 서비스가 원래 상태로 복원 되지 않을 수 있습니다. 이 문제를 해결 하려면 [문제 해결 가이드][troubleshooting]를 참조 하세요.

## <a name="using-logging-and-diagnostics"></a>로깅 및 진단 사용

`Azure Dev Spaces` [개발 컴퓨터의 *임시* 디렉터리][azds-tmp-dir]에 있는 디렉터리에서 진단 로그를 찾을 수 있습니다.

## <a name="remove-the-sample-application-from-your-cluster"></a>클러스터에서 샘플 응용 프로그램 제거

제공 된 스크립트를 사용 하 여 클러스터에서 샘플 응용 프로그램을 제거 합니다.

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>다음 단계

끌어오기 요청이 리포지토리의 주 분기에 병합 되기 전에 Azure Dev Spaces 및 GitHub 작업을 사용 하 여 끌어오기 요청에서 직접 AKS의 변경 내용을 테스트 하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Kubernetes Service & GitHub 작업][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[bikeshelper-cs-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/ReservationEngine/BikesHelper.cs#L26
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[visual-studio]: https://www.visualstudio.com/vs/
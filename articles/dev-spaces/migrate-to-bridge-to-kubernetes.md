---
title: Bridge to Kubernetes로 마이그레이션
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Azure Dev Spaces에서 Bridge to Kubernetes로의 마이그레이션 프로세스 설명하기
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Bridge to Kubernetes
ms.openlocfilehash: 8ffb7693ff223a9cb952964ded1e6967ceeb326e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499294"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Bridge to Kubernetes로 마이그레이션

> [!IMPORTANT]
> Azure Dev Spaces는 2023년 10월 31일부터 사용 중지됩니다. 고객은 클라이언트 개발자 도구인 Bridge to Kubernetes로 이동해야 합니다.
>
> Azure Dev Spaces를 사용했던 목적은 사용자들이 Kubernetes에서의 개발을 편리하게 하도록 하는 것이었습니다. 사용자들이 Kubernetes의 배포 개념 이외에 Docker와 Kubernetes 구성도 이해해야 한다는 부담이 Azure Dev Spaces를 통한 접근 방식의 주요 단점입니다. 시간이 지나면서, Azure Dev Spaces를 통한 접근 방식이 Kubernetes에서의 내부 루프 개발 속도를 효과적으로 줄여 주지도 못한다는 점이 명확해졌습니다. Bridge to Kubernetes는 내부 루프 개발 속도를 효과적으로 줄이는 한 편, 사용자에게 불필요한 부담을 지우지도 않습니다.
>
> 대규모 애플리케이션의 컨텍스트 상의 마이크로 서비스 코드를 개발하고 테스트하며 디버그할 최상의 환경을 빌드한다는 핵심 목표는 변하지 않습니다.

Bridge to Kubernetes는 Azure Dev Spaces와 함께 작동하는 여러 가지 개발 시나리오에 대해 보다 가벼운 대안을 제공합니다. Bridge to Kubernetes는 [Visual Studio][vs] 및 [Visual Studio Code][vsc]의 확장명을 사용하는 클라이언트 측 전용 환경입니다.  

Bridge to Kubernetes는 구축된Kubernetes 애플리케이션이 로컬 개발 워크스테이션에서 실행되는 서비스를 포함하도록 하여 개발 환경을 지원합니다. Dev Spaces와는 다르게, Bridge to Kubernetes는 Docker 구성과 Kubernetes 구성을 만들 필요성을 회피하여 내부 루프 복잡성을 줄이고 개발자가 마이크로 서비스 코드의 비즈니스 논리에만 몰두할 수 있도록 합니다.

Bridge to Kubernetes는 Kubernetes 환경에서의 종속성 및 기존 구성을 사용하는 동안 개발자 컴퓨터에서 실행되는 코드를 반복하는 데 도움이 됩니다. 이와는 대조적으로, Azure Dev Spaces는 원격으로 서비스를 디버그하고 코드를 반복하기 전에 마이크로 서비스를 Kubernetes 환경에 배포합니다.

본 문서는 Azure Dev Spaces와 Bridge to Kubernetes 간의 비교 내용 및 Azure Dev Spaces에서 Bridge to Kubernetes로의 마이그레이션 지침을 제공합니다.

## <a name="development-approaches"></a>개발 접근 방식

![개발 접근 방식](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces는 배포된 환경과 다른 로컬 환경을 요구하지 않도록 해 Kubernetes 개발자들이 자신의 AKS 클러스터에서 직접 코드 러닝 작업을 할 수 있도록 도왔습니다. 이러한 접근 방식이 개발 관련 일정 부분을 개선하긴 했지만, Azure Dev Spaces를 사용하기 전에 Docker, Kubernetes, Helm 등의 추가적인 개념을 학습하고 유지해야 할 필요성을 제시하기도 했습니다.

Bridge to Kubernetes를 사용하면 개발자들은 클러스터의 나머지 부분과 상호작용하는 동안에도 자신의 개발 컴퓨터에서 직접 작업을 할 수 있습니다. 이러한 식의 접근 방식은 개발 컴퓨터에서 직접 코드를 실행한다는 익숙함과 속도를 활용하면서도 클러스터에서 제공한 종속성과 환경을 공유합니다. 이러한 접근 방식은 Kubernetes에서 실행되는 것과 같은 충실도 및 크기 조정을 활용할 수도 있습니다.

## <a name="feature-comparison"></a>기능 비교

Azure Dev Spaces와 Bridge to Kubernetes에는 비슷한 기능이 있지만 몇 가지 차이점이 있습니다.

| 요구 사항  | Azure Dev 공간  | Bridge to Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | 15개의 Azure 지역 내 | 모든 AKS 서비스 지역    |
| **보안** |
| 클러스터에 필요한 보안 액세스  | AKS 클러스터 contributor  | Kubernetes RBAC -배포 업데이트   |
| 개발 컴퓨터에 필요한 보안 액세스  | 해당 없음  | 로컬 관리자/sudo   |
| **유용성** |
| Kubernetes 및 Docker 아티팩트와 독립적  | 예  | 예   |
| 변경 내용 자동 롤백, 사후 디버그  | 예  | 예   |
| **지원하는 클라이언트 도구** |
| Visual Studio 2019에서 작동  | 예  | 예   |
| Visual Studio Code에서 작동  | 예  | 예   |
| CLI에서 작동  | 예  | 예   |
| **운영 체제 호환성** |
| Windows 10에서 작동  | 예  | 예  |
| Linux에서 작동  | 예  | 예  |
| macOS에서 작동  | 예  | 예  |
| **Capabilities** |
| 개발자 격리 또는 팀 개발  | 예  | 예  |
| 환경 변수 선택적으로 덮어쓰기  | 예  | 예  |
| Dockerfile 및 Helm 차트 만들기  | 예  | 예  |
| Kubernetes에 코드를 지속적으로 배포  | 예  | 예  |
| Kubernetes pod에서 원격 디버깅  | 예  | 예  |
| Kubernetes에 연결, 로컬 디버깅  | 예  | 예  |
| 동일한 워크스테이션에서 동시에 여러 서비스 디버깅  | 예  | 예  |

## <a name="kubernetes-inner-loop-development"></a>Kubernetes 내부 루프 개발

Azure Dev Spaces와 Bridge to Kubernetes의 가장 큰 차이점은 코드의 실행 위치입니다. Azure Dev Spaces는 마이크로 서비스 코드의 개발과 디버그에 도움을 주지만 자신의 클러스터 내에서 해당 코드를 실행해야 합니다. Bridge to Kubernetes는 개발 컴퓨터에서 직접 마이크로 서비스 코드를 개발하고 디버그할 수 있으면서도 Kubernetes에서 실행되는 대규모 애플리케이션의 컨텍스트를 활용할 수 있습니다. Bridge to Kubernetes는 Kubernetes 클러스터의 범위를 넓혀 로컬 프로세스가 Kubernetes의 구성을 물려받을 수 있도록 합니다.

![내부 루프 개발](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Bridge to Kubernetes를 사용하는 경우, 개발 컴퓨터와 클러스터 간에 네트워크가 설정됩니다.해당 연결이 유지되는 동안에는 개발 컴퓨터에 서비스 요청을 리디렉션하는 Kubernetes 배포 대신 프록시가 클러스터에 추가됩니다. 연결이 해제되면 클러스터에서 실행되는 배포 원래 버전을 사용하도록 애플리케이션 배포가 되돌아갑니다. 이러한 접근 방식은 클러스터와 동기화되고 빌드된 다음 실행되는 코드에서 작동하는 Azure Dev Spaces의 방식과는 차이가 있습니다. Azure Dev Spaces는 또한 코드를 롤백하지 않습니다.

Bridge to Kubernetes는 배포 방법과 상관 없이 Kubernetes에서 실행되는 애플리케이션과 함께 사용할 수 있어 융통성이 있습니다. 설정된 도구나 사용자 지정 스크립트를 사용하는 지와 무관하게 애플리케이션을 빌드하여 실행하는 데 CI/CD를 사용한다고 해도 여전히 Bridge to Kubernetes를 사용해 코드를 개발하고 디버그할 수 있습니다.

> [!TIP]
>  [Microsoft Kubernetes 확장][kubernetes-extension]은 Intellisense를 사용하여 신속하게 Kubernetes를 개발할 수 있도록 하며 스캐폴드 Helm 차트에 도움을 줍니다.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Azure Dev Spaces에서 Bridge to Kubernetes로의 전환

1. Visual Studio를 사용 중이라면 Visual Studio IDE를 16.7 이후 버전으로 업데이트하고 [Visual Studio Marketplace][vs-marketplace]에서 Bridge to Kubernetes 확장을 설치합니다. Visual Studio Code를 사용 중이라면 [Bridge to Kubernetes 확장][vsc-marketplace]을 설치합니다.
1. Azure Portal 또는 [Azure Dev Spaces CLI][azds-delete]를 사용해 Azure Dev Spaces를 사용하지 않도록 설정합니다.
1. [Azure Cloud Shell](https://shell.azure.com)을 사용합니다. 또는 Bash가 설치된 Mac이나 Linux 또는 Windows의 경우, Bash 셸 프롬프트를 엽니다. 명령줄 환경에서 Azure CLI, docker, kubectl, curl, tar, gunzip 도구를 사용할 수 있는지 확인합니다.
1. 컨테이너 레지스트리를 만들거나 기존의 것을 사용합니다. [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)나 [Docker Hub](https://hub.docker.com/)를 사용해 Azure에 컨테이너 레지스트리를 만들 수 있습니다. Azure Cloud Shell을 사용하는 경우, docker 이미지를 호스트하려면 Azure Container Registry만 사용할 수 있습니다.
1. Azure Dev Spaces 자산을 Bridge to Kubernetes 자산으로 변환할 마이그레이션 스크립트를 실행합니다. 이 스크립트는 Bridge to Kubernetes에 호환되는 새 이미지를 빌드하고, 해당 이미지를 지정된 레지스트리에 업로드한 뒤 [Helm](https://helm.sh)을 사용해 이미지가 있는 클러스터를 업데이트합니다. 반드시 리소스 그룹, AKS 클러스터 이름, 컨테이너 레지스트리를 제공해야 합니다. 다른 명령줄 옵션은 다음과 같습니다.

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   스크립트는 다음 플래그를 지원합니다.

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. *azds.yaml* 의 환경 변수 설정과 같은 사용자 지정 항목을 수동으로 프로젝트의 *values.yml* 파일에 마이그레이션합니다.
1. (옵션) 프로젝트에서 `azds.yaml` 파일을 삭제합니다.
1. 배포된 애플리케이션에서 Bridge to Kubernetes를 구성합니다. Visual Studio에서 Bridge to Kubernetes 사용하기에 대한 자세한 내용은 [Visual Studio에서 Bridge to Kubernetes 사용하기][use-btk-vs]를 참조하세요. VS Code의 경우, [VS Code에서 Bridge to Kubernetes 사용하기][use-btk-vsc]를 참조하세요.
1. 새로 만든 Bridge to Kubernetes 디버그/시작 프로필을 사용해 디버그를 시작합니다.
1. 클러스터에 재배포하는 데 필요한 만큼 여러 번 스크립트를 실행할 수 있습니다.

## <a name="team-development-in-a-shared-cluster"></a>공유 클러스터의 팀 개발

Bridge to Kubernetes로 개발자 특정 라우팅을 사용할 수도 있습니다. Azure Dev Spaces 팀 개발 시나리오에서는 다양한 Kubernetes 네임스페이스를 사용하여 부모 네임스페이스와 자식 네임스페이스 개념을 통해 서비스를 나머지 애플리케이션으로부터 격리합니다. Bridge to Kubernetes도 동일한 기능을 제공하지만 동일 애플리케이션 네임스페이스 내에서 향상된 성능 특성을 갖춘 채로 제공합니다.

Bridge to Kubernetes와 Azure Dev Spaces는 모두 애플리케이션을 통해 HTTP 헤더를 제공 및 전파해야 합니다. Azure Dev Spaces을 위해 헤더 전파를 처리하도록 애플리케이션을 이미 구성한 상태라면 헤더를 업데이트해야 합니다. Azure Dev Spaces에서 Bridge to Kubernetes로 전환하려면 구성된 헤더를 *azds-route-as* 에서 *kubernetes-route-as* 로 업데이트합니다.

## <a name="evaluate-bridge-to-kubernetes"></a>Bridge to Kubernetes 평가하기

클러스터에서 Azure Dev Spaces를 사용하지 않도록 설정하기 전에 Bridge to Kubernetes를 시험해 보려면 새 클러스터를 사용하는 것이 가장 간단합니다. 동일한 클러스터에서 Azure Dev Spaces와 Bridge to Kubernetes를 동시에 사용하려 한다면 양쪽에 대한 라우팅 기능을 사용할 때 여러 문제가 발생합니다.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Visual Studio를 사용해 Bridge to Kubernetes 평가하기

1. Visual Studio IDE를 16.7 이후 버전으로 업데이트하고 [Visual Studio Marketplace][vs-marketplace]에서 Bridge to Kubernetes 확장을 설치합니다.
1. 새 AKS 클러스터를 만들고 애플리케이션을 배포합니다. [애플리케이션 예제][btk-sample-app]를 사용할 수도 있습니다.
1. 배포된 애플리케이션에서 Bridge to Kubernetes를 구성합니다. Visual Studio에서 Bridge to Kubernetes 사용하기에 대한 자세한 내용은 [Bridge to Kubernetes 사용하기][use-btk-vs]를 참조하세요.
1. 새로 만든 Bridge to Kubernetes 디버그 프로필을 사용해 Visual Studio에서 디버그를 시작합니다.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Visual Studio Code를 사용해 Bridge to Kubernetes 평가하기

1. [Bridge to Kubernetes 확장][vsc-marketplace]을 설치합니다.
1. 새 AKS 클러스터를 만들고 애플리케이션을 배포합니다. [애플리케이션 예제][btk-sample-app]를 사용할 수도 있습니다.
1. 배포된 애플리케이션에서 Bridge to Kubernetes를 구성합니다. Visual Studio Code에서 Bridge to Kubernetes 사용하기에 대한 자세한 내용은 [Bridge to Kubernetes 사용하기][use-btk-vsc]를 참조하세요.
1. 새로 만든 Bridge to Kubernetes 시작 프로필을 사용해 Visual Studio에서 디버그를 시작합니다.

## <a name="next-steps"></a>다음 단계

자세한 Bridge to Kubernetes 작동 방식을 알아봅니다.

> [!div class="nextstepaction"]
> [Bridge to Kubernetes 작동 방식][how-it-works-bridge-to-kubernetes]


[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/

---
title: Bridge to Kubernetes로 마이그레이션
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Azure Dev Spaces에서 Kubernetes로의 마이그레이션 프로세스에 대해 설명 합니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Kubernetes에 브리지
ms.openlocfilehash: 6a6fe2367fca3d2068bb7d9a8e1a157fd2e5ca9b
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329801"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Bridge to Kubernetes로 마이그레이션

> [!IMPORTANT]
> Azure Dev Spaces는 2023 년 10 월 31 일에 사용 중지 됩니다. 개발자는 클라이언트 개발자 도구인 Kubernetes에 대 한 Bridge 사용으로 이동 해야 합니다.
>
> Azure Dev Spaces 목적은 개발자가 Kubernetes에서 개발 하는 것을 간소화 하는 것 이었습니다. Azure Dev Spaces 접근 방식의 중요 한 단점은 Docker 및 Kubernetes 구성 뿐만 아니라 Kubernetes 배포 개념을 이해 하기 위해 개발자에 게 추가 부담을 주는 것입니다. 시간이 지남에 따라 Azure Dev Spaces의 접근 방식이 Kubernetes에서 내부 루프 개발 속도를 효과적으로 저하 하지 않았음을 알 수 있습니다. Kubernetes에 연결 하면 내부 루프 개발 속도가 효과적으로 줄어들고 개발자에 대 한 불필요 한 부담을 피할 수 있습니다.
>
> 핵심 업무는 변경 되지 않은 상태로 유지 됩니다. 더 큰 응용 프로그램의 컨텍스트에서 마이크로 서비스 코드를 개발, 테스트 및 디버그 하기 위한 최상의 개발자 환경을 구축 합니다.

Kubernetes에 연결 하면 Azure Dev Spaces와 함께 작동 하는 여러 개발 시나리오에 대 한 보다 간단한 가중치를 제공 합니다. Kubernetes에 대 한 브리지는 [Visual Studio][vs]   및 [Visual Studio Code][vsc]에서 확장을 사용 하는 클라이언트 쪽 전용 환경입니다.  

Kubernetes에 연결 하면 설정 된 Kubernetes 응용 프로그램이 로컬 개발 워크스테이션에서 실행 되는 서비스를 포함 하도록 허용 하 여 개발 환경을 지원 합니다. Dev 공백과 달리 Kubernetes에 대 한 연결은 Docker 및 Kubernetes 구성을 만들어야 하는 필요성을 병렬 실행 하 여 내부 루프 복잡성을 줄여 개발자가 마이크로 서비스 코드의 비즈니스 논리에만 집중할 수 있도록 합니다.

Kubernetes에 연결 하면 Kubernetes 환경에서 종속성 및 기존 구성을 사용 하는 동안 개발 컴퓨터에서 실행 되는 코드를 반복할 수 있습니다. 이와 Azure Dev Spaces 대조적으로 마이크로 서비스는 서비스를 원격으로 디버깅 하 고 코드를 반복 하기 전에 Kubernetes 환경에 배포 합니다.

이 문서에서는 Azure Dev Spaces 간의 비교와 Kubernetes에 대 한 연결을 제공 하 고 Azure Dev Spaces에서 브리지로 Kubernetes로 마이그레이션하기 위한 지침을 제공 합니다.

## <a name="development-approaches"></a>개발 접근 방식

![개발 방법](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces 개발자가 AKS 클러스터에서 직접 실행 되는 코드를 사용 하 여 배포 된 환경과 유사한 로컬 환경의 필요성을 Kubernetes 수 있습니다. 이 접근 방식은 개발의 특정 측면을 개선 했지만 Azure Dev Spaces 사용을 시작 하기 전에 Docker, Kubernetes 및 투구와 같은 추가 개념을 학습 하 고 유지 관리 해야 하는 필수 구성 요소를 도입 했습니다.

Kubernetes에 연결 하면 개발자가 나머지 클러스터와 상호 작용 하는 동안 개발 컴퓨터에서 직접 작업할 수 있습니다. 이 방법은 해당 클러스터에서 제공 하는 종속성과 환경을 공유 하는 동시에 개발 컴퓨터에서 직접 실행 되는 코드의 친숙 한 속도와 속도를 활용 합니다. 이 방법을 사용 하면 Kubernetes에서 실행 되는 것과 같은 충실도 및 크기 조정을 활용할 수 있습니다.

## <a name="feature-comparison"></a>기능 비교

Kubernetes에는 비슷한 기능이 포함 되어 있으며, 다음과 같은 여러 영역에서 차이가 있습니다. Azure Dev Spaces

| 요구 사항  | Azure Dev Spaces  | Kubernetes에 대한 브리지  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | Azure 지역 15 개 | 모든 AKS 서비스 지역    |
| **Security** |
| 클러스터에 필요한 보안 액세스  | AKS 클러스터 기여자  | Kubernetes RBAC-배포 업데이트   |
| 개발 컴퓨터에 필요한 보안 액세스  | 해당 없음  | 로컬 관리자/sudo   |
| **유용성** |
| Kubernetes 및 Docker 아티팩트와 독립적  | 아니요  | 예   |
| 변경 내용 자동 롤백, 사후 디버그  | 아니요  | 예   |
| **지원 되는 클라이언트 도구** |
| Visual Studio 2019에서 작동  | 예  | 예   |
| Visual Studio Code에서 작동  | 예  | 예   |
| CLI에서 작동  | 예  | 아니요   |
| **운영 체제 호환성** |
| Windows 10에서 작동  | 예  | 예  |
| Linux에서 작동  | 예  | 예  |
| MacOS에서 작동  | 예  | 예  |
| **Capabilities** |
| 개발자 격리 또는 팀 개발  | 예  | 예  |
| 선택적으로 환경 변수 덮어쓰기  | 아니요  | 예  |
| Dockerfile 및 투구 차트 만들기  | 예  | 아니요  |
| Kubernetes에 코드를 지속적으로 배포  | 예  | 아니요  |
| Kubernetes pod에서 원격 디버깅  | 예  | 아니요  |
| Kubernetes에 연결 된 로컬 디버깅  | 아니요  | 예  |
| 동일한 워크스테이션에서 동시에 여러 서비스 디버깅  | 예  | 예  |

## <a name="kubernetes-inner-loop-development"></a>Kubernetes inner 루프 개발

Azure Dev Spaces와 Kubernetes 간의 가장 큰 차이점은 코드가 실행 되는 위치입니다. Azure Dev Spaces 마이크로 서비스 코드를 개발 하 고 디버그 하는 데 도움이 되지만 클러스터에서 해당 코드를 실행 해야 합니다. Kubernetes에 연결 하면 Kubernetes에서 실행 되는 더 큰 응용 프로그램의 컨텍스트에서도 개발 컴퓨터에서 직접 마이크로 서비스 코드를 개발 하 고 디버그할 수 있습니다. Kubernetes에 대 한 브리지는 Kubernetes 클러스터의 경계를 확장 하 고 로컬 프로세스에서 Kubernetes의 구성을 상속할 수 있도록 합니다.

![내부 루프 개발](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Kubernetes에 브리지를 사용 하는 경우 개발 컴퓨터와 클러스터 간의 네트워크 연결이 설정 됩니다.이 연결의 수명 동안에는 서비스에 대 한 요청을 개발 컴퓨터로 리디렉션하는 Kubernetes 배포 대신 프록시가 클러스터에 추가 됩니다. 연결을 끊으면 응용 프로그램 배포는 클러스터에서 실행 되는 원래 버전의 배포를 사용 하 여로 돌아갑니다. 이 방법은 코드가 클러스터에 동기화 되 고, 빌드된 후 실행 되는 Azure Dev Spaces 작동 하는 방법과 다릅니다. 또한 Azure Dev Spaces 코드를 롤백하지 않습니다.

Kubernetes에 대 한 브리지는 배포 방법에 관계 없이 Kubernetes에서 실행 되는 응용 프로그램을 유연 하 게 사용할 수 있습니다. 설정 된 도구나 사용자 지정 스크립트를 사용 하는지에 관계 없이 CI/CD를 사용 하 여 응용 프로그램을 빌드하고 실행 하는 경우에도 Bridge를 사용 하 여 코드를 개발 하 고 디버그할 수 있습니다.

> [!TIP]
>  [Microsoft Kubernetes 확장][kubernetes-extension] 을 사용 하면 Intellisense를 사용 하 여 Kubernetes 매니페스트를 빠르게 개발 하 고 스 캐 폴드 투구 차트를 활용할 수 있습니다.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Azure Dev Spaces에서 Kubernetes로의 브리지로 전환

1. Visual Studio를 사용 하는 경우 Visual Studio IDE를 16.7 이상 버전으로 업데이트 하 고 [Visual Studio Marketplace][vs-marketplace]에서 Kubernetes 확장에 대 한 브리지를 설치 합니다. Visual Studio Code를 사용 하는 경우 [Kubernetes 확장에 대 한 브리지][vsc-marketplace]를 설치 합니다.
1. Azure Portal 또는 [AZURE DEV SPACES CLI][azds-delete]를 사용 하 여 Azure Dev Spaces 컨트롤러를 사용 하지 않도록 설정 합니다.
1. [Azure Cloud Shell](https://shell.azure.com)를 사용 합니다. 또는 bash가 설치 된 Mac, Linux 또는 Windows에서 bash 셸 프롬프트를 엽니다. 명령줄 환경에서 Azure CLI, docker, kubectl, 말아, tar, gunzip 등의 도구를 사용할 수 있는지 확인 합니다.
1. 컨테이너 레지스트리를 만들거나 기존 항목을 사용 합니다. [Azure Container Registry](../container-registry/index.yml) 또는 [Docker Hub](https://hub.docker.com/)를 사용 하 여 Azure에서 컨테이너 레지스트리를 만들 수 있습니다.
1. 마이그레이션 스크립트를 실행 하 Azure Dev Spaces 자산을 Kubernetes 자산으로 변환 합니다. 이 스크립트는 Kubernetes에 대 한 브리지와 호환 되는 새 이미지를 빌드하고, 지정 된 레지스트리에 업로드 한 후, [투구](https://helm.sh) 를 사용 하 여 이미지를 사용 하 여 클러스터를 업데이트 합니다. 리소스 그룹, AKS 클러스터의 이름 및 컨테이너 레지스트리를 제공 해야 합니다. 여기에 표시 된 다른 명령줄 옵션은 다음과 같습니다.

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   스크립트는 다음 플래그를 지원 합니다.

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. *Azds* 의 환경 변수 설정과 같은 사용자 지정 항목을 프로젝트의 *값 .yml* 파일에 수동으로 마이그레이션합니다.
1. 필드 `azds.yaml` 프로젝트에서 파일을 제거 합니다.
1. 배포 된 응용 프로그램에서 Kubernetes에 대 한 브리지를 구성 합니다. Visual Studio에서 Kubernetes 연결을 사용 하는 방법에 대 한 자세한 내용은 [Visual studio에서 Kubernetes에 브리지 사용][use-btk-vs]을 참조 하세요. VS Code [VS Code에서 Bridge를 사용 하 여 Kubernetes][use-btk-vsc]를 참조 하세요.
1. 새로 만든 Bridge를 사용 하 여 디버깅을 시작 하 고 디버그/시작 프로필을 Kubernetes.
1. 필요에 따라 스크립트를 다시 실행 하 여 클러스터로 다시 배포할 수 있습니다.

## <a name="team-development-in-a-shared-cluster"></a>공유 클러스터의 팀 개발

Kubernetes에 대 한 브리지가 있는 개발자 특정 라우팅을 사용할 수도 있습니다. Azure Dev Spaces 팀 개발 시나리오에서는 여러 Kubernetes 네임 스페이스를 사용 하 여 부모 및 자식 네임 스페이스의 개념을 사용 하 여 응용 프로그램의 나머지 부분에서 서비스를 격리 합니다. Kubernetes에 대 한 브리지는 동일한 기능을 제공 하지만 향상 된 성능 특성과 동일한 응용 프로그램 네임 스페이스 내에서 제공 됩니다.

Kubernetes 및 Azure Dev Spaces에 대 한 브리지는 모두 응용 프로그램 전체에서 HTTP 헤더를 제공 하 고 전파 해야 합니다. Azure Dev Spaces에 대 한 헤더 전파를 처리 하도록 응용 프로그램을 이미 구성한 경우 헤더를 업데이트 해야 합니다. Azure Dev Spaces에서 Kubernetes로의 브리지로 전환 하려면 구성 된 헤더를 *azds* 에서 *Kubernetes*로 업데이트 합니다.

## <a name="evaluate-bridge-to-kubernetes"></a>Kubernetes에 대 한 브리지 평가

클러스터에서 Azure Dev Spaces를 사용 하지 않도록 설정 하기 전에 Kubernetes에 대 한 연결을 시험해 보려는 경우 가장 쉬운 방법은 새 클러스터를 사용 하는 것입니다. Azure Dev Spaces를 사용 하 고 동일한 클러스터에서 동시에 Kubernetes에 연결 하는 경우 둘 다에서 라우팅 기능을 사용할 때 문제가 발생 합니다.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Visual Studio를 사용 하 여 Kubernetes에 대 한 브리지 평가

1. Visual Studio IDE를 16.7 이상 버전으로 업데이트 하 고 [Visual Studio Marketplace][vs-marketplace]에서 Kubernetes 확장에 대 한 브리지를 설치 합니다.
1. 새 AKS 클러스터를 만들고 응용 프로그램을 배포 합니다. [예제 응용 프로그램][btk-sample-app]을 사용할 수도 있습니다.
1. 배포 된 응용 프로그램에서 Kubernetes에 대 한 브리지를 구성 합니다. Visual Studio에서 Kubernetes 연결을 사용 하는 방법에 대 한 자세한 내용은 [Kubernetes에 브리지 사용][use-btk-vs]을 참조 하세요.
1. Kubernetes 디버그 프로필에 새로 만든 Bridge를 사용 하 여 Visual Studio에서 디버깅을 시작 합니다.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Visual Studio Code를 사용 하 여 Kubernetes에 대 한 브리지 평가

1. [Kubernetes 확장에 대 한 브리지][vsc-marketplace]를 설치 합니다.
1. 새 AKS 클러스터를 만들고 응용 프로그램을 배포 합니다. [예제 응용 프로그램][btk-sample-app]을 사용할 수도 있습니다.
1. 배포 된 응용 프로그램에서 Kubernetes에 대 한 브리지를 구성 합니다. Bridge를 사용 하 여 Visual Studio Code에서 Kubernetes 하는 방법에 대 한 자세한 내용은 [Kubernetes에 브리지 사용][use-btk-vsc]을 참조 하세요.
1. Kubernetes 시작 프로필에 새로 만든 Bridge를 사용 하 여 Visual Studio에서 디버깅을 시작 합니다.

## <a name="next-steps"></a>다음 단계

Kubernetes에 대 한 브리지가 작동 하는 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Bridge to Kubernetes 작동 방식][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes?view=vs-2019#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes?view=vs-2019#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/

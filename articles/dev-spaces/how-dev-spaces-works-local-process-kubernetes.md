---
title: Kubernetes를 사용하는 로컬 프로세스의 작동 방식
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Kubernetes에서 로컬 프로세스를 사용 하 여 개발 컴퓨터를 Kubernetes 클러스터에 연결 하는 프로세스에 대해 설명 합니다.
keywords: Kubernetes, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너를 사용 하는 로컬 프로세스
ms.openlocfilehash: dd126fc55a86b1de115239a31e5adb7b1d264846
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84974413"
---
# <a name="how-local-process-with-kubernetes-works"></a>Kubernetes를 사용하는 로컬 프로세스의 작동 방식

Kubernetes를 사용 하는 로컬 프로세스를 사용 하면 응용 프로그램의 나머지 부분을 사용 하 여 Kubernetes 클러스터에 연결 하면서도 개발 컴퓨터에서 코드를 실행 하 고 디버그할 수 있습니다. 예를 들어 상호 의존적인 많은 서비스 및 데이터베이스를 포함 하는 많은 마이크로 서비스 아키텍처가 있는 경우 개발 컴퓨터에 이러한 종속성을 복제 하는 것은 어려울 수 있습니다. 또한 내부 루프 개발 중에 각 코드 변경에 대해 코드를 작성 하 고 Kubernetes 클러스터에 배포 하는 작업은 속도가 느리고 시간이 오래 걸리고 디버거에서 사용 하기 어려울 수 있습니다.

Kubernetes를 사용 하는 로컬 프로세스에서는 개발 컴퓨터와 클러스터 간에 직접 연결을 만들어 클러스터에 코드를 빌드하고 배포할 필요가 없습니다. 디버깅 하는 동안 개발 컴퓨터를 클러스터에 연결 하면 Docker 또는 Kubernetes 구성을 만들지 않고도 전체 응용 프로그램의 컨텍스트에서 서비스를 신속 하 게 테스트 하 고 개발할 수 있습니다.

Kubernetes를 사용 하는 로컬 프로세스는 연결 된 Kubernetes 클러스터와 개발 컴퓨터 간에 트래픽을 리디렉션합니다. 이러한 트래픽 리디렉션을 통해 Kubernetes 클러스터에서 실행 되는 서비스와 개발 컴퓨터의 코드가 동일한 Kubernetes 클러스터에 있는 것 처럼 통신할 수 있습니다. Kubernetes를 사용 하는 로컬 프로세스도 개발 컴퓨터의 Kubernetes 클러스터에서 pod에 사용할 수 있는 환경 변수 및 탑재 된 볼륨을 복제 하는 방법을 제공 합니다. 개발 컴퓨터에서 환경 변수 및 탑재 된 볼륨에 대 한 액세스를 제공 하면 해당 종속성을 수동으로 복제 하지 않고도 코드를 빠르게 처리할 수 있습니다.

## <a name="using-local-process-with-kubernetes"></a>Kubernetes에서 로컬 프로세스 사용

Kubernetes를 사용 하 여 로컬 프로세스를 사용 하려면 macOS 또는 Windows 10 뿐만 아니라 [AZURE DEV SPACES CLI가 설치][azds-cli]되어 실행 되는 [Azure Dev Spaces][azds-vs-code] 및 [Azure Kubernetes Service][az-aks-vs-code] 확장에 [Visual Studio Code][vs-code] 해야 합니다. *ASP.NET 및 웹 개발* 및 *Azure 개발* 워크 로드와 함께 Windows 10에서 실행 되는 [Visual Studio 2019][visual-studio] 을 사용 하 고, [설치 된 Azure Dev Spaces CLI][azds-cli]뿐만 아니라 *LocalKubernetesDebugging* 미리 보기 기능 플래그를 사용할 수도 있습니다. Kubernetes에서 로컬 프로세스를 사용 하 여 Kubernetes 클러스터에 대 한 연결을 설정 하는 경우 클러스터의 기존 pod로 들어오고 나가는 모든 트래픽을 개발 컴퓨터로 리디렉션하는 옵션이 있습니다.

> [!NOTE]
> Kubernetes에서 로컬 프로세스를 사용 하는 경우 개발 컴퓨터로 리디렉션할 서비스의 이름을 묻는 메시지가 표시 됩니다. 이 옵션은 리디렉션의 pod를 식별 하는 편리한 방법입니다. Kubernetes 클러스터와 개발 컴퓨터 간의 모든 리디렉션은 pod 용입니다.

Kubernetes를 사용 하는 로컬 프로세스에서 클러스터에 대 한 연결이 설정 되 면 다음을 수행 합니다.

* 는 클러스터에서 바꿀 서비스, 코드에 사용할 개발 컴퓨터의 포트, 일회성 작업으로 코드에 대 한 시작 작업을 구성 하 라는 메시지를 표시 합니다.
* 클러스터에서 pod의 컨테이너를 개발 컴퓨터로 트래픽을 리디렉션하는 원격 에이전트 컨테이너로 바꿉니다.
* 개발 컴퓨터에서 [kubectl 포트][kubectl-port-forward] 를 실행 하 여 개발 컴퓨터에서 클러스터를 실행 하는 원격 에이전트로 트래픽을 전달 합니다.
* 원격 에이전트를 사용 하 여 클러스터에서 환경 정보를 수집 합니다. 이 환경 정보에는 환경 변수, 표시 되는 서비스, 볼륨 탑재 및 비밀 탑재가 포함 됩니다.
* 개발 컴퓨터의 서비스가 클러스터에서 실행 되는 것과 동일한 변수에 액세스할 수 있도록 Visual Studio 또는 Visual Studio Code에서 환경을 설정 합니다.  
* 호스트 파일을 업데이트 하 여 클러스터의 서비스를 개발 컴퓨터의 로컬 IP 주소에 매핑합니다. 이러한 호스트 파일 항목을 통해 개발 컴퓨터에서 실행 되는 코드를 사용 하 여 클러스터에서 실행 되는 다른 서비스에 요청을 수행할 수 있습니다. Hosts 파일을 업데이트 하기 위해 Kubernetes를 사용 하는 로컬 프로세스는 클러스터에 연결할 때 개발 컴퓨터에 대 한 관리자 액세스를 요청 합니다.
* 개발 컴퓨터에서 코드를 실행 하 고 디버깅 하기 시작 합니다. 필요한 경우 Kubernetes를 사용 하는 로컬 프로세스는 현재 해당 포트를 사용 중인 서비스나 프로세스를 중지 하 여 개발 컴퓨터에서 필요한 포트를 해제 합니다.

클러스터에 대 한 연결을 설정한 후에는 컨테이너 화 없이 컴퓨터에서 기본적으로 코드를 실행 하 고 디버그할 수 있으며, 코드는 나머지 클러스터와 직접 상호 작용할 수 있습니다. 원격으로 실행 되는 코드에서 해당 트래픽을 허용 하 고 처리할 수 있도록 원격 에이전트가 수신 하는 모든 네트워크 트래픽이 연결 중에 지정 된 로컬 포트로 리디렉션됩니다. 개발 컴퓨터에서 실행 되는 코드에서 클러스터의 환경 변수, 볼륨 및 암호를 사용할 수 있습니다. 또한 Kubernetes를 사용 하 여 로컬 프로세스에서 개발자 컴퓨터에 추가 된 호스트 파일 항목 및 포트 전달 때문에, 코드는 클러스터의 서비스 이름을 사용 하 여 클러스터에서 실행 되는 서비스로 네트워크 트래픽을 전송 하 고 클러스터에서 실행 중인 서비스에 트래픽을 전달할 수 있습니다. 사용자가 연결 하는 전체 시간 동안 개발 컴퓨터와 클러스터 간에 트래픽이 라우팅됩니다.

## <a name="diagnostics-and-logging"></a>진단 및 로깅

Kubernetes에서 로컬 프로세스를 사용 하 여 클러스터에 연결 하는 경우 클러스터의 진단 로그가 개발 컴퓨터의 [임시 디렉터리][azds-tmp-dir]에 기록 됩니다. Visual Studio Code 사용 하 여 *진단 정보 표시* 명령을 사용 하 여 클러스터의 현재 환경 변수 및 DNS 항목을 인쇄할 수도 있습니다.

## <a name="limitations"></a>제한 사항

Kubernetes를 사용 하는 로컬 프로세스에는 다음과 같은 제한 사항이 있습니다.

* Kubernetes를 사용 하는 로컬 프로세스는 단일 서비스의 트래픽을 개발 컴퓨터로 리디렉션합니다. Kubernetes와 함께 로컬 프로세스를 사용 하 여 여러 서비스를 동시에 리디렉션할 수는 없습니다.
* 서비스는 해당 서비스에 연결 하기 위해 단일 pod에 의해 지원 되어야 합니다. 복제본이 있는 서비스와 같은 여러 pod를 사용 하 여 서비스에 연결할 수 없습니다.
* Pod에는 Kubernetes를 사용 하는 로컬 프로세스에서 성공적으로 연결 하기 위해 해당 pod에서 실행 되는 단일 컨테이너가 있을 수 있습니다. Kubernetes를 사용 하는 로컬 프로세스는 서비스 메시에 의해 삽입 된 사이드카 컨테이너와 같은 추가 컨테이너가 있는 pod를 사용 하 여 서비스에 연결할 수 없습니다.
* Kubernetes를 사용 하는 로컬 프로세스에는 호스트 파일을 편집 하기 위해 개발 컴퓨터에서 실행 하는 데 상승 된 권한이 필요 합니다.

## <a name="next-steps"></a>다음 단계

Kubernetes에서 로컬 프로세스를 사용 하 여 클러스터에 로컬 개발 컴퓨터에 연결 하는 방법에 대 한 자세한 내용은 with [Visual Studio Code Kubernetes과 함께 로컬 프로세스 사용][local-process-kubernetes-vs-code] 및 [Visual Studio에서 Kubernetes를 사용][local-process-kubernetes-vs]하 여 로컬 프로세스 사용을 참조 하세요.

[azds-cli]: how-to/install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[local-process-kubernetes-vs-code]: how-to/local-process-kubernetes-vs-code.md
[local-process-kubernetes-vs]: how-to/local-process-kubernetes-visual-studio.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[visual-studio]: https://www.visualstudio.com/vs/
[vs-code]: https://code.visualstudio.com/download
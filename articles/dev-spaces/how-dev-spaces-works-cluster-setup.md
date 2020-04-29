---
title: Azure Dev Spaces 작동을 위해 클러스터를 설정 하는 방법
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces 작동을 위해 Azure Kubernetes 서비스 클러스터를 설정 하는 방법을 설명 합니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241726"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Azure Dev Spaces 작동을 위해 클러스터를 설정 하는 방법

Azure Dev Spaces은 Kubernetes 응용 프로그램을 신속 하 게 반복 하 고 디버그할 수 있는 여러 가지 방법을 제공 하 고 AKS (Azure Kubernetes Service) 클러스터에서 팀과 공동 작업을 합니다. 한 가지 방법은 [클러스터에서 직접 서비스를 실행][how-it-works-up] 하 고 [추가 네트워킹 및 라우팅 기능][how-it-works-routing]을 사용할 수 있도록 AKS 클러스터에 대 한 Azure Dev Spaces를 사용 하도록 설정 하는 것입니다. 이 문서에서는 클러스터를 준비 하 고 Azure Dev Spaces를 사용 하도록 설정 하면 어떤 일이 발생 하는지 설명 합니다.

## <a name="prepare-your-aks-cluster"></a>AKS 클러스터 준비

AKS 클러스터에서 Dev 공간을 준비 하려면 AKS 클러스터가 [Azure Dev Spaces 지원 되][supported-regions] 는 지역에 있고 Kubernetes 1.10.3 이상을 실행 중인지 확인 합니다. Azure Portal에서 클러스터에 대 한 Azure Dev Spaces를 사용 하도록 설정 하려면 클러스터로 이동 하 고, *Dev spaces*를 클릭 하 고, *dev Spaces 사용* 을 *예*로 변경 하 고, *저장*을 클릭 합니다. 을 실행 `az aks use-dev-spaces`하 여 Azure CLI에서 Azure Dev Spaces을 사용 하도록 설정할 수도 있습니다.

Dev 공간에 대해 AKS 클러스터를 설정 하는 예제는 [팀 개발 빠른][quickstart-team]시작을 참조 하세요.

AKS 클러스터에서 Azure Dev Spaces 사용 하도록 설정 되 면 클러스터에 대 한 컨트롤러를 설치 합니다. 컨트롤러가 AKS 클러스터 외부에 상주 합니다. 클라이언트 쪽 도구와 AKS 클러스터 간의 동작 및 통신을 구동 합니다. 사용 하도록 설정 되 면 클라이언트 쪽 도구를 사용 하 여 컨트롤러와 상호 작용할 수 있습니다.

컨트롤러는 다음 작업을 수행 합니다.

* 개발 공간 만들기 및 선택 항목을 관리 합니다.
* 응용 프로그램의 투구 차트를 설치 하 고 Kubernetes 개체를 만듭니다.
* 응용 프로그램의 컨테이너 이미지를 빌드합니다.
* 응용 프로그램을 AKS에 배포 합니다.
* 소스 코드가 변경 될 때 증분 빌드와 다시 시작 합니다.
* 로그 및 HTTP 추적을 관리 합니다.
* Stdout 및 stderr을 클라이언트 쪽 도구에 전달 합니다.
* 공간 내의 응용 프로그램에 대 한 라우팅과 부모 및 하위 공간을 모두 구성 합니다.

컨트롤러는 클러스터 외부의 별도의 Azure 리소스 이며 클러스터의 리소스에 대해 다음을 수행 합니다.

* 개발 공간으로 사용할 Kubernetes 네임 스페이스를 만들거나 지정 합니다.
* *Azds*라는 Kubernetes 네임 스페이스가 있는 경우이를 제거 하 고 새 네임 스페이스를 만듭니다.
* Kubernetes webhook 구성을 배포 합니다.
* Webhook 허용 서버를 배포 합니다.

AKS 클러스터에서 다른 Azure Dev Spaces 구성 요소에 대 한 서비스 호출을 수행 하는 데 사용 하는 것과 동일한 서비스 주체를 사용 합니다.

![클러스터 준비 Azure Dev Spaces](media/how-dev-spaces-works/prepare-cluster.svg)

Azure Dev Spaces를 사용 하려면 Dev 공간이 하나 이상 있어야 합니다. Azure Dev Spaces는 AKS 클러스터 내에서 Dev 공간에 Kubernetes 네임 스페이스를 사용 합니다. 컨트롤러를 설치 하는 경우 새 Kubernetes 네임 스페이스를 만들거나 첫 번째 dev 공간으로 사용할 기존 네임 스페이스를 선택 하 라는 메시지가 표시 됩니다. 기본적으로 컨트롤러는 기존 *기본* Kubernetes 네임 스페이스를 첫 번째 dev 공간으로 업그레이드 하도록 제공 합니다.

네임 스페이스를 dev 공간으로 지정 하면 컨트롤러는 해당 네임 스페이스에 *azds.io/space=true* 레이블을 추가 하 여이를 dev 공간으로 식별 합니다. 사용자가 만들거나 지정 하는 초기 개발 공간은 클러스터를 준비한 후 기본적으로 선택 됩니다. 공백을 선택 하면 새 작업을 만들기 위해 Azure Dev Spaces에서 사용 됩니다.

클라이언트 쪽 도구를 사용 하 여 새 개발 공간을 만들고 기존 개발 공간을 제거할 수 있습니다. Kubernetes의 제한으로 인해 *기본* 개발 공간은 제거할 수 없습니다. 또한 컨트롤러는 *azds* 이라는 기존 Kubernetes 네임 스페이스를 제거 하 여 클라이언트 쪽 `azds` 도구에서 사용 하는 명령과 충돌 하지 않도록 합니다.

Kubernetes webhook 허용 서버는 계측을 위해 배포 하는 동안 세 개의 컨테이너를 사용 하 여 pod를 삽입 하는 데 사용 됩니다. **이러한 세 컨테이너는 모두 AKS 클러스터에서 루트 액세스로 실행 됩니다.** 또한 AKS 클러스터에서 다른 Azure Dev Spaces 구성 요소에 대 한 서비스 호출을 수행 하는 데 사용 하는 것과 동일한 서비스 주체를 사용 합니다.

![Azure Dev Spaces Kubernetes webhook 허용 서버](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Devspaces-proxy 컨테이너는 응용 프로그램 컨테이너에서 들어오고 나가는 모든 TCP 트래픽을 처리 하는 사이드카 컨테이너 이며 라우팅을 수행 하는 데 도움이 됩니다. 특정 공백이 사용 되는 경우 devspaces 프록시 컨테이너는 HTTP 메시지를 라우팅합니다. 예를 들어 부모 및 자식 공간의 응용 프로그램 간에 HTTP 메시지를 라우팅하는 데 도움이 될 수 있습니다. 모든 비 HTTP 트래픽은 devspaces-프록시를 수정 되지 않은 상태로 전달 합니다. 또한 devspaces 프록시 컨테이너는 모든 인바운드 및 아웃 바운드 HTTP 메시지를 기록 하 고이를 클라이언트 쪽 도구에 추적으로 보냅니다. 개발자는 이러한 추적을 보고 응용 프로그램의 동작을 검사할 수 있습니다.

Devspaces-proxy-init 컨테이너는 응용 프로그램 컨테이너에 공간 계층 구조를 기반으로 하는 추가 라우팅 규칙을 추가 하는 [init 컨테이너](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) 입니다. 시작 하기 전에 응용 프로그램 컨테이너의 */etc/resolv.conf* 파일 및 iptables 구성을 업데이트 하 여 라우팅 규칙을 추가 합니다. */Etc/resolv.conf* 에 대 한 업데이트는 부모 공간에서 서비스에 대 한 DNS 확인을 허용 합니다. Iptables 구성 업데이트는 응용 프로그램의 컨테이너에 대 한 모든 TCP 트래픽이 devspaces 프록시를 통해 라우팅되는 지 확인 합니다. Kubernetes에서 추가 하는 규칙 외에도 devspaces의 모든 업데이트-프록시-init가 발생 합니다.

Devspaces-build 컨테이너는 init 컨테이너 이며 프로젝트 소스 코드와 Docker 소켓이 탑재 되어 있습니다. 프로젝트 소스 코드 및 Docker에 대 한 액세스를 통해 pod에서 직접 응용 프로그램 컨테이너를 빌드할 수 있습니다.

> [!NOTE]
> Azure Dev Spaces는 동일한 노드를 사용 하 여 응용 프로그램의 컨테이너를 빌드하고 실행 합니다. 따라서 응용 프로그램을 빌드 및 실행 하기 위해 Azure Dev Spaces에는 외부 컨테이너 레지스트리가 필요 하지 않습니다.

Kubernetes webhook 허용 서버는 AKS 클러스터에서 만들어진 모든 새 pod를 수신 대기 합니다. 이 pod가 *azds.io/space=true* 레이블을 사용 하 여 네임 스페이스에 배포 되는 경우 추가 컨테이너를 사용 하 여 해당 pod를 삽입 합니다. Devspaces-build 컨테이너는 응용 프로그램의 컨테이너가 클라이언트 쪽 도구를 사용 하 여 실행 되는 경우에만 삽입 됩니다.

AKS 클러스터를 준비한 후에는 클라이언트 쪽 도구를 사용 하 여 개발 공간에서 코드를 준비 하 고 실행할 수 있습니다.

## <a name="client-side-tooling"></a>클라이언트 쪽 도구

클라이언트 쪽 도구를 사용 하 여 다음을 수행할 수 있습니다.
* 응용 프로그램에 대 한 Dockerfile, 투구 차트 및 Azure Dev Spaces 구성 파일을 생성 합니다.
* 부모 및 자식 개발 공간을 만듭니다.
* 응용 프로그램을 빌드하고 시작 하도록 컨트롤러에 지시 합니다.

응용 프로그램이 실행 되는 동안 클라이언트 쪽 도구는 다음을 수행 합니다.
* AKS에서 실행 되는 응용 프로그램에서 stdout 및 stderr을 받아서 표시 합니다.
* 는 [포트 전달을](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) 사용 하 여 http:/localhost.를 사용 하는 응용\/프로그램에 대 한 웹 액세스를 허용 합니다.
* AKS에서 실행 중인 응용 프로그램에 디버거를 연결 합니다.
* 증분 빌드에 대 한 변경 내용이 검색 되 면 개발 공간에 소스 코드를 동기화 하 여 빠른 반복을 허용 합니다.
* 개발자 컴퓨터를 AKS 클러스터에 직접 연결할 수 있습니다.

명령의 일부로 `azds` 명령줄에서 클라이언트 쪽 도구를 사용할 수 있습니다. 클라이언트 쪽 도구를 다음과 함께 사용할 수도 있습니다.

* [Azure Dev Spaces 확장](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)을 사용 하 여 Visual Studio Code 합니다.
* [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools)포함 된 Visual Studio

## <a name="next-steps"></a>다음 단계

클라이언트 쪽 도구를 사용 하 여 개발 공간에서 코드를 준비 하 고 실행 하는 방법에 대 한 자세한 내용은 [Azure Dev Spaces 작동을 위해 프로젝트를 준비 하는 방법][how-it-works-prep]을 참조 하세요.

팀 개발을 위한 Azure Dev Spaces 사용을 시작 하려면 Azure Dev Spaces 빠른 시작 [에서 팀 개발][quickstart-team] 을 참조 하세요.

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md
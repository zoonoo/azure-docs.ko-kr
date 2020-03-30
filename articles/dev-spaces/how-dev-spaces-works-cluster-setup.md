---
title: Azure 개발자 공간에 대한 클러스터 설정의 작동 방식
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure 개발자 공간에 대한 Azure Kubernetes 서비스 클러스터 설정의 작동 방식에 대해 설명합니다.
keywords: Azure 개발자 공간, 개발자 공간, 도커, 쿠버넷, Azure, AKS, Azure Kubernetes 서비스, 컨테이너
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241726"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Azure 개발자 공간에 대한 클러스터 설정의 작동 방식

Azure 개발자 공간은 Kubernetes 응용 프로그램을 빠르게 반복 및 디버깅하고 AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업하는 여러 가지 방법을 제공합니다. 한 가지 방법은 AKS 클러스터에서 Azure 개발자 공간을 활성화하여 [클러스터에서 직접 서비스를 실행하고][how-it-works-up] 추가 네트워킹 및 라우팅 [기능을][how-it-works-routing]사용할 수 있도록 하는 것입니다. 이 문서에서는 클러스터를 준비하고 Azure 개발자 공간을 사용하도록 설정할 때 발생하는 작업을 설명합니다.

## <a name="prepare-your-aks-cluster"></a>AKS 클러스터 준비

개발자 공간에 대한 AKS 클러스터를 준비하려면 AKS 클러스터가 [Azure 개발자 공간에서 지원하는][supported-regions] 지역에 있고 Kubernetes 1.10.3 이상을 실행 중인지 확인합니다. Azure 포털에서 클러스터의 Azure 개발자 공간을 활성화하려면 클러스터로 이동한 다음 *개발자 공간을*클릭하고 *개발자 공간 사용을* *예로*변경하고 *저장을*클릭합니다. 을 실행하여 `az aks use-dev-spaces`Azure CLI에서 Azure 개발자 공간을 활성화할 수도 있습니다.

개발자 공간에 대한 AKS 클러스터를 설정하는 예는 [팀 개발 퀵스타트를][quickstart-team]참조하십시오.

AKS 클러스터에서 Azure 개발자 공간을 사용하도록 설정하면 클러스터에 대한 컨트롤러가 설치됩니다. 컨트롤러는 AKS 클러스터 외부에 있습니다. 클라이언트 측 툴링과 AKS 클러스터 간의 동작 및 통신을 구동합니다. 활성화되면 클라이언트 측 툴링을 사용하여 컨트롤러와 상호 작용할 수 있습니다.

컨트롤러는 다음 작업을 수행합니다.

* 개발 공간 생성 및 선택을 관리합니다.
* 응용 프로그램의 Helm 차트를 설치하고 Kubernetes 개체를 만듭니다.
* 응용 프로그램의 컨테이너 이미지를 빌드합니다.
* 응용 프로그램을 AKS에 배포합니다.
* 소스 코드가 변경될 때 증분 빌드 및 다시 시작됩니다.
* 로그 및 HTTP 추적을 관리합니다.
* 클라이언트 측 툴링으로 stdout 및 stderr를 전달합니다.
* 공간 내의 응용 프로그램뿐만 아니라 부모 및 하위 공간 전체에 대한 라우팅을 구성합니다.

컨트롤러는 클러스터 외부의 별도의 Azure 리소스이며 클러스터의 리소스에 대해 다음을 수행합니다.

* 개발 공간으로 사용할 Kubernetes 네임스페이스를 만들거나 지정합니다.
* *azds라는*Kubernetes 네임스페이스를 제거하고 있는 경우 새 네임스페이스를 만듭니다.
* Kubernetes 웹후크 구성을 배포합니다.
* 웹후크 입학 서버를 배포합니다.

AKS 클러스터가 다른 Azure 개발자 공간 구성 요소에 대한 서비스 호출을 만드는 데 사용하는 것과 동일한 서비스 주체를 사용합니다.

![Azure 개발자 공간은 클러스터를 준비합니다.](media/how-dev-spaces-works/prepare-cluster.svg)

Azure 개발자 공간을 사용하려면 개발 공간이 하나 이상 있어야 합니다. Azure 개발자 공간은 개발 공간에 AKS 클러스터 내에서 Kubernetes 네임스페이스를 사용합니다. 컨트롤러를 설치할 때 새 Kubernetes 네임스페이스를 만들거나 첫 번째 개발 공간으로 사용할 기존 네임스페이스를 선택하라는 메시지가 표시됩니다. 기본적으로 컨트롤러는 기존 *기본* Kubernetes 네임스페이스를 첫 번째 개발 공간으로 업그레이드합니다.

네임스페이스가 개발 공간으로 지정되면 컨트롤러는 *해당* 네임스페이스에 azds.io/space=true 레이블을 추가하여 개발 공간으로 식별합니다. 클러스터를 준비한 후 기본적으로 작성하거나 지정하는 초기 개발 공간이 선택됩니다. 공백을 선택하면 Azure 개발자 공간에서 새 워크로드를 만드는 데 사용됩니다.

클라이언트 측 툴링을 사용하여 새 개발 공간을 만들고 기존 개발 공간을 제거할 수 있습니다. Kubernetes의 제한으로 인해 *기본* 개발 공간을 제거할 수 없습니다. 또한 컨트롤러는 *azds라는* 기존 Kubernetes 네임스페이스를 `azds` 제거하여 클라이언트 측 툴링에서 사용되는 명령과의 충돌을 방지합니다.

Kubernetes 웹후크 승인 서버는 계측을 위해 배포하는 동안 세 개의 컨테이너가 있는 포드를 삽입하는 데 사용됩니다: devspaces-프록시 컨테이너, devspaces-프록시-init 컨테이너 및 devspaces-build 컨테이너. **이 세 가지 컨테이너는 모두 AKS 클러스터에서 루트 액세스로 실행됩니다.** 또한 AKS 클러스터가 다른 Azure 개발자 공간 구성 요소에 대한 서비스 호출을 하는 데 사용하는 것과 동일한 서비스 주체를 사용합니다.

![Azure 개발자 공간 Kubernetes 웹후크 입장 서버](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

devspaces-프록시 컨테이너는 응용 프로그램 컨테이너의 모든 TCP 트래픽을 처리하고 라우팅을 수행하는 데 도움이 되는 사이드카 컨테이너입니다. devspaces-프록시 컨테이너는 특정 공백을 사용하는 경우 HTTP 메시지를 다시 라우팅합니다. 예를 들어 상위 공간과 자식 공간의 응용 프로그램 간에 HTTP 메시지를 라우팅하는 데 도움이 될 수 있습니다. HTTP가 아닌 모든 트래픽은 수정되지 않은 devspaces-프록시를 통과합니다. 또한 devspaces-프록시 컨테이너는 모든 인바운드 및 아웃바운드 HTTP 메시지를 기록하고 이를 클라이언트 측 도구에 추적으로 보냅니다. 그런 다음 개발자가 이러한 추적을 보고 응용 프로그램의 동작을 검사할 수 있습니다.

devspaces-proxy-init 컨테이너는 응용 프로그램의 컨테이너에 공간 계층 구조를 기반으로 추가 라우팅 규칙을 추가 하는 [초기화](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) 컨테이너입니다. 응용 프로그램 컨테이너의 */etc/resolv.conf* 파일 및 iptables 구성을 시작하기 전에 업데이트하여 라우팅 규칙을 추가합니다. */etc/resolv.conf에* 대한 업데이트는 부모 공간에서 서비스의 DNS 확인을 허용합니다. iptables 구성 업데이트는 응용 프로그램의 컨테이너안팎의 모든 TCP 트래픽이 devspaces-proxy를 통해 라우팅되도록 합니다. 개발자 공간 프록시-init의 모든 업데이트는 Kubernetes가 추가하는 규칙 외에도 발생합니다.

devspaces-build 컨테이너는 초기화 컨테이너이며 프로젝트 소스 코드와 Docker 소켓이 장착되어 있습니다. 프로젝트 소스 코드 및 Docker에 대한 액세스를 통해 응용 프로그램 컨테이너를 포드에서 직접 빌드할 수 있습니다.

> [!NOTE]
> Azure 개발자 공간은 동일한 노드를 사용하여 응용 프로그램의 컨테이너를 빌드하고 실행합니다. 따라서 Azure 개발자 공간은 응용 프로그램을 빌드하고 실행하기 위한 외부 컨테이너 레지스트리가 필요하지 않습니다.

Kubernetes 웹후크 승인 서버는 AKS 클러스터에서 생성된 새 포드를 수신합니다. 해당 포드가 *azds.io/space=true* 레이블이 있는 모든 네임스페이스에 배포되면 해당 포드에 추가 컨테이너가 삽입됩니다. devspaces-build 컨테이너는 클라이언트 측 툴링을 사용하여 응용 프로그램의 컨테이너가 실행되는 경우에만 주입됩니다.

AKS 클러스터를 준비한 후에는 클라이언트 측 도구를 사용하여 개발 공간에서 코드를 준비하고 실행할 수 있습니다.

## <a name="client-side-tooling"></a>클라이언트 측 툴링

클라이언트 측 툴링을 사용하면 다음을 수행할 수 있습니다.
* 응용 프로그램에 대한 Dockerfile, 헬름 차트 및 Azure 개발자 공간 구성 파일을 생성합니다.
* 부모 및 자식 개발 공간을 만듭니다.
* 컨트롤러에 응용 프로그램을 빌드하고 시작하라고 지시합니다.

응용 프로그램이 실행되는 동안 클라이언트 측 툴링도 있습니다.
* AKS에서 실행 중인 응용 프로그램에서 stdout 및 stderr를 수신하고 표시합니다.
* [포트 포워드를](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) 사용하여 http:\//localhost를 사용하여 응용 프로그램에 대한 웹 액세스를 허용합니다.
* AKS에서 실행 중인 응용 프로그램에 디버거를 연결합니다.
* 증분 빌드에 대한 변경이 감지되면 소스 코드를 개발 공간에 동기화하여 빠른 반복을 허용합니다.
* 개발자 컴퓨터를 AKS 클러스터에 직접 연결할 수 있습니다.

명령줄에서 클라이언트 측 도구를 `azds` 명령의 일부로 사용할 수 있습니다. 다음과 같은 경우 클라이언트 측 툴링을 사용할 수도 있습니다.

* [Azure 개발자 공간 확장을](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)사용하는 시각적 스튜디오 코드.
* [Kubernetes에 대한 비주얼 스튜디오 도구와 비주얼 스튜디오](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>다음 단계

개발자 공간에서 코드를 준비하고 실행하는 데 클라이언트 쪽 도구를 사용하는 방법에 대해 자세히 알아보려면 [Azure 개발자 공간에 대한 프로젝트 준비의 작동 방식을][how-it-works-prep]참조하세요.

팀 개발을 위해 Azure 개발자 공간 사용을 시작하려면 [Azure 개발자 공간 퀵스타트의 팀 개발을][quickstart-team] 참조하세요.

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md
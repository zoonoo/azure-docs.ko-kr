---
title: Azure 개발자 공간에서 라우팅이 작동하는 방식
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure 개발자 공간에 전원을 공급하는 프로세스와 라우팅 작동 방식에 대해 설명합니다.
keywords: Azure 개발자 공간, 개발자 공간, 도커, 쿠버넷, Azure, AKS, Azure Kubernetes 서비스, 컨테이너
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241388"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Azure 개발자 공간에서 라우팅이 작동하는 방식

Azure 개발자 공간은 Kubernetes 응용 프로그램을 빠르게 반복 및 디버깅하고 AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업하는 여러 가지 방법을 제공합니다. 프로젝트가 개발 공간에서 실행되면 Azure 개발자 공간은 프로젝트에 대한 추가 네트워킹 및 라우팅 기능을 제공합니다.

이 문서에서는 라우팅이 개발자 공간에서 작동하는 방식에 대해 설명합니다.

## <a name="how-routing-works"></a>라우팅 작동 방식

개발 공간은 AKS 위에 구축되며 동일한 [네트워킹 개념을](../aks/concepts-network.md)사용합니다. Azure 개발자 공간에는 중앙 집중식 *인그레스관리자* 서비스도 있으며 자체 받는 컨트롤러를 AKS 클러스터에 배포합니다. *inressmanager* 서비스는 개발 공간이 있는 AKS 클러스터를 모니터링하고 응용 프로그램 포드로 라우팅하기 위한 Ingress 개체가 있는 클러스터의 Azure 개발자 공간 인그레스 컨트롤러를 보강합니다. 각 포드의 devspaces-프록시 컨테이너는 URL을 `azds-route-as` 기반으로 하는 개발 공간에 HTTP 트래픽에 대한 HTTP 헤더를 추가합니다. 예를 들어 URL에 *http://azureuser.s.default.serviceA.fedcba09...azds.io* 대한 요청은 `azds-route-as: azureuser`에서 HTTP 헤더를 가져옵니다. devspaces-프록시 컨테이너는 이미 `azds-route-as` 있는 경우 헤더를 추가 하지 않습니다.

클러스터 외부에서 서비스에 대한 HTTP 요청이 이루어지면 요청은 Ingress 컨트롤러로 이동합니다. Ingress 컨트롤러는 받는 개체 및 규칙에 따라 요청을 해당 창으로 직접 라우팅합니다. 포드의 devspaces-proxy 컨테이너는 요청을 수신하고 `azds-route-as` URL을 기반으로 헤더를 추가한 다음 요청을 응용 프로그램 컨테이너로 라우팅합니다.

클러스터 내의 다른 서비스에서 서비스에 대한 HTTP 요청이 이루어지면 요청은 먼저 호출 서비스의 devspaces-proxy 컨테이너를 거칩니다. devspaces-프록시 컨테이너는 HTTP 요청을 보고 `azds-route-as` 헤더를 확인합니다. 헤더에 따라 devspaces-프록시 컨테이너는 헤더 값과 연결된 서비스의 IP 주소를 찾습니다. IP 주소가 발견되면 devspaces-프록시 컨테이너는 요청을 해당 IP 주소로 다시 라우팅합니다. IP 주소를 찾을 수 없는 경우 devspaces-프록시 컨테이너는 요청을 부모 응용 프로그램 컨테이너로 라우팅합니다.

예를 들어 응용 프로그램 *serviceA* 및 *serviceB는* *default라고*하는 부모 개발 공간에 배포됩니다. *serviceA는* *serviceB를* 사용하여 HTTP 호출을 합니다. Azure User는 *azureuser*라는 *기본* 공간을 기반으로 자식 개발 공간을 만듭니다. 또한 Azure User는 자체 버전의 *serviceA를* 자식 공간에 배포합니다. 요청이 이루어진 *http://azureuser.s.default.serviceA.fedcba09...azds.io*경우:

![Azure 개발자 공간 라우팅](media/how-dev-spaces-works/routing.svg)

1. Ingress 컨트롤러는 *SERVICEA.azureuser*인 URL과 연결된 포드에 대한 IP를 찾습니다.
1. Inress 컨트롤러는 Azure User의 개발 공간에서 포드에 대한 IP를 찾고 *serviceA.azureuser* 포드에 요청을 라우팅합니다.
1. *serviceA.azureuser* 포드의 devspaces-proxy 컨테이너는 요청을 `azds-route-as: azureuser` 수신하고 HTTP 헤더로 추가합니다.
1. *serviceA.azureuser* 포드의 devspaces-프록시 컨테이너는 *serviceA.azureuser* 포드의 *serviceA* 응용 프로그램 컨테이너에 요청을 라우팅합니다.
1. *serviceA.azureuser* 포드의 *serviceA* 응용 프로그램은 *serviceB를*호출합니다. *serviceA* 응용 프로그램에는 기존 `azds-route-as` 헤더를 보존하는 코드도 `azds-route-as: azureuser`포함되어 있습니다.
1. *serviceA.azureuser* 포드의 devspaces-proxy 컨테이너는 요청을 수신하고 `azds-route-as` 헤더 값을 기반으로 *serviceB의* IP를 찾습니다.
1. *serviceA.azureuser* 포드의 devspaces-프록시 컨테이너는 *serviceB.azureuser*에 대한 IP를 찾을 수 없습니다.
1. *serviceA.azureuser* 포드의 devspaces-프록시 컨테이너는 *serviceB.default인*부모 공간에서 *serviceB에* 대한 IP를 찾습니다.
1. *serviceA.azureuser* 포드의 devspaces-프록시 컨테이너는 *serviceB.default에* 대한 IP를 찾아 *serviceB.default* 포드로 요청을 라우팅합니다.
1. *serviceB.default* 포드의 devspaces-proxy 컨테이너는 요청을 수신하고 *serviceB.default* 포드에서 *serviceB* 응용 프로그램 컨테이너로 요청을 라우팅합니다.
1. *serviceB.default* 포드의 *serviceB* 응용 프로그램은 *serviceA.azureuser* 포드에 대한 응답을 반환합니다.
1. *serviceA.azureuser* 포드의 devspaces-proxy 컨테이너는 응답을 수신하고 *serviceA.azureuser* 포드에서 *serviceA* 응용 프로그램 컨테이너에 대한 응답을 라우팅합니다.
1. *serviceA* 응용 프로그램은 응답을 받은 다음 자체 응답을 반환합니다.
1. *serviceA.azureuser* 포드의 devspaces-proxy 컨테이너는 *serviceA* 응용 프로그램 컨테이너에서 응답을 수신하고 클러스터 외부의 원래 호출자에게 응답을 라우팅합니다.

HTTP가 아닌 다른 모든 TCP 트래픽은 수정되지 않은 Ingress 컨트롤러 및 devspaces-프록시 컨테이너를 통과합니다.

## <a name="sharing-a-dev-space"></a>개발 공간 공유

팀과 함께 작업할 때 [전체 팀에서 개발 공간을 공유하고](how-to/share-dev-spaces.md) 파생 개발 공간을 만들 수 있습니다. 개발 공간은 참여자가 개발 공간의 리소스 그룹에 액세스할 수 있는 모든 사용자가 사용할 수 있습니다.

다른 개발 공간에서 파생된 새 개발 공간을 만들 수도 있습니다. 파생 된 개발 공간을 만들 때 *azds.io/parent-space=PARENT-SPACE-NAME* 레이블은 파생 된 개발 공간의 네임 스페이스에 추가 됩니다. 또한 부모 개발 공간의 모든 응용 프로그램은 파생 된 개발 공간과 공유됩니다. 파생 된 개발 공간에 응용 프로그램의 업데이트 된 버전을 배포 하는 경우 파생 된 개발 공간에만 존재 하 고 부모 개발 공간 영향을 받지 않습니다. 파생 된 개발 공간 또는 *조부모* 공간의 최대 3 레벨을 가질 수 있습니다.

또한 파생된 개발 공간은 자체 응용 프로그램과 부모로부터 공유되는 응용 프로그램 간에 요청을 지능적으로 라우팅합니다. 라우팅은 파생된 개발 공간에서 응용 프로그램에 요청을 라우팅하고 부모 개발 공간에서 공유 응용 프로그램으로 다시 라우팅하여 작동합니다. 응용 프로그램이 부모 공간에 없는 경우 라우팅은 대조부모 공간의 공유 응용 프로그램으로 대체됩니다.

예를 들어:
* 개발 공간 *기본값에는* 응용 프로그램 *서비스A* 및 *serviceB가*있습니다.
* 개발 공간 *azureuser기본값에서* 파생됩니다. *default*
* 업데이트된 버전의 *serviceA가* *azureuser*에 배포됩니다.

*azureuser를*사용하는 경우 *serviceA에* 대한 모든 요청은 *azureuser*의 업데이트된 버전으로 라우팅됩니다. *serviceB에* 대한 요청은 먼저 *serviceB의* *azureuser* 버전으로 라우팅하려고 시도합니다. 존재하지 않으므로 *serviceB*의 *기본* 버전으로 라우팅됩니다. *Azureuser* 버전의 *serviceA가* 제거되면 *serviceA에* 대한 모든 요청이 *기본* 버전의 *serviceA*를 사용하는 것으로 대체됩니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces에서 라우팅을 사용하여 빠른 반복 및 개발을 제공하는 방법의 몇 가지 예를 보려면 [개발 컴퓨터를 개발 공간에 연결하는 방법,][how-it-works-connect]Azure 개발자 공간으로 코드를 원격 [디버깅하는 방법][how-it-works-remote-debugging]및 Azure [Kubernetes 서비스에 & GitHub 작업.][pr-flow]

팀 개발을 위해 Azure 개발자 공간에서 라우팅을 사용하려면 [Azure 개발자 공간 퀵스타트의 팀 개발을][quickstart-team] 참조하세요.

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md
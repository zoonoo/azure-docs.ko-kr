---
title: Azure Dev Spaces의 작동 원리
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure 개발자 공간에 전원을 공급하는 프로세스에 대해 설명합니다.
keywords: Azure 개발자 공간, 개발자 공간, 도커, 쿠버넷, Azure, AKS, Azure Kubernetes 서비스, 컨테이너
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234981"
---
# <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces의 작동 원리

Kubernetes 응용 프로그램을 개발하는 것은 어려울 수 있습니다. Docker 및 Kubernetes 구성 파일이 필요합니다. 응용 프로그램을 로컬로 테스트하고 다른 종속 서비스와 상호 작용하는 방법을 파악해야 합니다. 한 번에 여러 서비스 개발 및 테스트를 개발자 팀과 함께 처리해야 할 수 있습니다.

Azure 개발자 공간은 Kubernetes 응용 프로그램을 빠르게 반복 및 디버깅하고 팀과 공동 작업하는 여러 가지 방법을 제공합니다. 이 문서에서는 Azure 개발자 공간에서 수행할 수 있는 작업과 작동 방식에 대해 설명합니다.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Kubernetes 애플리케이션을 빠르게 반복하고 디버깅

Azure 개발자 공간은 AKS 클러스터의 컨텍스트에서 Kubernetes 응용 프로그램을 개발, 테스트 및 반복하는 노력을 줄입니다. 이러한 노력의 감소를 통해 개발자는 응용 프로그램의 비즈니스 논리에 집중할 수 있으며 Kubernetes에서 실행되도록 서비스를 구성하지 않아도 됩니다.

### <a name="connect-your-development-machine-to-aks"></a>개발 기계를 AKS에 연결

Azure 개발자 공간을 사용하면 개발 컴퓨터를 AKS 클러스터에 연결하여 클러스터에서 실행 중인 것처럼 개발 컴퓨터에서 코드를 실행하고 디버깅할 수 있습니다. Azure Dev Spaces는 개발 컴퓨터와 클러스터 간에 트래픽을 리디렉션하는 원격 에이전트 역할을 하는 클러스터에서 포드를 실행하여 연결된 AKS 클러스터 간의 트래픽을 리디렉션합니다. 이 트래픽 리디렉션을 사용하면 AKS 클러스터에서 실행중인 개발 컴퓨터 및 서비스의 코드가 동일한 AKS 클러스터에 있는 것처럼 통신할 수 있습니다. 개발 컴퓨터를 AKS에 연결하는 방법에 대한 자세한 내용은 [개발 컴퓨터를 AKS 클러스터에 연결하는 방법을][how-it-works-connect]참조하십시오.

### <a name="run-your-code-in-aks"></a>AKS에서 코드 실행

Azure 개발자 공간을 사용하면 개발 컴퓨터와 AKS 클러스터 간에 트래픽을 리디렉션하는 것 외에도 AKS에서 직접 코드를 구성하고 빠르게 실행할 수 있습니다. Visual Studio, Visual Studio 코드 또는 Azure 개발자 공간 CLI를 사용하면 Azure 개발자 공간에서 클러스터에 코드를 업로드한 다음 빌드하고 실행합니다. Azure 개발자 공간은 코드 변경 내용을 지능적으로 동기화하고 필요에 따라 변경 내용을 반영하도록 서비스를 다시 시작할 수도 있습니다. 코드를 실행하는 동안 빌드 로그 및 HTTP 추적이 클라이언트로 다시 스트리밍되므로 진행 상황을 모니터링하고 문제를 진단할 수 있습니다. Azure 개발자 공간을 사용하여 Visual Studio 및 Visual Studio 코드의 디버거를 Java, Node.js 및 .NET Core 서비스에 연결할 수도 있습니다. 자세한 내용은 [Azure 개발자 공간에 대한 프로젝트 준비 의 작동 방식,][how-it-works-prep]Azure 개발자 공간에서 코드 실행 방법 및 Azure [개발자 공간에서][how-it-works-up] [코드를 원격 디버깅하는 방법을 참조하세요.][how-it-works-remote-debugging]

## <a name="team-development"></a>팀 개발

Azure 개발자 공간은 팀이 중단 없이 동일한 AKS 클러스터에서 응용 프로그램에서 생산적으로 작업할 수 있도록 도와줍니다.

### <a name="intelligent-routing-between-dev-spaces"></a>개발 공간 간의 지능형 라우팅

Azure 개발자 공간을 사용하면 팀이 클라우드 네이티브 응용 프로그램을 실행하는 단일 AKS 클러스터를 공유하고 다른 개발 공간을 방해하지 않고 개발, 테스트 및 디버깅할 수 있는 격리된 개발 공간을 만들 수 있습니다. 응용 프로그램의 기준 버전은 루트 개발 공간에서 실행됩니다. 그런 다음 팀 멤버는 응용 프로그램의 변경 내용을 개발, 테스트 및 디버깅하기 위한 루트 공간을 기반으로 독립적인 자식 개발 공간을 만듭니다. 개발자 공간의 라우팅 기능을 통해 자식 개발 공간은 자식 개발 공간에서 실행되는 서비스와 부모 개발 공간 간에 요청을 라우팅할 수 있습니다. 이 라우팅을 통해 개발자는 부모 공간에서 종속 서비스를 재사용하는 동안 자신의 버전의 서비스를 실행할 수 있습니다. 각 하위 공간에는 공동 작업을 위해 다른 사용자가 공유하고 액세스할 수 있는 고유한 URL이 있습니다. Azure 개발자 공간에서 라우팅의 작동 방식에 대한 자세한 내용은 [라우팅이 Azure 개발자 공간에서 작동하는 방식을][how-it-works-routing]참조하십시오.

### <a name="live-testing-an-open-pull-request"></a>라이브 테스트 열린 풀 요청

또한 Azure 개발자 공간과 함께 GitHub 작업을 사용하여 병합하기 전에 클러스터에서 직접 끌어오기 요청에서 응용 프로그램의 변경 내용을 테스트할 수 있습니다. Azure 개발자 공간은 응용 프로그램의 검토 버전을 클러스터에 자동으로 배포하여 작성자 및 다른 팀 구성원이 전체 응용 프로그램의 컨텍스트에서 변경 내용을 검토할 수 있도록 합니다. Azure 개발자 공간의 라우팅 기능을 사용하여 이 검토 버전의 응용 프로그램도 다른 개발 공간에 영향을 주지 않고 클러스터에 배포됩니다. 이러한 모든 기능을 사용하면 끌어오기 요청을 자신 있게 승인하고 병합할 수 있습니다. GitHub 작업 및 Azure 개발자 공간의 예를 보려면 [GitHub 작업 & Azure Kubernetes 서비스][pr-flow]입니다.

## <a name="next-steps"></a>다음 단계

로컬 개발 컴퓨터를 AKS 클러스터에 연결하기 시작하려면 [개발 컴퓨터를 AKS 클러스터에 연결합니다.][connect]

팀 개발을 위해 Azure 개발자 공간 사용을 시작하려면 [Azure 개발자 공간 퀵스타트의 팀 개발을][quickstart-team] 참조하세요.

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development
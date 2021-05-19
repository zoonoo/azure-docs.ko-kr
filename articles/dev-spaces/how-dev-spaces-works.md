---
title: Azure Dev Spaces의 작동 원리
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Azure Dev Spaces를 지원하는 프로세스에 대해 설명합니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: 8de2c27ce03c871e60b6437656ad630fc8de8408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91963706"
---
# <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces의 작동 원리

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Kubernetes 애플리케이션 개발은 힘든 작업일 수 있습니다. Docker 및 Kubernetes 구성 파일이 필요합니다. 로컬에서 애플리케이션을 테스트하고 다른 종속 서비스를 조작하는 방법을 파악해야 합니다. 개발자 팀과 함께 한 번에 여러 서비스 개발 및 테스트를 처리해야 할 수도 있습니다.

Azure Dev Spaces를 사용하면 여러 가지 방법으로 Kubernetes 애플리케이션을 신속하게 반복 및 디버그하고 팀과 협업할 수 있습니다. 이 문서에서는 Azure Dev Spaces에서 수행할 수 있는 작업과 작동 방식을 설명합니다.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>신속하게 Kubernetes 애플리케이션 반복 및 디버그

Azure Dev Spaces를 사용하면 AKS 클러스터 컨텍스트에서 Kubernetes 애플리케이션을 간편하게 개발, 테스트, 반복할 수 있습니다. 작업이 간소화됨에 따라 개발자는 Kubernetes에서 실행되도록 서비스를 구성하는 데 시간을 낭비하지 않고 애플리케이션의 비즈니스 논리에 집중할 수 있습니다.

### <a name="bridge-to-kubernetes"></a>Bridge to Kubernetes

Bridge to Kubernetes를 사용하면 개발 컴퓨터를 Kubernetes 클러스터에 연결하여 클러스터에서 실행하는 것처럼 개발 컴퓨터에서 코드를 실행하고 디버그할 수 있습니다. Bridge to Kubernetes는 개발 컴퓨터와 클러스터 간에 트래픽을 리디렉션하는 원격 에이전트 역할을 하는 Pod를 클러스터에서 실행하여 연결된 클러스터 간에 트래픽을 리디렉션합니다. 이 트래픽 리디렉션을 통해 개발 컴퓨터의 코드와 클러스터에서 실행되는 서비스가 동일한 클러스터에 있는 것처럼 통신할 수 있습니다. 개발 컴퓨터를 Kubernetes 클러스터에 연결하는 방법에 대한 자세한 내용은 [Bridge to Kubernetes의 작동 방식][how-it-works-bridge-to-kubernetes]을 참조하세요.

### <a name="run-your-code-in-aks"></a>AKS에서 코드 실행

Azure Dev Spaces를 사용하면 개발 컴퓨터와 AKS 클러스터 간에 트래픽을 리디렉션하는 것 외에도 AKS에서 직접 코드를 구성하고 신속하게 실행할 수 있습니다. Azure Dev Spaces는 Visual Studio, Visual Studio Code 또는 Azure Dev Spaces CLI를 사용하여 코드를 클러스터에 업로드한 다음, 빌드하고 실행합니다. Azure Dev Spaces에서 코드 변경 내용을 지능적으로 동기화하고 서비스를 다시 시작하여 필요에 따라 변경 내용을 반영할 수도 있습니다. 코드를 실행하는 동안 빌드 로그 및 HTTP 추적이 클라이언트로 다시 스트리밍되므로 진행률을 모니터링하고 이슈를 진단할 수 있습니다. 또한 Azure Dev Spaces를 사용하여 Visual Studio 및 Visual Studio Code의 디버거를 Java, Node.js, .NET Core 서비스에 연결할 수 있습니다. 자세한 내용은 [Azure Dev Spaces에 대한 프로젝트 준비 소개][how-it-works-prep], [Azure Dev Spaces를 사용한 코드 실행 소개][how-it-works-up], [Azure Dev Spaces를 사용한 코드 원격 디버깅 소개][how-it-works-remote-debugging]를 참조하세요.

## <a name="team-development"></a>팀 개발

Azure Dev Spaces는 팀이 중단 없이 동일한 AKS 클러스터의 애플리케이션에서 생산적으로 작업하는 데 도움이 됩니다.

### <a name="intelligent-routing-between-dev-spaces"></a>Dev Spaces 간 지능형 라우팅

Azure Dev Spaces를 사용하면 팀이 클라우드 네이티브 애플리케이션을 실행하는 단일 AKS 클러스터를 공유할 수 있을 뿐 아니라 다른 개발 공간을 방해하지 않고 개발, 테스트, 디버그할 수 있는 격리된 개발 공간을 만들 수 있습니다. 애플리케이션의 기준 버전은 루트 개발 공간에서 실행됩니다. 팀원은 루트 공간을 기반으로 하여 애플리케이션 변경 내용을 개발, 테스트, 디버그하기 위한 독립적인 자식 개발 공간을 만듭니다. Dev Spaces의 라우팅 기능을 통해 자식 개발 공간은 자식 개발 공간과 부모 개발 공간에서 실행되는 서비스 간에 요청을 라우팅할 수 있습니다. 이 라우팅을 사용하여 개발자는 부모 공간의 종속 서비스를 재사용하면서 고유한 버전의 서비스를 실행할 수 있습니다. 각 자식 공간에는 다른 사용자가 협업을 위해 공유하고 액세스할 수 있는 고유한 URL이 있습니다. Azure Dev Spaces에서 라우팅의 작동 방식에 대한 자세한 내용은 [Azure Dev Spaces를 사용한 라우팅의 작동 방식][how-it-works-routing]을 참조하세요.

### <a name="live-testing-an-open-pull-request"></a>열린 끌어오기 요청 라이브 테스트

Azure Dev Spaces와 함께 GitHub 작업을 사용하여 병합 전에 클러스터에서 직접 끌어오기 요청으로 애플리케이션 변경 내용을 테스트할 수도 있습니다. Azure Dev Spaces는 애플리케이션의 검토 버전을 클러스터에 자동으로 배포할 수 있으므로 작성자와 다른 팀원이 전체 애플리케이션 컨텍스트에서 변경 내용을 검토할 수 있습니다. Azure Dev Spaces의 라우팅 기능을 사용하면 이 검토 버전의 애플리케이션이 다른 개발 공간에 영향을 주지 않고 클러스터에도 배포됩니다. 이 모든 기능을 통해 끌어오기 요청을 안전하게 승인하고 병합할 수 있습니다. GitHub 작업 및 Azure Dev Spaces의 예제를 보려면 [Github 작업 및 Azure Kubernetes Service][pr-flow]를 참조하세요.

## <a name="next-steps"></a>다음 단계

AKS 클러스터에 로컬 개발 컴퓨터 연결을 시작하려면 [AKS 클러스터에 개발 컴퓨터 연결][connect]을 참조하세요.


[connect]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[routing]: #team-development
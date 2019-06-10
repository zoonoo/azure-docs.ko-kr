---
title: 포함 파일
description: 포함 파일
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 40c1be20df845b975c023616e38cbb932c985735
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157882"
---
# <a name="team-development-with-azure-dev-spaces"></a>Azure Dev Spaces로 팀 개발

이 자습서에서는 개발자 팀이 Dev Spaces를 사용하여 동일한 Kubernetes 클러스터에서 동시에 공동 작업을 수행할 수 있는 방법을 알아봅니다.

## <a name="learn-about-team-development"></a>팀 개발 알아보기
지금까지 애플리케이션에서 작업하는 유일한 개발자인 것처럼 애플리케이션 코드를 실행했습니다. 이 섹션에서는 Azure Dev Spaces에서 팀 개발을 간소화하는 방법에 대해 알아봅니다.
* 필요에 따라 공유 개발 공간 또는 고유한 개발 공간에서 작업하여 개발자 팀이 동일한 환경에서 작업하도록 설정합니다.
* 각 개발자가 다른 개발자를 방해하지 않고 격리된 상태에서 자신의 코드를 반복할 수 있도록 지원합니다.
* 모의 개체를 만들거나 종속성을 시뮬레이션할 필요 없이 코드를 커밋하기 전에 엔드투엔드 코드를 테스트합니다.

### <a name="challenges-with-developing-microservices"></a>마이크로 서비스 개발 문제
현재 샘플 애플리케이션은 그다지 복잡하지 않습니다. 하지만 실제 개발 환경에서는 더 많은 서비스를 추가하고 개발 팀이 성장함에 따라 문제가 곧 발생합니다. 개발을 위해 모든 작업을 로컬로 실행하는 것은 현실적이지 않습니다.

* 필요한 모든 서비스를 한 번에 실행하기에 개발 머신의 리소스가 충분치 않을 수도 있습니다.
* 일부 서비스는 공개적으로 연결할 수 있어야 합니다. 예를 들어 서비스에는 웹후크에 대응하는 엔드포인트가 필요할 수도 있습니다.
* 서비스의 서브넷을 실행하려는 경우 모든 서비스 간 전체 종속성 계층 구조를 알아야 합니다. 이를 결정하기란 어려울 수 있는데, 특히 서비스 수가 늘어날수록 어렵습니다.
* 일부 개발자는 서비스 종속성 대부분을 시뮬레이션하거나 모델링하는 데 의존합니다. 이 방법은 유용할 수 있지만 이러한 모의 개체 관리는 곧 개발 비용에 영향을 줄 수 있습니다. 또한 이 접근법은 개발 환경을 프로덕션과는 매우 다르게 보이게 하는데, 이로 인해 감지하기 힘든 버그들이 발생할 수 있습니다.
* 따라서 모든 종류의 통합 테스트를 수행하는 것이 어려워집니다. 통합 테스트는 현실적으로 커밋 후에만 수행할 수 있습니다. 이는 나중에 개발 주기에서 문제가 발생한다는 것을 의미합니다.

    ![](../articles/dev-spaces/media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>공유 개발 공간에서 작업
Azure Dev Spaces를 사용하면 Azure에서 *공유* 개발 공간을 설정할 수 있습니다. 각 개발자는 애플리케이션의 일부에만 집중할 수 있으며, 시나리오에 종속된 다른 모든 서비스와 클라우드 리소스가 이미 포함되어 있는 개발 공간에서 *사전 커밋 코드*를 반복적으로 개발할 수 있습니다. 종속성은 항상 최신이며, 개발자는 프로덕션을 미러하는 방식으로 작업합니다.

### <a name="work-in-your-own-space"></a>개발자 고유의 공간에서 작업
서비스에 대한 코드를 개발하고 이 코드를 체크 인할 준비가 되기 전에 코드 상태가 좋지 않은 경우가 많습니다. 아직도 코드를 반복적으로 만들고, 테스트하고, 솔루션에서 실험합니다. Azure Dev Spaces는 **공간**이라는 개념을 제공합니다. 이 공간을 사용하면 팀 구성원을 방해할 염려 없이 격리된 상태에서 작업할 수 있습니다.

## <a name="use-dev-spaces-for-team-development"></a>팀 개발에 Dev Spaces 사용
*webfrontend* -> *mywebapi* 샘플 애플리케이션을 사용하여 이러한 아이디어를 구체적인 예제로 설명해보겠습니다. 개발자 Scott이 *mywebapi* 서비스, *오직* 해당 서비스만 변경해야 하는 시나리오를 가정해보겠습니다. *webfrontend*는 Scott 업데이트의 일부로 변경할 필요가 없습니다.

Scott은 Dev Spaces를 사용하지 _않고도_ 몇 가지 방법으로 자신의 업데이트를 개발하고 테스트할 수는 있습니다. 그 중 어떤 것도 이상적인 방법은 아닙니다.
* 모든 구성 요소를 로컬로 실행합니다. 이를 위해서는 Docker가 설치된 더 강력한 개발 머신과 잠재적으로 MiniKube가 필요합니다.
* Kubernetes 클러스터에서 격리된 네임스페이스의 모든 구성 요소를 실행합니다. *webfrontend*는 변경되지 않으므로 이는 클러스터 리소스를 낭비하는 방법입니다.
* *mywebapi*만 실행하고 테스트를 위해 수동 REST 호출을 만듭니다. 이는 전체 엔드투엔드 흐름을 테스트하지 않습니다.
* 개발자가 다른 *mywebapi*의 인스턴스에 요청을 보낼 수 있는 *webfrontend*에 개발중점 코드를 추가합니다. 이는 *webfrontend* 서비스를 복잡하게 합니다.

### <a name="set-up-your-baseline"></a>기준 설정
먼저 서비스의 기준을 배포해야 합니다. 이 배포는 로컬 코드의 동작과 확인된 버전을 손쉽게 비교할 수 있도록 “마지막으로 성공한 항목”을 나타냅니다. 그런 다음, 더 큰 애플리케이션의 컨텍스트 내에서 *mywebapi*에 대한 변경 내용을 테스트할 수 있도록 이 기준에 따라 자식 공간을 만듭니다.

1. [Dev Spaces 샘플 애플리케이션](https://github.com/Azure/dev-spaces)을 복제합니다. `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. 원격 분기 *azds_updates*를 확인합니다. `git checkout -b azds_updates origin/azds_updates`
1. _dev_ 공간을 선택합니다. `azds space select --name dev`. 부모 개발 공간을 선택하라는 메시지가 표시되면 _\<none\>_ 을 선택합니다.
1. _mywebapi_ 디렉터리로 이동하고 다음을 실행합니다. `azds up -d`
1. _webfrontend_ 디렉터리로 이동하고 다음을 실행합니다. `azds up -d`
1. `azds list-uris`를 실행하여 _webfrontend_에 대한 공개 엔드포인트를 확인합니다.

> [!TIP]
> 위의 단계는 기준을 수동으로 설정하지만, 팀에서 CI/CD를 사용하여 자동으로 최신 커밋된 코드로 기준을 유지하는 것이 좋습니다.
>
> 다음 다이어그램과 유사한 워크플로를 만들 수 있는 [Azure DevOps를 사용하여 CI/CD를 설정하기 위한 가이드](../articles/dev-spaces/how-to/setup-cicd.md)를 확인해보세요.
>
> ![CI/CD 다이어그램의 예제](../articles/dev-spaces/media/common/ci-cd-complex.png)

이 시점에서 기준이 실행되어야 합니다. `azds list-up --all` 명령을 실행하면 다음과 유사한 결과가 표시됩니다.

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

DevSpace 열은 두 서비스 모두 _dev_라는 공간에서 실행되고 있음을 보여줍니다. 공용 URL을 열고 웹앱으로 이동하는 모든 사용자는 확인된 코드 경로를 호출하며, 이 코드는 두 서비스 모두를 통해 실행됩니다. 이제 _mywebapi_ 개발을 계속하려는 경우를 가정해보겠습니다. 개발 환경을 사용하는 다른 개발자에게 영향을 주지 않으면서 코드를 변경하고 이를 테스트하려면 어떻게 해야 할까요? 이렇게 하려면 자신만의 고유한 공간을 설정합니다.

### <a name="create-a-dev-space"></a>개발 환경 만들기
_dev_ 이외의 공간에서 고유한 버전의 _mywebapi_를 실행하려면 다음 명령을 사용하여 고유한 공간을 만들면 됩니다.

```cmd
azds space select --name scott
```

메시지가 나타나면 **부모 개발 공간**으로 _dev_를 선택합니다. 즉, 새 공간 _dev/scott_은 _dev_ 공간에서 파생됩니다. 이것이 어떻게 도움이 되는지 테스트로 간략히 표시됩니다.

이 입문적인 가설에서는 동료들이 작동하는 사람을 식별할 수 있도록 _scott_이란 이름을 새 공간에 사용하였습니다. 하지만 마음에 드는 다른 이름으로 불러도 좋으며, _sprint4_나 _demo_처럼 그 의미에 대해서도 자유롭게 사용할 수 있습니다. 어떠한 사례든 _dev_는 이 애플리케이션 부분에서 작업하는 모든 개발자를 위한 기준으로 사용됩니다.

![](../articles/dev-spaces/media/common/ci-cd-space-setup.png)

`azds space list` 명령을 실행하면 개발 환경의 모든 공간 목록이 표시됩니다. _선택된_ 열은 현재 선택한 공간을 나타냅니다(true/false). 여러분의 경우 _dev/scott_이라는 공간이 만들어질 때 자동으로 선택되었습니다. 언제든지 `azds space select` 명령을 사용하여 다른 공간을 선택할 수 있습니다.

작동을 확인합니다.
---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76694515"
---
## <a name="provide-feedback-to-the-user"></a>사용자에게 피드백 제공

세션 업데이트 이벤트를 처리하는 코드를 작성할 수 있습니다. 이 이벤트는 세션에서 사용자 환경에 대한 이해가 향상될 때마다 발생합니다. 이렇게 하면 다음과 같은 작업을 수행할 수 있습니다.

- 디바이스가 이동하고 세션에서 해당 환경의 이해를 업데이트할 때 `UserFeedback` 클래스를 사용하여 사용자에게 피드백을 제공합니다. 이 작업을 수행하려면
- 공간 앵커를 만들기에 충분한 추적 공간 데이터가 있는 지점을 확인합니다. `ReadyForCreateProgress` 또는 `RecommendedForCreateProgress`를 사용하여 이를 확인합니다. `ReadyForCreateProgress`가 1을 초과하면 클라우드 공간 앵커를 저장하기에 충분한 데이터가 있지만 이 작업을 수행하려면 `RecommendedForCreateProgress`가 1을 초과할 때까지 대기하는 것이 좋습니다.

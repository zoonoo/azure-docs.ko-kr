---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006409"
---
## <a name="setting-up-the-library"></a>라이브러리 설정

세션에서 환경 데이터를 처리할 수 있도록 하려면 `Start()`를 호출합니다.

세션에서 발생하는 이벤트를 처리하려면 세션의 `delegate` 속성을 보기와 같은 개체로 설정합니다. 이 개체는 `SSCCloudSpatialAnchorSessionDelegate` 프로토콜을 구현해야 합니다.

---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67181904"
---
## <a name="setting-up-the-library"></a>라이브러리 설정

세션에서 환경 데이터를 처리할 수 있도록 하려면 Start()를 호출합니다.

세션에서 발생하는 이벤트를 처리하려면 세션의 `delegate` 속성을 보기와 같은 개체로 설정합니다. 이 개체는 SSCCloudSpatialAnchorSessionDelegate 프로토콜을 구현해야 합니다.

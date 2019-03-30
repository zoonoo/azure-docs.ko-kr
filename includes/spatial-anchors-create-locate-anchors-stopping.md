---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632439"
---
## <a name="pause-reset-or-stop-the-session"></a>일시 중지, 다시 설정 하거나 세션 중지

세션을 중지 하려면 일시적으로 호출할 수 있습니다 `Stop()`합니다. 이렇게는 모든 감시자 및 중지 환경 처리 ProcessFrame()를 호출 하는 경우에 합니다. 호출할 수 있습니다 `Start()` 처리를 다시 시작 합니다. 다시 시작 하는 경우 세션에서 이미 캡처한 환경 데이터 유지 됩니다.

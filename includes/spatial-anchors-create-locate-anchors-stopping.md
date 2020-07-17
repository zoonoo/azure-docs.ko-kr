---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006419"
---
## <a name="pause-reset-or-stop-the-session"></a>세션 일시 중지, 재설정 또는 중지

세션을 일시적으로 중지하려면 `Stop()`을 호출하면 됩니다. 이렇게 하면 `ProcessFrame()`을 호출하는 경우에도 감시자 및 환경 처리가 중지됩니다. 그런 다음, `Start()`를 호출하여 처리를 다시 시작할 수 있습니다. 다시 시작하는 경우 세션에서 이미 캡처된 환경 데이터가 유지 관리됩니다.

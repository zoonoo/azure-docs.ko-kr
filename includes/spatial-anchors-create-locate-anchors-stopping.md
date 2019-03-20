---
ms.openlocfilehash: 8e02067b32d9404a5bbdf7362b2cc32712b96250
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907702"
---
## <a name="pause-reset-or-stop-the-session"></a>일시 중지, 다시 설정 하거나 세션 중지

일시적으로 세션을 중지 하기 stop ()를 호출할 수 있습니다. 이렇게는 모든 감시자 및 중지 환경 처리 ProcessFrame()를 호출 하는 경우에 합니다. 그런 다음 처리를 다시 시작 하려면 start ()를 호출할 수 있습니다. 다시 시작 하는 경우 세션에서 이미 캡처한 환경 데이터 유지 됩니다.

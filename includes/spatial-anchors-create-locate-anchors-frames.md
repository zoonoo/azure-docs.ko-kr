---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006449"
---
## <a name="provide-frames-to-the-session"></a>세션에 프레임 제공

공간 앵커 세션은 사용자 주위의 공간을 매핑하여 작동합니다. 이렇게 하면 앵커가 배치된 위치를 확인하는 데 도움이 됩니다. 모바일 플랫폼(iOS & Android)은 플랫폼의 AR 라이브러리에서 프레임을 가져오기 위한 카메라 피드에 대한 네이티브 호출이 필요합니다. 반면, HoloLens는 지속적으로 환경을 검색하므로 모바일 플랫폼에서와 같은 특정 호출이 필요하지 않습니다.
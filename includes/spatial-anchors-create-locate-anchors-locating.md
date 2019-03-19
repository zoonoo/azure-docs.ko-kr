---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907739"
---
## <a name="locate-a-cloud-spatial-anchor"></a>클라우드 공간 앵커를 찾습니다.

클라우드 공간 앵커를 찾으려면 해당 식별자를 알아야 해야 합니다. 앵커 Id는 응용 프로그램의 백 엔드 서비스에서 저장 및를 제대로 인증할 수 있는 모든 장치에 액세스할 수 있습니다. 이 참조의 예 [자습서: 장치 공간 앵커 공유할](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)합니다.

AnchorLocateCriteria 개체를 인스턴스화할 원하는 및 세션에서 CreateWatcher 메서드를 호출 하 여 AnchorLocateCriteria를 제공 하 여 식별자를 설정 합니다.

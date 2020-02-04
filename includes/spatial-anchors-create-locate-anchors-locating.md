---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76887792"
---
## <a name="locate-a-cloud-spatial-anchor"></a>클라우드 공간 앵커 찾기

이전에 저장된 클라우드 공간 앵커를 찾는 것은 Azure 공간 앵커를 사용하는 주요 이유 중 하나입니다. 여러 가지 방법으로 클라우드 공간 앵커를 찾을 수 있습니다. 감시자에서 한 번에 한 가지 전략을 사용할 수 있습니다.
- 식별자로 앵커를 찾습니다.
- 이전에 찾은 앵커에 연결된 앵커를 찾습니다. [여기에서](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/) 앵커 관계에 대해 알아볼 수 있습니다.
- [광역 위치 재결정](/azure/spatial-anchors/concepts/coarse-reloc/)을 사용하여 앵커를 찾습니다.

식별자로 클라우드 공간 앵커를 찾는 경우 클라우드 공간 앵커 식별자를 애플리케이션의 백 엔드 서비스에 저장하고 적절하게 인증할 수 있는 모든 디바이스에서 액세스할 수 있도록 합니다. 이에 대한 예제는 [자습서: 여러 디바이스 간에 Spatial Anchors 공유](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)를 참조하세요.

`AnchorLocateCriteria` 개체를 인스턴스화하고, 찾고자 하는 식별자를 설정하고, `AnchorLocateCriteria`를 제공하여 세션에서 `CreateWatcher` 메서드를 호출합니다.
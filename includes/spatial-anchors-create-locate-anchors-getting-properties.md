---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67181914"
---
서비스에서 앵커를 만든 후에는 앵커 위치를 업데이트할 수 없습니다. 새 앵커를 만들고 이전 앵커를 삭제하여 새 위치를 추적해야 합니다.

해당 속성을 업데이트하기 위해 앵커를 찾을 필요가 없는 경우 `GetAnchorPropertiesAsync()` 메서드를 사용할 수 있습니다. 이 메서드는 속성을 사용하여 `CloudSpatialAnchor` 개체를 반환합니다.

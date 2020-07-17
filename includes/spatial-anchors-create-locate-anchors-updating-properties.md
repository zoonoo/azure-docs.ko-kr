---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67181916"
---
## <a name="update-properties"></a>속성 업데이트

앵커에 대한 속성을 업데이트하려면 `UpdateAnchorProperties()` 메서드를 사용합니다. 두 개 이상의 디바이스가 동일한 앵커에 대한 속성을 동시에 업데이트하려고 하면 낙관적 동시성 모델을 사용합니다. 즉, 첫 번째 쓰기가 성공하게 됩니다.  다른 모든 쓰기에는 "동시성" 오류가 발생합니다. 다시 시도하기 전에 속성의 새로 고침이 필요합니다.

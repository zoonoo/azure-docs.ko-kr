---
ms.openlocfilehash: eafe71902ee849c310847f4537214722e129ad42
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907678"
---
## <a name="update-properties"></a>속성 업데이트

앵커에 속성을 업데이트 하려면 UpdateAnchorProperties 메서드를 사용 합니다. 두 개 이상의 장치를 동시에 동일한 앵커에 대 한 속성을 업데이트 하려고 하는 경우 낙관적 동시성 모델을 사용 합니다. 즉, 첫 번째 쓰기 승리 합니다.  다른 모든 쓰기 "동시성" 오류가 발생 합니다: 속성의 새로 고침을 다시 시도 하기 전에 필요 합니다.

---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180062"
---
감시자를 만든 후에는 요청된 모든 앵커에 대해 `AnchorLocated` 이벤트가 발생합니다. 이 이벤트는 앵커가 있는 경우 또는 앵커를 찾을 수 없는 경우에 발생합니다. 이러한 상황이 발생하는 경우 이유가 상태에 명시됩니다. 감시자의 모든 앵커를 처리하거나, 찾거나, 찾지 못하게 되면 `LocateAnchorsCompleted` 이벤트가 발생합니다. 감시자당 식별자는 35개로 제한됩니다. 

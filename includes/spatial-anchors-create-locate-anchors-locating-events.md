---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993057"
---
감시자를 만든 후에는 요청된 모든 앵커에 대해 `AnchorLocated` 이벤트가 발생합니다. 이 이벤트는 앵커가 있는 경우 또는 앵커를 찾을 수 없는 경우에 발생합니다. 이러한 상황이 발생하는 경우 이유가 상태에 명시됩니다. 감시자의 모든 앵커를 처리하거나, 찾거나, 찾지 못하게 되면 `LocateAnchorsCompleted` 이벤트가 발생합니다. 감시자당 식별자는 35개로 제한됩니다. 

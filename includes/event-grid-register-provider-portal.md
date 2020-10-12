---
title: 포함 파일
description: 포함 파일
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 04/16/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8833bb75f5f50372deda3e71c1df90bed9a20054
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81605817"
---
## <a name="enable-event-grid-resource-provider"></a>Event Grid 리소스 공급자를 사용하도록 설정

이전에 Azure 구독에서 Event Grid를 사용하지 않은 경우 Event Grid 리소스 공급자를 등록해야 할 수 있습니다.

Azure Portal에서 다음을 수행합니다.

1. 왼쪽 메뉴에서 **구독**을 선택합니다.
1. Event Grid에 대해 사용 중인 구독을 선택합니다.
1. 왼쪽 메뉴의 **설정**에서 **리소스 공급자**를 선택합니다.
1. **Microsoft.EventGrid**를 찾습니다.
1. 등록되지 않은 경우 **등록**을 선택합니다. 

등록을 완료하는 데 잠시 시간이 걸릴 수 있습니다. **새로 고침**을 선택하여 상태를 업데이트합니다. **상태**가 **등록됨**이면 진행할 준비가 된 것입니다.
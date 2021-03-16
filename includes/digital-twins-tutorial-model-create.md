---
author: baanders
description: Azure Digital Twins용 파일 포함 - 명령행 자습서용 모델 지침
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: a94b9304ecd6c6630f6ad45652e76d2879bbc1b8
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463788"
---
1. **버전 번호를 업데이트** 하여 이 모델의 최신 버전을 제공하고 있음을 나타냅니다. 이렇게 하려면 `@id` 값의 끝에 있는 *1* 을 *2* 로 변경합니다. 현재 버전 번호보다 큰 번호도 사용할 수 있습니다.
1. **속성을 편집** 합니다. `Humidity` 속성의 이름을 *HumidityLevel* 로 변경합니다. 원하는 경우 다른 이름을 사용할 수 있습니다. *HumidityLevel* 이 아닌 다른 이름을 사용하는 경우 해당 이름을 기억하여 자습서 전체에서 *HumidityLevel* 대신 사용합니다.
1. **속성을 추가** 합니다. 줄 15에서 끝나는 `HumidityLevel` 속성 아래에 다음 코드를 붙여 넣어 `RoomName` 속성을 room에 추가합니다.

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **관계를 추가** 합니다. 방금 추가한 `RoomName` 속성 아래에 다음 코드를 붙여 넣어 이러한 유형의 트윈이 다른 트윈과 *포함* 관계를 형성하는 기능을 추가합니다.

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

완료 시 업데이트된 모델은 이와 일치해야 합니다.

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

계속 진행하기 전에 파일을 저장합니다.
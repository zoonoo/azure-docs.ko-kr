---
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: include
ms.date: 05/03/2021
ms.openlocfilehash: 7f5f062e35aad6b7623f1a2f97ab3b9133266ac6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108748285"
---
작업이 App Configuration 저장소에 액세스할 수 있도록 작업 내에서 사용 중인 자격 증명에 적절한 App Configuration 역할 할당을 할당합니다.

1. 대상 App Configuration 저장소로 이동합니다. 
1. 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.
1. 오른쪽 창에서 **역할 할당 추가** 를 선택합니다.
    
    :::image type="content" source="./media/azure-app-configuration-role-assignment/add-role-assignment-button.png" alt-text="역할 할당 추가 단추를 보여 주는 스크린샷":::

1. **역할** 에 대해 **App Configuration 데이터 소유자** 를 선택합니다. 이 역할을 통해 App Configuration 저장소에서 읽고 쓰는 작업을 수행할 수 있습니다. 
1. 이전 섹션에서 만든 서비스 연결과 연관된 서비스 주체를 선택합니다.

    :::image type="content" source="./media/azure-app-configuration-role-assignment/add-role-assignment.png" alt-text="역할 할당 추가 대화 상자를 보여 주는 스크린샷":::

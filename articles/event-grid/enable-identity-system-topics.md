---
title: Azure Event Grid 시스템에서 관리 되는 id 사용 항목
description: 이 문서에서는 Azure Event Grid 시스템 항목에 대해 관리 서비스 id를 사용 하도록 설정 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 66b418787e5570dc5da06a5332dd834ccbfd4aef
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630521"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Event Grid 시스템 항목에 시스템 관리 id 할당
이 문서에서는 기존 Event Grid 시스템 항목에 대해 시스템 관리 id를 사용 하도록 설정 하는 방법에 대해 알아봅니다. 관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조 하세요.  

> [!IMPORTANT]
> 현재 시스템 항목을 지 원하는 Azure 리소스에서 이벤트 구독을 만들 때 새 시스템 항목을 만들 때 시스템 관리 id를 사용 하도록 설정할 수 없습니다. 


## <a name="use-azure-portal"></a>Azure Portal 사용
다음 절차에서는 시스템 항목에 대해 시스템 관리 id를 사용 하도록 설정 하는 방법을 보여 줍니다. 

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 위쪽의 검색 창에서 **event grid 시스템 항목** 을 검색 합니다.
3. 관리 id를 사용 하도록 설정 하려는 **시스템 항목** 을 선택 합니다. 
4. 왼쪽 메뉴에서 **id** 를 선택 합니다. 전역 위치에 있는 시스템 항목에 대 한이 옵션은 표시 되지 않습니다. 
5. 스위치 **를 설정 하** 여 id를 사용 하도록 설정 합니다. 
1. 도구 모음에서 **저장** 을 선택 하 여 설정을 저장 합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="시스템 항목에 대 한 id 페이지"::: 
1. 확인 메시지에서 **예** 를 선택합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="시스템 항목에 id 할당-확인"::: 
1. 시스템 할당 관리 id의 개체 ID가 표시 되는지 확인 하 고 역할 할당 링크를 확인 합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="시스템 항목에 id 할당-완료 됨"::: 

## <a name="global-azure-sources"></a>글로벌 Azure 원본
지역 Azure 리소스에 대해서만 시스템 관리 id를 사용 하도록 설정할 수 있습니다. Azure 구독, 리소스 그룹 또는 Azure Maps와 같은 글로벌 Azure 리소스와 관련 된 시스템 토픽에는이 기능을 사용 하도록 설정할 수 없습니다. 이러한 글로벌 소스에 대 한 시스템 항목도 특정 지역과 연결 되어 있지 않습니다. 위치가 **Global** 로 설정 된 시스템 항목에 대 한 **id** 페이지가 표시 되지 않습니다. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="위치가 Global로 설정 된 시스템 항목"::: 



## <a name="next-steps"></a>다음 단계
대상의 적절 한 역할 Service Bus (예: Service Bus 큐)에 id를 추가 합니다. 자세한 단계는 [대상의 Azure 역할에 Id 추가](add-identity-roles.md)를 참조 하세요. 
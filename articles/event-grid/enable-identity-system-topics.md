---
title: Azure Event Grid 시스템 항목에서 관리 ID 사용
description: 이 문서에서는 Azure Event Grid 시스템 항목에 대해 관리형 서비스 ID를 사용하도록 설정하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: d8219cf9cba4ce0a4fb5ddbcf695f1ec72de36fe
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280516"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Event Grid 시스템 항목에 시스템 관리 ID 할당
이 문서에서는 기존 Event Grid 시스템 항목에 대해 시스템 관리 ID를 사용하도록 설정하는 방법을 알아봅니다. 관리 ID에 대해 알아보려면 [Azure 리소스용 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.  

> [!IMPORTANT]
> 현재 새 시스템 항목을 만들 때, 즉 시스템 항목을 지원하는 Azure 리소스에서 이벤트 구독을 만들 때 시스템 관리 ID를 사용하도록 설정할 수 없습니다. 


## <a name="use-azure-portal"></a>Azure Portal 사용
다음 절차에서는 시스템 항목에 대해 시스템 관리 ID를 사용하도록 설정하는 방법을 보여줍니다. 

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 위쪽의 검색 창에서 **이벤트 그리드 시스템 항목** 을 검색합니다.
3. 관리 ID를 사용하도록 설정할 **시스템 항목** 을 선택합니다. 
4. 왼쪽 메뉴에서 **ID** 를 선택합니다. 글로벌 위치에 있는 시스템 항목에는 이 옵션이 표시되지 않습니다. 
5. 스위치를 **켜면** ID가 활성화됩니다. 
1. 도구 모음에서 **저장** 을 선택하여 설정을 저장합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="시스템 항목에 대한 ID 페이지"::: 
1. 확인 메시지에서 **예** 를 선택합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="시스템 항목에 ID 할당 - 확인"::: 
1. 시스템 할당 관리 ID의 개체 ID와 역할을 할당하는 링크가 표시되는지 확인합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="시스템 항목에 ID 할당 - 완료"::: 

## <a name="global-azure-sources"></a>글로벌 Azure 원본
지역 Azure 리소스에 대해서만 시스템 관리 ID를 사용하도록 설정할 수 있습니다. Azure 구독, 리소스 그룹 또는 Azure Maps와 같은 글로벌 Azure 리소스와 관련된 시스템 항목에 대해서는 이 기능을 사용하도록 설정할 수 없습니다. 이러한 글로벌 소스에 대한 시스템 항목도 특정 지역과 관련이 없습니다. 위치가 **Global** 로 설정된 시스템 항목에 대한 **ID** 페이지가 표시되지 않습니다. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="위치가 Global로 설정된 시스템 항목"::: 



## <a name="next-steps"></a>다음 단계
대상(예: Service Bus 큐)의 적절한 역할(예: Service Bus 데이터 보낸 사람)에 ID를 추가합니다. 자세한 단계는 [관리 ID에 Event Grid 대상에 대한 액세스 권한 부여](add-identity-roles.md)를 참조하세요. 
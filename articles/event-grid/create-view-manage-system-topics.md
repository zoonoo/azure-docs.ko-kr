---
title: Azure Event Grid에서 시스템 항목 만들기, 보기 및 관리 (포털)
description: 이 문서에서는 Azure Portal를 사용 하 여 기존 시스템 항목을 보고 Azure Event Grid 시스템 항목을 만드는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2dbb0893668d72cfebf2d64e6515ff6ab6fed9bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86115114"
---
# <a name="create-view-and-manage-event-grid-system-topics-in-the-azure-portal"></a>Azure Portal에서 Event Grid 시스템 항목을 만들고, 확인 하 고, 관리 합니다.
이 문서에서는 Azure Portal 사용 하 여 시스템 항목을 만들고 관리 하는 방법을 보여 줍니다. 시스템 항목의 개요는 [시스템 항목](system-topics.md)을 참조 하십시오.

## <a name="create-a-system-topic"></a>시스템 항목 만들기
다음 두 가지 방법으로 Azure 리소스 (저장소 계정, Event Hubs 네임 스페이스 등)에 대 한 시스템 항목을 만들 수 있습니다.

- 리소스의 **이벤트** 페이지 (예: Storage 계정 또는 Event Hubs 네임 스페이스)를 사용 합니다. Azure Portal에서 **이벤트** 페이지를 사용 하 여 azure 원본에서 발생 하는 이벤트에 대 한 이벤트 구독을 만드는 경우 (예: Azure Storage 계정), 포털은 azure 리소스에 대 한 시스템 항목을 만든 다음 시스템 항목에 대 한 구독을 만듭니다. 처음으로 Azure 리소스에 대 한 이벤트 구독을 만드는 경우 시스템 항목의 이름을 지정 합니다. 처음부터 시스템 항목 이름이 읽기 전용 모드에서 표시 됩니다. 자세한 단계 [Azure Portal는 빠른 시작: Blob storage 이벤트를 웹 끝점에 라우팅을](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage) 참조 하세요.
- **Event Grid 시스템 항목** 페이지를 사용 합니다. 다음 단계는 **Event Grid 시스템 항목** 페이지를 사용 하 여 시스템 항목을 만들기 위한 것입니다. 

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 위쪽의 검색 상자에 **Event Grid 시스템 항목**을 입력 한 다음 **enter**키를 누릅니다. 

    ![시스템 항목 검색](./media/create-view-manage-system-topics/search-system-topics.png)
3. **Event Grid 시스템 항목** 페이지의 도구 모음에서 **+ 추가** 를 선택 합니다.

    ![시스템 추가 항목-도구 모음 단추](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. **Event Grid 시스템 항목 만들기** 페이지에서 다음 단계를 수행 합니다.
    1. **항목 유형을**선택 합니다. 다음 예제에서는 **저장소 계정** 옵션을 선택 합니다. 
    2. 저장소 계정 리소스를 포함 하는 **Azure 구독** 을 선택 합니다. 
    3. 저장소 계정이 있는 **리소스 그룹** 을 선택 합니다. 
    4. **저장소 계정**을 선택합니다. 
    5. 만들 시스템 항목의 **이름을** 입력 합니다. 
    
        > [!NOTE]
        > 이 시스템 항목 이름을 사용 하 여 메트릭 및 진단 로그를 검색할 수 있습니다.
    6. **검토 + 만들기**를 선택합니다.

        ![시스템 항목 만들기](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. 설정을 검토 하 고 **만들기**를 선택 합니다. 
        
        ![시스템 항목 검토 및 만들기](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. 배포에 성공한 후 **리소스로 이동** 을 선택 하 여 만든 시스템 항목에 대 한 **Event Grid 시스템 항목** 페이지를 표시 합니다. 

        ![시스템 항목 페이지](./media/create-view-manage-system-topics/system-topic-page.png)


## <a name="view-all-system-topics"></a>모든 시스템 항목 보기
기존 Event Grid 시스템 항목을 모두 보려면 다음 단계를 따르세요. 

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 위쪽의 검색 상자에 **Event Grid 시스템 항목**을 입력 한 다음 **enter**키를 누릅니다. 

    ![시스템 항목 검색](./media/create-view-manage-system-topics/search-system-topics.png)
3. **Event Grid 시스템 항목** 페이지에 모든 시스템 항목이 표시 됩니다. 

    ![시스템 항목 목록](./media/create-view-manage-system-topics/list-system-topics.png)
4. 목록에서 **시스템 항목** 을 선택 하 여 세부 정보를 확인 합니다. 

    ![시스템 항목 세부 정보](./media/create-view-manage-system-topics/system-topic-details.png)

    이 페이지에는 다음과 같은 시스템 항목에 대 한 세부 정보가 표시 됩니다. 
    - 원본. 시스템 항목을 만든 리소스의 이름입니다.
    - 원본 유형입니다. 리소스 유형입니다. 예를 들어 `Microsoft.Storage.StorageAccounts` , 등이 `Microsoft.EventHub.Namespaces` `Microsoft.Resources.ResourceGroups` 있습니다.
    - 시스템 토픽에 대해 만들어진 모든 구독

    이 페이지에서는 다음과 같은 작업을 수행할 수 있습니다.
    - 이벤트 구독 만들기 도구 모음에서 **+ 이벤트 구독** 을 선택 합니다. 
    - 이벤트 구독을 삭제 합니다. 도구 모음에서 **삭제** 를 선택 합니다. 
    - 시스템 항목에 대 한 태그를 추가 합니다. 왼쪽 메뉴에서 **태그** 를 선택 하 고 태그 이름 및 값을 지정 합니다. 


## <a name="delete-a-system-topic"></a>시스템 항목 삭제
1. [시스템 항목 보기](#view-all-system-topics) 섹션의 지침에 따라 모든 시스템 항목을 보고 목록에서 삭제 하려는 시스템 항목을 선택 합니다. 
2. **Event Grid 시스템 항목** 페이지의 도구 모음에서 **삭제** 를 선택 합니다. 

    ![시스템 항목-삭제 단추](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. 확인 페이지에서 **확인** 을 선택 하 여 삭제를 확인 합니다. 시스템 항목 및 시스템 항목에 대 한 모든 이벤트 구독을 삭제 합니다.  

## <a name="create-an-event-subscription"></a>이벤트 구독 만들기
1. [시스템 항목 보기](#view-all-system-topics) 섹션의 지침에 따라 모든 시스템 항목을 보고 목록에서 삭제 하려는 시스템 항목을 선택 합니다. 
2. **Event Grid 시스템 항목** 페이지의 도구 모음에서 **+ 이벤트 구독** 을 선택 합니다. 

    ![시스템 항목-이벤트 구독 추가 단추](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. **토픽 유형**, **원본 리소스**및 **토픽 이름이** 자동으로 채워지는지 확인 합니다. 이름을 입력 하 고, **끝점 유형을**선택 하 고, **끝점**을 지정 합니다. 그런 다음 **만들기** 를 선택 하 여 이벤트 구독을 만듭니다. 

    ![시스템 항목-이벤트 구독 만들기](./media/create-view-manage-system-topics/create-event-subscription.png)

## <a name="next-steps"></a>다음 단계
Azure Event Grid에서 지 원하는 시스템 항목과 항목 유형에 대 한 자세한 내용은 [Azure Event Grid의 시스템 항목](system-topics.md) 을 참조 하십시오. 

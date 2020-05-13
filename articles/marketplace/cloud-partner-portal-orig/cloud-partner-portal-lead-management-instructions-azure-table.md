---
title: Azure 테이블 저장소 | Azure Marketplace
description: Azure 테이블 저장소에서 리드 관리를 구성 합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: e65f2041cdb2e25341bfd63783c70ec09c1216b5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124706"
---
# <a name="lead-management-instructions-for-table-storage"></a>테이블 저장소에 대 한 리드 관리 지침

이 문서에서는 영업 리드를 관리 하기 위해 Azure Table storage를 구성 하는 방법을 설명 합니다. Table storage는 고객 정보를 저장 하 고 수정 하는 데 도움이 됩니다.

## <a name="configure-table-storage"></a>Table Storage 구성

1. Azure 계정이 없으면 [무료 평가판 계정을 만듭니다](https://azure.microsoft.com/pricing/free-trial/).
1. 계정이 활성화 되 면 [Azure Portal](https://portal.azure.com)에 로그인 합니다.
1. Azure Portal에서 다음 단계를 수행 합니다.  
    1. 왼쪽 창에서 **+ 리소스 만들기** 를 선택 합니다. **새** 창이 열립니다.
    1. **새** 창에서 **저장소**를 선택 합니다. **중요** 한 목록이 오른쪽에 열립니다.
    1. **저장소 계정**을 선택 합니다. 그런 다음 [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)의 지침을 따릅니다.

    ![Azure Storage 계정 만들기](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    저장소 계정에 대 한 자세한 내용은 [빠른 시작 자습서](https://docs.microsoft.com/azure/storage/)를 참조 하세요. 가격 책정 정보는 [Azure storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조 하세요.

1. 저장소 계정이 프로 비전 될 때까지 기다렸다가 일반적으로 몇 분이 걸립니다. 그런 다음 Azure Portal의 홈 페이지에서 계정에 액세스 합니다. 탐색 창에서 **모든 리소스** 또는 **모든 리소스** 보기를 선택 합니다.

    ![Azure 저장소 계정에 액세스](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. 저장소 계정 창에서 키에 대 한 저장소 계정 연결 문자열을 복사 합니다. Cloud 파트너 포털의 저장소 계정에 대 한 **연결 문자열** 필드에 붙여넣습니다.

    연결 문자열 예:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure Storage 키](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

[Azure Storage 탐색기](https://azurestorageexplorer.codeplex.com/) 또는 유사한 도구를 사용 하 여 테이블 저장소의 데이터를 볼 수 있습니다. 데이터를 내보낼 수도 있습니다.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>테이블 저장소와 Microsoft Flow 사용 (*선택 사항*)

리더가 테이블 저장소에 추가 되는 경우 [Microsoft Flow](https://docs.microsoft.com/flow/) 를 사용 하 여 자동으로 알림을 보낼 수 있습니다. Microsoft Flow 계정이 없으면 [무료 계정에 등록](https://flow.microsoft.com/)하세요.

### <a name="lead-notification-example"></a>잠재 고객 알림 예제

이 예에서는 기본 흐름을 만드는 방법을 보여 줍니다. 흐름은 새 잠재 고객이 테이블 저장소에 추가 되 면 매시간 전자 메일 알림을 자동으로 보냅니다.

1. Microsoft Flow 계정에 로그인합니다.
1. 왼쪽의 탐색 창에서 **내 흐름**을 선택 합니다.
1. 위쪽 탐색 모음에서 **+ 새로 만들기**를 선택 합니다.  
1. 드롭다운 목록에서 **+ 빈 페이지에서 만들기**를 선택 합니다.
1. **빈 흐름 만들기**에서 **빈 페이지에서 만들기**를 선택 합니다.

   ![빈 곳에서 새 흐름 만들기](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. 커넥터 및 트리거 검색 페이지에서 **트리거**를 선택합니다.
1. **트리거**에서 **되풀이**를 선택합니다.
1. **되풀이** 창에서 기본 설정인 **간격**을 **1** 로 유지 합니다. **빈도** 드롭다운 목록에서 **시간**을 선택 합니다.

   >[!NOTE] 
   >이 예에서는 1 시간 간격을 사용 합니다. 그러나 비즈니스 요구 사항에 가장 적합 한 간격 및 빈도를 선택할 수 있습니다.

   ![되풀이에 대해 1 시간 빈도 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. **+ 새 단계**를 선택 합니다.
1. **이전 시간**을 검색 한 다음 **작업 선택**에서 **이전 시간 가져오기** 를 선택 합니다.

    !["과거 시간 가져오기" 작업을 찾아 선택 합니다.](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. **이전 시간 가져오기** 창에서 **간격** 을 **1**로 설정 합니다.  **시간 단위** 드롭다운 목록에서 **시간**을 선택 합니다.
    >[!IMPORTANT] 
    >**간격** 및 **시간 단위가** 되풀이에 대해 구성 된 간격 및 빈도 (8 단계)와 일치 하는지 확인 합니다.

    ![이전 시간 간격 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >언제 든 지 흐름을 확인 하 여 각 단계가 올바르게 구성 되었는지 확인할 수 있습니다. 흐름 메뉴 모음에서 **흐름 검사기** 를 선택 합니다.

다음 단계 집합에서 저장소 테이블에 연결 하 고 새 잠재 고객을 처리 하는 처리 논리를 설정 합니다.

1. **이전 시간 가져오기** 단계를 완료 한 후 **+ 새 단계**를 선택 하 고 **엔터티 가져오기**를 검색 합니다.
1. **작업**에서 **엔터티 가져오기** 및 **고급 옵션 표시**를 차례로 선택합니다.
1. **엔터티 가져오기** 창에서 다음 필드를 입력 합니다.

   - **테이블**: 테이블 저장소의 이름입니다. 다음 이미지는 입력 한 "MarketPlaceLeads"을 보여 줍니다.

     ![Azure 테이블 이름에 대한 사용자 지정 값 선택](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **필터 쿼리**:이 필드를 선택 하면 **이전 시간 가져오기** 아이콘이 팝업 창에 표시 됩니다. **이전 시간** 을 선택 하 여이 값을 타임 스탬프로 사용 하 여 쿼리를 필터링 합니다. 또는 필드에 다음 함수를 붙여 넣을 수 있습니다.
   
      `CreatedTime Timestamp gt '@{body('Get_past_time')}'` 

     ![필터 쿼리 함수 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. 새 **단계** 를 선택 하 여 새 잠재 고객에 대 한 테이블 저장소를 검색 하는 조건을 추가 합니다.

   !["새 단계"를 사용 하 여 테이블 저장소를 검사 하는 조건 추가](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. **작업 선택** 창에서 **작업**을 선택한 다음 **조건 제어**를 선택 합니다.

     ![조건 컨트롤 추가](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. **조건** 창에서 **값 선택**을 선택 하 고 팝업 창에서 **식** 을 선택 합니다.
1. `length(body('Get_entities')?['value'])` ***Fx*** 필드에 붙여넣습니다. **확인**을 선택하여 이 함수를 추가합니다. 



     ![조건에 함수 추가](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. 조건의 결과에 따라 수행할 작업을 설정합니다.

    1. 드롭다운 목록에서 **보다 큼을** 선택 합니다.
   1. 값으로 **0** 을 입력 합니다.

     ![조건 결과에 따라 작업 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. 조건이 "If no"로 확인 되 면 아무 작업도 수행 하지 않습니다.

    조건이 "예 인 경우"로 확인 되 면 Office 365 계정을 연결 하 여 전자 메일을 보내는 작업을 트리거합니다.
   1. **작업 추가**를 선택 합니다.
   1. **이메일 보내기**를 선택합니다.
   1. **전자 메일 보내기** 창에서 다음 필드에 정보를 입력 합니다.

      - **받는**사람: 알림을 받을 사람의 전자 메일 주소입니다.
      - **제목**: 전자 메일의 제목입니다. 예: *새 잠재 고객!*
      - **Body**: 각 전자 메일에 포함 하려는 텍스트입니다 (선택 사항). 또한 `body('Get_entities')?['value']` 잠재 고객 정보를 삽입 하는 함수로 붙여 넣습니다.

        >[!NOTE] 
        >전자 메일의 본문에 추가 정적 또는 동적 데이터 요소를 삽입할 수 있습니다.

      ![잠재 고객 알림에 대한 이메일 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. **저장**을 선택하여 흐름을 저장합니다. Microsoft Flow에서 오류를 자동으로 테스트 합니다. 오류가 없으면 흐름이 저장 된 후 실행이 시작 됩니다.

    다음 그림은 최종 흐름이 표시 되는 방법의 예를 보여 줍니다.

    [![최종 흐름 시퀀스](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    *이미지를 선택 하 여 확대 합니다.*

### <a name="manage-your-flow"></a>흐름 관리

흐름을 실행 한 후에는 쉽게 관리할 수 있습니다. 흐름에 대해 완전히 제어할 수 있습니다. 예를 들어 흐름을 중지하고 편집하고 실행 기록을 확인하고 분석을 얻을 수 있습니다. 다음 그림에서는 흐름 관리 옵션을 보여 줍니다.

 ![흐름 관리 옵션](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

흐름 **해제**를 선택할 때까지 흐름은 계속 실행 됩니다.

잠재 고객 전자 메일 알림을 받지 못하는 경우에는 새 잠재 고객이 테이블 저장소에 추가 되지 않았습니다.
흐름 오류가 발생 하는 경우 다음 예제와 같은 전자 메일을 받게 됩니다.

 ![흐름 실패 이메일 알림](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>다음 단계

[잠재 고객 구성](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)

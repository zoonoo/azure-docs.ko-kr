---
title: Azure 테이블 저장소 | Azure 마켓플레이스
description: Azure 테이블 저장소에서 잠재 고객 관리를 구성합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280357"
---
# <a name="lead-management-instructions-for-table-storage"></a>테이블 스토리지에 대한 리드 관리 지침

이 문서에서는 판매 잠재 고객을 관리하도록 Azure Table 저장소를 구성하는 방법을 설명합니다. 테이블 저장소를 사용하면 고객 정보를 저장하고 수정할 수 있습니다.

## <a name="configure-table-storage"></a>Table Storage 구성

1. Azure 계정이 없는 경우 무료 [평가판 계정을 만듭니다.](https://azure.microsoft.com/pricing/free-trial/)
1. 계정이 활성화되면 [Azure 포털에](https://portal.azure.com)로그인합니다.
1. Azure 포털에서 다음 단계를 따르십시오.  
    1. **왼쪽창에서 +자원 만들기를** 선택합니다. **새** 창이 열립니다.
    1. **새** 창에서 **저장소**를 선택합니다. **추천** 목록이 오른쪽에 열립니다.
    1. **저장소 계정을**선택합니다. 그런 다음 저장소 [계정 만들기의](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)지침을 따릅니다.

    ![Azure Storage 계정 만들기](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    저장소 계정에 대한 자세한 내용은 [빠른 시작 자습서를](https://docs.microsoft.com/azure/storage/)참조하십시오. 가격 정보는 [Azure 저장소 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하십시오.

1. 일반적으로 몇 분 정도 걸리는 저장소 계정이 프로비전될 때까지 기다립니다. 그런 다음 Azure 포털의 홈 페이지에서 계정에 액세스: 탐색 창에서 모든 리소스 또는 **모든 리소스** **보기를** 선택합니다.

    ![Azure 저장소 계정에 액세스](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. 저장소 계정 창에서 키에 대한 저장소 계정 연결 문자열을 복사합니다. 클라우드 파트너 포털의 저장소 계정에 대한 **연결 문자열** 필드에 붙여넣습니다.

    예제 연결 문자열:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure Storage 키](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

[Azure 저장소 탐색기](https://azurestorageexplorer.codeplex.com/) 또는 유사한 도구를 사용하여 테이블 저장소의 데이터를 볼 수 있습니다. 데이터에서 내보낼 수도 있습니다.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>테이블 저장소와 함께 Microsoft 흐름*사용(선택 사항)*

[Microsoft Flow를](https://docs.microsoft.com/flow/) 사용하여 잠재 고객을 테이블 저장소에 추가할 때 자동으로 알림을 보낼 수 있습니다. Microsoft Flow 계정이 없는 경우 [무료 계정에 등록하십시오.](https://flow.microsoft.com/)

### <a name="lead-notification-example"></a>잠재 고객 알림 예제

이 예제에서는 기본 흐름을 만드는 방법을 보여 주며 있습니다. 새 잠재 고객을 테이블 저장소에 추가하면 흐름이 매시간 이메일 알림을 자동으로 보냅니다.

1. Microsoft Flow 계정에 로그인합니다.
1. 왼쪽의 탐색 창에서 **내 흐름을**선택합니다.
1. 상단 탐색 모음에서 **+New 를 선택합니다.**  
1. 드롭다운 목록에서 **빈 에서 +Create를**선택합니다.
1. **공백에서 흐름 만들기에서** **빈에서 만들기를 선택합니다.**

   ![빈 곳에서 새 흐름 만들기](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. 커넥터 및 트리거 검색 페이지에서 **트리거**를 선택합니다.
1. **트리거**에서 **되풀이**를 선택합니다.
1. **되풀이** 창에서 **간격에**대해 기본 설정을 **1로** 유지합니다. **빈도** 드롭다운 목록에서 **시간을**선택합니다.

   >[!NOTE] 
   >이 예제에서는 1시간 간격을 사용합니다. 그러나 비즈니스 요구 사항에 가장 적합한 간격과 빈도를 선택할 수 있습니다.

   ![재발을 위한 1시간 빈도 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. **+새 단계를**선택합니다.
1. 과거 **시간 받기를**검색한 다음 **작업 선택에서**과거 시간 **받기를** 선택합니다.

    ![찾기 및 "과거 시간 받기" 작업 선택](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. 지난 시간 열기 창에서 **간격을** **1로** **설정합니다.**  시간 **단위** 드롭다운 목록에서 **시간을**선택합니다.
    >[!IMPORTANT] 
    >**간격** 및 시간 **단위가** 되풀이를 위해 구성한 간격 및 빈도와 일치하는지 확인합니다(8단계).

    ![과거 시간 간격 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >언제든지 흐름을 확인하여 각 단계가 올바르게 구성되었는지 확인할 수 있습니다: 흐름 메뉴 모음에서 **흐름 검사기** 선택.

다음 단계에서는 저장소 테이블에 연결하고 새 잠재 고객을 처리하도록 처리 논리를 설정합니다.

1. 지난 **시간** 단계 받기 후 **+새 단계**를 선택한 다음 **엔터티 받기를**검색합니다.
1. **작업**에서 **엔터티 가져오기** 및 **고급 옵션 표시**를 차례로 선택합니다.
1. 엔터티 **받기** 창에서 다음 필드를 채웁니다.

   - **표**: 테이블 저장소의 이름입니다. 다음 이미지는 "MarketPlaceLeads"가 입력된 것을 보여줍니다.

     ![Azure 테이블 이름에 대한 사용자 지정 값 선택](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **쿼리 필터**: 이 필드를 선택하면 **과거 시간 표시 아이콘이** 팝업 창에 표시됩니다. 이 값을 타임스탬프로 사용하여 쿼리를 필터링하려면 **지난 시간을** 선택합니다. 또는 다음 함수를 필드에 붙여넣을 수 있습니다.
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![필터 쿼리 기능 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. **새 단계를** 선택하여 조건을 추가하여 테이블 저장소에서 새 잠재 고객을 검색합니다.

   !["새 단계"를 사용하여 테이블 저장소를 스캔하는 조건을 추가합니다.](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. 작업 창 **에서** **작업**선택을 선택한 다음 **조건 제어**를 선택합니다.

     ![조건 컨트롤 추가](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. **조건** 창에서 **값 선택을**선택한 다음 팝업 창에서 **표현식을** 선택합니다.
1. FX `length(body('Get_entities')?['value'])` 필드에 ***fx*** 붙여 넣습니다. **확인**을 선택하여 이 함수를 추가합니다. 



     ![조건에 함수 추가](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. 조건의 결과에 따라 수행할 작업을 설정합니다.

    1. 선택은 드롭다운 **목록보다 큽입니다.**
   1. **값으로 0을** 입력합니다.

     ![조건 결과에 따라 작업 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. 조건이 "아니오"로 확인되면 아무 것도 하지 마십시오.

    조건이 "예인 경우"로 확인되면 Office 365 계정을 연결하여 전자 메일을 보내는 작업을 트리거합니다.
   1. **작업 추가를 선택합니다.**
   1. **이메일 보내기**를 선택합니다.
   1. 전자 **메일 보내기** 창에서 다음 필드에 정보를 입력합니다.

      - **에**: 알림을 받을 모든 사람을위한 이메일 주소.
      - **제목**: 이메일의 제목입니다. 예: *새 잠재 고객!*
      - **본문**: 각 이메일에 포함할 텍스트입니다(선택 사항). 또한 잠재 `body('Get_entities')?['value']` 고객 정보를 삽입하는 함수로 붙여 넣습니다.

        >[!NOTE] 
        >이메일 본문에 추가 정적 또는 동적 데이터 점을 삽입할 수 있습니다.

      ![잠재 고객 알림에 대한 이메일 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. **저장**을 선택하여 흐름을 저장합니다. Microsoft Flow는 자동으로 오류를 테스트합니다. 오류가 없는 경우 흐름이 저장된 후 실행되기 시작합니다.

    다음 이미지는 최종 흐름의 모양을 보여 주며,

    [![최종 흐름 시퀀스](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (확대*할 이미지를 선택합니다.*)

### <a name="manage-your-flow"></a>흐름 관리

실행 후 흐름을 쉽게 관리할 수 있습니다. 흐름에 대해 완전히 제어할 수 있습니다. 예를 들어 흐름을 중지하고 편집하고 실행 기록을 확인하고 분석을 얻을 수 있습니다. 다음 이미지는 흐름 관리 옵션을 보여 주었습니다.

 ![흐름 관리 옵션](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

흐름 해제를 선택할 때까지 흐름이 계속 **실행됩니다.**

잠재 고객 이메일 알림을 받지 못하는 경우 테이블 저장소에 새 잠재 고객을 추가하지 않은 것입니다.
흐름 오류가 발생하는 경우 다음 예제와 같은 전자 메일을 받게 됩니다.

 ![흐름 실패 이메일 알림](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>다음 단계

[고객 잠재 고객 구성](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)

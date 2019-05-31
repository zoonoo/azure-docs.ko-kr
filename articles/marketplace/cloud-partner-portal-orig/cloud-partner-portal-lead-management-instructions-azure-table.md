---
title: Azure 테이블 | Azure Marketplace
description: Azure 테이블 관련 잠재 고객 관리 구성 지침을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: a1bcab9816627b453ba8b20b7bcd9402c2dfd151
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240651"
---
# <a name="lead-management-instructions-for-azure-table"></a>Azure 테이블에 대 한 리드 관리 지침

이 문서에서는 영업 잠재 고객 저장용 Azure 테이블을 구성하는 방법을 설명합니다. Azure 테이블을 사용하면 고객 정보를 저장하고 사용자 지정할 수 있습니다.


## <a name="how-to-configure-azure-table"></a>Azure 테이블을 구성 하는 방법

1. Azure 계정이 없으면 [평가판 계정을 생성](https://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.
2. Azure 계정에 활성화 된 후에 로그인 합니다 [Azure portal](https://portal.azure.com)합니다.
3. Azure portal에서 다음 절차를 사용 하 여 저장소 계정을 만듭니다.  
    1. 선택 **+ 리소스 만들기** 왼쪽된 메뉴 모음에서.  합니다 **새로 만들기** (블레이드) 창 오른쪽에 표시 됩니다.
    2. 선택 **저장소** 에 **새로 만들기** 창입니다.  A **추천** 목록이 오른쪽에 표시 됩니다.
    3. 선택 **저장소 계정** 계정 만들기를 시작 합니다.  문서의 지침을 따릅니다 [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)합니다.

    ![Azure Storage 계정을 만드는 단계](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    저장소 계정에 대 한 자세한 내용은 선택 [빠른 시작 자습서](https://docs.microsoft.com/azure/storage/)합니다.  저장소 가격에 대한 자세한 내용은 [저장소 가격](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

4. 저장소 계정 프로 비전 될 때까지 대기, 일반적으로 몇 분을 수행 하는 프로세스입니다.  다음에서 저장소 계정에 액세스 합니다 **홈** 선택 하 여 Azure 포털의 페이지 **모든 리소스를 참조 하세요** 를 선택 하 여 **모든 리소스** 왼쪽된 탐색 영역에서 Azure portal의 메뉴 모음입니다.

    ![Azure storage 계정에 액세스](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

5. 저장소 계정 창에는 키에 대 한 저장소 계정 연결 문자열을 복사 및 붙여 넣습니다 합니다 **저장소 계정 연결 문자열** 클라우드 파트너 포털에서 필드입니다. 연결 문자열의 예는 다음과 같습니다.

```sql
DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
```

  ![Azure 저장소 키](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

사용할 수 있습니다 [Azure storage 탐색기](https://azurestorageexplorer.codeplex.com/) 또는 다른 유사한 도구 저장소 테이블에서 데이터를 표시 합니다. 또한 Azure 테이블에서 데이터를 내보낼 수 있습니다.


## <a name="use-microsoft-flow-with-an-azure-table-optional"></a>Microsoft Flow 사용 하 여 Azure 테이블을 사용 하 여 (*선택적*) 

[Microsoft Flow](https://docs.microsoft.com/flow/)를 사용하여 잠재 고객을 Azure 테이블에 추가할 때마다 알림을 자동화할 수 있습니다. 계정이 없으면 있습니다 [무료 계정을 신청할](https://flow.microsoft.com/)합니다.


### <a name="lead-notification-example"></a>잠재 고객 알림 예제

Azure 테이블에 새 잠재 고객이 추가 되 면 전자 메일 알림을 자동으로 전송 하는 기본 흐름을 만드는 지침으로이 예제를 사용 합니다. 이 예제에서는 테이블 스토리지가 업데이트되면 매시간 잠재 고객 정보를 되풀이해서 보내도록 설정합니다.

1. Microsoft Flow 계정에 로그인합니다.
2. 왼쪽 탐색 모음에서 **내 흐름**을 선택합니다.
3. 왼쪽 탐색 모음에서 **+ 새로 만들기**를 선택합니다.  
4. 드롭다운 목록에서 **+ 빈 곳에서 만들기** 선택
5. 빈 곳에서 흐름 만들기에서 **빈 곳에서 만들기**를 선택합니다.

   ![빈 곳에서 새 흐름 만들기](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

6. 커넥터 및 트리거 검색 페이지에서 **트리거**를 선택합니다.
7. **트리거**에서 **되풀이**를 선택합니다.
8. **되풀이** 창에서 **간격**에 기본 설정 1을 유지합니다. **빈도** 드롭다운 목록에서 **시간**을 선택합니다.

   >[!NOTE] 
   >이 예제에서는 1시간 간격을 사용하지만 비즈니스 요구에 가장 적합한 간격 및 빈도를 선택할 수 있습니다.

   ![되풀이 대 한 1 시간 빈도 설정 합니다.](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

9. **+ 새 단계**를 선택합니다.
10. "과거 시간 가져오기"를 검색하고 작업 아래에서 **과거 시간 가져오기**를 선택합니다. 

    ![과거 시간 가져오기 찾기 및 선택 작업](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

11. **과거 시간 가져오기** 창에서 **간격**을 1로 설정합니다.  **시간 단위** 드롭다운 목록에서 **시간**을 선택합니다.
    >[!IMPORTANT] 
    >이 간격 및 시간 단위가 되풀이에 대해 구성한 간격 및 빈도와 일치하는지 확인합니다.

    ![과거 시간 가져오기 간격 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >흐름을 검사하여 각 단계가 올바르게 구성됐는지 확인할 수 있습니다. 흐름을 검사하려면 흐름 메뉴 모음에서 **흐름 검사기**를 선택합니다.

다음 단계의 세트에서 Azure 테이블에 연결하고 새 잠재 고객을 처리하는 처리 논리를 설정합니다.

1. 과거 시간 가져오기 단계 이후에 **+ 새 단계**를 선택한 다음, "엔터티 가져오기"를 검색합니다.
2. **작업**에서 **엔터티 가져오기** 및 **고급 옵션 표시**를 차례로 선택합니다.
3. **엔터티 가져오기** 창에서 다음 필드에 정보를 입력합니다.

   - **테이블** – Azure Table Storage의 이름을 입력합니다. 다음 화면 캡처에는 이 예제에 "MarketPlaceLeads"를 입력할 때 메시지가 표시됩니다. 

     ![Azure 테이블 이름에 대한 사용자 지정 값 선택](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **필터 쿼리** –이 필드를 클릭 하며 **과거 시간 가져오기** 팝업 창에 아이콘이 표시 됩니다. **과거 시간**을 선택하고 타임스탬프로 사용하여 쿼리를 필터링합니다. 또는 필드에 다음 함수를 붙여넣을 수 있습니다. CreatedTime `gt datetime'@{body('Get_past_time')}'` 

     ![필터 쿼리 함수 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

4. **새 단계**를 선택하고 조건을 추가하여 Azure 테이블에서 새 잠재 고객을 검색합니다.

   ![Azure 테이블을 검색하려면 새 단계를 사용하여 조건 추가](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

5. **작업 선택** 창에서 **작업**를 선택한 다음, **조건** 컨트롤을 선택합니다.

     ![조건 컨트롤 추가](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

6. **조건** 창에서 **값 선택** 필드를 선택한 다음, 팝업 창에서 **식**을 선택합니다.
7. ***fx*** 필드에 `length(body('Get_entities')?['value'])`를 붙여넣기합니다. **확인**을 선택하여 이 함수를 추가합니다. 조건 설정을 마치려면

   - 드롭다운 목록에서 “다음 값보다 큼”을 선택합니다.
   - 값으로 0 입력 

     ![조건에 함수 추가](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

8. 조건의 결과에 따라 수행할 작업을 설정합니다.

     ![조건 결과에 따라 작업 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

9. 조건이 **아니요인 경우**를 확인하면 아무 것도 수행하지 않습니다. 
10. 조건이 **예인 경우**를 확인하면 Office 365 계정에 연결하는 작업을 트리거하여 이메일을 전송합니다. **작업 추가**를 선택합니다.
11. **이메일 보내기**를 선택합니다. 
12. **이메일 보내기** 창에서 다음 필드에 정보를 입력합니다.

    - **받는 사람** - 이 알림을 받을 모든 사람의 이메일 주소를 입력합니다.
    - **제목** – 메일의 제목을 입력합니다. 예를 들면 다음과 같습니다. 새 잠재 고객!
    - **본문**:   각 이메일에 추가하려는 텍스트를 추가(선택 사항)한 다음, `body('Get_entities')?['value']` 본문에 잠재 고객 정보를 삽입하는 함수로 붙여넣습니다.

      >[!NOTE] 
      >이 이메일의 본문에 정적 또는 동적 추가 데이터 요소를 삽입할 수 있습니다.

      ![잠재 고객 알림에 대한 이메일 설정](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. **저장**을 선택하여 흐름을 저장합니다. Microsoft Flow에서는 흐름을 자동으로 테스트하여 오류를 검색합니다. 오류가 없으면 흐름이 저장된 후 실행을 시작합니다.

다음 화면 캡처에서는 최종 흐름이 표시되는 방법의 예제를 보여줍니다.

[![최종 흐름 순서](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

(*확대 하려면 이미지를 클릭 합니다.* )


### <a name="manage-your-flow"></a>흐름 관리

실행된 후 흐름을 관리하기는 쉽습니다.  흐름에 대해 완전히 제어할 수 있습니다. 예를 들어 흐름을 중지하고 편집하고 실행 기록을 확인하고 분석을 얻을 수 있습니다. 다음 화면 캡처에서는 흐름을 관리할 수 있는 옵션을 보여줍니다. 

 ![흐름 관리](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

흐름은 **흐름 해제** 옵션을 사용하여 중지될 때까지 계속 실행 상태를 유지합니다.

모든 잠재 고객 이메일 알림을 받지 못하는 경우는 새 잠재 고객이 Azure 테이블에 추가되지 않았음을 의미합니다. 모든 흐름이 실패한 경우 다음 화면 캡처의 예제와 같은 이메일을 받습니다.

 ![흐름 실패 이메일 알림](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)


## <a name="next-steps"></a>다음 단계

[잠재 고객 가져오기](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)

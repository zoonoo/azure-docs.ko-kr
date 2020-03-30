---
title: Azure 테이블 | Azure 마켓플레이스
description: Azure 테이블 관련 잠재 고객 관리 구성 지침을 설명합니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: f511a60b533d6d1e0b1ae8847d0ee0fb6be3500c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288838"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Azure 테이블을 사용하여 잠재 고객 관리 구성

CrM(고객 관계 관리) 시스템이 Azure Marketplace 및 AppSource 잠재 고객을 수신하기 위해 파트너 센터에서 명시적으로 지원되지 않는 경우 Azure Table을 사용하여 이러한 잠재 고객을 처리할 수 있습니다. 그런 다음 데이터를 내보내고 CRM 시스템으로 가져올 수 있습니다. 이 문서의 지침은 Azure Storage 계정및 해당 계정 아래의 Azure 테이블을 만드는 프로세스를 안내합니다. 또한 Microsoft Flow를 사용하여 새 흐름을 만들어 오퍼가 잠재 고객용을 받을 때 전자 메일 알림을 보낼 수 있습니다.

## <a name="configure-azure-table"></a>Azure 테이블 구성

1. Azure 계정이 없으면 [평가판 계정을 생성](https://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.
2. Azure 계정이 활성화되면 [Azure 포털에](https://portal.azure.com)로그인합니다.
3. Azure 포털에서 다음 절차를 사용하여 저장소 계정을 만듭니다.  
    1. 왼쪽 메뉴 모음에서 **+리소스 만들기를** 선택합니다.  **새** 창(블레이드)이 오른쪽에 표시됩니다.
    2. **새** 창에서 **저장소를** 선택합니다.  **추천** 목록이 오른쪽에 표시됩니다.
    3. 계정 만들기를 시작하려면 **저장소 계정을** 선택합니다.  [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)문서의 지침을 따릅니다.

        ![Azure Storage 계정을 만드는 단계](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        저장소 계정에 대한 자세한 내용은 [빠른 시작 자습서를](https://docs.microsoft.com/azure/storage/)선택합니다.  스토리지 가격에 대한 자세한 내용은 [스토리지 가격](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

4. 저장소 계정이 프로비전될 때까지 기다립니다( 일반적으로 몇 분 정도 소요되는 프로세스).  그런 다음 모든 리소스 보기를 선택하거나 Azure 포털의 왼쪽 탐색 메뉴 모음에서 **모든 리소스를** 선택하여 Azure 포털의 **홈** 페이지에서 저장소 계정에 **액세스합니다.**

    ![Azure 저장소 계정에 액세스](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. 저장소 계정 창에서 **액세스 키를** 선택하고 키에 대한 *연결 문자열* 값을 복사합니다. 이 값은 마켓플레이스 오퍼에 대한 잠재 고객을 받기 위해 게시 포털에서 제공해야 하는 *저장소 계정 연결 문자열* 값입니다. 

    연결 찌르기의 예는 다음과 같은 것입니다.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure Storage 키](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. 저장소 계정 창에서 **테이블을** 선택하고 **+Table을** 선택하여 테이블을 만듭니다. 테이블의 이름을 입력하고 **확인을**선택합니다. 잠재 고객을 받을 때 전자 메일 알림을 수신하도록 MS Flow를 구성하려는 경우 필요에 따라 이 값을 저장합니다.

    ![Azure 테이블](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    [Azure Storage Explorer](https://archive.codeplex.com/?p=azurestorageexplorer) 또는 다른 도구를 사용하여 스토리지 테이블의 데이터를 확인할 수 있습니다. Azure 테이블에서 데이터를 내보낼 수도 있습니다. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>(선택 사항) Azure 테이블과 함께 Microsoft 흐름 사용  

[Microsoft Flow](https://docs.microsoft.com/flow/)를 사용하여 잠재 고객을 Azure 테이블에 추가할 때마다 알림을 자동화할 수 있습니다. 계정이 없는 경우 [무료 계정에 가입할](https://flow.microsoft.com/)수 있습니다.

### <a name="lead-notification-example"></a>잠재 고객 알림 예제

Azure 테이블에 새 잠재 고객이 추가되면 이메일 알림을 자동으로 전송하는 간단한 흐름을 만드는 가이드로 이 예제를 사용합니다. 이 예제는 테이블 저장소가 업데이트되는 경우 매시간 잠재 고객 정보를 보내도록 되풀이를 설정합니다.

1. Microsoft Flow 계정에 로그인합니다.
2. 왼쪽 탐색 모음에서 **내 흐름**을 선택합니다.
3. 왼쪽 탐색 모음에서 **+ 새로 만들기**를 선택합니다.  
4. 드롭다운 목록에서 **공백에서 + 예약된** 옵션을 선택합니다.

   ![내 흐름 **+ 예약 - 공백에서**](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  에서 *간격에* 대한 "1"과 주파수에 대한 "시간"을 모든 선택 반복에서 예약 된 흐름 창 을 구축합니다. *Repeat every* 또한 원하는 경우 흐름에 이름을 지정합니다. **만들기**를 선택합니다.

    >[!Note]
    >이 예제에서는 1시간 간격을 사용하지만 비즈니스 요구 사항에 가장 적합한 간격과 빈도를 선택할 수 있습니다.

    ![예약된 흐름을 빌드합니다.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. **+ 새 단계**를 선택합니다.
7. 에서 *작업* 창 선택 "과거 시간" 다음 작업에서 **과거 시간 받기를** 선택 합니다.

   ![작업을 선택합니다.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. **과거 시간 가져오기** 창에서 **간격**을 1로 설정합니다. **시간 단위** 드롭다운 목록에서 **시간**을 선택합니다.

    >[!Important]
    >이 간격 및 시간 단위가 5단계에서 되풀이를 위해 구성한 간격 및 빈도와 일치하는지 확인합니다.

    ![과거 시간 가져오기 간격 설정](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>흐름을 검사하여 각 단계가 올바르게 구성됐는지 확인할 수 있습니다. 흐름을 검사하려면 흐름 메뉴 모음에서 **흐름 검사기**를 선택합니다.

다음 단계에서는 Azure 테이블에 연결하고 새 잠재 고객을 처리하도록 처리 논리를 설정합니다.

9. 지난 시간 단계 받기 후 **+ 새 단계를**선택한 다음 작업 선택 창에서 "엔터티 받기"를 *검색합니다.*
10. **작업에서** **엔터티 받기(Azure 테이블 저장소)를**선택합니다.
11. Azure **테이블 저장소** 창에서 다음 필드에 대한 정보를 제공하고 **다음 만들기를**선택합니다.

    * *연결 이름* - 이 흐름과 Azure Table 간에 설정하는 연결에 대해 의미 있는 이름을 제공합니다.
    * *저장소 계정 이름* - Azure 테이블에 대한 저장소 계정 이름을 제공합니다. 저장소 계정의 **액세스 키** 페이지에서 찾을 수 있습니다.
    * *공유 저장소 키* - Azure 테이블에 대한 저장소 계정의 키 값을 제공합니다. 저장소 계정의 **액세스 키** 페이지에서 찾을 수 있습니다.

        ![Azure Table Storage](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    엔터티 만들기를 클릭하면 *엔터티 받기* 창이 표시됩니다. 여기서 **고급 옵션 표시를** 선택하고 다음 필드에 대한 정보를 제공합니다.

       * *표* - Azure 테이블을 구성하는 방법에 대한 지침의 6단계에서 Azure 테이블 저장소의 이름을 선택합니다. 다음 화면 캡처는 이 예제에서 "마켓플레이스 리드" 테이블을 선택한 경우 프롬프트를 표시합니다.

            ![Azure Table은 엔터티를 가져옵니다.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *쿼리 필터* - 이 필드를 선택하고 이 함수를 필드에 붙여넣습니다.`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure 테이블 은 엔터티를 가져옵니다 - 필터 퀴리.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Azure 테이블에 대한 연결 설정을 완료한 후 **새 단계를** 선택하여 Azure 테이블을 검색하여 새 잠재 고객을 검색하는 조건을 추가합니다. 

13. 작업 창 **에서** **작업**선택을 선택한 다음 **조건 컨트롤을**선택합니다.

    ![Azure 테이블 - 작업을 선택합니다.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. **조건** 창에서 **값 선택** 필드를 선택한 다음, 팝업 창에서 **식**을 선택합니다.

15. ***fx*** 필드에 `length(body('Get_entities')?['value'])`를 붙여넣기합니다. **확인**을 선택하여 이 함수를 추가합니다. 

16. 조건 설정을 마치려면
    1. 드롭다운 목록에서 "보다 큽"을 선택합니다.
    2. 값으로 0 입력

        ![Azure 테이블 - 조건입니다.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

다음 몇 단계에서는 조건의 결과에 따라 수행할 작업을 설정합니다.

* 조건이 **없는 경우로**확인되면 아무 것도 수행하지 마십시오.
* 조건이 **예인 경우**를 확인하면 Office 365 계정에 연결하는 작업을 트리거하여 이메일을 전송합니다. 

17. 작업 **추가를** **선택합니다.예인 경우**.

    ![Azure 테이블 - 조건, **예**.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. **전자 메일 보내기(Office 365 Outlook)를 선택합니다.**

    ![Azure 테이블 - 조건** 예**인 경우 전자 메일을 보냅니다.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >다른 이메일 공급자 검색을 사용 하 고 대신 작업으로 전자 메일 알림 (메일) 보내기를 선택 합니다. 이 지침은 Office 365 Outlook을 사용하여 구성하는 방법을 보여 주지만 지침은 다른 전자 메일 공급자와 비슷합니다.

19. Office **365 Outlook** 창에서 다음 필드에 대 한 정보를 제공 합니다.

    1. **받는 사람** - 이 알림을 받을 모든 사람의 이메일 주소를 입력합니다.
    1. **제목** - 이메일의 제목을 제공합니다. 예: 새 잠재 고객!
    1. **본문** - 각 이메일에 포함할 텍스트를 추가한 다음(선택 `body('Get_entities')?['value']`사항) 본문에 붙여넣습니다.

    >[!Note]
    >이 이메일의 본문에 정적 또는 동적 추가 데이터 요소를 삽입할 수 있습니다.

    ![Azure 테이블 - 조건, **예**, Office 365 Outlook 창입니다.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. **저장**을 선택하여 흐름을 저장합니다. Microsoft Flow에서는 흐름을 자동으로 테스트하여 오류를 검색합니다. 오류가 없는 경우 흐름이 저장된 후 실행되기 시작합니다.

다음 화면 캡처에서는 최종 흐름이 표시되는 방법의 예제를 보여줍니다.

![최종 흐름의 예입니다.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>흐름 관리

실행 후 흐름을 쉽게 관리할 수 있습니다. 흐름에 대해 완전히 제어할 수 있습니다. 예를 들어 흐름을 중지하고 편집하고 실행 기록을 확인하고 분석을 얻을 수 있습니다. 다음 화면 캡처에서는 흐름을 관리할 수 있는 옵션을 보여줍니다. 

 ![흐름 관리](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

흐름은 **흐름 해제** 옵션을 사용하여 중지될 때까지 계속 실행 상태를 유지합니다.

잠재 고객 이메일 알림을 받지 못하는 경우 새 잠재 고객을 Azure 테이블에 추가하지 않았음을 의미합니다. 흐름 오류가 있는 경우 다음 화면 캡처의 예제와 같은 전자 메일을 받게 됩니다.

 ![흐름 실패 이메일 알림](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Azure 테이블로 잠재 고객을 보내도록 오퍼 구성

게시 포털에서 오퍼의 잠재 고객 관리 정보를 구성할 준비가 되면 다음 단계를 따르세요.

1. **오퍼의 제안 설정** 페이지로 이동합니다.
2. 잠재 고객 관리 섹션에서 **연결을** 선택합니다.
3. 연결 세부 정보 팝업 창에서 **잠재 고객 대상에**대한 **Azure 테이블을** 선택하고 저장소 계정 **연결 문자열** 필드에 이전 단계를 수행하여 만든 Azure 저장소 계정의 연결 문자열에 붙여넣습니다.
4. **저장**을 선택합니다. 

>[!Note]
>오퍼의 나머지 부분을 구성을 완료하고 게시해야 쿠폰에 대한 잠재 고객을 받을 수 있습니다.

잠재 고객을 생성하면 Microsoft는 잠재 고객을 Azure 테이블로 보냅니다. 흐름을 구성한 경우 구성한 전자 메일 주소로도 전자 메일이 전송됩니다.

![잠재 고객 관리](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![잠재 고객 관리 - 연결 세부 정보](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![잠재 고객 관리 - 연결 세부 정보 저장소 계정](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)


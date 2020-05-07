---
title: Azure Table storage를 사용 하 여 리드 관리-Microsoft 상업적 marketplace
description: Azure Table storage를 사용 하 여 Microsoft AppSource 및 Azure Marketplace에 대 한 잠재 고객을 구성 하는 방법을 알아봅니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 2ecca18e9de02bfe5f3bcb972d0b4034ab8012ac
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791020"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>Azure Table storage를 사용 하 여 상업적 marketplace 잠재 고객 관리

CRM (고객 관계 관리) 시스템을 파트너 센터에서 명시적으로 지원 하지 않고 Microsoft AppSource 및 Azure Marketplace 잠재 고객을 받을 수 있는 경우 Azure Table storage를 사용 하 여 이러한 잠재 고객을 처리할 수 있습니다. 그런 다음 데이터를 내보내 CRM 시스템으로 가져오도록 선택할 수 있습니다. 이 문서에서는 Azure storage 계정 및 해당 계정 아래에 테이블을 만드는 방법을 설명 합니다. 또한 제품에서 잠재 고객을 받을 때 전자 메일 알림을 보내기 위해 전원 자동화를 사용 하 여 새 흐름을 만들 수 있습니다.

## <a name="configure-an-azure-storage-account"></a>Azure storage 계정 구성

1. Azure 계정이 없으면 [평가판 계정을 생성](https://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.
1. Azure 계정이 활성화 되 면 [Azure Portal](https://portal.azure.com)에 로그인 합니다.
1. Azure Portal에서 다음 절차를 사용 하 여 저장소 계정을 만듭니다.

    1. 왼쪽 메뉴 모음에서 **+ 리소스 만들기** 를 선택 합니다. **새** 창이 오른쪽에 나타납니다.
    1. **새** 창에서 **저장소** 를 선택 합니다. **추천** 목록이 오른쪽에 표시 됩니다.
    1. 계정 만들기를 시작 하려면 **저장소 계정** 을 선택 합니다. [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)의 지침을 따릅니다.

        ![Azure Storage 계정을 만드는 단계](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        저장소 계정에 대 한 자세한 내용은 [빠른 시작 자습서](https://docs.microsoft.com/azure/storage/)를 참조 하세요. 저장소 가격 책정에 대 한 자세한 내용은 [저장소 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조 하세요.

1. 저장소 계정이 프로 비전 될 때까지 기다립니다. 이 프로세스는 일반적으로 몇 분 정도 걸립니다. 

## <a name="create-a-table-in-your-storage-account"></a>저장소 계정에 테이블 만들기

1. Azure Portal의 **홈** 페이지에서 저장소 계정에 액세스 하기 위해 **모든 리소스 보기** 를 선택 합니다. Azure Portal의 왼쪽 메뉴 모음에서 **모든 리소스** 를 선택할 수도 있습니다.

    ![Azure 저장소 계정에 액세스](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

1. 저장소 계정 창에서 **액세스 키** 를 선택 하 고 키에 대 한 **연결 문자열** 값을 복사 합니다. Azure Marketplace 제품에 대 한 잠재 고객을 받기 위해 게시 포털에 제공 해야 하는 **저장소 계정 연결 문자열** 값 이므로이 값을 저장 합니다. 

    다음은 연결 문자열의 예입니다.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    ![Azure Storage 키](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

1. 저장소 계정 창에서 **테이블**을 선택 하 고 **+ 테이블** 을 선택 하 여 테이블을 만듭니다. 테이블의 이름을 입력 하 고 **확인**을 선택 합니다. 잠재 고객이 수신 될 때 전자 메일 알림을 받도록 흐름을 구성 하려는 경우이 값을 저장 합니다.

    ![Azure 테이블](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    [Azure Storage 탐색기](https://archive.codeplex.com/?p=azurestorageexplorer) 또는 다른 도구를 사용 하 여 저장소 테이블의 데이터를 볼 수 있습니다. Azure 테이블에서 데이터를 내보낼 수도 있습니다. 

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>필드 전원 자동화를 사용 하 여 잠재 고객 알림 받기

Azure Storage 테이블에 리드를 추가할 때마다 [전원 자동화](https://docs.microsoft.com/flow/) 를 사용 하 여 알림을 자동화할 수 있습니다. 계정이 없는 경우 [무료 계정에 등록할](https://flow.microsoft.com/)수 있습니다.

### <a name="lead-notification-example"></a>잠재 고객 알림 예제

이 예에서는 새 잠재 고객이 Azure Table storage에 추가 될 때 자동으로 전자 메일 알림을 보내는 흐름을 만듭니다. 이 예에서는 테이블 저장소가 업데이트 되는 경우 1 시간 마다 리드 정보를 보내도록 되풀이를 설정 합니다.

1. 전원 자동화 계정에 로그인 합니다.
1. 왼쪽 막대에서 **내 흐름**을 선택 합니다.
1. 위쪽 막대에서 **+ 새로 만들기**를 선택 합니다. 
1. 드롭다운 목록에서 **+ 예약 됨--비어 있음**을 선택 합니다.

   ![내 흐름 + 예약 됨-비어 있음](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. 예약 된 **흐름 작성** 창에서 **각 반복**에 대해 빈도와 **시간** 간격으로 **1** 을 선택 합니다. 또한 원하는 경우 흐름 이름을 지정 합니다. **만들기**를 선택합니다.

   >[!NOTE]
   >이 예에서는 1 시간 간격을 사용 하지만 비즈니스 요구에 가장 적합 한 간격 및 빈도를 선택할 수 있습니다.

   ![예약 된 흐름 만들기](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. **+ 새 단계**를 선택합니다.
1. **작업 선택** 창에서 **과거 시간**을 검색 합니다. 그런 다음 **작업**에서 **이전 시간 가져오기**를 선택 합니다.

   ![작업 선택](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. **이전 시간 가져오기** 창에서 **간격** 을 **1**로 설정 합니다. **시간 단위** 드롭다운 목록에서 **시간**을 선택 합니다.

    >[!IMPORTANT]
    >8 단계에서 보낸 간격 및 시간 단위가 5 단계에서 되풀이에 대해 구성한 간격 및 빈도와 일치 하는지 확인 합니다.

    ![이전 시간 간격 가져오기 설정](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP] 
   >흐름을 검사하여 각 단계가 올바르게 구성됐는지 확인할 수 있습니다. 흐름을 확인 하려면 **흐름** 메뉴 모음에서 **흐름 검사기** 를 선택 합니다.

   다음 단계에서는 테이블에 연결 하 고 새 잠재 고객을 처리 하는 처리 논리를 설정 합니다.

1. **+ 새 단계**를 선택합니다. 그런 다음 **작업 선택** 창에서 **엔터티 가져오기** 를 검색 합니다.
1. **작업**아래에서 **엔터티 가져오기 (Azure Table Storage)** 를 선택 합니다.
1. **Azure Table Storage** 창에서 다음 상자에 대 한 정보를 제공 하 고 **만들기**를 선택 합니다.

    * **연결 이름**:이 흐름과 테이블 사이에 설정 하는 연결에 대 한 의미 있는 이름을 제공 합니다.
    * **저장소 계정 이름**: 테이블의 저장소 계정 이름을 제공 합니다. 저장소 계정의 **액세스 키** 페이지에서이 이름을 찾을 수 있습니다.
    * **공유 저장소 키**: 테이블의 저장소 계정에 대 한 키 값을 제공 합니다. 저장소 계정의 **액세스 키** 페이지에서이 값을 찾을 수 있습니다.

      ![Azure Table Storage 창](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   **만들기**를 선택 하면 **엔터티 가져오기** 창이 표시 됩니다. 여기에서 **고급 옵션 표시**를 선택 하 고 다음 상자에 대 한 정보를 제공 합니다.

   * **테이블**: 테이블 [만들기](#create-a-table-in-your-storage-account)에서 테이블 이름을 선택 합니다. 다음 이미지는이 예제에서 "marketplaceleads" 테이블을 선택 하는 경우의 프롬프트를 보여 줍니다.

     ![엔터티 가져오기 창](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **필터 쿼리**:이 상자를 선택 하 고 상자에이 함수를 붙여넣습니다.`Timestamp gt datetime'@{body('Get_past_time')}'`

     ![엔터티 가져오기, 필터 쿼리 상자](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. 이제 Azure 테이블에 대 한 연결 설정을 완료 했으므로 **새 단계** 를 선택 하 여 새 잠재 고객을 위해 azure 테이블을 검색 하는 조건을 추가 합니다.

1. **작업 선택** 창에서 **작업**을 선택 합니다. 그런 다음 **조건 제어**를 선택 합니다.

    ![작업 창 선택](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. **조건** 창에서 **값 선택**을 선택 합니다. 그런 다음 팝업 창에서 **식** 을 선택 합니다.

1. Fx `length(body('Get_entities')?['value'])` 상자에 **fx** 붙여넣습니다. **확인**을 선택하여 이 함수를 추가합니다. 

1. 조건 설정을 마치려면
    1. 드롭다운 목록에서 **보다 큼을** 선택 합니다.
    2. 값으로 **0** 을 입력 합니다.

        ![조건 창](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   다음 몇 단계에서는 조건의 결과에 따라 수행할 작업을 설정 합니다.

   * 조건이 **이 아닌 경우**로 확인 되 면 아무 작업도 수행 하지 않습니다.
   * 조건이 **예인 경우**를 확인하면 Office 365 계정에 연결하는 작업을 트리거하여 이메일을 전송합니다. 

1. **예 인 경우**에서 **작업 추가를** 선택 합니다.

    ![조건 창, 예 인 경우 작업을 추가 합니다.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. **전자 메일 보내기 (Office 365 Outlook)** 를 선택 합니다.

    ![조건 창, 예 인 경우 전자 메일을 보냅니다.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >다른 전자 메일 공급자를 사용 하려면를 검색 하 고 대신 작업으로 메일 **알림 (메일) 보내기** 를 선택 합니다. 지침은 Office 365 Outlook을 사용 하 여를 구성 하는 방법을 보여 주지만 다른 전자 메일 공급자의 지침은 유사 합니다.

1. Office 365 Outlook 창에서 다음 상자에 대 한 정보를 제공 합니다.

    1. **받는**사람:이 알림을 받을 모든 사람에 대 한 전자 메일 주소를 입력 합니다.
    1. **제목**: 전자 메일의 제목을 입력 합니다. **새 잠재 고객** 을 예로 들 수 있습니다.
    1. **본문**: 각 전자 메일에 포함할 텍스트를 추가 하 고 (선택 사항) 붙여 넣습니다 `body('Get_entities')?['value']`.

    >[!NOTE]
    >이 이메일의 본문에 정적 또는 동적 추가 데이터 요소를 삽입할 수 있습니다.

    ![조건 창, 예 인 경우 Office 365 Outlook 창](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. **저장**을 선택하여 흐름을 저장합니다. 전원 자동화는 오류에 대 한 흐름을 자동으로 테스트 합니다. 오류가 없으면 흐름이 저장 된 후 실행이 시작 됩니다.

다음 그림은 최종 흐름이 표시 되는 방법의 예를 보여 줍니다.

![최종 흐름의 예](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>흐름 관리

흐름을 실행 한 후에는 쉽게 관리할 수 있습니다. 흐름에 대해 완전히 제어할 수 있습니다. 예를 들어 흐름을 중지하고 편집하고 실행 기록을 확인하고 분석을 얻을 수 있습니다. 다음 그림은 흐름을 관리 하는 데 사용할 수 있는 옵션을 보여 줍니다.

 ![흐름 관리](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

흐름은 **흐름 해제** 옵션을 사용하여 중지될 때까지 계속 실행 상태를 유지합니다.

잠재 고객 전자 메일 알림을 받지 못하는 경우 Azure 테이블에 새 잠재 고객이 추가 되지 않았음을 의미 합니다. 흐름 오류가 발생 하는 경우이 예제와 같은 전자 메일을 받게 됩니다.

 ![흐름 실패 이메일 알림](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Azure 테이블에 리드를 보내도록 제품 구성

게시 포털에서 제품에 대 한 리드 관리 정보를 구성할 준비가 되 면 다음 단계를 수행 합니다.

1. 제품에 대 한 **제품 설정** 페이지로 이동 합니다.

1. **리드 관리** 섹션에서 **연결** 을 선택 합니다.
     ![잠재 고객 관리](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

1. **연결 정보** 팝업 창에서 **리드 대상**에 대해 **Azure 테이블** 을 선택 합니다. 
     ![리드 관리, 연결 세부 정보](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. **저장소 계정 연결 문자열** 상자의 이전 단계를 수행 하 여 만든 Azure storage 계정에서 연결 문자열을 붙여 넣습니다.
     ![리드 관리, 연결 정보 저장소 계정](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **연락처 전자 메일**: 회사에서 새 잠재 고객이 수신 될 때 전자 메일 알림을 받을 사람의 전자 메일을 제공 합니다. 세미콜론으로 구분 하 여 여러 전자 메일을 제공할 수 있습니다.

1. **확인**을 선택합니다.

선행 대상에 성공적으로 연결 되었는지 확인 하려면 **유효성 검사** 단추를 선택 합니다. 성공 하면 잠재 고객 대상에 테스트 리드를 갖게 됩니다.

>[!NOTE]
>제안에 대 한 잠재 고객을 받으려면 먼저 나머지 제품을 구성 하 고 게시 해야 합니다.

잠재 고객이 생성 되 면 Microsoft는 Azure 테이블로 잠재 고객을 보냅니다. 흐름을 구성한 경우 전자 메일도 구성한 전자 메일 주소로 전송 됩니다.

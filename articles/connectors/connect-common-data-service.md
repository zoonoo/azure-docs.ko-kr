---
title: Common Data Service에 연결
description: Azure Logic Apps를 사용 하 여 Common Data Service 레코드 만들기 및 관리
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/08/2020
tags: connectors
ms.openlocfilehash: 98da7e959e4b59ad2d0f3f3f79364391b4ceddbd
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997101"
---
# <a name="create-and-manage-records-in-common-data-service-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 Common Data Service에서 레코드 만들기 및 관리

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Common Data Service 커넥터](https://docs.microsoft.com/connectors/commondataservice/)를 사용 하 여 [Common Data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro) 데이터베이스에서 레코드를 관리 하는 자동화 된 워크플로를 작성할 수 있습니다. 이러한 워크플로는 레코드를 만들고, 레코드를 업데이트 하 고, 기타 작업을 수행할 수 있습니다. Common Data Service 데이터베이스에서 정보를 가져와 논리 앱에서 사용할 수 있는 다른 작업에 대 한 출력을 제공할 수도 있습니다. 예를 들어 Common Data Service 데이터베이스에서 레코드가 업데이트 되는 경우 Office 365 Outlook 커넥터를 사용 하 여 전자 메일을 보낼 수 있습니다.

이 문서에서는 새 잠재 고객 레코드를 만들 때마다 작업 레코드를 만드는 논리 앱을 빌드하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 조직에서 비즈니스 데이터 및 Common Data Service 데이터베이스를 저장, 관리 및 공유 하는 공간에 해당 하는 [Common Data Service 환경](https://docs.microsoft.com/power-platform/admin/environments-overview)입니다. 이러한 응용 프로그램은 Azure AD Graph API를 사용할 수 있습니다. 자세한 내용은 다음 리소스를 참조하세요.<p>

  * [자세한 정보: Common Data Service 시작](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
  * [전원 플랫폼-환경 개요](https://docs.microsoft.com/power-platform/admin/environments-overview)

* Common Data Service 데이터베이스의 레코드에 액세스 하려는 논리 앱 및 논리 앱을 [만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md) 에 대 한 기본 기술 자료입니다. Common Data Service 트리거를 사용 하 여 논리 앱을 시작 하려면 빈 논리 앱이 필요 합니다. Azure Logic Apps를 처음 사용 하는 경우 [빠른 시작: Azure Logic Apps를 사용 하 여 첫 번째 워크플로 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토 하세요.

## <a name="add-common-data-service-trigger"></a>Common Data Service 트리거 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

이 예에서는 새 레코드를 만들 때 발생 하는 Common Data Service 트리거를 추가 합니다.

1. [Azure Portal](https://portal.azure.com)에서 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 검색 상자에 `common data service`를 입력합니다. 예를 들어 트리거 목록에서 **레코드가 만들어지는 경우**의 트리거를 선택합니다.

   !["레코드를 만들 때" 트리거를 선택 합니다.](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. 메시지가 표시 되 면 Common Data Service에 로그인 합니다.

1. 트리거에서 새 "잠재 고객" 레코드를 모니터링 하려는 환경에 대 한 정보를 제공 합니다. 예를 들면 다음과 같습니다.

   ![모니터링할 환경에 대 한 트리거 정보](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **환경** | 예 | 모니터링할 환경 (예: "Fabrikam Sales Production")입니다. 자세한 내용은 [전원 플랫폼-환경 개요](https://docs.microsoft.com/power-platform/admin/environments-overview)를 참조 하세요. |
   | **엔터티 이름** | 예 | 모니터링할 엔터티입니다 (예: "잠재 고객"). |
   | **범위** | 예 | 새 레코드를 만든 원본 (예: 조직 내 사용자 또는 사업부의 사용자)입니다. 이 예에서는 "비즈니스 단위"를 사용 합니다. |
   ||||

## <a name="add-common-data-service-action"></a>Common Data Service 작업 추가

이제 새 "잠재 고객" 레코드에 대 한 작업 레코드를 만드는 Common Data Service 작업을 추가 합니다.

1. 레코드를 **만들 때** 트리거에서 **새 단계**를 선택 합니다.

1. 검색 상자에 `common data service`를 입력합니다. 작업 목록에서 **새 레코드 만들기** 작업을 선택합니다.

   !["새 레코드 만들기" 작업을 선택 합니다.](./media/connect-common-data-service/select-create-new-record-action.png)

1. 작업에서 새 작업 레코드를 만들려는 환경에 대 한 정보를 제공 합니다. 사용할 수 있는 경우이 작업에 대해 선택한 엔터티를 기준으로 다른 속성도 표시 됩니다. 예를 들면 다음과 같습니다.

   ![레코드를 만들 환경에 대 한 작업 정보](./media/connect-common-data-service/create-new-record-action-details.png)

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **조직 이름** | 예 | 레코드를 만들려는 환경 (트리거에서 동일한 환경이 아니어도 되는 환경)이 예에서는 "Fabrikam Sales Production"입니다. |
   | **엔터티 이름** | 예 | 레코드를 만들려는 엔터티, 예를 들어 "작업" |
   | **Subject** | 예,이 예제에서 선택한 엔터티를 기반으로 합니다. | 이 작업의 목표에 대 한 간단한 설명입니다. |
   ||||

   1. **Subject** 속성의 경우 뒤에 공백을 사용 하 여이 텍스트를 입력 합니다.

      `Follow up with new lead:`

   1. 동적 콘텐츠 목록이 계속 표시 되도록 **제목** 상자 내에 포인터를 유지 합니다.
   
   1. 목록에서 **레코드를 만들 때** 섹션에서 작업 레코드에 포함할 트리거 출력을 선택 합니다. 예를 들면 다음과 같습니다.

      ![작업 레코드에서 사용할 트리거 출력 선택](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | 트리거 출력 | Description |
      |----------------|-------------|
      | **이름** | 작업 레코드에서 기본 연락처로 사용할 잠재 고객 레코드의 첫 번째 이름입니다. |
      | **성** | 작업 레코드에서 기본 연락처로 사용할 잠재 고객 레코드의 성입니다. |
      | **설명** | 전자 메일 주소 및 회사 전화 번호와 같은 작업 레코드에 포함할 기타 출력 |
      |||

   완료 되 면 작업은 다음 예제와 같이 보일 수 있습니다.

   !["새 레코드 만들기" 작업 완료](./media/connect-common-data-service/finished-create-record-action-details.png)

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

1. 논리 앱을 수동으로 시작 하려면 디자이너 도구 모음에서 **실행**을 선택 합니다. 논리 앱을 테스트 하려면 새 "잠재 고객" 레코드를 만듭니다.

## <a name="trigger-only-on-updated-attributes"></a>업데이트 된 특성에 대해서만 트리거

레코드가 업데이트 될 때 실행 되는 트리거의 경우 (예: **레코드가 업데이트 되는 경우** ), 지정 된 특성이 업데이트 될 때만 논리 앱이 실행 되도록 필터 특성을 사용할 수 있습니다. 이 기능은 불필요 한 논리 앱 실행을 방지 하는 데 도움이 됩니다.

1. 트리거의 **새 매개 변수 추가** 목록에서 **특성 필터**를 선택 합니다.

   !["특성 필터" 속성 추가](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. 각 **특성 필터 항목**에 대해 다음과 같이 업데이트를 모니터링 하려는 특성을 선택 합니다.

   !["특성 필터" 속성 추가](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>필터를 기준으로 레코드 나열

레코드 **나열** 작업과 같이 레코드를 반환 하는 작업의 경우 지정 된 필터를 기반으로 레코드를 반환 하는 ODATA 쿼리를 사용할 수 있습니다. 예를 들어 작업에 활성 계정에 대 한 레코드만 나열 됩니다.

1. 동작에서 **새 매개 변수 추가** 목록을 열고 **쿼리 필터** 속성을 선택 합니다.

   !["필터 쿼리" 속성 추가](./media/connect-common-data-service/list-records-action-filter-query.png)

1. 이제 동작에 표시 되는 **필터 쿼리** 속성에서 다음 ODATA 필터 쿼리를 입력 합니다.`statuscode eq 1`

   ![레코드 필터링을 위한 ODATA 필터 쿼리 입력](./media/connect-common-data-service/list-records-action-filter-query-value.png)

시스템 쿼리 옵션에 `$filter` 대 한 자세한 내용은 [Common Data Service 필터 결과](https://docs.microsoft.com/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results)를 참조 하세요.

## <a name="list-records-based-on-an-order"></a>주문을 기준으로 레코드 나열

레코드 **나열** 작업과 같이 레코드를 반환 하는 작업의 경우 지정 된 순서로 레코드를 반환 하는 ODATA 쿼리를 사용할 수 있습니다 .이 쿼리는 작업이 반환 하는 레코드에 따라 달라 집니다. 예를 들어 계정 이름을 기준으로 레코드를 나열 하는 작업을 수행할 수 있습니다.

1. 동작에서 **새 매개 변수 추가** 목록을 열고 **Order by** 속성을 선택 합니다.

   !["Order by" 속성 추가](./media/connect-common-data-service/list-records-action-order-by.png)

1. 이제 동작에 표시 되는 **Order by** 속성에서 다음 ODATA 필터 쿼리를 입력 합니다.`name`

   ![레코드 순서 지정을 위한 ODATA 필터 쿼리 입력](./media/connect-common-data-service/list-records-action-order-by-value.png)

시스템 쿼리 옵션에 `$orderby` 대 한 자세한 내용은 [Common Data Service 주문 결과](https://docs.microsoft.com/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results)를 참조 하세요.

## <a name="field-data-types"></a>필드 데이터 형식

수동으로 값을 입력 하거나 트리거 또는 동작의 필드에 대 한 동적 콘텐츠 목록에서 값을 선택 하는지 여부에 관계 없이 값의 데이터 형식은 필드의 필수 데이터 형식과 일치 해야 합니다.

이 표에서는 일부 필드 형식 및 해당 필드의 값에 필요한 데이터 형식에 대해 설명 합니다.

| 필드 | 데이터 형식 | Description |
|-------|-----------|-------------|
| 텍스트 필드 | 한 줄의 텍스트 | 에는 다음 속성과 같이 텍스트 데이터 형식의 텍스트 한 줄 또는 동적 콘텐츠가 필요 합니다. <p><p>- **한** <br>- **범주** |
| 정수 필드 | 정수 | 에는 정수 데이터 형식의 정수 또는 동적 콘텐츠 (예: 다음 속성)가 필요 합니다. <p><p>- **완료율** <br>- **작업** |
| 날짜 필드 | 날짜 및 시간 | 에는 MM/DD/YYY 형식의 날짜 또는 날짜 데이터 형식의 동적 콘텐츠 (예: 다음 속성)가 필요 합니다. <p><p>- **만든 시기** <br>- **시작 날짜** <br>- **실제 시작** <br>- **실제 끝** <br>- **기한** |
| 다른 엔터티 레코드를 참조 하는 필드 | 기본 키 | 에는 GUID와 같은 레코드 ID와 조회 유형이 모두 필요 합니다. 즉, 동적 콘텐츠 목록의 값은 작동 하지 않습니다. 예를 들어 다음과 같은 속성이 있습니다. <p><p>- **Owner**: 유효한 사용자 id 또는 팀 레코드 id 여야 합니다. <br>- **소유자 유형**: 또는 `systemusers` `teams`와 같은 조회 유형 이어야 합니다. <p><p>- **관련**항목: 계정 id 또는 연락처 레코드 id와 같은 유효한 레코드 id 여야 합니다. <br>- **형식에 대**한 자세한 내용은 각각 `accounts` 또는 `contacts`같은 조회 형식 이어야 합니다. <p><p>- **Customer**: 계정 id 또는 연락처 레코드 id와 같은 유효한 레코드 id 여야 합니다. <br>- **고객 유형**: 또는 `accounts` `contacts`와 같은 조회 유형 이어야 합니다. |
||||

이 예에서는 **새 레코드 만들기** 작업을 통해 다른 엔터티 레코드, 특히 사용자 레코드 및 계정 레코드와 연결 된 새 "작업" 레코드를 만드는 방법을 보여 줍니다. 작업은 관련 속성에 대해 예상 되는 데이터 형식과 일치 하는 값을 사용 하 여 해당 엔터티 레코드에 대 한 Id 및 조회 유형을 지정 합니다.

* 사용자 ID를 지정 하는 **owner** 속성 및 `systemusers` 조회 유형을 지정 하는 **owner type** 속성에 따라 동작은 새 "Tasks" 레코드를 특정 사용자와 연결 합니다.

* 레코드 ID를 지정 하는 **관련** 속성 및 `accounts` 조회 유형을 지정 하는 **관련 유형** 속성에 따라 동작은 새 "작업" 레코드를 특정 계정과 연결 합니다.

![Id 및 조회 유형과 연결 된 "작업" 레코드 만들기](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 설명 (예: 트리거, 작업, 제한 및 기타 세부 정보)을 기반으로 하는 기술 정보는 [커넥터의 참조 페이지](https://docs.microsoft.com/connectors/commondataservice/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에 대 한 다른 커넥터](../connectors/apis-list.md) 에 대해 알아보기
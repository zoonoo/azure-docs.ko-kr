---
title: Common Data Service(Microsoft Dataverse)에 연결
description: Azure Logic Apps를 사용하여 Common Data Service(Microsoft Dataverse) 레코드 만들기 및 관리
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 02/11/2021
tags: connectors
ms.openlocfilehash: bec3416195358121b85eb61679ab39647e664a9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382357"
---
# <a name="create-and-manage-records-in-common-data-service-microsoft-dataverse-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Common Data Service(Microsoft Dataverse)에서 레코드 만들기 및 관리

> [!NOTE]
> 2020년 11월에 Common Data Service는 Microsoft Dataverse로 이름이 변경되었습니다.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Common Data Service 커넥터](/connectors/commondataservice/)를 사용하여 [Common Data Service(이제 Microsoft Dataverse)](/powerapps/maker/common-data-service/data-platform-intro) 데이터베이스에서 레코드를 관리하는 자동화된 워크플로를 만들 수 있습니다. 이러한 워크플로는 레코드를 만들고 레코드를 업데이트 등의 작업을 수행할 수 있습니다. 또한 Dataverse 데이터베이스에서 정보를 가져오고 논리 앱에서 사용할 다른 작업에 출력을 사용할 수 있도록 설정할 수 있습니다. 예를 들어 Dataverse 데이터베이스에서 레코드가 업데이트되는 경우 Office 365 Outlook 커넥터를 사용하여 이메일을 보낼 수 있습니다.

이 문서에서는 새 잠재 고객 레코드가 만들어질 때마다 작업 레코드를 생성하는 논리 앱을 빌드하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 조직에서 비즈니스 데이터 및 Common Data Service 데이터베이스를 저장, 관리, 공유하는 공간에 해당하는 [Common Data Service 환경](/power-platform/admin/environments-overview). 이러한 응용 프로그램은 Azure AD Graph API를 사용할 수 있습니다. 자세한 내용은 다음 리소스를 참조하세요.<p>

  * [Learn: Common Data Service 시작](/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Power Platform - 환경 개요](/power-platform/admin/environments-overview)

* [논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 대한 기본 지식 및 Dataverse 데이터베이스의 레코드에 액세스하려는 논리 앱. Common Data Service 트리거를 통해 논리 앱을 시작하려면 빈 논리 앱이 필요합니다. Azure Logic Apps를 처음 사용하는 경우 [빠른 시작: Azure Logic Apps를 사용하여 첫 번째 워크플로 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

## <a name="add-common-data-service-trigger"></a>Common Data Service 트리거 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

이 예제에서는 새 레코드가 만들어지면 실행되는 Common Data Service 트리거를 추가합니다.

1. [Azure Portal](https://portal.azure.com)에서 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 검색 상자에 `common data service`를 입력합니다. 예를 들어 트리거 목록에서 **레코드가 만들어지는 경우** 의 트리거를 선택합니다.

   !['레코드가 만들어지는 경우' 트리거를 선택합니다.](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. 메시지가 표시되면 Common Data Service에 로그인합니다.

1. 트리거에서 새 '잠재 고객' 레코드를 모니터링하려는 환경에 대한 정보를 제공합니다. 예를 들면 다음과 같습니다.

   ![모니터링할 환경에 대한 트리거 정보](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **환경** | 예 | 모니터링할 환경, 예: 'Fabrikam Sales Production'. 자세한 내용은 [Power Platform - 환경 개요](/power-platform/admin/environments-overview)를 참조하세요. |
   | **엔터티 이름** | 예 | 모니터링할 엔터티, 예: '잠재 고객' |
   | **범위** | 예 | 새 레코드를 만든 소스, 예: 사업부 내 사용자 또는 조직 내 사용자. 이 예제에서는 '사업부'를 사용합니다. |
   ||||

## <a name="add-common-data-service-action"></a>Common Data Service 작업 추가

이제 새 '잠재 고객' 레코드에 대한 작업 레코드를 만드는 Common Data Service 작업을 추가합니다.

1. **레코드가 만들어지는 경우** 트리거 아래에서 **새 단계** 를 선택합니다.

1. 검색 상자에 `common data service`를 입력합니다. 작업 목록에서 **새 레코드 만들기** 작업을 선택합니다.

   !['새 레코드 만들기' 작업을 선택합니다.](./media/connect-common-data-service/select-create-new-record-action.png)

1. 작업에서 새 작업 레코드를 만들려는 환경에 대한 정보를 제공합니다. 사용할 수 있는 경우 이 작업에 대해 선택한 엔터티를 기준으로 다른 속성도 표시됩니다. 예를 들면 다음과 같습니다.

   ![레코드를 만들 환경에 대한 작업 정보](./media/connect-common-data-service/create-new-record-action-details.png)

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **조직 이름** | 예 | 레코드를 만들려는 환경은 트리거와 동일한 환경일 필요는 없지만 이 예제에서는 'Fabrikam Sales Production'입니다. |
   | **엔터티 이름** | 예 | 레코드를 만들려는 엔터티, 예를 들어 "작업" |
   | **제목** | 예(이 예제에서 선택한 엔터티에 따라서) | 이 작업의 목표에 대한 간단한 설명입니다. |
   ||||

   1. **제목** 속성에 다음 텍스트를 입력하고 후행 공백을 하나 둡니다.

      `Follow up with new lead:`

   1. 동적 콘텐츠 목록이 계속 보이도록 포인터를 **제목** 상자 안에 계속 둡니다.
   
   1. 목록의 **레코드가 만들어지는 경우** 섹션에서 작업 레코드에 포함할 트리거 출력을 선택합니다. 예를 들면 다음과 같습니다.

      ![작업 레코드에서 사용할 트리거 출력 선택](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | 트리거 출력 | Description |
      |----------------|-------------|
      | **First Name** | 작업 레코드의 기본 연락처로 사용할 잠재 고객 레코드에 포함된 이름 |
      | **Last Name** | 작업 레코드의 기본 연락처로 사용할 잠재 고객 레코드에 포함된 성 |
      | **설명** | 이메일 주소 및 회사 전화 번호와 같은 작업 레코드에 포함할 기타 출력 |
      |||

   완료되면 작업은 다음 예제와 같습니다.

   ![완료된 '새 레코드 만들기' 작업](./media/connect-common-data-service/finished-create-record-action-details.png)

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 논리 앱을 수동으로 시작하려면 디자이너 도구 모음에서 **실행** 을 선택합니다. 논리 앱을 테스트하려면 새 '잠재 고객' 레코드를 만듭니다.

## <a name="trigger-only-on-updated-attributes"></a>업데이트된 특성에 대해서만 트리거

레코드가 업데이트될 때 실행되는 트리거의 경우(예: **레코드가 업데이트되는 경우**), 지정된 특성이 업데이트될 때만 논리 앱이 실행되도록 필터 특성을 사용할 수 있습니다. 이 기능은 불필요한 논리 앱 실행을 방지하는 데 도움이 됩니다.

1. 트리거의 **새 매개 변수 추가** 목록에서 **특성 필터** 를 선택합니다.

   !['레코드가 업데이트되는 경우' 작업에서 '새 매개 변수 추가' 목록이 열리고 '특성 필터' 속성이 선택된 모습을 보여 주는 스크린샷](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. 각 **특성 필터 항목** 에 대해 다음과 같이 업데이트를 모니터링하려는 특성을 선택합니다.

   !['특성 필터' 속성 추가](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>필터를 기준으로 레코드 나열

**레코드 나열** 작업과 같이 레코드를 반환하는 작업의 경우 지정된 필터를 기반으로 레코드를 반환하는 ODATA 쿼리를 사용할 수 있습니다. 예를 들어 작업이 활성 계정에 대한 레코드만 나열하도록 할 수 있습니다.

1. 작업에서 **새 매개 변수 추가** 목록을 열고 **필터 쿼리** 속성을 선택합니다.

   !['필터 쿼리' 속성 추가](./media/connect-common-data-service/list-records-action-filter-query.png)

1. 이제 표시되는 **필터 쿼리** 속성에서 다음 ODATA 필터 쿼리를 입력합니다. `statuscode eq 1`

   ![레코드 필터링을 위한 ODATA 필터 쿼리 입력](./media/connect-common-data-service/list-records-action-filter-query-value.png)

`$filter` 시스템 쿼리 옵션에 대한 자세한 내용은 [Common Data Service - 결과 필터링](/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results)을 참조하세요.

## <a name="list-records-based-on-an-order"></a>순서를 기준으로 레코드 나열

**레코드 나열** 작업과 같이 레코드를 반환하는 작업의 경우 지정된 순서로 레코드를 반환하는 ODATA 쿼리를 사용할 수 있습니다. 이 쿼리는 작업이 반환하는 레코드에 따라 달라집니다. 예를 들어 작업이 계정 이름을 기준으로 레코드를 나열하도록 할 수 있습니다.

1. 작업에서 **새 매개 변수 추가** 목록을 열고 **정렬 기준** 속성을 선택합니다.

   !['정렬 기준' 속성 추가](./media/connect-common-data-service/list-records-action-order-by.png)

1. 이제 표시되는 **정렬 기준** 속성에서 다음 ODATA 필터 쿼리를 입력합니다. `name`

   ![레코드 순서 지정을 위한 ODATA 필터 쿼리 입력](./media/connect-common-data-service/list-records-action-order-by-value.png)

`$orderby` 시스템 쿼리 옵션에 대한 자세한 내용은 [Common Data Service - 결과 정렬](/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results)을 참조하세요.

## <a name="field-data-types"></a>필드 데이터 형식

트리거 또는 작업의 필드에 수동으로 값을 입력하는지 또는 동적 콘텐츠 목록에서 값을 선택하는지 관계없이 값의 데이터 형식은 필드의 필수 데이터 형식과 일치해야 합니다.

다음 표에서는 일부 필드 형식 및 해당 필드의 값에 필요한 데이터 형식에 대해 설명합니다.

| 필드 | 데이터 형식 | Description |
|-------|-----------|-------------|
| 텍스트 필드 | 한 줄의 텍스트 | 텍스트 데이터 형식의 한 줄 텍스트 또는 동적 콘텐츠가 필요합니다. 예를 들어 다음과 같은 속성이 있습니다. <p><p>- **설명** <br>- **범주** |
| 정수 필드 | 정수 | 정수 데이터 형식의 정수 또는 동적 콘텐츠가 필요합니다. 예를 들어 다음과 같은 속성이 있습니다. <p><p>- **완료율** <br>- **기간** |
| 날짜 필드 | 날짜 및 시간 | MM/DD/YYY 형식의 날짜 또는 날짜 데이터 형식의 동적 콘텐츠가 필요합니다. 예를 들어 다음과 같은 속성이 있습니다. <p><p>- **만든 날짜** <br>- **시작 날짜** <br>- **실제 시작** <br>- **실제 종료** <br>- **기한** |
| 다른 엔터티 레코드를 참조하는 필드 | 기본 키 | 다음 GUID와 같은 레코드 ID와 조회 유형이 모두 필요합니다. 즉, 동적 콘텐츠 목록의 값은 유효하지 않습니다. 예를 들어 다음과 같은 속성이 있습니다. <p><p>- **소유자**: 유효한 사용자 ID 또는 팀 레코드 ID여야 합니다. <br>- **소유자 유형**: 각각 `systemusers` 또는 `teams` 같은 조회 유형이어야 합니다. <p><p>- **관련 항목**: 계정 ID 또는 연락처 레코드 ID 등의 유효한 레코드 ID여야 합니다. <br>- **관련 항목 유형**: 각각 `accounts` 또는 `contacts` 같은 조회 유형이어야 합니다. <p><p>- **고객**: 계정 ID 또는 연락처 레코드 ID 등의 유효한 레코드 ID여야 합니다. <br>- **고객 유형**: 각각 `accounts` 또는 `contacts` 같은 조회 유형이어야 합니다. |
||||

이 예제에서는 **새 레코드 만들기** 작업을 통해 다른 엔터티 레코드, 특히 사용자 레코드 및 계정 레코드와 연결된 새 '작업' 레코드를 만드는 방법을 보여 줍니다. 작업은 관련 속성에 필요한 데이터 형식과 일치하는 값을 사용하여 해당 엔터티 레코드에 대한 ID 및 조회 유형을 지정합니다.

* 사용자 ID를 지정하는 **소유자** 속성과 `systemusers` 조회 유형을 지정하는 **소유자 유형** 속성에 따라, 작업은 새 '작업' 레코드를 특정 사용자와 연결합니다.

* 레코드 ID를 지정하는 **관련 항목** 속성과 `accounts` 조회 유형을 지정하는 **관련 항목 유형** 속성에 따라, 작업은 새 '작업' 레코드를 특정 계정과 연결합니다.

![ID 및 조회 유형과 연결된 '작업' 레코드 만들기](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 설명(예: 트리거, 작업, 제한 및 기타 세부 정보)을 기반으로 하는 기술 정보는 [커넥터의 참조 페이지](/connectors/commondataservice/)를 참조하세요.

## <a name="troubleshooting-problems"></a>문제 해결

### <a name="calls-from-multiple-environments"></a>여러 환경에서 호출

두 커넥터 Common Data Service 및 Common Data Service(현재 환경) 모두 Microsoft Dataverse에서 `callbackregistrations` 엔터티를 사용하여 엔터티 변경 사항에 대한 알림을 받아야 하는 논리 앱 워크플로에 대한 정보를 저장합니다. Dataverse 조직을 복사하면 웹후크도 복사됩니다. 조직에 매핑된 워크플로를 비활성화하기 전에 조직을 복사하는 경우 복사된 웹후크가 동일한 논리 앱을 가리키고, 그러면 여러 조직에서 알림을 받게 됩니다.

원치 않는 알림을 중지하려면 다음 단계를 수행하여 해당 알림을 보내는 조직에서 콜백 등록을 삭제합니다.

1. 알림을 제거하려는 Dataverse 조직을 식별하여 해당 조직에 로그인합니다.

1. Chrome 브라우저에서 다음 단계를 수행하여 삭제할 콜백 등록을 찾습니다.

   1. `callbackregistrations` 엔터티 내부의 데이터를 볼 수 있도록 다음 OData URI에서 모든 콜백 등록에 대한 제네릭 목록을 검토합니다.

      `https://{organization-name}.crm{instance-number}.dynamics.com/api/data/v9.0/callbackregistrations`:

      > [!NOTE]
      > 반환된 값이 없는 경우 이 엔터티 유형을 볼 수 있는 권한이 없거나 올바른 조직에 로그인하지 않은 것일 수 있습니다.

   1. 트리거하는 엔터티의 논리적 이름과 `entityname` 논리 앱 워크플로(메시지)와 일치하는 알림 이벤트를 필터링합니다. 각 이벤트 형식은 다음과 같이 메시지 정수로 매핑됩니다.

      | 이벤트 유형 | 메시지 정수 |
      |------------|-----------------|
      | 생성 | 1 |
      | 삭제 | 2 |
      | 업데이트 | 3 |
      | CreateOrUpdate | 4 |
      | CreateOrDelete | 5 |
      | UpdateOrDelete | 6 |
      | CreateOrUpdateOrDelete | 7 |
      |||

      이 예제에서는 예제 조직에 대해 다음 OData URI를 사용하여 `nov_validation`이라는 엔터티에 대한 `Create` 알림을 필터링하는 방법을 보여 줍니다.

      `https://fabrikam-preprod.crm1.dynamics.com/api/data/v9.0/callbackregistrations?$filter=entityname eq 'nov_validation' and message eq 1`

      ![주소 표시줄에 OData URI가 표시된 브라우저 창을 보여 주는 스크린샷](./media/connect-common-data-service/find-callback-registrations.png)

      > [!TIP]
      > 동일한 엔터티 또는 이벤트에 대한 여러 트리거가 있는 경우 `createdon` 및 `_owninguser_value` 특성과 같은 추가 필터를 사용하여 목록을 필터링할 수 있습니다. 소유자 사용자의 이름은 `/api/data/v9.0/systemusers({id})` 아래에 나타납니다.

   1. 삭제할 콜백 등록의 ID를 찾은 후 다음 단계를 수행합니다.
   
      1. Chrome 브라우저에서 Chrome 개발자 도구를 엽니다(키보드: F12).

      1. 창의 맨 위에서 **콘솔** 탭을 선택합니다.

      1. 명령줄 프롬프트에서 다음 명령을 입력합니다. 이 명령은 지정된 콜백 등록을 삭제하는 요청을 보냅니다.

         `fetch('http://{organization-name}.crm{instance-number}.dynamics.com/api/data/v9.0/callbackregistrations({ID-to-delete})', { method: 'DELETE'})`

         > [!IMPORTANT]
         > 예를 들어 OData 또는 API 응답 페이지 자체와 같은 UCI(비 통합 클라이언트 인터페이스) 페이지에서 요청을 수행해야 합니다. 그렇지 않으면 app.js 파일의 논리가 이 작업을 방해할 수 있습니다.

   1. 콜백 등록이 더 이상 존재하지 않는지 확인하려면 콜백 등록 목록을 확인합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps용 다른 커넥터](../connectors/apis-list.md)에 대해 자세히 알아보기

---
title: Dynamics 365-Azure Logic Apps에 연결
description: Dynamics 365(온라인) REST API 및 Azure Logic Apps로 레코드 만들기 및 관리
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: b81efba0ce860bea5fd68dd99ce52980e6816b7e
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310577"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Dynamics 365 레코드 관리

Azure Logic Apps 및 Dynamics 365 커넥터를 사용하여 Dynamics 365의 사용자 레코드에 따라 자동화된 작업 및 워크플로를 만들 수 있습니다. 워크플로는 Dynamics 365 계정에서 레코드 만들기, 항목 업데이트, 레코드 반환 등을 할 수 있습니다. 논리 앱에 작업을 포함시켜 Dynamics 365에서 응답을 가져오고 출력을 다른 작업에 사용할 수 있게 할 수 있습니다. 예를 들어, Dynamics 365에서 항목이 업데이트되면 Office 365를 사용하여 이메일을 보낼 수 있습니다.

이 항목에서는 Dynamics 365에서 새 잠재 고객 레코드가 생성될 때마다 Dynamics 365에서 작업을 생성하는 논리 앱을 빌드하는 방법을 보여줍니다.
논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

* [Dynamics 365 계정](https://dynamics.microsoft.com)

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* Dynamics 365 계정에 액세스하려는 논리 앱입니다. Dynamics 365 트리거를 통해 논리 앱을 시작하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 필요합니다.

## <a name="add-dynamics-365-trigger"></a>Dynamics 365 트리거 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

첫째, 새 잠재 고객 레코드가 Dynamics 365에 표시되는 경우 발생되는 Dynamics 365 트리거를 추가합니다.

1. [Azure Portal](https://portal.azure.com)에서 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 검색 상자에 “Dynamics 365”를 필터로 입력합니다. 예를 들어 트리거 목록에서이 트리거를 선택 합니다. **레코드가 만들어질 때**

   ![트리거 선택](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Dynamics 365에 로그인하라는 메시지가 나타나면 지금 로그인합니다.

1. 다음과 같은 트리거 세부 정보를 제공합니다.

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **조직 이름** | 예 | 모니터링할 조직의 Dynamics 365 인스턴스의 이름, 예를 들어 "Contoso" |
   | **엔터티 이름** | 예 | 모니터링할 엔터티의 이름, 예를 들어 "리드" | 
   | **Frequency(빈도)** | 예 | 트리거와 관련된 업데이트를 확인할 때 간격으로 사용할 시간 단위 |
   | **간격** | 예 | 다음 확인 때까지 경과할 초, 분, 시간, 일, 주, 달 수 |
   ||| 

   ![트리거 세부 정보](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Dynamics 365 작업 추가

이제 새 잠재 고객 레코드에 대한 작업 레코드를 생성하는 Dynamics 365 작업을 추가합니다.

1. 트리거 아래에서 **새 단계**를 선택합니다.

1. 검색 상자에 “Dynamics 365”를 필터로 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **새 레코드 만들기**

   ![작업 선택](./media/connectors-create-api-crmonline/select-action.png)

1. 이러한 작업 세부 정보를 제공합니다.

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **조직 이름** | 예 | 레코드를 만들려는 경우, 반드시 트리거의 동일한 인스턴스는 아니지만, 이 예제에서는 "Contoso"인 Dynamics 365 인스턴스 |
   | **엔터티 이름** | 예 | 레코드를 만들려는 엔터티, 예를 들어 "작업" |
   | | |

   ![작업 세부 정보](./media/connectors-create-api-crmonline/action-details.png)

1. **제목** 상자가 사용자 작업에 표시되는 경우 동적 콘텐츠 목록이 표시되도록 **제목** 내부를 클릭합니다. 이 목록에서 새 잠재 고객 레코드와 연결된 작업 레코드에 포함될 필드 값을 선택합니다.

   | 필드 | 설명 |
   |-------|-------------|
   | **성** | 레코드의 기본 연락처로 사용될 잠재 고객의 성 |
   | **항목** | 레코드에서 잠재 고객에 대한 설명이 포함된 이름 |
   | | |

   ![작업 레코드 세부 정보](./media/connectors-create-api-crmonline/create-record-details.png)

1. 디자이너 도구 모음에서 논리 앱에 대해 **저장**을 선택합니다. 

1. 수동으로 논리 앱을 실행하려면 디자이너 도구 모음에서 **실행**을 선택합니다.

   ![논리 앱 실행](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. 이제 논리 앱의 워크플로를 트리거할 수 있도록 Dynamics 365의 잠재 고객 레코드를 만듭니다.

## <a name="add-filter-or-query"></a>쿼리 또는 필터 추가

Dynamics 365 작업에서 데이터를 필터링하는 방법을 지정하려면 해당 작업에서 **고급 옵션 표시**를 선택합니다. 그런 다음, 쿼리별 주문 또는 필터를 추가할 수 있습니다.
예를 들어 필터 쿼리를 사용하여 계정 이름으로 활성 계정만 가져오고 해당 레코드를 주문할 수 있습니다. 이 작업의 경우, 다음 단계를 따릅니다.

1. **필터 쿼리** 아래에 이 OData 필터 쿼리를 입력합니다. `statuscode eq 1`

2. 동적 콘텐츠 목록이 나타나면 **정렬 방식(Order By)** 아래에서 **계정 이름**을 선택합니다. 

   ![필터 및 순서 지정](./media/connectors-create-api-crmonline/advanced-options.png)

자세한 내용은 이러한 Dynamics 365 Customer Engagement Web API 시스템 쿼리 옵션을 참조하세요.

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>고급 옵션에 대한 모범 사례

작업 또는 트리거의 필드 값을 지정하면 수동으로 값을 입력하든 또는 동적 콘텐츠 목록에서 값을 선택하든, 값의 데이터 형식이 필드 형식과 일치해야 합니다.

이 표에서는 일부 필드 형식 및 해당 값에 대한 필수 데이터 형식에 대해 설명합니다.

| 필드 형식 | 필수 데이터 유형 | 설명 | 
|------------|--------------------|-------------|
| 텍스트 필드 | 한 줄의 텍스트 | 이러한 필드에는 한 줄의 텍스트 또는 텍스트 형식이 포함된 동적 콘텐츠가 필요합니다. <p><p>*예제에서는 필드*: **설명을** 고 **범주** | 
| 정수 필드 | 정수 | 일부 필드에는 정수 또는 정수 형식이 포함된 동적 콘텐츠가 필요합니다. <p><p>*예제에서는 필드*: **완료율** 고 **기간** | 
| 날짜 필드 | 날짜 및 시간 | 일부 필드에 mm/dd/yyyy 형식의 날짜 또는 날짜 형식 필드가 포함된 동적 콘텐츠가 필요합니다. <p><p>*예제에서는 필드*: **만든**, **시작 날짜**를 **실제 시작**를 **실제 종료**, 및 **기한** | 
| 레코드 ID와 조회 유형이 모두 필요한 필드 | 기본 키 | 다른 엔터티 레코드를 참조하는 일부 필드에는 레코드 ID와 조회 유형이 모두 필요합니다. | 
||||

이러한 필드 형식을 확장하면, 레코드 ID와 조회 유형이 모두 필요한 Dynamics 365 트리거 및 작업의 예제 필드는 다음과 같습니다. 즉, 동적 목록에서 선택한 값이 작동하지 않습니다.

| 필드 | 설명 |
|-------|-------------|
| **소유자** | 유효한 사용자 ID 또는 팀 레코드 ID여야 합니다. |
| **소유자 유형** | **시스템 사용자**이거나 **팀**이어야 합니다. |
| **관련 항목** | 계정 ID 또는 연락처 레코드 ID 등의 유효한 레코드 ID여야 합니다. |
| **관련 유형** | **계정** 또는 **연락처** 등의 조회 유형이어야 합니다. |
| **고객** | 계정 ID 또는 연락처 레코드 ID 등의 유효한 레코드 ID여야 합니다. |
| **고객 유형** | **계정** 또는 **연락처** 등의 조회 유형이어야 합니다. |
|||

이 예제에서는 **새 레코드 만들기**라는 작업이 새 작업 레코드를 만듭니다.

![레코드 ID 및 조회 유형을 사용한 작업 레코드 만들기](./media/connectors-create-api-crmonline/create-record-advanced.png)

이 작업은 **소유자** 필드의 레코드ID 및 **소유자 형식** 필드의 조회 유형에 따라 작업 레코드를 특정 사용자 ID 또는 팀 레코드 ID에 할당합니다.

![소유자 레코드 ID와 조회 유형](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

또한 이 작업은 **관련 항목** 필드에 추가된 레코드 ID 및 **관련 유형** 필드의 조회 유형과 연결된 계정 레코드를 추가합니다.

![관련 레코드 ID 및 조회 유형](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>레코드 ID 찾기

레코드 ID를 찾으려면 다음과 같은 단계를 따릅니다.

1. Dynamics 365에서 계정 레코드 등의 레코드를 엽니다.

2. 작업 도구 모음에서 이러한 단계 중 하나를 선택합니다.

   * **팝아웃**을 선택합니다. ![팝아웃 레코드](./media/connectors-create-api-crmonline/popout-record.png) 
   * 기본 이메일 프로그램에 전체 URL을 복사할 수 있도록 **이메일로 링크 보내기**를 선택합니다.

   `%7b` 및 `%7d` 인코딩 문자 간 URL에 레코드 ID가 표시됩니다.

   ![레코드 ID 찾기](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>실패한 문제 해결 실행

논리 앱에서 실패한 단계를 찾아 검토하려면 논리 앱의 실행 기록, 상태, 입력, 출력 등을 볼 수 있습니다.

1. Azure Portal의 논리 앱 주 메뉴에서 **개요**를 선택합니다. 논리 앱에 대한 모든 실행 상태를 보여주는 **실행 기록** 섹션에서 실패한 실행을 선택하면 자세한 내용을 확인할 수 있습니다.

   ![논리 앱 실행 상태](./media/connectors-create-api-crmonline/run-history.png)

1. 자세한 세부 정보를 확인할 수 있도록 실패한 단계를 확장합니다.

   ![실패한 단계 확장](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. 오류의 원인을 찾는 데 도움이 될 수 있는 입력 및 출력 같은 단계의 세부 정보를 검토합니다.

   ![실패한 단계 - 입력 및 출력](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

논리 앱 문제 해결에 대한 자세한 내용은 [논리 앱 오류 진단](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

## <a name="connector-reference"></a>커넥터 참조

트리거, 작업 및 커넥터의 OpenAPI에 설명 된 대로 제한 등의 기술 세부 정보에 대 한 (이전의 Swagger) 파일, 참조를 [커넥터의 참조 페이지](/connectors/dynamicscrmonline/)합니다.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.

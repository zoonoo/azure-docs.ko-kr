---
title: Azure Scheduler에서 Azure Logic Apps로 마이그레이션
description: 조만간 사용이 중지되는 Azure Scheduler의 작업을 Azure Logic Apps로 바꾸는 방법을 알아봅니다.
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 02/29/2020
ms.openlocfilehash: 0e9ca2c7b5d15fcc6be451bbe215c8b85d5309eb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368267"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Azure Scheduler 작업을 Azure Logic Apps로 마이그레이션

> [!IMPORTANT]
> [Azure Scheduler](#retire-date)는 조만간 사용 중지되고 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)로 대체됩니다. Scheduler에서 설정한 작업으로 계속 작업하려면 이 문서를 따라 가능한 한 빨리 Azure Logic Apps로 마이그레이션하세요. 
>
> Scheduler는 더 이상 Azure Portal에서 사용할 수 없지만 [REST API](/rest/api/scheduler) 및 [Azure Scheduler PowerShell cmdlet](scheduler-powershell-reference.md)은 현재 사용 가능하므로 작업 및 작업 컬렉션을 관리할 수 있습니다.

이 문서에서는 Azure Scheduler 대신 Azure Logic Apps를 사용하여 자동화된 워크플로를 만들어서 일회성 및 되풀이 작업을 예약하는 방법을 보여줍니다. Logic Apps를 사용하여 예약된 작업을 만들면 다음과 같은 이점이 있습니다.

* Azure Blob Storage, Azure Service Bus, Office 365 Outlook 및 SAP와 같은 수백 개의 서비스에서 비주얼 디자이너 및 [바로 사용할 수 있는 커넥터](../connectors/apis-list.md)를 사용하여 작업을 빌드합니다.

* 예약된 각 워크플로를 최고 수준의 Azure 리소스로 관리합니다. 각 논리 앱은 개별 Azure 리소스이므로 *작업 컬렉션* 개념에 대해 걱정할 필요가 없습니다.

* 단일 논리 앱을 사용하여 일회성 작업을 여러 개 실행합니다.

* 표준 시간대를 지원하고 DST(일광 절약 시간제)로 자동 조정되는 일정을 설정합니다.

자세한 내용은 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 참조하거나 빠른 시작에서 첫 번째 논리 앱을 만들어 봅니다. [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* HTTP 요청을 전송하여 논리 앱을 트리거하려면 [Postman 데스크톱 앱](https://www.getpostman.com/apps) 같은 도구를 사용합니다.

## <a name="migrate-by-using-a-script"></a>스크립트를 사용하여 마이그레이션

각 스케줄러 작업은 고유하므로 스케줄러 작업을 Azure Logic Apps로 마이그레이션하는 데 적합한 단일 크기 도구는 없습니다. 그러나 사용자 요구에 맞게 [이 스크립트를 편집](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration)할 수 있습니다.

## <a name="schedule-one-time-jobs"></a>일회성 작업 예약

단일 논리 앱을 만들어서 일회성 작업을 여러 개 실행할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 논리 앱 디자이너에서 빈 논리 앱을 만듭니다.

   기본 단계는 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

1. 검색 상자에 `when a http request`를 입력하여 요청 트리거를 찾습니다. 트리거 목록에서 다음 트리거를 선택합니다. **HTTP 요청을 수신하는 경우**.

   !["요청" 트리거 추가](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. 요청 트리거의 경우 필요에 따라 JSON 스키마를 제공할 수 있습니다. 이를 통해 Logic App Designer는 요청 트리거에 대한 인바운드 호출에 포함된 입력의 구조를 이해하고 워크플로 뒷부분에서 출력을 좀 더 쉽게 선택할 수 있습니다.

   **요청 본문 JSON 스키마** 상자에서 스키마를 입력합니다. 예를 들면 다음과 같습니다.

   ![요청 스키마](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   스키마는 없지만 JSON 형식의 샘플 페이로드가 있는 경우 해당 페이로드로 스키마를 생성할 수 있습니다.

   1. 요청 트리거에서 **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.

   1. 다음과 같이 **샘플 JSON 페이로드를 입력하거나 붙여넣기**에서 샘플 페이로드를 입력하고 **완료**를 선택합니다.

      ![샘플 페이로드](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

      ```json
      {
         "runat": "2012-08-04T00:00Z",
         "endpoint": "https://www.bing.com"
      }
      ```

1. 트리거 아래에서 **다음 단계**를 선택합니다.

1. 검색 상자에서 필터로 `delay until`을 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **다음 기간까지 지연**

   이 작업은 지정된 날짜 및 시간까지 논리 앱 워크플로를 일시 중지합니다.

   !["다음 기간까지 지연" 작업 추가](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. 논리 앱 워크플로를 시작할 타임스탬프를 입력합니다.

   **타임스탬프** 상자 내부를 클릭하면 필요에 따라 트리거에서 출력을 선택할 수 있는 동적 콘텐츠 목록이 나타납니다.

   !["다음 기간까지 지연" 세부 정보 입력](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. [수백 개의 바로 사용할 수 있는 커넥터](../connectors/apis-list.md)에서 선택하여 실행하려는 다른 작업을 추가합니다.

   예를 들어 URL로 요청을 전송하는 HTTP 작업 또는 Storage 큐, Service Bus 큐, Service Bus 토픽을 사용하는 작업을 포함할 수 있습니다.

   ![스크린샷 후 작업 후 POST 메서드를 사용 하는 H T T P 작업이 오는 지연 시간을 보여 줍니다.](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. 완료되면 논리 앱을 저장합니다.

   ![논리 앱 저장](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   논리 앱을 처음으로 저장하면 논리 앱의 요청 트리거에 대한 엔드포인트 URL이 **HTTP POST URL** 상자에 나타납니다. 논리 앱을 호출하고 논리 앱으로 입력을 보내 처리하려면 이 URL을 호출 대상으로 사용합니다.

   ![요청 트리거 엔드포인트 URL 저장](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. 나중에 논리 앱을 트리거하는 수동 요청을 보낼 수 있도록 이 엔드포인트 URL을 복사하여 저장합니다.

## <a name="start-a-one-time-job"></a>일회성 작업 시작

일회성 작업을 수동으로 실행하거나 트리거하려면 논리 앱의 요청 트리거에 대한 엔드포인트 URL에 호출을 보냅니다. 이 호출에서 전송할 입력 또는 페이로드를 지정합니다. 앞에서 스키마를 지정하여 입력 또는 페이로드에 대해 설명했을 수도 있습니다.

예를 들어 Postman 앱을 사용하는 경우 이 샘플과 비슷한 설정으로 POST 요청을 만든 후 **보내기**를 선택하여 요청을 만들 수 있습니다.

| 요청 메서드 | URL | 본문 | headers |
|----------------|-----|------|---------|
| **POST** | <*endpoint-URL*> | **원시** <p>**JSON(애플리케이션/json)** <p>**원시** 상자에 요청에서 보낼 페이로드를 입력합니다. <p>**참고**: 이 설정은 자동으로 **헤더** 값을 구성합니다. | **키**: 콘텐츠 형식 <br>**값**: 애플리케이션/json |
|||||

![수동으로 논리 앱을 트리거하는 요청 보내기](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

호출을 보낸 후 **본문** 탭의 **원시** 상자 아래에 논리 앱의 응답이 표시됩니다. 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> 나중에 작업을 취소하려면 **헤더** 탭을 선택합니다. 응답에서 **x-ms-workflow-run-id** 헤더 값을 찾아 복사합니다. 
>
> ![응답](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>일회성 작업 취소

Logic Apps에서 각 일회성 작업은 단일 논리 앱 실행 인스턴스로 실행됩니다. 일회성 작업을 취소하려면 Logic Apps REST API에서 [Workflow Runs - Cancel](/rest/api/logic/workflowruns/cancel)을 사용하면 됩니다. 트리거 호출을 보낼 때 [워크플로 실행 ID](#workflow-run-id)를 제공해야 합니다.

## <a name="schedule-recurring-jobs"></a>되풀이 작업 예약

1. [Azure Portal](https://portal.azure.com)의 논리 앱 디자이너에서 빈 논리 앱을 만듭니다.

   기본 단계는 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

1. 검색 상자에서 필터로 “되풀이”를 입력합니다. 트리거 목록에서 다음 트리거를 선택합니다. **되풀이**

   !["되풀이" 트리거 추가](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. 원하는 경우 고급 일정을 설정합니다.

   ![고급 일정](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   고급 일정 옵션에 대한 자세한 내용은 [Azure Logic Apps를 사용하여 되풀이 작업 및 워크플로를 만들고 실행](../connectors/connectors-native-recurrence.md)을 참조하세요.

1. [수백 개의 바로 사용할 수 있는 커넥터](../connectors/apis-list.md)에서 선택하여 원하는 다른 작업을 추가합니다. 트리거 아래에서 **다음 단계**를 선택합니다. 원하는 작업을 찾아 선택합니다.

   예를 들어 URL로 요청을 전송하는 HTTP 작업 또는 Storage 큐, Service Bus 큐, Service Bus 토픽을 사용하는 작업을 포함할 수 있습니다.

   ![스크린샷-POST 메서드를 사용 하는 H T T P 동작을 보여 줍니다.](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. 완료되면 논리 앱을 저장합니다.

   ![논리 앱 저장](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>고급 설정

작업을 사용자 지정하는 다른 방법이 있습니다.

### <a name="retry-policy"></a>다시 시도 정책

일시적인 오류가 발생할 때 논리 앱에서 작업 재실행을 시도하는 방법을 제어하려면 다음과 같이 각 작업의 설정에서 [재시도 정책](../logic-apps/logic-apps-exception-handling.md#retry-policies)을 설정하면 됩니다.

1. 작업의 줄임표( **...** ) 메뉴를 열고 **설정**을 선택합니다.

   ![작업 설정 열기](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. 원하는 재시도 정책을 선택합니다. 각 정책에 대한 자세한 내용은 [재시도 정책](../logic-apps/logic-apps-exception-handling.md#retry-policies)을 참조하세요.

   ![재시도 정책 선택](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>예외 및 오류 처리

Azure Scheduler에서 기본 작업이 실행에 실패하는 경우 오류 조건을 해결하는 대체 작업을 실행할 수 있습니다. Azure Logic Apps에서도 동일한 작업을 수행할 수 있습니다.

1. 논리 앱 디자이너에서, 처리하려는 작업의 위쪽에서 포인터를 단계 사이의 화살표 위로 이동하고 **병렬 분기 추가**를 선택합니다.

   ![병렬 분기 추가](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. 대체 작업으로 실행할 작업을 찾아서 선택합니다.

   ![병렬 작업 추가](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. 대체 작업에서 줄임표( **...** ) 메뉴를 열고 **실행 조건 구성**을 선택합니다.

   ![실행 조건 구성](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. **성공** 속성의 확인란을 선택 취소합니다. **실패함**, **건너뜀** 및 **시간이 초과됨** 속성을 선택합니다.

   !["실행 조건" 속성 설정](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. 완료되면 **완료**를 선택합니다.

예외 처리에 대한 자세한 내용은 [오류 및 예외 처리 - RunAfter 속성](../logic-apps/logic-apps-exception-handling.md#control-run-after-behavior)을 참조하세요.

## <a name="faq"></a>FAQ

<a name="retire-date"></a>

**Q**: Azure Scheduler는 언제 사용 중지되나요? <br>
**A**: Azure Scheduler는 2019년 12월 31일에 완전히 사용 중지될 예정입니다. 이 날짜 이전에 수행해야 할 중요한 단계와 자세한 타임라인은 [Scheduler의 사용 중지 날짜를 2019년 12월 31일로 연장](https://azure.microsoft.com/updates/extending-retirement-date-of-scheduler/)을 참조하세요. 일반 업데이트는 [Azure 업데이트 - Scheduler](https://azure.microsoft.com/updates/?product=scheduler)를 참조하세요.

**Q**: 서비스가 중지된 후 작업 컬렉션과 작업은 어떻게 되나요? <br>
**A**: 모든 Scheduler 작업 컬렉션과 작업의 실행이 중지되고 시스템에서 삭제됩니다.

**Q**: Scheduler 작업을 Logic Apps로 마이그레이션하기 전에 다른 작업을 백업하거나 수행해야 하나요? <br>
**A**: 작업을 백업하는 것은 언제나 가장 좋은 모범 사례입니다. 이전에 만든 논리 앱이 예상대로 실행되는지 확인한 후 Scheduler 작업을 삭제하거나 사용하지 않도록 설정하세요.

**Q**: 작업을 Scheduler에서 Logic Apps로 쉽게 마이그레이션할 수 있는 도구가 있나요? <br>
**A**: 각 Scheduler 작업은 고유하기 때문에 만능 도구는 존재하지 않습니다. 그러나 필요에 따라 [이 스크립트를 편집하여 Azure Scheduler 작업을 Azure Logic Apps로 마이그레이션](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration)할 수 있습니다.

**Q**: Scheduler 작업 마이그레이션에 대한 지원은 어디서 받을 수 있나요? <br>
**A**: 다음과 같은 방법으로 지원을 받을 수 있습니다.

**Azure Portal**

Azure 구독에 유료 지원 플랜이 있는 경우 Azure Portal에서 기술 지원 요청을 만들 수 있습니다. 없는 경우 다른 지원 옵션을 선택할 수 있습니다.

1. [Azure Portal](https://portal.azure.com) 주 메뉴에서 **도움말 + 지원**을 선택합니다.

1. **지원** 메뉴에서 **새 지원 요청**을 선택합니다. 요청에 대해 다음 정보를 제공합니다.

   | 속성 | 값 |
   |---------|-------|
   | **문제 유형** | **기술** |
   | **구독** | <*your-Azure-subscription*> |
   | **서비스** | **모니터링 및 관리** 아래에서 **Scheduler**를 선택합니다. **Scheduler**를 찾을 수 없는 경우 먼저 **모든 서비스**를 선택합니다. |
   ||| 

1. 원하는 지원 옵션을 선택합니다. 유료 지원 계획이 있는 경우 **다음**을 선택합니다.

**커뮤니티**

* [Azure Logic Apps에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-logic-apps.html)
* [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps를 사용하여 정기적으로 실행되는 작업 및 워크플로 만들기](../connectors/connectors-native-recurrence.md)
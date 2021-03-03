---
title: Azure Logic Apps에 대 한 비용 관리 계획
description: 에서 비용 분석을 사용 하 여 Azure Logic Apps에 대 한 비용을 계획 하 고 관리 하는 방법을 알아봅니다 Azure Portal
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 44351497ed58c8d49404c094f6800b52186edabb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709634"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Azure Logic Apps에 대 한 비용 계획 및 관리

이 문서는 Azure Logic Apps에 대 한 비용을 계획 하 고 관리 하는 데 도움이 됩니다. 이 서비스를 사용 하 여 리소스를 만들거나 추가 하기 전에 Azure 가격 계산기를 사용 하 여 비용을 예측 합니다. Logic Apps 리소스 사용을 시작한 후에는 [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 사용 하 여 예산을 설정 하 고 비용을 모니터링할 수 있습니다. 작업을 수행할 수 있는 영역을 식별 하기 위해 예상 비용을 검토 하 고 지출 추세를 모니터링할 수도 있습니다.

Logic Apps에 대 한 비용은 Azure 청구서의 월별 비용의 일부일 뿐입니다. 이 문서에서는 Logic Apps에 대 한 비용을 예측 하 고 관리 하는 방법을 설명 하지만 모든 타사 서비스를 포함 하 여 Azure 구독에 사용 되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구 됩니다. Logic Apps에 대 한 비용 관리에 익숙해 졌으 면 구독에 사용 되는 모든 Azure 서비스에 대 한 비용을 관리 하는 비슷한 방법을 적용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 는 대부분의 Azure 계정 유형을 지원 합니다. 지원 되는 모든 계정 유형을 보려면 [데이터 Cost Management 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조 하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다.

Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>청구 모델 이해

Azure Logic Apps는 Azure 인프라에서 실행 되며 새 리소스를 배포할 때 [비용이](https://azure.microsoft.com/pricing/details/logic-apps/) 발생 합니다. [관련 Azure 리소스와 함께 Logic Apps 서비스에 대 한 청구 모델](logic-apps-pricing.md)을 이해 하 고 배포 된 리소스를 변경할 때 이러한 종속성으로 인 한 비용을 관리 해야 합니다.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>일반적으로 Azure Logic Apps으로 계산 되는 비용

Logic Apps 서비스는 사용자가 만들고 사용 하는 리소스에 따라 다른 가격 책정 모델을 적용 합니다.

* 다중 테 넌 트 Logic Apps 서비스에서 만들고 실행 하는 논리 앱 리소스는 [소비 가격 책정 모델](../logic-apps/logic-apps-pricing.md#consumption-pricing)을 사용 합니다.

* [ISE (통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 에서 만들고 실행 하는 논리 앱 리소스는 [고정 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 사용 합니다.

다음은 논리 앱에서 사용 하기 위해 만들 때 비용이 발생 하는 기타 리소스입니다.

* [통합 계정은](../logic-apps/logic-apps-pricing.md#integration-accounts) 사용자가 만들고 B2B 통합을 빌드하기 위한 논리 앱에 연결 하는 별도의 리소스입니다. 통합 계정은 사용 하는 통합 계정 유형 또는 *계층* 을 기반으로 하는 [고정 가격 책정 모델](../logic-apps/logic-apps-pricing.md#integration-accounts) 을 사용 합니다.

* [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) 는 가상 네트워크의 리소스에 직접 액세스 해야 하는 논리 앱에 대 한 배포 위치로 만드는 별도의 리소스입니다. ISEs는 생성 된 ISE SKU 및 기타 설정에 따라 비율이 [고정 된 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing) 을 사용 합니다.

* [사용자 지정 커넥터](../logic-apps/logic-apps-pricing.md#consumption-pricing) 는 논리 앱에서 사용할 미리 빌드된 커넥터가 없는 REST API에 대해 만드는 별도의 리소스입니다. 사용자 지정 커넥터 실행은 ISE에서 사용 하는 경우를 제외 하 고 [소비 가격 책정 모델](../logic-apps/logic-apps-pricing.md#consumption-pricing) 을 사용 합니다.

* 다중 테 넌 트 Logic Apps 서비스에서 [데이터 보존 및 저장소 사용량](../logic-apps/logic-apps-pricing.md#data-retention) 은 [고정 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 사용 하 여 비용을 계산 합니다. 예를 들어 실행 기록의 입력 및 출력은 백그라운드 저장소에 유지 됩니다 .이 저장소는 논리 앱에서 독립적으로 만들고 관리 하 고 액세스 하는 저장소 리소스와는 다릅니다.

  ISE에서 데이터 보존 및 저장소 소비는 비용이 발생 하지 않습니다.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>리소스 삭제 후 비용이 발생할 수 있습니다.

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

논리 앱을 삭제 한 후 Logic Apps 서비스는 새 워크플로 인스턴스를 만들거나 실행 하지 않습니다. 그러나 진행 중인 모든 실행 및 보류 중인 실행은 완료 될 때까지 계속 됩니다. 이러한 실행의 수에 따라이 프로세스에 다소 시간이 걸릴 수 있습니다. 자세한 내용은 [논리 앱 관리](manage-logic-apps-with-azure-portal.md#delete-logic-apps)를 참조 하세요.

논리 앱을 삭제 한 후 이러한 리소스가 있는 경우 이러한 리소스는 계속 존재 하며 삭제 될 때까지 비용이 계산 됩니다.

* Azure 함수 앱, event hubs, event grid 등과 같이 해당 리소스에 연결 하는 논리 앱과 독립적으로 만들고 관리 하는 azure 리소스

* 통합 계정

* Integration service environment (ISEs)

  ISE를 [삭제](ise-manage-integration-service-environment.md#delete-ise)하면 연결 된 Azure virtual network, 서브넷 및 기타 관련 리소스가 계속 존재 합니다. ISE를 삭제 한 후에는 가상 네트워크 또는 서브넷을 삭제 하기 전에 특정 시간을 기다려야 할 수 있습니다.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Azure Logic Apps에서 금액 크레딧 사용

EA 금액 약정 크레딧을 사용 하 여 Azure Logic Apps 요금을 지불할 수 있습니다. 그러나 Azure Marketplace에서 비롯 한 타사 제품 및 서비스에 대 한 요금을 지불 하기 위해 EA 현금 약정 크레딧을 사용할 수는 없습니다.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>비용 예측

Azure Logic Apps를 사용 하 여 리소스를 만들기 전에 [Azure 가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용 하 여 비용을 예측 합니다. 자세한 내용은 [Azure Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md)을 참조 하세요.

1. [Azure 가격 계산기 페이지](https://azure.microsoft.com/pricing/calculator/)의 왼쪽 메뉴에서 **Integration**  >  **Azure Logic Apps** 를 선택 합니다.

   !["Azure Logic Apps"가 선택 된 Azure 가격 계산기를 보여 주는 스크린샷](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Azure Logic Apps 가격 계산기를 볼 수 있을 때까지 페이지 아래로 스크롤합니다. Azure Logic Apps와 직접적으로 관련 된 Azure 리소스에 대 한 다양 한 섹션에서 사용 하려는 리소스의 수와 해당 리소스를 사용할 수 있는 간격의 수를 입력 합니다.

   이 스크린샷에서는 계산기를 사용 하 여 비용을 예측 하는 예를 보여 줍니다.

   ![Azure 가격 계산기의 예상 비용을 보여 주는 예제](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. 관련 된 새 리소스를 만들고 사용할 때 예상 되는 비용을 업데이트 하려면이 계산기로 돌아가서 해당 리소스를 업데이트 합니다.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>예산 및 경고 만들기

Azure 계정 또는 구독에 대 한 비용을 사전에 관리 하려면 [Azure Cost Management 및 청구](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 서비스 및 기능을 사용 하 여 [예산과](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 만들 수 있습니다.  Azure 구독 및 리소스 그룹에 대 한 예산과 경고가 생성 되므로 전체 비용 모니터링 전략의 일부로 유용 합니다.

예산 및 비용 임계값에 비해 지출에 따라 경고는 관련자에 게 변칙 및 과도 한 지출 위험에 대해 자동으로 알립니다. 모니터링에 더 많은 세분성이 필요한 경우 Azure에서 특정 리소스 또는 서비스에 대 한 필터를 사용 하는 예산을 만들 수도 있습니다. 필터를 통해 추가 비용을 절감할 수 있는 새 리소스를 실수로 만들지 않도록 할 수 있습니다. 필터 옵션에 대 한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조 하세요.

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>비용 모니터링

리소스 사용 단위 비용은 초, 분, 시간, 일 등의 시간 간격 또는 단위 사용 (예: 바이트, 메가바이트 등)에 따라 달라 집니다. 몇 가지 예로는 일, 현재 및 이전 달 및 연도가 있습니다. 시간이 지남에 따라 더 긴 뷰로 전환 하면 지출 추세를 식별 하는 데 도움이 됩니다. 비용 분석 기능을 사용 하는 경우 다양 한 시간 간격에 걸쳐 그래프 및 테이블로 비용을 볼 수 있습니다. 예산 및 비용 예측을 만든 경우 예산을 초과 하 고 과도 하 게 소요 된 위치를 쉽게 찾을 수 있습니다.

Azure에서을 만들거나 사용 하는 리소스에 대 한 비용을 발생 시킨 후에는 다음과 같은 방법으로 이러한 비용을 검토 하 고 모니터링할 수 있습니다.

* Azure Monitor를 사용 하 여 [논리 앱 실행 및 저장소 사용량 모니터링](#monitor-billing-metrics)

* [Azure Cost Management 및 요금 청구](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 사용 하 여 [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 실행

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>논리 앱 실행 및 저장소 사용량 모니터링

Azure Monitor를 사용 하 여 특정 논리 앱에 대 한 다음 메트릭을 볼 수 있습니다.

* 청구 가능한 작업 실행
* 청구 가능한 트리거 실행
* 기본 작업 실행에 대 한 청구 사용량
* 표준 커넥터 실행에 대 한 청구 사용량
* 저장소 사용량에 대 한 청구 사용량
* 청구 가능한 총 실행

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>실행 및 저장소 사용량 메트릭 보기

1. Azure Portal에서 논리 앱을 찾아서 엽니다. 논리 앱 메뉴의 **모니터링** 아래에서 **메트릭** 을 선택 합니다.

1. 오른쪽 창의 **차트 제목** 아래에 있는 메트릭 표시줄에서 **메트릭** 목록을 열고 원하는 메트릭을 선택 합니다.

   > [!NOTE]
   > 저장소 소비는 논리 앱이 사용 하 고 청구 되는 저장소 단위 (GB)의 수로 측정 됩니다. 저장소에서 500 MB 미만의 사용을 사용 하는 실행은 모니터링 보기에 표시 되지 않을 수 있지만 여전히 청구 됩니다.

   ![열린 "메트릭" 목록이 있는 메트릭 창을 보여 주는 스크린샷](./media/logic-apps-pricing/select-metric.png)

1. 창의 오른쪽 위 모서리에서 원하는 기간을 선택 합니다.

1. 논리 앱의 실행 기록에서 다른 저장소 소비 데이터, 특히 동작 입력 및 출력 크기를 보려면 [다음 단계를 수행](#view-input-output-sizes)합니다.

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>실행 기록에서 작업 입력 및 출력 크기 보기

1. Azure Portal에서 논리 앱을 찾아서 엽니다.

1. 논리 앱의 메뉴에서 **개요** 를 선택 합니다.

1. 오른쪽 창의 **실행 기록** 에서 보려는 입력 및 출력을 포함 하는 실행을 선택 합니다.

1. **논리 앱 실행** 에서 **실행 세부 정보** 를 선택 합니다.

1. **논리 앱 실행 세부 정보** 창의 작업 테이블에서 각 작업의 상태 및 기간을 나열 하는 작업 테이블에서 보려는 작업을 선택 합니다.

1. **논리 앱 작업** 창에서 해당 작업의 입력 및 출력에 대 한 크기를 찾습니다. **입력 링크** 및 **출력 링크** 에서 해당 입력 및 출력에 대 한 링크를 찾습니다.

   > [!NOTE]
   > 루프의 경우 최상위 작업만 입력 및 출력의 크기를 표시 합니다. 중첩 된 루프 안에 있는 작업의 경우 입력 및 출력은 0 크기를 표시 하 고 링크를 표시 하지 않습니다.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Azure Cost Management 및 요금 청구를 사용 하 여 비용 분석 실행

특정 범위 (예: Azure 구독)를 기반으로 Logic Apps 서비스에 대 한 비용을 검토 하려면 [Azure Cost Management 및 청구](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)의 [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 기능을 사용할 수 있습니다.

1. Azure Portal에서 Azure 구독과 같이 원하는 범위를 엽니다. 왼쪽 메뉴의 **Cost Management** 아래에서 **비용 분석** 을 선택 합니다.

   비용 분석 창을 처음으로 열면 위쪽 그래프에 현재 달의 구독에 있는 모든 서비스에 대 한 실제 및 예상 사용량 비용이 표시 됩니다.

   ![구독에서 실제 및 예상 비용에 대 한 예제와 함께 Azure Portal 및 비용 분석 창을 보여 주는 스크린샷](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > 범위를 변경 하려면 **비용 분석** 창의 필터 모음에서 **범위** 필터를 선택 합니다. **범위 선택** 창에서 원하는 범위로 전환 합니다.

   아래에서 도넛형 차트는 azure 서비스, Azure 지역 (위치) 및 리소스 그룹별로 현재 비용을 보여 줍니다.

   ![서비스, 지역 및 리소스 그룹에 대 한 예제 도넛형 차트가 포함 된 Azure Portal 및 비용 분석 창을 보여 주는 스크린샷](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. 차트를 서비스 또는 리소스와 같은 특정 영역으로 필터링 하려면 필터 모음에서 **필터 추가** 를 선택 합니다.

1. 왼쪽 목록에서 필터 유형 (예: **서비스 이름**)을 선택 합니다. 오른쪽 목록에서 필터를 선택 합니다 (예: **논리 앱**). 완료 되 면 녹색 확인 표시를 선택 합니다.

   ![필터를 선택 하 여 Azure Portal 및 비용 분석 창을 보여 주는 스크린샷](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   예를 들어 Logic Apps 서비스에 대 한 결과는 다음과 같습니다.

   !["논리 앱"에서 필터링 된 결과가 포함 된 Azure Portal 및 비용 분석 창을 보여 주는 스크린샷](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>비용 데이터 내보내기

비용에 대 한 더 많은 데이터 분석을 수행 해야 하는 경우 [비용 데이터](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 저장소 계정으로 내보낼 수 있습니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용 하 여이 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정에 따라 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 집합을 검색 하는 데 권장 되는 방법입니다.

## <a name="other-ways-to-manage-and-reduce-costs"></a>비용을 관리 하 고 절감 하는 다른 방법

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

논리 ap 및 관련 리소스에 대 한 비용을 줄이기 위해 다음 옵션을 사용해 보세요.

* 가능 하면 [기본 제공 트리거 및 동작](../connectors/apis-list.md#built-in)을 사용 합니다. 관리 되는 [커넥터 트리거 및 작업](../connectors/apis-list.md#managed-connectors)보다 실행 당 실행 되는 비용을 줄일 수 있습니다.

  예를 들어 [HTTP 작업](../connectors/connectors-native-http.md) 을 사용 하거나 [Azure Functions 서비스](../azure-functions/functions-overview.md) 를 사용 하 여 만든 함수를 호출 하 고 [기본 제공 Azure Functions 작업](../logic-apps/logic-apps-azure-functions.md)을 사용 하 여 다른 리소스에 액세스할 때 비용을 줄일 수 있습니다. 그러나 Azure Functions를 사용 하는 경우에도 비용이 발생 하므로 옵션을 비교 해야 합니다.

* 워크플로를 실행 하기 위한 [정확한 트리거 조건을 지정](logic-apps-workflow-actions-triggers.md#trigger-conditions) 합니다.

  예를 들어 대상 웹 사이트가 내부 서버 오류를 반환 하는 경우에만 트리거가 발생 하도록 지정할 수 있습니다. 트리거의 JSON 정의에서 속성을 사용 하 여 `conditions` 트리거의 상태 코드를 참조 하는 조건을 지정 합니다.

* 트리거에 폴링 버전 및 webhook 버전이 있는 경우 이벤트를 정기적으로 확인 하는 것이 아니라 발생 하기 전에 지정 된 이벤트가 발생할 때까지 대기 하는 webhook 버전을 시도 합니다.

* 워크플로가 실행 되어야 하는 경우에만 트리거가 발생 하도록 다른 서비스를 통해 논리 앱을 호출 합니다.

  예를 들어 Azure Functions 서비스를 사용 하 여 만들고 실행 하는 함수에서 논리 앱을 호출할 수 있습니다. 예를 들어 [Azure Functions 및 Azure Service Bus를 사용 하 여 논리 앱 호출 또는 트리거](logic-apps-scenario-function-sb-trigger.md)를 참조 하세요.

* 지속적으로 실행 하지 않아도 되는 [논리 앱을 사용 하지 않도록 설정](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) 하거나, 더 이상 필요 하지 않은 [논리 앱을 삭제할](manage-logic-apps-with-azure-portal.md#delete-logic-apps) 수 있습니다. 가능 하면 지속적으로 활성화 하지 않아도 되는 다른 리소스를 사용 하지 않도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cost Management로 클라우드 투자를 최적화](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [비용 분석을 사용 하 여 비용 관리](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [예기치 않은 비용 방지](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정 수행
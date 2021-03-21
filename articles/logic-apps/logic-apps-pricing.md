---
title: 청구 & 가격 책정 모델
description: 가격 책정 및 청구 모델의 작동 방식에 대 한 개요 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 2b37308bcbcd489876c21dce56878de7e0daf545
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699031"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Azure Logic Apps에 대 한 가격 책정 및 청구 모델

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 를 사용 하면 클라우드에서 규모를 확장할 수 있는 자동화 된 통합 워크플로를 만들고 실행할 수 있습니다. 이 문서에서는 Logic Apps 서비스 및 관련 리소스에 대 한 청구 및 가격 책정 모델이 작동 하는 방법을 설명 합니다. 특정 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조 하세요. 비용을 계획, 관리 및 모니터링 하는 방법을 알아보려면 [Azure Logic Apps에 대 한 비용 계획 및 관리](plan-manage-costs.md)를 참조 하세요.

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>다중 테 넌 트 가격 책정

사용할 종 량 제 사용량 가격 책정 모델은 공용, "전역", 다중 테 넌 트 Logic Apps 서비스에서 실행 되는 논리 앱에 적용 됩니다. 성공 및 실패 한 실행은 모두 요금제로 청구 됩니다.

예를 들어 폴링 트리거의 요청은 해당 트리거를 건너뛰고 논리 앱 워크플로 인스턴스를 만들지 않은 경우에도 계속 실행으로 측정 됩니다.

| 항목 | 설명 |
|-------|-------------|
| [기본 제공](../connectors/apis-list.md#built-in) 트리거 및 작업 | Logic Apps 서비스에서 기본적으로 실행 되며 [ **작업** 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용 하 여 계량 됩니다. <p><p>예를 들어 HTTP 트리거와 요청 트리거는 기본 제공 트리거 이지만 HTTP 작업 및 응답 동작은 기본 제공 작업입니다. 루프, 조건, 스위치, 병렬 분기 등과 같은 데이터 작업, 일괄 처리 작업, 변수 작업 및 [워크플로 제어 작업](../connectors/apis-list.md#control-workflow)은 기본 제공 작업 이기도 합니다. |
| [표준 커넥터](../connectors/apis-list.md#managed-connectors) 트리거 및 작업 <p><p>[사용자 지정 커넥터](../connectors/apis-list.md#custom) 트리거 및 작업 | [표준 커넥터 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용 하 여 계량 됩니다. |
| [엔터프라이즈 커넥터](../connectors/apis-list.md#managed-connectors) 트리거 및 작업 | [엔터프라이즈 커넥터 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용 하 여 계량 됩니다. 그러나 공개 미리 보기 중에 엔터프라이즈 커넥터는 [ *표준* 커넥터 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용 하 여 계량 됩니다. |
| [루프](logic-apps-control-flow-loops.md) 내부의 작업 | 루프에서 실행 되는 각 작업은를 실행 하는 각 루프 주기에 대해 측정 됩니다. <p><p>예를 들어 목록을 처리 하는 동작을 포함 하는 "for each" 루프가 있다고 가정 합니다. Logic Apps 서비스는 목록 항목의 수를 루프의 작업 수와 곱하여 루프를 시작 하는 작업을 추가 하 여 해당 루프에서 실행 되는 각 작업을 측정 합니다. 따라서 10 개 항목 목록에 대 한 계산은 (10 * 1) + 1 이며이로 인해 11 개의 작업 실행이 발생 합니다. |
| 다시 시도 횟수 | 가장 기본적인 예외 및 오류를 처리 하려면 지원 되는 트리거 및 작업에 대 한 [재시도 정책을](logic-apps-exception-handling.md#retry-policies) 설정할 수 있습니다. 원래 요청과 함께 이러한 재시도는 트리거 또는 작업에 기본 제공, 표준 또는 엔터프라이즈 유형이 있는지에 따라 요금이 청구 됩니다. 예를 들어 두 번의 재시도로 실행 되는 작업은 3 개의 작업 실행에 대해 요금이 청구 됩니다. |
| [데이터 보존 및 저장소 사용량](#data-retention) | **가격 정보** 테이블의 [Logic Apps 가격 책정 페이지](https://azure.microsoft.com/pricing/details/logic-apps/)에서 찾을 수 있는 데이터 보존 가격을 사용 하 여 계량 됩니다. |
|||

자세한 내용은 다음을 참조하세요.

* [실행 및 저장소 사용량에 대 한 메트릭 보기](plan-manage-costs.md#monitor-billing-metrics)
* [Azure Logic Apps 제한](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>요금제에 없음

* 충족 되지 않은 조건으로 인해 건너뛴 트리거
* 완료 하기 전에 논리 앱이 중지 되어 실행 되지 않은 작업
* [사용 하지 않는 논리 앱](#disabled-apps)

### <a name="other-related-resources"></a>기타 관련 된 리소스

논리 앱은 통합 계정, 온-프레미스 데이터 게이트웨이 및 integration service environment (ISEs)와 같은 다른 관련 리소스와 함께 작동 합니다. 이러한 리소스의 가격 책정에 대 한 자세한 내용은이 항목의 뒷부분에 나오는 다음 섹션을 검토 하세요.

* [On-premises data gateway (온-프레미스 데이터 게이트웨이)](#data-gateway)
* [통합 계정 가격 책정 모델](#integration-accounts)
* [ISE 가격 책정 모델](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>소비 비용 예측을 위한 팁

보다 정확한 소비 비용을 예측할 수 있도록 다음 팁을 검토 하십시오.

* 폴링 간격 으로만 계산을 기반으로 하는 대신 특정 날짜에 도착할 수 있는 메시지 또는 이벤트의 수를 고려 합니다.

* 이벤트나 메시지가 트리거 조건을 충족하면 많은 트리거가 조건을 충족하는 대기 중인 모든 이벤트나 메시지를 즉시 읽으려고 시도합니다. 이러한 동작은 폴링 간격을 길게 선택하더라도 워크플로를 시작하기에 적합한 대기 중인 이벤트나 메시지 수를 기반으로 트리거가 실행된다는 것을 의미합니다. 이러한 동작을 따르는 트리거에는 Azure Service Bus 및 Azure Event Hub가 있습니다.

  예를 들어 매일 끝점을 확인 하는 트리거를 설정 한다고 가정 합니다. 트리거가 엔드포인트를 확인하여 조건을 충족하는 이벤트를 15개 찾으면, 트리거가 실행되고 해당 워크플로가 15번 실행됩니다. Logic Apps 서비스는 트리거 요청을 포함 하 여 해당 15 개의 워크플로가 수행 하는 모든 작업을 측정 합니다.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>ISE 가격

고정 가격 책정 모델은 [ISE ( *integration service environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 실행 되는 논리 앱에 적용 됩니다. ISE는 생성 하는 [ise 수준 또는 *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) 에 따라 달라 지는 [통합 서비스 환경 가격](https://azure.microsoft.com/pricing/details/logic-apps)을 사용 하 여 청구 됩니다. 이 가격은 사용 여부에 관계 없이 예약 된 용량과 전용 리소스에 대 한 요금을 지불 하므로 다중 테 넌 트 가격 책정과 다릅니다.

| ISE SKU | 설명 |
|---------|-------------|
| **Premium** | 기본 단위는 [고정 된 용량](logic-apps-limits-and-config.md#integration-service-environment-ise) 을 가지 며 [Premium SKU에 대 한 시간당 요금으로 청구](https://azure.microsoft.com/pricing/details/logic-apps)됩니다. 더 많은 처리량이 필요한 경우 ISE를 만들 때 또는 나중에 [확장 단위를 추가할](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) 수 있습니다. 각 배율 단위는 [기본 단가의 약 절반에 해당 하는 시간당 요금](https://azure.microsoft.com/pricing/details/logic-apps)으로 청구 됩니다. <p><p>용량 및 제한 정보는 [Azure Logic Apps ISE 제한](logic-apps-limits-and-config.md#integration-service-environment-ise)을 참조 하세요. |
| **개발자** | 기본 단위는 [고정 용량](logic-apps-limits-and-config.md#integration-service-environment-ise) 을 가지 며 [개발자 SKU에 대 한 시간당 요금으로 청구](https://azure.microsoft.com/pricing/details/logic-apps)됩니다. 그러나이 SKU에는 SLA (서비스 수준 계약), 확장 기능 또는 중복성을 제공 하지 않습니다. 즉, 지연 또는 가동 중지 시간이 발생할 수 있습니다. 백 엔드 업데이트는 간헐적으로 서비스를 중단할 수 있습니다. <p><p>**중요**:이 SKU는 프로덕션 또는 성능 테스트가 아닌 탐색, 실험, 개발 및 테스트에만 사용 해야 합니다. <p><p>용량 및 제한 정보는 [Azure Logic Apps ISE 제한](logic-apps-limits-and-config.md#integration-service-environment-ise)을 참조 하세요. |
|||

### <a name="included-at-no-extra-cost"></a>추가 비용 없이 포함

| 항목 | 설명 |
|-------|-------------|
| [기본 제공](../connectors/apis-list.md#built-in) 트리거 및 작업 | **핵심** 레이블을 표시 하 고 논리 앱과 동일한 ISE에서 실행 합니다. |
| [표준 커넥터](../connectors/apis-list.md#managed-connectors) <p><p>[엔터프라이즈 커넥터](../connectors/apis-list.md#enterprise-connectors) | - **ISE** 레이블을 표시 하는 관리 커넥터는 온-프레미스 데이터 게이트웨이 없이 작동 하 고 논리 앱과 동일한 ISE에서 실행 되도록 특별히 설계 되었습니다. ISE 가격에는 원하는 만큼의 엔터프라이즈 연결이 포함 됩니다. <p><p>-ISE 레이블을 표시 하지 않는 커넥터는 다중 테 넌 트 Logic Apps 서비스에서 실행 됩니다. 그러나 ise 가격은 ISE에서 실행 되는 논리 앱에 대 한 이러한 실행을 포함 합니다. |
| [루프](logic-apps-control-flow-loops.md) 내부의 작업 | ISE 가격에는 실행 되는 각 루프 주기에 대해 루프에서 실행 되는 각 작업이 포함 됩니다. <p><p>예를 들어 목록을 처리 하는 동작을 포함 하는 "for each" 루프가 있다고 가정 합니다. 작업 실행의 총 수를 가져오려면 목록 항목 수와 루프의 작업 수를 곱하고 루프를 시작 하는 작업을 추가 합니다. 따라서 10 개 항목 목록에 대 한 계산은 (10 * 1) + 1 이며이로 인해 11 개의 작업 실행이 발생 합니다. |
| 다시 시도 횟수 | 가장 기본적인 예외 및 오류를 처리 하려면 지원 되는 트리거 및 작업에 대 한 [재시도 정책을](logic-apps-exception-handling.md#retry-policies) 설정할 수 있습니다. ISE 가격은 원래 요청과 함께 재시도를 포함 합니다. |
| [데이터 보존 및 저장소 사용량](#data-retention) | ISE의 논리 앱은 보존 및 저장소 비용을 초래 하지 않습니다. |
| [통합 계정](#integration-accounts) | 에는 추가 비용 없이 ISE SKU에 따라 단일 통합 계정 계층에 대 한 사용이 포함 됩니다. |
|||

제한 정보는 [Azure Logic Apps ISE 제한](logic-apps-limits-and-config.md#integration-service-environment-ise)을 참조 하세요.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>통합 계정

[통합 계정은](../logic-apps/logic-apps-pricing.md#integration-accounts) [EDI](logic-apps-enterprise-integration-b2b.md) 및 [XML 처리](logic-apps-enterprise-integration-xml.md) 기능을 사용 하는 B2B 통합 솔루션을 탐색, 빌드 및 테스트할 수 있도록 논리 앱을 만들고 연결 하는 별도의 리소스입니다.

Azure Logic Apps는 논리 앱이 소비 기반 인지 ISE 기반 인지에 따라 가격 책정 및 [청구 모델](logic-apps-pricing.md#integration-accounts) [에 따라 달라](https://azure.microsoft.com/pricing/details/logic-apps/) 지는 이러한 통합 계정 수준 또는 계층을 제공 합니다.

| 계층 | Description |
|------|-------------|
| **기본** | 메시지 처리만 하거나 더 큰 비즈니스 엔터티와 거래 파트너 관계가 있는 소규모 비즈니스 파트너 역할을 수행 하려는 시나리오 <p><p>Logic Apps SLA에서 지원 됩니다. |
| **Standard** | 더 복잡 한 B2B 관계와 관리 해야 하는 엔터티 수가 증가 하는 시나리오 <p><p>Logic Apps SLA에서 지원 됩니다. |
| **Free** | 프로덕션 시나리오가 아닌 예비 시나리오에 적합 합니다. 이 계층에는 지역 가용성, 처리량 및 사용에 대 한 제한이 있습니다. 예를 들어 무료 계층은 Azure의 공용 지역 (예: 미국 서 부 또는 동남 아시아)에 대해서만 사용할 수 있지만, [Azure 중국 21vianet](/azure/china/overview-operations) 또는 [Azure Government](../azure-government/documentation-government-welcome.md)는 사용할 수 없습니다. <p><p>**참고**: Logic Apps SLA에서 지원 되지 않습니다. |
|||

통합 계정 제한에 대 한 자세한 내용은 [Azure Logic Apps에 대 한 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)을 참조 하세요.

* [Azure 구독 당 통합 계정에 대 한 제한](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [통합 계정에 따라 다양 한 아티팩트에 대 한 제한](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)입니다. 아티팩트에는 거래 업체, 규약, 맵, 스키마, 어셈블리, 인증서, 일괄 처리 구성 등이 포함 됩니다.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>소비 기반 논리 앱에 대 한 통합 계정

통합 계정은 사용 하는 계정 계층을 기반으로 하는 고정 된 [통합 계정 가격](https://azure.microsoft.com/pricing/details/logic-apps/) 을 사용 하 여 청구 됩니다.

### <a name="ise-based-logic-apps"></a>ISE 기반 논리 앱

추가 비용 없이 ISE에는 ISE SKU에 따라 단일 통합 계정이 포함 되어 있습니다. 추가 비용을 위해 ise에 대 한 더 많은 통합 계정을 만들어 [총 ise 한도](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)를 사용할 수 있습니다. 이 항목의 앞부분에 나오는 [ISE 가격 책정 모델](#fixed-pricing) 에 대해 자세히 알아보세요.

| ISE SKU | 포함 된 통합 계정 | 추가 비용 |
|---------|------------------------------|-----------------|
| **Premium** | 단일 [표준](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정 | 최대 19 개의 표준 계정. 체험 또는 기본 계정은 허용되지 않습니다. |
| **개발자** | 단일 [무료](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정 | 무료 계정이 이미 있는 경우 최대 19 개의 표준 계정, 무료 계정이 없는 경우 20 개의 총 표준 계정 기본 계정은 허용되지 않습니다. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>데이터 보존 및 저장소 사용량

논리 앱 실행 기록의 모든 입력 및 출력은 해당 앱의 [실행 지속 시간 및 기록 보존 기간](logic-apps-limits-and-config.md#run-duration-retention-limits)에 따라 저장 되 고 계량 됩니다.

* 다중 테 넌 트 Logic Apps 서비스의 논리 앱에 대 한 저장소 사용량은 고정 가격으로 청구 되며 가격 **정보** 테이블의 [Logic Apps 가격 책정 페이지](https://azure.microsoft.com/pricing/details/logic-apps)에서 찾을 수 있습니다.

* ISEs에서 논리 앱의 경우 저장소 사용에는 데이터 보존 비용이 발생 하지 않습니다.

저장소 사용량 사용량을 모니터링 하려면 [실행 및 저장소 소비에 대 한 메트릭 보기](plan-manage-costs.md#monitor-billing-metrics)를 참조 하세요.

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이

[온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-install.md) 는 논리 앱이 특정 게이트웨이 지원 커넥터를 사용 하 여 온-프레미스 데이터에 액세스할 수 있도록 만드는 별도의 리소스입니다. 게이트웨이를 통해 실행 되는 커넥터 작업에는 요금이 발생 하지만 게이트웨이 자체에는 요금이 부과 되지 않습니다.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>사용 하지 않는 논리 앱

사용 하지 않도록 설정 된 논리 앱은 사용 하지 않도록 설정 된 상태에서 새 인스턴스를 만들 수 없으므로 요금이 청구 되지 않습니다 논리 앱을 사용 중지하면 현재 실행 중인 인스턴스가 완전히 중지되기까지 조금 시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에 대 한 비용 계획 및 관리](plan-manage-costs.md)

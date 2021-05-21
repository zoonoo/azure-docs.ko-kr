---
title: 청구 및 가격 책정 모델
description: Azure Logic Apps에서의 가격 책정 및 청구 모델 작동 방식 개요
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a3c20dd85c94c359259cf69e25bb9083d56857fc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777152"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Azure Logic Apps의 가격 책정 및 청구 모델

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하면 클라우드에서 확장 가능한 자동화된 통합 워크플로를 만들어 실행할 수 있습니다. 이 문서에서는 Logic Apps 서비스 및 관련 리소스에 대해 청구 및 가격 책정 모델이 어떻게 작동하는지 설명합니다. 특정 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조하세요. 비용 계획, 관리 및 모니터링 방법에 대해 알아보려면 [Azure Logic Apps에 대한 비용 계획 및 관리](plan-manage-costs.md)를 참조하세요.

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>다중 테넌트 가격 책정

종량제 소비 가격 책정 모델은 공용, “글로벌”, 다중 테넌트 Logic Apps 서비스에서 실행되는 논리 앱에 적용됩니다. 성공 및 실패한 실행이 모두 측정되어 요금이 청구됩니다.

예를 들어, 폴링 트리거의 요청은 해당 트리거를 건너뛰고 논리 앱 워크플로 인스턴스를 만들지 않은 경우에도 계속 실행으로 측정됩니다.

| 항목 | 설명 |
|-------|-------------|
| [기본 제공](../connectors/built-in.md) 트리거 및 작업 | Logic Apps 서비스에서 기본적으로 실행되며 [**작업** 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용하여 측정됩니다. <p><p>예를 들어, HTTP 트리거와 요청 트리거는 기본 제공 트리거이고 HTTP 작업 및 응답 작업은 기본 제공 작업입니다. 루프, 조건, 스위치, 병렬 분기 등과 같은 데이터 작업, 일괄 처리 작업, 변수 작업 및 [워크플로 제어 작업](../connectors/built-in.md)도 기본 제공 작업입니다. |
| [표준 커넥터](../connectors/managed.md) 트리거 및 작업 <p><p>[사용자 지정 커넥터](../connectors/apis-list.md#custom-apis-and-connectors) 트리거 및 작업 | [표준 커넥터 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용하여 측정됩니다. |
| [엔터프라이즈 커넥터](../connectors/managed.md) 트리거 및 작업 | [엔터프라이즈 커넥터 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용하여 측정됩니다. 그러나 공개 미리 보기 중에는 엔터프라이즈 커넥터가 [*표준* 커넥터 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용하여 측정됩니다. |
| [루프](logic-apps-control-flow-loops.md) 내부의 작업 | 루프에서 실행되는 각 작업은 실행되는 각 루프 주기에 대해 측정됩니다. <p><p>예를 들어, 목록을 처리하는 작업을 포함하는 "for each" 루프가 있다고 가정합니다. Logic Apps 서비스는 목록 항목의 수를 루프의 작업 수와 곱하여 해당 루프에서 실행되는 각각의 작업을 측정하고, 루프를 시작하는 작업을 추가합니다. 따라서 10개 항목 목록에 대한 계산은 (10 * 1) + 1이므로 작업 실행은 11개가 됩니다. |
| 다시 시도 횟수 | 가장 기본적인 예외 및 오류를 처리하기 위해 지원되는 트리거 및 작업에 대한 [재시도 정책](logic-apps-exception-handling.md#retry-policies)을 설정할 수 있습니다. 원래의 요청과 함께 이러한 재시도는 트리거 또는 작업에 기본 제공, 표준 또는 엔터프라이즈 유형이 있는지 여부에 따라 요금이 청구됩니다. 예를 들어, 두 번의 재시도로 실행되는 작업은 3개의 작업 실행에 대해 요금이 청구됩니다. |
| [데이터 보존 및 스토리지 소비](#data-retention) | 데이터 보존 가격을 사용하여 측정되며, [Logic Apps 가격 책정 페이지](https://azure.microsoft.com/pricing/details/logic-apps/)의 **가격 책정 세부 정보** 표에서 확인할 수 있습니다. |
|||

자세한 내용은 다음을 참조하세요.

* [실행 및 스토리지 소비에 대한 메트릭 보기](plan-manage-costs.md#monitor-billing-metrics)
* [Azure Logic Apps의 한도](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>요금제에 없음

* 충족되지 않은 조건으로 인해 건너뛴 트리거
* 완료되기 전에 논리 앱이 중지되어 실행되지 않은 작업
* [사용할 수 없는 논리 앱](#disabled-apps)

### <a name="other-related-resources"></a>기타 관련 리소스

논리 앱은 통합 계정, 온-프레미스 데이터 게이트웨이 및 ISE(통합 서비스 환경)와 같은 다른 관련 리소스와 함께 작동합니다. 이러한 리소스의 가격 책정에 대한 자세한 정보는 이 항목의 뒷부분에 나오는 다음 섹션을 검토하세요.

* [On-premises data gateway (온-프레미스 데이터 게이트웨이)](#data-gateway)
* [통합 계정 가격 책정 모델](#integration-accounts)
* [ISE 가격 책정 모델](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>소비 비용 예측을 위한 팁

더 정확한 소비 비용 예측을 위해 다음 팁을 검토하세요.

* 폴링 간격만을 기반으로 계산하기 보다는 지정된 날짜에 수신할 수 있는 메시지 또는 이벤트 수를 고려합니다.

* 이벤트나 메시지가 트리거 조건을 충족하면 많은 트리거가 조건을 충족하는 대기 중인 모든 이벤트나 메시지를 즉시 읽으려고 시도합니다. 이러한 동작은 폴링 간격을 길게 선택하더라도 워크플로를 시작하기에 적합한 대기 중인 이벤트나 메시지 수를 기반으로 트리거가 실행된다는 것을 의미합니다. 이러한 동작을 따르는 트리거에는 Azure Service Bus 및 Azure Event Hub가 있습니다.

  예를 들어, 매일 엔드포인트를 확인하는 트리거를 설정한다고 가정하겠습니다. 트리거가 엔드포인트를 확인하여 조건을 충족하는 이벤트를 15개 찾으면, 트리거가 실행되고 해당 워크플로가 15번 실행됩니다. Logic Apps 서비스는 트리거 요청을 포함하여 15개 워크플로에서 수행하는 모든 작업을 측정합니다.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>ISE 가격 책정

고정 가격 책정 모델은 [*ISE*(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 실행되는 논리 앱에 적용됩니다. ISE는 생성하는 [ISE 수준 또는 *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)에 따라 달라지는 [통합 서비스 환경 가격](https://azure.microsoft.com/pricing/details/logic-apps)을 사용하여 청구됩니다. 이 가격은 사용 여부에 관계없이 예약된 용량과 전용 리소스에 대한 요금을 지불하므로 다중 테넌트 가격 책정과 다릅니다.

| ISE SKU | Description |
|---------|-------------|
| **Premium** | 기준 단위는 [고정 용량](logic-apps-limits-and-config.md#integration-service-environment-ise)이며, [프리미엄 SKU에 대한 시간당 요금](https://azure.microsoft.com/pricing/details/logic-apps)으로 청구됩니다. 더 많은 처리량이 필요한 경우 ISE를 만들 때 또는 나중에 [배율 단위를 더 추가](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)할 수 있습니다. 각 배율 단위는 [기준 단위 요금의 약 절반에 해당하는 시간당 요금](https://azure.microsoft.com/pricing/details/logic-apps)으로 청구됩니다. <p><p>용량 및 한도 정보는 [Azure Logic Apps의 ISE 한도](logic-apps-limits-and-config.md#integration-service-environment-ise)를 참조하세요. |
| **개발자** | 기준 단위는 [고정 용량](logic-apps-limits-and-config.md#integration-service-environment-ise)이며, [개발자 SKU에 대한 시간당 요금](https://azure.microsoft.com/pricing/details/logic-apps)으로 청구됩니다. 그러나 이 SKU는 SLA(서비스 수준 계약), 스케일 업 기능 또는 재활용 시 중복성이 없으므로, 지연 또는 가동 중지 시간이 발생할 수 있습니다. 백 엔드 업데이트로 인해 서비스가 간헐적으로 중단될 수 있습니다. <p><p>**중요**: 이 SKU는 프로덕션 또는 성능 테스트가 아닌 탐색, 실험, 개발 및 테스트에만 사용해야 합니다. <p><p>용량 및 한도 정보는 [Azure Logic Apps의 ISE 한도](logic-apps-limits-and-config.md#integration-service-environment-ise)를 참조하세요. |
|||

### <a name="included-at-no-extra-cost"></a>추가 비용 없이 포함

| 항목 | 설명 |
|-------|-------------|
| [기본 제공](../connectors/built-in.md) 트리거 및 작업 | **Core** 레이블을 표시하고 논리 앱과 동일한 ISE에서 실행합니다. |
| [표준 커넥터](../connectors/managed.md) <p><p>[엔터프라이즈 커넥터](../connectors/managed.md#enterprise-connectors) | - **ISE** 레이블을 표시하는 관리형 커넥터는 온-프레미스 데이터 게이트웨이 없이 작동하고, 논리 앱과 동일한 ISE에서 실행되도록 특별히 설계되었습니다. ISE 가격 책정에는 사용자가 원하는 만큼의 엔터프라이즈 연결이 포함됩니다. <p><p>- ISE 레이블을 표시하지 않는 커넥터는 다중 테넌트 Logic Apps 서비스에서 실행됩니다. 그러나 ISE 가격 책정에는 ISE에서 실행되는 논리 앱에 대한 이러한 실행이 포함됩니다. |
| [루프](logic-apps-control-flow-loops.md) 내부의 작업 | ISE 가격 책정에는 실행되는 각 루프 주기에 대해 루프에서 실행되는 각 작업이 포함됩니다. <p><p>예를 들어, 목록을 처리하는 작업을 포함하는 "for each" 루프가 있다고 가정합니다. 총 작업 실행 수를 가져오려면 목록 항목 수와 루프의 작업 수를 곱하고 루프를 시작하는 작업을 더합니다. 따라서 10개 항목 목록에 대한 계산은 (10 * 1) + 1이므로 작업 실행은 11개가 됩니다. |
| 다시 시도 횟수 | 가장 기본적인 예외 및 오류를 처리하기 위해 지원되는 트리거 및 작업에 대한 [재시도 정책](logic-apps-exception-handling.md#retry-policies)을 설정할 수 있습니다. ISE 가격 책정에는 원래 요청과 함께 재시도가 포함됩니다. |
| [데이터 보존 및 스토리지 소비](#data-retention) | ISE의 논리 앱은 보존 및 스토리지 비용을 발생시키지 않습니다. |
| [통합 계정](#integration-accounts) | 추가 비용 없이 ISE SKU를 기반으로 한 단일 통합 계정 계층에 대한 사용량이 포함됩니다. |
|||

한도 정보는 [Azure Logic Apps의 ISE 한도](logic-apps-limits-and-config.md#integration-service-environment-ise)를 참조하세요.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>통합 계정

[통합 계정](../logic-apps/logic-apps-pricing.md#integration-accounts)은 [EDI](logic-apps-enterprise-integration-b2b.md) 및 [XML 처리](logic-apps-enterprise-integration-xml.md) 기능을 사용하는 B2B 통합 솔루션을 검색, 빌드 및 테스트할 수 있도록 만들어 논리 앱에 연결하는 별도의 리소스입니다.

Azure Logic Apps는 논리 앱이 소비 기반인지 또는 ISE 기반인지에 따라 [가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)과 [청구 모델](logic-apps-pricing.md#integration-accounts)이 달라지는 다음과 같은 통합 계정 수준 또는 계층을 제공합니다.

| 계층 | Description |
|------|-------------|
| **기본** | 메시지 처리만 원하는 시나리오 또는 대규모 비즈니스 엔터티와 거래 업체 관계를 맺고 있는 소규모 비즈니스 파트너 역할을 수행하는 시나리오의 경우. <p><p>Logic Apps SLA에서 지원됩니다. |
| **Standard** | 더 복잡한 B2B 관계가 있고 관리해야 하는 엔터티 수가 늘어난 시나리오의 경우. <p><p>Logic Apps SLA에서 지원됩니다. |
| **Free** | 프로덕션 시나리오가 아닌 예비 시나리오에 적합합니다. 이 계층에는 지역 가용성, 처리량 및 사용량에 대한 한도가 있습니다. 예를 들어, 무료 계층은 Azure의 공용 지역(예: 미국 서부 또는 동남 아시아)에서만 사용할 수 있고, [Azure 중국 21Vianet](/azure/china/overview-operations) 또는 [Azure Government](../azure-government/documentation-government-welcome.md)에서는 사용할 수 없습니다. <p><p>**참고**: Logic Apps SLA에서 지원되지 않습니다. |
|||

통합 계정 한도에 대한 자세한 내용은 [Azure Logic Apps의 한도 및 구성](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)을 참조하세요.

* [Azure 구독당 통합 계정 한도](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [통합 계정당 여러 아티팩트에 대한 한도](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 아티팩트에는 거래 업체, 계약, 맵, 스키마, 어셈블리, 인증서, 일괄 처리 구성 등이 포함됩니다.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>소비 기반 논리 앱에 대한 통합 계정

통합 계정은 사용하는 계정 계층을 기반으로 하는 고정된 [통합 계정 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용하여 청구됩니다.

### <a name="ise-based-logic-apps"></a>ISE 기반 논리 앱

추가 비용 없이 ISE는 ISE SKU를 기반으로 한 단일 통합 계정을 포함합니다. 추가 비용으로 ISE에 대한 통합 계정을 더 많이 만들어 [총 ISE 한도](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)까지 사용할 수 있습니다. 이 토픽의 앞부분에서 [ISE 가격 책정 모델](#fixed-pricing)에 대해 자세히 알아보세요.

| ISE SKU | 통합 계정 포함 | 추가 비용 |
|---------|------------------------------|-----------------|
| **Premium** | 단일 [표준](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정 | 표준 계정 최대 19개 추가. 체험 또는 기본 계정은 허용되지 않습니다. |
| **개발자** | 단일 [무료](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정 | 체험 계정이 이미 있는 경우 최대 19개의 표준 계정 또는 체험 계정이 없는 경우 총 20개의 표준 계정이 추가됩니다. 기본 계정은 허용되지 않습니다. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>데이터 보존 및 스토리지 소비

논리 앱 실행 기록의 모든 입력 및 출력은 해당 앱의 [실행 지속 시간 및 기록 보존 기간](logic-apps-limits-and-config.md#run-duration-retention-limits)에 따라 저장 및 측정됩니다.

* 다중 테넌트 Logic Apps 서비스의 논리 앱의 경우 스토리지 소비는 고정 가격으로 청구되며, [Logic Apps 가격 책정 페이지](https://azure.microsoft.com/pricing/details/logic-apps)의 **가격 책정 세부 정보** 표에서 확인할 수 있습니다.

* ISE의 논리 앱에서는 스토리지 소비에서 데이터 보존 비용이 발생하지 않습니다.

스토리지 소비 사용량을 모니터링하려면 [실행 및 스토리지 소비에 대한 메트릭 보기](plan-manage-costs.md#monitor-billing-metrics)를 참조하세요.

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이

[온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-install.md)는 논리 앱이 특정 게이트웨이 지원 커넥터를 사용하여 온-프레미스 데이터에 액세스할 수 있도록 만드는 별도의 리소스입니다. 게이트웨이를 통해 실행되는 커넥터 작업에는 요금이 발생하지만 게이트웨이 자체에는 요금이 부과되지 않습니다.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>사용할 수 없는 논리 앱

사용할 수 없는 논리 앱은 새 인스턴스를 만들 수 없으므로 사용하지 않는 동안 요금이 청구되지 않습니다. 논리 앱을 사용 중지하면 현재 실행 중인 인스턴스가 완전히 중지되기까지 조금 시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에 대한 비용 계획 및 관리](plan-manage-costs.md)

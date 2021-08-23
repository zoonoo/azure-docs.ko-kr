---
title: 청구 및 가격 책정 모델
description: Azure Logic Apps에서의 가격 책정 및 청구 모델 작동 방식 개요
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 3b627abdf27907a5c0739e6dd7920932a3035ee7
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111981700"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Azure Logic Apps의 가격 책정 및 청구 모델

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하면 클라우드에서 확장 가능한 자동화된 통합 워크플로를 만들어 실행할 수 있습니다. 이 문서에서는 Azure Logic Apps 및 관련 리소스에 대해 청구 및 가격 책정 모델이 어떻게 작동하는지 설명합니다. 특정 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조하세요. 비용 계획, 관리 및 모니터링 방법에 대해 알아보려면 [Azure Logic Apps에 대한 비용 계획 및 관리](plan-manage-costs.md)를 참조하세요.

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-multi-tenant"></a>소비 가격 책정(다중 테넌트)

종량제 소비 가격 책정 모델은 퍼블릭, ‘글로벌’, 다중 테넌트 Azure Logic Apps 환경에서 실행되는 논리 앱에 적용됩니다. 다음과 같은 여러 옵션을 사용하여 이러한 논리 앱을 만들 수 있습니다.

* Azure Portal의 논리 앱(소비) 리소스 유형
* Visual Studio Code의 Azure Logic Apps(소비) 확장
* Visual Studio의 Azure Logic Apps 도구 확장
* `Microsoft.Logic` 리소스 유형을 사용하는 ARM 템플릿(Azure Resource Manager 템플릿)
* `az logic` 명령을 사용하는 Azure Logic Apps에 대한 Azure CLI
* `Az.LogicApp` 모듈을 사용하는 Azure Logic Apps에 대한 Azure PowerShell
* Azure Logic Apps에 대한 REST API
* Azure Portal의 [자동화 작업](create-automation-tasks-azure-resources.md)

계량 및 청구는 논리 앱 워크플로의 트리거 및 작업 실행을 기준으로 합니다. 이러한 실행은 워크플로가 성공적으로 실행되는지 또는 워크플로가 인스턴스화되었는지 여부에 관계없이 계량 및 요금이 청구됩니다. 예를 들어 자동화 태스크가 정기적으로 엔드포인트에 나가는 호출을 수행하는 폴링 트리거를 사용한다고 가정합니다. 이 아웃바운드 요청은 트리거가 발생하거나 건너뛰는지 여부에 관계없이 계량되고 실행으로 청구됩니다. 이는 워크플로 인스턴스가 만들어지는지 여부에 영향을 줍니다.

| 항목 | 설명 |
|-------|-------------|
| [기본 제공](../connectors/built-in.md) 트리거 및 작업 | Logic Apps 서비스에서 기본적으로 실행되며 [**작업** 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용하여 측정됩니다. <p><p>예를 들어, HTTP 트리거와 요청 트리거는 기본 제공 트리거이고 HTTP 작업 및 응답 작업은 기본 제공 작업입니다. 루프, 조건, 스위치, 병렬 분기 등과 같은 데이터 작업, 일괄 처리 작업, 변수 작업 및 [워크플로 제어 작업](../connectors/built-in.md)도 기본 제공 작업입니다. <p><p>**참고**: 월별 보너스로, 소비 계획에는 수천 개의 기본 제공 실행이 무료로 포함됩니다. |
| [표준 커넥터](../connectors/managed.md) 트리거 및 작업 <p><p>[사용자 지정 커넥터](../connectors/apis-list.md#custom-apis-and-connectors) 트리거 및 작업 | [표준 커넥터 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용하여 측정됩니다. |
| [엔터프라이즈 커넥터](../connectors/managed.md) 트리거 및 작업 | [엔터프라이즈 커넥터 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용하여 측정됩니다. 그러나 커넥터 미리 보기 중에는 엔터프라이즈 커넥터가 [*표준* 커넥터 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용하여 측정됩니다. |
| [루프](logic-apps-control-flow-loops.md) 내부의 작업 | 루프에서 실행되는 각 작업은 실행되는 각 루프 주기에 대해 측정됩니다. <p><p>예를 들어, 목록을 처리하는 작업을 포함하는 "for each" 루프가 있다고 가정합니다. Logic Apps 서비스는 목록 항목의 수를 루프의 작업 수와 곱하여 해당 루프에서 실행되는 각각의 작업을 측정하고, 루프를 시작하는 작업을 추가합니다. 따라서 10개 항목 목록에 대한 계산은 (10 * 1) + 1이므로 작업 실행은 11개가 됩니다. |
| 다시 시도 횟수 | 가장 기본적인 예외 및 오류를 처리하기 위해 지원되는 트리거 및 작업에 대한 [재시도 정책](logic-apps-exception-handling.md#retry-policies)을 설정할 수 있습니다. 원래의 요청과 함께 이러한 재시도는 트리거 또는 작업에 기본 제공, 표준 또는 엔터프라이즈 유형이 있는지 여부에 따라 요금이 청구됩니다. 예를 들어, 두 번의 재시도로 실행되는 작업은 3개의 작업 실행에 대해 요금이 청구됩니다. |
| [데이터 보존 및 스토리지 사용량](#data-retention) | 데이터 보존 가격을 사용하여 측정되며, [Logic Apps 가격 책정 페이지](https://azure.microsoft.com/pricing/details/logic-apps/)의 **가격 책정 세부 정보** 표에서 확인할 수 있습니다. |
|||

자세한 내용은 다음 설명서를 검토하세요.

* [실행 및 스토리지 사용량에 대한 메트릭 보기](plan-manage-costs.md#monitor-billing-metrics)
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

* 이벤트나 메시지가 트리거 조건을 충족하면 많은 트리거가 조건을 충족하는 대기 중인 다른 이벤트나 메시지를 즉시 읽으려고 시도합니다. 이러한 동작은 폴링 간격을 길게 선택하더라도 워크플로를 시작하기에 적합한 대기 중인 이벤트나 메시지 수를 기반으로 트리거가 실행된다는 것을 의미합니다. 이러한 동작을 따르는 트리거에는 Azure Service Bus 및 Azure Event Hub가 있습니다.

  예를 들어, 매일 엔드포인트를 확인하는 트리거를 설정한다고 가정하겠습니다. 트리거가 엔드포인트를 확인하여 조건을 충족하는 이벤트를 15개 찾으면, 트리거가 실행되고 해당 워크플로가 15번 실행됩니다. Logic Apps 서비스는 트리거 요청을 포함하여 15개 워크플로에서 수행하는 모든 작업을 측정합니다.

<a name="standard-pricing"></a>

## <a name="standard-pricing-single-tenant"></a>표준 가격 책정(단일 테넌트)

호스팅 계획 및 가격 책정 계층 기반 가격 책정 모델은 단일 테넌트 Azure Logic Apps 환경에서 실행되는 논리 앱에 적용됩니다. 이 가격은 Azure Portal의 **논리 앱(표준)** 리소스 형식 또는 Visual Studio Code에 대한 **Azure Logic Apps(표준)** 확장을 사용하여 작업하는 논리 앱에 적용됩니다. 이러한 논리 앱을 만들거나 배포할 때 워크플로 실행 시에 측정 및 요금 청구에 사용할 가격 요율을 결정하는 호스팅 플랜 및 가격 책정 계층을 선택해야 합니다.

> [!NOTE]
> **논리 앱(표준)** 리소스 종류를 사용하여 만드는 새 논리 앱의 경우 **워크플로 표준** 호스팅 계획을 사용해야 합니다. App Service 플랜 및 App Service Environment는 새 논리 앱에 사용할 수 없습니다.

<a name="hosting-plans"></a>

### <a name="pricing-tiers-and-billing-rates"></a>가격 책정 계층 및 청구 요율

호스팅 플랜의 각 가격 책정 계층에는 특정 양의 컴퓨팅, 메모리 및 스토리지 리소스가 포함됩니다. 리소스 및 지역별 시간당 요율에 대해서는 [Azure Logic Apps 가격 책정 페이지](https://azure.microsoft.com/pricing/details/logic-apps/)를 검토하세요.

가격 책정 방식을 더 잘 이해하기 위해 이 예제에서는 *미국 동부 2 지역* 에 대한 샘플 예상치를 제공합니다.

* [Azure Logic Apps 가격 책정 페이지](https://azure.microsoft.com/pricing/details/logic-apps/)에서 **미국 동부 2** 지역을 선택하여 시간당 요율을 보거나 다음 표를 검토하세요.

  | 리소스 | 시간별 USD(미국 동부 2) |
  |----------|------------------------|
  | **vCPU(가상 CPU)** | $0.192 |
  | **메모리** | GB당 $0.0137 |
  |||

* 위의 정보에 따라 이 표에서는 각 가격 책정 계층에 대한 월별 예상 요금 및 해당 가격 책정 계층에 포함된 리소스를 보여 줍니다.

  | 가격 책정 계층 | 월별 USD(미국 동부 2) | vCPU(가상 CPU) | 메모리(GB) | 스토리지(GB) |
  |--------------|-------------------------|--------------------|-------------|--------------|
  | **WS1** | $175.20 | 1 | 3.5 | 250 |
  | **WS2** | $350.40 | 2 | 7 | 250 |
  | **WS3** | $700.80 | 4 | 14 | 250 |
  ||||||

* 위의 정보에 따라 이 표에는 **WS1** 가격 책정 계층을 선택하는 경우의 각 리소스 및 예상 월별 요금이 나열됩니다.

  | 리소스 | Amount | 월별 USD(미국 동부 2) |
  |----------|--------|-------------------------|
  | **vCPU(가상 CPU)** | 1 | $140.16 |
  | **메모리** | 3.5 GB | $35.04 |
  ||||

<a name="storage-transactions"></a>

### <a name="storage-transactions"></a>스토리지 트랜잭션

Azure Logic Apps는 모든 스토리지 작업에 [Azure Storage](../storage/index.yml)를 사용합니다. 다중 테넌트 Azure Logic Apps를 사용하여 모든 스토리지 사용량 및 비용을 논리 앱에 연결합니다. 단일 테넌트 Azure Logic Apps를 사용하여 고유한 Azure [Storage 계정](../azure-functions/storage-considerations.md#storage-account-requirements)을 사용할 수 있습니다. 이 기능은 Logic Apps 데이터에 대해 더 많은 제어와 유연성을 제공합니다.

*상태 저장* 워크플로가 작업을 실행할 때 Azure Logic Apps 런타임은 스토리지 트랜잭션을 수행합니다. 예를 들어 큐는 예약에 사용되고 테이블 및 Blob은 워크플로 상태를 저장하는 데 사용됩니다. 스토리지 비용은 워크플로 콘텐츠에 따라 변경됩니다. 트리거, 작업 및 페이로드가 다르면 스토리지 작업 및 요구 사항도 다릅니다. 스토리지 트랜잭션은 [Azure Storage 가격 책정 모델](https://azure.microsoft.com/pricing/details/storage/)을 따릅니다. 스토리지 비용은 Azure 청구서에 별도로 표시됩니다.

### <a name="tips-for-estimating-storage-needs-and-costs"></a>스토리지 요구 사항 및 비용 예측을 위한 팁

워크플로가 실행할 수 있는 스토리지 작업 수와 해당 비용을 파악하려면 [Logic Apps 스토리지 계산기](https://logicapps.azure.com/calculator)를 사용해 보세요. 샘플 워크플로를 선택하거나 기존 워크플로 정의를 사용할 수 있습니다. 첫 번째 계산은 워크플로의 스토리지 작업 수를 예측합니다. 그런 다음, 이러한 수치를 사용하여 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)에서 가능한 비용을 예상할 수 있습니다.

자세한 내용은 다음 설명서를 검토하세요.

* [단일 테넌트 Azure Logic Apps의 워크플로에 대한 스토리지 요구량 및 비용 추산](estimate-storage-costs.md)
* [Azure Storage 가격 책정 정보](https://azure.microsoft.com/pricing/details/storage/)

<a name="fixed-pricing"></a>

## <a name="ise-pricing-dedicated"></a>ISE 가격 책정(전용)

고정 가격 책정 모델은 전용 [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 실행되는 논리 앱에 적용됩니다. ISE는 생성하는 [ISE 수준 또는 *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)에 따라 달라지는 [통합 서비스 환경 가격](https://azure.microsoft.com/pricing/details/logic-apps)을 사용하여 청구됩니다. 이 가격은 사용 여부에 관계없이 예약된 용량과 전용 리소스에 대한 요금을 지불하므로 다중 테넌트 가격 책정과 다릅니다.

| ISE SKU | Description |
|---------|-------------|
| **Premium** | 기준 단위는 [고정 용량](logic-apps-limits-and-config.md#integration-service-environment-ise)이며, [프리미엄 SKU에 대한 시간당 요금](https://azure.microsoft.com/pricing/details/logic-apps)으로 청구됩니다. 더 많은 처리량이 필요한 경우 ISE를 만들 때 또는 나중에 [배율 단위를 더 추가](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)할 수 있습니다. 각 배율 단위는 [기준 단위 요금의 약 절반에 해당하는 시간당 요금](https://azure.microsoft.com/pricing/details/logic-apps)으로 청구됩니다. <p><p>용량 및 한도 정보는 [Azure Logic Apps의 ISE 한도](logic-apps-limits-and-config.md#integration-service-environment-ise)를 참조하세요. |
| **개발자** | 기준 단위는 [고정 용량](logic-apps-limits-and-config.md#integration-service-environment-ise)이며, [개발자 SKU에 대한 시간당 요금](https://azure.microsoft.com/pricing/details/logic-apps)으로 청구됩니다. 그러나 이 SKU는 SLA(서비스 수준 계약), 스케일 업 기능 또는 재활용 시 중복성이 없으므로, 지연 또는 가동 중지 시간이 발생할 수 있습니다. 백 엔드 업데이트로 인해 서비스가 간헐적으로 중단될 수 있습니다. <p><p>**중요**: 이 SKU는 프로덕션 또는 성능 테스트가 아닌 탐색, 실험, 개발 및 테스트에만 사용해야 합니다. <p><p>용량 및 한도 정보는 [Azure Logic Apps의 ISE 한도](logic-apps-limits-and-config.md#integration-service-environment-ise)를 참조하세요. |
|||

### <a name="included-at-no-extra-cost"></a>추가 비용 없이 포함

| 항목 | 설명 |
|-------|-------------|
| [기본 제공](../connectors/built-in.md) 트리거 및 작업 | **Core** 레이블을 표시하고 논리 앱과 동일한 ISE에서 실행합니다. |
| [표준 커넥터](../connectors/managed.md) <p><p>[엔터프라이즈 커넥터](../connectors/managed.md#enterprise-connectors) | - **ISE** 레이블을 표시하는 관리형 커넥터는 온-프레미스 데이터 게이트웨이 없이 작동하고, 논리 앱과 동일한 ISE에서 실행되도록 특별히 설계되었습니다. ISE 가격 책정에는 사용자가 원하는 만큼의 엔터프라이즈 연결이 포함됩니다. <p><p>- ISE 레이블을 표시하지 않는 커넥터는 단일 테넌트 Azure Logic Apps 서비스에서 실행됩니다. 그러나 ISE 가격 책정에는 ISE에서 실행되는 논리 앱에 대한 이러한 실행이 포함됩니다. |
| [루프](logic-apps-control-flow-loops.md) 내부의 작업 | ISE 가격 책정에는 실행되는 각 루프 주기에 대해 루프에서 실행되는 각 작업이 포함됩니다. <p><p>예를 들어, 목록을 처리하는 작업을 포함하는 "for each" 루프가 있다고 가정합니다. 총 작업 실행 수를 가져오려면 목록 항목 수와 루프의 작업 수를 곱하고 루프를 시작하는 작업을 더합니다. 따라서 10개 항목 목록에 대한 계산은 (10 * 1) + 1이므로 작업 실행은 11개가 됩니다. |
| 다시 시도 횟수 | 가장 기본적인 예외 및 오류를 처리하기 위해 지원되는 트리거 및 작업에 대한 [재시도 정책](logic-apps-exception-handling.md#retry-policies)을 설정할 수 있습니다. ISE 가격 책정에는 원래 요청과 함께 재시도가 포함됩니다. |
| [데이터 보존 및 스토리지 사용량](#data-retention) | ISE의 논리 앱은 데이터 보존 및 스토리지 사용 비용을 발생시키지 않습니다. |
| [통합 계정](#integration-accounts) | 추가 비용 없이 ISE SKU를 기반으로 한 단일 통합 계정 계층에 대한 사용량이 포함됩니다. |
|||

한도 정보는 [Azure Logic Apps의 ISE 한도](logic-apps-limits-and-config.md#integration-service-environment-ise)를 참조하세요.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>통합 계정

[통합 계정](../logic-apps/logic-apps-pricing.md#integration-accounts)은 [EDI](logic-apps-enterprise-integration-b2b.md) 및 [XML 처리](logic-apps-enterprise-integration-xml.md) 기능을 사용하는 B2B 통합 솔루션을 검색, 빌드 및 테스트할 수 있도록 만들어 논리 앱에 연결하는 별도의 리소스입니다.

Azure Logic Apps는 논리 앱이 Azure Logic Apps(다중 테넌트 또는 단일 테넌트)에서 실행되는지 또는 ISE에서 실행되는지에 따라 [가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/) 및 [청구 모델](logic-apps-pricing.md#integration-accounts)에 따라 달라지는 이러한 통합 계정 수준 또는 계층을 제공합니다.

| 계층 | Description |
|------|-------------|
| **기본** | 메시지 처리만 원하는 시나리오 또는 대규모 비즈니스 엔터티와 거래 업체 관계를 맺고 있는 소규모 비즈니스 파트너 역할을 수행하는 시나리오의 경우. <p><p>Logic Apps SLA에서 지원됩니다. |
| **Standard** | 더 복잡한 B2B 관계가 있고 관리해야 하는 엔터티 수가 늘어난 시나리오의 경우. <p><p>Logic Apps SLA에서 지원됩니다. |
| **Free** | 프로덕션 시나리오가 아닌 예비 시나리오에 적합합니다. 이 계층에는 지역 가용성, 처리량 및 사용량에 대한 한도가 있습니다. 예를 들어, 무료 계층은 Azure의 공용 지역(예: 미국 서부 또는 동남 아시아)에서만 사용할 수 있고, [Azure 중국 21Vianet](/azure/china/overview-operations) 또는 [Azure Government](../azure-government/documentation-government-welcome.md)에서는 사용할 수 없습니다. <p><p>**참고**: Logic Apps SLA에서 지원되지 않습니다. |
|||

통합 계정 한도에 대한 자세한 내용은 [Azure Logic Apps의 한도 및 구성](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)을 참조하세요.

* [Azure 구독당 통합 계정 한도](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [통합 계정당 여러 아티팩트에 대한 한도](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 아티팩트에는 거래 업체, 계약, 맵, 스키마, 어셈블리, 인증서, 일괄 처리 구성 등이 포함됩니다.

### <a name="multi-tenant-or-single-tenant-based-logic-apps"></a>다중 테넌트 또는 단일 테넌트 기반 논리 앱

통합 계정은 사용하는 계정 계층을 기반으로 하는 고정된 [통합 계정 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용하여 청구됩니다.

### <a name="ise-based-logic-apps"></a>ISE 기반 논리 앱

추가 비용 없이 ISE는 ISE SKU를 기반으로 한 단일 통합 계정을 포함합니다. 추가 비용으로 ISE에 대한 통합 계정을 더 많이 만들어 [총 ISE 한도](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)까지 사용할 수 있습니다. 이 토픽의 앞부분에서 [ISE 가격 책정 모델](#fixed-pricing)에 대해 자세히 알아보세요.

| ISE SKU | 통합 계정 포함 | 추가 비용 |
|---------|------------------------------|-----------------|
| **Premium** | 단일 [표준](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정 | 표준 계정 최대 19개 추가. 체험 또는 기본 계정은 허용되지 않습니다. |
| **개발자** | 단일 [무료](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정 | 체험 계정이 이미 있는 경우 최대 19개의 표준 계정 또는 체험 계정이 없는 경우 총 20개의 표준 계정이 추가됩니다. 기본 계정은 허용되지 않습니다. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-usage"></a>데이터 보존 및 스토리지 사용량

Azure Logic Apps는 모든 스토리지 작업에 [Azure Storage](../storage/index.yml)를 사용합니다. 워크플로 실행 기록의 입력 및 출력은 논리 앱의 [실행 기록 보존 한도](logic-apps-limits-and-config.md#run-duration-retention-limits)에 따라 저장되고 측정됩니다. 스토리지 사용량을 모니터링하려면 [실행 및 스토리지 사용량에 대한 메트릭 보기](plan-manage-costs.md#monitor-billing-metrics)를 참조하세요.

| 환경 | 메모 |
|-------------|-------|
| **다중 테넌트** | 스토리지 사용량 및 보존은 고정 요금을 사용하여 청구되며 이러한 요금은 **가격 정보** 테이블의 [Logic Apps 가격 책정 페이지](https://azure.microsoft.com/pricing/details/logic-apps)에서 찾을 수 있습니다. |
| **단일 테넌트** | 스토리지 사용량 및 보존 요금은 [Azure Storage 가격 책정 모델](https://azure.microsoft.com/pricing/details/storage/)을 사용하여 청구됩니다. 스토리지 비용은 Azure 청구서에 별도로 표시됩니다. 자세한 내용은 [스토리지 트랜잭션(단일 테넌트)](#storage-transactions)을 참조하세요. |
| **ISE** | 스토리지 사용량 및 보존에는 요금이 부과되지 않습니다. |
|||

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이

[온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-install.md)는 논리 앱 워크플로가 특정 게이트웨이 지원 커넥터를 사용하여 온-프레미스 데이터에 액세스할 수 있도록 만드는 별도의 리소스입니다. 게이트웨이를 통해 실행되는 커넥터 작업에는 요금이 발생하지만 게이트웨이 자체에는 요금이 부과되지 않습니다.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps-or-workflows"></a>사용하지 않도록 설정된 논리 앱 또는 워크플로

사용하지 않도록 설정된 논리 앱(다중 테넌트) 또는 워크플로(단일 테넌트)는 사용하지 않도록 설정된 동안 새 인스턴스를 만들 수 없기 때문에 요금이 발생하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에 대한 비용 계획 및 관리](plan-manage-costs.md)
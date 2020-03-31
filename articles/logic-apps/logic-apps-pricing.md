---
title: 가격 & 청구 모델
description: Azure Logic Apps에 대한 가격 책정 및 청구 모델의 작동 방식에 대한 개요
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 795acd67a8d4a9f8b8b7d78799a92134f249cf8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270460"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure Logic Apps용 가격 책정 모델

[Azure Logic Apps를](../logic-apps/logic-apps-overview.md) 사용하면 클라우드에서 확장할 수 있는 자동화된 통합 워크플로를 만들고 실행할 수 있습니다. 이 문서에서는 Azure Logic Apps에 대한 청구 및 가격 책정이 어떻게 작동하는지 설명합니다. 가격 책정 요금은 [논리 앱 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조하십시오.

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>소비 가격 책정 모델

공용 또는 "글로벌" Azure Logic Apps 서비스에서 실행되는 새 논리 앱의 경우 사용한 것에 대해서만 비용을 지불합니다. 이러한 논리 앱은 사용량 기반 계획 및 가격 책정 모델을 사용합니다. 논리 앱에서 각 단계는 작업이며 Azure Logic Apps는 논리 앱에서 실행되는 모든 작업을 측정합니다.

예를 들어, 작업에는 다음이 포함됩니다.

* 트리거( 특수 작업)입니다. 모든 논리 앱은 첫 번째 단계로 트리거가 필요합니다.
* ["기본 제공" 또는](../connectors/apis-list.md#built-in) HTTP, Azure Functions 및 API 관리 에 대한 호출 등과 같은 기본 작업
* Outlook 365, Dropbox 등과 같은 [관리형 커넥터에](../connectors/apis-list.md#managed-connectors) 대한 호출
* 루프, 조건문 등과 같은 흐름 단계를 제어합니다.

[표준 커넥터는 표준](../connectors/apis-list.md#managed-connectors) [커넥터 가격으로](https://azure.microsoft.com/pricing/details/logic-apps)충전됩니다. 일반적으로 사용 가능한 [엔터프라이즈 커넥터는](../connectors/apis-list.md#managed-connectors) [엔터프라이즈 커넥터 가격으로](https://azure.microsoft.com/pricing/details/logic-apps)요금이 부과되고 공용 미리 보기 엔터프라이즈 커넥터는 표준 커넥터 [가격으로](https://azure.microsoft.com/pricing/details/logic-apps)청구됩니다.

[트리거](#triggers) 및 [작업에](#actions)대한 청구 작동 방식에 대해 자세히 알아보세요.

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>고정 가격 책정 모델

[ *ISE(통합 서비스 환경)는* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Azure 가상 네트워크의 리소스에 액세스할 수 있는 논리 앱을 만들고 실행하는 격리된 방법을 제공합니다. ISE 내에서 실행되는 새 논리 앱의 경우 다음 기능에 대해 [고정월별 요금을](https://azure.microsoft.com/pricing/details/logic-apps) 지불합니다.

* [기본 제공](../connectors/apis-list.md#built-in) 트리거 및 작업

  ISE 내에서 기본 제공 트리거 및 작업은 **Core** 레이블을 표시하고 논리 앱과 동일한 ISE에서 실행됩니다.

* [표준](../connectors/apis-list.md#managed-connectors) 커넥터 및 [엔터프라이즈](../connectors/apis-list.md#enterprise-connectors) 커넥터(원하는 만큼 엔터프라이즈 연결)

   **ISE** 레이블을 표시하는 표준 및 엔터프라이즈 커넥터는 논리 앱과 동일한 ISE에서 실행됩니다. ISE 레이블을 표시하지 않는 커넥터는 글로벌 로직 앱 서비스에서 실행됩니다. 고정 월별 가격 책정은 ISE에서 실행되는 논리 앱과 함께 사용할 때 전역 서비스에서 실행되는 커넥터에도 적용됩니다.

* [ISE SKU에](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)따라 추가 비용 없이 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 사용:

  * **프리미엄 SKU**: 단일 [표준 계층](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정

  * **개발자 SKU**: 단일 [프리 티어](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정

  각 ISE SKU는 총 5개의 통합 계정으로 제한됩니다. 추가 비용의 경우 ISE SKU를 기반으로 더 많은 통합 계정을 가질 수 있습니다.

  * **프리미엄 SKU**: 최대 4 개의 표준 계정. 무료 또는 기본 계정이 없습니다.

  * **개발자 SKU**: 최대 4 개의 표준 계정 또는 최대 5 개의 총 표준 계정. 기본 계정이 없습니다.

  통합 계정 제한에 대한 자세한 내용은 [논리 앱 제한 및 구성을](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)참조하십시오. [통합 계정 계층 및 해당 가격 모델에](#integration-accounts) 대해 자세히 알아볼 수 있습니다.

프리미엄 ISE SKU를 선택하면 기본 장치에 고정 용량이 있습니다. 처리량이 더 필요한 경우 생성 중이거나 나중에 [배율 단위를 더 추가할](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)수 있습니다. 개발자 ISE SKU에는 더 많은 축척 단위를 추가할 수 있는 기능이 없습니다. ISE에서 실행되는 논리 앱에는 데이터 보존 비용이 발생하지 않습니다.

가격 책정 요금은 [논리 앱 가격 책정을](https://azure.microsoft.com/pricing/details/logic-apps)참조하십시오.

<a name="connectors"></a>

## <a name="connectors"></a>커넥터

Azure Logic Apps 커넥터는 [트리거,](#triggers) [작업](#actions)또는 둘 다를 제공하여 클라우드 또는 온프레미스의 로직 앱이 앱, 서비스 및 시스템에 액세스하는 데 도움이 됩니다. 커넥터는 표준 또는 엔터프라이즈로 분류됩니다. 이러한 커넥터에 대한 개요는 [Azure 논리 앱에 대한 커넥터를](../connectors/apis-list.md)참조하십시오. 논리 앱에서 사용할 REST API에 미리 빌드된 커넥터를 사용할 수 없는 경우 해당 REST API 주위에 래퍼인 [사용자 지정 커넥터를](https://docs.microsoft.com/connectors/custom-connectors)만들 수 있습니다. 사용자 지정 커넥터는 표준 커넥터로 청구됩니다. 다음 섹션에서는 트리거 및 작업에 대한 청구가 작동하는 방식에 대한 자세한 정보를 제공합니다.

<a name="triggers"></a>

## <a name="triggers"></a>트리거

트리거는 특정 이벤트가 발생할 때 논리 앱 인스턴스를 만드는 특수 작업입니다. 트리거는 논리 앱이 계량되는 방식에 영향을 주는 다양한 방법으로 작동합니다. Azure 논리 앱에 존재하는 다양한 종류의 트리거는 다음과 같습니다.

* **폴링 트리거**: 이 트리거는 논리 앱 인스턴스를 만들고 워크플로를 시작하기 위한 기준을 충족하는 메시지에 대한 끝점을 지속적으로 검사합니다. 논리 앱 인스턴스를 만들지 않은 경우에도 Logic Apps는 각 폴링 요청을 실행으로 측정합니다. 폴링 간격을 설정하려면 논리 앱 디자이너를 통해 트리거를 설정합니다.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 트리거**: 이 트리거는 클라이언트가 특정 끝점으로 요청을 보낼 때까지 기다립니다. 웹후크 엔드포인트로 전송된 각 요청은 작업 실행으로 계산됩니다. 예를 들어, 요청 및 HTTP 웹후크 트리거는 둘 다 웹후크 트리거입니다.

* **되풀이 트리거**: 이 트리거는 트리거에서 설정한 되풀이 간격을 기반으로 논리 앱 인스턴스를 만듭니다. 예를 들어 3일마다 또는 보다 복잡한 일정에 따라 실행되는 되풀이 트리거를 설정할 수 있습니다.

<a name="actions"></a>

## <a name="actions"></a>동작

Azure 논리 앱 미터는 HTTP와 같은 "기본 제공" 작업을 네이티브 작업으로 측정합니다. 예를 들어 기본 제공 작업에는 HTTP 호출, Azure Functions 또는 API 관리의 호출 및 조건, 루프 및 switch 문과 같은 흐름 단계를 제어합니다. 각 작업에는 고유한 작업 유형이 있습니다. 예를 들어 커넥터를 호출하는 작업에는 "ApiConnection" 형식이 [있습니다.](https://docs.microsoft.com/connectors) 이러한 커넥터는 표준 또는 엔터프라이즈 커넥터로 분류되며 해당 [가격에](https://azure.microsoft.com/pricing/details/logic-apps)따라 미터화됩니다. *미리 보기의* 엔터프라이즈 커넥터는 표준 커넥터로 충전됩니다.

Azure Logic Apps는 성공 및 실패한 모든 작업을 실행으로 측정합니다. 그러나 논리 앱은 다음과 같은 작업을 측정하지 않습니다.

* 충족되지 않은 조건으로 인해 건너뛰는 작업
* 완료하기 전에 논리 앱이 중지되어 실행되지 않는 작업

루프 내에서 실행되는 작업의 경우 Azure Logic Apps는 루프의 각 주기에 대한 각 작업을 계산합니다. 예를 들어 목록을 처리하는 "for each" 루프가 있다고 가정합니다. Logic Apps는 목록 항목의 수를 루프의 작업 수와 곱하여 해당 루프에서 작업을 측정하고, 루프를 시작하는 작업을 추가합니다. 따라서 10개 항목 목록에 대한 계산은 (10 * 1) + 1이며, 이로 인해 11개의 작업 실행이 발생합니다.

## <a name="disabled-logic-apps"></a>비활성화된 논리 앱

비활성화된 논리 앱은 비활성화된 동안 새 인스턴스를 만들 수 없으므로 요금이 청구되지 않습니다. 논리 앱을 사용 중지하면 현재 실행 중인 인스턴스가 완전히 중지되기까지 조금 시간이 걸릴 수 있습니다.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>통합 계정

[고정 가격 책정 모델은](https://azure.microsoft.com/pricing/details/logic-apps) Azure Logic Apps에서 [B2B 및 EDI](logic-apps-enterprise-integration-b2b.md) 및 [XML 처리](logic-apps-enterprise-integration-xml.md) 기능을 추가 비용 없이 탐색, 개발 및 테스트할 수 있는 통합 [계정에](logic-apps-enterprise-integration-create-integration-account.md) 적용됩니다. 각 Azure 구독에는 최대 [특정 통합 계정 제한이](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)있을 수 있습니다. 각 통합 계정은 거래 파트너, 계약, 맵, 스키마, 어셈블리, 인증서, 일괄 처리 구성 등을 포함하는 [아티팩트의 특정 한도까지](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)저장할 수 있습니다.

Azure 논리 앱은 무료, 기본 및 표준 통합 계정을 제공합니다. 기본 및 표준 계층은 논리 앱 서비스 수준 계약(SLA)에서 지원되지만 프리 티어는 SLA에서 지원되지 않으며 처리량 및 사용량에 제한이 있습니다. 프리 티어 통합 계정을 제외하고 각 Azure 리전에서 두 개 이상의 통합 계정을 가질 수 있습니다. 가격 책정 요금은 [논리 앱 가격 책정을](https://azure.microsoft.com/pricing/details/logic-apps/)참조하십시오.

통합 서비스 [ *환경(ISE)* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)( [프리미엄 또는 개발자)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)중 하나가 있는 경우 ISE에는 총 5개의 통합 계정이 있을 수 있습니다. 고정 가격 책정 모델이 ISE에 어떻게 작동하는지 알아보려면 이 항목의 이전 [고정 가격 책정 모델](#fixed-pricing) 섹션을 참조하세요. 가격 책정 요금은 [논리 앱 가격 책정을](https://azure.microsoft.com/pricing/details/logic-apps)참조하십시오.

무료, 기본 또는 표준 통합 계정 중에서 선택하려면 다음 사용 사례 설명을 검토하십시오.

* **무료**: 프로덕션 시나리오가 아닌 예비 시나리오를 시도하려는 경우

* **기본**: 메시지 처리만하거나 대규모 비즈니스 엔터티와 거래 파트너 관계를 맺은 소규모 비즈니스 파트너역할을 하려는 경우

* **표준**: 더 복잡한 B2B 관계가 있고 관리해야 하는 엔터티 수가 증가한 경우

<a name="data-retention"></a>

## <a name="data-retention"></a>데이터 보존

통합 서비스 환경(ISE)에서 실행되는 논리 앱을 제외하고 논리 앱의 실행 기록에 저장된 모든 입력 및 출력은 논리 앱의 [실행 보존 기간에](logic-apps-limits-and-config.md#run-duration-retention-limits)따라 청구됩니다. ISE에서 실행되는 논리 앱에는 데이터 보존 비용이 발생하지 않습니다. 가격 책정 요금은 [논리 앱 가격 책정을](https://azure.microsoft.com/pricing/details/logic-apps)참조하십시오.

논리 앱의 저장소 사용량을 모니터링하는 데 도움이 될 수 있습니다.

* 논리 앱이 매월 사용하는 GB의 저장소 단위 수를 봅니다.
* 논리 앱의 실행 기록에서 특정 작업의 입력 및 출력에 대한 크기를 봅니다.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>논리 앱 저장소 사용량 보기

1. Azure Portal에서 논리 앱을 찾아서 엽니다.

1. 논리 앱의 메뉴에서 **모니터링**에서 **메트릭을 선택합니다.**

1. 오른쪽 창에서 **메트릭** 목록에서 **차트 제목**아래에 저장소 사용량 실행에 대한 **청구 사용량을 선택합니다.**

   이 메트릭은 청구되는 월별 GB의 스토리지 소비 단위 수를 제공합니다.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>작업 입력 및 출력 크기 보기

1. Azure Portal에서 논리 앱을 찾아서 엽니다.

1. 논리 앱의 메뉴에서 **개요를**선택합니다.

1. 오른쪽 창에서 **실행 기록**아래에서 확인하려는 입력 및 출력이 있는 실행을 선택합니다.

1. **논리 앱 실행에서** **세부 정보 실행을**선택합니다.

1. Logic **앱 실행 창에서** 각 작업의 상태및 기간을 나열하는 작업 테이블에서 보려는 작업을 선택합니다.

1. 논리 **앱 작업** 창에서 해당 작업의 입력 및 출력에 대한 크기를 입력 **링크** 및 출력 링크 아래에 각각 **표시합니다.**

## <a name="next-steps"></a>다음 단계

* [Azure 논리 앱에 대해 자세히 알아보기](logic-apps-overview.md)
* [첫 번째 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)

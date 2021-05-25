---
title: 연결된 Log Analytics 작업 영역에 대해 지원되는 지역
description: 이 문서에서는 Azure Automation의 특정 기능과 관련된 Automation 계정과 Log Analytics 작업 영역 간에 지원되는 지역 매핑을 설명합니다.
ms.date: 04/01/2021
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 4ac2a575c9fe551d5b1b396ab06b2735a749f9da
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221877"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>연결된 Log Analytics 작업 영역에 대해 지원되는 지역

Azure Automation에서 서버 및 가상 머신에 대한 업데이트 관리, 변경 내용 추적 및 인벤토리, 작업 시간 외 VM 시작/중지 기능을 사용하도록 설정할 수 있습니다. 이러한 기능은 Log Analytics 작업 영역에 종속되므로 작업 영역을 Automation 계정과 연결해야 합니다. 그러나 특정 지역만 함께 연결할 수 있습니다. 일반적으로 이러한 기능을 사용하지 않는 작업 영역에 Automation 계정을 연결하려는 경우에는 매핑이 적용되지 *않습니다*.

여기에 설명된 매핑은 Log Analytics 작업 영역을 Automation 계정에 연결하는 경우에만 적용됩니다. Automation 계정에 연결된 작업 영역에 연결한 VM(가상 머신)에는 적용되지 않습니다. VM은 지정된 Log Analytics 작업 영역에서 지원하는 지역으로 제한되지 않습니다. 모든 지역이 지원됩니다. VM이 다른 지역에 있는 경우 주, 지역 및 국가 규정 요구 사항 또는 회사의 규정 준수 요구 사항에 영향을 줄 수 있습니다. 다른 지역에 VM이 있으면 데이터 대역폭 요금이 부과될 수도 있습니다.

VM을 다른 지역의 작업 영역에 연결하기 전에 요구 사항 및 잠재적인 비용을 검토하여 법률 및 비용의 영향을 확인하고 이해해야 합니다.

이 문서에서는 Automation 계정에서 이러한 기능을 사용하도록 설정하고 사용할 수 있도록 지원되는 매핑을 제공합니다.

자세한 내용은 [Log Analytics 작업 영역 및 Automation 계정](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)을 참조하세요.

## <a name="supported-mappings"></a>지원되는 매핑

> [!NOTE]
> 다음 표에 표시된 것처럼 Log Analytics와 Azure Automation 사이에는 하나의 매핑만 있을 수 있습니다.

다음 표에 지원되는 매핑이 나와 있습니다.

|**Log Analytics 작업 영역 지역**|**Azure Automation 지역**|
|---|---|
|**US**||
|EastUS<sup>1</sup>|EastUS2|
|EastUS2<sup>2</sup>|EastUS|
|WestUS|WestUS|
|WestUS2|WestUS2|
|NorthCentralUS|NorthCentralUS|
|CentralUS|CentralUS|
|SouthCentralUS|SouthCentralUS|
|WestCentralUS|WestCentralUS|
|**브라질**||
|BrazilSouth|BrazilSouth|
|**캐나다**||
|CanadaCentral|CanadaCentral|
|**중국**||
|ChinaEast2<sup>3</sup>|ChinaEast2|
|**아시아 태평양**||
|EastAsia|EastAsia|
|SoutheastAsia|SoutheastAsia|
|**인도**||
|CentralIndia|CentralIndia|
|**일본**||
|JapanEast|JapanEast|
|**오스트레일리아**||
|AustraliaEast|AustraliaEast|
|AustraliaSoutheast|AustraliaSoutheast|
|**한국**||
|KoreaCentral|KoreaCentral|
|**노르웨이**||
|NorwayEast|NorwayEast|
|**유럽**||
|NorthEurope|NorthEurope|
|WestEurope|WestEurope|
|**프랑스**||
|FranceCentral|FranceCentral|
|**영국**
|UKSouth|UKSouth|
|**스위스**||
|SwitzerlandNorth|SwitzerlandNorth|
|**아랍에미리트연합국**||
|UAENorth|UAENorth|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>3</sup>|USGovArizona|

<sup>1</sup> Automation 계정에 대한 Log Analytics 작업 영역의 EastUS 매핑은 정확한 지역 간 매핑이 아니라 올바른 매핑입니다.

<sup>2</sup> Automation 계정에 대한 Log Analytics 작업 영역의 EastUS2 매핑은 정확한 지역 간 매핑이 아니라 올바른 매핑입니다.

<sup>3</sup> 이 지역에서는 업데이트 관리만 지원되며 변경 내용 추적 및 인벤토리 같은 다른 기능을 현재 사용할 수 없습니다.

## <a name="unlink-a-workspace"></a>작업 영역 연결 해제

Automation 계정을 Log Analytics작업 영역에 더 이상 통합하지 않기로 결정할 경우 Azure Portal에서 직접 계정 연결을 해제할 수 있습니다. 계속하기 전에 먼저 사용 중인 경우 업데이트 관리, 변경 내용 추적 및 인벤토리, 작업 시간 외 VM 시작/중지를 [제거](move-account.md#remove-features)해야 합니다. 제거하지 않으면 연결 해제 작업을 완료할 수 없습니다.

기능이 제거되면 아래 단계에 따라 Automation 계정 연결을 해제할 수 있습니다.

> [!NOTE]
> 이전 버전의 Azure SQL 모니터링 솔루션을 포함한 일부 기능에서 Automation 자산을 만들었을 수 있으며, 작업 영역을 연결 해제하기 전에 제거해야 할 수도 있습니다.

1. Azure Portal에서 Automation 계정을 엽니다. Automation 계정 페이지의 **관련 리소스** 아래에서 **연결된 작업 영역** 을 선택합니다.

2. 작업 영역 연결 해제 페이지에서 **작업 영역 연결 해제** 를 선택합니다. 계속 진행할지 확인하는 메시지가 나타납니다.

3. Azure Automation이 Log Analytics 작업 영역에서 계정 연결을 해제하는 동안 메뉴의 **알림** 에서 진행 상황을 추적할 수 있습니다.

4. 업데이트 관리를 사용한 경우 필요에 따라 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

    * 일정 업데이트: 각각에는 사용자가 만든 업데이트 배포와 일치하는 이름이 있습니다.
    * 기능에 대해 생성된 Hybrid Worker 그룹: 각각의 이름은 `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`과 비슷합니다.

5. 작업 시간 외 VM 시작/중지를 사용한 경우 필요에 따라 더 이상 필요하지 않은 다음 항목을 제거할 수 있습니다.

    * VM runbook 시작 및 중지 일정
    * VM runbook 시작 및 중지
    * variables

또는 작업 영역 내 Automation 계정에서 작업 영역의 연결을 해제할 수 있습니다.

1. 작업 영역의 **관련 리소스** 아래에서 **Automation 계정** 을 선택합니다.
2. Automation 계정 페이지에서 **계정 연결 해제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [업데이트 관리 개요](../update-management/overview.md)에서 업데이트 관리에 대해 알아봅니다.
* [변경 내용 추적 및 인벤토리 개요](../change-tracking/overview.md)에서 변경 내용 추적 및 인벤토리에 대해 알아봅니다.
* [작업 시간 외 VM 시작/중지](../automation-solution-vm-management.md)에서 작업 시간 외 VM 시작/중지에 대해 알아봅니다.
---
title: 연결된 Log Analytics 작업 영역에 대해 지원되는 지역
description: 이 문서에서는 Automation 계정과 Log Analytics 작업 영역 간에 지원되는 지역 매핑을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.custom: references_regions
ms.openlocfilehash: d594d5a9dd263817023d2b5987c03aa50828191c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84191851"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>연결된 Log Analytics 작업 영역에 대해 지원되는 지역

Azure Automation에서 VM에 대한 업데이트 관리, 변경 내용 추적 및 인벤토리, 작업 시간 외 VM 시작/중지 기능을 사용하도록 설정할 수 있습니다. 그러나 특정 Azure 지역에서만 구독의 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지역 매핑은 Automation 계정 및 Log Analytics 작업 영역에만 적용됩니다. Log Analytics 작업 영역과 Automation 계정이 동일한 구독에 있어야 하지만 동일한 지역에 배포된 다른 리소스 그룹에 있을 수 있습니다. 자세한 내용은 [Log Analytics 작업 영역 및 Automation 계정](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)을 참조하세요.

## <a name="supported-mappings"></a>지원되는 매핑

다음 표에 지원되는 매핑이 나와 있습니다.

|**Log Analytics 작업 영역 지역**|**Azure Automation 지역**|
|---|---|
|**US**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**캐나다**||
|CanadaCentral|CanadaCentral|
|**아시아 태평양**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**유럽**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> Automation 계정에 대한 Log Analytics 작업 영역의 EastUS 매핑은 정확한 지역 간 매핑이 아니라 올바른 매핑입니다.

<sup>2</sup> 용량 제한으로 인해 새 리소스를 만들 때 지역을 사용할 수 없습니다. 여기에는 Automation 계정 및 Log Analytics 작업 영역이 포함됩니다. 그러나 지역에서 기존에 연결된 리소스는 계속 작동해야 합니다.

## <a name="unlink-a-workspace"></a>작업 영역 연결 해제

Automation 계정을 Log Analytics작업 영역에 더 이상 통합하지 않기로 결정할 경우 Azure Portal에서 직접 계정 연결을 해제할 수 있습니다. 계속하기 전에 먼저 사용 중인 경우 업데이트 관리, 변경 내용 추적 및 인벤토리, 작업 시간 외 VM 시작/중지를 [제거](move-account.md#remove-features)해야 합니다. 제거하지 않으면 연결 해제 작업을 완료할 수 없습니다. 

기능이 제거되면 아래 단계에 따라 Automation 계정 연결을 해제할 수 있습니다.

> [!NOTE]
> 이전 버전의 Azure SQL 모니터링 솔루션을 포함한 일부 기능에서 Automation 자산을 만들었을 수 있으며, 작업 영역을 연결 해제하기 전에 제거해야 할 수도 있습니다.

1. Azure Portal에서 Automation 계정을 엽니다. Automation 계정 페이지의 **관련 리소스** 아래에서 **연결된 작업 영역**을 선택합니다.

2. 작업 영역 연결 해제 페이지에서 **작업 영역 연결 해제**를 선택합니다. 계속 진행할지 확인하는 메시지가 나타납니다.

3. Azure Automation이 Log Analytics 작업 영역에서 계정 연결을 해제하는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

4. 업데이트 관리를 사용한 경우 필요에 따라 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

    * 일정 업데이트: 각각에는 사용자가 만든 업데이트 배포와 일치하는 이름이 있습니다.
    * 기능에 대해 생성된 Hybrid Worker 그룹: 각각의 이름은 `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`과 비슷합니다.

5. 작업 시간 외 VM 시작/중지를 사용한 경우 필요에 따라 더 이상 필요하지 않은 다음 항목을 제거할 수 있습니다.

    * VM runbook 시작 및 중지 일정
    * VM runbook 시작 및 중지
    * variables

또는 작업 영역 내 Automation 계정에서 작업 영역의 연결을 해제할 수 있습니다.

1. 작업 영역의 **관련 리소스** 아래에서 **Automation 계정**을 선택합니다. 
2. Automation 계정 페이지에서 **계정 연결 해제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [업데이트 관리 개요](../automation-update-management.md)에서 업데이트 관리에 대해 알아봅니다.
* [변경 내용 추적 및 인벤토리 개요](../change-tracking.md)에서 변경 내용 추적 및 인벤토리에 대해 알아봅니다.
* [작업 시간 외 VM 시작/중지](../automation-solution-vm-management.md)에서 작업 시간 외 VM 시작/중지에 대해 알아봅니다.

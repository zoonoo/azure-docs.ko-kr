---
title: Azure Automation 및 Log Analytics 작업 영역 매핑
description: 이 문서에서는 Automation 계정과 Log Analytics 작업 영역 간에 허용 되는 매핑을 설명 합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6b00b25dc4eaea5bc8a3f5fbd42389aff501f14a
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901019"
---
# <a name="workspace-mappings"></a>작업 영역 매핑:

Azure Automation에서 "업데이트 관리", "변경 내용 추적 및 인벤토리" 및 "시간 외 Vm 시작/중지" 솔루션을 사용 하도록 설정할 수 있습니다. 그러나이 작업을 수행 하는 경우에는 구독에서 Log Analytics 작업 영역 및 Automation 계정을 연결 하는 데 특정 영역만 지원 됩니다. 이 매핑은 Automation 계정 및 Log Analytics 작업 영역에만 적용 됩니다. Log Analytics 작업 영역 및 Automation 계정은 동일한 구독에 있어야 하지만 동일한 지역에 배포 된 다른 리소스 그룹에 있을 수 있습니다.

자세한 내용은 [Log Analytics 작업 영역 및 Automation 계정](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)을 참조 하세요.

## <a name="supported-mappings"></a>지원 되는 매핑

다음 표에 지원되는 매핑이 나와 있습니다.

|**Log Analytics 작업 영역 영역**|**Azure Automation 지역**|
|---|---|
|**US**||
|E Us<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canada**||
|CanadaCentral|CanadaCentral|
|**아시아 태평양**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Europe**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> Log Analytics 작업 영역을 Automation 계정으로 매핑하는 것은 정확한 지역 간 매핑이 아니라 올바른 매핑입니다.

<sup>2</sup> 용량 제한은으로 인해 새 리소스를 만들 때 지역을 사용할 수 없습니다. 여기에는 Automation 계정 및 Log Analytics 작업 영역이 포함 됩니다. 그러나 지역에서 기존에 연결 된 리소스는 계속 작동 해야 합니다.

## <a name="unlink-a-workspace"></a>작업 영역 연결 해제

Automation 계정을 Log Analytics 작업 영역에 더 이상 통합 하지 않기로 결정 한 경우 Azure Portal에서 직접 계정 연결을 해제할 수 있습니다. 계속 하기 전에 "업데이트 관리", "변경 내용 추적 및 인벤토리" 및 "사용 하지 않는 시간 동안 Vm 시작/중지"를 먼저 제거 해야 합니다. 제거 하지 않으면 연결 끊기 작업을 완료할 수 없습니다. 제거 하는 데 필요한 단계를 이해 하기 위해 사용 하도록 설정 하는 각 솔루션에 대 한 문서를 검토 합니다.

제거 후 다음 단계를 수행 하 여 Automation 계정의 연결을 해제할 수 있습니다.

> [!NOTE]
> 이전 버전의 Azure SQL 모니터링 솔루션을 포함 한 일부 솔루션은 자동화 자산을 만들었을 수 있으며 작업 영역 연결을 끊기 전에 제거 해야 할 수 있습니다.

1. Azure Portal에서 Automation 계정을 엽니다. **Automation 계정** 페이지의 **관련 리소스**에서 **연결 된 작업 영역** 을 선택 합니다.

2. **작업 영역 연결 해제** 페이지에서 **작업 영역 연결 해제**를 선택 합니다. 계속 하 시겠습니까? 확인 메시지가 표시 됩니다.

3. Azure Automation이 Log Analytics 작업 영역의 연결을 끊으려고 하는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

4. "업데이트 관리"을 사용 하는 경우 필요에 따라 제거 후 더 이상 필요 하지 않은 다음 항목을 제거할 수 있습니다.

    * 업데이트 일정: 각에는 사용자가 만든 업데이트 배포와 일치 하는 이름이 있습니다.
    * 솔루션에 대해 생성 된 Hybrid worker 그룹: 각각은와 유사 `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`합니다.

5. "업무 시간 외 Vm 시작/중지"를 사용한 경우 필요 하지 않은 다음 항목을 제거한 후 제거할 수 있습니다.

    * VM runbook 시작 및 중지 일정
    * VM runbook 시작 및 중지
    * variables

또는 작업 영역 내 Automation 계정에서 작업 영역의 연결을 끊을 수 있습니다.

1. 작업 영역에서 **관련 리소스**아래에 있는 **Automation 계정** 을 선택 합니다. 
2. **Automation 계정** 페이지에서 **계정 연결 해제**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

* "업데이트 관리" 및 "변경 내용 추적 및 인벤토리"를 사용 하 여 시작 하는 방법을 알아봅니다.

    * [가상 컴퓨터](../automation-onboard-solutions-from-vm.md)에서.
    * [Automation 계정](../automation-onboard-solutions-from-automation-account.md)에서.
    * [여러 컴퓨터를 검색](../automation-onboard-solutions-from-browse.md)하는 경우
    * [Runbook](../automation-onboard-solutions.md)에서.

* "사용 하지 않는 시간 동안 Vm 시작/중지"를 사용 하 여 시작 하는 방법을 알아봅니다.

    * [업무 시간 외 Vm 시작/중지 개요](../automation-solution-vm-management.md).

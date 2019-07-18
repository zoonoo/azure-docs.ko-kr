---
title: Azure Automation 및 Log Analytics 작업 영역 매핑
description: 이 문서에는 솔루션을 지 원하는 수는 Automation 계정과 Log Analytics 작업 영역 간의 매핑에 대해 설명 합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9faa261f4b6293d778c5da685f630a21fd88f600
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478572"
---
# <a name="workspace-mappings"></a>작업 영역 매핑

작업이 없는 동안 업데이트 관리, 변경 내용 추적 및 인벤토리 또는 Vm 시작/중지와 같은 솔루션을 사용 하도록 설정 하면, Log Analytics 작업 영역 및 Automation 계정 연결에 대 한 특정 지역 에서만 지원 됩니다. 이 매핑에 Automation 계정 및 Log Analytics 작업 영역에만 적용 됩니다. Automation 계정 또는 Log Analytics 작업 영역에 보고 하는 리소스를 다른 지역에 있을 수 있습니다.

## <a name="supported-mappings"></a>지원 되는 매핑

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

<sup>1</sup> EastUS 매핑 Automation 계정에 Log Analytics 작업 영역에 대 한 정확한 지역 매핑이 되지 않지만 올바른 매핑이 있습니다.

<sup>2</sup> 용량 제한으로 인해 지역을 사용할 수 없는 새 리소스를 만들 때. Automation 계정 및 Log Analytics 작업 영역을 포함 합니다. 그러나 지역에서 기존 연결 된 리소스는 계속 작동 합니다.

## <a name="unlink-workspace"></a>작업 영역 연결 해제

더 이상 Log Analytics 작업 영역과 Automation 계정을 통합 하려는 경우 Azure portal에서 직접 계정 연결을 해제할 수 없습니다. 계속 진행 하기 전에 먼저 사용 하는 경우 외 솔루션 중 업데이트 관리, 변경 내용 추적 및 인벤토리 또는 Vm 시작/중지를 제거 해야 합니다. 해도 제거 되지 않습니다 하는 경우이 프로세스를 계속에서 차단 됩니다. 가져온 특정 솔루션에 대한 문서를 검토하여 제거에 필요한 단계를 이해하세요.

이러한 솔루션을 제거한 후에 다음 단계에 따라 Automation 계정 연결을 해제할 수 있습니다.

> [!NOTE]
> 이전 버전의 Azure SQL 모니터링 솔루션을 포함한 일부 솔루션에서 자동화 자산을 만들었을 수 있으며, 작업 영역을 연결 해제하기 전에 제거해야 할 수도 있습니다.

1. Azure Portal에서 Automation 계정을 열고 Automation 계정 페이지에서 왼쪽의 **관련된 리소스** 섹션 아래의 **연결된 작업 영역**을 선택합니다.

2. 작업 영역 연결 해제 페이지에서 **작업 영역 연결 해제**를 클릭합니다. 계속 하려면 확인 프롬프트를 받게 됩니다.

3. Azure Automation이 Log Analytics 작업 영역의 연결을 끊으려고 하는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

업데이트 관리 솔루션을 사용한 경우 솔루션을 제거한 후 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

* 업데이트 일정 - 각 일정에는 사용자가 만든 업데이트 배포와 일치하는 이름이 지정됩니다.

* 솔루션용으로 만든 Hybrid Worker 그룹 - 각 그룹에는 machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8과 비슷하게 이름이 지정됩니다.

작업이 없는 동안 VM 시작/중지를 사용한 경우 솔루션을 제거한 후 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

* VM runbook 시작 및 중지 일정
* VM runbook 시작 및 중지
* variables

또는 수 연결을 끊을 수도 작업 영역이 Automation 계정에서 Log Analytics 작업 영역에서 합니다. 작업 영역 선택 **Automation 계정** 아래에서 **관련 된 리소스**합니다. Automation 계정 페이지에서 선택 **계정을 연결 해제**합니다.

## <a name="next-steps"></a>다음 단계

에 대해 알아봅니다 어떻게 등록 하려면 다음 해결 방법:

관리 및 변경 내용 추적을 업데이트 하 고 인벤토리:

* [가상 머신](../automation-onboard-solutions-from-vm.md)
* 사용자 [Automation 계정](../automation-onboard-solutions-from-automation-account.md)
* 때 [여러 컴퓨터를 검색 합니다.](../automation-onboard-solutions-from-browse.md)
* [runbook](../automation-onboard-solutions.md)

작업이 없는 동안 VM 시작/중지

* [작업 시간 외 Vm 시작/중지를 배포 합니다.](../automation-solution-vm-management.md)
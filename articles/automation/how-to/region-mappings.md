---
title: Azure Automation 및 Log Analytics 작업 영역 매핑
description: 이 문서에서는 솔루션을 지원 하기 위해 Automation 계정과 Log Analytics 작업 영역 간에 허용 되는 매핑을 설명 합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd4500b4ef6492f0b6499bb1e9aa1a773313e860
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498361"
---
# <a name="workspace-mappings"></a>작업 영역 매핑

업데이트 관리, 변경 내용 추적 및 인벤토리 또는 작업 시간 외 Vm 시작/중지 솔루션을 사용 하도록 설정 하는 경우 Log Analytics 작업 영역 및 Automation 계정을 연결 하는 데 특정 영역만 지원 됩니다. 이 매핑은 Automation 계정 및 Log Analytics 작업 영역에만 적용 됩니다. Automation 계정 또는 Log Analytics 작업 영역에 보고 하는 리소스는 다른 지역에 있을 수 있습니다.

## <a name="supported-mappings"></a>지원 되는 매핑

다음 표에 지원되는 매핑이 나와 있습니다.

|**Log Analytics 작업 영역 지역**|**Azure Automation 지역**|
|---|---|
|**US**||
|E Us<sup>1</sup>|EastUS2|
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

<sup>1</sup> Log Analytics 작업 영역을 Automation 계정에 매핑하는 것은 지역 매핑의 정확한 지역이 아니지만 올바른 매핑입니다.

<sup>2</sup> 용량 제한은 인해 새 리소스를 만들 때 지역을 사용할 수 없습니다. 여기에는 Automation 계정 및 Log Analytics 작업 영역이 포함 됩니다. 그러나 지역에서 기존에 연결 된 리소스는 계속 작동 해야 합니다.

## <a name="unlink-workspace"></a>작업 영역 연결 해제

Automation 계정을 Log Analytics 작업 영역에 더 이상 통합 하지 않기로 결정 한 경우 Azure Portal에서 직접 계정 연결을 해제할 수 있습니다. 계속 하기 전에 업데이트 관리, 변경 내용 추적 및 인벤토리를 제거 하거나, 사용 하는 경우 업무 시간 외 Vm 시작/중지 솔루션을 사용 하는 경우 해당 솔루션을 제거 해야 합니다. 제거 하지 않으면이 프로세스를 계속 진행할 수 없습니다. 가져온 특정 솔루션에 대한 문서를 검토하여 제거에 필요한 단계를 이해하세요.

이러한 솔루션을 제거한 후에 다음 단계에 따라 Automation 계정 연결을 해제할 수 있습니다.

> [!NOTE]
> 이전 버전의 Azure SQL 모니터링 솔루션을 포함한 일부 솔루션에서 자동화 자산을 만들었을 수 있으며, 작업 영역을 연결 해제하기 전에 제거해야 할 수도 있습니다.

1. Azure Portal에서 Automation 계정을 열고 Automation 계정 페이지에서 왼쪽의 **관련된 리소스** 섹션 아래의 **연결된 작업 영역**을 선택합니다.

2. 작업 영역 연결 해제 페이지에서 **작업 영역 연결 해제**를 클릭합니다. 계속 하 시겠습니까를 확인 하는 메시지가 표시 됩니다.

3. Azure Automation이 Log Analytics 작업 영역의 연결을 끊으려고 하는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

업데이트 관리 솔루션을 사용한 경우 솔루션을 제거한 후 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

* 업데이트 일정 - 각 일정에는 사용자가 만든 업데이트 배포와 일치하는 이름이 지정됩니다.

* 솔루션에 대해 생성 되는 Hybrid worker 그룹-각각은 `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`와 비슷하게 이름이 지정 됩니다.

작업이 없는 동안 VM 시작/중지를 사용한 경우 솔루션을 제거한 후 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

* VM runbook 시작 및 중지 일정
* VM runbook 시작 및 중지
* 변수

또는 Log Analytics 작업 영역에서 Automation 계정의 작업 영역 연결을 해제할 수도 있습니다. 작업 영역에서 **관련 리소스**아래에 있는 **Automation 계정** 을 선택 합니다. Automation 계정 페이지에서 **계정 연결 해제**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

다음 솔루션을 등록 하는 방법에 대해 알아봅니다.

업데이트 관리 및 변경 내용 추적 및 인벤토리:

* [가상 컴퓨터](../automation-onboard-solutions-from-vm.md) 에서
* [Automation 계정](../automation-onboard-solutions-from-automation-account.md) 에서
* [여러 컴퓨터를 검색](../automation-onboard-solutions-from-browse.md) 하는 경우
* [Runbook](../automation-onboard-solutions.md) 에서

작업이 없는 동안 VM 시작/중지

* [업무 시간 외 Vm 시작/중지 배포](../automation-solution-vm-management.md)

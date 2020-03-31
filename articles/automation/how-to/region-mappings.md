---
title: Azure 자동화 및 로그 분석 작업 영역 매핑
description: 이 문서에서는 자동화 계정과 솔루션을 지원하기 위해 로그 분석 작업 영역 간에 허용되는 매핑에 대해 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849516"
---
# <a name="workspace-mappings"></a>작업 영역 매핑:

업데이트 관리, 변경 추적 및 인벤토리 또는 근무 외 솔루션 중에 VM 시작/중지와 같은 솔루션을 사용하도록 설정하면 로그 분석 작업 영역과 자동화 계정을 연결하기 위해 특정 지역만 지원됩니다. 이 매핑은 자동화 계정 및 로그 분석 작업 영역에만 적용됩니다. 자동화 계정 또는 로그 분석 작업 영역에 보고하는 리소스는 다른 지역에 있을 수 있습니다.

## <a name="supported-mappings"></a>지원되는 매핑

다음 표에 지원되는 매핑이 나와 있습니다.

|**Log Analytics 작업 영역 지역**|**Azure Automation 지역**|
|---|---|
|**우리**||
|이스트US<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|웨스트센트럴US<sup>2</sup>|웨스트센트럴US<sup>2</sup>|
|**Canada**||
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

<sup>1</sup> Log Analytics 작업 영역을 자동화 계정에 대한 EastUS 매핑은 지역 매핑에 대한 정확한 영역이 아니라 올바른 매핑입니다.

<sup>2</sup> 용량 제한으로 인해 새 리소스를 만들 때 지역을 사용할 수 없습니다. 여기에는 자동화 계정 및 로그 분석 작업 영역이 포함됩니다. 그러나 리전에서 기존 에 연결된 리소스는 계속 작동해야 합니다.

## <a name="unlink-workspace"></a>작업 영역 연결 해제

자동화 계정을 Log Analytics 작업 영역과 더 이상 통합하지 않으려면 Azure 포털에서 직접 계정을 연결 해제할 수 있습니다. 계속하기 전에 먼저 업데이트 관리, 변경 추적 및 인벤토리 또는 사용 중인 사용 시간 동안 VM 시작/중지를 제거해야 합니다. 제거하지 않으면 이 프로세스가 진행되지 않습니다. 가져온 특정 솔루션에 대한 문서를 검토하여 제거에 필요한 단계를 이해하세요.

이러한 솔루션을 제거한 후에 다음 단계에 따라 Automation 계정 연결을 해제할 수 있습니다.

> [!NOTE]
> 이전 버전의 Azure SQL 모니터링 솔루션을 포함한 일부 솔루션에서 자동화 자산을 만들었을 수 있으며, 작업 영역을 연결 해제하기 전에 제거해야 할 수도 있습니다.

1. Azure Portal에서 Automation 계정을 열고 Automation 계정 페이지에서 왼쪽의 **관련된 리소스** 섹션 아래의 **연결된 작업 영역**을 선택합니다.

2. 작업 영역 연결 해제 페이지에서 **작업 영역 연결 해제**를 클릭합니다. 계속하려면 묻는 메시지가 표시됩니다.

3. Azure Automation이 Log Analytics 작업 영역의 연결을 끊으려고 하는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

업데이트 관리 솔루션을 사용한 경우 솔루션을 제거한 후 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

* 업데이트 일정 - 각 일정에는 사용자가 만든 업데이트 배포와 일치하는 이름이 지정됩니다.

* 솔루션용으로 만든 하이브리드 작업자 그룹 - `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`각각은 )와 유사하게 이름이 지정됩니다.

작업이 없는 동안 VM 시작/중지를 사용한 경우 솔루션을 제거한 후 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

* VM runbook 시작 및 중지 일정
* VM runbook 시작 및 중지
* 변수

또는 Log Analytics 작업 영역에서 자동화 계정에서 작업 영역의 연결을 해제할 수도 있습니다. 작업 영역에서 **관련 리소스**에서 **자동화 계정을** 선택합니다. 자동화 계정 페이지에서 **계정 연결 해제를 선택합니다.**

## <a name="next-steps"></a>다음 단계

다음 솔루션을 온보로 하는 방법에 대해 알아봅니다.

업데이트 관리 및 변경 사항 추적 및 인벤토리:

* 가상 [컴퓨터에서](../automation-onboard-solutions-from-vm.md)
* 자동화 [계정에서](../automation-onboard-solutions-from-automation-account.md)
* [여러 컴퓨터를 탐색할](../automation-onboard-solutions-from-browse.md) 때
* [런북에서](../automation-onboard-solutions.md)

작업이 없는 동안 VM 시작/중지

* [근무 외 시간에 VM 을 시작/중지배포](../automation-solution-vm-management.md)

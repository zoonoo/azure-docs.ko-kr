---
title: Azure 변경 내용 추적을 사용하여 문제 해결
description: Azure 자동화 변경 변경 추적 및 인벤토리 기능을 사용하여 문제를 해결하고 해결하는 방법을 알아봅니다.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198533"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 문제 해결

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>시나리오: Windows 컴퓨터에 대한 변경 추적 레코드가 표시되지 않습니다.

#### <a name="issue"></a>문제

변경 추적을 위해 온보기중인 Windows 컴퓨터에 대한 변경 검사 또는 인벤토리 결과가 표시되지 않습니다.

#### <a name="cause"></a>원인

이 오류에는 다음과 같은 원인이 있을 수 있습니다.

* Microsoft 모니터링 에이전트가 실행되고 있지 않습니다.
* 자동화 계정으로의 통신이 차단되고 있습니다.
* 변경 추적에 대한 관리 팩은 다운로드되지 않습니다.
* 온보던 VM은 Microsoft 모니터링 에이전트가 설치된 복제되지 않은 복제 된 컴퓨터에서 왔을 수 있습니다.

#### <a name="resolution"></a>해결 방법

아래에 설명된 해결 방법은 문제를 해결하는 데 도움이 될 수 있습니다. 그래도 도움이 필요한 경우 진단 정보를 수집하고 지원팀에 문의할 수 있습니다. 에이전트 컴퓨터에서 C:\프로그램 파일\Microsoft 모니터링 에이전트\에이전트\도구로 이동하여 다음 명령을 실행합니다.

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> 기본적으로 오류 추적이 활성화됩니다. 앞의 예제와 같이 자세한 오류 메시지를 사용하려면 *VER* 매개 변수를 사용합니다. 정보 추적의 경우 **StartTracing.cmd를**호출할 때 *INF를* 사용합니다.

##### <a name="microsoft-monitoring-agent-not-running"></a>실행 중인 Microsoft 모니터링 에이전트

Microsoft 모니터링 에이전트(HealthService.exe)가 컴퓨터에서 실행 중인지 확인합니다.

##### <a name="communication-to-automation-account-blocked"></a>자동화 계정으로의 통신이 차단되었습니다.

컴퓨터에서 이벤트 뷰어를 확인하고 "변경 추적"이라는 단어가 있는 이벤트를 찾습니다.

[하이브리드 Runbook 워커를 사용하여 데이터 센터 또는 클라우드의 리소스 자동화를](../automation-hybrid-runbook-worker.md#network-planning) 참조하여 변경 사항 추적이 작동하도록 허용되어야 하는 주소 및 포트에 대해 알아봅니다.

##### <a name="management-packs-not-downloaded"></a>관리 팩이 다운로드되지 않음

다음 변경 사항 추적 및 재고 관리 팩이 로컬에 설치되어 있는지 확인합니다.

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>sysprepped되지 않은 복제 된 컴퓨터에서 VM

복제된 이미지를 사용하는 경우 먼저 이미지를 준비한 다음 Microsoft 모니터링 에이전트를 설치합니다.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>시나리오: Linux 컴퓨터에서 변경 사항 추적 또는 인벤토리 결과 없음

#### <a name="issue"></a>문제

변경 추적을 위해 온보온중인 Linux 컴퓨터에 대한 인벤토리 또는 변경 추적 결과가 표시되지 않습니다. 

#### <a name="cause"></a>원인
이 문제와 관련된 가능한 원인은 다음과 같습니다.
* Linux용 로그 분석 에이전트가 실행되고 있지 않습니다.
* Linux용 로그 분석 에이전트가 올바르게 구성되지 않았습니다.
* FIM(파일 무결성 모니터링) 충돌이 있습니다.

#### <a name="resolution"></a>해결 방법 

##### <a name="log-analytics-agent-for-linux-not-running"></a>실행되지 않는 Linux용 로그 분석 에이전트

Linux용 로그 분석 에이전트(omsagent)의 데몬이 컴퓨터에서 실행되고 있는지 확인합니다. 자동화 계정에 연결된 Log Analytics 작업 영역에서 다음 쿼리를 실행합니다.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

쿼리 결과에 컴퓨터가 표시되지 않으면 최근에 체크 인하지 않은 것입니다. 로컬 구성 문제가 있을 수 있으므로 에이전트를 다시 설치해야 합니다. 설치 및 구성에 대한 자세한 내용은 [로그 분석 에이전트를 사용하여 로그 데이터 수집을](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)참조하십시오. 

컴퓨터가 쿼리 결과에 표시되는 경우 범위 구성을 확인합니다. [Azure 모니터의 모니터링 솔루션 대상 을](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)참조하십시오.

이 문제의 자세한 해결을 위해 [문제: Linux 데이터가 표시되지 않는](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)경우 를 참조하십시오.

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>제대로 구성되지 않은 Linux용 로그 분석 에이전트

OMS 로그 수집기 도구를 사용하여 로그 및 명령줄 출력 컬렉션에 대해 Linux용 로그 분석 에이전트가 올바르게 구성되지 않을 수 있습니다. [변경 내용 추적 솔루션을 사용하여 환경의 변경 내용 추적을](../change-tracking.md)참조하십시오.

##### <a name="fim-conflicts"></a>FIM 충돌

Azure 보안 센터의 FIM 기능이 Linux 파일의 무결성을 잘못 검사할 수 있습니다. FIM이 작동하고 Linux 파일 모니터링을 위해 올바르게 구성되었는지 확인합니다. [변경 내용 추적 솔루션을 사용하여 환경의 변경 내용 추적을](../change-tracking.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 사용하여 더 많은 지원을 하세요.

* Azure 포럼을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/forums/)
* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.

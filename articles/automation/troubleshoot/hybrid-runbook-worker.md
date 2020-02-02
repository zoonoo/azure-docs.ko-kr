---
title: 문제 해결 - Azure Automation Hybrid Runbook Worker
description: 이 문서에서는 Azure Automation Hybrid Runbook Worker 문제 해결 정보를 제공합니다.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a5885df67464095061d9a95aa59010a1629fb8f8
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930342"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hybrid Runbook Worker 문제 해결

이 문서에서는 Hybrid Runbook Worker 문제 해결에 대한 정보를 제공합니다.

## <a name="general"></a>일반

Hybrid Runbook Worker는 Automation 계정과 통신하여 작업자를 등록하고, Runbook 작업을 수신하고, 상태를 보고하는 에이전트를 사용합니다. Windows의 경우이 에이전트는 Windows 용 Log Analytics 에이전트 (Microsoft Monitoring Agent (MMA) 라고도 함)입니다. Linux의 경우 Linux 용 Log Analytics 에이전트입니다.

### <a name="runbook-execution-fails"></a>시나리오: Runbook 실행 실패

#### <a name="issue"></a>문제

Runbook 실행에 실패하고 다음 오류가 발생합니다.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook이 3회 실행 시도 직후 일시 중단됩니다. Runbook이 완료 되지 않도록 방해할 수 있는 조건이 있습니다. 관련 오류 메시지에 추가 정보가 포함 되지 않을 수 있습니다.

#### <a name="cause"></a>원인

가능한 원인은 다음과 같습니다.

* 로컬 리소스를 사용하여 Runbook을 인증할 수 없습니다.

* Hybrid Worker가 프록시 또는 방화벽 뒤에 있습니다.

* 로컬 리소스를 사용하여 Runbook을 인증할 수 없습니다.

* Hybrid Runbook Worker 기능을 실행 하도록 구성 된 컴퓨터가 최소 하드웨어 요구 사항을 충족 하지 않습니다.

#### <a name="resolution"></a>해상도

*.azure-automation.net에 대한 아웃바운드 액세스 권한이 컴퓨터의 443 포트에 있는지 확인합니다.

Hybrid Runbook Worker를 실행 하는 컴퓨터는이 기능을 호스팅하도록 작업자를 구성 하기 전에 최소 하드웨어 요구 사항을 충족 해야 합니다. Runbook 및이를 사용 하는 백그라운드 프로세스에서 시스템을 과도 하 게 사용 하 여 runbook 작업 지연 또는 시간 초과를 일으킬 수 있습니다.

Hybrid Runbook Worker 기능을 실행할 컴퓨터가 최소 하드웨어 요구 사항을 충족하는지 확인합니다. 충족하는 경우 CPU 및 메모리 사용을 모니터링하여 Hybrid Runbook Worker 프로세스의 성능과 Windows 사이에 어떠한 상관 관계가 있는지 확인합니다. 모든 메모리 또는 CPU 압력은 리소스를 업그레이드 해야 함을 나타낼 수 있습니다. 최소 요구 사항을 지원할 수 있는 다른 컴퓨팅 리소스를 선택하고 워크로드 수요가 증가의 필요성을 나타낼 경우 규모를 확장할 수도 있습니다.

**Microsoft-SMA** 이벤트 로그에 *Win32 프로세스가[4294967295] 코드와 함께 종료되었습니다.* 라고 설명하는 이벤트가 있는지 확인합니다. 이 오류의 원인은 Runbook에 인증을 구성하지 않았거나 Hybrid Worker 그룹에 대해 실행 자격 증명을 지정하지 않았기 때문일 수 있습니다. [Runbook 권한](../automation-hrw-run-runbooks.md#runbook-permissions)을 검토하여 Runbook에 대한 인증을 올바르게 구성했는지 확인합니다.

### <a name="no-cert-found"></a>시나리오: Hybrid Runbook Worker의 인증서 저장소에서 인증서를 찾을 수 없습니다.

#### <a name="issue"></a>문제

Hybrid Runbook Worker에서 실행되는 Runbook이 실패하고 다음 오류 메시지가 표시됩니다.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>원인

이 오류는 실행 계정 인증서가 없는 Hybrid Runbook Worker에서 실행되는 Runbook에서 [실행 계정](../manage-runas-account.md)을 사용하려고 시도할 때 발생합니다. Hybrid Runbook Worker의 경우 제대로 작동하기 위해 실행 계정에 필요한 인증서 자산이 기본적으로 로컬에 없습니다.

#### <a name="resolution"></a>해상도

Hybrid Runbook Worker Azure VM 인 경우 [azure 리소스에 대 한 관리 되는 id](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) 를 대신 사용할 수 있습니다. 이 시나리오는 실행 계정 대신 Azure VM의 관리 되는 id를 사용 하 여 Azure 리소스에 인증할 수 있도록 하 여 인증을 간소화 합니다. Hybrid Runbook Worker가 온-프레미스 머신인 경우 머신에 실행 계정 인증서를 설치해야 합니다. 인증서를 설치 하는 방법을 알아보려면 [Hybrid Runbook Worker에서 runbook을 실행](../automation-hrw-run-runbooks.md)하는 PowerShell runbook 내보내기-export-runascertificatetohybridworker을 실행 하는 단계를 참조 하세요.

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker는 자동화 계정과 통신 하 여 작업자를 등록 하 고, Runbook 작업을 수신 하 고, 상태를 보고 하는 [linux 용 Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md) 에 따라 달라 집니다. 작업자 등록이 실패하는 경우 다음과 같은 몇 가지 오류 원인이 있을 수 있습니다.

### <a name="oms-agent-not-running"></a>시나리오: Linux 용 Log Analytics 에이전트가 실행 되 고 있지 않음

#### <a name="issue"></a>문제

Linux 용 Log Analytics 에이전트가 실행 되 고 있지 않습니다.

#### <a name="cause"></a>원인

에이전트가 실행 되 고 있지 않으면 Linux Hybrid Runbook Worker Azure Automation와 통신할 수 없습니다. 다양한 이유로 에이전트를 실행하지 못할 수 있습니다.

#### <a name="resolution"></a>해상도

 `ps -ef | grep python` 명령을 입력하여 에이전트가 실행 중인지 확인하세요. 다음과 비슷한 출력, 즉 **nxautomation** 사용자 계정을 사용하는 python 프로세스가 표시됩니다. 업데이트 관리 또는 Azure Automation 솔루션을 사용하도록 설정하지 않은 경우 다음 프로세스 중 어떤 것도 실행되지 않습니다.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

다음 목록은 Linux Hybrid Runbook Worker에 대해 시작된 프로세스를 보여줍니다. 모두 `/var/opt/microsoft/omsagent/state/automationworker/` 디렉터리에 있습니다.


* **oms.conf** - 이 값은 작업자 관리자 프로세스입니다. DSC에서 직접 시작됩니다.

* **worker.conf** - 이 프로세스는 자동 등록 Hybrid Worker 프로세스로, 작업자 관리자가 시작합니다. 이 프로세스는 업데이트 관리에서 사용되며 사용자에게 투명합니다. 컴퓨터에서 업데이트 관리 솔루션을 사용하도록 설정하지 않으면 이 프로세스가 없습니다.

* **diy/worker.conf** - 이 프로세스는 DIY 하이브리드 작업자 프로세스입니다. DIY 하이브리드 작업자 프로세스는 Hybrid Runbook Worker에서 사용자 Runbook을 실행하는 데 사용됩니다. 자동 등록 된 하이브리드 작업자 프로세스는 다른 구성을 사용 한다는 핵심 정보에만 다릅니다. Azure Automation 솔루션을 사용 하지 않도록 설정 하 고 DIY Linux Hybrid Worker 등록 되지 않은 경우에는이 프로세스가 제공 되지 않습니다.

에이전트가 실행 되 고 있지 않은 경우 다음 명령을 실행 하 여 서비스를 시작 합니다. `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="error-403-on-registration"></a>시나리오: Hybrid Runbook Worker 등록 중 오류 403

#### <a name="issue"></a>문제

작업자의 초기 등록 단계가 실패 하 고 다음과 같은 오류 (403)가 표시 됩니다.

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>원인

가능한 원인은 다음과 같습니다.
* 에이전트 설정에 잘못 된 작업 영역 ID 또는 작업 영역 키 (기본)가 있습니다. 
* Hybrid Runbook Worker에서 구성을 다운로드할 수 없어서 계정 연결 오류가 발생 합니다. Azure에서 솔루션을 사용 하도록 설정 하면 Log Analytics 작업 영역 및 Automation 계정을 연결 하는 데 필요한 특정 영역만 지원 합니다. 컴퓨터에 잘못 된 날짜 및/또는 시간을 설정할 수도 있습니다. 시간이 현재 시간에서 +/-15 분 이면 온 보 딩이 실패 합니다.

#### <a name="resolution"></a>해상도

##### <a name="mistyped-workspace-idkey"></a>잘못 입력 한 작업 영역 i d/키
에이전트의 작업 영역 ID 또는 작업 영역 키의 철자가 잘못 되었는지 확인 하려면 [작업 영역 추가 또는 제거 –](../../azure-monitor/platform/agent-manage.md#windows-agent) windows 에이전트에 대 한 windows 에이전트 또는 linux 에이전트에 대 한 [작업 영역 추가 또는 제거 – linux 에이전트](../../azure-monitor/platform/agent-manage.md#linux-agent) 를 참조 하세요.  Azure Portal에서 전체 문자열을 선택 하 고 복사 하 여 신중 하 게 붙여 넣어야 합니다.

##### <a name="configuration-not-downloaded"></a>구성 다운로드 안 됨

Log Analytics 작업 영역 및 Automation 계정은 연결 된 지역에 있어야 합니다. 지원 되는 지역 목록은 [Azure Automation 및 Log Analytics 작업 영역 매핑](../how-to/region-mappings.md)을 참조 하세요.

컴퓨터의 날짜 및 표준 시간대를 업데이트 해야 할 수도 있습니다. 사용자 지정 시간 범위를 선택 하는 경우 범위가 UTC (현지 표준 시간대와 다를 수 있음) 인지 확인 합니다.

### <a name="class-does-not-exist"></a>시나리오: 지정 된 클래스가 존재 하지 않습니다.

오류가 표시 되는 경우 **지정 된 클래스가 존재 하지** 않습니다. `/var/opt/microsoft/omsconfig/omsconfig.log`에서 Linux 용 Log Analytics agent를 업데이트 해야 합니다. 다음 명령을 실행 하 여 에이전트를 다시 설치 합니다.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker은 [windows 용 Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md) 를 사용 하 여 작업자를 등록 하 고, Runbook 작업을 수신 하 고, 상태를 보고 하기 위해 Automation 계정과 통신 합니다. 작업자 등록이 실패하는 경우 다음과 같은 몇 가지 오류 원인이 있을 수 있습니다.

### <a name="mma-not-running"></a>시나리오: Microsoft Monitoring Agent 실행 되 고 있지 않음

#### <a name="issue"></a>문제

`healthservice` 서비스가 Hybrid Runbook Worker 컴퓨터에서 실행되고 있지 않습니다.

#### <a name="cause"></a>원인

Microsoft Monitoring Agent Microsoft 서비스를 실행 하 고 있지 않으면이 상태에서 Hybrid Runbook Worker Azure Automation와 통신할 수 없습니다.

#### <a name="resolution"></a>해상도

PowerShell에서 `Get-Service healthservice` 명령을 입력하여 에이전트가 실행 중인지 확인하세요. 서비스가 중지된 경우 PowerShell에서 `Start-Service healthservice` 명령을 입력하여 서비스를 시작하세요.

### <a name="event-4502"></a>시나리오: Operations Manager 로그의 이벤트 4502

#### <a name="issue"></a>문제

**응용 프로그램 및 서비스 Logs\Operations 관리자** 이벤트 로그에 microsoft.enterprisemanagement.reporting.code를 포함 하는 이벤트 4502 및 eventmessage가 표시 됩니다. **health service** 는 *서비스에서 제공 하는 인증서 \<Wsid\>. oms.opinsights.azure.com은 microsoft 서비스에 사용 되는 인증 기관에서 발급 되지 않았습니다. 네트워크 관리자에 게 문의 하 여 TLS/SSL 통신을 가로채는 프록시를 실행 하 고 있는지 확인 하십시오.*

#### <a name="cause"></a>원인

이 문제는 프록시 또는 네트워크 방화벽이 Microsoft Azure와의 통신을 차단하기 때문일 수 있습니다. *.azure-automation.net에 대한 아웃바운드 액세스 권한이 컴퓨터의 443 포트에 있는지 확인합니다. 

#### <a name="resolution"></a>해상도

로그는 각 Hybrid Worker의 로컬에 저장되며 위치는 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes입니다. **응용 프로그램 및 서비스 Logs\Microsoft-SMA\Operations** 및 **응용 프로그램 및 서비스 Logs\Operations 관리자** 이벤트 로그에 경고 또는 오류 이벤트가 있는지 확인할 수 있습니다 .이 이벤트 로그에는 정상적인 작업에서 Azure Automation 하거나 발급할 역할의 온 보 딩에 영향을 주는 연결 또는 기타 문제가 표시 됩니다. Log Analytics 에이전트와 관련 된 문제 해결에 대 한 자세한 내용은 [Windows 에이전트 Log Analytics 문제 해결](../../azure-monitor/platform/agent-windows-troubleshoot.md)을 참조 하십시오.

[Runbook 출력 및 메시지](../automation-runbook-output-and-messages.md)는 클라우드에서 실행되는 Runbook 작업처럼 Hybrid Worker에서 Azure Automation으로 전송됩니다. Verbose 및 Progress 스트림을 다른 Runbook과 같은 방식으로 사용할 수도 있습니다.

### <a name="corrupt-cache"></a>시나리오: 보고 하지 Hybrid Runbook Worker

#### <a name="issue"></a>문제

Hybrid Runbook Worker 시스템이 실행 중이지만 작업 영역에 해당 시스템에 대한 하트비트 데이터가 표시되지 않습니다.

다음 예제 쿼리는 작업 영역의 시스템과 해당 마지막 하트비트를 보여 줍니다.

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>원인

이 문제는 Hybrid Runbook Worker의 손상된 캐시로 인해 발생할 수 있습니다.

#### <a name="resolution"></a>해상도

이 문제를 해결하려면 Hybrid Runbook Worker에 로그인하고 다음 스크립트를 실행합니다. 이 스크립트는 Microsoft Monitoring Agent를 중지하고 해당 캐시를 제거한 후 서비스를 다시 시작합니다. 이 작업을 수행 하면 Hybrid Runbook Worker Azure Automation의 구성이 강제로 redownload 됩니다.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>시나리오: Hybrid Runbook Worker을 추가할 수 없습니다.

#### <a name="issue"></a>문제

`Add-HybridRunbookWorker` cmdlet을 사용하여 Hybrid Runbook Worker를 추가하려고 할 때 다음 메시지가 수신됩니다.

```error
Machine is already registered
```

#### <a name="cause"></a>원인

이 문제는 컴퓨터가 다른 Automation 계정에 이미 등록 되어 있거나 컴퓨터에서 제거 후 Hybrid Runbook Worker 다시 추가 하려는 경우에 발생할 수 있습니다.

#### <a name="resolution"></a>해상도

이 문제를 해결하려면 다음 레지스트리 키를 제거하고 `HealthService`를 다시 시작한 다음, `Add-HybridRunbookWorker` cmdlet을 다시 시도합니다.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
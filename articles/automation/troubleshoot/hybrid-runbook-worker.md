---
title: Hybrid Runbook Worker Azure Automation 문제 해결
description: 이 문서에서는 Hybrid Runbook Worker Azure Automation 문제 해결에 대 한 정보를 제공 합니다.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679326"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hybrid Runbook Worker 문제 해결

이 문서에서는 Hybrid Runbook Worker 문제 해결에 대한 정보를 제공합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](../automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="general"></a>일반

Hybrid Runbook Worker는 Automation 계정과 통신하여 작업자를 등록하고, Runbook 작업을 수신하고, 상태를 보고하는 에이전트를 사용합니다. Windows의 경우이 에이전트는 Windows 용 Log Analytics 에이전트입니다. Linux의 경우 Linux 용 Log Analytics 에이전트입니다.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>시나리오: Runbook 실행 실패

#### <a name="issue"></a>문제

Runbook 실행이 실패 하 고 다음과 같은 오류가 표시 됩니다.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook이 3 회 실행을 시도한 직후에 일시 중단 됩니다. Runbook을 완료 하지 못하도록 방해할 수 있는 조건이 있습니다. 관련 오류 메시지에 추가 정보가 포함 되지 않을 수 있습니다.

#### <a name="cause"></a>원인

가능한 원인은 다음과 같습니다.

* Runbook은 로컬 리소스를 사용 하 여 인증할 수 없습니다.
* Hybrid Worker가 프록시 또는 방화벽 뒤에 있습니다.
* Hybrid Runbook Worker를 실행 하도록 구성 된 컴퓨터가 최소 하드웨어 요구 사항을 충족 하지 않습니다.

#### <a name="resolution"></a>해결 방법

컴퓨터의 포트 443에서 ***. azure-automation.net** 에 대 한 아웃 바운드 액세스 권한이 있는지 확인 합니다.

Hybrid Runbook Worker를 실행 하는 컴퓨터는이 기능을 호스팅하도록 작업자를 구성 하기 전에 최소 하드웨어 요구 사항을 충족 해야 합니다. Runbook 및이를 사용 하는 백그라운드 프로세스에서 시스템을 과도 하 게 사용 하 여 runbook 작업 지연 또는 시간 초과가 발생할 수 있습니다.

Hybrid Runbook Worker 기능을 실행 하는 컴퓨터가 최소 하드웨어 요구 사항을 충족 하는지 확인 합니다. 충족하는 경우 CPU 및 메모리 사용을 모니터링하여 Hybrid Runbook Worker 프로세스의 성능과 Windows 사이에 어떠한 상관 관계가 있는지 확인합니다. 모든 메모리 또는 CPU 압력은 리소스를 업그레이드 해야 함을 나타낼 수 있습니다. 또한 최소 요구 사항을 지 원하는 다른 계산 리소스를 선택 하 고 워크 로드 요구가 증가 하는 것으로 표시 되는 경우 크기를 조정할 수 있습니다.

설명과 `Win32 Process Exited with code [4294967295]`함께 해당 이벤트에 대 한 **Microsoft SMA** 이벤트 로그를 확인 합니다. 이 오류의 원인은 runbook에서 인증을 구성 하지 않았거나 Hybrid Runbook Worker 그룹에 대해 실행 자격 증명을 지정 하지 않았기 때문입니다. Runbook에 대 한 인증을 올바르게 구성 했는지 확인 하려면 [Hybrid Runbook Worker에서](../automation-hrw-run-runbooks.md) Runbook을 실행 하는 runbook 사용 권한을 검토 합니다.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>시나리오: Hybrid Runbook Worker의 이벤트 15011

#### <a name="issue"></a>문제

Hybrid Runbook Worker는 이벤트 15011를 받으며 쿼리 결과가 유효 하지 않음을 나타냅니다. Worker가 [SignalR 서버](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1)와의 연결을 열려고 할 때 다음 오류가 나타납니다.

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>원인

자동화 된 배포 솔루션에 대 한 Hybrid Runbook Worker 올바르게 구성 되지 않았습니다. 이 솔루션에는 VM을 Log Analytics 작업 영역에 연결 하는 파트가 포함 되어 있습니다. PowerShell 스크립트는 제공 된 이름을 사용 하 여 구독에서 작업 영역을 찾습니다. 이 경우 Log Analytics 작업 영역은 다른 구독에 있습니다. 스크립트에서 작업 영역을 찾을 수 없고 작업 영역을 만들려고 했지만 이름이 이미 사용 중입니다. 따라서 배포가 실패 합니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결 하는 두 가지 옵션이 있습니다.

* PowerShell 스크립트를 수정 하 여 다른 구독에서 Log Analytics 작업 영역을 찾습니다. 이는 향후 많은 Hybrid Runbook Worker 컴퓨터를 배포 하려는 경우에 적합 한 솔루션입니다.

* Orchestrator 샌드박스에서 실행 되도록 작업자 컴퓨터를 수동으로 구성 합니다. 그런 다음 작업자의 Azure Automation 계정에서 생성 된 runbook을 실행 하 여 기능을 테스트 합니다.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>시나리오: hybrid worker 그룹에서 자동으로 삭제 된 Windows Azure Vm

#### <a name="issue"></a>문제

작업자 컴퓨터가 오랜 시간 동안 꺼진 경우에는 Hybrid Runbook Worker 또는 Vm을 볼 수 없습니다.

#### <a name="cause"></a>원인

Hybrid Runbook Worker 컴퓨터가 30 일 넘게 Azure Automation ping 되지 않았습니다. 따라서 Automation은 Hybrid Runbook Worker 그룹 또는 시스템 작업자 그룹을 제거 했습니다. 

#### <a name="resolution"></a>해결 방법

작업자 컴퓨터를 시작 하 고 Azure Automation를 사용 하 여 rereregister 합니다. Runbook 환경을 설치 하 고 [Windows Hybrid Runbook Worker 배포](../automation-windows-hrw-install.md)에서 Azure Automation에 연결 하는 방법에 대 한 지침을 참조 하세요.

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>시나리오: Hybrid Runbook Worker의 인증서 저장소에서 인증서를 찾을 수 없습니다.

#### <a name="issue"></a>문제

Hybrid Runbook Worker에서 실행 되는 runbook이 실패 하 고 다음 오류 메시지가 발생 합니다.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>원인

이 오류는 실행 계정 인증서가 없는 Hybrid Runbook Worker에서 실행 되는 runbook에서 [실행 계정을](../manage-runas-account.md) 사용 하려고 할 때 발생 합니다. 하이브리드 Runbook 작업자는 기본적으로 인증서 자산을 로컬로 보유 하지 않습니다. 실행 계정에는이 자산이 제대로 작동 해야 합니다.

#### <a name="resolution"></a>해결 방법

Hybrid Runbook Worker Azure VM 인 경우 [azure 리소스에 대 한 관리 되는 id](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) 를 대신 사용할 수 있습니다. 이 시나리오는 실행 계정 대신 Azure VM의 관리 되는 id를 사용 하 여 Azure 리소스에 인증할 수 있도록 하 여 인증을 간소화 합니다. Hybrid Runbook Worker 온-프레미스 컴퓨터의 경우 컴퓨터에 실행 계정 인증서를 설치 해야 합니다. 인증서를 설치 하는 방법을 알아보려면 [Hybrid Runbook Worker에서 runbook을 실행](../automation-hrw-run-runbooks.md)하는 PowerShell runbook **내보내기-export-runascertificatetohybridworker** 을 실행 하는 단계를 참조 하세요.

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>시나리오: Hybrid Runbook Worker 등록 중 오류 403

#### <a name="issue"></a>문제

작업자의 초기 등록 단계가 실패 하 고 다음과 같은 오류 (403)가 표시 됩니다.

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>원인

가능한 원인은 다음과 같습니다.

* 에이전트 설정에 잘못 된 작업 영역 ID 또는 작업 영역 키 (기본)가 있습니다. 

* Hybrid Runbook Worker에서 구성을 다운로드할 수 없어서 계정 연결 오류가 발생 합니다. Azure에서 솔루션을 사용 하도록 설정 하면 Log Analytics 작업 영역 및 Automation 계정을 연결 하는 데 필요한 특정 영역만 지원 합니다. 컴퓨터에 잘못 된 날짜 및/또는 시간을 설정할 수도 있습니다. 시간이 현재 시간에서 +/-15 분 이면 온 보 딩이 실패 합니다.

#### <a name="resolution"></a>해결 방법

##### <a name="mistyped-workspace-idkey"></a>잘못 입력 한 작업 영역 i d/키
에이전트의 작업 영역 ID 또는 작업 영역 키의 철자가 잘못 되었는지 확인 하려면 [작업 영역 추가 또는 제거 –](../../azure-monitor/platform/agent-manage.md#windows-agent) windows 에이전트에 대 한 windows 에이전트 또는 linux 에이전트에 대 한 [작업 영역 추가 또는 제거 – linux 에이전트](../../azure-monitor/platform/agent-manage.md#linux-agent) 를 참조 하세요.  Azure Portal에서 전체 문자열을 선택 하 고 복사 하 여 신중 하 게 붙여 넣어야 합니다.

##### <a name="configuration-not-downloaded"></a>구성 다운로드 안 됨

Log Analytics 작업 영역 및 Automation 계정은 연결 된 지역에 있어야 합니다. 지원 되는 지역 목록은 [Azure Automation 및 Log Analytics 작업 영역 매핑](../how-to/region-mappings.md)을 참조 하세요.

컴퓨터의 날짜 및/또는 표준 시간대를 업데이트 해야 할 수도 있습니다. 사용자 지정 시간 범위를 선택 하는 경우 범위가 UTC (현지 표준 시간대와 다를 수 있음) 인지 확인 합니다.

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker는 자동화 계정과 통신 하 여 작업자를 등록 하 고, Runbook 작업을 수신 하 고, 상태를 보고 하는 [linux 용 Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md) 에 따라 달라 집니다. 작업자 등록이 실패하는 경우 다음과 같은 몇 가지 오류 원인이 있을 수 있습니다.

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>시나리오: Linux 용 Log Analytics 에이전트가 실행 되 고 있지 않음

#### <a name="issue"></a>문제

Linux 용 Log Analytics 에이전트가 실행 되 고 있지 않습니다.

#### <a name="cause"></a>원인

에이전트가 실행 되 고 있지 않으면 Linux Hybrid Runbook Worker Azure Automation와 통신할 수 없습니다. 여러 가지 이유로 에이전트가 실행 되 고 있지 않을 수 있습니다.

#### <a name="resolution"></a>해결 방법

 명령을 `ps -ef | grep python`입력 하 여 에이전트가 실행 되 고 있는지 확인 합니다. **Nxautomation** 사용자 계정을 사용 하 여 Python 프로세스와 유사한 출력이 표시 됩니다. 업데이트 관리 또는 Azure Automation 솔루션을 사용 하도록 설정 하지 않은 경우 다음 프로세스가 실행 되지 않습니다.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

다음 목록은 Linux Hybrid Runbook Worker에 대해 시작된 프로세스를 보여줍니다. 모두 **/var/opt/microsoft/omsagent/state/automationworker/** 디렉터리에 있습니다.

* **oms** -작업자 관리자 프로세스입니다. DSC에서 직접 시작됩니다.

* **worker** -자동 등록 된 하이브리드 작업자 프로세스입니다. 작업자 관리자에서 시작됩니다. 이 프로세스는 업데이트 관리에서 사용되며 사용자에게 투명합니다. 컴퓨터에서 업데이트 관리 솔루션을 사용하도록 설정하지 않으면 이 프로세스가 없습니다.

* **diy/** diy 하이브리드 작업자 프로세스입니다. DIY 하이브리드 작업자 프로세스는 Hybrid Runbook Worker에서 사용자 Runbook을 실행하는 데 사용됩니다. 이는 자동 등록 된 하이브리드 작업자 프로세스와 다른 구성을 사용 한다는 핵심 정보에만 다릅니다. Azure Automation 솔루션을 사용 하지 않도록 설정 하 고 DIY Linux Hybrid Worker 등록 되지 않은 경우에는이 프로세스가 제공 되지 않습니다.

에이전트가 실행 되 고 있지 않으면 다음 명령을 실행 하 여 서비스를 시작 `sudo /opt/microsoft/omsagent/bin/service_control restart`합니다.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>시나리오: 지정 된 클래스가 존재 하지 않습니다.

`The specified class does not exist..` **/Var/opt/microsoft/omsconfig/omsconfig.log**에 오류가 표시 되는 경우 Linux 용 Log Analytics agent를 업데이트 해야 합니다. 다음 명령을 실행 하 여 에이전트를 다시 설치 합니다.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker은 [windows 용 Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md) 를 사용 하 여 작업자를 등록 하 고, Runbook 작업을 수신 하 고, 상태를 보고 하기 위해 Automation 계정과 통신 합니다. 작업자 등록이 실패 하는 경우이 섹션에는 몇 가지 가능한 이유가 포함 됩니다.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>시나리오: Windows 용 Log Analytics 에이전트가 실행 되 고 있지 않음

#### <a name="issue"></a>문제

이 `healthservice` Hybrid Runbook Worker 컴퓨터에서 실행 되 고 있지 않습니다.

#### <a name="cause"></a>원인

Windows 서비스에 대 한 Log Analytics 실행 되 고 있지 않으면 Hybrid Runbook Worker Azure Automation와 통신할 수 없습니다.

#### <a name="resolution"></a>해결 방법

PowerShell에서 다음 명령을 입력 하 여 에이전트가 실행 되 고 있는지 확인 `Get-Service healthservice`합니다. 서비스가 중지된 경우 PowerShell에서 `Start-Service healthservice` 명령을 입력하여 서비스를 시작하세요.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>시나리오: Operations Manager 로그의 이벤트 4502

#### <a name="issue"></a>문제

**응용 프로그램 및 서비스 Logs\Operations Manager** 이벤트 로그에 다음 설명과 함께를 포함 하는 이벤트 4502 및 `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` eventmessage가 표시 됩니다.<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>원인

이 문제는 프록시 또는 네트워크 방화벽이 Microsoft Azure와의 통신을 차단하기 때문일 수 있습니다. 컴퓨터의 포트 443에서 ***. azure-automation.net** 에 대 한 아웃 바운드 액세스 권한이 있는지 확인 합니다. 

#### <a name="resolution"></a>해결 방법

로그는 **C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**의 각 hybrid worker에 로컬로 저장 됩니다. **응용 프로그램 및 서비스 Logs\Microsoft-SMA\Operations** 및 **응용 프로그램 및 서비스 Logs\Operations 관리자** 이벤트 로그에 경고 또는 오류 이벤트가 있는지 확인할 수 있습니다. 이러한 로그는 Azure Automation 역할의 온 보 딩에 영향을 주는 연결 또는 다른 유형의 문제를 나타내거나 정상적인 작업에서 발생 하는 문제를 표시 합니다. Log Analytics 에이전트와 관련 된 문제 해결에 대 한 자세한 내용은 [Windows 에이전트 Log Analytics 문제 해결](../../azure-monitor/platform/agent-windows-troubleshoot.md)을 참조 하십시오.

Hybrid worker는 클라우드에서 실행 되는 runbook 작업에서 출력 및 메시지를 보내는 것과 동일한 방식으로 [runbook 출력 및 메시지](../automation-runbook-output-and-messages.md) 를 Azure Automation 보냅니다. Runbook에 대해 수행 하는 것과 같은 방법으로 자세한 정보 표시 및 진행률 스트림을 사용 하도록 설정할 수 있습니다.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>시나리오: Orchestrator. debug.exe는 프록시를 통해 Office 365에 연결할 수 없습니다.

#### <a name="issue"></a>문제

Windows Hybrid Runbook Worker에서 실행 되는 스크립트는 Orchestrator 샌드박스에서 Office 365에 정상적으로 연결할 수 없습니다. 스크립트가 연결에 대해 [connect-msolservice](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) 를 사용 하 고 있습니다. 

Proxy.config **를 조정 하 여 프록시와** 바이패스 목록을 설정 하면 샌드박스가 여전히 제대로 연결 되지 않습니다. 동일한 프록시 및 바이패스 목록 설정을 사용 하는 **Powershell_ise .exe .config** 파일이 정상적으로 작동 하는 것 같습니다. SMA (Service Management Automation) 로그 및 PowerShell 로그는 프록시와 관련 된 정보를 제공 하지 않습니다.

#### <a name="cause"></a>원인

서버의 ADFS (Active Directory Federation Services)에 대 한 연결은 프록시를 무시할 수 없습니다. PowerShell 샌드박스는 로깅된 사용자로 실행 됩니다. 그러나 오 케 스트레이 터 샌드박스에서 사용자 지정 되 고, 오 케 스트레이 터 **.config** 파일 설정을 무시할 수 있습니다. 컴퓨터 또는 MMA 프록시 설정을 처리 하는 특별 한 코드가 있지만 기타 사용자 지정 프록시 설정을 처리 하는 것은 아닙니다. 

#### <a name="resolution"></a>해결 방법

PowerShell cmdlet에 대 한 MSOnline 모듈 대신 Azure AD 모듈을 사용 하도록 스크립트를 마이그레이션하여 Orchestrator 샌드박스에 대 한 문제를 해결할 수 있습니다. [Orchestrator에서 Azure Automation로 마이그레이션 (베타)을](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration)참조 하세요.

MSOnline 모듈 cmdlet을 계속 사용 하려는 경우 스크립트를 변경 하 여 [Invoke 명령을](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7)사용 합니다. `ComputerName` 및 `Credential` 매개 변수의 값을 지정 합니다. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

이 코드 변경 내용은 지정 된 자격 증명의 컨텍스트에서 완전히 새로운 PowerShell 세션을 시작 합니다. 이를 통해 활성 사용자를 인증 하는 프록시 서버를 통해 트래픽을 전달할 수 있습니다.

>[!NOTE]
>이 솔루션은 샌드박스 구성 파일을 조작 하지 않아도 됩니다. 구성 파일이 스크립트와 함께 작동 하는 경우에도 Hybrid Runbook Worker 에이전트가 업데이트 될 때마다 파일이 지워집니다.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>시나리오: 보고 하지 Hybrid Runbook Worker

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

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 Hybrid Runbook Worker에 로그인하고 다음 스크립트를 실행합니다. 이 스크립트는 Windows 용 Log Analytics 에이전트를 중지 하 고, 캐시를 제거 하 고, 서비스를 다시 시작 합니다. 이 작업을 통해 Hybrid Runbook Worker는 강제로 Azure Automation에서 해당 구성을 다시 다운로드하게 됩니다.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>시나리오: Hybrid Runbook Worker을 추가할 수 없습니다.

#### <a name="issue"></a>문제

`Add-HybridRunbookWorker` cmdlet을 사용하여 Hybrid Runbook Worker를 추가하려고 할 때 다음 메시지가 수신됩니다.

```error
Machine is already registered
```

#### <a name="cause"></a>원인

이 문제는 컴퓨터가 다른 Automation 계정에 이미 등록 되어 있거나 컴퓨터에서 제거 후 Hybrid Runbook Worker 다시 추가 하려는 경우에 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결 하려면 다음 레지스트리 키를 제거 하 고를 `HealthService`다시 시작한 후 `Add-HybridRunbookWorker` cmdlet을 다시 시도 하십시오.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>다음 단계

위의 문제가 표시 되지 않거나 문제를 해결할 수 없는 경우 추가 지원을 위해 다음 채널 중 하나를 시도해 보세요.

* Azure [포럼](https://azure.microsoft.com/support/forums/)을 통해 azure 전문가 로부터 답변을 받으세요.
* Azure 커뮤니티 [@AzureSupport](https://twitter.com/azuresupport)를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정인를 사용 하 여 연결 하세요.
* Azure 지원 인시던트 제출 [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동 하 여 **지원 받기**를 선택 합니다.
---
title: Azure 자동화 하이브리드 Runbook 작업자 문제 해결
description: 이 문서에서는 Azure 자동화 하이브리드 Runbook 작업자 문제 해결에 대한 정보를 제공합니다.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679326"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hybrid Runbook Worker 문제 해결

이 문서에서는 Hybrid Runbook Worker 문제 해결에 대한 정보를 제공합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](../automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="general"></a>일반

Hybrid Runbook Worker는 Automation 계정과 통신하여 작업자를 등록하고, Runbook 작업을 수신하고, 상태를 보고하는 에이전트를 사용합니다. Windows의 경우 이 에이전트는 Windows용 로그 분석 에이전트입니다. 리눅스에 대 한, 그것은 리눅스에 대 한 로그 분석 에이전트.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>시나리오: Runbook 실행 실패

#### <a name="issue"></a>문제

Runbook 실행이 실패하면 다음과 같은 오류가 발생합니다.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook이 세 번 실행하려고 시도한 직후에 실행이 일시 중단됩니다. Runbook을 완료하는 데 방해가 될 수 있는 조건이 있습니다. 관련 오류 메시지에 추가 정보가 포함되지 않을 수 있습니다.

#### <a name="cause"></a>원인

가능한 원인은 다음과 같습니다.

* Runbook은 로컬 리소스로 인증할 수 없습니다.
* Hybrid Worker가 프록시 또는 방화벽 뒤에 있습니다.
* 하이브리드 Runbook 워커를 실행하도록 구성된 컴퓨터가 최소 하드웨어 요구 사항을 충족하지 않습니다.

#### <a name="resolution"></a>해결 방법

컴퓨터가 포트 443에서 ***.azure-automation.net** 아웃바운드 액세스 권한이 있는지 확인합니다.

하이브리드 Runbook 워커를 실행하는 컴퓨터는 작업자가 이 기능을 호스팅하도록 구성하기 전에 최소 하드웨어 요구 사항을 충족해야 합니다. Runbook 및 사용 하는 백그라운드 프로세스로 인해 시스템이 과도 하 게 사용 되 고 Runbook 작업 지연 또는 시간 초과를 일으킬 수 있습니다.

하이브리드 Runbook 작업자 기능을 실행하는 컴퓨터가 최소 하드웨어 요구 사항을 충족하는지 확인합니다. 충족하는 경우 CPU 및 메모리 사용을 모니터링하여 Hybrid Runbook Worker 프로세스의 성능과 Windows 사이에 어떠한 상관 관계가 있는지 확인합니다. 메모리 또는 CPU 압력은 리소스를 업그레이드해야 함을 나타낼 수 있습니다. 최소 요구 사항을 지원하는 다른 계산 리소스를 선택하고 워크로드 요구량이 증가해야 함을 나타내는 경우 확장할 수도 있습니다.

설명과 `Win32 Process Exited with code [4294967295]`함께 해당 이벤트에 대 한 **Microsoft-SMA** 이벤트 로그를 확인 합니다. 이 오류의 원인은 Runbook에서 인증을 구성하지 않았거나 하이브리드 Runbook Worker 그룹에 대한 실행 자격 증명을 지정하지 않았기 때문에 발생합니다. [하이브리드 Runbook 워커에서 Runbook 실행](../automation-hrw-run-runbooks.md) 의 Runbook 권한을 검토하여 Runbook에 대한 인증을 올바르게 구성한 지 확인합니다.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>시나리오: 하이브리드 Runbook 작업자의 이벤트 15011

#### <a name="issue"></a>문제

하이브리드 Runbook Worker는 쿼리 결과가 유효하지 않음을 나타내는 이벤트 15011을 수신합니다. 작업자가 [SignalR 서버와의](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1)연결을 열려고 할 때 다음 오류가 나타납니다.

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>원인

하이브리드 Runbook 작업자가 자동화된 배포 솔루션에 대해 올바르게 구성되지 않았습니다. 이 솔루션에는 VM을 Log Analytics 작업 영역에 연결하는 부품이 포함되어 있습니다. PowerShell 스크립트는 제공된 이름으로 구독의 작업 영역을 찾습니다. 이 경우 Log Analytics 작업 영역이 다른 구독에 있습니다. 스크립트에서 작업 영역을 찾을 수 없고 작업 영역을 만들려고 시도하지만 이름이 이미 지정되었습니다. 따라서 배포가 실패합니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하기 위한 두 가지 옵션이 있습니다.

* PowerShell 스크립트를 수정하여 다른 구독에서 로그 분석 작업 영역을 찾습니다. 나중에 많은 하이브리드 Runbook 작업자 컴퓨터를 배포하려는 경우 이 솔루션이 적합합니다.

* 오케스트레이터 샌드박스에서 실행되도록 작업자 컴퓨터를 수동으로 구성합니다. 그런 다음 작업자의 Azure 자동화 계정에서 만든 Runbook을 실행하여 기능을 테스트합니다.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>시나리오: Windows Azure VM이 하이브리드 작업자 그룹에서 자동으로 삭제되었습니다.

#### <a name="issue"></a>문제

작업자 컴퓨터가 오랫동안 꺼져 있으면 하이브리드 Runbook 작업자 또는 VM을 볼 수 없습니다.

#### <a name="cause"></a>원인

하이브리드 Runbook 작업자 컴퓨터가 30일 이상 Azure 자동화를 ping하지 않았습니다. 결과적으로 자동화는 하이브리드 Runbook 작업자 그룹 또는 시스템 작업자 그룹을 제거했습니다. 

#### <a name="resolution"></a>해결 방법

작업자 컴퓨터를 시작한 다음 Azure 자동화로 다시 등록합니다. [Windows 하이브리드 Runbook 작업자 배포에서](../automation-windows-hrw-install.md)Runbook 환경을 설치하고 Azure 자동화에 연결하는 방법에 대한 지침을 참조하세요.

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>시나리오: 하이브리드 Runbook 작업자의 인증서 저장소에서 인증서를 찾을 수 없습니다.

#### <a name="issue"></a>문제

하이브리드 Runbook 작업자에서 실행 중인 Runbook은 다음과 같은 오류 메시지와 함께 실패합니다.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>원인

이 오류는 [RunAs 계정](../manage-runas-account.md) 인증서가 없는 하이브리드 Runbook 워커에서 실행되는 Runbook에서 Run As 계정을 사용하려고 할 때 발생합니다. 하이브리드 Runbook 작업자는 기본적으로 로컬에 인증서 자산을 가지고 있지 않습니다. 실행 As 계정은 이 자산이 제대로 작동하도록 요구합니다.

#### <a name="resolution"></a>해결 방법

하이브리드 Runbook 워커가 Azure VM인 경우 [대신 Azure 리소스에 대해 관리되는 ID를](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) 사용할 수 있습니다. 이 시나리오에서는 Run As 계정 대신 Azure VM의 관리되는 ID를 사용하여 Azure 리소스에 인증할 수 있도록 하여 인증을 단순화합니다. 하이브리드 Runbook 작업자가 온-프레미스 컴퓨터인 경우 컴퓨터에 Run As 계정 인증서를 설치해야 합니다. 인증서를 설치하는 방법을 알아보려면 [하이브리드 Runbook 작업자에서 실행 중인 runbook에서](../automation-hrw-run-runbooks.md)PowerShell 실행첩 **내보내기-RunAsCertificateToHybridWorker를** 실행하는 단계를 참조하세요.

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>시나리오: 하이브리드 Runbook 작업자 등록 중 오류 403

#### <a name="issue"></a>문제

작업자의 초기 등록 단계가 실패하면 다음과 같은 오류(403)가 나타납니다.

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>원인

가능한 원인은 다음과 같습니다.

* 에이전트 설정에 잘못 입력된 작업 영역 ID 또는 작업 영역 키(기본)가 있습니다. 

* 하이브리드 Runbook 작업자는 구성을 다운로드할 수 없으며 계정 연결 오류가 발생합니다. Azure에서 솔루션을 활성화하면 Log Analytics 작업 영역과 자동화 계정을 연결하기 위한 특정 지역만 지원합니다. 컴퓨터에 잘못된 날짜 및/또는 시간이 설정되어 있는 것일 수도 있습니다. 시간이 현재 시간에서 +/-15분이면 온보딩이 실패합니다.

#### <a name="resolution"></a>해결 방법

##### <a name="mistyped-workspace-idkey"></a>잘못 입력된 작업 영역 ID/키
에이전트의 작업 영역 ID 또는 작업 영역 키가 잘못 입력되었는지 확인하려면 [작업 영역 추가 또는 제거 -](../../azure-monitor/platform/agent-manage.md#windows-agent) Windows 에이전트용 Windows 에이전트 추가 또는 작업 영역 추가 또는 제거 - Linux 에이전트용 Linux [에이전트를](../../azure-monitor/platform/agent-manage.md#linux-agent) 참조하십시오.  Azure 포털에서 전체 문자열을 선택하고 복사하여 신중하게 붙여 넣으십시오.

##### <a name="configuration-not-downloaded"></a>구성이 다운로드되지 않음

로그 분석 작업 영역 및 자동화 계정은 연결된 지역에 있어야 합니다. 지원되는 지역 목록은 [Azure 자동화 및 로그 분석 작업 영역 매핑을](../how-to/region-mappings.md)참조하십시오.

컴퓨터의 날짜 및/또는 표준 시간대를 업데이트해야 할 수도 있습니다. 사용자 지정 시간 범위를 선택하는 경우 해당 범위가 현지 표준 시간대와 다를 수 있는 UTC에 있는지 확인합니다.

## <a name="linux"></a>Linux

Linux 하이브리드 Runbook 작업자는 [리눅스의 로그 분석 에이전트에](../../azure-monitor/platform/log-analytics-agent.md) 따라 작업자를 등록하고 Runbook 작업을 수신하고 상태를 보고하기 위해 자동화 계정과 통신합니다. 작업자 등록이 실패하는 경우 다음과 같은 몇 가지 오류 원인이 있을 수 있습니다.

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>시나리오: Linux용 로그 분석 에이전트가 실행되고 있지 않습니다.

#### <a name="issue"></a>문제

Linux용 로그 분석 에이전트가 실행되고 있지 않습니다.

#### <a name="cause"></a>원인

에이전트가 실행되지 않으면 Linux 하이브리드 Runbook 작업자가 Azure 자동화와 통신하지 못하게 됩니다. 에이전트가 여러 가지 이유로 실행되지 않을 수 있습니다.

#### <a name="resolution"></a>해결 방법

 명령을 `ps -ef | grep python`입력하여 에이전트가 실행되고 있는지 확인합니다. **nxautomation** 사용자 계정으로 파이썬 이 프로세스를 다음과 유사한 출력이 표시됩니다. 업데이트 관리 또는 Azure 자동화 솔루션을 사용할 수 없는 경우 다음 프로세스가 실행되지 않습니다.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

다음 목록은 Linux Hybrid Runbook Worker에 대해 시작된 프로세스를 보여줍니다. 그들은 모두 **/var / 옵트 / 마이크로 소프트 / 옴에이전트 / 상태 / 자동화 작업자 /** 디렉토리에 있습니다.

* **oms.conf** - 작업자 관리자 프로세스입니다. DSC에서 직접 시작됩니다.

* **worker.conf** - 자동 등록 하이브리드 작업자 프로세스입니다. 작업자 관리자에서 시작됩니다. 이 프로세스는 업데이트 관리에서 사용되며 사용자에게 투명합니다. 컴퓨터에서 업데이트 관리 솔루션을 사용하도록 설정하지 않으면 이 프로세스가 없습니다.

* **diy/worker.conf** - DIY 하이브리드 작업자 프로세스입니다. DIY 하이브리드 작업자 프로세스는 Hybrid Runbook Worker에서 사용자 Runbook을 실행하는 데 사용됩니다. 다른 구성을 사용하는 키 세부 사항에서 자동 등록 하이브리드 작업자 프로세스와만 다릅니다. Azure 자동화 솔루션을 사용하지 않도록 설정하 고 DIY Linux 하이브리드 워커가 등록 되지 않은 경우이 프로세스가 나타나지 않습니다.

에이전트가 실행되고 있지 않으면 다음 명령을 실행하여 `sudo /opt/microsoft/omsagent/bin/service_control restart`서비스를 시작합니다.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>시나리오: 지정된 클래스가 존재하지 않습니다.

`The specified class does not exist..` **/var/opt/microsoft/omsconfig/omsconfig.log에**오류가 표시되면 Linux용 로그 분석 에이전트를 업데이트해야 합니다. 다음 명령을 실행하여 에이전트를 다시 설치합니다.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows 하이브리드 Runbook 작업자는 [Windows용 로그 분석 에이전트에](../../azure-monitor/platform/log-analytics-agent.md) 따라 자동화 계정과 통신하여 작업자를 등록하고 Runbook 작업을 수신하고 상태를 보고합니다. 작업자 등록에 실패하는 경우 이 섹션에는 몇 가지 가능한 이유가 포함됩니다.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>시나리오: Windows용 로그 분석 에이전트가 실행되고 있지 않습니다.

#### <a name="issue"></a>문제

`healthservice` 하이브리드 Runbook 작업자 컴퓨터에서 실행되지 않습니다.

#### <a name="cause"></a>원인

Windows용 로그 분석 서비스가 실행되지 않는 경우 하이브리드 Runbook 작업자는 Azure 자동화와 통신할 수 없습니다.

#### <a name="resolution"></a>해결 방법

PowerShell: `Get-Service healthservice`에서 다음 명령을 입력하여 에이전트가 실행 되고 있는지 확인합니다. 서비스가 중지된 경우 PowerShell에서 `Start-Service healthservice` 명령을 입력하여 서비스를 시작하세요.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>시나리오: 운영 관리자 로그의 이벤트 4502

#### <a name="issue"></a>문제

응용 **프로그램 및 서비스 로그\운영 관리자** 이벤트 로그에서 다음 설명과 `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` 함께 포함된 이벤트 4502 및 EventMessage가 표시됩니다.<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>원인

이 문제는 프록시 또는 네트워크 방화벽이 Microsoft Azure와의 통신을 차단하기 때문일 수 있습니다. 컴퓨터가 포트 443에서 ***.azure-automation.net** 아웃바운드 액세스 권한이 있는지 확인합니다. 

#### <a name="resolution"></a>해결 방법

로그는 **C:\ProgramData\Microsoft 시스템 센터\오케스트레이터\7.2\SMA\샌드박스에서**각 하이브리드 작업자에 로컬로 저장됩니다. **응용 프로그램 및 서비스 로그\Microsoft-SMA\운영** 및 응용 프로그램 및 **서비스 로그\운영 관리자** 이벤트 로그에 경고 또는 오류 이벤트가 있는지 확인할 수 있습니다. 이러한 로그는 Azure Automation에 대한 역할의 온보딩에 영향을 주는 연결 또는 기타 유형의 문제 또는 정상적인 작업에서 발생한 문제를 나타냅니다. 로그 분석 에이전트의 문제 해결에 대한 추가 도움말은 [로그 분석 Windows 에이전트의 문제 해결을](../../azure-monitor/platform/agent-windows-troubleshoot.md)참조하세요.

하이브리드 작업자는 클라우드에서 실행 중인 Runbook 작업이 출력 및 메시지를 보내는 것과 동일한 방식으로 [Runbook 출력 및 메시지를](../automation-runbook-output-and-messages.md) Azure Automation에 보냅니다. Runbook과 마찬가지로 자세한 내용 및 진행률 스트림을 활성화할 수 있습니다.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>시나리오: Orchestrator.Sandbox.exe 프록시를 통해 Office 365에 연결할 수 없습니다.

#### <a name="issue"></a>문제

Windows 하이브리드 Runbook 작업자에서 실행 중인 스크립트는 오케스트레이터 샌드박스의 Office 365에 예상대로 연결할 수 없습니다. 스크립트는 연결을 위해 [Connect-MsolService를](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) 사용하고 있습니다. 

**Orchestrator.Sandbox.exe.config를** 조정하여 프록시 및 바이패스 목록을 설정해도 샌드박스가 여전히 제대로 연결되지 않습니다. 동일한 프록시 및 바이패스 목록 설정이 있는 **Powershell_ise.exe.config** 파일이 예상대로 작동하는 것 같습니다. SMA(서비스 관리 자동화) 로그 및 PowerShell 로그는 프록시에 대한 정보를 제공하지 않습니다.

#### <a name="cause"></a>원인

서버의 ADFS(Active Directory Federation Services)에 대한 연결은 프록시를 우회할 수 없습니다. PowerShell 샌드박스는 기록된 사용자로 실행됩니다. 그러나 오케스트레이터 샌드박스는 사용자 지정이 매우 많으며 **Orchestrator.Sandbox.exe.config** 파일 설정을 무시할 수 있습니다. 그것은 기계 또는 MMA 프록시 설정을 처리하기위한 특별한 코드를 가지고 있지만, 다른 사용자 정의 프록시 설정을 처리하기위한. 

#### <a name="resolution"></a>해결 방법

PowerShell cmdlet에 대 한 MSOnline 모듈 대신 Azure AD 모듈을 사용 하 여 스크립트를 마이그레이션 하 여 오케스트레이터 샌드 박스에 대 한 문제를 해결할 수 있습니다. [오케스트레이터에서 Azure 자동화(베타)로 마이그레이션을](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration)참조하십시오.

MSOnline 모듈 cmdlet을 계속 사용하려면 스크립트를 변경하여 [Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7)을 사용합니다. `ComputerName` 및 `Credential` 매개변수에 대한 값을 지정합니다. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

이 코드 변경은 지정된 자격 증명의 컨텍스트에서 완전히 새로운 PowerShell 세션을 시작합니다. 활성 사용자를 인증하는 프록시 서버를 통해 트래픽이 흐르도록 해야 합니다.

>[!NOTE]
>이 솔루션을 사용하면 샌드박스 구성 파일을 조작할 필요가 없습니다. 구성 파일이 스크립트로 작업하는 데 성공하더라도 하이브리드 Runbook 작업자 에이전트가 업데이트될 때마다 파일이 지워집니다.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>시나리오: 하이브리드 Runbook 작업자가 보고하지 않음

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

이 문제를 해결하려면 Hybrid Runbook Worker에 로그인하고 다음 스크립트를 실행합니다. 이 스크립트는 Windows용 로그 분석 에이전트를 중지하고 캐시를 제거하고 서비스를 다시 시작합니다. 이 작업을 통해 Hybrid Runbook Worker는 강제로 Azure Automation에서 해당 구성을 다시 다운로드하게 됩니다.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>시나리오: 하이브리드 Runbook 작업자를 추가할 수 없습니다.

#### <a name="issue"></a>문제

`Add-HybridRunbookWorker` cmdlet을 사용하여 Hybrid Runbook Worker를 추가하려고 할 때 다음 메시지가 수신됩니다.

```error
Machine is already registered
```

#### <a name="cause"></a>원인

이 문제는 컴퓨터가 이미 다른 자동화 계정에 등록되어 있거나 컴퓨터에서 컴퓨터를 제거한 후 하이브리드 Runbook 워커를 읽으려고 하는 경우에 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 다음 레지스트리 키를 제거하고 `HealthService`을 다시 `Add-HybridRunbookWorker` 시작하고 cmdlet을 다시 시도하십시오.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>다음 단계

위에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 시도해 보십시오.

* Azure 포럼을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/forums/)
* [@AzureSupport](https://twitter.com/azuresupport)Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.
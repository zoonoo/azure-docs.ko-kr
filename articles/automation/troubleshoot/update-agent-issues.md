---
title: Windows Hybrid Runbook Worker 진단-Azure 업데이트 관리
description: 업데이트 관리를 지 원하는 Windows의 Azure Automation Hybrid Runbook Worker 문제를 해결 하 고 문제를 해결 하는 방법에 대해 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: ec35d11eba59ea21947e2c3cd5286bababa4eabb
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76153857"
---
# <a name="understand-and-resolve-windows-hybrid-runbook-worker-health-in-update-management"></a>업데이트 관리에서 Windows Hybrid Runbook Worker 상태 이해 및 해결

업데이트 관리에서 컴퓨터가 **준비**를 표시하지 않는 이유에는 여러 가지가 있을 수 있습니다. 업데이트 관리에서 Hybrid Runbook Worker 에이전트의 상태를 확인 하 여 근본적인 문제를 확인할 수 있습니다. 이 문서에서는 [오프 라인 시나리오](#troubleshoot-offline)에서 Azure Portal 및 비 azure 컴퓨터에서 azure 컴퓨터에 대 한 문제 해결사를 실행 하는 방법을 설명 합니다.

다음 목록은 컴퓨터가 나타낼 수 있는 세 가지 준비 상태입니다.

* **준비** 됨-Hybrid Runbook Worker 배포 되었으며 마지막으로 1 시간 전에 표시 되었습니다.
* **연결 끊김** -Hybrid Runbook Worker 배포 되었으며 마지막으로 1 시간 전에 표시 되었습니다.
* **구성 되지 않음** -Hybrid Runbook Worker를 찾을 수 없거나 등록을 완료 하지 못했습니다.

> [!NOTE]
> Azure Portal 표시 되는 내용과 컴퓨터의 현재 상태 사이에 약간의 지연이 있을 수 있습니다.

## <a name="start-the-troubleshooter"></a>문제 해결사 시작

Azure 머신의 경우 포털의 **업데이트 에이전트 준비** 열에서 **문제 해결** 링크를 클릭하여 **업데이트 에이전트 문제 해결** 페이지를 시작합니다. 비 Azure 컴퓨터의 경우 링크를 통해이 문서를 볼 수 있습니다. 비 Azure 컴퓨터 문제를 해결 하려면 [오프 라인 지침](#troubleshoot-offline) 을 참조 하세요.

![가상 머신의 업데이트 관리 목록](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Hybrid Runbook Worker 상태를 확인 하려면 VM이 실행 중 이어야 합니다. VM이 실행되고 있는 않은 경우 **VM 시작** 단추가 나타납니다.

**업데이트 에이전트 문제 해결** 페이지에서 **검사 실행**을 선택하여 문제 해결사를 시작합니다. 문제 해결사는 [실행 명령을](../../virtual-machines/windows/run-command.md) 사용 하 여 컴퓨터에서 종속성을 확인 하는 스크립트를 실행 합니다. 문제 해결사가 완료되면 검사 결과를 반환합니다.

![업데이트 에이전트 문제 해결 페이지](../media/update-agent-issues/troubleshoot-page.png)

준비가 되면 페이지에 결과가 표시됩니다. 검사 섹션에서는 각 검사에 포함된 항목을 보여줍니다.

![업데이트 에이전트 문제 해결 검사](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>필수 구성 요소 검사

### <a name="operating-system"></a>운영 체제

운영 체제 검사는 Hybrid Runbook Worker에서 다음 운영 체제 중 하나를 실행 하 고 있는지 여부를 확인 합니다.

|운영 체제  |메모  |
|---------|---------|
|Windows Server 2012 이상 |.NET Framework 4.6 이상이 필요 합니다. ([.NET Framework 다운로드](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 5.1이 필요 합니다.  ([Windows Management Framework 5.1 다운로드](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

.NET Framework 검사는 시스템에 최소 [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) 가 설치 되어 있는지 확인 합니다.

### <a name="wmf-51"></a>WMF 5.1

WMF 검사는 시스템에 WMF (Windows Management Framework)의 필수 버전이 있는지 확인 합니다.- [Windows Management framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

이 검사는 TLS 1.2를 사용하여 통신을 암호화하고 있는지 확인합니다. TLS 1.0은 더 이상 플랫폼에서 지원되지 않습니다. 클라이언트가 TLS 1.2를 사용하여 업데이트 관리와 통신하는 것이 좋습니다.

## <a name="connectivity-checks"></a>연결 검사

### <a name="registration-endpoint"></a>등록 엔드포인트

이 검사는 에이전트가 에이전트 서비스와 제대로 통신할 수 있는지 확인합니다.

프록시 및 방화벽 구성에서는 Hybrid Runbook Worker 에이전트가 등록 엔드포인트와 통신하도록 허용해야 합니다. 주소 및 포트 목록을 열려면 [Hybrid Worker에 대한 네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="operations-endpoint"></a>작업 엔드포인트

이 검사는 에이전트가 작업 런타임 데이터 서비스와 제대로 통신할 수 있는지 확인합니다.

프록시 및 방화벽 구성에서는 Hybrid Runbook Worker 에이전트가 작업 런타임 데이터 서비스와 통신하도록 허용해야 합니다. 주소 및 포트 목록을 열려면 [Hybrid Worker에 대한 네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

## <a name="vm-service-health-checks"></a>VM 서비스 상태 검사

### <a name="monitoring-agent-service-status"></a>에이전트 서비스 상태 모니터링

이 검사는 Microsoft Monitoring Agent인 `HealthService`가 머신에서 실행되고 있는지 확인합니다.

서비스 문제를 해결하는 방법에 대한 자세한 내용은 [Microsoft Monitoring Agent가 실행되고 있지 않습니다.](hybrid-runbook-worker.md#mma-not-running)를 참조하세요.

Microsoft Monitoring Agent를 다시 설치하려면 [Microsoft Monitoring Agent 설치 및 구성](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)을 참조하세요.

### <a name="monitoring-agent-service-events"></a>에이전트 서비스 이벤트 모니터링

이 검사는 지난 24시간 동안 머신의 Azure Operations Manager 로그에 모든 `4502` 이벤트가 표시되는지 확인합니다.

이 이벤트에 대한 자세한 내용은 이 이벤트에 대한 [문제 해결 가이드](hybrid-runbook-worker.md#event-4502)를 참조하세요.

## <a name="access-permissions-checks"></a>액세스 권한 검사

### <a name="machinekeys-folder-access"></a>MachineKeys 폴더 액세스

암호화 폴더 액세스 검사는 로컬 시스템 계정에 C:\ProgramData\Microsoft\Crypto\RSA에 대한 액세스 권한이 있는지 확인합니다.

## <a name="troubleshoot-offline"></a>오프라인으로 문제 해결

스크립트를 로컬로 실행하여 Hybrid Runbook Worker에서 오프라인으로 문제 해결사를 사용할 수 있습니다. PowerShell 갤러리에서 [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) 스크립트를 가져올 수 있습니다. 스크립트를 실행 하려면 WMF 4.0 이상 버전이 설치 되어 있어야 합니다. 최신 버전의 PowerShell을 다운로드 하려면 [다양 한 버전의 Powershell 설치](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)를 참조 하세요.

이 스트립트의 출력은 다음 예제와 같이 표시됩니다.

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>다음 단계

Hybrid Runbook Worker의 문제를 더 많이 해결하려면 [Hybrid Runbook Worker 문제 해결](hybrid-runbook-worker.md)을 참조하세요.

---
title: Azure 업데이트 관리에서 에이전트 확인 결과 이해
description: 업데이트 관리 에이전트의 문제를 해결하는 방법을 알아봅니다.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 20323afe79ad3de1e3dfccd4752c4f7e28d22266
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095374"
---
# <a name="understand-the-agent-check-results-in-update-management"></a>업데이트 관리에서 에이전트 확인 결과 이해

업데이트 관리에서 Azure 이외 컴퓨터가 **준비**를 표시하지 않는 이유에는 여러 가지가 있을 수 있습니다. 업데이트 관리에서 Hybrid Worker 에이전트의 상태를 확인하여 기본 문제를 검토할 수 있습니다. 이 문서에서는 Azure Portal 및 오프라인 시나리오에서 문제 해결사를 실행하는 방법을 설명합니다.

## <a name="start-the-troubleshooter"></a>문제 해결사 시작

포털의 **업데이트 에이전트 준비** 열에서 **문제 해결** 링크를 클릭하여 **업데이트 에이전트 문제 해결** 페이지를 시작합니다. 이 페이지는 문제 해결에 도움을 주기 위해 에이전트의 문제와 이 문서로 연결되는 링크를 표시합니다.

![VM 목록 페이지](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> 검사를 수행하려면 VM이 실행되고 있어야 합니다. VM이 실행되고 있지 않으면 **VM 시작** 단추가 제공됩니다.

**업데이트 에이전트 문제 해결** 페이지에서 **검사 실행**을 클릭하여 문제 해결사를 시작합니다. 문제 해결사는 [실행 명령](../../virtual-machines/windows/run-command.md)을 사용하여 컴퓨터에서 에이전트가 갖는 종속성을 확인하는 스크립트를 실행합니다. 문제 해결사가 완료되면 검사 결과를 반환합니다.

![문제 해결 페이지](../media/update-agent-issues/troubleshoot-page.png)

완료되면 창에 결과가 반환됩니다. [검사 섹션](#pre-requisistes-checks)은 각 검사가 찾으려는 정보를 제공합니다.

![업데이트 에이전트 검사 페이지](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>필수 구성 요소 검사

### <a name="operating-system"></a>운영 체제

OS 검사는 Hybrid Runbook Worker에서 다음 운영 체제 중 하나가 실행 중인지 확인합니다.

|운영 체제  |메모  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | 업데이트 평가만 지원합니다.         |
|Windows Server 2008 R2 SP1 이상     |.NET Framework 4.5.1 이상이 필요합니다. ([.NET Framework 다운로드](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 이상이 필요합니다. ([WMF 4.0 다운로드](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1은 안정성 개선을 위해 필요합니다.  ([WMF 5.1 다운로드](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6(x86/x64) 및 7(x64)      | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다. 분류 기반 패치에는 CentOS에 기본 제공되지 않은 보안 데이터를 반환하기 위해 'yum'이 필요합니다.         |
|Red Hat Enterprise 6(x86/x64) 및 7(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|Ubuntu 14.04 LTS, 16.04 LTS 및 18.04 LTS(x86/x64)      |Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.         |

### <a name="net-451"></a>.NET 4.5.1

.NET framework 검사는 시스템에 최소한 [.NET Framework 4.5.1](https://www.microsoft.com/download/details.aspx?id=30653)이 있는지 확인합니다.

### <a name="wmf-51"></a>WMF 5.1

WMF 검사는 시스템에 필요한 버전의 Windows Management Framework가 있는지 확인합니다. [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)은 지원되는 최하위 버전입니다. Hybrid Runbook Worker의 안정성 향상을 위해 [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)을 설치하는 것이 좋습니다.

### <a name="tls-12"></a>TLS 1.2

이 검사는 TLS 1.2를 사용하여 통신을 암호화하고 있는지 확인합니다. TLS 1.0은 플랫폼에서 더 이상 지원되지 않으며 클라이언트는 TLS 1.2를 사용하여 업데이트 관리와 통신하는 것이 좋습니다.

## <a name="connectivity-checks"></a>연결 검사

### <a name="registration-endpoint"></a>등록 엔드포인트

이 검사는 에이전트가 에이전트 서비스와 제대로 통신할 수 있는지를 확인합니다.

프록시 및 방화벽 구성에서는 Hybrid Runbook Worker 에이전트가 등록 엔드포인트와 통신하도록 허용해야 합니다. 주소 및 열 포트 목록에 대해서는 [Hybrid Worker에 대한 네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="operations-endpoint"></a>작업 엔드포인트

이 검사는 에이전트가 작업 런타임 데이터 서비스와 제대로 통신할 수 있는지를 확인합니다.

프록시 및 방화벽 구성에서는 Hybrid Runbook Worker 에이전트가 작업 런타임 데이터 서비스와 통신하도록 허용해야 합니다. 주소 및 열 포트 목록에 대해서는 [Hybrid Worker에 대한 네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

## <a name="vm-service-health-checks"></a>VM 서비스 상태 검사

### <a name="monitoring-agent-service-status"></a>에이전트 서비스 상태 모니터링

이 검사는 Microsoft Monitoring Agent인 `HealthService`가 컴퓨터에서 실행되고 있는지 확인합니다.

서비스 문제를 해결하는 방법에 대한 자세한 내용은 [Microsoft Monitoring Agent가 실행되고 있지 않습니다.](hybrid-runbook-worker.md#mma-not-running)를 참조하세요.

Microsoft Monitoring Agent를 다시 설치하려면 [Microsoft Monitoring Agent 설치 및 구성](/log-analytics/log-analytics-concept-hybrid.md#install-and-configure-agent)을 참조하세요.

### <a name="monitoring-agent-service-events"></a>에이전트 서비스 이벤트 모니터링

이 검사는 지난 24시간 동안 컴퓨터의 Operations Manager 로그에 `4502` 이벤트가 있는지 확인합니다.

이 이벤트에 대한 자세한 내용은 이 이벤트에 대한 [문제 해결 가이드](hybrid-runbook-worker.md#event-4502)를 참조하세요.

## <a name="access-permissions-checks"></a>액세스 권한 검사

### <a name="machinekeys-folder-access"></a>MachineKeys 폴더 액세스

암호화 폴더 액세스 검사는 로컬 시스템 계정이 `C:\ProgramData\Microsoft\Crypto\RSA`에 액세스할 수 있는지 확인합니다.

## <a name="troubleshoot-offline"></a>오프라인으로 문제 해결

이 스크립트를 로컬로 실행하여 Hybrid Runbook Worker에서 오프라인으로 문제 해결사를 사용할 수 있습니다. 스크립트 [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)은 PowerShell 갤러리에서 찾을 수 있습니다. 이 스크립트의 출력 예제는 다음 예제에 표시됩니다.

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .Net Framework 4.5+
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

Hybrid Runbook Worker의 추가 문제를 해결하려면 [문제 해결 - Hybrid Runbook Worker](hybrid-runbook-worker.md)를 참조하세요.

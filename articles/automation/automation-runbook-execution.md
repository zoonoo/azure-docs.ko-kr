---
title: Azure Automation에서 Runbook 실행
description: Azure Automation의 Runbook이 처리되는 방법에 대한 자세한 내용을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 1933688459cd02ee4da448d2e83b0a7a92a1d2c8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994738"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Automation에서 Runbook 실행

Azure Automation의 프로세스 자동화를 사용 하 여 PowerShell, PowerShell 워크플로 및 그래픽 runbook을 만들고 관리할 수 있습니다. 자세한 내용은 [Azure Automation runbook](automation-runbook-types.md)을 참조 하세요. 

Automation은 내부에 정의 된 논리에 따라 runbook을 실행 합니다. Runbook이 중단 되 면 처음부터 다시 시작 됩니다. 이 동작을 수행 하려면 일시적인 문제가 발생 하는 경우 다시 시작을 지 원하는 runbook을 작성 해야 합니다.

Azure Automation에서 runbook을 시작 하면 runbook의 단일 실행 인스턴스인 작업이 생성 됩니다. 각 작업은 Azure 구독에 연결 하 여 Azure 리소스에 액세스 합니다. 공용 클라우드에서 이러한 리소스에 액세스할 수 있는 경우 작업은 데이터 센터의 리소스에만 액세스할 수 있습니다.

Azure Automation는 runbook을 실행 하는 동안 각 작업을 실행할 작업자를 할당 합니다. 작업자가 많은 Azure 계정에서 공유되지만 여러 Automation 계정의 작업은 서로 격리됩니다. 작업에서 요청 하는 작업자 서비스를 제어할 수 없습니다.

Azure Portal에서 runbook 목록을 볼 때 각 runbook에 대해 시작 된 각 작업의 상태가 표시 됩니다. Azure Automation는 최대 30 일 동안 작업 로그를 저장 합니다.

다음 다이어그램은 [powershell runbook](automation-runbook-types.md#powershell-runbooks), [powershell 워크플로 runbook](automation-runbook-types.md#powershell-workflow-runbooks)및 [그래픽 runbook](automation-runbook-types.md#graphical-runbooks)에 대 한 runbook 작업의 수명 주기를 보여 줍니다.

![작업 상태 - PowerShell 워크플로](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="runbook-execution-environment"></a>Runbook 실행 환경

Azure Automation의 runbook은 Azure sandbox 또는 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 실행할 수 있습니다. 

Runbook이 Azure의 리소스에 대해 인증 되 고 실행 되도록 설계 된 경우 여러 작업에서 사용할 수 있는 공유 환경인 Azure 샌드박스에서 실행 됩니다. 동일한 샌드박스를 사용하는 작업에는 샌드박스의 리소스 제한이 적용됩니다. Azure sandbox 환경은 대화형 작업을 지원 하지 않습니다. 모든 out-of-process COM 서버에 대 한 액세스를 방지 합니다. 또한 Win32 호출을 수행 하는 runbook에 대해 로컬 MOF 파일을 사용 해야 합니다.

또한 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) 를 사용 하 여 역할을 호스트 하는 컴퓨터와 환경의 로컬 리소스에 대해 runbook을 직접 실행할 수 있습니다. Azure Automation는 runbook을 저장 하 고 관리 한 다음 하나 이상의 할당 된 컴퓨터에 배달 합니다.

>[!NOTE]
>Linux Hybrid Runbook Worker에서 실행 하려면 스크립트에 서명 하 여 적절 하 게 구성 해야 합니다. 또는 [서명 유효성 검사를 해제 해야](automation-linux-hrw-install.md#turn-off-signature-validation)합니다. 

다음 표에서는 각각에 대해 나열 된 권장 실행 환경을 사용 하는 몇 가지 runbook 실행 태스크를 보여 줍니다.

|작업|권장|메모|
|---|---|---|
|Azure 리소스와 통합|Azure 샌드박스|Azure에서 호스트 되는 인증은 더 간단 합니다. Azure VM에서 Hybrid Runbook Worker를 사용 하는 경우 [관리 되는 id와 함께 Runbook 인증을 사용할](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)수 있습니다.|
|Azure 리소스를 관리 하기 위한 최적의 성능 얻기|Azure 샌드박스|스크립트는 대기 시간이 짧은 동일한 환경에서 실행 됩니다.|
|운영 비용 최소화|Azure 샌드박스|계산 오버 헤드가 없으며 VM이 필요 하지 않습니다.|
|장기 실행 스크립트 실행|Hybrid Runbook Worker|Azure 샌드박스에는 [리소스 제한이](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)있습니다.|
|로컬 서비스와 상호 작용|Hybrid Runbook Worker|호스트 컴퓨터 또는 다른 클라우드 환경 또는 온-프레미스 환경의 리소스에 직접 액세스 합니다. |
|타사 소프트웨어 및 실행 파일 필요|Hybrid Runbook Worker|운영 체제를 관리 하 고 소프트웨어를 설치할 수 있습니다.|
|Runbook으로 파일 또는 폴더 모니터링|Hybrid Runbook Worker|Hybrid Runbook Worker에서 [감시자 태스크](automation-watchers-tutorial.md) 를 사용 합니다.|
|리소스를 많이 사용 하는 스크립트 실행|Hybrid Runbook Worker| Azure 샌드박스에는 [리소스 제한이](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)있습니다.|
|특정 요구 사항과 함께 모듈 사용| Hybrid Runbook Worker|예는 다음과 같습니다.</br> WinSCP - winscp.exe에 대한 종속성 </br> IIS 관리-IIS 사용 또는 관리에 대 한 종속성|
|설치 관리자를 사용 하 여 모듈 설치|Hybrid Runbook Worker|샌드박스에 대 한 모듈은 복사를 지원 해야 합니다.|
|4.7.2와 다른 .NET Framework 버전이 필요한 runbook 또는 모듈 사용|Hybrid Runbook Worker|4.7.2 .NET Framework Azure 샌드박스 지원 및 다른 버전으로의 업그레이드는 지원 되지 않습니다.|
|권한 상승이 필요한 스크립트 실행|Hybrid Runbook Worker|샌드박스에서 권한 상승이 허용 되지 않습니다. Hybrid Runbook Worker를 사용 하 여 UAC를 끄고 권한 상승이 필요한 명령을 실행할 때 [Invoke 명령을](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) 사용할 수 있습니다.|
|WMI(Windows Management Instrumentation) (WMI)에 액세스 해야 하는 스크립트 실행|Hybrid Runbook Worker|클라우드의 샌드박스에서 실행 되는 작업은 WMI 공급자에 액세스할 수 없습니다. |

## <a name="resources"></a>리소스

Runbook은 [리소스](https://docs.microsoft.com/rest/api/resources/resources)(예: vm, 네트워크 및 네트워크의 리소스)를 처리 하는 논리를 포함 해야 합니다. 리소스는 Azure 구독에 연결 되며, runbook에는 리소스에 액세스 하기 위한 적절 한 자격 증명이 필요 합니다. Runbook에서 리소스를 처리 하는 방법에 대 한 예제는 [리소스 처리](manage-runbooks.md#handle-resources)를 참조 하세요. 

## <a name="security"></a>보안

Azure Automation는 [ASC (Azure Security Center)](../security-center/security-center-intro.md) 를 사용 하 여 리소스에 대 한 보안을 제공 하 고 Linux 시스템에서 손상을 감지 합니다. 리소스는 Azure에 있든 관계 없이 워크 로드에서 제공 됩니다. [Azure Automation의 인증 소개를](automation-security-overview.md)참조 하세요.

ASC는 VM에서 서명 되거나 서명 되지 않은 모든 스크립트를 실행할 수 있는 사용자에 게 제약 조건을 배치 합니다. VM에 대 한 루트 액세스 권한이 있는 사용자는 컴퓨터를 명시적으로 디지털 서명으로 구성 하거나 해제 해야 합니다. 그렇지 않으면 자동화 계정을 만들고 적절 한 기능을 사용 하도록 설정한 후에만 스크립트를 실행 하 여 운영 체제 업데이트를 적용할 수 있습니다.

## <a name="subscriptions"></a>Subscriptions

Azure [구독은](https://docs.microsoft.com/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) Microsoft와 계약 하 여 요금이 부과 되는 클라우드 기반 서비스를 하나 이상 사용 합니다. Azure Automation 각 구독은 Azure Automation 계정에 연결 되며, 계정에 [여러 구독을 만들](manage-runbooks.md#work-with-multiple-subscriptions) 수 있습니다.

## <a name="credentials"></a>자격 증명

Azure 또는 타사 시스템의 경우에 따라 runbook에는 리소스에 액세스 하기 위한 적절 한 [자격 증명이](shared-resources/credentials.md) 필요 합니다. 이러한 자격 증명은 Azure Automation, Key Vault 등에 저장 됩니다.  

## <a name="azure-monitor"></a>Azure Monitor

Azure Automation는 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 를 사용 하 여 컴퓨터 작업을 모니터링 합니다. 작업에는 Log Analytics 작업 영역 및 [Log Analytics 에이전트가](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)필요 합니다.

### <a name="log-analytics-agent-for-windows"></a>Windows용 Log Analytics 에이전트

[Windows 용 Log Analytics 에이전트](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows) 는 windows vm 및 물리적 컴퓨터를 관리 하는 Azure Monitor와 함께 작동 합니다. 컴퓨터는 Azure에서 실행 되거나 로컬 데이터 센터와 같은 비 Azure 환경에서 실행 될 수 있습니다. 하나 이상의 Log Analytics 작업 영역에 보고 하도록 에이전트를 구성 해야 합니다. 

>[!NOTE]
>Windows 용 Log Analytics 에이전트는 이전에 Microsoft Monitoring Agent (MMA)로 알려져 있었습니다.

### <a name="log-analytics-agent-for-linux"></a>Linux용 Log Analytics 에이전트

[Linux 용 Log Analytics 에이전트](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux) 는 Windows 용 에이전트와 유사 하 게 작동 하지만 Azure Monitor에 linux 컴퓨터를 연결 합니다. 에이전트는 **nxautomation** 사용자 계정을 사용 하 여 설치 됩니다. 예를 들어 Hybrid Runbook Worker에서 루트 권한이 필요한 명령을 실행할 수 있습니다. **Nxautomation** 계정은 암호를 요구 하지 않는 시스템 계정입니다. 

[Linux Hybrid Runbook worker를 설치](automation-linux-hrw-install.md)하는 동안 해당 sudo 권한이 있는 **nxautomation** 계정이 있어야 합니다. 작업자를 설치 하려고 시도 했지만 해당 계정이 없거나 적절 한 권한이 없는 경우 설치에 실패 합니다.

Log Analytics 에이전트와 **nxautomation** 계정에 사용할 수 있는 로그는 다음과 같습니다.

* /var/opt/microsoft/omsagent/log/omsagent.log-Log Analytics 에이전트 로그 
* /var/opt/microsoft/omsagent/run/automationworker/worker.log-자동화 작업자 로그

>[!NOTE]
>업데이트 관리의 일부로 **nxautomation** 사용자 등록은 서명 된 runbook만 실행 합니다.

## <a name="runbook-permissions"></a>Runbook 사용 권한

Runbook은 자격 증명을 통해 Azure에 인증할 수 있는 권한이 필요 합니다. [Azure Automation 실행 계정 관리](manage-runas-account.md)를 참조 하세요. 

## <a name="modules"></a>모듈

Azure Automation는 일부 AzureRM 모듈 (AzureRM) 및 여러 내부 cmdlet이 포함 된 모듈을 비롯 한 다양 한 기본 모듈을 지원 합니다. 또한 AzureRM 모듈에 대 한 기본 설정에서 현재 사용 중인 Az modules (Az. Automation)을 포함 하는 설치 가능한 모듈도 지원 됩니다. Runbook 및 DSC 구성에 사용할 수 있는 모듈에 대 한 자세한 내용은 [Azure Automation 모듈 관리](shared-resources/modules.md)를 참조 하세요.

## <a name="certificates"></a>인증서

Azure Automation는 Azure에 대 한 인증을 위해 [인증서](shared-resources/certificates.md) 를 사용 하거나 azure 또는 타사 리소스에 추가 합니다. 인증서는 runbook 및 DSC 구성에서 액세스 하기 위해 안전 하 게 저장 됩니다. 

Runbook은 CA (인증 기관)에서 서명 하지 않은 자체 서명 된 인증서를 사용할 수 있습니다. [새 인증서 만들기를](shared-resources/certificates.md#create-a-new-certificate)참조 하세요.

## <a name="jobs"></a>작업

Azure Automation는 동일한 Automation 계정에서 작업을 실행 하는 환경을 지원 합니다. 단일 Runbook에서 동시에 많은 작업을 실행할 수 있습니다. 더 많은 작업을 동시에 실행할 수록 동일한 샌드박스에 더 자주 디스패치될 수 있습니다. 

동일한 샌드박스 프로세스에서 실행 되는 작업은 서로 영향을 줄 수 있습니다. 한 가지 예는 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) cmdlet을 실행 하는 것입니다. 이 cmdlet을 실행 하면 공유 샌드박스 프로세스에서 각 runbook 작업의 연결이 끊어집니다. 이 시나리오를 사용 하는 예제는 [동시 작업 방지](manage-runbooks.md#prevent-concurrent-jobs)를 참조 하세요.

>[!NOTE]
>Azure 샌드박스에서 실행 되는 runbook에서 시작 된 PowerShell 작업은 전체 [powershell 언어 모드](/powershell/module/microsoft.powershell.core/about/about_language_modes)에서 실행 되지 않을 수 있습니다. 

### <a name="job-statuses"></a>작업 상태

다음 표에서는 작업에 사용할 수 있는 상태에 대해 설명 합니다. 모든 runbook 작업에 대 한 상태 요약을 보거나 Azure Portal의 특정 runbook 작업에 대 한 세부 정보를 자세히 확인할 수 있습니다. 또한 Runbook의 작업 상태와 작업 스트림을 전달하기 위해 Log Analytics 작업 영역과 통합하도록 구성할 수도 있습니다. Azure Monitor 로그와 통합 하는 방법에 대 한 자세한 내용은 [자동화에서 작업 상태 및 작업 스트림을 Azure Monitor 로그로 전달](automation-manage-send-joblogs-log-analytics.md)을 참조 하세요. Runbook의 상태를 사용 하는 예제는 [작업 상태 가져오기](manage-runbooks.md#obtain-job-statuses) 도 참조 하세요.

| 상태 | Description |
|:--- |:--- |
| Completed |작업이 완료되었습니다. |
| Failed |그래픽 또는 PowerShell 워크플로 runbook을 컴파일하지 못했습니다. PowerShell runbook을 시작 하지 못했거나 작업에서 예외가 발생 했습니다. [Azure Automation runbook 형식](automation-runbook-types.md)을 참조 하세요.|
| Failed, waiting for resources |작업이 [공평 분배](#fair-share) 한도에 세 번 도달했기 때문에 실패했고 매번 동일한 검사점 또는 Runbook의 처음부터 시작되었습니다. |
| Queued |작업은 자동화 작업자의 리소스를 사용 하 여 시작할 수 있을 때까지 대기 중입니다. |
| Resuming |시스템이 일시 중단된 후 작업을 재개하는 중입니다. |
| 실행 중 |작업이 실행 중입니다. |
| Running, waiting for resources |작업이 공평 분배 한도에 도달했기 때문에 언로드되었습니다. 잠시 후 마지막 검사점에서 작업이 다시 시작됩니다. |
| 시작 중 |작업이 작업자에게 지정되었으며 시스템이 작업을 시작하고 있습니다. |
| 중지됨 |작업이 완료되기 전에 사용자에 의해 중지되었습니다. |
| 중지 중 |시스템이 작업을 중지하는 중입니다. |
| 일시 중단 |[그래픽 및 PowerShell 워크플로 runbook](automation-runbook-types.md) 에만 적용 됩니다. 작업이 Runbook의 사용자, 시스템 또는 명령에 의해 일시 중단되었습니다. Runbook에 검사점이 없으면 처음부터 시작 합니다. 검사점이 있으면 다시 시작되고 마지막 검사점에서 재개될 수 있습니다. 시스템은 예외가 발생 하는 경우에만 runbook을 일시 중단 합니다. 기본적으로 `ErrorActionPreference` 변수는 Continue로 설정 되며,이는 작업이 오류 발생 시 계속 실행 됨을 나타냅니다. 기본 설정 변수가 Stop으로 설정 된 경우에는 오류 발생 시 작업이 일시 중단 됩니다.  |
| Suspending |[그래픽 및 PowerShell 워크플로 runbook](automation-runbook-types.md) 에만 적용 됩니다. 시스템이 사용자의 요청에 따라 작업을 일시 중단하려고 합니다. Runbook의 다음 검사점에 도달해야만 Runbook을 일시 중단할 수 있습니다. 이미 마지막 검사점을 통과 한 경우 일시 중단 되기 전에 완료 됩니다. |

## <a name="activity-logging"></a>활동 로깅

Azure Automation에서 runbook을 실행 하면 Automation 계정에 대 한 활동 로그에 세부 정보가 기록 됩니다. 로그 사용에 대 한 자세한 내용은 [활동 로그에서 세부 정보 검색](manage-runbooks.md#retrieve-details-from-activity-log)을 참조 하세요. 

## <a name="exceptions"></a>예외

이 섹션에서는 runbook에서 예외 또는 일시적인 문제를 처리 하는 몇 가지 방법을 설명 합니다. 예를 들어 WebSocket 예외가 있습니다. 올바른 예외 처리를 통해 일시적인 네트워크 오류로 인해 runbook이 실패 하지 않도록 방지 합니다. 

### <a name="erroractionpreference"></a>ErrorActionPreference

[Erroractionpreference 설정](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) 변수는 PowerShell이 종료 되지 않는 오류에 응답 하는 방법을 결정 합니다. 종료 오류는 항상 종료 되며의 영향을 `ErrorActionPreference`받지 않습니다.

Runbook에서를 사용 `ErrorActionPreference`하는 경우 [Get childitem](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) cmdlet의 경우 `PathNotFound` 와 같이 일반적으로 종료 되지 않는 오류는 runbook이 완료 되지 않도록 중지 합니다. 다음 예에서는 `ErrorActionPreference`의 사용법을 보여줍니다. 스크립트를 중지 하면 최종 [쓰기 출력](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7) 명령이 실행 되지 않습니다.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>마지막으로 Catch 시도

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) 는 PowerShell 스크립트에서 종료 오류를 처리 하는 데 사용 됩니다. 스크립트는이 메커니즘을 사용 하 여 특정 예외 또는 일반 예외를 catch 할 수 있습니다. `catch` 문을 사용 하 여 오류를 추적 하거나 처리 해야 합니다. 다음 예에서는 존재 하지 않는 파일을 다운로드 하려고 시도 합니다. 예외를 `System.Net.WebException` catch 하 고 다른 예외에 대 한 마지막 값을 반환 합니다.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) 를 사용 하 여 종료 오류를 생성할 수 있습니다. 이 메커니즘은 runbook에서 고유한 논리를 정의할 때 유용할 수 있습니다. 스크립트가 중지 되어야 하는 조건을 충족 하는 경우 `throw` 문을 사용 하 여 중지할 수 있습니다. 다음 예에서는이 문을 사용 하 여 필수 함수 매개 변수를 표시 합니다.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>오류

Runbook에서 오류를 처리 해야 합니다. Azure Automation는 두 가지 유형의 PowerShell 오류인 종료 및 종료를 지원 하지 않습니다. 

종료 오류가 발생 하면 runbook 실행이 중지 됩니다. 작업 상태가 실패로 인해 runbook이 중지 됩니다.

종료 되지 않는 오류는 스크립트가 발생 한 후에도 스크립트를 계속 실행할 수 있습니다. 종료 되지 않는 오류의 예는 runbook이 존재 하지 않는 경로를 사용 하 여 `Get-ChildItem` cmdlet을 사용 하는 경우에 발생 하는 오류입니다. PowerShell은 경로가 없다는 것을 확인하고 오류가 throw한 후 다음 폴더로 계속 진행됩니다. 이 경우 오류는 runbook 작업 상태를 Failed로 설정 하지 않으며 작업이 완료 될 수도 있습니다. 종료되지 않는 오류에서 강제로 Runbook을 중지하려면 cmdlet에서 `ErrorAction Stop`을 사용할 수 있습니다.

## <a name="calling-processes"></a>호출 프로세스

Azure 샌드박스에서 실행 되는 runbook은 실행 파일 (**.exe** 파일) 또는 하위 프로세스와 같은 호출 프로세스를 지원 하지 않습니다. 그 이유는 Azure sandbox는 모든 기본 Api에 액세스 하지 못할 수 있는 컨테이너에서 실행 되는 공유 프로세스 이기 때문입니다. 타사 소프트웨어 또는 하위 프로세스에 대 한 호출이 필요한 시나리오의 경우 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 runbook을 실행 해야 합니다.

## <a name="device-and-application-characteristics"></a>장치 및 응용 프로그램 특성

Azure 샌드박스의 Runbook 작업은 장치나 응용 프로그램 특성에 액세스할 수 없습니다. Windows에서 성능 메트릭을 쿼리 하는 데 사용 되는 가장 일반적인 API는 WMI 이며, 일부 일반적인 메트릭은 메모리와 CPU 사용량입니다. 그러나 클라우드에서 실행 되는 작업은 웹 기반 엔터프라이즈 관리 (WBEM)의 Microsoft 구현에 액세스할 수 없으므로 어떤 API를 사용 하는지에 관계 없이 사용 됩니다. 이 플랫폼은 CIM(Common Information Model) (CIM)를 기반으로 하며, 장치 및 응용 프로그램 특징을 정의 하는 산업 표준을 제공 합니다.

## <a name="webhooks"></a>Webhook

Azure DevOps Services 및 GitHub와 같은 외부 서비스는 Azure Automation에서 runbook을 시작할 수 있습니다. 이 유형의 시작을 수행 하기 위해 서비스는 단일 HTTP 요청을 통해 [webhook](automation-webhooks.md) 를 사용 합니다. Webhook를 사용 하면 전체 Azure Automation 솔루션을 구현 하지 않고 runbook을 시작할 수 있습니다. 

## <a name="shared-resources"></a><a name="fair-share"></a>공유 리소스

클라우드의 모든 runbook에서 리소스를 공유 하기 위해 Azure는 공평 하 게 공유 라는 개념을 사용 합니다. Azure는 공평 공유를 사용 하 여 3 시간 이상 실행 된 모든 작업을 일시적으로 언로드하고 중지 합니다. [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) 및 [Python runbook](automation-runbook-types.md#python-runbooks) 에 대 한 작업은 중지 되며 다시 시작 되지 않으며, 작업 상태가 중지 됨이 됩니다.

장기 실행 Azure Automation 작업의 경우 Hybrid Runbook Worker를 사용 하는 것이 좋습니다. Hybrid Runbook Worker는 공평 분배로 제한되지 않으며, Runbook을 실행할 수 있는 기간에 대한 제한이 없습니다. 다른 작업 [제한](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)은 Azure 샌드박스 및 Hybrid Runbook Worker에 모두 적용됩니다. Hybrid Runbook Worker는 3 시간 공평 공유 제한에 의해서만 제한 되지 않지만 예기치 않은 로컬 인프라 문제에서 다시 시작을 지 원하는 작업자에서 실행할 runbook을 개발 해야 합니다.

또 다른 옵션은 자식 runbook을 사용 하 여 runbook을 최적화 하는 것입니다. 예를 들어 여러 데이터베이스에서 데이터베이스 작업을 수행 하는 등의 여러 리소스에서 동일한 기능을 사용 하 여 runbook을 반복할 수 있습니다. 이 함수를 [자식 runbook](automation-child-runbooks.md) 으로 이동 하 고 Runbook에서 [AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)를 사용 하 여 호출 하도록 할 수 있습니다. 자식 runbook은 별도의 프로세스에서 병렬로 실행 됩니다.

자식 runbook을 사용 하면 부모 runbook을 완료 하는 데 걸리는 총 시간을 줄일 수 있습니다. [AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) cmdlet을 사용 하 여 자식 runbook이 완료 되 면 자식 runbook에 대 한 작업 상태를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Runbook을 사용 하 여 작업을 시작 하려면 [Azure Automation에서 Runbook 관리](manage-runbooks.md)를 참조 하세요.
* 언어 참조 및 학습 모듈을 포함하여 PowerShell에 대한 자세한 내용은 [PowerShell 문서](https://docs.microsoft.com/powershell/scripting/overview)를 참조하세요.
* PowerShell cmdlet 참조는 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)을 참조하세요.

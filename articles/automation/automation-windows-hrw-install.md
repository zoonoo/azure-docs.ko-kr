---
title: Azure Automation Windows Hybrid Runbook Worker
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 환경의 Windows 기반 컴퓨터에서 Runbook을 실행할 수 있도록 해주는 Azure Automation Hybrid Runbook Worker를 설치하는 방법에 대한 정보를 제공합니다.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 53dfe07ebd4925c96290db140b6e613c38eef564
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617345"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker 배포

Azure Automation의 Hybrid Runbook Worker 기능을 사용하여 역할을 호스팅하는 컴퓨터에서 직접 그리고 환경의 리소스에 대해 Runbook을 실행하여 해당 로컬 리소스를 관리할 수 있습니다. Azure Automation은 Runbook을 저장및 관리한 다음 하나 이상의 지정된 컴퓨터에 배달합니다. 이 문서에서는 Windows 컴퓨터에 하이브리드 Runbook 작업자를 배포하는 방법에 대해 설명합니다.

Runbook Worker를 성공적으로 배포한 후에는 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 검토하여 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아봅니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Windows 하이브리드 Runbook 작업자 설치 및 구성

Windows 하이브리드 Runbook 워커를 설치하고 구성하려면 다음 방법 중 하나를 사용할 수 있습니다.

* Azure VM의 경우 Windows 에 대한 [가상 컴퓨터 확장을](../virtual-machines/extensions/oms-windows.md)사용하여 Windows용 로그 분석 에이전트를 설치합니다. 확장은 Azure 가상 시스템에 로그 분석 에이전트를 설치하고 Azure 리소스 관리자 템플릿 또는 PowerShell을 사용하여 가상 컴퓨터를 기존 로그 분석 작업 영역에 등록합니다. 에이전트가 설치되면 자동화 계정의 하이브리드 Runbook 작업자 그룹에 VM을 추가할 수 있습니다. [수동 배포](#manual-deployment) 섹션의 3단계와 4단계를 참조하십시오.

* 자동화 실행책을 사용하여 Windows 컴퓨터 구성 프로세스를 완전히 자동화합니다. 이 방법은 데이터 센터 또는 다른 클라우드 환경의 컴퓨터에 권장되는 방법입니다.

* 단계별 절차에 따라 AzureVM이 아닌 VM에서 하이브리드 Runbook 작업자 역할을 수동으로 설치하고 구성합니다.

> [!NOTE]
> 원하는 상태 구성(DSC)을 사용하여 하이브리드 Runbook 작업자 역할을 지원하는 서버의 구성을 관리하려면 서버를 DSC 노드로 추가해야 합니다.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Windows 하이브리드 Runbook 작업자에 대한 최소 요구 사항

Windows Hybrid Runbook Worker에 대한 최소 요구 사항은 다음과 같습니다.

* Windows Server 2012 이상
* 윈도우 파워 쉘 5.1 이상[(다운로드 WMF 5.1)](https://www.microsoft.com/download/details.aspx?id=54616)
* .NET Framework 4.6.2 이상
* 두 개의 코어
* 4GB의 RAM
* 443 포트(아웃바운드)

### <a name="network-configuration"></a>네트워크 구성

Hybrid Runbook Worker에 대한 상세한 네트워킹 요구 사항은 [네트워크 구성](automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="server-onboarding-for-management-with-automation-dsc"></a>자동화 DSC를 통한 관리를 위한 서버 온보딩

DSC관리를 위한 온보딩 서버에 대한 자세한 내용은 [Azure 자동화 DSC의 관리를 위한 온보딩 컴퓨터를](automation-dsc-onboarding.md)참조하십시오.

[업데이트 관리 솔루션을](../operations-management-suite/oms-solution-update-management.md) 사용하면 로그 분석 작업 영역에 연결된 모든 Windows 컴퓨터가 하이브리드 Runbook 워커로 자동으로 구성되어 솔루션에 포함된 Runbook을 지원합니다. 그러나 이 작업자는 자동화 계정에 이미 정의된 하이브리드 Runbook 작업자 그룹에 등록되지 않습니다.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>하이브리드 Runbook 작업자 그룹에 컴퓨터 추가

자동화 계정의 하이브리드 Runbook 작업자 그룹에 작업자 컴퓨터를 추가할 수 있습니다. 솔루션과 하이브리드 Runbook Worker 그룹 구성원 모두에 대해 동일한 계정을 사용하는 경우 자동화 런북을 지원해야 합니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

## <a name="automated-deployment"></a>자동화된 배포

대상 컴퓨터에서 다음 단계를 수행하여 Windows 하이브리드 작업자 역할의 설치 및 구성을 자동화합니다.

### <a name="step-1---download-the-powershell-script"></a>1단계 - PowerShell 스크립트 다운로드

[PowerShell 갤러리에서](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) **새 온프레미스하이브리드워커.ps1** 스크립트를 다운로드하십시오. 다운로드는 하이브리드 Runbook 작업자 역할을 실행하는 컴퓨터 또는 사용자 환경의 다른 컴퓨터에서 직접 다운로드해야 합니다. 스크립트를 다운로드한 경우 작업자에게 복사합니다. **New-OnPremiseHybridWorker.ps1** 스크립트는 실행 중에 아래에 설명된 매개 변수를 사용합니다.

| 매개 변수 | Status | Description |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | 필수 | Automation 계정과 연결된 리소스 그룹의 이름입니다. |
| `AutomationAccountName` | 필수 | Automation 계정의 이름입니다.
| `Credential` | 옵션 | Azure 환경에 로그인할 때 사용할 자격 증명입니다. |
| `HybridGroupName` | 필수 | 이 시나리오를 지원하는 Runbook에 대한 대상으로 지정할 Hybrid Runbook Worker 그룹의 이름입니다. |
| `OMSResourceGroupName` | 옵션 | Log Analytics 작업 영역에 대한 리소스 그룹의 이름입니다. 이 리소스 그룹을 지정하지 않으면 `AAResourceGroupName` 값이 사용됩니다. |
| `SubscriptionID` | 필수 | 자동화 계정과 연결된 Azure 구독의 식별자입니다. |
| `TenantID` | 옵션 | 자동화 계정과 연결된 테넌트 조직의 식별자입니다. |
| `WorkspaceName` | 옵션 | Log Analytics 작업 영역 이름입니다. Log Analytics 작업 영역이 없는 경우 스크립트에서 하나를 만들어 구성합니다. |

> [!NOTE]
> 솔루션을 사용하도록 설정하는 경우 Azure Automation은 로그 분석 작업 영역과 자동화 계정을 연결하기 위한 특정 지역만 지원합니다. 지원되는 매핑 쌍 목록은 자동화 [계정 및 Log Analytics 작업 영역에 대한 지역 매핑을](how-to/region-mappings.md)참조하십시오.

### <a name="step-2---open-windows-powershell-command-line-shell"></a>2 단계 - 열려있는 윈도우 파워 쉘 명령줄 쉘

관리자 모드에서 **시작** 화면에서 **Windows PowerShell을** 엽니다.

### <a name="step-3---run-the-powershell-script"></a>3단계 - PowerShell 스크립트 실행

PowerShell 명령줄 셸에서 다운로드한 스크립트가 포함된 폴더를 찾아봅습니다. 매개 변수 에 대한 `AutomationAccountName` `AAResourceGroupName`값을 `OMSResourceGroupName` `HybridGroupName` `SubscriptionID`변경합니다. `WorkspaceName` 그런 다음, 스크립트를 실행합니다.

스크립트를 실행한 후에 Azure 인증을 요청하는 메시지가 나타납니다. 구독 관리자 역할의 멤버이자 구독의 공동 관리자인 계정으로 로그인해야 합니다.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>4 단계 - NuGet 설치

NuGet을 설치하고 Azure 자격 증명을 사용하여 인증하는 데 동의하라는 메시지가 표시됩니다. 최신 NuGet 버전이 없는 경우 [사용 가능한 NuGet 배포 버전에서](https://www.nuget.org/downloads)가져올 수 있습니다.

### <a name="step-5---verify-the-deployment"></a>5단계 - 배포 확인

스크립트가 완료되면 Hybrid Worker 그룹 페이지에 새 그룹 및 멤버 수가 표시됩니다. 기존 그룹인 경우 멤버 수가 증가합니다. 하이브리드 작업자 그룹 페이지의 목록에서 그룹을 선택하고 **하이브리드 작업자** 타일을 선택할 수 있습니다. 하이브리드 작업자 페이지에서 나열된 그룹의 각 구성원을 볼 수 있습니다.

## <a name="manual-deployment"></a>수동 배포

대상 컴퓨터에서 자동화 환경에 대해 처음 두 단계를 한 번 수행합니다. 그런 다음 각 작업자 컴퓨터에 대해 나머지 단계를 수행합니다.

### <a name="step-1---create-a-log-analytics-workspace"></a>1단계 - 로그 분석 작업 영역 만들기

로그 분석 작업 영역이 아직 없는 경우 작업 영역을 만들기 전에 [Azure Monitor Log 디자인 지침을](../azure-monitor/platform/design-logs-deployment.md) 검토합니다.

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>2단계 - 로그 분석 작업 영역에 자동화 솔루션 추가

자동화 솔루션은 하이브리드 Runbook 작업자에 대한 지원을 포함하여 Azure 자동화에 대한 기능을 추가합니다. Log Analytics 작업 영역에 솔루션을 추가하면 다음 단계에서 설명한 대로 설치한 작업자 구성 요소를 에이전트 컴퓨터에 자동으로 푸시합니다.

작업 영역에 자동화 솔루션을 추가하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>3단계 - Windows용 로그 분석 에이전트 설치

Windows용 로그 분석 에이전트는 컴퓨터를 Azure 모니터 로그 분석 작업 영역에 연결합니다. 컴퓨터에 에이전트를 설치하고 작업 영역에 연결하면 하이브리드 Runbook 작업자에 필요한 구성 요소가 자동으로 다운로드됩니다.

컴퓨터에 에이전트를 설치하려면 Windows 컴퓨터 [연결에서 Azure 모니터 로그에](../log-analytics/log-analytics-windows-agent.md)대한 지침을 따릅니다. 이 과정을 여러 컴퓨터에 반복하여 사용자의 환경에 여러 작업자를 추가합니다.

몇 분 후에 에이전트가 Log Analytics 작업 영역에 성공적으로 연결되면 다음 쿼리를 실행하여 작업 영역으로 하트비트 데이터를 전송하고 있는지 확인할 수 있습니다.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

검색 결과에 컴퓨터에 대한 하트비트 레코드가 표시되어 컴퓨터에 연결되어 있고 서비스에 보고되었음을 나타냅니다. 기본적으로 모든 에이전트는 하트비트 레코드를 할당된 작업 공간으로 전달합니다. 

다음 단계를 사용하여 에이전트 설치 및 설정을 완료합니다.

1. 솔루션을 사용하여 에이전트 컴퓨터를 온보보드로 보온합니다. [작업 영역에서 온보드 컴퓨터를](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace)참조하십시오.
2. 에이전트가 자동화 솔루션을 올바르게 다운로드했는지 확인합니다. 
3. 하이브리드 Runbook 작업자의 버전을 확인하려면 **C:\프로그램 파일\Microsoft 모니터링 에이전트\AzureAutomation을** **찾아버전** 하위 폴더를 확인합니다.

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>4단계 - Runbook 환경을 설치하고 Azure 자동화에 연결

로그 분석 작업 영역에 보고할 에이전트를 구성할 때 `HybridRegistration` 자동화 솔루션은 cmdlet을 포함하는 PowerShell 모듈을 `Add-HybridRunbookWorker` 푸시합니다. 이 cmdlet을 사용하여 컴퓨터에 Runbook 환경을 설치하고 Azure 자동화에 등록합니다.

관리자 모드에서 PowerShell 세션을 열고 다음 명령을 실행하여 모듈을 가져옵니다.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

이제 다음 `Add-HybridRunbookWorker` 구문을 사용하여 cmdlet을 실행합니다.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Azure portal의 키 관리 페이지에서 이 cmdlet에 필요한 정보를 가져올 수 있습니다. 자동화 계정의 설정 페이지에서 **키를** 선택하여 이 페이지를 엽니다.

![키 관리 페이지](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* 매개 `GroupName` 변수의 경우 하이브리드 Runbook 작업자 그룹의 이름을 사용합니다. 이 그룹이 자동화 계정에 이미 있으면 현재 컴퓨터가 추가되고, 그룹이 없으면 추가됩니다.
* 매개 `EndPoint` 변수의 경우 키 관리 페이지에서 **URL** 항목을 사용합니다.
* 매개 `Token` 변수의 경우 키 관리 페이지에서 **기본 액세스 키** 항목을 사용합니다.
* 필요한 경우 매개 `Verbose` 변수를 설정하여 설치에 대한 세부 정보를 수신합니다.

### <a name="step-5----install-powershell-modules"></a>5단계 - PowerShell 모듈 설치

Runbook은 Azure Automation 환경에 설치된 모듈에 정의된 활동 및 cmdlet을 사용할 수 있습니다. 이러한 모듈은 온-프레미스 컴퓨터에 자동으로 배포되지 므로 수동으로 설치해야 합니다. Azure 모듈은 예외입니다. 이 모듈은 기본적으로 설치되며 Azure 자동화에 대한 모든 Azure 서비스 및 활동에 대한 cmdlet에 대한 액세스를 제공합니다.

하이브리드 Runbook Worker 기능의 주요 목적은 로컬 리소스를 관리하는 것이기 때문에 이러한 리소스, 특히 `PowerShellGet` 모듈을 지원하는 모듈을 설치해야 할 가능성이 큽니다. Windows PowerShell 모듈 설치에 대한 자세한 내용은 [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell)을 참조하십시오.

하이브리드 작업자가 자동으로 가져올 수 있도록 설치된 `PSModulePath` 모듈은 환경 변수에서 참조하는 위치에 있어야 합니다. 자세한 내용은 [PSModulePath의 모듈 설치를](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)참조하십시오.

## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.
* 하이브리드 Runbook 작업자 제거에 대한 지침은 [Azure 자동화 하이브리드 Runbook 작업자 제거를](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)참조하십시오.
* 하이브리드 Runbook 작업자 문제를 해결하는 방법에 대한 자세한 내용은 [Windows 하이브리드 Runbook 작업자 문제를 해결합니다.](troubleshoot/hybrid-runbook-worker.md#windows)
* 업데이트 관리 문제 해결을 위한 추가 단계는 [업데이트 관리: 문제 해결을](troubleshoot/update-management.md)참조하십시오.

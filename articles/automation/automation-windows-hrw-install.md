---
title: Azure Automation Windows Hybrid Runbook Worker
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 환경의 Windows 기반 컴퓨터에서 Runbook을 실행할 수 있도록 해주는 Azure Automation Hybrid Runbook Worker를 설치하는 방법에 대한 정보를 제공합니다.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 9f3e06f66996be4a2b43b64e6100c62a2fa41381
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649962"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker 배포

Azure Automation의 Hybrid Runbook Worker 기능을 사용하여 역할을 호스팅하는 컴퓨터에서 직접 그리고 환경의 리소스에 대해 Runbook을 실행하여 해당 로컬 리소스를 관리할 수 있습니다. Runbook은 Azure Automation에 저장 및 관리된 후 하나 이상의 지정된 컴퓨터에 전달됩니다. 이 문서에서는 Windows 컴퓨터에 Hybrid Runbook Worker를 배포 하는 방법을 설명 합니다.

Runbook Worker를 성공적으로 배포한 후에는 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 검토하여 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아봅니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Windows Hybrid Runbook Worker 설치 및 구성

Windows Hybrid Runbook Worker를 설치 하 고 구성 하려면 다음 방법 중 하나를 사용할 수 있습니다.

* Azure Vm의 경우 [windows 용 가상 머신 확장](../virtual-machines/extensions/oms-windows.md)을 사용 하 여 windows 용 Log Analytics 에이전트를 설치 합니다. 확장은 Azure 가상 컴퓨터에 Log Analytics 에이전트를 설치 하 고 Azure Resource Manager 템플릿 또는 PowerShell을 사용 하 여 기존 Log Analytics 작업 영역에 가상 컴퓨터를 등록 합니다. 에이전트가 설치 되 면 아래 [수동 배포](#manual-deployment) 섹션에서 **4 단계** 를 수행 하 여 Automation 계정의 Hybrid Runbook Worker 그룹에 VM을 추가할 수 있습니다.

* Automation runbook을 사용 하 여 Windows 컴퓨터를 구성 하는 프로세스를 완전히 자동화할 수 있습니다. 이 방법은 데이터 센터 또는 다른 클라우드 환경의 컴퓨터에 권장 되는 방법입니다.

* 단계별 절차에 따라 Azure가 아닌 VM에 Hybrid Runbook Worker 역할을 수동으로 설치 하 고 구성 합니다.

> [!NOTE]
> DSC(필요한 상태 구성)로 Hybrid Runbook Worker 역할을 지원하는 서버의 구성을 관리하려면 이들을 DSC 노드로 추가해야 합니다.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker에 대 한 최소 요구 사항

Windows Hybrid Runbook Worker에 대한 최소 요구 사항은 다음과 같습니다.

* Windows Server 2012 이상
* Windows PowerShell 5.1 이상 ([WMF 5.1 다운로드](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 이상
* 두 개의 코어
* 4GB의 RAM
* 443 포트(아웃바운드)

### <a name="network-configuration"></a>네트워크 구성

Hybrid Runbook Worker에 대한 상세한 네트워킹 요구 사항은 [네트워크 구성](automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="server-onboarding-for-management-with-automation-dsc"></a>자동화 DSC를 사용 하 여 관리를 위한 서버 온 보 딩

DSC를 통한 관리를 위한 온 보 딩 서버에 대 한 자세한 내용은 DSC를 사용 [Azure Automation 하 여 관리용 컴퓨터 온 보 딩](automation-dsc-onboarding.md)을 참조 하세요.

[업데이트 관리 솔루션](../operations-management-suite/oms-solution-update-management.md)을 사용 하도록 설정 하면 Log Analytics 작업 영역에 연결 된 모든 Windows 컴퓨터가이 솔루션에 포함 된 runbook을 지원 하기 위한 Hybrid Runbook Worker 자동으로 구성 됩니다. 그러나 Automation 계정에서 이미 정의한 모든 Hybrid Worker 그룹에 등록되지 않았습니다.

### <a name="adding-the-computer-to-a-hybrid-runbook-worker-group"></a>Hybrid Runbook Worker 그룹에 컴퓨터 추가

솔루션과 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용하는 한 Automation Runbook을 지원하기 위해 Automation 계정의 Hybrid Runbook Worker 그룹에 컴퓨터를 추가할 수 있습니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

## <a name="automated-deployment"></a>자동화된 배포

대상 컴퓨터에서 다음 단계를 수행 하 여 Windows Hybrid Worker 역할의 설치와 구성을 자동화 합니다.

### <a name="1-download-the-powershell-script"></a>1. PowerShell 스크립트를 다운로드 합니다.

Hybrid Runbook Worker 역할을 실행하는 컴퓨터에서 직접 또는 사용자 환경의 다른 컴퓨터에서 [PowerShell 갤러리](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)의 새로 OnPremiseHybridWorker.ps1 스크립트를 다운로드합니다. 스크립트를 작업자에 복사합니다. New-OnPremiseHybridWorker.ps1 스크립트에는 실행 중 다음 매개 변수가 필요합니다.

* *AAResourceGroupName*(필수): Automation 계정과 연결된 리소스 그룹의 이름
* *OMSResourceGroupName*(선택 사항): Log Analytics 작업 영역에 대한 리소스 그룹의 이름. 이 리소스 그룹을 지정하지 않으면 *AAResourceGroupName*을 사용합니다.
* *SubscriptionID* (필수): Automation 계정이 있는 AZURE 구독 ID입니다.
* *TenantID* (선택 사항): Automation 계정과 연결 된 테 넌 트 조직의 식별자입니다.
* *WorkspaceName*(선택 사항): Log Analytics 작업 영역 이름 Log Analytics 작업 영역이 없는 경우 스크립트에서 하나를 만들어 구성합니다.
* *Automationaccountname* (필수): Automation 계정의 이름입니다.
* *HybridGroupName*(필수): 이 시나리오를 지원하는 Runbook에 대한 대상으로 지정할 Hybrid Runbook Worker 그룹의 이름
* *자격 증명* (선택 사항): Azure 환경에 로그인 할 때 사용할 자격 증명입니다.
  
> [!NOTE]
> 솔루션을 사용 하도록 설정 하는 경우 Log Analytics 작업 영역 및 Automation 계정을 연결 하는 데 특정 영역만 지원 됩니다. 지원 되는 매핑 쌍 목록은 [Automation 계정 및 Log Analytics 작업 영역에 대 한 지역 매핑](how-to/region-mappings.md)을 참조 하세요.

### <a name="2-open-windows-powershell-command-line-shell"></a>2. Windows PowerShell 명령줄 셸을 엽니다.

관리자 모드의 **시작** 화면에서 **Windows PowerShell** 을 엽니다.

### <a name="3-run-the-powershell-script"></a>3. PowerShell 스크립트를 실행 합니다.

PowerShell 명령줄 셸에서 다운로드 한 스크립트가 포함 된 폴더로 이동 합니다. *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* 및 *-WorkspaceName* 매개 변수의 값을 변경합니다. 그런 다음, 스크립트를 실행합니다.

스크립트를 실행한 후에 Azure 인증을 요청하는 메시지가 나타납니다. 구독 관리자 역할의 구성원이 고 구독의 공동 관리자 인 계정으로 로그인 해야 합니다.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="4-install-nuget"></a>4. NuGet 설치

NuGet을 설치 하 고 Azure 자격 증명을 사용 하 여 인증 하는 것에 동의 하 라는 메시지가 표시 됩니다. 최신 NuGet 버전이 없으면 [사용 가능한 Nuget 배포 버전](https://www.nuget.org/downloads)에서 가져올 수 있습니다.

### <a name="5-verify-the-deployment"></a>5. 배포 확인

스크립트가 완료되면 **Hybrid Worker 그룹** 페이지에 새 그룹 및 멤버 수가 표시됩니다. 기존 그룹인 경우 멤버 수가 증가합니다. **Hybrid Worker 그룹** 페이지의 목록에서 그룹을 선택 하 고 **Hybrid worker** 타일을 선택할 수 있습니다. **Hybrid Worker** 페이지에서 나열된 그룹의 각 멤버를 확인합니다.

## <a name="manual-deployment"></a>수동 배포

대상 컴퓨터에서 Automation 환경에 대해 처음 두 단계를 한 번 수행 합니다. 그런 다음 각 작업자 컴퓨터에 대해 나머지 단계를 수행 합니다.

### <a name="1-create-a-log-analytics-workspace"></a>1. Log Analytics 작업 영역 만들기

Log Analytics 작업 영역이 아직 없는 경우 작업 영역을 만들기 전에 먼저 [Azure Monitor 로그 디자인 지침](../azure-monitor/platform/design-logs-deployment.md) 을 검토 합니다.

### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. 자동화 솔루션을 Log Analytics 작업 영역에 추가 합니다.

Automation 솔루션은 Hybrid Runbook Worker에 대한 지원을 포함하여 Azure Automation을 위한 기능을 추가합니다. Log Analytics 작업 영역에 솔루션을 추가 하면 다음 단계에서 설치할 에이전트 컴퓨터에 자동으로 작업자 구성 요소가 푸시 됩니다.

작업 영역에 **Automation** 솔루션을 추가 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. Windows 용 Log Analytics 에이전트 설치

Windows 용 Log Analytics 에이전트는 Azure Monitor Log Analytics 작업 영역에 컴퓨터를 연결 합니다. 컴퓨터에 에이전트를 설치 하 고 작업 영역에 연결 하면 Hybrid Runbook Worker 하는 데 필요한 구성 요소가 자동으로 다운로드 됩니다.

컴퓨터에 에이전트를 설치 하려면 [Azure Monitor 로그에 Windows 컴퓨터 연결](../log-analytics/log-analytics-windows-agent.md)의 지침을 따르세요. 이 과정을 여러 컴퓨터에 반복하여 사용자의 환경에 여러 작업자를 추가합니다.

몇 분 후에 에이전트가 Log Analytics 작업 영역에 성공적으로 연결 되 면 다음 쿼리를 실행 하 여 작업 영역에 하트 비트 데이터를 보내고 있는지 확인할 수 있습니다.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

검색 결과에는 컴퓨터에 대 한 하트 비트 레코드가 표시 되 고이 레코드가 서비스에 연결 되 고 보고 되 고 있음을 나타냅니다. 기본적으로 모든 에이전트는 하트 비트 레코드를 할당 된 작업 영역으로 전달 합니다. C:\Program Files\Microsoft Monitoring Agent\Agent.에 AzureAutomationFiles 라는 폴더가 있는 경우 에이전트가 Automation 솔루션을 올바르게 다운로드 했는지 확인할 수 있습니다. Hybrid Runbook Worker 버전을 확인 하려면 C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\로 이동 하 여 \\*버전* 하위 폴더를 확인 합니다.

### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. runbook 환경을 설치 하 고 Azure Automation에 연결

Log Analytics 작업 영역에 보고 하도록 에이전트를 구성 하는 경우 Automation 솔루션은 **add-hybridrunbookworker** cmdlet이 포함 된 HybridRegistration PowerShell 모듈을 푸시합니다. 이 cmdlet을 사용 하 여 컴퓨터에 runbook 환경을 설치 하 고 Azure Automation에 등록 합니다.

관리자 모드에서 PowerShell 세션을 열고 다음 명령을 실행하여 모듈을 가져옵니다.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

그런 후 다음 구문을 사용 하 여 **add-hybridrunbookworker** cmdlet을 실행 합니다.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Azure Portal의 "키 관리" 페이지에서이 cmdlet에 필요한 정보를 가져올 수 있습니다. Automation 계정의 **설정** 페이지에서 **키** 옵션을 선택하여 이 페이지를 엽니다.

![“키 관리” 페이지](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* *GroupName* 매개 변수의 경우 Hybrid Runbook Worker 그룹의 이름을 사용 합니다. 이 그룹이 자동화 계정에 이미 있으면 현재 컴퓨터가 추가되고, 그룹이 없으면 추가됩니다.
* *끝점* 매개 변수의 경우 키 관리 페이지의 **URL** 항목을 사용 합니다.
* *토큰* 매개 변수의 경우 키 관리 페이지에서 **기본 액세스 키** 항목을 사용 합니다.

설치에 대해 자세한 정보를 받으려면 *Add-HybridRunbookWorker*와 함께 **-Verbose** 스위치를 사용합니다.

### <a name="5-install-powershell-modules"></a>5. PowerShell 모듈 설치

Runbook은 Azure Automation 환경에 설치된 모듈에 정의된 활동 및 cmdlet을 사용할 수 있습니다. 이러한 모듈은 온-프레미스 컴퓨터에 자동으로 배포되지 않으므로 수동으로 설치해야 합니다. Azure 모듈은 예외입니다. 이 모듈은 기본적으로 설치 되며, Azure Automation에 대 한 모든 Azure 서비스 및 활동에 대 한 cmdlet에 대 한 액세스를 제공 합니다.

Hybrid Runbook Worker 기능의 주요 목적은 로컬 리소스를 관리 하는 것 이므로 이러한 리소스를 지 원하는 모듈 (특히 PowerShellGet 모듈)을 설치 해야 할 가능성이 높습니다. Windows PowerShell 모듈을 설치 하는 방법에 대 한 자세한 내용은 [Windows powershell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell)을 참조 하세요.

설치 된 모듈은 PSModulePath 환경 변수에서 참조 하는 위치에 있어야 하이브리드 작업자에서 자동으로 가져올 수 있습니다. 자세한 내용은 [Install Modules In PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.
* Hybrid Runbook Worker를 제거하는 방법의 지침은 [Azure Automation Hybrid Runbook Worker 제거](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)를 참조하세요.
* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Windows Hybrid Runbook Worker 문제 해결](troubleshoot/hybrid-runbook-worker.md#windows)을 참조하세요.
* 업데이트 관리 관련 문제를 해결하는 방법에 대한 추가 단계는 [업데이트 관리: 문제 해결](troubleshoot/update-management.md)을 참조하세요.

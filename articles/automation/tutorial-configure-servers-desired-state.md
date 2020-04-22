---
title: 원하는 상태로 서버 구성 및 Azure Automation을 사용하여 드리프트 관리
description: 자습서 - Azure Automation 상태 구성으로 서버 구성 관리
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678702"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>원하는 상태로 서버 구성 및 드리프트 관리

Azure Automation 상태 구성을 사용하면 서버 구성을 지정하고 시간이 지남에 따라 해당 서버가 지정된 상태에 있는지 확인할 수 있습니다.

> [!div class="checklist"]
> - Azure Automation DSC에서 관리되는 VM 등록
> - Azure Automation에 구성 업로드
> - 노드 구성으로 구성 컴파일
> - 관리되는 노드에 노드 구성 할당
> - 관리되는 노드에 대한 준수 상태 확인

이 자습서에서는 IIS가 VM에 설치되도록 하는 간단한 [DSC 구성을](/powershell/scripting/dsc/configurations/configurations) 사용합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

- Azure Automation 계정. Azure Automation 실행 계정 만들기에 대한 지침은 [Azure 실행 계정](automation-sec-configure-azure-runas-account.md)을 참조하세요.
- Windows Server 2008 R2 이상을 실행하는 Azure 리소스 관리자 VM(클래식이 아님)입니다. VM 을 만드는 방법에 대한 지침은 [Azure 포털에서 첫 번째 Windows 가상 컴퓨터 만들기를](../virtual-machines/virtual-machines-windows-hero-tutorial.md)참조하십시오.
- Azure PowerShell 모듈 버전 3.6 이상 - `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/azurerm/install-azurerm-ps)를 참조하세요.
- DSC(필요한 상태 구성)와 익숙함. DSC에 대한 자세한 내용은 [Windows PowerShell 원하는 상태 구성 개요를](/powershell/scripting/dsc/overview/overview)참조하십시오.

## <a name="support-for-partial-configurations"></a>부분 구성 지원

Azure 자동화 상태 구성은 [부분 구성의](/powershell/scripting/dsc/pull-server/partialconfigs)사용을 지원합니다. 이 시나리오에서 DSC는 여러 구성을 독립적으로 관리하도록 구성되며 각 구성은 Azure Automation에서 검색됩니다. 그러나 자동화 계정당 노드에는 하나의 구성만 할당할 수 있습니다. 즉, 노드에 대해 두 개의 구성을 사용하는 경우 두 개의 자동화 계정이 필요합니다.

끌어오기 서비스에서 부분 구성을 등록하는 방법에 대한 자세한 내용은 [부분 구성에](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)대한 설명서를 참조하십시오.

팀이 함께 작업하여 구성을 코드로 사용하여 서버를 공동으로 관리하는 방법에 대한 자세한 내용은 [CI/CD 파이프라인에서 DSC의 역할 이해를](/powershell/scripting/dsc/overview/authoringadvanced)참조하십시오.

## <a name="log-in-to-azure"></a>Azure에 로그인

[Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) cmdlet을 사용하여 Azure 구독에 로그인하고 화면의 지침을 따릅니다.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Azure Automation에 구성 만들기 및 업로드


텍스트 편집기에서 다음을 입력하고 **TestConfig.ps1로**로컬로 저장합니다.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> DSC Resources를 제공하는 여러 모듈을 가져와야 하는 고급 시나리오에서는 각 모듈에 `Import-DscResource` 고유한 라인이 구성되어 있는지 확인합니다.

[가져오기-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) cmdlet을 호출하여 구성을 자동화 계정에 업로드합니다.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>노드 구성으로 구성 컴파일

DSC 구성을 노드에 할당하려면 먼저 노드 구성으로 컴파일해야 합니다. [DSC 구성을](/powershell/scripting/dsc/configurations/configurations)참조하십시오.

[Start-AzAutomationDsc컴파일작업](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) cmdlet을 호출하여 자동화 `TestConfig` 계정에 명명된 `TestConfig.WebServer` 노드 구성으로 구성을 컴파일합니다.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>상태 구성에서 관리하려면 VM 등록

Azure Automation 상태 구성을 사용하여 Azure VM(클래식 및 Resource Manager), 온-프레미스 VM, Linux 머신, AWS VM 및 온-프레미스 물리적 머신을 관리할 수 있습니다. 이 항목에서는 Azure Resource Manager VM만 등록하는 방법에 대해 설명합니다. 다른 유형의 머신 등록에 대한 정보는 [Azure Automation 상태 구성을 통한 관리용 머신 온보드](automation-dsc-onboarding.md)를 참조하세요.

[레지스터-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) cmdlet을 호출하여 Azure 자동화 상태 구성에 VM을 관리 노드로 등록합니다. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>구성 모드 설정 지정

[레지스터-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) cmdlet을 사용하여 VM을 관리 노드로 등록하고 구성 속성을 지정합니다. 예를 들어 `ApplyOnly` `ConfigurationMode` 속성 값으로 지정하여 컴퓨터의 상태를 한 번만 적용하도록 지정할 수 있습니다. 상태 구성은 초기 검사 후 구성을 적용하려고 하지 않습니다.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

속성을 사용하여 DSC가 구성 상태를 확인하는 빈도를 지정할 수도 있습니다. `ConfigurationModeFrequencyMins` DSC 구성 설정에 대한 자세한 내용은 [로컬 구성 관리자 구성](/powershell/scripting/dsc/managing-nodes/metaConfig)을 참조하세요.

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>관리되는 노드에 노드 구성 할당

이제 구성하려는 VM에 컴파일된 노드 구성을 할당할 수 있습니다.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

이렇게 하면 등록된 DSC 노드에 명명된 `TestConfig.WebServer` 노드 구성이 `DscVm`할당됩니다. 기본적으로 DSC 노드에서는 30분마다 노드 구성이 준수되는지 확인합니다. 준수 확인 간격을 변경하는 방법에 대한 자세한 내용은 [로컬 구성 관리자 구성](/powershell/scripting/dsc/managing-nodes/metaConfig)을 참조하세요.

## <a name="check-the-compliance-status-of-a-managed-node"></a>관리되는 노드에 대한 준수 상태 확인

[Get-AzAutomationDscNodeReport](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0) cmdlet을 사용하여 관리되는 노드의 규정 준수 상태에 대한 보고서를 얻을 수 있습니다.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>서비스에서 노드 제거

Azure 자동화 상태 구성에 노드를 추가하면 로컬 구성 관리자의 설정이 서비스에 등록하고 구성 및 컴퓨터를 구성하는 데 필요한 모듈을 가져오도록 설정됩니다.
서비스에서 노드를 제거하도록 선택한 경우 Azure 포털 또는 Az cmdlet을 사용하여 노드를 제거할 수 있습니다.

> [!NOTE]
> 서비스에서 노드를 등록 취소하면 로컬 구성 관리자 설정만 설정되므로 노드가 더 이상 서비스에 연결되지 않습니다.
> 현재 노드에 적용 된 구성에 영향을 주지 않습니다.
> 현재 구성을 제거하려면 [PowerShell을](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) 사용하거나 로컬 구성 파일을 삭제합니다(Linux 노드의 유일한 옵션).

### <a name="azure-portal"></a>Azure portal

Azure 자동화에서 목치표에서 **DSC(상태 구성)를** 클릭합니다.
다음으로 **노드를** 클릭하여 서비스에 등록된 노드 목록을 봅니다.
제거할 노드의 이름을 클릭합니다.
열리는 노드 보기에서 **등록 취소를**클릭합니다.

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 Azure 자동화 상태 구성 서비스에서 노드를 등록 취소하려면 cmdlet [레지스터 등록 취소-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0)에 대한 설명서를 따르십시오.

## <a name="next-steps"></a>다음 단계

- 시작하려면 Azure [자동화 상태 구성을 시작하십시오.](automation-dsc-getting-started.md)
- 노드온보딩 하는 방법을 알아보려면 [Azure 자동화 상태 구성에 의해 관리를 위한 온보딩 컴퓨터를](automation-dsc-onboarding.md)참조하십시오.
- 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure 자동화 상태 구성의 구성 컴파일을](automation-dsc-compile.md)참조하십시오.
- PowerShell cmdlet 참조에 대 한 [Azure 자동화 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조 하십시오.
- 가격 정보는 [Azure 자동화 상태 구성 가격 책정을](https://azure.microsoft.com/pricing/details/automation/)참조하십시오.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.

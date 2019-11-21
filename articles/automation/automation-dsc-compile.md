---
title: Azure Automation 상태 구성에서 구성 컴파일
description: 이 문서에서는 Azure Automation에 대한 DSC(필요한 상태 구성) 구성을 컴파일하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6d3cca9d8954d9ac158d88b393c46672da3faa19
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231712"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Azure Automation 상태 구성에서 DSC 구성 컴파일

You can compile Desired State Configuration (DSC) configurations in two ways with Azure Automation State Configuration: in Azure and in Windows PowerShell. 다음 테이블에서는 각각의 특징을 기반으로 어떤 방법을 언제 사용할지 결정하도록 합니다.

- Azure State Configuration compilation service
  - Beginner method with interactive user interface
   - 작업 상태를 쉽게 추적

- Windows PowerShell
  - Call from Windows PowerShell on local workstation or build service
  - Integrate with development test pipeline
  - Provide complex parameter values
  - Work with node and non-node data at scale
  - Significant performance improvement

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compiling a DSC Configuration in Azure State Configuration

### <a name="portal"></a>포털

1. Automation 계정에서 **상태 구성(DSC)** 을 클릭합니다.
1. **구성** 탭 및 컴파일할 구성 이름을 차례로 클릭합니다.
1. **컴파일**을 클릭합니다.
1. 구성에 매개 변수가 없는 경우 컴파일할지 확인하라는 메시지가 표시됩니다. 구성에 매개 변수가 있는 경우 **컴파일 구성** 블레이드를 열어 매개 변수 값을 제공할 수 있습니다. 매개 변수에 대한 자세한 내용은 다음 [**기본 매개 변수**](#basic-parameters) 섹션을 참조하세요.
1. **컴파일 작업** 페이지를 열어서 컴파일 작업의 상태 및 노드 구성(MOF 구성 문서)을 추적할 수 있습니다. 이렇게 하면 Azure Automation 상태 구성 끌어오기 서버에 배치됩니다.

### <a name="azure-powershell"></a>Azure PowerShell

Windows PowerShell을 사용하여 컴파일하기 시작하는 데 [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) 를 사용할 수 있습니다. 다음 샘플 코드는 **SampleConfig**이라는 DSC 구성의 컴파일을 시작합니다.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` 는 해당 상태를 추적하는 데 사용할 수 있는 컴파일 작업 개체를 반환합니다. 그런 다음, 이 컴파일 작업 개체를 [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob)과 함께 사용하여
컴파일 작업 상태를 확인하고, [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)을 사용하여
스트림(출력)을 볼 수 있습니다. 다음 샘플 코드는 **SampleConfig** 구성의 컴파일을 시작하고 완료될 때까지 대기한 다음 해당 스트림을 표시합니다.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>기본 매개 변수

매개 변수 형식 및 속성을 포함하는 DSC 구성의 매개 변수 선언은 Azure Automation runbook과 동일하게 작동합니다. [Azure Automation에서 runbook 시작](automation-starting-a-runbook.md) 을 참조하여 runbook 매개 변수에 대한 자세한 내용을 알아봅니다.

다음 예제에서는 **FeatureName** 및 **IsPresent**라는 두 개의 매개 변수를 사용하여 **ParametersExample.sample** 노드 구에서 속성의 값을 결정하며 이는 컴파일하는 동안 생성됩니다.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Azure Automation 상태 구성 포털에서 또는 Azure PowerShell로 기본 매개 변수를 사용하는 DSC 구성을 컴파일할 수 있습니다.

#### <a name="portal"></a>포털

포털에서 **컴파일**을 클릭한 후에 매개 변수 값을 입력할 수 있습니다.

![구성 컴파일 매개 변수](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell은 키와 매개 변수 이름이 일치하고 매개 변수 값과 값이 같은 [hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) 에 매개 변수가 필요합니다.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

PSCredentials을 매개 변수로 전달하는 방법에 대한 정보는 아래의 [자격 증명 자산](#credential-assets)을 참조하세요.

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>Compiling configurations in Azure Automation that contain Composite Resources

**복합 리소스**를 사용하면 구성 내에서 중첩된 리소스로 DSC 구성을 사용할 수 있습니다. 이렇게 하면 단일 리소스에 여러 구성을 적용할 수 있습니다. **복합 리소스**에 대해 자세히 알아보려면 [복합 리소스: DSC 구성을 리소스로 사용](/powershell/scripting/dsc/resources/authoringresourcecomposite)을 참조하세요.

> [!NOTE]
> In order for configurations containing **Composite Resources** to compile correctly, you must first ensure that any DSC Resources that the composite relies on are first imported in to Azure Automation.

Adding a DSC **Composite Resource** is no different than adding any PowerShell module to Azure Automation.
The step by step instruction for this process is documented in the article [Manage Modules in Azure Automation](/azure/automation/shared-resources/modules).

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Managing ConfigurationData when compiling configuration in Azure Automation

**ConfigurationData** 를 사용하면 PowerShell DSC를 사용하는 동안 구조적 구성을 환경별 구성과 구분할 수 있습니다. [PowerShell DSC의 "위치"에서 "대상" 분리](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) 를 참조하여 **ConfigurationData**에 대해 자세히 알아봅니다.

> [!NOTE]
> You can use **ConfigurationData** when compiling in Azure Automation State Configuration using Azure PowerShell but not in the Azure portal.

다음 예제 DSC 구성은 **$ConfigurationData** 및 **$AllNodes** 키워드를 통해 **ConfigurationData**를 사용합니다. 또한 다음과 같이 예를 들어 [**xWebAdministration** 모듈](https://www.powershellgallery.com/packages/xWebAdministration/)이 필요합니다.

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

You can compile the preceding DSC configuration with Windows PowerShell. The following script adds two node configurations to the Azure Automation State Configuration Pull Service: **ConfigurationDataSample.MyVM1** and **ConfigurationDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Working with Assets in Azure Automation during compilation

자산 참조는 Azure Automation 상태 구성 및 Runbook에서 동일합니다. 자세한 내용은

- [인증서](automation-certificates.md)
- [연결](automation-connections.md)
- [자격 증명](automation-credentials.md)
- [변수](automation-variables.md)

#### <a name="credential-assets"></a>자격 증명 자산

Azure Automation에서 DSC 구성은 `Get-AutomationPSCredential` cmdlet를 사용하여 Automation 자격 증명 자산을 참조할 수 있습니다. 구성에 **PSCredential** 형식의 매개 변수가 있는 경우 자격 증명을 검색하는 cmdlet에 Azure Automation 자격 증명 자산의 문자열 이름을 전달하여 `Get-AutomationPSCredential` cmdlet을 사용할 수 있습니다. 그런 다음, 해당 개체를 **PSCredential** 개체가 필요한 매개 변수로 사용할 수 있습니다. 내부적으로 해당 이름을 가진 Azure Automation 자격 증명 자산은 검색되고 구성에 전달됩니다. 아래 예제에서는 작업 중인 이 항목을 보여줍니다.

자격 증명을 노드 구성(MOF 구성 문서)에서 안전하게 유지하려면 노드 구성 MOF 파일에 자격 증명을 암호화해야 합니다. 그러나 현재 PowerShell DSC가 노드 구성 MOF을 생성하는 동안 자격 증명을 일반 텍스트로 출력해도 되는지 알아야 합니다. PowerShell DSC은 Azure Automation이 컴파일 작업을 통해 생성된 후에 전체 MOF 파일을 암호화한다는 것을 모르기 때문입니다.

You can tell PowerShell DSC that it is okay for credentials to be outputted in plain text in the generated node configuration MOFs using Configuration Data. DSC 구성에 표시되고 자격 증명을 사용하는 각 노드 블록 이름의 경우 **ConfigurationData**를 통해 `PSDscAllowPlainTextPassword = $true`을 전달해야 합니다.

다음 예제에서는 Automation 자격 증명 자산을 사용하는 DSC 구성을 보여줍니다.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

PowerShell로 이전 DSC 구성을 컴파일할 수 있습니다. 다음 PowerShell은 Azure Automation 상태 구성 끌어오기 서버에 **CredentialSample.MyVM1** 및 **CredentialSample.MyVM2**라는 두 개의 노드 구성을 추가합니다.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> 컴파일이 완료되면 오류 메시지 **'Microsoft.PowerShell.Management' 스냅인을 이미 가져왔으므로 'Microsoft.PowerShell.Management' 모듈을 가져오지 못했습니다.** 가 표시될 수 있습니다. 이 경고는 무시해도 됩니다.

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Compiling configurations in Windows PowerShell and publishing to Azure Automation

Azure 외부에서 컴파일한 노드 구성(MOF)을 가져올 수도 있습니다.
This includes compiling from a developer workstation or in a service such as [Azure DevOps](https://dev.azure.com).
There are multiple advantages to this approach including performance and reliability.
Compiling in Windows PowerShell also provides the option to sign configuration content.
서명된 노드 구성은 DSC 에이전트에 의해 관리되는 노드에서 로컬로 확인되어 인증된 출처에서 가져온 노드에 구성을 적용할 수 있습니다.

> [!NOTE]
> 노드 구성 파일은 Azure Automation으로 가져오기 위해 1MB보다 크지 않아야 합니다.

노드 구성에 서명하는 방법에 대한 자세한 내용은 [WMF 5.1의 향상된 기능 - 구성 및 모듈에 서명하는 방법](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)을 참조하세요.

### <a name="compiling-a-configuration-in-windows-powershell"></a>Compiling a configuration in Windows PowerShell

The process to compile DSC configurations in Windows PowerShell is included in the PowerShell DSC documentation [Write, Compile, and Apply a Configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
This can be executed from a developer workstation or within a build service such as [Azure DevOps](https://dev.azure.com).

The MOF file or files produced by compiling the configuration can then be imported directly in to the Azure State Configuration service.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Azure Portal에서 노드 구성 가져오기

1. Automation 계정 페이지의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. **상태 구성(DSC)** 페이지에서 **구성** 탭을 클릭한 다음, **+ 추가**를 클릭합니다.
1. **가져오기** 페이지에서 **노드 구성 파일** 텍스트 상자 옆의 폴더 아이콘을 클릭하여 로컬 컴퓨터에서 노드 구성 파일(MOF)을 찾습니다.

   ![로컬 파일 찾기](./media/automation-dsc-compile/import-browse.png)

1. **구성 이름** 텍스트 상자에 이름을 입력합니다. 이 이름은 노드 구성이 컴파일된 구성 이름과 일치해야 합니다.
1. **확인**을 클릭합니다.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Importing a node configuration with Azure PowerShell

[Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) cmdlet을 사용하여 노드 구성을 Automation 계정으로 가져올 수 있습니다.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Automation 상태 구성 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.

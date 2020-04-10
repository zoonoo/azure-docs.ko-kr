---
title: Azure 자동화 상태 구성에서 DSC 구성 컴파일
description: 이 문서에서는 Azure Automation에 대한 DSC(필요한 상태 구성) 구성을 컴파일하는 방법을 설명합니다.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 6ce55b83f5547534e44d689adccdd952abc025d5
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010956"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Azure 자동화 상태 구성에서 DSC 구성 컴파일

Azure 자동화 상태 구성에서 원하는 상태 구성(DSC) 구성을 다음과 같은 방법으로 컴파일할 수 있습니다.

- Azure 상태 구성 컴파일 서비스
  - 대화형 사용자 인터페이스를 사용하는 초보자 방법
   - 작업 상태를 쉽게 추적

- Windows PowerShell
  - 로컬 워크스테이션에서 Windows PowerShell에서 전화 걸기 또는 서비스 구축
  - 개발 테스트 파이프라인과 통합
  - 복잡한 매개 변수 값 제공
  - 노드 및 비노드 데이터 확장작업
  - 성능 이량

DSC(Azure 원하는 상태 구성) 확장이 있는 Azure 리소스 관리자 템플릿을 사용하여 Azure VM에 구성을 푸시할 수도 있습니다. Azure DSC 확장은 Azure VM 에이전트 프레임워크를 사용하여 Azure VM에서 실행되는 DSC 구성을 제공하고 적용하며 보고합니다. Azure 리소스 관리자 템플릿을 사용하여 컴파일 세부 정보는 [Azure 리소스 관리자 템플릿을 사용하여 원하는 상태 구성 확장을](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details)참조하십시오. 

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Azure 상태 구성에서 DSC 구성 컴파일

### <a name="portal"></a>포털

1. Automation 계정에서 **상태 구성(DSC)** 을 클릭합니다.
1. **구성** 탭 및 컴파일할 구성 이름을 차례로 클릭합니다.
1. **컴파일**을 클릭합니다.
1. 구성에 매개 변수가 없는 경우 컴파일할지 확인하라는 메시지가 표시됩니다. 구성에 매개 변수가 있는 경우 매개 변수 값을 제공할 수 있도록 **구성 컴파일** 블레이드가 열립니다.
1. 컴파일 작업 페이지가 열리므로 컴파일 작업 상태를 추적할 수 있습니다. 이 페이지를 사용하여 Azure 자동화 상태 구성 끌어오기 서버에 배치된 노드 구성(MOF 구성 문서)을 추적할 수도 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell

[시작-AzAutomationDsc 컴파일작업을](/powershell/module/az.automation/start-azautomationdsccompilationjob) 사용하여 Windows PowerShell으로 컴파일을 시작할 수 있습니다. 다음 샘플 코드는 SampleConfig라는 DSC 구성의 컴파일을 시작합니다.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**Start-AzAutomationDsc컴파일작업은** 작업 상태를 추적하는 데 사용할 수 있는 컴파일 작업 개체를 반환합니다. 그런 다음 [Get-AzAutomationDscCompilationJob을](/powershell/module/az.automation/get-azautomationdsccompilationjob) 사용하여 컴파일 작업의 상태를 확인하고 [Get-AzAutomationDsc 컴파일작업작업](/powershell/module/az.automation/get-azautomationdscconfiguration) 출력을 사용하여 스트림(출력)을 볼 수 있습니다. 다음 샘플은 SampleConfig 구성의 컴파일을 시작하고 완료될 때까지 기다린 다음 스트림을 표시합니다.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>기본 매개 변수 선언

매개 변수 형식 및 속성을 포함하는 DSC 구성의 매개 변수 선언은 Azure Automation runbook과 동일하게 작동합니다. [Azure Automation에서 runbook 시작](automation-starting-a-runbook.md) 을 참조하여 runbook 매개 변수에 대한 자세한 내용을 알아봅니다.

다음 예제에서는 *FeatureName* 및 *IsPresent*라는 두 개의 매개 변수를 사용하여 ParametersExample.sample 노드 구에서 속성의 값을 결정하며 이는 컴파일하는 동안 생성됩니다.

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

Azure 자동화 상태 구성 포털 또는 Azure PowerShell을 사용하여 기본 매개 변수를 사용하는 DSC 구성을 컴파일할 수 있습니다.

#### <a name="portal"></a>포털

포털에서 **컴파일**을 클릭한 후에 매개 변수 값을 입력할 수 있습니다.

![구성 컴파일 매개 변수](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell은 키가 매개 변수 이름과 일치하고 값이 매개 변수 값과 같은 [해시 테이블의](/powershell/module/microsoft.powershell.core/about/about_hash_tables)매개 변수가 필요합니다.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

PSCredentials을 매개 변수로 전달하는 방법에 대한 정보는 아래의 [자격 증명 자산](#credential-assets) 을 참조하세요.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Azure 자동화에서 복합 리소스를 포함하는 구성 컴파일

**복합 리소스** 기능을 사용하면 DSC 구성을 구성 내부에 중첩된 리소스로 사용할 수 있습니다. 이 기능을 사용하면 여러 구성을 단일 리소스에 적용할 수 있습니다. [복합 리소스: DSC 구성을 리소스로 사용하여](/powershell/scripting/dsc/resources/authoringresourcecomposite) 복합 리소스에 대해 자세히 알아봅니다.

> [!NOTE]
> 복합 리소스가 포함된 구성이 올바르게 컴파일되도록 먼저 복합 리소스가 사용하는 모든 DSC 리소스를 Azure 자동화로 가져와야 합니다. DSC 복합 리소스를 추가하는 것은 Azure 자동화에 PowerShell 모듈을 추가하는 것도 마찬가지입니다. 이 프로세스는 [Azure 자동화의 모듈 관리에 설명되어 있습니다.](/azure/automation/shared-resources/modules)

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Azure 자동화에서 구성을 컴파일할 때 ConfigurationData 관리

**ConfigurationData** 를 사용하면 PowerShell DSC를 사용하는 동안 구조적 구성을 환경별 구성과 구분할 수 있습니다. 자세한 내용은 [PowerShell DSC의 "위치"에서 "What"을 분리하는](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/)것을 참조하십시오.

> [!NOTE]
> Azure 자동화 상태 구성에서 컴파일할 때 Azure PowerShell에서 **ConfigurationData를** 사용할 수 있지만 Azure 포털에서는 사용할 수 없습니다.

다음 예제 DSC 구성은 $ConfigurationData 및 $AllNodes 키워드를 통해 **ConfigurationData**를 사용합니다. 이 예제에서는 [xWeb관리 모듈도](https://www.powershellgallery.com/packages/xWebAdministration/) 필요합니다.

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

Windows PowerShell을 통해 위의 DSC 구성을 컴파일할 수 있습니다. 다음 스크립트는 Azure 자동화 상태 구성 풀 서비스에 두 개의 노드 구성을 추가합니다: ConfigurationDataSample.MyVM1 및 ConfigurationDataSample.MyVM3.

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>컴파일 하는 동안 Azure 자동화에서 자산작업

자산 참조는 Azure 자동화 상태 구성 및 Runbook 모두에서 동일합니다. 자세한 내용은

- [인증서](automation-certificates.md)
- [연결](automation-connections.md)
- [자격 증명](automation-credentials.md)
- [variables](automation-variables.md)

#### <a name="credential-assets"></a>자격 증명 자산

Azure 자동화의 DSC 구성은 **Get-AutomationPSCredential** cmdlet을 사용하여 자동화 자격 증명 자산을 참조할 수 있습니다. 구성에 **PSCredential** 개체를 지정하는 매개 변수가 있는 경우 Azure Automation 자격 증명 자산의 문자열 이름을 cmdlet에 전달하여 자격 증명을 검색하여 **Get-AutomationPSCredential를** 사용합니다. 그런 다음 **PSCredential** 개체가 필요한 매개 변수에 해당 개체를 사용합니다. 내부적으로 해당 이름을 가진 Azure Automation 자격 증명 자산은 검색되고 구성에 전달됩니다. 아래 예제에서는 이 시나리오가 실행 중입니다.

자격 증명을 노드 구성(MOF 구성 문서)에서 안전하게 유지하려면 노드 구성 MOF 파일에 자격 증명을 암호화해야 합니다. 현재 Node 구성 MOF 생성 중에 일반 텍스트로 자격 증명을 출력할 수 있는 PowerShell DSC 권한을 제공해야 합니다. PowerShell DSC는 Azure Automation이 컴파일 작업을 통해 생성 후 전체 MOF 파일을 암호화한다는 것을 인식하지 못하고 있습니다.

PowerShell DSC는 구성 데이터를 사용하여 생성된 노드 구성 MoF에서 자격 증명을 일반 텍스트로 출력해도 괜찮다고 알릴 수 있습니다. DSC `PSDscAllowPlainTextPassword = $true` 구성에 나타나고 자격 증명을 사용하는 각 노드 블록 이름에 대해 **ConfigurationData를** 통해 전달해야 합니다.

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

PowerShell로 이전 DSC 구성을 컴파일할 수 있습니다. 다음 PowerShell 코드는 Azure 자동화 상태 구성 풀 서버인 CredentialSample.MyVM1 및 CredentialSample.MyVM2에 두 개의 노드 구성을 추가합니다.

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> 컴파일이 완료되면 'Microsoft.PowerShell.Management' 스냅인이 이미 가져왔기 때문에 'Microsoft.PowerShell.Management' 모듈을 가져오지 않은 오류 메시지가 나타납니다. 이 메시지는 무시해도 됩니다.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Windows PowerShell에서 DSC 구성 컴파일

Azure 외부에서 컴파일한 노드 구성(MOF)을 가져올 수도 있습니다. 가져오기에는 개발자 워크스테이션 또는 Azure DevOps 와 같은 서비스에서 [컴파일된 컴파일이 포함됩니다.](https://dev.azure.com) 이 접근 방식에는 성능 및 안정성을 비롯한 여러 가지 장점이 있습니다.

Windows PowerShell에서 컴파일하면 구성 콘텐츠에 서명할 수도 있습니다. DSC 에이전트는 관리되는 노드에서 로컬로 서명된 노드 구성을 확인합니다. 확인을 통해 노드에 적용된 구성이 승인된 소스에서 온 것을 확인합니다.

> [!NOTE]
> 노드 구성 파일은 Azure Automation으로 가져오기 위해 1MB보다 크지 않아야 합니다.

노드 구성 의 서명에 대한 자세한 내용은 [WMF 5.1 - 구성 및 모듈에 서명하는 방법의 개선](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)을 참조하십시오.

### <a name="compile-the-dsc-configuration"></a>DSC 구성 컴파일

Windows PowerShell에서 DSC 구성을 컴파일하는 프로세스는 PowerShell DSC [설명서 쓰기, 컴파일 및 적용 구성](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)에 포함됩니다.
개발자 워크스테이션 또는 [Azure DevOps](https://dev.azure.com)와 같은 빌드 서비스 내에서 이 프로세스를 실행할 수 있습니다. 그런 다음 구성을 Azure 상태 구성 서비스로 컴파일하여 생성된 MOF 파일을 가져올 수 있습니다.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Azure 포털에서 노드 구성 가져오기

1. Automation 계정 페이지의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. 상태 구성(DSC) 페이지에서 구성 탭을 클릭한 다음 **+ 추가**를 **클릭합니다.**
1. 가져오기 페이지에서 **노드 구성 파일** 텍스트 상자 옆에 있는 폴더 아이콘을 클릭하여 로컬 컴퓨터에서 노드 구성 파일(MOF)을 찾아봅니다.

   ![로컬 파일 찾기](./media/automation-dsc-compile/import-browse.png)

1. **구성 이름** 필드에 이름을 입력합니다. 이 이름은 노드 구성이 컴파일된 구성 이름과 일치해야 합니다.
1. **확인**을 클릭합니다.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Azure PowerShell을 사용 하 고 노드 구성 가져오기

[가져오기-AzAutomationDscConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) cmdlet을 사용하여 노드 구성을 자동화 계정으로 가져올 수 있습니다.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>다음 단계

- 시작하려면 Azure [자동화 상태 구성을 시작하십시오.](automation-dsc-getting-started.md)
- 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure 자동화 상태 구성의 구성 컴파일을](automation-dsc-compile.md)참조하십시오.
- PowerShell cmdlet 참조에 대 한 [Azure 자동화 상태 구성 cmdlet](/powershell/module/az.automation)을 참조 하십시오.
- 가격 정보는 [Azure 자동화 상태 구성 가격 책정을](https://azure.microsoft.com/pricing/details/automation/)참조하십시오.
- 연속 배포 파이프라인에서 Azure 자동화 상태 구성을 사용하는 예제를 보려면 [Azure 자동화 상태 구성 및 초콜릿을 사용하여 가상 시스템에 대한 지속적인 배포를](automation-dsc-cd-chocolatey.md)참조하십시오.

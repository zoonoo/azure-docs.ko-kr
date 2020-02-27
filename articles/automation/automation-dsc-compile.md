---
title: Azure Automation 상태 구성에서 구성 컴파일
description: 이 문서에서는 Azure Automation에 대한 DSC(필요한 상태 구성) 구성을 컴파일하는 방법을 설명합니다.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: a4a46837e366caf2860aa0d6597ec7aebc94905a
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650013"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Azure Automation 상태 구성에서 DSC 구성 컴파일

다음과 같은 방법으로 Azure Automation 상태 구성에서 DSC (필요한 상태 구성) 구성을 컴파일할 수 있습니다.

- Azure 상태 구성 컴파일 서비스
  - 대화형 사용자 인터페이스를 사용 하는 초보자 방법
   - 작업 상태를 쉽게 추적

- Windows PowerShell
  - 로컬 워크스테이션 또는 빌드 서비스의 Windows PowerShell에서 호출
  - 개발 테스트 파이프라인과 통합
  - 복잡 한 매개 변수 값 제공
  - 규모에 맞게 노드 및 비 노드 데이터 작업
  - 뛰어난 성능 향상

컴파일 정보는 [필요한 상태 구성 확장 Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details)을 참조 하세요.

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Azure 상태 구성에서 DSC 구성 컴파일

### <a name="portal"></a>포털

1. Automation 계정에서 **상태 구성(DSC)** 을 클릭합니다.
1. **구성** 탭 및 컴파일할 구성 이름을 차례로 클릭합니다.
1. **컴파일**을 클릭합니다.
1. 구성에 매개 변수가 없는 경우 컴파일할지 여부를 확인 하는 메시지가 표시 됩니다. 구성에 매개 변수가 있는 경우 매개 변수 값을 제공할 수 있도록 **컴파일 구성** 블레이드가 열립니다.
1. 컴파일 작업 상태를 추적할 수 있도록 컴파일 작업 페이지가 열립니다. 또한이 페이지를 사용 하 여 Azure Automation 상태 구성 끌어오기 서버에 배치 된 노드 구성 (MOF 구성 문서)을 추적할 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell

[AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) 를 사용 하 여 Windows PowerShell을 사용 하 여 컴파일을 시작할 수 있습니다. 다음 샘플 코드는 SampleConfig 이라는 DSC 구성의 컴파일을 시작 합니다.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**AzAutomationDscCompilationJob** 는 작업 상태를 추적 하는 데 사용할 수 있는 컴파일 작업 개체를 반환 합니다. 그런 다음이 컴파일 작업 개체를 [AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) 와 함께 사용 하 여 컴파일 작업의 상태를 확인 하 고 [AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) 를 사용 하 여 스트림 (출력)을 볼 수 있습니다. 다음 샘플에서는 SampleConfig 구성의 컴파일을 시작 하 고 완료 될 때까지 기다린 다음 해당 스트림을 표시 합니다.

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

다음 예제에서는 *FeatureName* 및 *IsPresent*라는 두 매개 변수를 사용 하 여 컴파일 중에 생성 된 ParametersExample 노드 구성의 속성 값을 확인 합니다.

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

Azure Automation 상태 구성 포털에서 또는 Azure PowerShell를 사용 하 여 기본 매개 변수를 사용 하는 DSC 구성을 컴파일할 수 있습니다.

#### <a name="portal"></a>포털

포털에서 **컴파일**을 클릭한 후에 매개 변수 값을 입력할 수 있습니다.

![구성 컴파일 매개 변수](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell에는 키가 매개 변수 이름과 일치 하 고 값이 매개 변수 값과 일치 하는 [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables)의 매개 변수가 필요 합니다.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

PSCredentials을 매개 변수로 전달하는 방법에 대한 정보는 아래의 [자격 증명 자산](#credential-assets)을 참조하세요.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Azure Automation의 복합 리소스를 포함 하는 컴파일 구성

**복합 리소스** 기능을 사용 하면 구성 내에서 중첩 리소스로 DSC 구성을 사용할 수 있습니다. 이 기능을 사용 하면 단일 리소스에 여러 구성의 응용 프로그램을 적용할 수 있습니다. 복합 리소스에 대해 자세히 알아보려면 [복합 리소스: DSC 구성을 리소스로 사용](/powershell/scripting/dsc/resources/authoringresourcecomposite) 을 참조 하세요.

> [!NOTE]
> 복합 리소스가 포함 된 구성이 올바르게 컴파일되는 경우에는 먼저 복합 리소스가 의존 하는 DSC 리소스를 Azure Automation으로 가져와야 합니다. DSC 복합 리소스 추가는 Azure Automation에 대 한 PowerShell 모듈을 추가 하는 것과 다르지 않습니다. 이 프로세스는 [Azure Automation 모듈 관리](/azure/automation/shared-resources/modules)에 설명 되어 있습니다.

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Azure Automation에서 구성을 컴파일할 때 ConfigurationData 관리

**ConfigurationData** 를 사용하면 PowerShell DSC를 사용하는 동안 구조적 구성을 환경별 구성과 구분할 수 있습니다. 자세한 내용은 [POWERSHELL DSC의 "What"에서 "Where](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx)"를 구분 하는 방법을 참조 하세요.

> [!NOTE]
> Azure Automation 상태 구성에서 컴파일하는 경우에는 Azure Portal에서 **ConfigurationData** 를 사용할 수 있지만 Azure PowerShell는 사용할 수 없습니다.

다음 예제 DSC 구성에서는 $ConfigurationData 및 $AllNodes 키워드를 통해 **ConfigurationData** 을 사용 합니다. 또한이 예제에는 [Xwebadministration 모듈이](https://www.powershellgallery.com/packages/xWebAdministration/) 필요 합니다.

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

Windows PowerShell을 사용 하 여 이전 DSC 구성을 컴파일할 수 있습니다. 다음 스크립트는 Azure Automation 상태 구성 끌어오기 서비스에 두 개의 노드 구성을 추가 합니다. MyVM1 및 ConfigurationMyVM3.

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>컴파일하는 동안 Azure Automation의 자산 작업

자산 참조는 Azure Automation 상태 구성과 runbook 모두에서 동일 합니다. 자세한 내용은 다음 항목을 참조하세요.

- [인증서](automation-certificates.md)
- [연결](automation-connections.md)
- [자격 증명](automation-credentials.md)
- [변수](automation-variables.md)

#### <a name="credential-assets"></a>자격 증명 자산

Azure Automation의 DSC 구성은 **Get AutomationPSCredential** cmdlet을 사용 하 여 자동화 자격 증명 자산을 참조할 수 있습니다. 구성에 **PSCredential** 개체를 지정 하는 매개 변수가 있는 경우 Azure Automation 자격 증명 자산의 문자열 이름을 cmdlet에 전달 하 여 **Get automationpscredential** 을 사용 하 여 자격 증명을 검색 합니다. 그런 다음이 개체를 사용 하 여 **PSCredential** 개체가 필요한 매개 변수를 만듭니다. 내부적으로 해당 이름을 가진 Azure Automation 자격 증명 자산은 검색되고 구성에 전달됩니다. 아래 예제에서는이 시나리오를 수행 하는 방법을 보여 줍니다.

자격 증명을 노드 구성(MOF 구성 문서)에서 안전하게 유지하려면 노드 구성 MOF 파일에 자격 증명을 암호화해야 합니다. 현재 노드 구성 MOF 생성 중에 일반 텍스트로 자격 증명을 출력 하려면 PowerShell DSC 권한을 제공 해야 합니다. PowerShell DSC는 컴파일 작업을 통해 생성 된 후 전체 MOF 파일을 암호화 하는 Azure Automation을 인식 하지 못합니다.

구성 데이터를 사용 하 여 생성 된 노드 구성 Mof에서 일반 텍스트로 자격 증명이 출력 되도록 PowerShell DSC에 지시할 수 있습니다. DSC 구성에 표시 되 고 자격 증명을 사용 하는 각 노드 블록 이름에 대해 **ConfigurationData** 를 통해 `PSDscAllowPlainTextPassword = $true`를 전달 해야 합니다.

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

PowerShell로 이전 DSC 구성을 컴파일할 수 있습니다. 다음 PowerShell 코드는 Azure Automation 상태 구성 끌어오기 서버: CredentialSample. MyVM1 및 MyVM2에 두 개의 노드 구성을 추가 합니다.

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
> 컴파일이 완료 되 면 ' Microsoft. powershell. 관리 ' 스냅인을 이미 가져왔으므로 "' Microsoft. PowerShell. 관리 ' 모듈을 가져오지 못했습니다." 라는 오류 메시지가 표시 될 수 있습니다. 이 메시지는 무시 해도 됩니다.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Windows PowerShell에서 DSC 구성 컴파일

Azure 외부에서 컴파일한 노드 구성(MOF)을 가져올 수도 있습니다. 가져오기에는 개발자 워크스테이션 또는 [Azure DevOps](https://dev.azure.com)와 같은 서비스에서 컴파일이 포함 됩니다. 이 방법은 성능 및 안정성을 비롯 한 여러 가지 이점을 제공 합니다.

Windows PowerShell에서 컴파일하면 구성 콘텐츠를 서명 하는 옵션도 제공 됩니다. DSC 에이전트는 관리 되는 노드에서 로컬로 서명 된 노드 구성을 확인 합니다. 확인은 노드에 적용 된 구성이 권한 있는 원본에서 제공 되는지 확인 합니다.

> [!NOTE]
> 노드 구성 파일은 Azure Automation으로 가져오기 위해 1MB보다 크지 않아야 합니다.

노드 구성의 서명에 대 한 자세한 내용은 [WMF 5.1의 향상 된 기능-구성 및 모듈에 서명 하는 방법](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)을 참조 하세요.

### <a name="compile-the-dsc-configuration"></a>DSC 구성 컴파일

Windows PowerShell에서 DSC 구성을 컴파일하는 프로세스는 PowerShell DSC 설명서에서 [구성 작성, 컴파일 및 적용](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)에 포함 되어 있습니다.
개발자 워크스테이션 또는 [Azure DevOps](https://dev.azure.com)와 같은 빌드 서비스 내에서이 프로세스를 실행할 수 있습니다. 그런 다음 구성을 컴파일하여 생성 된 MOF 파일을 Azure State 구성 서비스로 가져올 수 있습니다.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Azure Portal에서 노드 구성 가져오기

1. Automation 계정 페이지의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. 상태 구성 (DSC) 페이지에서 **구성** 탭을 클릭 한 다음 **+ 추가**를 클릭 합니다.
1. 가져오기 페이지에서 **노드 구성 파일** 텍스트 상자 옆의 폴더 아이콘을 클릭 하 여 로컬 컴퓨터에서 노드 구성 파일 (MOF)을 찾습니다.

   ![로컬 파일 찾기](./media/automation-dsc-compile/import-browse.png)

1. **구성 이름** 필드에 이름을 입력 합니다. 이 이름은 노드 구성이 컴파일된 구성 이름과 일치해야 합니다.
1. **확인**을 클릭합니다.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Azure PowerShell를 사용 하 여 노드 구성 가져오기

[AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) cmdlet을 사용 하 여 노드 구성을 Automation 계정으로 가져올 수 있습니다.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>다음 단계

- 시작 하려면 [Azure Automation 상태 구성 시작](automation-dsc-getting-started.md)을 참조 하세요.
- 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조 하세요.
- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/az.automation)을 참조 하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조 하세요.
- 연속 배포 파이프라인에서 Azure Automation 상태 구성을 사용 하는 예제를 보려면 [Azure Automation State configuration 및 Chocolatey를 사용 하 여 가상 머신에 연속 배포](automation-dsc-cd-chocolatey.md)를 참조 하세요.

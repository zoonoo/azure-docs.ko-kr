---
title: Azure Automation에서 모듈 관리
description: 이 문서에서는 Azure Automation 모듈을 관리 하는 방법을 설명 합니다.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 995c87ca6f091e9ccf0b82af831bbf43ff17846f
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100841"
---
# <a name="manage-modules-in-azure-automation"></a>Azure Automation에서 모듈 관리

Azure Automation를 사용 하면 PowerShell 모듈을 가져와서 DSC 구성의 runbook 및 DSC 리소스에서 cmdlet을 사용 하도록 설정할 수 있습니다. Azure Automation에 사용 되는 모듈은 다음과 같습니다.

* [Azure PowerShell Az. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Windows `Orchestrator.AssetManagement.Cmdlets` 용 Log Analytics 에이전트에 대 한 내부 모듈
* [Azureautomation기관 Ingtoolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* 기타 PowerShell 모듈
* 사용자가 만든 사용자 지정 모듈 

Automation 계정을 만들 때 Azure Automation는 기본적으로 일부 모듈을 가져옵니다. [기본 모듈](#default-modules)을 참조 하세요.

Azure Automation runbook 및 DSC 컴파일 작업을 실행할 때 runbook을 실행할 수 있고 DSC 구성을 컴파일할 수 있는 샌드박스에 모듈을 로드 합니다. 또한 자동화는 dsc 풀 서버에 있는 모듈의 모든 DSC 리소스를 자동으로 배치 합니다. 컴퓨터는 DSC 구성을 적용할 때 리소스를 끌어올 수 있습니다.

>[!NOTE]
>Runbook 및 DSC 구성에 실제로 필요한 모듈만 가져와야 합니다. 루트 Az module은 필요 하지 않을 수 있는 다양 한 모듈을 포함 하 고 있으므로 성능 문제를 일으킬 수 있으므로 권장 하지 않습니다. 대신 Az. Compute 등의 개별 모듈을 가져옵니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](../automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="default-modules"></a>기본 모듈

다음 표에서는 Automation 계정을 만들 때 기본적으로 가져오기를 Azure Automation 하는 모듈을 나열 합니다. Automation은 이러한 모듈의 최신 버전을 가져올 수 있습니다. 그러나 새 버전을 삭제 하는 경우에도 Automation 계정에서 원래 버전을 제거할 수 없습니다. 이러한 기본 모듈에는 여러 AzureRM 모듈이 포함 되어 있습니다. 

Az. Automation 모듈이 runbook 및 DSC 구성에서 선호 됩니다. 그러나 Azure Automation는 루트 Az module을 신규 또는 기존 Automation 계정으로 자동으로 가져오지 않습니다. 이러한 모듈을 사용 하는 방법에 대 한 자세한 내용은 [Az modules로 마이그레이션](#migrating-to-az-modules)을 참조 하세요.

> [!NOTE]
> [Azure Automation에서 작업 시간 외 VM 시작/중지 솔루션](../automation-solution-vm-management.md)을 포함 하는 Automation 계정에서 모듈 및 runbook을 변경 하지 않는 것이 좋습니다.

|모듈 이름|버전|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator. Cmdlet | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="internal-cmdlets"></a>내부 cmdlet

다음 표에서는 `Orchestrator.AssetManagement.Cmdlets` 모듈에서 지 원하는 내부 cmdlet을 정의 합니다. Runbook 및 DSC 구성에서 이러한 cmdlet을 사용 하 여 Automation 계정 내에서 Azure 자산과 상호 작용할 수 있습니다. Cmdlet은 Azure PowerShell cmdlet 대신 사용 하 여 암호화 된 변수, 자격 증명 및 암호화 된 연결에서 비밀을 검색 하도록 설계 되었습니다. 

>[!NOTE]
>내부 cmdlet은 Azure 샌드박스 환경 또는 Windows Hybrid Runbook Worker에서 runbook을 실행 하는 경우에만 사용할 수 있습니다. 

|속성|Description|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|시작-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|대기-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

내부 cmdlet은 Az 및 AzureRM cmdlet에서 이름을 다르게 지정 합니다. 내부 cmdlet 이름에는 명사에 "Azure" 또는 "Az"와 같은 단어가 포함 되지 않지만 "Automation" 이라는 단어를 사용 합니다. Azure sandbox 또는 Windows hybrid worker에서 runbook을 실행 하는 동안 Az 또는 AzureRM cmdlet을 사용 하는 것이 좋습니다. 이러한 매개 변수는 매개 변수를 덜 필요로 하며 이미 실행 중인 작업의 컨텍스트에서 실행 됩니다.

Runbook의 컨텍스트 외부에서 Azure Automation 리소스를 조작 하는 데 Az 또는 AzureRM cmdlet을 사용 합니다. 이러한 경우에는 실행 계정을 사용 하 여 Azure에 인증 하는 경우 처럼 cmdlet을 사용 하는 경우 Azure에 암시적으로 연결 해야 합니다. 

## <a name="modules-supporting-get-automationpscredential"></a>Get AutomationPSCredential을 지 원하는 모듈

Azure Automation 작성 도구 키트를 사용 하 여 로컬 개발 `Get-AutomationPSCredential` 을 위해 Cmdlet은 [Azureautomation기관 ingtoolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)어셈블리의 일부입니다. Automation 컨텍스트를 사용 하는 Azure의 경우 cmdlet은에 `Orchestrator.AssetManagement.Cmdlets`있습니다. Azure Automation 자격 증명 사용에 대 한 자세한 내용은 [Azure Automation의 자격 증명 자산](credentials.md)을 참조 하세요.

는 `Get-AutomationPsCredential` 자격 증명을 `PSCredential` 사용 하는 대부분의 PowerShell cmdlet에 필요한 개체를 반환 합니다. [AzAutomationCredential](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationcredential?view=azps-3.8.0) cmdlet 대신이 cmdlet을 사용 해야 하는 경우가 많습니다. `Get-AzAutomationCredential`자격 증명에 대 한 메타 데이터를 포함 하는 [Credentialinfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) 개체를 검색 합니다. 이 정보는 일반적으로 다른 cmdlet에 전달 하는 데 유용 하지 않습니다.

## <a name="migrating-to-az-modules"></a>Az 모듈로 마이그레이션

Azure Automation에서 Az 모듈을 사용할 때 고려해 야 할 몇 가지 사항이 있습니다.

* 문제를 발생 시킬 수 있으므로 동일한 Automation 계정에서 AzureRM 모듈 및 Az 모듈을 실행 하지 않는 것이 좋습니다. [AzureRM에서 Az로 Azure PowerShell 마이그레이션을](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0)참조 하세요. 

* Az modules을 가져오기 전에 별도 Automation 계정에서 모든 runbook 및 DSC 구성을 신중 하 게 테스트 해야 합니다. 

* Az module을 Automation 계정으로 가져오면 runbook에서 사용 하는 PowerShell 세션에 모듈을 자동으로 가져오지 않습니다. 다음과 같은 상황에서 모듈을 PowerShell 세션으로 가져올 수 있습니다.

    * Runbook이 모듈에서 cmdlet을 호출 하는 경우
    * Runbook이 import-module cmdlet을 사용 하 여 모듈을 명시적 [으로 가져오는 경우](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7)
    * Runbook이 다른 종속 모듈을 가져오는 경우

모듈의 마이그레이션을 완료 한 후 Automation 계정에서 AzureRM 모듈을 사용 하 여 runbook을 시작 해 보세요. 또한 계정에서 AzureRM 모듈을 가져오거나 업데이트 하지 않는 것이 좋습니다. Az. Automation으로 마이그레이션한 계정을 고려 하 고 Az modules 에서만 작동 합니다.

>[!IMPORTANT]
>새 Automation 계정을 만들 때 Azure Automation는 기본적으로 AzureRM 모듈을 설치 합니다. AzureRM cmdlet을 사용 하 여 자습서 runbook을 계속 업데이트할 수 있습니다. 그러나 이러한 runbook은 실행 해서는 안 됩니다.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>AzureRM 모듈을 사용 하는 모든 runbook 중지 및 예약 해제

AzureRM 모듈을 사용 하는 기존 runbook을 실행 하지 않도록 하려면 AzureRmAutomationSchedule cmdlet을 사용 하 여 영향을 받는 모든 runbook을 중지 하 고 예약 [취소](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) 합니다. 필요에 따라 runbook에 대해 나중에 일정을 다시 예약할 수 있도록 각 일정을 개별적으로 검토 하는 것이 중요 합니다.

```powershell
Get-AzureRmAutomationSchedule -AutomationAccountName "Contoso17" -Name "DailySchedule08" -ResourceGroupName "ResourceGroup01" 
Remove-AzureRmAutomationSchedule -AutomationAccountName "Contoso17" -Name "DailySchedule08" -ResourceGroupName "ResourceGroup01"
```

### <a name="import-the-az-modules"></a>Az 모듈 가져오기

이 섹션에서는 Azure Portal에서 Az 모듈을 가져오는 방법을 설명 합니다. 전체 Az. Automation 모듈이 아니라 필요한 Az 모듈만 가져와야 합니다. [Az. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) 는 다른 Az 모듈의 종속성 이므로 다른 모듈 앞에이 모듈을 가져와야 합니다.

1. Automation 계정의 **공유 리소스**아래에서 **모듈** 을 선택 합니다. 
2. **갤러리 찾아보기**를 클릭하여 갤러리 찾아보기 페이지를 엽니다.  
3. 검색 창에서 모듈 이름 (예:)을 입력 합니다 `Az.Accounts`. 
4. PowerShell 모듈 페이지에서 **가져오기** 를 클릭 하 여 모듈을 Automation 계정으로 가져옵니다.

    ![Automation 계정으로 모듈 가져오기](../media/modules/import-module.png)

가져올 모듈을 검색 하 여 [PowerShell 갤러리](https://www.powershellgallery.com) 를 통해이 가져오기 프로세스를 수행할 수도 있습니다. 모듈을 찾은 후 선택 하 고 **Azure Automation** 탭을 선택한 다음 **배포를 클릭 하 여 Azure Automation**합니다.

![갤러리에서 직접 모듈 가져오기](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Runbook 테스트

Az 모듈을 Automation 계정으로 가져온 후에는 새 모듈을 사용 하도록 runbook 편집을 시작할 수 있습니다. AzureRm 접두사가 Az로 변경 된 경우를 제외 하 고 대부분의 cmdlet은 AzureRM 모듈의 이름과 동일 합니다. 이 명명 규칙을 따르지 않는 모듈 목록은 [예외 목록](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)을 참조 하십시오.

Runbook을 사용 하 여 runbook을 수정 하는 한 가지 방법은 runbook의 시작 부분 `Enable-AzureRmAlias -Scope Process` 에서를 사용 하는 것입니다. Runbook에이 명령을 추가 하 여 스크립트를 변경 없이 실행할 수 있습니다. 

## <a name="authoring-modules"></a>제작 모듈

Azure Automation에서 사용할 PowerShell 모듈을 제작할 때이 섹션의 고려 사항을 따르는 것이 좋습니다.

### <a name="references-to-azurerm-and-az"></a>AzureRM 및 Az에 대 한 참조

모듈에서 Az 모듈을 참조 하는 경우 AzureRM 모듈도 참조 하지 않도록 합니다. 두 모듈 집합을 동시에 사용할 수는 없습니다. 

### <a name="version-folder"></a>버전 폴더

모듈에 대 한 **.zip** 패키지에 버전 폴더를 포함 하지 마십시오.  이 문제는 runbook에 대 한 문제는 적고, 상태 구성 (DSC) 서비스에 문제가 발생 합니다. Azure Automation는 모듈이 DSC에서 관리 하는 노드에 배포 될 때 자동으로 버전 폴더를 만듭니다. 버전 폴더가 있는 경우 두 개의 인스턴스가 표시 됩니다. DSC 모듈의 예제 폴더 구조는 다음과 같습니다.

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>도움말 정보

모듈의 모든 cmdlet에 대 한 개요, description 및 help URI를 포함 합니다. PowerShell에서 `Get-Help` cmdlet을 사용 하 여 cmdlet에 대 한 도움말 정보를 정의할 수 있습니다. 다음 예제에서는 개요 및 help URI를 **.psm1** 모듈 파일에 정의 하는 방법을 보여 줍니다.

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  이 정보를 제공 하면 PowerShell 콘솔에서 `Get-Help` cmdlet을 통해 도움말 텍스트를 볼 수 있습니다. 이 텍스트는 Azure Portal에도 표시 됩니다.

  ![통합 모듈 도움말](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>연결 형식

모듈이 외부 서비스에 연결 하는 경우 [연결 유형을](#adding-a-connection-type-to-your-module)정의 합니다. 모듈의 각 cmdlet은 연결 개체 (해당 연결 형식의 인스턴스)를 매개 변수로 허용 해야 합니다. 사용자는 cmdlet을 호출할 때마다 cmdlet의 해당 매개 변수에 연결 자산의 매개 변수를 매핑합니다. 이전 섹션의 예제를 기반으로 하는 다음 runbook 예제에서는 contoso 연결 자산을 사용 하 여 `ContosoConnection` contoso 리소스에 액세스 하 고 외부 서비스에서 데이터를 반환 합니다. 이 예제에서 필드는 `UserName` `Password` `PSCredential` 개체의 및 속성에 매핑되고 cmdlet에 전달 됩니다.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  이 동작을 수행 하는 더 쉽고 나은 방법은 cmdlet에 연결 개체를 직접 전달 하는 것입니다.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  매개 변수에 대 한 연결 필드 대신 연결 개체를 매개 변수로 직접 허용할 수 있도록 허용 하 여 cmdlet에 대해 비슷한 동작을 사용 하도록 설정할 수 있습니다. 일반적으로 각각에 대해 매개 변수를 설정하여 Azure Automation을 사용하는 사용자가 hashtable을 생성하지 않고 cmdlet를 호출하여 연결 개체의 역할을 할 수 있도록 합니다. 매개 변수 집합 `UserAccount` 은 연결 필드 속성을 전달 하는 데 사용 됩니다. `ConnectionObject`를 통해 연결을 직접 전달할 수 있습니다.

### <a name="output-type"></a>출력 형식

모듈의 모든 cmdlet에 대 한 출력 형식을 정의 합니다. Cmdlet에 대 한 출력 형식을 정의 하면 디자인 타임 IntelliSense에서 제작 하는 동안 cmdlet의 출력 속성을 확인할 수 있습니다. 이 방법은 그래픽 runbook을 작성 하는 동안 특히 유용 합니다 .이는 디자인 타임 지식이 모듈을 사용 하는 쉬운 사용자 환경에 중요 합니다.

Where `[OutputType([<MyOutputType>])]` `MyOutputType` 가 유효한 유형인를 추가 합니다. 에 `OutputType`대해 자세히 알아보려면 [함수 정보 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)를 참조 하세요. 다음 코드는 cmdlet에를 추가 `OutputType` 하는 예입니다.

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![그래픽 Runbook 출력 형식](../media/modules/runbook-graphical-module-output-type.png)

  이 동작은 PowerShell ISE에서 cmdlet의 출력을 실행하지 않는 "사전 입력" 기능과 비슷합니다.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet 상태

모듈의 모든 cmdlet을 상태 비저장으로 설정 합니다. 여러 runbook 작업은 동일한 `AppDomain` 프로세스와 샌드박스에서 동시에 실행할 수 있습니다. 이러한 수준에서 공유 된 상태가 있으면 작업은 서로 영향을 줄 수 있습니다. 이 동작으로 인해 간헐적이 고 진단이 어려운 문제가 발생할 수 있습니다. 다음은 수행할 수 없는 작업의 예입니다.

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>모듈 종속성

모듈이 xcopy를 사용 하 여 복사할 수 있는 패키지에 완전히 포함 되어 있는지 확인 합니다. Runbook이 실행 될 때 Azure Automation 모듈이 Automation 샌드박스에 배포 됩니다. 모듈은 실행 하는 호스트와 독립적으로 작동 해야 합니다. 

모듈 패키지를 압축 하 고 이동 하 여 다른 호스트의 PowerShell 환경으로 가져올 때 정상적으로 작동 하도록 할 수 있습니다. 이를 위해서는 모듈이 Azure Automation로 가져올 때 압축 되는 모듈 폴더 외부의 파일에 종속 되지 않도록 해야 합니다. 

모듈은 호스트의 고유한 레지스트리 설정에 의존해 서는 안 됩니다. 예는 제품이 설치 될 때 적용 되는 설정입니다. 

### <a name="module-file-paths"></a>모듈 파일 경로

모듈의 모든 파일에 140 자 미만의 경로가 있는지 확인 합니다. 140 자를 초과 하는 경로는 runbook을 가져오는 동안 문제가 발생 합니다. Azure Automation 경로 크기가 140 자인 파일을 PowerShell 세션에서로 `Import-Module`가져올 수 없습니다.

## <a name="importing-modules"></a>모듈 가져오기

이 섹션에서는 Automation 계정으로 모듈을 가져올 수 있는 몇 가지 방법을 정의 합니다. 

### <a name="import-modules-in-azure-portal"></a>Azure Portal에서 모듈 가져오기

Azure Portal에서 모듈을 가져오려면 다음을 수행 합니다.

1. Automation 계정으로 이동합니다.
2. **공유 리소스**아래에서 **모듈** 을 선택 합니다.
3. **모듈 추가를**클릭 합니다. 
4. 모듈을 포함 하는 **.zip** 파일을 선택 합니다.
5. **확인** 을 클릭 하 여 가져오기 프로세스를 시작 합니다.

### <a name="import-modules-using-powershell"></a>PowerShell을 사용 하 여 모듈 가져오기

[AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) cmdlet을 사용 하 여 모듈을 Automation 계정으로 가져올 수 있습니다. Cmdlet은 모듈 .zip 패키지에 대 한 URL을 사용 합니다.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

동일한 cmdlet을 사용 하 여 PowerShell 갤러리에서 모듈을 직접 가져올 수도 있습니다. PowerShell 갤러리를 잡아 잡아 `ModuleName` `ModuleVersion` 를 확인 [PowerShell Gallery](https://www.powershellgallery.com)합니다.

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>PowerShell 갤러리에서 모듈 가져오기

갤러리에서 직접 또는 Automation 계정에서 [PowerShell 갤러리](https://www.powershellgallery.com) 모듈을 가져올 수 있습니다.

PowerShell 갤러리에서 직접 모듈을 가져오려면 다음을 수행 합니다.

1. 로 https://www.powershellgallery.com 이동 하 여 가져올 모듈을 검색 합니다.
2. **배포를 클릭 하** 여 **설치 옵션**아래의 **Azure Automation** 탭에서 Azure Automation 합니다. 이 작업을 수행 하면 Azure Portal 열립니다. 
3. 가져오기 페이지에서 Automation 계정을 선택 하 고 **확인**을 클릭 합니다.

![가져오기 모듈 PowerShell 갤러리](../media/modules/powershell-gallery.png)

Automation 계정에서 직접 PowerShell 갤러리 모듈을 가져오려면 다음을 수행 합니다.

1. **공유 리소스**아래에서 **모듈** 을 선택 합니다. 
2. 모듈 페이지에서 **갤러리 찾아보기**를 클릭 한 다음 모듈의 갤러리를 검색 합니다. 
3. 가져올 모듈을 선택 하 고 **가져오기**를 클릭 합니다. 
4. 가져오기 페이지에서 **확인** 을 클릭 하 여 가져오기 프로세스를 시작 합니다.

![Azure Portal에서 가져오기 PowerShell 갤러리](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>모듈 삭제

모듈에 문제가 있거나 이전 버전의 모듈로 롤백해야 하는 경우 Automation 계정에서 삭제할 수 있습니다. Automation 계정을 만들 때 가져온 [기본 모듈](#default-modules) 의 원래 버전은 삭제할 수 없습니다. 삭제할 모듈이 [기본 모듈](#default-modules)중 하나의 최신 버전인 경우 Automation 계정과 함께 설치 된 버전으로 롤백됩니다. 그렇지 않으면 Automation 계정에서 삭제 한 모든 모듈이 제거 됩니다.

### <a name="delete-modules-in-azure-portal"></a>Azure Portal에서 모듈 삭제

Azure Portal에서 모듈을 제거 하려면 다음을 수행 합니다.

1. Automation 계정으로 이동 하 고 **공유 리소스**아래에서 **모듈** 을 선택 합니다. 
2. 제거 하려는 모듈을 선택 합니다. 
3. **모듈** 페이지에서 **삭제**를 선택 합니다. 이 모듈이 [기본 모듈](#default-modules)중 하나 이면 Automation 계정을 만들 때 있던 버전으로 롤백됩니다.

### <a name="delete-modules-using-powershell"></a>PowerShell을 사용 하 여 모듈 삭제

PowerShell을 통해 모듈을 제거 하려면 다음 명령을 실행 합니다.

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>모듈에 연결 형식 추가

모듈에 선택적 메타 데이터 파일을 추가 하 여 Automation 계정에서 사용할 사용자 지정 [연결 형식을](../automation-connections.md) 제공할 수 있습니다. 이 파일은 Automation 계정에서 모듈의 cmdlet과 함께 사용할 Azure Automation 연결 형식을 지정 합니다. PowerShell 모듈을 작성 하는 방법에 대 한 자세한 내용은 [Powershell 스크립트 모듈을 작성 하는 방법](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)을 참조 하세요.

![Azure Portal에서 사용자 지정 연결 사용](../media/modules/connection-create-new.png)

연결 유형 속성을 지정 하는 파일의 이름은 ** &lt;ModuleName&gt;-Automation. json** 이며 압축 된 **.zip** 파일의 module 폴더에 있습니다. 이 파일은 모듈이 나타내는 시스템 또는 서비스에 연결 하는 데 필요한 연결의 필드를 포함 합니다. 구성에서는 Azure Automation 연결 유형을 만들 수 있습니다. 이 파일을 사용 하 여 필드 이름, 형식 및 모듈의 연결 유형에 대 한 필드의 암호화 여부를 설정할 수 있습니다. 다음 예제는 사용자 이름 및 암호 속성을 정의 하는 **json** 파일 형식의 템플릿입니다.

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="next-steps"></a>다음 단계

* Azure PowerShell 모듈을 사용 하는 방법에 대 한 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)을 참조 하세요.
* PowerShell 모듈을 만드는 방법에 대해 자세히 알아보려면 [Windows Powershell 모듈 작성](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7)을 참조 하세요.

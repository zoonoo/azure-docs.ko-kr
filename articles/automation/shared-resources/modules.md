---
title: Azure 자동화에서 모듈 관리
description: 이 문서에서는 Azure 자동화에서 모듈을 관리하는 방법에 대해 설명합니다.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278338"
---
# <a name="manage-modules-in-azure-automation"></a>Azure 자동화에서 모듈 관리

Azure 자동화는 PowerShell 기반 Runbook에서 사용할 수 있도록 자동화 계정으로 PowerShell 모듈을 가져오는 기능을 제공합니다. 이러한 모듈은 PowerShell 갤러리또는 Azure용 AzureRM 및 Az 모듈에서 만든 사용자 지정 모듈일 수 있습니다. 자동화 계정을 만들 때 일부 모듈은 기본적으로 가져옵니다.

## <a name="import-modules"></a>모듈 가져오기

자동화 계정으로 모듈을 가져올 수 있는 방법에는 여러 가지가 있습니다. 다음 섹션에서는 모듈을 가져오는 다양한 방법을 보여 준다.

> [!NOTE]
> Azure 자동화에서 사용할 모듈의 파일의 최대 경로는 140자입니다. 140자를 넘는 모든 경로는 `Import-Module`을 사용하여 PowerShell 세션으로 가져올 수 없습니다.

### <a name="powershell"></a>PowerShell

[New-AzureRmAutomationModule을](/powershell/module/azurerm.automation/new-azurermautomationmodule) 사용하여 자동화 계정으로 모듈을 가져올 수 있습니다. cmdlet모듈 zip 패키지로 URL을 받습니다.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

동일한 cmdlet을 사용하여 PowerShell 갤러리에서 직접 모듈을 가져올 수도 있습니다. [PowerShell 갤러리에서](https://www.powershellgallery.com) **모듈 이름** 및 **모듈버전을** 잡아야 합니다.

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure portal

Azure 포털에서 자동화 계정으로 이동하여 공유 **리소스**에서 **모듈을 선택합니다.** **+ 모듈 추가를**클릭합니다. 모듈이 포함된 **.zip** 파일을 선택하고 **확인을** 클릭하여 프로세스 가져오기를 시작합니다.

### <a name="powershell-gallery"></a>PowerShell 갤러리

PowerShell 갤러리의 모듈은 [PowerShell 갤러리에서](https://www.powershellgallery.com) 직접 가져오거나 자동화 계정에서 가져올 수 있습니다.

PowerShell 갤러리에서 모듈을 가져오려면 https://www.powershellgallery.com 가져오려는 모듈을 검색하십시오. 설치 옵션 아래의 **Azure 자동화** 탭에서 Azure **자동화에 대한 배포를** **클릭합니다.** 이 작업을 수행하면 Azure Portal이 열립니다. **가져오기** 페이지에서 자동화 계정을 선택하고 **확인을**클릭합니다.

![파워쉘 갤러리 가져오기 모듈](../media/modules/powershell-gallery.png)

자동화 계정에서 직접 PowerShell 갤러리에서 모듈을 가져올 수도 있습니다. 자동화 계정에서 **공유 리소스**아래에서 **모듈을** 선택합니다. 모듈 페이지에서 갤러리 **찾아보기를**클릭한 다음 PowerShell 갤러리에서 모듈을 검색합니다. 가져올 모듈을 선택하고 **가져오기**를 클릭합니다. **가져오기** 페이지에서 **확인을** 클릭하여 가져오기 프로세스를 시작합니다.

![Azure 포털에서 PowerShell 갤러리 가져오기](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>모듈 삭제

모듈에 문제가 있거나 이전 버전의 모듈로 롤백해야 하는 경우 자동화 계정에서 모듈을 삭제할 수 있습니다. 자동화 계정을 만들 때 가져온 [기본 모듈의](#default-modules) 원래 버전은 삭제할 수 없습니다. 삭제하려는 모듈이 설치된 [기본 모듈](#default-modules) 중 하나의 최신 버전인 경우 자동화 계정과 함께 설치된 버전으로 롤백됩니다. 그렇지 않으면 자동화 계정에서 삭제한 모든 모듈이 제거됩니다.

### <a name="azure-portal"></a>Azure portal

Azure 포털에서 자동화 계정으로 이동하여 공유 **리소스**에서 **모듈을 선택합니다.** 제거할 모듈을 선택합니다. **모듈** 페이지에서 **삭제를**선택합니다. 이 모듈이 기본 [모듈](#default-modules)중 하나인 경우 자동화 계정을 만들 때 있던 버전으로 롤백됩니다.

### <a name="powershell"></a>PowerShell

PowerShell을 통해 모듈을 제거하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>내부 cmdlet

다음은 모든 자동화 계정으로 가져온 내부 `Orchestrator.AssetManagement.Cmdlets` 모듈의 cmdlet 목록입니다. 이러한 cmdlet은 Runbook 및 DSC 구성에서 액세스할 수 있으며 자동화 계정 내의 자산과 상호 작용할 수 있습니다. 또한 내부 cmdlet을 사용하면 암호화된 **변수** 값, 자격 증명 및 암호화된 **연결** 필드에서 비밀을 검색할 수 **있습니다.** Azure PowerShell cmdlet은 이러한 비밀을 검색할 수 없습니다. 이러한 cmdlet은 Azure에 인증하기 위해 계정으로 실행을 사용하는 것과 같이 Azure를 사용할 때 암시적으로 Azure에 연결할 필요가 없습니다.

>[!NOTE]
>이러한 내부 cmdlet은 Windows 하이브리드 Runbook 작업자에서 사용할 수 있으며 Linux 하이브리드 Runbook 작업자에서는 사용할 수 없습니다. 컴퓨터에서 직접 실행되는 런북이나 사용자 환경의 리소스에 대해 해당 [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) 또는 [Az 모듈을](../az-modules.md) 사용합니다. 
>

|이름|설명|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|시작 자동화Runbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|대기 자동화 작업|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>모듈에 연결 유형 추가

모듈에 선택적 파일을 추가하여 자동화 계정에서 사용할 수 있는 사용자 지정 [연결 유형을](../automation-connections.md) 제공할 수 있습니다. 이 파일은 자동화 계정에서 모듈의 cmdlet과 함께 사용할 Azure 자동화 연결 유형을 지정하는 메타데이터 파일입니다. 이를 위해서는 먼저 PowerShell 모듈을 작성하는 방법을 알고 있어야 합니다. 모듈 작성에 대한 자세한 내용은 [PowerShell 스크립트 모듈 을 작성하는 방법을](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)참조하십시오.

![Azure 포털에서 사용자 지정 연결 사용](../media/modules/connection-create-new.png)

Azure 자동화 연결 유형을 추가하려면 모듈에 연결 유형 `<ModuleName>-Automation.json` 속성을 지정하는 이름이 있는 파일이 포함되어야 합니다. json 파일은 압축된 .zip 파일의 모듈 폴더 내에 배치됩니다. 이 파일에는 모듈이 나타내는 시스템 또는 서비스에 연결하는 데 필요한 연결 필드를 포함합니다. 구성은 Azure 자동화에서 연결 유형을 만듭니다. 이 파일을 사용하면 모듈의 연결 유형에 대해 필드 이름, 형식 및 필드를 암호화할지 선택사항인지 여부를 설정할 수 있습니다. 다음 예제는 사용자 이름 및 암호 속성을 정의하는 json 파일 형식의 템플릿입니다.

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

## <a name="module-best-practices"></a>모듈 모범 사례

PowerShell 모듈은 Azure Automation으로 가져와서 DSC 구성 내에서 사용할 수 있는 runbook 및 DSC 리소스 내에서 해당 cmdlet을 사용할 수 있도록 합니다. 배후에서 Azure Automation는 이러한 모듈을 저장하고 runbook 작업 및 DSC 컴파일 작업 실행 시 runbook을 실행하고 DSC 구성을 컴파일하는 Azure Automation 샌드박스에 로드합니다. 모듈의 모든 DSC 리소스 또한 Automation DSC 끌어오기 서버에 자동으로 배치됩니다. 이러한 리소스는 DSC 구성을 적용할 때 컴퓨터에서 끌어올 수 있습니다.

Azure 자동화에서 사용할 PowerShell 모듈을 작성할 때 다음을 고려하는 것이 좋습니다.

* .zip 패키지에 버전 폴더를 포함하지 마십시오.  이 문제는 Runbook에 대한 우려는 적지만 상태 구성 서비스에 문제가 발생합니다.  Azure Automation은 모듈이 DSC에서 관리하는 노드에 배포될 때 버전 폴더를 자동으로 만들고 버전 폴더가 있으면 두 개의 인스턴스로 끝납니다.  DSC 모듈의 폴더 구조 예:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* 모듈에서 모든 cmdlet에 대한 개요, 설명 및 도움말 URI를 포함합니다. PowerShell에서는 cmdlet에 대한 도움말 정보를 정의하여 사용자가 **Get-Help** cmdlet과 해당 정보를 통해 도움을 받을 수 있습니다. 다음 예제에서는 .psm1 모듈 파일에서 개요를 정의하고 URI를 돕는 방법을 보여 줍니다.

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

  이 정보를 제공하면 PowerShell 콘솔에서 **Get-Help** cmdlet을 사용할 때 이 도움말이 표시됩니다. 이 설명은 Azure 포털에도 표시됩니다.

  ![통합 모듈 도움말](../media/modules/module-activity-description.png)

* 모듈이 외부 서비스에 연결되면 [연결 유형이](#add-a-connection-type-to-your-module)포함되어야 합니다. 모듈의 각 cmdlet은 매개 변수로 연결 개체(해당 연결 형식의 인스턴스)를 포함해야 합니다. 사용자는 연결 자산의 매개 변수를 cmdlet을 호출할 때마다 cmdlet의 해당 매개 변수에 매핑합니다. 위의 Runbook 예제에 따라 ContosoConnection이라는 Contoso 연결 자산을 사용하여 Contoso 리소스에 액세스하고 외부 서비스에서 데이터를 반환합니다.

  다음 예제에서는 필드가 `PSCredential` 개체의 UserName 및 Password 속성에 매핑된 다음 cmdlet에 전달됩니다.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  이 동작에 접근하는 더 쉽고 더 좋은 방법은 연결 개체를 cmdlet으로 직접 전달하는 것입니다.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  매개 변수에 대한 연결 필드 대신에 매개 변수인 연결 개체를 직접 수용할 수 있도록 하여 cmdlet에 이전 예제와 같은 동작을 설정할 수 있습니다. 일반적으로 각각에 대해 매개 변수를 설정하여 Azure Automation을 사용하는 사용자가 hashtable을 생성하지 않고 cmdlet를 호출하여 연결 개체의 역할을 할 수 있도록 합니다. 매개 변수 `UserAccount`집합은 연결 필드 속성을 전달하는 데 사용됩니다. `ConnectionObject`연결을 바로 통과할 수 있습니다.

* 모듈의 모든 cmdlet에 대한 출력 유형을 정의합니다. cmdlet에 대한 출력 형식을 정의하면 디자인 타임 IntelliSense에서 작성 중에 사용하기 위한 cmdlet의 출력 속성을 확인할 수 있습니다. Automation runbook 그래픽을 작성하는 동안 특히 유용하며 이 경우 디자인 타임 지식은 모듈을 사용하는 쉬운 사용자 환경의 키입니다.

MyOutputTypeType이 유효한 형식인 위치를 추가합니다. `[OutputType([<MyOutputType>])]` OutputType에 대한 자세한 내용은 [함수 출력TypeAttribute 에 대해 참조하세요.](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) 다음 코드는 cmdlet에 추가하는 `OutputType` 예입니다.

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

* 모듈의 모든 cmdlet을 상태 비저장으로 만듭니다. 여러 Runbook 작업은 동일한 AppDomain과 동일한 프로세스 및 샌드박스에서 동시에 실행할 수 있습니다. 이러한 수준에서 공유되는 상태가 있으면 작업이 서로 영향을 줄 수 있습니다. 이 동작은 간헐적으로 발생할 수 있으며 문제를 진단하기 어려울 수 있습니다.  다음은 수행해서는 안되는 작업의 예제입니다.

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

* 모듈은 xcopy 가능 패키지에 완전히 포함되어야 합니다. Azure 자동화 모듈은 Runbook을 실행해야 할 때 자동화 샌드박스에 배포됩니다. 모듈은 실행 중인 호스트와는 독립적으로 작동해야 합니다. 모듈 패키지를 Zip으로 압축한 후 이동하여 다른 호스트의 PowerShell 환경으로 가져왔을 때 정상적으로 작동하게 만들 수 있어야 합니다. 이를 위해 모듈은 모듈 폴더 외부의 파일에 의존하면 안 됩니다. 이 폴더는 모듈을 Azure Automation으로 가져올 때 zip으로 압축되는 폴더입니다. 모듈 또한 호스트의 고유한 레지스트리 설정(예: 제품 설치 시 지정된 설정)에 의존하면 안 됩니다. 모듈의 모든 파일에는 140자 미만의 경로가 있어야 합니다. 140자를 넘는 경로는 Runbook을 가져오는 데 문제가 발생합니다. 이 모범 사례를 따르지 않으면 모듈은 Azure Automation에서 사용할 수 없습니다.  

* 모듈에서 [ 모듈](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)을 참조하는 경우 `AzureRM`도 참조하지 않는지 확인합니다. `Az` 모듈은 `AzureRM` 모듈과 함께 사용할 수 없습니다. `Az`는 기본적으로 가져오지 않는 Runbook에서 지원됩니다. `Az` 모듈과 고려할 사항에 대해 알아보려면 [Azure Automation의 Az 모듈 지원](../az-modules.md)을 참조하세요.

## <a name="default-modules"></a>기본 모듈

다음 표에는 자동화 계정을 만들 때 기본적으로 가져오는 모듈이 나열되어 있습니다. 아래에 나열된 모듈은 최신 버전을 가져올 수 있지만 최신 버전을 삭제하더라도 원래 버전은 자동화 계정에서 제거할 수 없습니다.

|모듈 이름|버전|
|---|---|
| 감사정책Dsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GP레지스트리정책파서 | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| 오케스트레이터.자산관리.Cmdlet | 1 |
| PSDsc자원 | 2.9.0.0 |
| 보안정책Dsc | 2.1.0.0 |
| 스테이트컨피그복합리소스 | 1 |
| xDSC도메인조인 | 1.1 |
| x파워쉘실행정책 | 1.1.0.0 |
| x원격데스크톱관리자 | 1.1.0.0 |

## <a name="next-steps"></a>다음 단계

* PowerShell 모듈을 만드는 자세한 내용은 [Windows PowerShell 모듈 작성](/powershell/scripting/developer/windows-powershell)

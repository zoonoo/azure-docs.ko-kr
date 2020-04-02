---
title: Azure 자동화에서 모듈 관리
description: 이 문서에서는 Azure 자동화에서 모듈을 관리하는 방법을 설명합니다.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 859eea66d10e07a3503e33166bc77c8a97577acd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548928"
---
# <a name="manage-modules-in-azure-automation"></a>Azure 자동화에서 모듈 관리

Azure 자동화를 사용하면 PowerShell 기반 런북에서 PowerShell 모듈을 자동화 계정으로 가져올 수 있습니다. 이러한 모듈은 생성한 사용자 지정 모듈, PowerShell 갤러리의 모듈 또는 Azure용 AzureRM 및 Az 모듈일 수 있습니다. 자동화 계정을 만들 때 일부 모듈은 기본적으로 가져옵니다.

## <a name="import-modules"></a>모듈 가져오기

자동화 계정으로 모듈을 가져올 수 있는 방법에는 여러 가지가 있습니다. 다음 섹션에서는 모듈을 가져오는 다양한 방법을 보여 준다.

> [!NOTE]
> Azure 자동화에 사용되는 모듈의 파일의 최대 경로 크기는 140자입니다. 자동화는 경로 크기가 140자를 초과하는 파일을 을 사용 `Import-Module`하 여 PowerShell 세션으로 가져올 수 없습니다.

### <a name="powershell"></a>PowerShell

[New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) cmdlet을 사용하여 자동화 계정으로 모듈을 가져올 수 있습니다. cmdlet모듈 .zip 패키지의 URL을 사용합니다.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

동일한 cmdlet을 사용하여 PowerShell 갤러리에서 직접 모듈을 가져올 수도 있습니다. 파워 쉘 `ModuleName` `ModuleVersion` [갤러리에서](https://www.powershellgallery.com)잡아 있는지 확인하십시오.

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure portal

Azure 포털에서 모듈을 가져오려면 다음을 수행합니다.

1. Automation 계정으로 이동합니다.
2. **공유 리소스**에서 **모듈을 선택합니다.**
3. **모듈 추가를 클릭합니다.** 
4. 모듈이 포함된 **.zip** 파일을 선택합니다.
5. 프로세스 가져오기를 시작하려면 **확인을** 클릭합니다.

### <a name="powershell-gallery"></a>PowerShell 갤러리

갤러리에서 직접 또는 자동화 계정에서 [PowerShell 갤러리](https://www.powershellgallery.com) 모듈을 가져올 수 있습니다.

PowerShell 갤러리에서 직접 모듈을 가져오려면 다음을 수행하십시오.

1. 로 https://www.powershellgallery.com 이동하여 가져올 모듈을 검색합니다.
2. 설치 옵션 아래의 **Azure 자동화** 탭에서 Azure **자동화에 대한 배포를** **클릭합니다.** 이 작업을 수행하면 Azure Portal이 열립니다. 
3. 가져오기 페이지에서 자동화 계정을 선택하고 **확인을**클릭합니다.

![파워쉘 갤러리 가져오기 모듈](../media/modules/powershell-gallery.png)

자동화 계정에서 직접 PowerShell 갤러리 모듈을 가져오려면 다음을 수행하십시오.

1. **공유 리소스**에서 **모듈을 선택합니다.** 
2. 모듈 페이지에서 **갤러리 찾아보기를**클릭한 다음 갤러리에서 모듈을 검색합니다. 
3. 가져올 모듈을 선택하고 **가져오기**를 클릭합니다. 
4. 가져오기 페이지에서 **확인을** 클릭하여 가져오기 프로세스를 시작합니다.

![Azure 포털에서 PowerShell 갤러리 가져오기](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>모듈 삭제

모듈에 문제가 있거나 이전 버전의 모듈로 롤백해야 하는 경우 자동화 계정에서 모듈을 삭제할 수 있습니다. 자동화 계정을 만들 때 가져온 [기본 모듈의](#default-modules) 원래 버전은 삭제할 수 없습니다. 삭제할 모듈이 [기본 모듈](#default-modules)중 하나의 최신 버전인 경우 자동화 계정에 설치된 버전으로 롤백합니다. 그렇지 않으면 자동화 계정에서 삭제한 모든 모듈이 제거됩니다.

### <a name="azure-portal"></a>Azure portal

Azure 포털에서 모듈을 제거하려면 다음을 수행하십시오.

1. 자동화 계정으로 이동하여 공유 **리소스**에서 **모듈을 선택합니다.** 
2. 제거할 모듈을 선택합니다. 
3. **모듈** 페이지에서 **삭제를**선택합니다. 이 모듈이 기본 [모듈](#default-modules)중 하나인 경우 자동화 계정을 만들 때 존재했던 버전으로 롤백됩니다.

### <a name="powershell"></a>PowerShell

PowerShell을 통해 모듈을 제거하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>내부 cmdlet

아래 표는 모든 자동화 계정으로 가져온 내부 `Orchestrator.AssetManagement.Cmdlets` 모듈의 cmdlet을 나열합니다. 이러한 cmdlet은 Runbook 및 DSC 구성에서 액세스할 수 있으며 자동화 계정 내의 자산과 상호 작용할 수 있습니다. 또한 내부 cmdlet을 사용하면 암호화된 변수, 자격 증명 및 암호화된 연결에서 비밀을 검색할 수 있습니다. Azure PowerShell cmdlet은 이러한 비밀을 검색할 수 없습니다. 이러한 cmdlet은 Azure에 인증하기 위해 계정으로 실행을 사용할 때와 같이 Azure를 사용할 때 암시적으로 Azure에 연결할 필요가 없습니다.

>[!NOTE]
>이러한 내부 cmdlet은 Windows 하이브리드 Runbook 작업자에서 사용할 수 있지만 Linux 하이브리드 Runbook 작업자에서는 사용할 수 없습니다. 컴퓨터에서 직접 실행되는 런북이나 환경의 리소스에 대해 해당 [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) 또는 [Az 모듈](../az-modules.md) cmdlet을 사용합니다. 

|속성|Description|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|시작 자동화Runbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|대기 자동화 작업|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>모듈에 연결 유형 추가

모듈에 선택적 메타데이터 파일을 추가하여 자동화 계정에서 사용할 사용자 지정 [연결 유형을](../automation-connections.md) 제공할 수 있습니다. 이 파일은 자동화 계정에서 모듈의 cmdlet과 함께 사용할 Azure 자동화 연결 유형을 지정합니다. 이를 위해서는 먼저 PowerShell 모듈을 작성하는 방법을 알고 있어야 합니다. [PowerShell 스크립트 모듈을 작성하는 방법을](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)참조하십시오.

![Azure 포털에서 사용자 지정 연결 사용](../media/modules/connection-create-new.png)

연결 유형 속성을 지정하는 파일은 ** &lt;ModuleName&gt;-Automation.json이며** 압축된 **.zip** 파일의 모듈 폴더에 있습니다. 이 파일에는 모듈이 나타내는 시스템 또는 서비스에 연결하는 데 필요한 연결 필드가 포함되어 있습니다. 구성을 사용하면 Azure 자동화에서 연결 유형을 만들 수 있습니다. 이 파일을 사용하면 필드 이름, 형식 및 필드가 모듈의 연결 유형에 대해 암호화되어 있는지 또는 선택 사항인지를 설정할 수 있습니다. 다음 예제는 사용자 이름 및 암호 속성을 정의하는 **.json** 파일 형식의 템플릿입니다.

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

PowerShell 모듈을 Azure 자동화로 가져오면 Runbook 내에서 cmdlet을 사용할 수 있고 DSC 구성 내에서 해당 DSC 리소스를 사용할 수 있습니다. 이면에 Azure Automation은 이러한 모듈을 저장합니다. Runbook 작업 및 DSC 컴파일 작업 실행 시간에서 자동화는 런북이 실행되고 DSC 구성이 컴파일되는 Azure Automation 샌드박스에 로드합니다. 모듈의 모든 DSC 리소스 또한 Automation DSC 끌어오기 서버에 자동으로 배치됩니다. 기계는 DSC 구성을 적용할 때 이를 끌어올 수 있습니다.

Azure 자동화에서 사용할 PowerShell 모듈을 작성할 때 다음을 고려하는 것이 좋습니다.

* **.zip** 패키지에 버전 폴더를 포함하지 마십시오.  이 문제는 Runbook에 대한 우려는 적지만 상태 구성 서비스에 문제가 발생합니다. Azure Automation은 모듈이 DSC에서 관리하는 노드에 배포될 때 버전 폴더를 자동으로 만듭니다. 버전 폴더가 있으면 두 개의 인스턴스가 표시됩니다. 다음은 DSC 모듈의 예제 폴더 구조입니다.

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* 모듈에서 모든 cmdlet에 대한 개요, 설명 및 도움말 URI를 포함합니다. PowerShell에서 cmdlet을 사용하여 cmdlet에 `Get-Help` 대한 도움말 정보를 정의할 수 있습니다. 다음 예제에서는 **.psm1** 모듈 파일에서 개요를 정의하고 URI를 돕는 방법을 보여 줍니다.

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

  이 정보를 제공하면 PowerShell 콘솔의 `Get-Help` cmdlet을 통해 도움말 텍스트가 표시됩니다. 이 텍스트는 Azure 포털에도 표시됩니다.

  ![통합 모듈 도움말](../media/modules/module-activity-description.png)

* 모듈이 외부 서비스에 연결하면 연결 [유형을](#add-a-connection-type-to-your-module)정의합니다. 모듈의 각 cmdlet은 연결 개체(해당 연결 유형의 인스턴스)를 매개 변수로 받아들여야 합니다. 사용자는 연결 자산의 매개 변수를 cmdlet을 호출할 때마다 cmdlet의 해당 매개 변수에 매핑합니다. 위의 Runbook 예제에 따라 Contoso 리소스에 액세스하고 외부 서비스에서 데이터를 반환하는 Contoso 연결 자산이라는 `ContosoConnection` 예제를 사용합니다.

  다음 예제에서는 필드가 `UserName` `Password` `PSCredential` 개체의 및 속성에 매핑된 다음 cmdlet에 전달됩니다.

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

  cmdlet에 대해 매개 변수에 대한 연결 필드만 있는 대신 연결 개체를 매개 변수로 직접 수락하도록 허용하여 cmdlet에 대해 유사한 동작을 활성화할 수 있습니다. 일반적으로 각각에 대해 매개 변수를 설정하여 Azure Automation을 사용하는 사용자가 hashtable을 생성하지 않고 cmdlet를 호출하여 연결 개체의 역할을 할 수 있도록 합니다. 매개 변수 `UserAccount`집합은 연결 필드 속성을 전달하는 데 사용됩니다. `ConnectionObject`연결을 바로 통과할 수 있습니다.

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

* 모듈은 xcopy 가능 패키지에 완전히 포함되어야 합니다. Azure 자동화 모듈은 Runbook을 실행해야 할 때 자동화 샌드박스에 배포됩니다. 모듈은 실행 중인 호스트와는 독립적으로 작동해야 합니다. 모듈 패키지를 압축하여 이동하고 다른 호스트의 PowerShell 환경으로 가져올 때 정상적으로 작동하도록 할 수 있어야 합니다. 이렇게 하려면 모듈을 Azure Automation으로 가져올 때 압축된 모듈 폴더 외부의 파일에 의존해서는 안 됩니다. 모듈 또한 호스트의 고유한 레지스트리 설정(예: 제품 설치 시 지정된 설정)에 의존하면 안 됩니다. 모듈의 모든 파일에는 140자 미만의 경로가 있어야 합니다. 140자를 넘는 경로는 Runbook을 가져오는 데 문제가 발생합니다. 이 모범 사례를 따르지 않으면 Azure 자동화에서 모듈을 사용할 수 없습니다.  

* 모듈에서 [Azure PowerShell Az 모듈을](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) 참조하는 경우 `AzureRM`을 참조하지 않는지 확인합니다. 모듈과 함께 모듈을 `Az` `AzureRM` 사용할 수 없습니다. `Az`Runbook에서 지원되지만 기본적으로 가져오지 않습니다. 고려해야 할 `Az` 모듈 및 고려 사항에 대해 알아보려면 [Azure Automation의 Az 모듈 지원을](../az-modules.md)참조하십시오.

## <a name="default-modules"></a>기본 모듈

다음 표에는 자동화 계정을 만들 때 기본적으로 가져온 모듈이 나열됩니다. 자동화는 이러한 모듈의 최신 버전을 가져올 수 있습니다. 그러나 최신 버전을 삭제하더라도 자동화 계정에서 원래 버전을 제거할 수 없습니다.

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

* PowerShell 모듈을 만드는 방법에 대한 자세한 내용은 [Windows PowerShell 모듈 작성을](/powershell/scripting/developer/windows-powershell)참조하십시오.

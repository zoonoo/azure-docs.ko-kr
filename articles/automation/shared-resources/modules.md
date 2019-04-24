---
title: Azure Automation에서 모듈을 관리
description: 이 문서에서는 Azure Automation에서 모듈을 관리 하는 방법 설명
services: automation
ms.service: automation
ms.subservice: shared-resources
author: georgewallace
ms.author: gwallace
ms.date: 03/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fa7f5d3fb38eb1dbca51dec9b73dca3c998436aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60500398"
---
# <a name="manage-modules-in-azure-automation"></a>Azure Automation에서 모듈을 관리

Azure Automation은 PowerShell 기반 runbook에서 사용할 자동화 계정에 PowerShell 모듈을 가져올 수 있는 기능을 제공 합니다. 이러한 모듈을 만든 PowerShell 갤러리에서 사용자 지정 모듈 또는 Azure에 대 한 Az 및 AzureRM 모듈을 수 있습니다.

## <a name="import-modules"></a>모듈 가져오기

여러 가지 방법으로 모듈을 Automation 계정으로 가져올 수 있습니다. 다음 섹션에는 모듈을 가져오려면 다른 방법을 보여 줍니다.

> [!NOTE]
> Azure Automation에서 사용할 모듈의 파일의 최대 경로 140 자입니다. 140 자 초과 모든 경로 사용 하 여 PowerShell 세션을 가져올 수 없게 `Import-Module`합니다.

### <a name="powershell"></a>PowerShell

사용할 수는 [New-azurermautomationmodule](/powershell/module/azurerm.automation/new-azurermautomationmodule) Automation 계정에는 모듈을 가져올 수 있습니다. Cmdlet은 모듈 zip 패키지에 url을 사용 합니다.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure portal

Azure portal에서 Automation 계정으로 이동 및 선택 **모듈** 아래에서 **공유 리소스**합니다. 클릭 **+ 모듈 추가**합니다. 선택는 **.zip** 모듈 및 클릭을 포함 하는 파일 **확인** 가져오기 프로세스를 시작 합니다.

### <a name="powershell-gallery"></a>PowerShell 갤러리

PowerShell 갤러리에서 모듈 하거나 가져올 수 있습니다에서 합니다 [PowerShell 갤러리](https://www.powershellgallery.com) 직접 또는 Automation 계정에서 합니다.

PowerShell 갤러리에서 모듈을 가져오려면,로 이동 https://www.powershellgallery.com 가져오려는 모듈을 검색 합니다. 클릭 **Azure Automation에 배포** 에 **Azure Automation** 탭에서 **설치 옵션**합니다. 이 작업은 Azure portal을 엽니다. 에 **가져오기** 페이지에서 Automation 계정을 선택 하 고 클릭 **확인**합니다.

![PowerShell 갤러리 모듈 가져오기](../media/modules/powershell-gallery.png)

또한 Automation 계정에서 직접 PowerShell 갤러리에서 모듈을 가져올 수 있습니다. Automation 계정에서 선택 **모듈** 아래에서 **공유 리소스**합니다. 모듈 페이지에서 클릭 **갤러리 찾아보기**합니다. 열립니다는 **갤러리 찾아보기** 페이지입니다. 이 페이지를 사용 하 여 모듈의 PowerShell 갤러리를 검색할 수 있습니다. 가져오기 및 클릭 하려는 모듈을 선택 **가져올**합니다. 에 **가져오기** 페이지에서 클릭 **확인** 가져오기 프로세스를 시작 합니다.

![Azure portal에서 PowerShell 갤러리 가져오기](../media/modules/gallery-azure-portal.png)

## <a name="internal-cmdlets"></a>내부 cmdlet

다음은 내부에서 cmdlet의 목록을 `Orchestrator.AssetManagement.Cmdlets` 모든 Automation 계정으로 가져온 모듈입니다. 이러한 cmdlet runbook 및 DSC 구성에 액세스할 수 있으며 Automation 계정 내 자산을 사용 하 여 상호 작용할 수 있도록 합니다. 내부 cmdlet은 암호를 검색할 수 있도록 또한에서 암호화 **변수** 값을 **자격 증명**, 및 암호화 **연결** 필드입니다. Azure PowerShell cmdlet에서 이러한 비밀을 검색 하지 못합니다. 이러한 cmdlet에 암시적으로 사용 하는 경우 Azure를 연결할 수 필요 하지 않습니다. 실행 계정을 사용 하 여 Azure에 인증 해야 하는 등의 연결을 해야 하는 시나리오에 유용 합니다.

|Name|설명|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>모듈에는 연결 형식 추가

사용자 지정을 제공할 수 있습니다 [연결 형식](../automation-connections.md) 모듈에는 선택적 파일을 추가 하 여 Automation 계정에서 사용할 수 있습니다. 이 파일은 Automation 계정에서 모듈의 cmdlet과 함께 사용할 Azure Automation 연결 형식을 지정 하는 메타 데이터 파일. 이렇게 하려면 먼저 PowerShell 모듈을 작성 하는 방법을 알아야 합니다. 모듈 작성에 대 한 자세한 내용은 참조 하세요. [PowerShell 스크립트 모듈을 작성 하는 방법을](/powershell/developer/module/how-to-write-a-powershell-script-module)합니다.

![Azure portal에서 사용자 지정 연결 사용](../media/modules/connection-create-new.png)

Azure Automation 연결 형식을 추가할 모듈 이름의 파일이 있어야 `<ModuleName>-Automation.json` 연결 유형 속성을 지정 하는 합니다. 압축된 .zip 파일의 모듈 폴더 내에 배치되는 json 파일입니다. 이 파일 시스템 또는 모듈이 나타내는 서비스에 연결 하는 데 필요한 연결의 필드를 포함 합니다. 결국 이 구성은 Azure Automation의 연결 형식을 만들게 됩니다. 형식 필드 이름을 설정할 수 있습니다이 파일을 사용 하 여 암호화 된 또는 모듈의 연결 형식에 대 한 선택적 필드 여야 하는지 여부 및 합니다. 다음 예제에서는 사용자 이름 및 암호 속성을 정의 하는 json 파일 형식으로 템플릿입니다.

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

## <a name="module-best-practices"></a>모듈에 대 한 유용한 정보

PowerShell 모듈은 Azure Automation으로 가져와서 DSC 구성 내에서 사용할 수 있는 runbook 및 DSC 리소스 내에서 해당 cmdlet을 사용할 수 있도록 합니다. 배후에서 Azure Automation는 이러한 모듈을 저장하고 runbook 작업 및 DSC 컴파일 작업 실행 시 runbook을 실행하고 DSC 구성을 컴파일하는 Azure Automation 샌드박스에 로드합니다. 모듈의 모든 DSC 리소스 또한 Automation DSC 끌어오기 서버에 자동으로 배치됩니다. 이러한 리소스는 DSC 구성을 적용할 때 컴퓨터에서 끌어올 수 있습니다.

Azure Automation에서 사용할 PowerShell 모듈을 작성 하는 경우에 다음을 고려해 야 하는 것이 좋습니다.

* 모듈에서 모든 cmdlet에 대한 개요, 설명 및 도움말 URI를 포함합니다. PowerShell에서는 cmdlet에 대한 도움말 정보를 정의하여 사용자가 **Get-Help** cmdlet과 해당 정보를 통해 도움을 받을 수 있습니다. 다음 예제에서는 psm1 모듈 파일에 대 한 URI를 도와 개요를 정의 하는 방법을 보여 줍니다.

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

  이 정보를 제공하면 PowerShell 콘솔에서 **Get-Help** cmdlet을 사용할 때 이 도움말이 표시됩니다. Azure portal에서이 설명도 표시 됩니다.

  ![통합 모듈 도움말](../media/modules/module-activity-description.png)

* 있어야 모듈 외부 서비스에 연결 하는 경우는 [연결 형식](#add-a-connection-type-to-your-module)합니다. 모듈의 각 cmdlet은 매개 변수로 연결 개체(해당 연결 형식의 인스턴스)를 포함해야 합니다. 이를 통해 사용자는 cmdlet를 호출할 때마다 cmdlet의 해당 매개 변수에 연결 자산의 매개 변수를 매핑할 필요가 없습니다. 위의 runbook 예제에 따라 사용 하 여 ContosoConnection 호출 예제에서는 Contoso 연결 자산을 Contoso 리소스에 액세스 하 여 외부 서비스에서 데이터를 반환 합니다.

  다음 예제에서는 필드의 UserName 및 Password 속성에 매핑되는 `PSCredential` 개체를 cmdlet으로 전달 합니다.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  이 문제에 접근 하는 것이 더 빠르고 더 나은 방법은 직접 cmdlet를 연결 개체를 전달 해야 합니다.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  매개 변수에 대한 연결 필드 대신에 매개 변수인 연결 개체를 직접 수용할 수 있도록 하여 cmdlet에 이전 예제와 같은 동작을 설정할 수 있습니다. 일반적으로 각각에 대해 매개 변수를 설정하여 Azure Automation을 사용하는 사용자가 hashtable을 생성하지 않고 cmdlet를 호출하여 연결 개체의 역할을 할 수 있도록 합니다. 매개 변수 집합 `UserAccount`, 연결 필드 속성을 전달 하는 데 사용 됩니다. `ConnectionObject` 연결을 통해 직접 전달할 수 있습니다.

* 모듈의 모든 cmdlet의 출력 형식을 정의 합니다. cmdlet에 대한 출력 형식을 정의하면 디자인 타임 IntelliSense에서 작성 중에 사용하기 위한 cmdlet의 출력 속성을 확인할 수 있습니다. Automation runbook 그래픽을 작성하는 동안 특히 유용하며 이 경우 디자인 타임 지식은 모듈을 사용하는 쉬운 사용자 환경의 키입니다.

  추가 하 여 수행할 수 있습니다 `[OutputType([<MyOutputType>])]` MyOutputType 유효한 형식이 있는 합니다. OutputType에 대 한 자세한 내용은 참조 하세요 [함수에 대 한 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)합니다. 다음 코드를 추가 하는 예로 `OutputType` cmdlet:

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

* 모듈의 모든 cmdlet을 상태 비저장으로 만듭니다. 여러 runbook 작업에 동일한 AppDomain 및 프로세스와 샌드박스 동시에 실행할 수 있습니다. 해당 수준에서 공유 상태 이면 작업 서로 달라질 수 있습니다. 이 문제는 간헐적으로 발생 하며 문제를 진단 하기가 시킬 수 있습니다.  다음은 수행해서는 안되는 작업의 예제입니다.

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

* 모듈은 xcopy 가능 패키지에 완전히 포함 되어야 합니다. Runbook을 실행 해야 하는 경우 azure Automation 모듈이 Automation 샌드박스에 분산 됩니다. 모듈은 실행 중인 호스트와는 독립적으로 작동해야 합니다. 모듈 패키지를 Zip으로 압축한 후 이동하여 다른 호스트의 PowerShell 환경으로 가져왔을 때 정상적으로 작동하게 만들 수 있어야 합니다. 이를 위해 모듈은 모듈 폴더 외부의 파일에 의존하면 안 됩니다. 이 폴더는 모듈을 Azure Automation으로 가져올 때 zip으로 압축되는 폴더입니다. 모듈 또한 호스트의 고유한 레지스트리 설정(예: 제품 설치 시 지정된 설정)에 의존하면 안 됩니다. 모듈의 모든 파일 경로 140 자 초과 적은 있어야 합니다. 140 자 초과 모든 경로 runbook을 가져오는 중 문제가 발생 합니다. 이 모범 사례를 따르지 않으면 모듈은 Azure Automation에서 사용할 수 없습니다.  

* 모듈에서 [ 모듈](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)을 참조하는 경우 `AzureRM`도 참조하지 않는지 확인합니다. `Az` 모듈은 `AzureRM` 모듈과 함께 사용할 수 없습니다. `Az`는 기본적으로 가져오지 않는 Runbook에서 지원됩니다. `Az` 모듈과 고려할 사항에 대해 알아보려면 [Azure Automation의 Az 모듈 지원](../az-modules.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* PowerShell 모듈을 만드는 자세한 내용은 [Windows PowerShell 모듈 작성](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)
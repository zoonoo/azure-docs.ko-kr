---
title: Azure Automation에서 모듈 관리
description: 이 문서에서는 Azure 자동화에서 모듈을 관리하는 방법을 설명합니다.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a567b156c3a7e0c48e2c2f8db2d268e0eb3307c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770938"
---
# <a name="manage-modules-in-azure-automation"></a>Azure Automation에서 모듈 관리

Azure 자동화를 사용하면 PowerShell 모듈을 가져오면 DSC 구성에서 Runbook 및 DSC 리소스에서 cmdlet을 사용할 수 있습니다. Azure 자동화에 사용되는 모듈은 다음과 같습니다.

* [Azure PowerShell Az.자동화](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM.자동화](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Windows용 로그 분석 에이전트용 내부 `Orchestrator.AssetManagement.Cmdlets` 모듈
* [Azure자동화작성툴킷](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* 기타 파워쉘 모듈
* 생성한 사용자 지정 모듈 

자동화 계정을 만들 때 Azure Automation은 기본적으로 일부 모듈을 수입합니다. [기본 모듈을](#default-modules)참조하십시오.

Azure Automation이 Runbook 및 DSC 컴파일 작업을 실행하면 모듈을 런북을 실행할 수 있고 DSC 구성을 컴파일할 수 있는 샌드박스로 로드됩니다. 또한 자동화는 DSC 풀 서버의 모듈에 DSC 리소스를 자동으로 배치합니다. 기계는 DSC 구성을 적용할 때 리소스를 가져올 수 있습니다.

>[!NOTE]
>Runbook 및 DSC 구성에 실제로 필요한 모듈만 가져와야 합니다. 롤업 모듈(예: Az.Automation)을 가져오지 마십시오.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](../automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="default-modules"></a>기본 모듈

다음 표에는 자동화 계정을 만들 때 Azure Automation이 기본적으로 가져오는 모듈이 나열되어 있습니다. 자동화는 이러한 모듈의 최신 버전을 가져올 수 있습니다. 그러나 최신 버전을 삭제하더라도 자동화 계정에서 원래 버전을 제거할 수 없습니다. 이러한 기본 모듈에는 여러 AzureRM 모듈이 포함되어 있습니다. 

> [!NOTE]
> 솔루션이 포함된 자동화 계정의 모듈 및 runbook을 변경하는 것은 권장되지 않습니다. 

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


Az.Automation 모듈은 런북 및 DSC 구성에서 선호됩니다. 그러나 Azure Automation은 롤업 Az 모듈을 새 계정이나 기존 자동화 계정으로 자동으로 가져오지 않습니다. 이러한 모듈 작업에 대한 자세한 내용은 [Az 모듈로 마이그레이션을](#migrating-to-az-modules)참조하십시오.

## <a name="internal-cmdlets"></a>내부 cmdlet

다음 표는 모듈에서 지원하는 내부 cmdlet을 정의합니다. `Orchestrator.AssetManagement.Cmdlets` Runbook 및 DSC 구성에서 이러한 기능을 사용하여 자동화 계정 내의 자동화 자산과 상호 작용할 수 있습니다. cmdlet은 암호화된 변수, 자격 증명 및 암호화된 연결에서 비밀을 검색하도록 설계되었습니다. 

> [!NOTE]
> Azure PowerShell cmdlet은 내부 cmdlets가 검색할 수 있는 자산 비밀을 가져올 수 없습니다. 

|속성|Description|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|시작 자동화Runbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|대기 자동화 작업|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

이러한 내부 cmdlet은 Windows 하이브리드 Runbook 작업자에서 사용할 수 있지만 Linux 하이브리드 Runbook 작업자에서는 사용할 수 없습니다. 하이브리드 작업자가 사용하는 오케스트레이터 샌드박스에서 실행됩니다.  인증에 대 한 Run As 계정을 사용 하는 경우와 같이 Azure에 대 한 암시적 연결이 필요 하지 않습니다.

내부 cmdlet을 사용하는 대신 컴퓨터에서 직접 실행되는 런북 및 구성에 Az 또는 AzureRM cmdlet을 사용하거나 사용자 환경의 리소스에 대해 사용합니다. 이러한 경우 cmdlet을 사용할 때 AAA를 사용하여 Azure를 인증할 때와 같이 암시적으로 Azure에 연결해야 합니다. 

## <a name="modules-supporting-get-automationpscredential"></a>자동화 PS 자격 증명을 지원하는 모듈

Azure 자동화 작성 도구 키트를 사용하는 `Get-AutomationPSCredential` 로컬 개발의 경우 cmdlet은 [어셈블리 AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)의 일부입니다. Azure에서 자동화 컨텍스트로 작업하는 경우 cmdlet이 에 있습니다. `Orchestrator.AssetManagement.Cmdlets` Azure 자동화에서 자격 증명의 사용에 대해 자세히 알아보려면 [Azure 자동화의 자격 증명 자산을](credentials.md)참조하십시오.

## <a name="migrating-to-az-modules"></a>Az 모듈로 마이그레이션

Azure 자동화에서 Az 모듈을 사용할 때 고려해야 할 몇 가지 사항이 있습니다.

* 자동화 계정의 상위 수준 솔루션은 Runbook 및 모듈을 사용할 수 있습니다. 따라서 Runbook을 편집하거나 모듈을 업그레이드하면 솔루션에 문제가 발생할 수 있습니다. Az 모듈을 가져오기 전에 별도의 자동화 계정에서 모든 Runbook 및 솔루션을 신중하게 테스트해야 합니다. 

* 모듈을 수정하면 [영업외 솔루션 동안 VM 시작/중지에](../automation-solution-vm-management.md)부정적인 영향을 줄 수 있습니다. 

* 자동화 계정에 Az 모듈을 가져오면 Runbook에서 사용하는 PowerShell 세션에서 모듈이 자동으로 가져오지 않습니다. 다음과 같은 상황에서 모듈을 PowerShell 세션으로 가져올 수 있습니다.

    * Runbook이 모듈에서 cmdlet을 호출할 때
    * runbook이 `Import-Module` cmdlet을 통해 모듈을 명시적으로 가져오는 경우
    * Runbook이 다른 종속 모듈을 가져오는 경우

모듈 마이그레이션을 완료한 후에는 자동화 계정의 AzureRM 모듈을 사용하여 Runbook을 시작하지 마십시오. 또한 계정에서 AzureRM 모듈을 가져오거나 업데이트하지 않는 것이 좋습니다. Az.Automation으로 마이그레이션된 계정을 고려하고 Az 모듈로만 작동합니다. 

>[!IMPORTANT]
>새 자동화 계정을 만들 때 Azure Automation은 기본적으로 AzureRM 모듈을 설치합니다. AzureRM cmdlet을 통해 자습서 Runbook을 업데이트할 수 있습니다. 그러나 이러한 Runbook을 실행하면 안 됩니다.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>AzureRM 모듈을 사용하는 모든 Runbook을 중지하고 예약 취소

AzureRM 모듈을 사용하는 기존 Runbook을 실행하지 않도록 하려면 영향을 받는 모든 Runbook을 중지하고 일정을 취소합니다. 이 예제와 유사한 코드를 실행하여 어떤 일정이 있는지, 제거할 일정을 확인할 수 있습니다.

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

각 일정을 별도로 검토하여 필요에 따라 나중에 Runbook에 대해 일정을 변경할 수 있도록 하는 것이 중요합니다.

### <a name="import-the-az-modules"></a>Az 모듈 가져오기

이 섹션에서는 Azure 포털에서 Az 모듈을 가져오는 방법을 설명합니다. 전체 Az.Automation 모듈이 아닌 필요한 Az 모듈만 가져와야 합니다. [Az.Accounts는](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) 다른 Az 모듈에 대한 종속성이므로 다른 모듈보다 이 모듈을 가져와야 합니다.

1. 자동화 계정에서 **공유 리소스**에서 **모듈을 선택합니다.** 
2. **갤러리 찾아보기**를 클릭하여 갤러리 찾아보기 페이지를 엽니다.  
3. 검색 표시줄에 모듈 이름을 입력합니다. `Az.Accounts` 
4. PowerShell 모듈 페이지에서 **가져오기를** 클릭하여 모듈을 자동화 계정으로 가져옵니다.

    ![자동화 계정으로 모듈 가져오기](../media/modules/import-module.png)

이 가져오기 프로세스는 가져올 모듈을 검색하여 [PowerShell 갤러리를](https://www.powershellgallery.com) 통해 수행할 수도 있습니다. 모듈을 찾은 후 모듈을 선택하고 **Azure 자동화** 탭을 선택하고 **Azure 자동화에 배포를 클릭합니다.**

![갤러리에서 직접 모듈 가져오기](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Runbook 테스트

자동화 계정으로 Az 모듈을 가져온 후에는 Runbook을 편집하여 새 모듈을 사용할 수 있습니다. 대부분의 cmdlet은 AzureRM(또는 AzureRm) 접두사가 Az로 변경된 경우를 제외하고 AzureRM 모듈과 동일한 이름을 갖습니다. 이 명명 규칙을 따르지 않는 모듈 목록은 예외 [목록을](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)참조하십시오.

새 cmdlet을 사용 하 여 Runbook의 수정을 `Enable-AzureRmAlias -Scope Process` 테스트 하는 한 가지 방법은 Runbook의 시작 부분에서 사용 하는 것입니다. Runbook에 이 명령을 추가하면 스크립트를 변경 없이 실행할 수 있습니다.

## <a name="authoring-modules"></a>모듈 작성

Azure 자동화에서 사용할 PowerShell 모듈을 작성할 때 이 섹션의 고려 사항을 따르는 것이 좋습니다.

### <a name="references-to-azurerm-and-az"></a>AzureRM 및 Az에 대한 참조

모듈에서 Az 모듈을 참조하는 경우 AzureRM 모듈도 참조하지 않는지 확인합니다. 두 모듈 집합을 동시에 사용할 수 없습니다. 

### <a name="version-folder"></a>버전 폴더

모듈의 **.zip** 패키지에 버전 폴더를 포함하지 마십시오.  이 문제는 Runbook에 대한 우려는 적지만 DSC(상태 구성) 서비스에 문제가 발생합니다. Azure Automation은 모듈이 DSC에서 관리하는 노드에 배포될 때 버전 폴더를 자동으로 만듭니다. 버전 폴더가 있으면 두 개의 인스턴스가 표시됩니다. 다음은 DSC 모듈의 예제 폴더 구조입니다.

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>도움말 정보

모듈의 모든 cmdlet에 대한 개요, 설명 및 도움말 URI를 포함합니다. PowerShell에서 cmdlet을 사용하여 cmdlet에 `Get-Help` 대한 도움말 정보를 정의할 수 있습니다. 다음 예제에서는 **.psm1** 모듈 파일에서 개요를 정의하고 URI를 돕는 방법을 보여 줍니다.

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

### <a name="connection-type"></a>연결 형식

모듈이 외부 서비스에 연결하면 연결 [유형을](#adding-a-connection-type-to-your-module)정의합니다. 모듈의 각 cmdlet은 연결 개체(해당 연결 유형의 인스턴스)를 매개 변수로 받아들여야 합니다. 사용자는 연결 자산의 매개 변수를 cmdlet을 호출할 때마다 cmdlet의 해당 매개 변수에 매핑합니다. 다음 Runbook 예제는 이전 섹션의 예제를 기반으로 Contoso 리소스에 액세스하고 외부 서비스에서 데이터를 반환하기 위해 호출된 `ContosoConnection` Contoso 연결 자산을 사용합니다. 이 예제에서는 필드가 `UserName` `Password` `PSCredential` 개체의 및 속성에 매핑된 다음 cmdlet에 전달됩니다.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  이 동작에 접근하는 더 쉽고 좋은 방법은 연결 개체를 cmdlet에 직접 전달하는 것입니다.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  cmdlet에 대해 매개 변수에 대한 연결 필드만 있는 대신 연결 개체를 매개 변수로 직접 수락하도록 허용하여 cmdlet에 대해 유사한 동작을 활성화할 수 있습니다. 일반적으로 각각에 대해 매개 변수를 설정하여 Azure Automation을 사용하는 사용자가 hashtable을 생성하지 않고 cmdlet를 호출하여 연결 개체의 역할을 할 수 있도록 합니다. 매개변수 `UserAccount` 집합은 연결 필드 속성을 전달하는 데 사용됩니다. `ConnectionObject`연결을 바로 통과할 수 있습니다.

### <a name="output-type"></a>출력 형식

모듈의 모든 cmdlet에 대한 출력 유형을 정의합니다. cmdlet에 대한 출력 유형을 정의하면 설계 시간 IntelliSense를 사용하여 작성 중에 cmdlet의 출력 속성을 확인할 수 있습니다. 이 방법은 디자인 타임 지식이 모듈을 통해 쉽게 사용자 환경을 제공하는 데 핵심적인 그래픽 런북 작성 중에 특히 유용합니다.

유효한 `[OutputType([<MyOutputType>])]` `MyOutputType` 형식인 위치를 추가합니다. 자세한 `OutputType`내용은 함수 [OutputTypeAttribute 에 대해](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)참조하세요. 다음 코드는 cmdlet에 추가하는 `OutputType` 예입니다.

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

모듈의 모든 cmdlet을 상태 비저장 상태로 만듭니다. 여러 Runbook 작업은 동일한 `AppDomain` 프로세스와 샌드박스에서 동시에 실행할 수 있습니다. 이러한 수준에서 공유되는 상태가 있으면 작업이 서로 영향을 줄 수 있습니다. 이 동작은 간헐적이고 진단하기 어려운 문제로 이어질 수 있습니다. 다음은 하지 말아야 할 일의 예입니다.

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

xcopy를 사용하여 복사할 수 있는 패키지에 모듈이 완전히 포함되어 있는지 확인합니다. Azure 자동화 모듈은 Runbook이 실행될 때 자동화 샌드박스에 배포됩니다. 모듈은 모듈을 실행하는 호스트와 독립적으로 작동해야 합니다. 

모듈 패키지를 압축하여 이동하고 다른 호스트의 PowerShell 환경으로 가져올 때 정상적으로 작동하도록 할 수 있어야 합니다. 이렇게 하려면 모듈을 Azure Automation으로 가져올 때 압축된 모듈 폴더 외부의 파일에 모듈이 종속되지 않도록 하십시오. 

모듈은 호스트의 고유한 레지스트리 설정에 의존해서는 안 됩니다. 예를 들어 제품을 설치할 때 설정이 만들어집니다. 

### <a name="module-file-paths"></a>모듈 파일 경로

모듈의 모든 파일에 140자 미만의 경로가 있는지 확인합니다. 길이가 140자를 초과하면 Runbook 가져오기에 문제가 발생합니다. Azure Automation은 경로 크기가 140자를 초과한 파일을 을 `Import-Module`사용 하 여 PowerShell 세션으로 가져올 수 없습니다.

## <a name="importing-modules"></a>모듈 가져오기

이 섹션에서는 자동화 계정으로 모듈을 가져올 수 있는 여러 가지 방법을 정의합니다. 

### <a name="import-modules-in-azure-portal"></a>Azure 포털에서 모듈 가져오기

Azure 포털에서 모듈을 가져오려면 다음을 수행합니다.

1. Automation 계정으로 이동합니다.
2. **공유 리소스**에서 **모듈을 선택합니다.**
3. **모듈 추가를 클릭합니다.** 
4. 모듈이 포함된 **.zip** 파일을 선택합니다.
5. 프로세스 가져오기를 시작하려면 **확인을** 클릭합니다.

### <a name="import-modules-using-powershell"></a>PowerShell을 사용하여 모듈 가져오기

[New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) cmdlet을 사용하여 자동화 계정으로 모듈을 가져올 수 있습니다. cmdlet모듈 .zip 패키지의 URL을 사용합니다.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

동일한 cmdlet을 사용하여 PowerShell 갤러리에서 직접 모듈을 가져올 수도 있습니다. 파워 쉘 `ModuleName` `ModuleVersion` [갤러리에서](https://www.powershellgallery.com)잡아 있는지 확인하십시오.

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>PowerShell 갤러리에서 모듈 가져오기

갤러리에서 직접 또는 자동화 계정에서 [PowerShell 갤러리](https://www.powershellgallery.com) 모듈을 가져올 수 있습니다.

PowerShell 갤러리에서 직접 모듈을 가져오려면 다음을 수행하십시오.

1. 로 https://www.powershellgallery.com 이동하여 가져올 모듈을 검색합니다.
2. 설치 옵션 아래의 **Azure 자동화** 탭에서 Azure **자동화에 대한 배포를** **클릭합니다.** 이 작업은 Azure 포털을 엽니다. 
3. 가져오기 페이지에서 자동화 계정을 선택하고 **확인을**클릭합니다.

![파워쉘 갤러리 가져오기 모듈](../media/modules/powershell-gallery.png)

자동화 계정에서 직접 PowerShell 갤러리 모듈을 가져오려면 다음을 수행하십시오.

1. **공유 리소스**에서 **모듈을 선택합니다.** 
2. 모듈 페이지에서 **갤러리 찾아보기를**클릭한 다음 갤러리에서 모듈을 검색합니다. 
3. 가져올 모듈을 선택하고 **가져오기**를 클릭합니다. 
4. 가져오기 페이지에서 **확인을** 클릭하여 가져오기 프로세스를 시작합니다.

![Azure 포털에서 PowerShell 갤러리 가져오기](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>모듈 삭제

모듈에 문제가 있거나 이전 버전의 모듈로 롤백해야 하는 경우 자동화 계정에서 모듈을 삭제할 수 있습니다. 자동화 계정을 만들 때 가져온 [기본 모듈의](#default-modules) 원래 버전은 삭제할 수 없습니다. 삭제할 모듈이 [기본 모듈](#default-modules)중 하나의 최신 버전인 경우 자동화 계정에 설치된 버전으로 롤백합니다. 그렇지 않으면 자동화 계정에서 삭제한 모든 모듈이 제거됩니다.

### <a name="delete-modules-in-azure-portal"></a>Azure 포털에서 모듈 삭제

Azure 포털에서 모듈을 제거하려면 다음을 수행하십시오.

1. 자동화 계정으로 이동하여 공유 **리소스**에서 **모듈을 선택합니다.** 
2. 제거할 모듈을 선택합니다. 
3. **모듈** 페이지에서 **삭제를**선택합니다. 이 모듈이 기본 [모듈](#default-modules)중 하나인 경우 자동화 계정을 만들 때 존재했던 버전으로 롤백됩니다.

### <a name="delete-modules-using-powershell"></a>PowerShell을 사용하여 모듈 삭제

PowerShell을 통해 모듈을 제거하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>모듈에 연결 유형 추가

모듈에 선택적 메타데이터 파일을 추가하여 자동화 계정에서 사용할 사용자 지정 [연결 유형을](../automation-connections.md) 제공할 수 있습니다. 이 파일은 자동화 계정에서 모듈의 cmdlet과 함께 사용할 Azure 자동화 연결 유형을 지정합니다. PowerShell 모듈 작성에 대한 자세한 내용은 [PowerShell 스크립트 모듈 을 작성하는 방법을](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)참조하세요.

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

## <a name="next-steps"></a>다음 단계

* Azure PowerShell 모듈 사용에 대한 자세한 내용은 [Azure PowerShell 을 사용하여 시작하기](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)를 참조하십시오.
* PowerShell 모듈을 만드는 방법에 대한 자세한 내용은 [Windows PowerShell 모듈 작성을](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7)참조하십시오.

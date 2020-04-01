---
title: Azure Automation의 자격 증명 자산
description: Azure Automation의 자격 증명 자산은 runbook 또는 DSC 구성을 통해 액세스 되는 리소스를 인증하는 보안 자격 증명을 포함합니다. 이 문서에서는 자격 증명 자산을 만들고 runbook 또는 DSC 구성에 사용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 680e68d17637d71c1a1e5e8cfa539ee90028ac4e
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478771"
---
# <a name="credential-assets-in-azure-automation"></a>Azure Automation의 자격 증명 자산

자동화 자격 증명 자산에는 사용자 이름 및 암호와 같은 보안 자격 증명이 포함된 개체가 있습니다. Runbook 및 DSC 구성은 인증을 위해 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) 개체를 허용하는 cmdlet을 사용합니다. 또는 `PSCredential` 인증이 필요한 일부 응용 프로그램 이나 서비스에 제공 하는 개체의 사용자 이름 및 암호를 추출할 수 있습니다. Azure Automation은 자격 증명의 속성을 안전하게 저장하고 Runbook 또는 DSC 구성에서 액세스 권한에 대한 액세스 권한을 [Get-AutomationPSCredential](#activities-used-to-access-credentials) 작업으로 저장합니다.

> [!NOTE]
> Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 자동화 계정에 대해 생성되는 고유 키를 사용하여 Azure 자동화에 암호화되고 저장됩니다. 이 키는 Key Vault에 저장됩니다. 보안 자산을 저장하기 전에 Key Vault에서 키가 로드된 다음, 자산을 암호화하는 데 사용됩니다.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>자격 증명 자산에 사용되는 Azure PowerShell Az cmdlet

Azure PowerShell Az 모듈의 경우 다음 표의 cmdlet을 사용하여 Windows PowerShell을 사용하여 자동화 자격 증명 자산을 만들고 관리합니다. 자동화 런북 및 DSC 구성에 사용할 수 있는 [Az.Automation 모듈의](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)일부로 제공됩니다. [Azure 자동화에서 Az 모듈 지원을](https://docs.microsoft.com/azure/automation/az-modules)참조하십시오.

| Cmdlet | Description |
|:--- |:--- |
| [Get-Az자동화 자격 증명](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |자격 증명 자산에 대한 정보를 검색합니다. 이 cmdlet은 PSCredential 개체를 반환하지 않습니다.  |
| [새로운 아즈자동화 자격 증명](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |새 Automation 자격 증명을 만듭니다. |
| [제거-아즈자동화 자격 증명](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Automation 자격 증명을 제거합니다. |
| [설정-아즈자동화 자격 증명](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |기존 Automation 자격 증명에 대한 속성을 설정합니다. |

## <a name="activities-used-to-access-credentials"></a>자격 증명에 액세스하는 데 사용되는 활동

다음 표의 활동은 Runbook 및 DSC 구성의 자격 증명에 액세스하는 데 사용됩니다.

| 활동 | Description |
|:--- |:--- |
| `Get-AutomationPSCredential` |Runbook 또는 DSC 구성에 사용하는 자격 증명을 가져옵니다. 자격 증명은 개체의 `PSCredential` 형태입니다. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |사용자 이름 및 암호에 대한 프롬프트가 있는 자격 증명을 가져옵니다. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | 자격 증명 자산을 만듭니다. |

Azure 자동화 작성 도구 키트를 사용하는 `Get-AutomationPSCredential` 로컬 개발의 경우 cmdlet은 [어셈블리 AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)의 일부입니다. Azure에서 자동화 컨텍스트로 작업하는 경우 cmdlet이 에 있습니다. `Orchestrator.AssetManagement.Cmdlets` [Azure 자동화에서 모듈 관리를](modules.md)참조하십시오.

코드에서 개체를 `PSCredential` 검색하려면 [PowerShell ISE](https://github.com/azureautomation/azure-automation-ise-addon)에 대한 Microsoft Azure 자동화 ISE 추가 기능을 설치할 수 있습니다.

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

스크립트는 다음 예제와 같이 필요한 모듈을 필요한 경우 가져올 수도 있습니다. 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> 의 매개 변수에서 변수를 `Name` 사용하지 않아야 합니다. `Get-AutomationPSCredential` 이러한 사용으로 런북 또는 DSC 구성과 자격 증명 자산 간의 종속성이 디자인 시간에 복잡해질 수 있습니다.

## <a name="python2-functions-that-access-credentials"></a>자격 증명에 액세스하는 Python2 함수

다음 표의 함수는 Python2 Runbook의 자격 증명에 액세스하는 데 사용됩니다.

| 함수 | Description |
|:---|:---|
| `automationassets.get_automation_credential` | 자격 증명 자산에 대한 정보를 검색합니다. |

> [!NOTE]
> 파이썬 `automationassets` 런북 상단에 있는 모듈을 가져와 서저작물 함수에 액세스합니다.

## <a name="creating-a-new-credential-asset"></a>새 자격 증명 자산 만들기

Azure 포털을 사용하거나 Windows PowerShell을 사용하여 새 자격 증명 자산을 만들 수 있습니다.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Azure 포털을 사용하여 새 자격 증명 자산 만들기

1. 자동화 계정에서 **공유 리소스**에서 **자격 증명을 선택합니다.**
1. **자격 증명 추가**를 선택합니다.
2. 새 자격 증명 창에서 이름 지정 표준에 따라 적절한 자격 증명 이름을 입력합니다. 
3. **사용자 이름** 필드에 액세스 ID를 입력합니다. 
4. 두 암호 필드에 대해 비밀 액세스 키를 입력합니다.
5. 다단계 인증 확인란이 선택되면 선택을 취소합니다. 
6. 새 자격 증명 자산을 저장하려면 **만들기를 클릭합니다.**

> [!NOTE]
> Azure Automation은 다단계 인증을 사용하는 사용자 계정을 지원하지 않습니다.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Windows PowerShell을 사용하여 새 자격 증명 자산 만들기

다음 예제에서는 새 자동화 자격 증명 자산을 만드는 방법을 보여 주입니다. 개체는 `PSCredential` 먼저 이름과 암호로 만든 다음 자격 증명 자산을 만드는 데 사용됩니다. 또는 cmdlet을 `Get-Credential` 사용하여 사용자에게 이름과 암호를 입력하라는 메시지를 표시할 수 있습니다.


```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell 자격 증명 사용

Runbook 또는 DSC 구성은 `Get-AutomationPSCredential` 활동이 있는 자격 증명 자산을 검색합니다. 이 활동은 자격 `PSCredential` 증명이 필요한 활동 또는 cmdlet과 함께 사용할 수 있는 개체를 검색합니다. 자격 증명 개체의 속성을 검색하여 개별적으로 사용할 수도 있습니다. 개체에는 사용자 이름과 보안 암호에 대한 속성이 있습니다. 또는 [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) 메서드를 사용하여 암호의 보안되지 않은 버전을 나타내는 [NetworkCredential](/dotnet/api/system.net.networkcredential) 개체를 검색할 수 있습니다.

> [!NOTE]
> `Get-AzAutomationCredential`인증에 `PSCredential` 사용할 수 있는 개체는 검색되지 않습니다. 자격 증명에 대한 정보만 제공합니다. Runbook에서 자격 증명을 사용해야 하는 경우 을 사용하여 `PSCredential` `Get-AutomationPSCredential`개체로 검색해야 합니다.

### <a name="textual-runbook-example"></a>텍스트 런북 예제

다음 예제에서는 Runbook에서 PowerShell 자격 증명을 사용하는 방법을 보여 주며 있습니다. 자격 증명을 검색하고 변수에 사용자 이름과 암호를 할당합니다.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

자격 증명을 사용하여 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0)를 사용하여 Azure에 인증할 수도 있습니다. 대부분의 경우 Run As [계정을](../manage-runas-account.md) 사용하고 [Get-AzAutomationConnection](../automation-connections.md)을 사용하여 연결을 검색해야 합니다.


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>그래픽 런북 예제

그래픽 편집기의 라이브러리 창에서 자격 증명을 마우스 오른쪽 단추로 클릭하고 `Get-AutomationPSCredential` **캔버스에 추가를**선택하여 그래픽 런북에 활동을 추가할 수 있습니다.

![캔버스에 자격 증명 추가](../media/credentials/credential-add-canvas.png)

다음 그림에서는 그래픽 Runbook에서 자격 증명을 사용하는 예제를 보여 줍니다. 이 경우 자격 증명은 Azure 를 인증하기 위해 [Azure 자동화에서 Azure AD 사용에](../automation-use-azure-ad.md)설명된 대로 Runbook에 대한 인증을 Azure 리소스에 제공하는 데 사용됩니다. 첫 번째 활동에서는 Azure 구독에 액세스할 수 있는 자격 증명을 검색합니다. 그런 다음 계정 연결 활동은 이 자격 증명을 사용하여 이후에 발생하는 모든 활동에 대한 인증을 제공합니다. 단일 [pipeline link](../automation-graphical-authoring-intro.md#links-and-workflow) 개체를 예상하기 `Get-AutomationPSCredential` 때문에 파이프라인 링크가 여기에 사용됩니다.  

![캔버스에 자격 증명 추가](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>DSC 구성에서 자격 증명 사용

Azure 자동화의 DSC 구성은 자격 증명 `Get-AutomationPSCredential`자산을 사용하여 작업할 수 있지만 매개 변수를 통해 자격 증명 자산을 전달할 수도 있습니다. 자세한 내용은 [Azure Automation DSC에서 구성을 컴파일](../automation-dsc-compile.md#credential-assets)을 참조하세요.

## <a name="using-credentials-in-python2"></a>Python2에서 자격 증명 사용

다음 예제에서는 Python2 Runbook에서 자격 증명에 액세스하는 예제를 보여 주며 있습니다.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>다음 단계

* 그래픽 작성의 링크에 대한 자세한 내용은 [그래픽 작성의 링크를](../automation-graphical-authoring-intro.md#links-and-workflow)참조하십시오.
* 자동화에 대한 다양한 인증 방법을 이해하려면 [Azure 자동화 보안](../automation-security-overview.md)을 참조하십시오.
* 그래픽 런북을 시작하려면 [내 첫 번째 그래픽 실행북을](../automation-first-runbook-graphical.md)참조하십시오.
* PowerShell 워크플로 런북을 시작하려면 [내 첫 번째 PowerShell 워크플로 런북을](../automation-first-runbook-textual.md)참조하십시오.
* Python2 런북을 시작하려면 [내 첫 번째 Python2 runbook을](../automation-first-runbook-textual-python2.md)참조하십시오. 

---
title: Azure Automation에서 자격 증명 관리
description: Azure Automation의 자격 증명 자산은 runbook 또는 DSC 구성을 통해 액세스 되는 리소스를 인증하는 보안 자격 증명을 포함합니다. 이 문서에서는 자격 증명 자산을 만들고 runbook 또는 DSC 구성에 사용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 42abeba310e7a30364f93e998f12129a2d3c1f15
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114295"
---
# <a name="manage-credentials-in-azure-automation"></a>Azure Automation에서 자격 증명 관리

Automation 자격 증명 자산은 사용자 이름 및 암호와 같은 보안 자격 증명을 포함 하는 개체를 보유 합니다. Runbook 및 DSC 구성은 인증을 위해 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) 개체를 허용 하는 cmdlet을 사용 합니다. 또는 인증을 요구 하는 일부 응용 프로그램 또는 서비스에 `PSCredential` 제공할 개체의 사용자 이름 및 암호를 추출할 수 있습니다. 

> [!NOTE]
> Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 자동화 계정에 대해 생성 되는 고유 키를 사용 하 여 암호화 되 고 Azure Automation 저장 됩니다. 이 키는 Key Vault에 저장됩니다. 보안 자산을 저장하기 전에 Key Vault에서 키가 로드된 다음, 자산을 암호화하는 데 사용됩니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](../automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>자격 증명 자산에 사용 되는 Azure PowerShell Az cmdlet

Azure PowerShell Az module의 일부로 다음 표의 cmdlet은 Windows PowerShell을 사용 하 여 자동화 자격 증명 자산을 만들고 관리 하는 데 사용 됩니다. Automation runbook 및 DSC 구성에서 사용할 수 있는 [Az. automation 모듈](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)에서 제공 됩니다. [Azure Automation의 Az module support을](https://docs.microsoft.com/azure/automation/az-modules)참조 하십시오.

| Cmdlet | 설명 |
|:--- |:--- |
| [AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |자격 증명에 대 한 메타 데이터를 포함 하는 [Credentialinfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) 개체를 검색 합니다. Cmdlet은 개체 자체를 `PSCredential` 검색 하지 않습니다.  |
| [AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |새 Automation 자격 증명을 만듭니다. |
| [AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Automation 자격 증명을 제거합니다. |
| [AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |기존 Automation 자격 증명에 대한 속성을 설정합니다. |

## <a name="activities-used-to-access-credentials"></a>자격 증명에 액세스 하는 데 사용 되는 활동

다음 표의 활동은 runbook 및 DSC 구성에서 자격 증명에 액세스 하는 데 사용 됩니다.

| 작업 | Description |
|:--- |:--- |
| `Get-AutomationPSCredential` |Runbook 또는 `PSCredential` DSC 구성에 사용할 개체를 가져옵니다. 후자는 자격 증명 정보만 검색 하므로 `Get-AzAutomationCredential` cmdlet 대신이 작업을 사용 해야 하는 경우가 많습니다. 이 정보는 일반적으로 다른 cmdlet에 전달 하는 데 유용 하지 않습니다. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |사용자 이름 및 암호에 대 한 프롬프트를 사용 하 여 자격 증명을 가져옵니다. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | 자격 증명 자산을 만듭니다. |

코드에서 `PSCredential` 개체를 검색 하려면 `Orchestrator.AssetManagement.Cmdlets` 모듈을 가져와야 합니다. 자세한 내용은 [Azure Automation에서 모듈 관리](modules.md)를 참조 하세요.

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> 의 `Name` `Get-AutomationPSCredential`매개 변수에서는 변수를 사용 하지 않아야 합니다. 이를 통해 디자인 타임에 runbook 또는 DSC 구성과 자격 증명 자산 간의 종속성을 검색 하는 것이 복잡할 수 있습니다.

## <a name="python-2-functions-that-access-credentials"></a>자격 증명에 액세스 하는 Python 2 함수

다음 표의 함수는 Python 2 runbook의 자격 증명에 액세스 하는 데 사용 됩니다.

| 함수 | 설명 |
|:---|:---|
| `automationassets.get_automation_credential` | 자격 증명 자산에 대한 정보를 검색합니다. |

> [!NOTE]
> 자산 기능 `automationassets` 에 액세스 하려면 Python runbook의 맨 위에 있는 모듈을 가져옵니다.

## <a name="creating-a-new-credential-asset"></a>새 자격 증명 자산 만들기

Azure Portal 또는 Windows PowerShell을 사용 하 여 새 자격 증명 자산을 만들 수 있습니다.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Azure Portal를 사용 하 여 새 자격 증명 자산 만들기

1. Automation 계정에서 **공유 리소스**아래에 있는 **자격 증명** 을 선택 합니다.
1. **자격 증명 추가**를 선택합니다.
2. 새 자격 증명 창에서 이름 지정 표준에 따라 적절 한 자격 증명 이름을 입력 합니다. 
3. **사용자 이름** 필드에 액세스 ID를 입력 합니다. 
4. 두 암호 필드 모두에 비밀 액세스 키를 입력 합니다.

    ![새 자격 증명 만들기](../media/credentials/credential-create.png)

5. Multi-factor authentication 확인란이 선택 되어 있으면 선택을 취소 합니다. 
6. **만들기** 를 클릭 하 여 새 자격 증명 자산을 저장 합니다.

> [!NOTE]
> Azure Automation은 multi-factor authentication을 사용 하는 사용자 계정을 지원 하지 않습니다.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Windows PowerShell을 사용 하 여 새 자격 증명 자산 만들기

다음 예제에서는 새 자동화 자격 증명 자산을 만드는 방법을 보여 줍니다. 먼저 `PSCredential` 이름 및 암호를 사용 하 여 개체를 만든 다음 자격 증명 자산을 만드는 데 사용 합니다. 대신 `Get-Credential` cmdlet을 사용 하 여 사용자에 게 이름과 암호를 입력 하 라는 메시지를 표시할 수 있습니다.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell 자격 증명 사용

Runbook 또는 DSC 구성은 `Get-AutomationPSCredential` 활동을 사용 하 여 자격 증명 자산을 검색 합니다. 이 활동은 자격 `PSCredential` 증명이 필요한 활동 또는 cmdlet에 사용할 수 있는 개체를 검색 합니다. 자격 증명 개체의 속성을 검색하여 개별적으로 사용할 수도 있습니다. 개체에는 사용자 이름 및 보안 암호에 대 한 속성이 있습니다. 또는 [Getnetworkcredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) 메서드를 사용 하 여 보안 되지 않은 버전의 암호를 나타내는 [networkcredential](/dotnet/api/system.net.networkcredential) 개체를 검색할 수 있습니다.

> [!NOTE]
> `Get-AzAutomationCredential`는 인증에 사용할 `PSCredential` 수 있는 개체를 검색 하지 않습니다. 자격 증명에 대 한 정보만 제공 합니다. Runbook에서 자격 증명을 사용 해야 하는 경우를 사용 하 여 `PSCredential` `Get-AutomationPSCredential`해당 자격 증명을 개체로 검색 해야 합니다.

### <a name="textual-runbook-example"></a>텍스트 runbook 예

다음 예제에서는 runbook에서 PowerShell 자격 증명을 사용 하는 방법을 보여 줍니다. 자격 증명을 검색 하 고 해당 사용자 이름과 암호를 변수에 할당 합니다.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

자격 증명을 사용 하 여 [AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0)로 Azure에 인증할 수도 있습니다. 대부분의 경우 [실행 계정을](../manage-runas-account.md) 사용 하 고 [AzAutomationConnection](../automation-connections.md)를 사용 하 여 연결을 검색 해야 합니다.


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>그래픽 runbook 예

그래픽 편집기의 라이브러리 `Get-AutomationPSCredential` 창에서 자격 증명을 마우스 오른쪽 단추로 클릭 하 고 **캔버스에 추가**를 선택 하 여 그래픽 runbook에 활동을 추가할 수 있습니다.

![캔버스에 자격 증명 추가](../media/credentials/credential-add-canvas.png)

다음 그림에서는 그래픽 Runbook에서 자격 증명을 사용하는 예제를 보여 줍니다. 이 경우 자격 증명은 azure에 대 한 인증 [을 위해 azure AZURE AUTOMATION AD 사용](../automation-use-azure-ad.md)에 설명 된 대로 azure 리소스에 runbook에 대 한 인증을 제공 합니다. 첫 번째 활동에서는 Azure 구독에 액세스할 수 있는 자격 증명을 검색합니다. 그러면 계정 연결 작업에서이 자격 증명을 사용 하 여 그 후에 발생 하는 모든 활동에 대 한 인증을 제공 합니다. 에는 단일 개체가 필요 하기 때문 `Get-AutomationPSCredential` 에 [파이프라인 링크가](../automation-graphical-authoring-intro.md#links-and-workflow) 사용 됩니다.  

![캔버스에 자격 증명 추가](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>DSC 구성에서 자격 증명 사용

Azure Automation의 DSC 구성은를 사용 하 `Get-AutomationPSCredential`여 자격 증명 자산에서 작동할 수 있지만 매개 변수를 통해 자격 증명 자산을 전달할 수도 있습니다. 자세한 내용은 [Azure Automation DSC에서 구성을 컴파일](../automation-dsc-compile.md#credential-assets)을 참조하세요.

## <a name="using-credentials-in-python-2"></a>Python 2에서 자격 증명 사용

다음 예제에서는 Python 2 runbook의 자격 증명에 액세스 하는 예를 보여 줍니다.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>다음 단계

* 그래픽 작성의 링크에 대해 자세히 알아보려면 [그래픽 작성 링크](../automation-graphical-authoring-intro.md#links-and-workflow)를 참조 하세요.
* 자동화에 대 한 다양 한 인증 방법을 이해 하려면 [Azure Automation 보안](../automation-security-overview.md)을 참조 하세요.
* 그래픽 runbook을 시작 하려면 [내 첫 번째 그래픽 runbook](../automation-first-runbook-graphical.md)을 참조 하세요.
* PowerShell 워크플로 runbook을 시작 하려면 [내 첫 번째 powershell 워크플로 runbook](../automation-first-runbook-textual.md)을 참조 하세요.
* Python 2 runbook을 시작 하려면 [내 첫 번째 python 2 runbook](../automation-first-runbook-textual-python2.md)을 참조 하세요. 

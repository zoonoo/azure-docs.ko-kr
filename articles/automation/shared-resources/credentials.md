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
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252702"
---
# <a name="credential-assets-in-azure-automation"></a>Azure Automation의 자격 증명 자산

Automation 자격 증명 자산은 사용자 이름 및 암호와 같은 보안 자격 증명을 포함 하는 개체를 보유 합니다. Runbook과 DSC 구성은 인증을 위해 PSCredential 개체를 허용하는 cmdlet을 사용할 수 있고, 일부 애플리케이션 또는 인증이 필요한 서비스에 제공하기 위해 PSCredential 개체의 사용자 이름과 암호를 추출할 수 있습니다. 자격 증명의 속성은 Azure Automation에 안전하게 저장되며 [Get-AutomationPSCredential](#activities) 활동을 통해 runbook과 DSC 구성에서 액세스할 수 있습니다.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 Automation 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure Automation에 저장됩니다. 이 키는 Key Vault에 저장됩니다. 보안 자산을 저장하기 전에 Key Vault에서 키가 로드된 다음, 자산을 암호화하는 데 사용됩니다.

## <a name="azure-powershell-az-cmdlets"></a>Azure PowerShell Az cmdlet

Azure PowerShell Az module의 경우 다음 표의 cmdlet은 Windows PowerShell을 사용 하 여 자동화 자격 증명 자산을 만들고 관리 하는 데 사용 됩니다. Automation runbook 및 DSC 구성에서 사용할 수 있는 [Azureaz. automation 모듈](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)의 일부로 제공 됩니다.

| Cmdlet | Description |
|:--- |:--- |
| [AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |자격 증명 자산에 대한 정보를 검색합니다. 이는 PSCredential 개체를 반환 하지 않습니다.  |
| [AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |새 Automation 자격 증명을 만듭니다. |
| [AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Automation 자격 증명을 제거합니다. |
| [AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |기존 Automation 자격 증명에 대한 속성을 설정합니다. |

## <a name="activities"></a>활동

다음 표의 활동은 runbook과 DSC 구성의 자격 증명에 액세스하는데 사용됩니다.

| 활동 | Description |
|:--- |:--- |
| Get-AutomationPSCredential |Runbook 또는 DSC 구성에 사용하는 자격 증명을 가져옵니다. [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) 개체를 반환합니다. |

> [!NOTE]
> Get-AutomationPSCredential의 Name 매개변수에서는 변수를 사용하면 안 됩니다. runbook 또는 DSC 구성과 design time의 자격 증명 간에 종속성이 발견되어 복잡해질 수 있기 때문입니다.

## <a name="python2-functions"></a>Python2 함수

다음 표의 함수는 Python2 Runbook의 자격 증명에 액세스하는 데 사용됩니다.

| 함수 | Description |
|:---|:---|
| automationassets.get_automation_credential | 자격 증명 자산에 대한 정보를 검색합니다. |

> [!NOTE]
> 자산 함수에 액세스하려면 Python Runbook 맨 위에서 "automationassets" 모듈을 가져와야 합니다.

## <a name="creating-a-new-credential-asset"></a>새 자격 증명 자산 만들기

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Azure 포털을 사용하여 새 자격 증명 자산을 만들려면

1. Automation 계정으로 **공유 리소스** 아래에서 **자격 증명**을 선택합니다.
1. **자격 증명 추가**를 선택합니다.
1. 양식을 작성 하 고 **만들기** 를 선택 하 여 새 자격 증명을 저장 합니다.

> [!NOTE]
> 다단계 인증을 사용하는 사용자 계정은 Azure Automation에서 사용할 수 없습니다.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Windows PowerShell을 사용하여 새 자격 증명 자산을 만들려면

다음 명령 예제에서는 새 자동화 자격 증명을 만드는 방법을 보여 줍니다. 먼저 이름 및 암호를 사용하여 PSCredential 개체를 만든 다음 이를 사용하여 자격 증명 자산을 만듭니다. 또는 **Get-Credential** cmdlet을 사용하여 이름 및 암호를 입력하라는 메시지를 표시할 수 있습니다.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell 자격 증명 사용

**Get-AutomationPSCredential** 활동을 사용하여 runbook 또는 DSC 구성의 자격 증명 자산을 검색합니다. 그러면 PSCredential 매개 변수가 필요한 활동 또는 cmdlet에서 사용할 수 있는 [PSCredential 개체](/dotnet/api/system.management.automation.pscredential) 가 반환됩니다. 자격 증명 개체의 속성을 검색하여 개별적으로 사용할 수도 있습니다. 이 개체에는 사용자 이름 및 보안 암호에 대한 속성이 있으며, **GetNetworkCredential** 메서드를 사용하여 보안되지 않은 버전의 암호를 제공하는 [NetworkCredential](/dotnet/api/system.net.networkcredential) 개체를 반환할 수도 있습니다.

> [!NOTE]
> **AzAutomationCredential** 는 인증에 사용할 수 있는 **PSCredential** 을 반환 하지 않습니다. 자격 증명에 대 한 정보만 제공 합니다. Runbook에서 자격 증명을 사용 해야 하는 경우에는 **Get AutomationPSCredential** 을 사용 하 여 **PSCredential** 개체를 검색 해야 합니다.

### <a name="textual-runbook-sample"></a>텍스트 Runbook 샘플

다음 명령 예제에서는 Runbook에서 PowerShell 자격 증명을 사용하는 방법을 보여 줍니다. 이 예제에서는 자격 증명을 검색하고 해당 사용자 이름 및 암호를 변수에 할당합니다.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

자격 증명을 사용 하 여 [AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0)로 Azure에 인증할 수도 있습니다. 대부분의 경우 [실행 계정을](../manage-runas-account.md) 사용 하 고 [AzAutomationConnection](../automation-connections.md)를 사용 하 여 검색 해야 합니다.

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>그래픽 Runbook 샘플

그래픽 편집기의 라이브러리 창에서 자격 증명을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택하여 **Get-AutomationPSCredential**를 그래픽 Runbook에 추가합니다.

![캔버스에 자격 증명 추가](../media/credentials/credential-add-canvas.png)

다음 그림에서는 그래픽 Runbook에서 자격 증명을 사용하는 예제를 보여 줍니다. 이 경우 [AZURE AD 사용자 계정으로 Runbook 인증](../automation-create-aduser-account.md)에 설명 된 대로 azure 리소스에 runbook에 대 한 인증을 제공 하는 데 사용 됩니다. 첫 번째 활동에서는 Azure 구독에 액세스할 수 있는 자격 증명을 검색합니다. 그런 다음 **connect-azurermaccount** 작업은이 자격 증명을 사용 하 여 그 이후에 오는 모든 활동에 대 한 인증을 제공 합니다. [Get-AutomationPSCredential](../automation-graphical-authoring-intro.md#links-and-workflow) 에는 단일 개체가 필요하기 때문에 여기에서는 **파이프라인 링크** 를 사용합니다.  

![캔버스에 자격 증명 추가](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>DSC에서 PowerShell 자격 증명을 사용

Azure Automation의 DSC 구성은 **Get AutomationPSCredential**을 사용 하 여 자격 증명 자산을 참조할 수 있지만 필요한 경우 매개 변수를 통해 자격 증명 자산을 전달할 수도 있습니다. 자세한 내용은 [Azure Automation DSC에서 구성을 컴파일](../automation-dsc-compile.md#credential-assets)을 참조하세요.

## <a name="using-credentials-in-python2"></a>Python2에서 자격 증명 사용

다음 샘플에서는 Python2 Runbook의 자격 증명에 액세스하는 예제를 보여 줍니다.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>다음 단계

* 그래픽 작성 링크에 대해 자세히 알아보려면 [그래픽 작성 링크](../automation-graphical-authoring-intro.md#links-and-workflow)
* 자동자동화가 포함된 다양한 메서드를 이해하려면 [Azure Automation 보안](../automation-security-overview.md)
* 그래픽 Runbook을 시작하려면 [내 첫 번째 그래픽 Runbook](../automation-first-runbook-graphical.md)
* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](../automation-first-runbook-textual.md)
* Python2 Runbook을 시작하려면 [내 첫 번째 Python2 Runbook](../automation-first-runbook-textual-python2.md)을 참조하세요. 

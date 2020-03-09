---
title: Azure Automation 자격 증명 자산
description: Azure Automation의 자격 증명 자산은 runbook 또는 DSC 구성에서 액세스 하는 리소스에 인증 하는 데 사용할 수 있는 보안 자격 증명을 포함 합니다. 이 문서에서는 자격 증명 자산을 만들고 runbook 또는 DSC 구성에서 사용 하는 방법을 설명 합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373524"
---
# <a name="credential-assets-in-azure-automation"></a>Azure Automation 자격 증명 자산

Automation 자격 증명 자산은 사용자 이름 및 암호와 같은 보안 자격 증명을 포함 하는 개체를 보유 합니다. Runbook 및 DSC 구성은 인증을 위해 PSCredential 개체를 허용 하는 cmdlet을 사용 하거나, 인증이 필요한 일부 응용 프로그램 또는 서비스에 제공할 PSCredential 개체의 사용자 이름 및 암호를 추출할 수 있습니다. 자격 증명에 대 한 속성은 Azure Automation에 안전 하 게 저장 되며, [Get AutomationPSCredential](#activities) 작업을 사용 하 여 RUNBOOK 또는 DSC 구성에서 액세스할 수 있습니다.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Azure Automation의 보안 자산에는 자격 증명, 인증서, 연결, 암호화 된 변수 등이 있습니다. 이러한 자산은 각 자동화 계정에 대해 생성 되는 고유 키를 사용 하 여 암호화 되 고 Azure Automation 저장 됩니다. 이 키는 Key Vault에 저장 됩니다. 보안 자산을 저장 하기 전에 Key Vault에서 키를 로드 한 다음 자산을 암호화 하는 데 사용 합니다.

## <a name="azure-powershell-az-cmdlets"></a>Azure PowerShell Az cmdlet

Azure PowerShell Az module의 경우 다음 표의 cmdlet은 Windows PowerShell을 사용 하 여 자동화 자격 증명 자산을 만들고 관리 하는 데 사용 됩니다. Automation runbook 및 DSC 구성에서 사용할 수 있는 [Azureaz. automation 모듈](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)의 일부로 제공 됩니다.

| Cmdlet | 한 |
|:--- |:--- |
| [AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |자격 증명 자산에 대 한 정보를 검색 합니다. 이는 PSCredential 개체를 반환 하지 않습니다.  |
| [AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |새 자동화 자격 증명을 만듭니다. |
| [AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |자동화 자격 증명을 제거 합니다. |
| [AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |기존 자동화 자격 증명에 대 한 속성을 설정 합니다. |

## <a name="activities"></a>내용

다음 표의 활동은 runbook 및 DSC 구성의 자격 증명에 액세스 하는 데 사용 됩니다.

| 내용 | 한 |
|:--- |:--- |
| 가져오기-AutomationPSCredential |Runbook 또는 DSC 구성에서 사용할 자격 증명을 가져옵니다. [System.object](/dotnet/api/system.management.automation.pscredential) 를 반환 합니다. |

> [!NOTE]
> Runbook 또는 DSC 구성 간에 종속성을 검색 하 고 디자인 타임에 자격 증명 자산을 검색 하는 것이 복잡할 수 있으므로-Name 매개 변수에서-Name 매개 변수를 사용 하지 않아야 합니다.

## <a name="python2-functions"></a>Python2 함수

다음 표의 함수는 Python2 runbook의 자격 증명에 액세스 하는 데 사용 됩니다.

| 칩셋용으로 | 한 |
|:---|:---|
| automationassets. get_automation_credential | 자격 증명 자산에 대 한 정보를 검색 합니다. |

> [!NOTE]
> 자산 기능에 액세스 하려면 Python runbook의 맨 위에 있는 "automationassets" 모듈을 가져와야 합니다.

## <a name="creating-a-new-credential-asset"></a>새 자격 증명 자산 만들기

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Azure Portal를 사용 하 여 새 자격 증명 자산을 만들려면

1. Automation 계정에서 **공유 리소스**아래에 있는 **자격 증명** 을 선택 합니다.
1. **자격 증명 추가를**선택 합니다.
1. 양식을 작성 하 고 **만들기** 를 선택 하 여 새 자격 증명을 저장 합니다.

> [!NOTE]
> Multi-factor authentication을 사용 하는 사용자 계정은 Azure Automation에서 사용 하도록 지원 되지 않습니다.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Windows PowerShell을 사용 하 여 새 자격 증명 자산을 만들려면

다음 명령 예제에서는 새 자동화 자격 증명을 만드는 방법을 보여 줍니다. 먼저 이름 및 암호를 사용 하 여 PSCredential 개체를 만든 다음이를 사용 하 여 자격 증명 자산을 만듭니다. 또는 **Get Credential** cmdlet을 사용 하 여 이름과 암호를 입력 하 라는 메시지를 표시할 수 있습니다.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell 자격 증명 사용

**Get AutomationPSCredential** 작업을 사용 하 여 RUNBOOK 또는 DSC 구성에서 자격 증명 자산을 검색 합니다. 그러면 PSCredential 매개 변수가 필요한 활동 또는 cmdlet에 사용할 수 있는 [pscredential 개체가](/dotnet/api/system.management.automation.pscredential) 반환 됩니다. 자격 증명 개체의 속성을 검색 하 여 개별적으로 사용할 수도 있습니다. 개체에는 사용자 이름 및 보안 암호에 대 한 속성이 포함 되어 있거나, **Getnetworkcredential** 메서드를 사용 하 여 보안 되지 않은 버전의 암호를 제공 하는 [networkcredential](/dotnet/api/system.net.networkcredential) 개체를 반환할 수 있습니다.

> [!NOTE]
> **AzAutomationCredential** 는 인증에 사용할 수 있는 **PSCredential** 을 반환 하지 않습니다. 자격 증명에 대 한 정보만 제공 합니다. Runbook에서 자격 증명을 사용 해야 하는 경우에는 **Get AutomationPSCredential** 을 사용 하 여 **PSCredential** 개체를 검색 해야 합니다.

### <a name="textual-runbook-sample"></a>텍스트 runbook 샘플

다음 명령 예제에서는 runbook에서 PowerShell 자격 증명을 사용 하는 방법을 보여 줍니다. 이 예제에서는 자격 증명을 검색 하 고 해당 사용자 이름과 암호를 변수에 할당 합니다.

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

### <a name="graphical-runbook-sample"></a>그래픽 runbook 샘플

그래픽 편집기의 라이브러리 창에서 자격 증명을 마우스 오른쪽 단추로 클릭 하 고 **캔버스에 추가**를 선택 하 여 그래픽 Runbook에 **Get automationpscredential** 작업을 추가 합니다.

![캔버스에 자격 증명 추가](../media/credentials/credential-add-canvas.png)

다음 그림에서는 그래픽 runbook에서 자격 증명을 사용 하는 예를 보여 줍니다. 이 경우 [AZURE AD 사용자 계정으로 Runbook 인증](../automation-create-aduser-account.md)에 설명 된 대로 azure 리소스에 runbook에 대 한 인증을 제공 하는 데 사용 됩니다. 첫 번째 작업은 Azure 구독에 대 한 액세스 권한이 있는 자격 증명을 검색 합니다. 그런 다음 **connect-azurermaccount** 작업은이 자격 증명을 사용 하 여 그 이후에 오는 모든 활동에 대 한 인증을 제공 합니다. **Get AutomationPSCredential** 에는 단일 개체가 필요 하기 때문에 [파이프라인 링크가](../automation-graphical-authoring-intro.md#links-and-workflow) 여기에 있습니다.  

![캔버스에 자격 증명 추가](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>DSC에서 PowerShell 자격 증명 사용

Azure Automation의 DSC 구성은 **Get AutomationPSCredential**을 사용 하 여 자격 증명 자산을 참조할 수 있지만 필요한 경우 매개 변수를 통해 자격 증명 자산을 전달할 수도 있습니다. 자세한 내용은 [AZURE AUTOMATION DSC에서 구성 컴파일](../automation-dsc-compile.md#credential-assets)을 참조 하세요.

## <a name="using-credentials-in-python2"></a>Python2에서 자격 증명 사용

다음 샘플에서는 Python2 runbook의 자격 증명에 액세스 하는 예를 보여 줍니다.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>다음 단계

* 그래픽 작성의 링크에 대해 자세히 알아보려면 [그래픽 작성 링크](../automation-graphical-authoring-intro.md#links-and-workflow) 를 참조 하세요.
* 자동화를 사용 하는 다양 한 인증 방법을 이해 하려면 [Azure Automation 보안](../automation-security-overview.md) 을 참조 하세요.
* 그래픽 runbook을 시작 하려면 [내 첫 번째 그래픽 runbook](../automation-first-runbook-graphical.md) 을 참조 하세요.
* PowerShell 워크플로 runbook을 시작 하려면 [내 첫 번째 powershell 워크플로 runbook](../automation-first-runbook-textual.md) 을 참조 하세요.
* Python2 runbook을 시작 하려면 [내 첫 번째 Python2 runbook](../automation-first-runbook-textual-python2.md) 을 참조 하세요. 

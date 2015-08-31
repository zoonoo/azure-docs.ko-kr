<properties 
   pageTitle="Azure 자동화의 자격 증명 자산 | Microsoft Azure"
   description="Azure 자동화의 자격 증명 자산에는 Runbook에서 액세스하는 리소스를 인증하는 데 사용할 수 있는 보안 자격 증명이 들어 있습니다. 이 문서에서는 자격 증명 자산을 만들고 Runbook에서 사용하는 방법에 대해 설명합니다."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/18/2015"
   ms.author="bwren" />

# Azure 자동화의 자격 증명 자산

자동화 자격 증명 자산은 사용자 이름과 암호 등의 보안 자격 증명을 포함하는 [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) 개체를 보유합니다. Runbook에서는 PSCredential 개체를 허용하는 cmdlet을 인증에 사용하거나, PSCredential 개체의 사용자 이름 및 암호를 추출하여 인증이 필요한 일부 응용 프로그램 또는 서비스에 제공할 수 있습니다. 자격 증명 속성은 Azure 자동화에 안전하게 저장되며, [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) 활동을 사용하여 Runbook에서 액세스할 수 있습니다.

>[AZURE.NOTE]Azure 자동화의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 자동화 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure 자동화에 저장됩니다. 이 키는 마스터 인증서로 암호화되어 Azure 자동화에 저장됩니다. 자동화 계정에 대한 키는 보안 자산을 저장하기 전에 마스터 인증서를 사용하여 암호가 해독된 후 자산을 암호화하는 데 사용됩니다.

## Windows PowerShell cmdlet

다음 표의 cmdlet은 Windows PowerShell을 사용하여 자동화 자격 증명 자산을 만들고 관리하는 데 사용됩니다. 이러한 cmdlet은 자동화 Runbook에서 사용할 수 있는 [Azure PowerShell 모듈](../powershell-install-configure.md)의 일부로 제공됩니다.

|Cmdlet|설명|
|:---|:---|
|[Get-AzureAutomationCredential](http://msdn.microsoft.com/library/dn913781.aspx)|자격 증명 자산에 대한 정보를 검색합니다. **Get-AutomationCredential** 활동에서는 자격 증명 자체만 검색할 수 있습니다.|
|[New-AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|새 자동화 자격 증명을 만듭니다.|
|[Remove- AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|자동화 자격 증명을 제거합니다.|
|[Set- AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|기존 자동화 자격 증명에 대한 속성을 설정합니다.|

## Runbook 활동

다음 표의 활동은 Runbook에서 자격 증명에 액세스하는 데 사용됩니다.

|활동|설명|
|:---|:---|
|Get-AutomationPSCredential|Runbook에서 사용할 자격 증명을 가져옵니다. [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) 개체를 반환합니다.|

>[AZURE.NOTE]Get-AutomationPSCredential의 –Name 매개 변수에 변수를 사용하지 않는 것이 좋습니다. 변수를 사용하면 디자인 타임에 Runbook과 자격 증명 자산 간의 종속성 검색이 복잡해질 수 있습니다.

## 새 자격 증명 만들기


### Azure 포털을 사용하여 새 자격 증명을 만들려면

1. 자동화 계정에서 창의 위쪽에 있는 **자산**을 클릭합니다.
1. 창의 아래쪽의 **설정 추가**를 클릭합니다.
1. **자격 증명 추가**를 클릭합니다.
2. **자격 증명 형식** 드롭다운에서 **PowerShell 자격 증명**을 선택합니다.
1. 마법사를 완료하고 새 자격 증명을 저장하는 확인란을 클릭합니다.


### Azure Preview 포털을 사용하여 새 자격 증명을 만들려면

1. 자동화 계정에서 **자산** 파트를 클릭하여 **자산** 블레이드를 엽니다.
1. **자격 증명** 파트를 클릭하여 **자격 증명** 블레이드를 엽니다.
1. 블레이드의 위쪽에서 **자격 증명 추가**를 클릭합니다.
1. 양식을 완료하고 **만들기**를 클릭하여 새 자격 증명을 저장합니다.


### Windows PowerShell을 사용하여 새 PowerShell 자격 증명을 만들려면

다음 명령 예제에서는 새 자동화 자격 증명을 만드는 방법을 보여 줍니다. 먼저 이름 및 암호를 사용하여 PSCredential 개체를 만든 다음 이를 사용하여 자격 증명 자산을 만듭니다. 또는 **Get-Credential** cmdlet을 사용하여 이름 및 암호를 입력하라는 메시지를 표시할 수 있습니다.

	$user = "MyDomain\MyUser"
	$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
	$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
	New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

## Runbook에서 PowerShell 자격 증명 사용

**Get-AutomationPSCredential** 활동을 사용하여 Runbook에서 자격 증명 자산을 검색할 수 있습니다. 그러면 PSCredential 매개 변수가 필요한 활동 또는 cmdlet에서 사용할 수 있는 [PSCredential 개체](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx)가 반환됩니다. 자격 증명 개체의 속성을 검색하여 개별적으로 사용할 수도 있습니다. 이 개체에는 사용자 이름 및 보안 암호에 대한 속성이 있으며, **GetNetworkCredential** 메서드를 사용하여 보안되지 않은 버전의 암호를 제공하는 [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) 개체를 반환할 수도 있습니다.

### 텍스트 Runbook 샘플

다음 명령 예제에서는 Runbook에서 PowerShell 자격 증명을 사용하는 방법을 보여 줍니다. 이 예제에서는 자격 증명을 검색하고 해당 사용자 이름 및 암호를 변수에 할당합니다.

	$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
	$userName = $myCredential.UserName
	$securePassword = $myCredential.Password
	$password = $myCredential.GetNetworkCredential().Password


### 그래픽 Runbook 샘플

그래픽 편집기의 라이브러리 창에서 자격 증명을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택하여 **Get-AutomationPSCredential**를 그래픽 Runbook에 추가합니다.


![캔버스에 자격 증명 추가](media/automation-credentials/credential-add-canvas.png)

다음 그림에서는 그래픽 Runbook에서 자격 증명을 사용하는 예제를 보여 줍니다. 이 예제에서는 [Azure 리소스에 대한 인증 구성](#automation-configuring.md)에 설명된 대로 자격 증명을 사용하여 Azure 리소스에 Runbook에 대한 인증을 제공합니다. 첫 번째 활동에서는 Azure 구독에 액세스할 수 있는 자격 증명을 검색합니다. 그런 다음 **Add-AzureAccount** 활동에서 이 자격 증명을 사용하여 이후의 모든 활동에 대한 인증을 제공합니다. **Get-AutomationPSCredential**에는 단일 개체가 필요하기 때문에 여기에서는 [파이프라인 링크](automation-graphical-authoring-intro.md#links-and-workflow)를 사용합니다.

![캔버스에 자격 증명 추가](media/automation-credentials/get-credential.png)


## 관련된 문서

- [그래픽 작성의 링크](automation-graphical-authoring-intro.md#links-and-workflow)

 

<!---HONumber=August15_HO8-->
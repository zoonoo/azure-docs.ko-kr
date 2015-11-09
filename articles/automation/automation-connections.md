<properties 
   pageTitle="Azure 자동화의 연결 자산 | Microsoft Azure"
   description="Azure 자동화의 연결 자산은 외부 서비스 또는 runbook이나 DSC 구성의 응용 프로그램을 연결하는데 필요한 정보를 포함합니다. 이 문서에서는 연결에 대해 자세히 알아보고 텍스트 작성과 그래픽 작성 모두에서 연결을 사용하는 방법을 설명합니다."
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
   ms.date="10/23/2015"
   ms.author="bwren" />

# Azure 자동화의 연결 자산

자동화 연결 자산은 외부 서비스 또는 runbook의 응용 프로그램이나 DSC 구성 연결에 필요한 정보를 포함합니다. 여기에는 URL 또는 포트와 같은 연결 정보 외에 사용자 이름 및 암호와 같은 인증에 필요한 정보가 포함될 수 있습니다. 연결 값은 여러 변수를 만드는 대신, 특정 응용 프로그램에 연결하기 위한 모든 속성을 하나의 자산에 유지합니다. 사용자는 한 위치의 연결에 대한 값을 편집할 수 있고 단일 매개 변수에서 연결 이름을 runbook 이나 DSC구성에 전달할 수 있습니다. 연결에 대한 속성은 **Get-AutomationConnection** 활동을 사용하여 runbook 또는 DSC 구성에 액세스할 수 있습니다.

연결을 만들 때 *연결 형식*을 지정해야 합니다. 연결 형식은 속성 집합을 정의하는 템플릿입니다. 연결은 해당 연결 형식에 정의된 각 속성의 값을 정의합니다. 연결 형식은 통합 모듈에서 Azure 자동화에 추가되거나 [Azure 자동화 API](http://msdn.microsoft.com/library/azure/mt163818.aspx)를 사용하여 만들어집니다. 연결을 만들 때 사용할 수 있는 연결 형식만 자동화 계정에 설치됩니다.

>[AZURE.NOTE]Azure 자동화의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 자동화 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure 자동화에 저장됩니다. 이 키는 마스터 인증서로 암호화되어 Azure 자동화에 저장됩니다. 자동화 계정에 대한 키는 보안 자산을 저장하기 전에 마스터 인증서를 사용하여 암호가 해독된 후 자산을 암호화하는 데 사용됩니다.

## Windows PowerShell cmdlet

다음 표에 나와있는 cmdlet는 Windows PowerShell Window PowerShell의 자동화 연결을 생성하고 관리하는데 사용됩니다. 자동화 runbook과 DSC 구성에 사용할 수 있는 [Azure PowerShell 모듈](../powershell-install-configure.md)의 일부로 전송됩니다.

|Cmdlet|설명|
|:---|:---|
|[Get-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921828.aspx)|연결을 검색합니다. 연결의 필드 값이 있는 해시 테이블을 포함합니다.|
|[New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx)|새 연결을 만듭니다.|
|[Remove-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921827.aspx)|기존 연결을 제거합니다.|
|[Set-AzureAutomationConnectionFieldValue](http://msdn.microsoft.com/library/dn921826.aspx)|기존 연결의 특정 필드 값을 설정합니다.|

## 활동

다음 표의 활동은 runbook 또는 DSC 구성의 연결에 액세스하는데 사용됩니다.

|활동|설명|
|---|---|
|Get-AutomationConnection|사용할 연결을 가져옵니다. 연결의 속성이 있는 해시 테이블을 반환합니다.|

>[AZURE.NOTE]**Get- AutomationConnection**의 Name 매개변수에서는 변수를 사용하면 안 됩니다. runbook 또는 DSC 구성과 design time의 자격 증명 간에 종속성이 발견되어 복잡해질 수 있기 때문입니다.

## 새 연결 만들기

### Azure 포털을 사용하여 새 연결을 만들려면

1. 자동화 계정에서 창의 위쪽에 있는 **자산**을 클릭합니다.
1. 창의 아래쪽의 **설정 추가**를 클릭합니다.
1. **연결 추가**를 클릭합니다.
2. **연결 형식** 드롭다운에서 만들려는 연결 형식을 선택합니다. 마법사에 해당 특정 형식에 대한 속성이 표시됩니다.
1. 마법사를 완료하고 새 연결을 저장하는 확인란을 클릭합니다.


### Azure Preview 포털을 사용하여 새 연결을 만들려면

1. 자동화 계정에서 **자산** 파트를 클릭하여 **자산** 블레이드를 엽니다.
1. **연결** 파트를 클릭하여 **연결** 블레이드를 엽니다.
1. 블레이드의 위쪽에서 **연결 추가**를 클릭합니다.
2. **형식** 드롭다운에서 만들려는 연결 형식을 선택합니다. 양식에 해당 특정 형식에 대한 속성이 표시됩니다.
1. 양식을 완료하고 **만들기**를 클릭하여 새 연결을 저장합니다.



### Windows PowerShell을 사용하여 새 연결을 만들려면

Windows PowerShell에서 [New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx) cmdlet을 사용하여 새 연결을 만듭니다. 이 cmdlet에는 연결 형식에 정의된 각 특성의 값을 정의하는 [해시 테이블](http://technet.microsoft.com/library/hh847780.aspx)이 필요한 **ConnectionFieldValues**라는 매개 변수가 있습니다.


다음 명령 예제에서는 텍스트 메시지를 주고받을 수 있도록 해주는 전화 통신 서비스인 [Twilio](http://www.twilio.com)에 대한 새 연결을 만듭니다. Twilio 연결 형식이 포함된 샘플 통합 모듈은 [스크립트 센터](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8)에서 사용할 수 있습니다. 이 연결 형식은 Twilio에 연결할 때 사용자 계정의 유효성을 검사하는 데 필요한 계정 SID 및 권한 부여 토큰에 대한 속성을 정의합니다. 이 샘플 코드를 사용하려면 [이 모듈을 다운로드](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8)하여 자동화 계정에 설치해야 합니다.

	$AccountSid = "DAf5fed830c6f8fac3235c5b9d58ed7ac5"
	$AuthToken  = "17d4dadfce74153d5853725143c52fd1"
	$FieldValues = @{"AccountSid" = $AccountSid;"AuthToken"=$AuthToken}

	New-AzureAutomationConnection -AutomationAccountName "MyAutomationAccount" -Name "TwilioConnection" -ConnectionTypeName "Twilio" -ConnectionFieldValues $FieldValues


## runbook 또는 DSC 구성에서 연결 사용하기

**Get-AutomationConnection** cmdlet를 사용하여 runbook 또는 DSC 구성의 연결을 검색합니다. 이 활동은 연결의 여러 필드값을 검색하고 runbook 또는 DSC 구성의 적절한 명령과 함께 사용될 수 있는 [해시 테이블](http://go.microsoft.com/fwlink/?LinkID=324844)로 반환합니다.

### 텍스트 Runbook 샘플
다음 명령 예제에서는 이전 예제의 Twilio 연결을 사용하여 Runbook에서 텍스트 메시지를 보내는 방법을 보여 줍니다. 여기에서 사용된 Send-TwilioSMS 활동에는 각각 Twilio 서비스를 인증하는 데 서로 다른 메서드를 사용하는 두 개의 매개 변수 집합이 있습니다. 하나는 연결 개체를 사용하고, 나머지는 계정 SID 및 권한 부여 토큰에 대한 개별 매개 변수를 사용합니다. 두 메서드 모두 이 샘플에 나와 있습니다.

	$Con = Get-AutomationConnection -Name "TwilioConnection"
	$NumTo = "14255551212"
	$NumFrom = "15625551212"
	$Body = "Text from Azure Automation."

	#Send text with connection object.
	Send-TwilioSMS -Connection $Con -From $NumFrom -To $NumTo -Body $Body

	#Send text with connection properties.
	Send-TwilioSMS -AccountSid $Con.AccountSid -AuthToken $Con.AuthToken $Con -From $NumFrom -To $NumTo -Body $Body

### 그래픽 Runbook 샘플

그래픽 편집기의 라이브러리 창에서 연결을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택하여 **Get-AutomationConnection**를 그래픽 Runbook에 추가합니다.

![](media/automation-connections/connection-add-canvas.png)

다음 그림에서는 그래픽 Runbook에서 연결을 사용하는 예제를 보여 줍니다. 이 예제는 텍스트 Runbook에서 Twilio를 사용하여 텍스트 메시지를 보내는 위의 예제와 동일합니다. 이 예제에서는 서비스 인증에 연결 개체를 사용하는 **UseConnectionObject** 매개 변수 집합을 **Send-TwilioSMS** 활동에 사용합니다. Connection 매개 변수에는 단일 개체가 필요하기 때문에 여기에서는 [파이프라인 링크](automation-graphical-authoring-intro.md#links-and-workflow)를 사용합니다.

**To** 매개 변수 값에 상수 값 대신 PowerShell 식이 사용된 이유는 이 매개 변수에는 여러 번호로 보낼 수 있도록 문자열 배열 값 형식이 필요하기 때문입니다. PowerShell 식을 사용하면 단일 값 또는 배열을 제공할 수 있습니다.

![](media/automation-connections/get-connection-object.png)

아래 그림에서는 위와 동일한 예제를 보여 주지만 인증에 연결 개체를 사용할 때와 달리 AccountSid 및 AuthToken 매개 변수를 개별적으로 지정해야 하는 **SpecifyConnectionFields** 매개 변수 집합을 사용합니다. 이 경우 개체 자체 대신 연결의 필드가 지정됩니다.

![](media/automation-connections/get-connection-properties.png)



## 관련된 문서

- [그래픽 작성의 링크](automation-graphical-authoring-intro.md#links-and-workflow)
 

<!---HONumber=Nov15_HO1-->
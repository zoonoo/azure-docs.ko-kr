<properties 
   pageTitle="Azure 자동화의 인증서 자산 | Microsoft Azure"
   description="인증서는 Azure 자동화에 안전하게 저장되어 Azure 및 타사 리소스에 대해 인증하는 runbook과 DSC 구성에 액세스할 수 있게 합니다. 이 문서에서는 인증서에 대해 자세히 알아보고 텍스트 작성과 그래픽 작성 모두에서 인증서를 사용하는 방법을 설명합니다."
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
   ms.date="01/27/2016"
   ms.author="bwren" />

# Azure 자동화의 인증서 자산

인증서는 Azure 자동화에 안전하게 저장되어 runbook 또는 DSC 구성이 **Get-AutomationCertificate** 활동을 사용하여 액세스할 수 있습니다. 인증에 대한 인증서를 사용하는 runboo과 DSC 구성을 만들거나 Azure 또는 타사 리소스에 추가할 수 있게 해줍니다.

>[AZURE.NOTE] Azure 자동화의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 자동화 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure 자동화에 저장됩니다. 이 키는 마스터 인증서로 암호화되어 Azure 자동화에 저장됩니다. 자동화 계정에 대한 키는 보안 자산을 저장하기 전에 마스터 인증서를 사용하여 암호가 해독된 후 자산을 암호화하는 데 사용됩니다.

## Windows PowerShell cmdlet

다음 표의 cmdlet은 Windows PowerShell을 사용하여 자동화 인증서 자산을 만들고 관리하는 데 사용됩니다. 자동화 runbook과 DSC 구성에 사용할 수 있는 [Azure PowerShell 모듈](../powershell-install-configure.md)의 일부로 전송됩니다.

|Cmdlet|설명|
|:---|:---|
|[Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx)|인증서에 대한 정보를 검색합니다. Get-AutomationCertificate 활동에서는 인증서 자체만 검색할 수 있습니다.|
|[New- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913764.aspx)|Azure 자동화로 새 인증서를 가져옵니다.|
|[Remove- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913773.aspx)|Azure 자동화에서 인증서를 제거합니다.|
|[Set- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913763.aspx)|인증서 파일 업로드 및 .pfx 암호 설정을 포함하여 기존 인증서에 대한 속성을 설정합니다.|

## 인증서에 액세스하는 활동

다음 표의 활동은 runbook 또는 DSC 구성에 대 한 인증서에 액세스 하는 데 사용됩니다.

|활동|설명|
|:---|:---|
|Get-AutomationCertificate|Runbook 또는 DSC 구성에 사용할 인증서를 가져옵니다.|

>[AZURE.NOTE] Get-AutomationCertificat의 Name 매개변수에서는 변수를 사용하면 안 됩니다. runbook 또는 DSC 구성과 design time의 인증서 자산 간에 종속성이 발견되어 복잡해질 수 있기 때문입니다.

## 새 인증서 만들기

새 인증서를 만들 때 .cer 또는 .pfx 파일을 Azure 자동화에 업로드합니다. 인증서를 내보내기 가능한 것으로 표시한 경우 Azure 자동화 인증서 저장소 외부로 전송할 수 있습니다. 내보낼 수 없는 경우, runbook 또는 DSC 구성내 에서 사용할 수 있는 것만 서명합니다.

### Azure 클래식 포털을 사용하여 새 인증서를 만들려면

1. 자동화 계정에서 창의 위쪽에 있는 **자산**을 클릭합니다.
1. 창의 아래쪽의 **설정 추가**를 클릭합니다.
1. **자격 증명 추가**를 클릭합니다.
2. **자격 증명 형식** 드롭다운에서 **인증서**를 선택합니다.
3. **이름** 상자에 인증서 이름을 입력하고 오른쪽 화살표를 클릭합니다.
4. .cer 또는 .pfx 파일을 찾습니다. .pfx 파일을 선택한 경우 암호 및 내보내기 허용 여부를 지정합니다.
1. 인증서 파일 업로드 확인 표시를 클릭하고 새 인증서 자산을 저장합니다.


### Azure 포털을 사용하여 새 인증서를 만들려면

1. 자동화 계정에서 **자산** 파트를 클릭하여 **자산** 블레이드를 엽니다.
1. **인증서** 파트를 클릭하여 **인증서** 블레이드를 엽니다.
1. 블레이드의 위쪽에서 **인증서 추가**를 클릭합니다.
2. **이름** 상자에 인증서 이름을 입력합니다.
2. **인증서 파일 업로드** 아래에서 **파일 선택**을 클릭하여 .cer 또는 .pfx 파일을 찾습니다. .pfx 파일을 선택한 경우 암호 및 내보내기 허용 여부를 지정합니다.
1. **만들기**를 클릭하여 새 인증서 자산을 저장합니다.


### Windows PowerShell을 사용하여 새 인증서를 만들려면

다음 명령 예제에서는 새 자동화 인증서를 만들고 내보내기 가능한 것으로 표시하는 방법을 보여 줍니다. 이 예제에서는 기존 .pfx 파일을 가져옵니다.

	$certName = 'MyCertificate'
	$certPath = '.\MyCert.pfx'
	$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
	
	New-AzureAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable

## 인증서 사용

**Get-AutomationCertificate** 활동만 사용하여 인증서를 사용합니다. [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx) cmdlet은 인증서 자산에 대한 정보를 반환하지만 인증서 자체는 반환하지 않으므로 사용할 수 없습니다.

### 텍스트 Runbook 샘플

다음 샘플 코드에서는 Runbook에서 클라우드 서비스에 인증서를 추가하는 방법을 보여 줍니다. 이 샘플에서는 암호화된 자동화 변수에서 암호를 검색합니다.

	$serviceName = 'MyCloudService'
	$cert = Get-AutomationCertificate -Name 'MyCertificate'
	$certPwd = Get-AutomationVariable –Name 'MyCertPassword'
	Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### 그래픽 Runbook 샘플

그래픽 편집기의 라이브러리 창에서 인증서를 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택하여 **Get-AutomationCertificate**를 그래픽 Runbook에 추가합니다.

![](media/automation-certificates/certificate-add-canvas.png)

다음 그림에서는 그래픽 Runbook에서 인증서를 사용하는 예제를 보여 줍니다. 이 예제는 텍스트 Runbook에서 클라우드 서비스에 인증서를 추가하는 위의 예제와 동일합니다.

이 예제에서는 서비스 인증에 연결 개체를 사용하는 **UseConnectionObject** 매개 변수 집합을 **Send-TwilioSMS** 활동에 사용합니다. 여기에서는 [파이프라인 링크](automation-graphical-authoring-intro.md#links-and-workflow)를 사용해야 합니다. 시퀀스 링크는 Connection 매개 변수가 필요 없는 단일 개체를 포함하는 컬렉션을 반환하기 때문입니다.

![](media/automation-certificates/add-certificate.png)


## 참고 항목

- [그래픽 작성의 링크](automation-graphical-authoring-intro.md#links-and-workflow) 

<!---HONumber=AcomDC_0204_2016-->
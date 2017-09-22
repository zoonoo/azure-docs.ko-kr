---
title: "Azure 자동화의 인증서 자산 | Microsoft Docs"
description: "인증서를 Azure Automation에 안전하게 저장하게 저장할 수 있으며, 그러면 Runbook 또는 DSC 구성이 인증서에 액세스하여 Azure 및 타사 리소스에 인증할 수 있습니다.  이 문서에서는 인증서에 대해 자세히 알아보고 텍스트 작성과 그래픽 작성 모두에서 인증서를 사용하는 방법을 설명합니다."
services: automation
documentationcenter: 
author: mgoedtel
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 7082f0c4b1a4cf0f67da5254b4ebb019c7299683
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---

# <a name="certificate-assets-in-azure-automation"></a>Azure 자동화의 인증서 자산

인증서를 Azure Automation에 안전하게 저장하게 저장할 수 있으며, 그러면 Runbook 또는 DSC 구성이 Azure Resource Manager 리소스에 대한 **Get-AzureRmAutomationCertificate** 활동을 사용하여 인증서에 액세스할 수 있습니다. 그러면 인증을 위해 인증서를 사용하는 Runbook 및 DSC 구성을 만들거나 Azure 또는 타사 리소스에 추가할 수 있습니다.

> [!NOTE] 
> Azure 자동화의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 자동화 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure 자동화에 저장됩니다. 이 키는 마스터 인증서로 암호화되어 Azure 자동화에 저장됩니다. 자동화 계정에 대한 키는 보안 자산을 저장하기 전에 마스터 인증서를 사용하여 암호가 해독된 후 자산을 암호화하는 데 사용됩니다.
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell cmdlet

다음 표의 cmdlet은 Windows PowerShell을 사용하여 자동화 인증서 자산을 만들고 관리하는 데 사용됩니다. 자동화 runbook과 DSC 구성에 사용할 수 있는 [Azure PowerShell 모듈](../powershell-install-configure.md) 의 일부로 전송됩니다.

|Cmdlet|설명|
|:---|:---|
|[Get AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-4.3.1)|Runbook 또는 DSC 구성에 사용할 인증서 정보를 검색합니다. Get-AutomationCertificate 활동에서는 인증서 자체만 검색할 수 있습니다.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate?view=azurermps-4.3.1)|Azure Automation으로 새 인증서를 만듭니다.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate?view=azurermps-4.3.1)|Azure 자동화에서 인증서를 제거합니다.|Azure Automation으로 새 인증서를 만듭니다.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate?view=azurermps-4.3.1)|인증서 파일 업로드 및 .pfx 암호 설정을 포함하여 기존 인증서에 대한 속성을 설정합니다.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|지정된 클라우드 서비스에 대한 서비스 인증서를 업로드합니다.|


## <a name="python2-functions"></a>Python2 함수

다음 테이블의 함수는 Python2 Runbook의 인증서에 액세스하는 데 사용됩니다.

| 함수 | 설명 |
|:---|:---|
| automationassets.get_automation_certificate | 인증서 자산에 대한 정보를 검색합니다. |

> [!NOTE]
> 자산 함수에 액세스하려면 Python Runbook을 시작할 때 **automationassets** 모듈을 가져와야 합니다.


## <a name="creating-a-new-certificate"></a>새 인증서 만들기

새 인증서를 만들 때 .cer 또는 .pfx 파일을 Azure 자동화에 업로드합니다. 인증서를 내보내기 가능한 것으로 표시한 경우 Azure 자동화 인증서 저장소 외부로 전송할 수 있습니다. 내보낼 수 없는 경우, runbook 또는 DSC 구성내 에서 사용할 수 있는 것만 서명합니다.


### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Azure 포털을 사용하여 새 인증서를 만들려면

1. Automation 계정에서 **자산** 타일을 클릭하여 **자산** 블레이드를 엽니다.
1. **인증서** 타일을 클릭하여 **인증서** 블레이드를 엽니다.
1. 블레이드의 위쪽에서 **인증서 추가** 를 클릭합니다.
2. **이름** 상자에 인증서 이름을 입력합니다.
2. **인증서 파일 업로드** 아래에서 **파일 선택**을 클릭하여 .cer 또는 .pfx 파일을 찾습니다.  .pfx 파일을 선택한 경우 암호 및 내보내기 허용 여부를 지정합니다.
1. **만들기** 를 클릭하여 새 인증서 자산을 저장합니다.


### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Windows PowerShell을 사용하여 새 인증서를 만들려면

다음 예제에서는 새 Automation 인증서를 만들고 내보내기 가능한 것으로 표시하는 방법을 보여 줍니다. 이 예제에서는 기존 .pfx 파일을 가져옵니다.

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
    $ResourceGroup = "ResourceGroup01"
    
    New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup

## <a name="using-a-certificate"></a>인증서 사용

**Get-AutomationCertificate** 활동만 사용하여 인증서를 사용합니다. [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx) cmdlet은 인증서 자산에 대한 정보를 반환하지만 인증서 자체는 반환하지 않으므로 사용할 수 없습니다.

### <a name="textual-runbook-sample"></a>텍스트 Runbook 샘플

다음 샘플 코드에서는 Runbook에서 클라우드 서비스에 인증서를 추가하는 방법을 보여 줍니다. 이 샘플에서는 암호화된 자동화 변수에서 암호를 검색합니다.

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>그래픽 Runbook 샘플

그래픽 편집기의 라이브러리 창에서 인증서를 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택하여 **Get-AutomationCertificate**를 그래픽 Runbook에 추가합니다.

![캔버스에 인증서 추가](media/automation-certificates/automation-certificate-add-to-canvas.png)

다음 그림에서는 그래픽 Runbook에서 인증서를 사용하는 예제를 보여 줍니다.  이 예제는 텍스트 Runbook에서 클라우드 서비스에 인증서를 추가하는 위의 예제와 동일합니다.

![그래픽 작성 예제 ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2 샘플
다음 샘플은 Python2 Runbook의 인증서에 액세스하는 방법을 보여 줍니다.

    # get a reference to the Azure Automation certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    
    # returns the binary cert content  
    print cert 

## <a name="next-steps"></a>다음 단계

- Runbook이 수행하도록 설계된 활동의 논리적 흐름을 제어하도록 링크로 작업하는 방법에 대한 자세한 정보는 [그래픽 작성의 링크](automation-graphical-authoring-intro.md#links-and-workflow)를 참조하세요. 


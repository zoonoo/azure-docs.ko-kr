---
title: Azure 자동화에서 인증서 관리
description: 인증서는 Azure 자동화에 안전하게 저장되므로 Runbook 또는 DSC 구성에 액세스하여 Azure 및 타사 리소스에 대해 인증할 수 있습니다. 이 문서에서는 인증서에 대해 자세히 알아보고 텍스트 작성과 그래픽 작성 모두에서 인증서를 사용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732821"
---
# <a name="manage-certificates-in-azure-automation"></a>Azure 자동화에서 인증서 관리

인증서는 Azure 자동화에 안전하게 저장되므로 Azure 리소스 관리자 리소스에 대한 [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) 작업을 사용하여 Runbook 또는 DSC 구성에서 액세스할 수 있습니다. 보안 인증서 저장소를 사용하면 인증을 위해 인증서를 사용하는 Runbook 및 DSC 구성을 만들거나 Azure 또는 타사 리소스에 추가할 수 있습니다.

Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 Automation 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure Automation에 저장됩니다. 이 키는 시스템 관리 키 볼트에 저장됩니다. 보안 자산을 저장하기 전에 Key Vault에서 키가 로드된 다음, 자산을 암호화하는 데 사용됩니다. Azure Automation에서 이 프로세스를 관리합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](../automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="az-powershell-cmdlets"></a>아즈 파워쉘 cmdlet

Az의 경우 다음 표의 cmdlet은 Windows PowerShell을 사용하여 자동화 자격 증명 자산을 만들고 관리하는 데 사용됩니다. 자동화 런북 및 DSC 구성에 사용할 수 있는 [Az.Automation 모듈의](/powershell/azure/overview)일부로 제공됩니다.

|Cmdlet |설명|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|지정된 클라우드 서비스에 대한 서비스 인증서를 업로드합니다.|
|[Get-Az자동화 인증서](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Runbook 또는 DSC 구성에 사용할 인증서 정보를 검색합니다. 활동을 사용하여 인증서 자체만 `Get-AutomationCertificate` 검색할 수 있습니다.|
|[뉴 아즈자동화 인증서](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Azure 자동화에서 새 인증서를 만듭니다.|
|[제거-아즈자동화 인증서](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Azure Automation에서 인증서를 제거합니다.|
|[세트-아즈자동화 인증서](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|인증서 파일을 업로드하고 **.pfx** 파일에 대한 암호를 설정하는 등 기존 인증서의 속성을 설정합니다.|

## <a name="activities"></a>활동

다음 테이블의 활동은 runbook 또는 DSC 구성의 인증서에 액세스하는 데 사용됩니다.

| 활동 | 설명 |
|:---|:---|
|`Get-AutomationCertificate`|Runbook 또는 DSC 구성에 사용할 인증서를 가져옵니다. [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 개체를 반환합니다.|

> [!NOTE] 
> Runbook 또는 DSC `Name` 구성의 `Get-AutomationCertificate` 매개 변수에서 변수를 사용하지 않아야 합니다. 이 매개 변수에서 변수를 사용하면 디자인 시 런북 또는 DSC 구성 및 자동화 변수 간의 종속성 검색이 복잡해집니다.

## <a name="python-2-functions"></a>파이썬 2 함수

다음 표의 함수는 Python 2 Runbook의 인증서에 액세스하는 데 사용됩니다.

| 함수 | Description |
|:---|:---|
| `automationassets.get_automation_certificate` | 인증서 자산에 대한 정보를 검색합니다. |

> [!NOTE]
> 자산 함수에 `automationassets` 액세스하려면 Python Runbook의 시작 부분에서 모듈을 가져와야합니다.

## <a name="creating-a-new-certificate"></a>새 인증서 만들기

새 인증서를 만들 때 .cer 또는 .pfx 파일을 Azure Automation에 업로드합니다. 인증서를 내보내기 가능한 것으로 표시한 경우 Azure Automation 인증서 저장소 외부로 전송할 수 있습니다. 내보낼 수 없는 경우 Runbook 또는 DSC 구성 내에서 서명하는 데만 사용할 수 있습니다. Azure 자동화에는 인증서에 **공급자Microsoft 향상된 RSA 및 AES 암호화 공급자가**있어야 합니다.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Azure 포털을 사용하여 새 인증서 만들기

1. 자동화 계정에서 **자산을** 클릭하여 자산 페이지를 엽니다.
2. **인증서 페이지를** 열려면 인증서를 선택합니다.
3. 페이지 상단에서 **인증서 추가를** 클릭합니다.
4. **이름** 필드에 인증서의 이름을 입력합니다.
5. **.cer** 또는 **.pfx** 파일을 찾아보려면 **인증서 파일 업로드**에서 파일 **선택을** 클릭합니다. **.pfx**파일을 선택하는 경우 암호를 지정하고 내보낼 수 있는지 지정합니다.
6. **만들기** 를 클릭하여 새 인증서 자산을 저장합니다.

### <a name="create-a-new-certificate-with-powershell"></a>PowerShell을 사용하여 새 인증서 만들기

다음 예제에서는 새 Automation 인증서를 만들고 내보내기 가능한 것으로 표시하는 방법을 보여 줍니다. 이 예제는 기존 **.pfx** 파일을 가져옵니다.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 새 인증서 만들기

다음 예제에서는 PowerShell을 통해 리소스 관리자 템플릿을 사용하여 자동화 계정에 인증서를 배포하는 방법을 보여 줍니다.

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>인증서 사용

인증서를 사용하려면 `Get-AutomationCertificate` 활동을 사용합니다. 인증서 자산에 대한 정보를 반환하지만 인증서 자체는 반환하지 않으므로 [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) cmdlet을 사용할 수 없습니다.

### <a name="textual-runbook-example"></a>텍스트 런북 예제

다음 예제에서는 Runbook에서 클라우드 서비스에 인증서를 추가하는 방법을 보여 주며 있습니다. 이 샘플에서는 암호화된 자동화 변수에서 암호를 검색합니다.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>그래픽 런북 예제

라이브러리 `Get-AutomationCertificate` 창의 인증서를 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가를**선택하여 그래픽 런북에 활동을 추가합니다.

![캔버스에 인증서 추가](../media/certificates/automation-certificate-add-to-canvas.png)

다음 그림에서는 그래픽 Runbook에서 인증서를 사용하는 예제를 보여 줍니다. 이는 텍스트 Runbook에서 클라우드 서비스에 인증서를 추가하는 방법을 보여 주는 앞의 예제와 동일합니다.

![그래픽 작성 예제](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>파이썬 2 예제

다음 예제에서는 Python2 Runbook에서 인증서에 액세스하는 방법을 보여 주습니다.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>다음 단계

- Runbook에서 수행하는 활동의 논리적 흐름을 제어하기 위해 링크로 작업하는 방법에 대해 자세히 알아보려면 [그래픽 작성의 링크를](../automation-graphical-authoring-intro.md#links-and-workflow)참조하십시오. 

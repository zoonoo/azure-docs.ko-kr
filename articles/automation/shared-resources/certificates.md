---
title: Azure Automation에서 인증서 관리
description: Runbook 또는 DSC 구성에서 Azure 및 타사 리소스에 대해 인증 하기 위해 액세스할 수 있도록 인증서가 Azure Automation에 안전 하 게 저장 됩니다. 이 문서에서는 인증서에 대해 자세히 알아보고 텍스트 작성과 그래픽 작성 모두에서 인증서를 사용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732821"
---
# <a name="manage-certificates-in-azure-automation"></a>Azure Automation에서 인증서 관리

인증서는 Azure Resource Manager 리소스에 대해 [AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) 활동을 사용 하 여 RUNBOOK 또는 DSC 구성에서 액세스할 수 있도록 Azure Automation에 안전 하 게 저장 됩니다. 보안 인증서 저장소를 사용 하면 인증을 위해 인증서를 사용 하는 runbook 및 DSC 구성을 만들거나 Azure 또는 타사 리소스에 추가할 수 있습니다.

Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 Automation 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure Automation에 저장됩니다. 이 키는 시스템에서 관리 하는 Key Vault에 저장 됩니다. 보안 자산을 저장하기 전에 Key Vault에서 키가 로드된 다음, 자산을 암호화하는 데 사용됩니다. Azure Automation에서 이 프로세스를 관리합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](../automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="az-powershell-cmdlets"></a>Az PowerShell cmdlet

Az의 경우 다음 표의 cmdlet은 Windows PowerShell을 사용 하 여 자동화 자격 증명 자산을 만들고 관리 하는 데 사용 됩니다. Automation runbook 및 DSC 구성에서 사용할 수 있는 [Az. automation 모듈](/powershell/azure/overview)의 일부로 제공 됩니다.

|Cmdlet |Description|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|지정된 클라우드 서비스에 대한 서비스 인증서를 업로드합니다.|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Runbook 또는 DSC 구성에 사용할 인증서 정보를 검색합니다. 활동을 `Get-AutomationCertificate` 사용 하 여 인증서 자체를 검색할 수 있습니다.|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Azure Automation에서 새 인증서를 만듭니다.|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Azure Automation에서 인증서를 제거합니다.|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|인증서 파일 업로드 및 **.pfx** 파일에 대 한 암호 설정을 비롯 하 여 기존 인증서에 대 한 속성을 설정 합니다.|

## <a name="activities"></a>활동

다음 테이블의 활동은 runbook 또는 DSC 구성의 인증서에 액세스하는 데 사용됩니다.

| 활동 | Description |
|:---|:---|
|`Get-AutomationCertificate`|Runbook 또는 DSC 구성에 사용할 인증서를 가져옵니다. [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 개체를 반환합니다.|

> [!NOTE] 
> Runbook 또는 DSC 구성의 `Name` 매개 변수 `Get-AutomationCertificate` 에서 변수를 사용 하지 않아야 합니다. 이 매개 변수에서 변수를 사용 하면 디자인 타임에 runbook 또는 DSC 구성과 자동화 변수 간의 종속성을 검색 하는 작업이 복잡해 집니다.

## <a name="python-2-functions"></a>Python 2 함수

다음 표의 함수는 Python 2 runbook의 인증서에 액세스 하는 데 사용 됩니다.

| 함수 | Description |
|:---|:---|
| `automationassets.get_automation_certificate` | 인증서 자산에 대한 정보를 검색합니다. |

> [!NOTE]
> 자산 기능에 액세스 `automationassets` 하려면 Python runbook의 시작 부분에 있는 모듈을 가져와야 합니다.

## <a name="creating-a-new-certificate"></a>새 인증서 만들기

새 인증서를 만들 때 .cer 또는 .pfx 파일을 Azure Automation에 업로드합니다. 인증서를 내보내기 가능한 것으로 표시한 경우 Azure Automation 인증서 저장소 외부로 전송할 수 있습니다. 내보낼 수 없는 경우 runbook 또는 DSC 구성 내에서 서명 하는 데만 사용할 수 있습니다. Azure Automation 하려면 인증서에 **Microsoft 고급 RSA 및 AES 암호화 공급자**를 제공 해야 합니다.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Azure Portal를 사용 하 여 새 인증서 만들기

1. Automation 계정에서 **자산** 을 클릭 하 여 자산 페이지를 엽니다.
2. 인증서 **를 선택 하** 여 인증서 페이지를 엽니다.
3. 페이지 맨 위에서 **인증서 추가** 를 클릭 합니다.
4. **이름** 필드에 인증서의 이름을 입력 합니다.
5. **.Cer** 또는 **.pfx** 파일을 찾아보려면 **인증서 파일 업로드**아래에서 **파일 선택** 을 클릭 합니다. **.Pfx**파일을 선택 하는 경우 암호를 지정 하 고 내보낼 수 있는지 여부를 지정 합니다.
6. **만들기** 를 클릭하여 새 인증서 자산을 저장합니다.

### <a name="create-a-new-certificate-with-powershell"></a>PowerShell을 사용 하 여 새 인증서 만들기

다음 예제에서는 새 Automation 인증서를 만들고 내보내기 가능한 것으로 표시하는 방법을 보여 줍니다. 이 예제에서는 기존 **.pfx** 파일을 가져옵니다.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 새 인증서 만들기

다음 예제에서는 PowerShell을 통해 리소스 관리자 템플릿을 사용 하 여 Automation 계정에 인증서를 배포 하는 방법을 보여 줍니다.

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

인증서를 사용 하려면 `Get-AutomationCertificate` 활동을 사용 합니다. [AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) cmdlet은 인증서 자산에 대 한 정보를 반환 하지만 인증서 자체는 반환 하지 않으므로 사용할 수 없습니다.

### <a name="textual-runbook-example"></a>텍스트 runbook 예

다음 예제에서는 runbook에서 클라우드 서비스에 인증서를 추가 하는 방법을 보여 줍니다. 이 샘플에서는 암호화된 자동화 변수에서 암호를 검색합니다.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>그래픽 runbook 예

라이브러리 창 `Get-AutomationCertificate` 에서 인증서를 마우스 오른쪽 단추로 클릭 하 고 **캔버스에 추가**를 선택 하 여 그래픽 runbook에 활동을 추가 합니다.

![캔버스에 인증서 추가](../media/certificates/automation-certificate-add-to-canvas.png)

다음 그림에서는 그래픽 Runbook에서 인증서를 사용하는 예제를 보여 줍니다. 이는 텍스트 runbook에서 클라우드 서비스에 인증서를 추가 하는 방법을 보여주는 앞의 예제와 동일 합니다.

![그래픽 작성 예제](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2 예제

다음 예제에서는 Python2 runbook의 인증서에 액세스 하는 방법을 보여 줍니다.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>다음 단계

- Runbook에서 수행 하는 작업의 논리적 흐름을 제어 하기 위한 링크를 사용 하는 방법에 대 한 자세한 내용은 [그래픽 작성 링크](../automation-graphical-authoring-intro.md#links-and-workflow)를 참조 하세요. 

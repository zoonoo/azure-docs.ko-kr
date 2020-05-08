---
title: Azure Automation에서 인증서 관리
description: Runbook 또는 DSC 구성에서 Azure 및 타사 리소스에 대해 인증 하기 위해 액세스할 수 있도록 인증서를 안전 하 게 저장 Azure Automation. 이 문서에서는 인증서에 대 한 세부 정보 및 텍스트와 그래픽 작성 모두에서 인증서를 사용 하는 방법을 설명 합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2793679fb4588d00ea4e37340b19183398cb9d90
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864320"
---
# <a name="manage-certificates-in-azure-automation"></a>Azure Automation에서 인증서 관리

Azure Automation는 Azure Resource Manager 리소스에 대해 [AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) cmdlet을 사용 하 여 RUNBOOK 및 DSC 구성에서 액세스 하기 위해 인증서를 안전 하 게 저장 합니다. 보안 인증서 저장소를 사용 하면 인증을 위해 인증서를 사용 하는 runbook 및 DSC 구성을 만들거나 Azure 또는 타사 리소스에 추가할 수 있습니다.

>[!NOTE]
>Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 Automation 계정에 대해 생성 되는 고유 키를 사용 하 여 암호화 되 고 자동화에 저장 됩니다. Automation은 시스템 관리 Key Vault 서비스에 키를 저장 합니다. 보안 자산을 저장 하기 전에 Automation은 Key Vault에서 키를 로드 한 다음이를 사용 하 여 자산을 암호화 합니다. 

>[!NOTE]
>이 문서에서는 Azure PowerShell Az module을 사용 하는 방법을 보여 줍니다. AzureRM 모듈을 계속 사용할 수 있습니다. Az module 및 AzureRM compatibility에 대해 자세히 알아보려면 [새 Azure PowerShell Az Module 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조 하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](../automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="powershell-cmdlets-to-access-certificates"></a>인증서에 액세스 하기 위한 PowerShell cmdlet

다음 표의 cmdlet은 PowerShell을 사용 하 여 자동화 인증서를 만들고 관리 합니다. [Az 모듈](modules.md#az-modules)의 일부로 제공 됩니다.

|Cmdlet |Description|
| --- | ---|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Runbook 또는 DSC 구성에 사용할 인증서 정보를 검색합니다. 내부 `Get-AutomationCertificate` cmdlet을 사용 하 여 인증서 자체를 검색할 수 있습니다.|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Automation에서 새 인증서를 만듭니다.|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Automation에서 인증서를 제거 합니다.|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|인증서 파일 업로드 및 **.pfx** 파일에 대 한 암호 설정을 비롯 하 여 기존 인증서에 대 한 속성을 설정 합니다.|

[AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate) cmdlet을 사용 하 여 지정 된 클라우드 서비스에 대 한 서비스 인증서를 업로드할 수도 있습니다.

## <a name="internal-cmdlets-to-access-certificates"></a>인증서에 액세스 하는 내부 cmdlet

다음 표의 내부 cmdlet은 runbook의 인증서에 액세스 하는 데 사용 됩니다. 이 cmdlet은 전역 모듈과 `Orchestrator.AssetManagement.Cmdlets`함께 제공 됩니다. 자세한 내용은 [내부 cmdlet](modules.md#internal-cmdlets)을 참조 하세요.

| 내부 Cmdlet | Description |
|:---|:---|
|`Get-AutomationCertificate`|Runbook 또는 DSC 구성에 사용할 인증서를 가져옵니다. [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 개체를 반환합니다.|

> [!NOTE] 
> Runbook 또는 DSC 구성의 `Name` 매개 변수 `Get-AutomationCertificate` 에서 변수를 사용 하지 않아야 합니다. 이러한 변수는 디자인 타임에 runbook 또는 DSC 구성과 자동화 변수 간의 종속성을 검색 하는 것이 복잡할 수 있습니다.

## <a name="python-2-functions-to-access-certificates"></a>인증서에 액세스 하는 Python 2 함수

다음 표의 함수를 사용 하 여 Python 2 runbook의 인증서에 액세스할 수 있습니다.

| 함수 | Description |
|:---|:---|
| `automationassets.get_automation_certificate` | 인증서 자산에 대한 정보를 검색합니다. |

> [!NOTE]
> 자산 기능에 액세스 `automationassets` 하려면 Python runbook의 시작 부분에 있는 모듈을 가져와야 합니다.

## <a name="create-a-new-certificate"></a>새 인증서 만들기

새 인증서를 만들 때 .cer 또는 .pfx 파일을 Automation에 업로드 합니다. 인증서를 내보낼 수 있는 것으로 표시 하면 자동화 인증서 저장소에서 인증서를 전송할 수 있습니다. 내보낼 수 없는 경우 runbook 또는 DSC 구성 내에서 서명 하는 데만 사용할 수 있습니다. Automation을 사용 하려면 인증서에 **Microsoft 고급 RSA 및 AES 암호화 공급자**를 제공 해야 합니다.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Azure Portal를 사용 하 여 새 인증서 만들기

1. Automation 계정에서 **자산** > **인증서** > **인증서 추가**를 선택 합니다.
1. **이름** 필드에 인증서의 이름을 입력 합니다.
1. **.Cer** 또는 **.pfx** 파일을 찾아보려면 **인증서 파일 업로드**아래에서 **파일 선택**을 선택 합니다. **.Pfx** 파일을 선택 하는 경우 암호를 지정 하 고 내보낼 수 있는지 여부를 지정 합니다.
1. **만들기** 를 선택 하 여 새 인증서 자산을 저장 합니다.

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

## <a name="get-a-certificate"></a>인증서 얻기

인증서를 검색 하려면 내부 `Get-AutomationCertificate` cmdlet을 사용 합니다. [AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) cmdlet은 인증서 자산에 대 한 정보를 반환 하지만 인증서 자체는 반환 하지 않으므로 사용할 수 없습니다.

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

라이브러리 창에서 인증서를 마우스 `Get-AutomationCertificate` 오른쪽 단추로 클릭 하 고 **캔버스에 추가**를 선택 하 여 내부 cmdlet에 대 한 활동을 그래픽 runbook에 추가 합니다.

![캔버스에 인증서를 추가 하는 스크린샷](../media/certificates/automation-certificate-add-to-canvas.png)

다음 그림에서는 그래픽 Runbook에서 인증서를 사용하는 예제를 보여 줍니다. 

![그래픽 작성 예제의 스크린샷](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2 예제

다음 예제에서는 Python 2 runbook의 인증서에 액세스 하는 방법을 보여 줍니다.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>다음 단계

* 인증서에 액세스 하는 데 사용 되는 cmdlet에 대 한 자세한 내용은 [Azure Automation 모듈 관리](modules.md)를 참조 하세요.
* Runbook에 대 한 일반 정보는 [Azure Automation에서 runbook 실행](../automation-runbook-execution.md)을 참조 하세요.
* DSC 구성에 대 한 자세한 내용은 [상태 구성 개요](../automation-dsc-overview.md)를 참조 하세요. 

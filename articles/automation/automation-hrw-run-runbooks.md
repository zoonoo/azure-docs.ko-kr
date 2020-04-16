---
title: Azure Automation Hybrid Runbook Worker에서 Runbook 실행
description: 이 문서에서는 Hybrid Runbook Worker 역할이 있는 로컬 데이터 센터 또는 클라우드 공급자의 컴퓨터에서 Runbook을 실행하는 방법에 대한 정보를 제공합니다.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: b65c72e0c65cf9aa84cb614478fbdf78258f3054
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405819"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 Runbook 실행

하이브리드 Runbook 워커를 대상으로 하는 Runbook은 일반적으로 로컬 컴퓨터또는 작업자가 배포된 로컬 환경의 리소스에 대해 리소스를 관리합니다. Azure Automation의 Runbook은 일반적으로 Azure 클라우드에서 리소스를 관리합니다. 다르게 사용되지만 Azure 자동화에서 실행되는 Runbook 및 하이브리드 Runbook 워커에서 실행되는 Runbook은 구조에서 동일합니다.

하이브리드 Runbook 워커에서 실행할 Runbook을 작성할 때는 작업자를 호스팅하는 컴퓨터에서 Runbook을 편집하고 테스트해야 합니다. 호스트 컴퓨터에는 로컬 리소스를 관리하고 액세스하는 데 필요한 모든 PowerShell 모듈과 네트워크 액세스가 있습니다. 하이브리드 Runbook 작업자 컴퓨터에서 Runbook을 테스트한 후 Azure 자동화 환경에 업로드하여 작업자에서 실행할 수 있습니다. 

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>하이브리드 Runbook 작업자에 대한 Runbook 권한

Azure 이외의 리소스에 액세스하는 경우 하이브리드 Runbook Worker에서 실행되는 Runbook은 일반적으로 Azure 리소스에 인증하는 Runbook에서 사용하는 인증 메커니즘을 사용할 수 없습니다. Runbook은 로컬 리소스에 자체 인증을 제공하거나 [Azure 리소스에 대해 관리되는 ID를](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)사용하여 인증을 구성합니다. 모든 Runbook에 대한 사용자 컨텍스트를 제공하기 위해 Run As 계정을 지정할 수도 있습니다.

### <a name="runbook-authentication"></a>Runbook 인증

기본적으로 Runbook은 로컬 컴퓨터에서 실행됩니다. Windows의 경우 로컬 **시스템** 계정의 컨텍스트에서 실행됩니다. 리눅스의 경우, 그들은 특별한 사용자 계정 **nxautomation의**맥락에서 실행됩니다. 두 시나리오 모두 Runbook은 액세스하는 리소스에 자체 인증을 제공해야 합니다.

runbook에서 [다른](automation-credentials.md) 리소스에 대해 인증할 수 있도록 자격 증명을 지정할 수 있는 cmdlet을 사용하여 Runbook에서 자격 증명 및 [인증서](automation-certificates.md) 자산을 사용할 수 있습니다. 다음 예제에서는 컴퓨터를 다시 시작하는 Runbook의 일부를 보여 줍니다. 자격 증명 자산과 가변 자산에서 컴퓨터 이름에서 자격 증명을 검색한 `Restart-Computer` 다음 cmdlet과 함께 이러한 값을 사용합니다.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

[인라인스크립트](automation-powershell-workflow.md#inlinescript) 활동을 사용할 수도 있습니다. `InlineScript`[PSCredential 공통 매개 변수에](/powershell/module/psworkflow/about/about_workflowcommonparameters)의해 지정된 자격 증명을 사용하여 다른 컴퓨터에서 코드 블록을 실행할 수 있습니다.

### <a name="run-as-account"></a>실행 계정

Runbook이 로컬 리소스에 자체 인증을 제공하는 대신 하이브리드 Runbook Worker 그룹에 대해 Run As 계정을 지정할 수 있습니다. 이렇게 하려면 로컬 리소스에 액세스할 수 있는 [자격 증명 자산을](automation-credentials.md) 정의해야 합니다. 이러한 리소스에는 인증서 저장소와 그룹의 하이브리드 Runbook 워커에서 이러한 자격 증명으로 실행되는 모든 Runbook이 포함됩니다.

자격 증명에 대한 사용자 이름은 다음 서식 중 하나여야 합니다.

* domain\username
* username@domain
* 사용자 이름(온-프레미스 컴퓨터에 로컬인 계정용)

다음 절차를 사용하여 하이브리드 Runbook 작업자 그룹에 대해 Run As 계정을 지정합니다.

1. 로컬 리소스에 대한 액세스로 [자격 증명 자산](automation-credentials.md)을 만듭니다.
2. Azure Portal에서 Automation 계정을 엽니다.
3. **Hybrid Worker 그룹** 타일을 선택한 다음 그룹을 선택합니다.
4. 하이브리드 작업자 그룹 **설정**다음에 모든 **설정을**선택합니다.
5. **실행** 값을 **기본값에서** **사용자 지정으로**변경합니다.
6. 자격 증명을 선택하고 **저장**을 클릭합니다.

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리되는 ID

Azure 가상 시스템의 하이브리드 Runbook 작업자는 Azure 리소스에 대해 관리되는 ID를 사용하여 Azure 리소스에 인증할 수 있습니다. Run As 계정 대신 Azure 리소스에 대해 관리되는 ID를 사용하면 다음을 수행할 필요가 없으므로 이점이 제공됩니다.

* 실행 인증서를 내보낸 다음 하이브리드 Runbook 작업자로 가져옵니다.
* Run As 계정에서 사용하는 인증서를 갱신합니다.
* Runbook 코드에서 연결로 실행 개체를 처리합니다.

하이브리드 Runbook 작업자에서 Azure 리소스에 대해 관리되는 ID를 사용하려면 다음 단계를 따릅니다.

1. Azure VM을 만듭니다.
2. VM에서 Azure 리소스에 대해 관리되는 ID를 구성합니다. Azure [포털을 사용하여 VM에서 Azure 리소스에 대해 관리되는 ID 구성을](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)참조하십시오.
3. 리소스 관리자의 리소스 그룹에 대한 VM 액세스 권한을 부여합니다. 리소스 [관리자에 액세스하려면 Windows VM 시스템에서 할당된 관리되는 ID 사용 을](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)참조하십시오.
4. VM에 하이브리드 Runbook 작업자를 설치합니다. [Windows 하이브리드 Runbook 작업자 배포를](automation-windows-hrw-install.md)참조하십시오.
5. `Identity` Runbook을 업데이트하여 매개 변수가 있는 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet을 사용하여 Azure 리소스를 인증합니다. 이 구성은 Run As 계정을 사용하고 관련 계정 관리를 수행할 필요가 줄어듭니다.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`시스템 할당 된 ID와 단일 사용자 할당 된 ID를 사용 하 여 하이브리드 Runbook 워커에 대 한 작동 합니다. 하이브리드 Runbook Worker에서 여러 사용자 할당 ID를 사용하는 경우 `AccountId` Runbook에서 특정 사용자 할당 ID를 선택할 `Connect-AzAccount` 매개 변수를 지정해야 합니다.

### <a name="automation-run-as-account"></a><a name="runas-script"></a>Automation 실행 계정

Azure에서 리소스를 배포하기 위한 자동화된 빌드 프로세스의 일부로 배포 순서의 작업 또는 단계 집합을 지원하기 위해 온-프레미스 시스템에 액세스해야 할 수 있습니다. Run As 계정을 사용하여 Azure에 대한 인증을 제공하려면 계정으로 실행 인증서를 설치해야 합니다.

다음 PowerShell 실행 북이라고 하는 **내보내기-RunAsCertificateToHybridWorker는**Azure 자동화 계정에서 인증서로 실행을 내보냅니다. Runbook은 동일한 계정에 연결된 하이브리드 Runbook 작업자의 로컬 컴퓨터 인증서 저장소로 인증서를 다운로드하고 가져오습니다. 이 단계가 완료되면 Runbook은 작업자가 Run As 계정을 사용하여 Azure를 성공적으로 인증할 수 있음을 확인합니다.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>PowerShell `Add-AzAccount` 런북의 `Add-AzureRMAccount` 경우 에 대한 `Connect-AzAccount`별칭입니다. 라이브러리 항목을 검색할 때 볼 `Connect-AzAccount`수 없는 `Add-AzAccount`경우 을 사용하거나 자동화 계정에서 모듈을 업데이트할 수 있습니다.

계정으로 실행 준비를 완료하려면 다음을 수행합니다.

1. **.ps1** 확장을 사용하여 **내보내기-RunAsCertificateToHybridWorker** 실행책을 컴퓨터에 저장합니다.
2. 자동화 계정으로 가져옵니다.
3. Runbook을 편집하여 `Password` 변수 값을 자신의 암호로 변경합니다. 
4. Runbook을 게시합니다.
5. Runbook을 실행하여 Run As 계정을 사용하여 Runbook을 실행하고 인증하는 하이브리드 Runbook 작업자 그룹을 대상으로 실행합니다. 
6. 작업 스트림을 검사하여 인증서를 로컬 컴퓨터 저장소로 가져오려는 시도를 보고하고 여러 줄로 따르는지 확인합니다. 이 동작은 구독에서 정의하는 자동화 계정 수와 인증 성공 정도에 따라 달라집니다.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>하이브리드 Runbook 작업자의 작업 동작

Azure 자동화는 하이브리드 Runbook 작업자의 작업을 Azure 샌드박스에서 실행되는 작업과 다소 다르게 처리합니다. 한 가지 중요한 차이점은 Runbook 작업자의 작업 기간에 제한이 없다는 것입니다. Azure 샌드박스에서 실행되는 Runbook은 [공정한 공유로](automation-runbook-execution.md#fair-share)인해 3시간으로 제한됩니다.

장기 실행 Runbook의 경우 작업자를 호스팅하는 컴퓨터가 재부팅되는 경우와 같은 가능한 다시 시작에 탄력적인지 확인해야 합니다. 하이브리드 Runbook Worker 호스트 컴퓨터가 재부팅되면 실행 중인 Runbook 작업이 처음부터 또는 PowerShell 워크플로 워크플로 런북의 마지막 검사점에서 다시 시작됩니다. Runbook 작업이 세 번 이상 다시 시작되면 일시 중단됩니다.

하이브리드 Runbook 작업자에 대한 작업은 Windows의 로컬 시스템 계정 또는 Linux의 **nxautomation** 계정에서 실행됩니다. Linux의 경우 **nxautomation** 계정이 Runbook 모듈이 저장된 위치에 액세스할 수 있는지 확인해야 합니다. [설치 모듈](/powershell/module/powershellget/install-module) cmdlet을 사용하는 경우 **nxautomation** 계정에 `Scope` 액세스 권한이 있는지 확인하기 위해 매개 변수에 대해 AllUsers를 지정해야 합니다. Linux의 PowerShell에 대한 자세한 내용은 [Windows 가 아닌 플랫폼에서 PowerShell에 대한 알려진 문제를](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)참조하십시오.

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>하이브리드 Runbook 작업자에서 Runbook 시작

[Azure 자동화에서 Runbook을 시작하면](automation-starting-a-runbook.md) Runbook을 시작하는 다양한 방법을 설명합니다. 하이브리드 Runbook 작업자에서 Runbook에 대한 시작은 하이브리드 Runbook 작업자 그룹의 이름을 지정할 수 있는 **실행 옵션을** 사용합니다. 그룹이 지정되면 해당 그룹의 작업자 중 하나가 Runbook을 검색하고 실행합니다. Runbook에서 이 옵션을 지정하지 않으면 Azure Automation은 평소와 같이 Runbook을 실행합니다.

Azure 포털에서 Runbook을 시작하면 **Azure** 또는 **하이브리드 워커를**선택할 수 있는 **실행 옵션옵션이** 표시됩니다. **하이브리드 워커를**선택하는 경우 드롭다운에서 하이브리드 Runbook 작업자 그룹을 선택할 수 있습니다.

`RunOn` [시작-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) cmdlet와 함께 매개 변수를 사용합니다. 다음 예제에서는 Windows PowerShell을 사용하여 MyHybridGroup이라는 하이브리드 Runbook 작업자 그룹에서 **테스트-Runbook이라는 런북을** 시작합니다.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> 이전 버전이 설치되어 있는 경우 [최신 PowerShell 버전을 다운로드해야](https://azure.microsoft.com/downloads/) 합니다. PowerShell에서 Runbook을 시작하는 워크스테이션에만 이 버전을 설치합니다. 이 컴퓨터에서 Runbook을 시작하려는 경우가 아니면 하이브리드 Runbook Worker 컴퓨터에 설치할 필요가 없습니다.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Windows 하이브리드 Runbook 작업자에서 서명된 Runbook 작업

서명된 Runbook만 실행하도록 Windows 하이브리드 Runbook 워커를 구성할 수 있습니다.

> [!IMPORTANT]
> 서명된 Runbook만 실행하도록 하Hybrid Runbook Worker를 구성하고 나면, 서명되지 않은 Runbook은 Worker에서 실행되지 않습니다.

### <a name="create-signing-certificate"></a>서명 인증서 만들기

다음 예제에서는 Runbook에 서명하는 데 사용할 수 있는 자체 서명된 인증서를 만듭니다. 이 코드는 인증서를 만들고 하이브리드 Runbook 작업자가 나중에 가져올 수 있도록 인증서를 내보냅니다. 지문은 나중에 인증서를 참조할 때 사용할 수 있습니다.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>인증서 가져오기 및 서명 유효성 검사를 위해 작업자 구성

그룹의 각 하이브리드 Runbook 작업자에게 만든 인증서를 복사합니다. 다음 스크립트를 실행하여 인증서를 가져오고 Runbook에서 서명 유효성 검사를 사용하도록 작업자를 구성합니다.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>인증서를 사용하여 Runbook에 서명

서명된 Runbook 만 사용하도록 구성된 하이브리드 Runbook 작업자를 사용하면 하이브리드 Runbook 작업자에서 사용할 런북에 서명해야 합니다. 다음 샘플 PowerShell 코드를 사용하여 이러한 Runbook에 서명합니다.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Runbook에 서명하면 자동화 계정으로 가져와 서명 블록으로 게시해야 합니다. Runbook을 가져오는 방법에 대한 자세한 내용은 [파일의 Runbook을 Azure Automation으로 가져오기](manage-runbooks.md#importing-a-runbook)를 참조하세요.

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Linux 하이브리드 Runbook 작업자에서 서명된 Runbook 작업

서명된 Runbook으로 작업하려면 Linux 하이브리드 Runbook 작업자가 로컬 컴퓨터에서 [GPG](https://gnupg.org/index.html) 실행 가능한 실행 이 있어야 합니다.

> [!IMPORTANT]
> 서명된 Runbook만 실행하도록 하Hybrid Runbook Worker를 구성하고 나면, 서명되지 않은 Runbook은 Worker에서 실행되지 않습니다.

### <a name="create-a-gpg-keyring-and-keypair"></a>GPG 인증 키 및 키 쌍 만들기

GPG 키링 및 키 쌍을 만들려면 하이브리드 Runbook Worker **nxautomation** 계정을 사용합니다.

1. sudo 응용 프로그램을 사용하여 **nxautomation** 계정으로 로그인합니다.

    ```bash
    sudo su – nxautomation
    ```

2. **nxautomation을**사용중이면 GPG 키쌍을 생성합니다. GPG는 단계를 안내합니다. 이름, 이메일 주소, 만료 시간 및 암호를 제공해야 합니다. 그런 다음 키가 생성될 수 있도록 컴퓨터에 충분한 엔트로피가 있을 때까지 기다립니다.

    ```bash
    sudo gpg --generate-key
    ```

3. GPG 디렉터리 sudo를 사용 하 여 생성 되었습니다 때문에 다음 명령을 사용 하 여 **nxautomation에** 소유자를 변경 해야 합니다.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>하이브리드 Runbook 작업자가 키링을 사용할 수 있도록 합니다.

키링을 만든 후에는 하이브리드 Runbook 작업자가 키링을 사용할 수 있도록 합니다. 파일 섹션 `[worker-optional]`아래에 다음 예제 코드를 포함하도록 설정 파일 **/var/opt/microsoft/omsagent/상태/자동화 작업자/diy/worker.conf를** 수정합니다.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>서명 유효성 검사가 켜고 있는지 확인합니다.

컴퓨터에서 서명 유효성 검사를 사용하지 않도록 설정한 경우 다음 sudo 명령을 실행하여 서명 유효성 검사를 켜야 합니다. 작업 `<LogAnalyticsworkspaceId>` 영역 ID로 바꿉습니다.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Runbook 서명

서명 유효성 검사를 구성한 후에는 다음 GPG 명령을 사용하여 Runbook에 서명합니다.

```bash
gpg –-clear-sign <runbook name>
```

서명된 Runbook을 ** <runbook name>.asc라고 합니다.**

이제 서명된 Runbook을 Azure Automation에 업로드하고 일반 Runbook처럼 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Runbook을 시작하는 방법에 대한 자세한 내용은 [Azure Automation에서 Runbook 시작을](automation-starting-a-runbook.md)참조하십시오.
* 텍스트 편집기를 사용하여 Azure 자동화에서 PowerShell Runbook을 사용하는 방법을 이해하려면 [Azure Automation에서 Runbook 편집을](automation-edit-textual-runbook.md)참조하십시오.
* Runbook이 성공적으로 완료되지 않은 경우 Runbook 실행 실패에 대한 문제 해결 [가이드를 검토하십시오.](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)
* 언어 참조 및 학습 모듈을 포함한 [PowerShell에](https://docs.microsoft.com/powershell/scripting/overview)대한 자세한 내용은 PowerShell 문서를 참조하십시오.
* PowerShell cmdlet 참조는 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)을 참조하십시오.

---
title: Azure Automation Hybrid Runbook Worker에서 Runbook 실행
description: 이 문서에서는 Hybrid Runbook Worker 사용 하 여 로컬 데이터 센터 또는 클라우드 공급자의 컴퓨터에서 runbook을 실행 하는 방법에 대 한 정보를 제공 합니다.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: a86139c7becaae996e343166088b416dd8d6404f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855639"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 Runbook 실행

Hybrid Runbook Worker를 대상으로 하는 runbook은 일반적으로 로컬 컴퓨터의 리소스나 작업자를 배포한 로컬 환경의 리소스를 관리 합니다. Azure Automation의 Runbook은 일반적으로 Azure 클라우드에서 리소스를 관리합니다. 다르게 사용 되는 경우에도 Azure Automation에서 실행 되는 runbook과 Hybrid Runbook Worker에서 실행 되는 runbook은 구조에서 동일 합니다.

Hybrid Runbook Worker에서 실행할 runbook을 작성 하는 경우 작업자를 호스트 하는 컴퓨터에서 runbook을 편집 하 고 테스트 해야 합니다. 호스트 컴퓨터에는 로컬 리소스를 관리 하 고 액세스 하는 데 필요한 모든 PowerShell 모듈 및 네트워크 액세스 권한이 있습니다. Hybrid Runbook Worker 컴퓨터에서 runbook을 테스트 한 후에는이를 작업자에서 실행할 수 있는 Azure Automation 환경에 업로드할 수 있습니다. 

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="plan-runbook-job-behavior"></a>Runbook 작업 동작 계획

Azure Automation는 Azure 샌드박스에서 실행 되는 작업과 약간 다른 방식으로 Hybrid Runbook Worker에 대 한 작업을 처리 합니다. 장기 실행 runbook이 있는 경우 다시 시작할 수 있도록 복원 력이 있는지 확인 합니다. 작업 동작에 대 한 자세한 내용은 [Hybrid Runbook Worker 작업](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs)을 참조 하세요.

Hybrid Runbook Worker에 대 한 작업은 Windows의 로컬 **시스템** 계정 또는 Linux의 **nxautomation** 계정에서 실행 된다는 점에 주의 하세요. Linux의 경우 **nxautomation** 계정에 runbook 모듈이 저장 된 위치에 대 한 액세스 권한이 있는지 확인 합니다. [Install-Module](/powershell/module/powershellget/install-module) cmdlet을 사용 하는 경우 **nxautomation** 계정에 액세스 권한이 `Scope` 있는지 확인 하려면 매개 변수에 대해 AllUsers를 지정 해야 합니다. Linux의 PowerShell에 대 한 자세한 내용은 [Windows 이외의 플랫폼에서 powershell에 대 한 알려진 문제](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)를 참조 하세요.

## <a name="set-up-runbook-permissions"></a>Runbook 사용 권한 설정

다음과 같은 방법으로 Hybrid Runbook Manager에서 실행할 runbook에 대 한 사용 권한을 정의 합니다.

* Runbook이 로컬 리소스에 대 한 자체 인증을 제공 하도록 합니다.
* [Azure 리소스에 대 한 관리 id를](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)사용 하 여 인증을 구성 합니다. 
* 모든 runbook에 대 한 사용자 컨텍스트를 제공 하는 실행 계정을 지정 합니다.

## <a name="use-runbook-authentication-to-local-resources"></a>로컬 리소스에 runbook 인증 사용

리소스에 대 한 자체 인증을 제공 하는 runbook을 준비 하는 경우 runbook에서 [자격 증명](automation-credentials.md) 및 [인증서](automation-certificates.md) 자산을 사용 합니다. Runbook이 여러 리소스를 인증할 수 있도록 자격 증명을 지정할 수 있는 여러 cmdlet이 있습니다. 다음 예제에서는 컴퓨터를 다시 시작하는 Runbook의 일부를 보여 줍니다. 자격 증명 자산에서 자격 증명을 검색 하 고 변수 자산에서 컴퓨터 이름을 검색 한 다음 `Restart-Computer` cmdlet에 이러한 값을 사용 합니다.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

[InlineScript](automation-powershell-workflow.md#inlinescript) 활동을 사용할 수도 있습니다. `InlineScript`[PSCredential 일반 매개 변수로](/powershell/module/psworkflow/about/about_workflowcommonparameters)지정 된 자격 증명을 사용 하 여 다른 컴퓨터에서 코드 블록을 실행할 수 있습니다.

## <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>관리 id로 runbook 인증 사용

Azure virtual machines의 Hybrid Runbook Worker는 azure 리소스에 대 한 관리 되는 id를 사용 하 여 Azure 리소스에 인증할 수 있습니다. 실행 계정이 아닌 Azure 리소스에 대해 관리 되는 id를 사용 하면 다음을 수행 하지 않아도 되므로 이점이 제공 됩니다.

* 실행 인증서를 내보낸 다음 Hybrid Runbook Worker 가져옵니다.
* 실행 계정에서 사용 하는 인증서를 갱신 합니다.
* Runbook 코드에서 실행 연결 개체를 처리 합니다.

Hybrid Runbook Worker에서 Azure 리소스에 관리 되는 id를 사용 하려면 다음 단계를 수행 합니다.

1. Azure VM을 만듭니다.
2. VM에서 Azure 리소스에 대 한 관리 되는 id를 구성 합니다. [Azure Portal를 사용 하 여 VM에서 Azure 리소스에 대 한 관리 되는 Id 구성](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)을 참조 하세요.
3. 리소스 관리자에서 리소스 그룹에 대 한 VM 액세스를 제공 합니다. 리소스 관리자에 [액세스 하려면 WINDOWS VM 시스템 할당 관리 Id 사용](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)을 참조 하세요.
4. VM에 Hybrid Runbook worker를 설치 합니다. [Windows Hybrid Runbook Worker 배포를](automation-windows-hrw-install.md)참조 하세요.
5. 매개 변수와 함께 AzAccount cmdlet을 사용 하 여 Azure 리소스에 인증 하도록 runbook을 업데이트 합니다. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) `Identity` 이 구성을 사용 하면 실행 계정을 사용 하 고 연결 된 계정 관리를 수행할 필요가 줄어듭니다.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`는 시스템 할당 id 및 단일 사용자 할당 id를 사용 하 여 Hybrid Runbook Worker에 대해 작동 합니다. Hybrid Runbook Worker에서 사용자 할당 id를 여러 개 사용 하는 경우 Runbook은에 대 한 `AccountId` `Connect-AzAccount` 매개 변수를 지정 하 여 특정 사용자 할당 id를 선택 해야 합니다.

## <a name="use-runbook-authentication-with-run-as-account"></a>실행 계정으로 runbook 인증 사용

Runbook이 로컬 리소스에 대 한 자체 인증을 제공 하는 대신 Hybrid Runbook Worker 그룹에 대 한 실행 계정을 지정할 수 있습니다. 이렇게 하려면 로컬 리소스에 대 한 액세스 권한이 있는 [자격 증명 자산](automation-credentials.md) 을 정의 해야 합니다. 이러한 리소스에는 인증서 저장소가 포함 되며, 모든 runbook은 그룹의 Hybrid Runbook Worker에 대해 이러한 자격 증명으로 실행 됩니다.

자격 증명에 대한 사용자 이름은 다음 서식 중 하나여야 합니다.

* domain\username
* username@domain
* 사용자 이름(온-프레미스 컴퓨터에 로컬인 계정용)

다음 절차를 사용 하 여 Hybrid Runbook Worker 그룹에 대 한 실행 계정을 지정할 수 있습니다.

1. 로컬 리소스에 대한 액세스로 [자격 증명 자산](automation-credentials.md)을 만듭니다.
2. Azure Portal에서 Automation 계정을 엽니다.
3. **Hybrid Worker 그룹** 타일을 선택한 다음 그룹을 선택합니다.
4. **모든 설정**, **Hybrid worker 그룹 설정**을 차례로 선택 합니다.
5. **실행** 값을 **기본값** 에서 **사용자 지정**으로 변경 합니다.
6. 자격 증명을 선택하고 **저장**을 클릭합니다.

### <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>실행 계정 인증서 설치

Azure에서 리소스를 배포 하는 자동화 된 빌드 프로세스의 일부로, 배포 시퀀스의 작업 또는 단계 집합을 지원 하기 위해 온-프레미스 시스템에 액세스 해야 할 수 있습니다. 실행 계정을 사용 하 여 Azure에 대 한 인증을 제공 하려면 실행 계정 인증서를 설치 해야 합니다.

**Export-runascertificatetohybridworker**라는 PowerShell runbook은 Azure Automation 계정에서 실행 인증서를 내보냅니다. Runbook은 인증서를 다운로드 하 여 동일한 계정에 연결 된 Hybrid Runbook Worker의 로컬 컴퓨터 인증서 저장소로 가져옵니다. 해당 단계가 완료 되 면 runbook은 작업자가 실행 계정을 사용 하 여 Azure에 성공적으로 인증할 수 있는지 확인 합니다.

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
>PowerShell Runbook의 경우 `Add-AzAccount` 및 `Add-AzureRMAccount`는 `Connect-AzAccount`에 대한 별칭입니다. 라이브러리 항목을 검색할 때가 표시 `Connect-AzAccount`되지 않으면를 사용 `Add-AzAccount`하거나 Automation 계정에서 모듈을 업데이트할 수 있습니다.

실행 계정 준비를 완료 하려면:

1. **Export-runascertificatetohybridworker** runbook을 **.** p s 1 확장명을 사용 하 여 컴퓨터에 저장 합니다.
2. Automation 계정으로 가져옵니다.
3. Runbook을 편집 하 고 `Password` 변수 값을 자신의 암호로 변경 합니다. 
4. Runbook을 게시 합니다.
5. Runbook을 실행 하 고 실행 계정을 사용 하 여 runbook을 실행 하 고 인증 하는 Hybrid Runbook Worker 그룹을 대상으로 지정 합니다. 
6. 작업 스트림을 검사 하 여 로컬 컴퓨터 저장소로 인증서를 가져오려고 시도 하는 것을 보고 하 고 여러 줄을 사용 하 여 다음을 확인 합니다. 이 동작은 구독에 정의 하는 Automation 계정 수와 인증의 성공 정도에 따라 달라 집니다.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 runbook 시작

Runbook [시작 Azure Automation에서](start-runbooks.md) runbook을 시작 하는 다양 한 방법을 설명 합니다. Hybrid Runbook Worker에서 runbook을 시작 하면 Hybrid Runbook Worker 그룹의 이름을 지정할 수 있는 **실행** 대상 옵션이 사용 됩니다. 그룹을 지정 하면 해당 그룹의 작업자 중 하나가 runbook을 검색 하 고 실행 합니다. Runbook이이 옵션을 지정 하지 않으면 Azure Automation는 평소와 같이 runbook을 실행 합니다.

Azure Portal에서 runbook을 시작 하면 **Azure** 또는 **Hybrid Worker**를 선택할 수 있는 **실행** 대상 옵션이 표시 됩니다. **Hybrid Worker**를 선택 하는 경우 드롭다운에서 Hybrid Runbook Worker 그룹을 선택할 수 있습니다.

AzAutomationRunbook cmdlet `RunOn` 에 매개 변수 [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) 를 사용 합니다. 다음 예제에서는 Windows PowerShell을 사용 하 여 MyHybridGroup 라는 Hybrid Runbook Worker 그룹에 대해 **Test runbook** 이라는 runbook을 시작 합니다.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> 이전 버전을 설치한 경우 [최신 PowerShell 버전을 다운로드](https://azure.microsoft.com/downloads/) 해야 합니다. PowerShell에서 runbook을 시작 하는 워크스테이션에만이 버전을 설치 합니다. 이 컴퓨터에서 runbook을 시작 하려는 경우를 제외 하 고 Hybrid Runbook Worker 컴퓨터에 설치할 필요는 없습니다.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker에서 서명 된 runbook으로 작업

서명 된 runbook만 실행 하도록 Windows Hybrid Runbook Worker를 구성할 수 있습니다.

> [!IMPORTANT]
> 서명된 Runbook만 실행하도록 하Hybrid Runbook Worker를 구성하고 나면, 서명되지 않은 Runbook은 Worker에서 실행되지 않습니다.

### <a name="create-signing-certificate"></a>서명 인증서 만들기

다음 예제에서는 Runbook에 서명하는 데 사용할 수 있는 자체 서명된 인증서를 만듭니다. 이 코드는 인증서를 만들어 Hybrid Runbook Worker 나중에 가져올 수 있도록 내보냅니다. 인증서를 참조 하는 나중에 사용 하기 위해 지문을 반환 합니다.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>서명 유효성 검사를 위해 인증서 가져오기 및 작업자 구성

만든 인증서를 그룹의 각 Hybrid Runbook Worker에 복사 합니다. 다음 스크립트를 실행 하 여 인증서를 가져오고 작업 자가 runbook에 서명 유효성 검사를 사용 하도록 구성 합니다.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>인증서를 사용 하 여 runbook 서명

Hybrid Runbook Worker가 서명 된 runbook만 사용 하도록 구성 된 경우 Hybrid Runbook Worker에 사용할 runbook에 서명 해야 합니다. 다음 샘플 PowerShell 코드를 사용 하 여 이러한 runbook에 서명 합니다.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Runbook이 서명 된 경우 Automation 계정으로 가져온 후 서명 블록을 사용 하 여 게시 해야 합니다. Runbook을 가져오는 방법에 대 한 자세한 내용은 [Runbook 가져오기](manage-runbooks.md#import-a-runbook)를 참조 하세요.

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker에서 서명 된 runbook에 대 한 작업

서명 된 runbook을 사용할 수 있으려면 Linux Hybrid Runbook Worker 로컬 컴퓨터에 [GPG](https://gnupg.org/index.html) 실행 파일이 있어야 합니다.

> [!IMPORTANT]
> 서명된 Runbook만 실행하도록 하Hybrid Runbook Worker를 구성하고 나면, 서명되지 않은 Runbook은 Worker에서 실행되지 않습니다.

### <a name="create-a-gpg-keyring-and-keypair"></a>GPG 인증 키 및 키 쌍 만들기

GPG 인증 키 및 키 쌍을 만들려면 Hybrid Runbook Worker **nxautomation** 계정을 사용 합니다.

1. Sudo 응용 프로그램을 사용 하 여 **nxautomation** 계정으로 로그인 합니다.

    ```bash
    sudo su – nxautomation
    ```

2. **Nxautomation**을 사용 하 여 GPG 키 쌍을 생성 합니다. GPG는 단계를 안내 합니다. 이름, 전자 메일 주소, 만료 시간 및 암호를 제공 해야 합니다. 그런 다음 키를 생성할 수 있을 만큼 충분 한 엔트로피가 컴퓨터에 있을 때까지 기다립니다.

    ```bash
    sudo gpg --generate-key
    ```

3. GPG 디렉터리는 sudo를 사용 하 여 생성 되었으므로 다음 명령을 사용 하 여 소유자를 **nxautomation** 으로 변경 해야 합니다.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 인증 키를 사용할 수 있도록 설정

인증 키가 생성 되 면 Hybrid Runbook Worker 사용할 수 있도록 설정 합니다. 파일 섹션 `[worker-optional]`아래에 다음 예제 코드를 포함 하도록 **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf** 설정 파일을 수정 합니다.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>서명 유효성 검사가 설정 되어 있는지 확인 합니다.

컴퓨터에서 서명 유효성 검사를 사용 하지 않도록 설정한 경우 다음 sudo 명령을 실행 하 여 설정 해야 합니다. 을 `<LogAnalyticsworkspaceId>` 작업 영역 ID로 바꿉니다.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Runbook 서명

서명 유효성 검사를 구성한 후에는 다음 GPG 명령을 사용 하 여 runbook에 서명 합니다.

```bash
gpg –-clear-sign <runbook name>
```

서명 된 runbook을 ** <runbook name>. asc**라고 합니다.

이제 서명 된 runbook을 Azure Automation로 업로드 하 고 일반 runbook 처럼 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 텍스트 편집기를 사용 하 여 Azure Automation PowerShell runbook으로 작업 하는 방법을 이해 하려면 [Azure Automation에서 Runbook 편집](automation-edit-textual-runbook.md)을 참조 하세요.
* Runbook이 성공적으로 완료 되지 않으면 [runbook 실행 오류](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)에 대 한 문제 해결 가이드를 검토 합니다.
* 언어 참조 및 학습 모듈을 포함하여 PowerShell에 대한 자세한 내용은 [PowerShell 문서](https://docs.microsoft.com/powershell/scripting/overview)를 참조하세요.
* PowerShell cmdlet 참조는 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)을 참조하세요.

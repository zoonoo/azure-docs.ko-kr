---
title: 응용 프로그램이 Azure Stack Key Vault 암호를 검색할 수 있도록 | Microsoft Docs
description: 샘플 앱을 사용 하 여 Azure Stack Key Vault를 사용 하려면
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2018
ms.author: sethm
ms.openlocfilehash: ed02174247de1a99f3d9a4880fd0afa60f867552
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139473"
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>키 및 key vault에 저장 된 비밀을 사용 하는 샘플 응용 프로그램

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack에서 key vault에서 키 및 비밀을 검색 하는 샘플 응용 프로그램 (HelloKeyVault)를 실행 하려면이 문서의 단계를 수행 합니다.

## <a name="prerequisites"></a>필수 조건

Azure Stack에서 다음 필수 구성 요소를 설치할 수 있습니다 [개발 키트](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), 또는 Windows 기반 외부 클라이언트에서 라면 [VPN을 통해 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* 설치할 [Azure Stack 호환 Azure PowerShell 모듈](azure-stack-powershell-install.md)합니다.
* 일관 된 [azure storage: 차이점 및 고려 사항](azure-stack-powershell-download.md).

## <a name="create-and-get-the-key-vault-and-application-settings"></a>만들기 및 키 자격 증명 모음 및 응용 프로그램 설정 가져오기

샘플 응용 프로그램에 대 한 준비:

* Azure Stack에서 key vault를 만듭니다.
* Azure Active Directory (Azure AD)에서 응용 프로그램을 등록 합니다.

샘플 응용 프로그램을 준비 하는 Azure portal 또는 PowerShell을 사용할 수 있습니다. 이 문서에는 key vault를 만들고 PowerShell을 사용 하 여 응용 프로그램을 등록 하는 방법을 보여 줍니다.

>[!NOTE]
>PowerShell 스크립트는 기본적으로 Active Directory에서 새 응용 프로그램을 만듭니다. 그러나 기존 응용 프로그램 중 하나를 등록할 수 있습니다.

 다음 스크립트를 실행 하기 전에 값을 제공 하는지 확인 합니다 `aadTenantName` 고 `applicationPassword` 변수입니다. 에 대 한 값을 지정 하지 않으면 `applicationPassword`,이 스크립트는 임의의 암호를 생성 합니다.

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

```

다음 화면 캡처에는 key vault를 만드는 데 스크립트의 출력을 보여 줍니다.

![액세스 키를 사용 하 여 키 자격 증명 모음](media/azure-stack-kv-sample-app/settingsoutput.png)

기록해는 **VaultUrl**를 **AuthClientId**, 및 **AuthClientSecret** 이전 스크립트에서 반환 되는 값입니다. 이러한 값을 사용 하 여 HelloKeyVault 응용 프로그램을 실행 합니다.

## <a name="download-and-configure-the-sample-application"></a>다운로드 및 샘플 응용 프로그램 구성

Azure에서 키 자격 증명 모음 샘플을 다운로드 [Key Vault 클라이언트 샘플](https://www.microsoft.com/en-us/download/details.aspx?id=45343) 페이지입니다. 개발용 워크스테이션에서.zip 파일 콘텐츠의 압축을 풉니다. 이 문서에서는 HelloKeyVault, samples 폴더에 두 개의 응용 프로그램이 있습니다.

HelloKeyVault 샘플을 로드 합니다.

* 로 이동 합니다 **Microsoft.Azure.KeyVault.Samples** > **샘플** > **HelloKeyVault** 폴더입니다.
* Visual Studio에서 HelloKeyVault 응용 프로그램을 엽니다.

### <a name="configure-the-sample-application"></a>샘플 애플리케이션 구성

Visual Studio에서

* 검색을 찾고 HelloKeyVault\App.config 파일을 엽니다는 &lt; **appSettings** &gt; 요소입니다.
* 업데이트를 **VaultUrl**를 **AuthClientId**, 및 **AuthClientSecret** 를 key vault를 만드는 데 사용 하 여 반환 되는 값을 사용 하 여 키입니다. (기본적으로 App.config 파일에 대 한 자리 표시자 *AuthCertThumbprint*합니다. 이 자리 표시자를 바꿉니다 *AuthClientSecret*.)

  ![앱 설정](media/azure-stack-kv-sample-app/appconfig.png)

* 솔루션을 다시 빌드합니다.

## <a name="run-the-application"></a>애플리케이션 실행

HelloKeyVault를 실행 하면 응용 프로그램 Azure AD에 로그인 하 고 AuthClientSecret 토큰을 사용 하 여 Azure Stack에서 key vault에 인증할 수 있습니다.

HelloKeyVault 샘플을 사용할 수 있습니다.

* 키와 비밀에 기본 작업 만들기, 암호화, 줄 바꿈 및 삭제를 수행 합니다.
* 와 같은 매개 변수를 전달 *암호화* 및 *해독* HelloKeyVault에 key vault에 지정된 된 변경 내용을 적용 합니다.

## <a name="next-steps"></a>다음 단계

[Key Vault 암호를 사용하여 VM 배포](azure-stack-kv-deploy-vm-with-secret.md)

[Key Vault 인증서를 사용 하 여 VM 배포](azure-stack-kv-push-secret-into-vm.md)

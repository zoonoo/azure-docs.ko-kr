---
title: 응용 프로그램이 Azure 스택 키 자격 증명 모음 암호를 검색할 수 있도록 | Microsoft Docs
description: Azure 스택 주요 자격 증명 모음을 사용 하는 샘플 응용 프로그램을 사용 하 여
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2017
ms.author: mabrigg
ms.openlocfilehash: da2c733dbb1d1d3c8294ee82e722d33d363b29ab
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>키 및 키 자격 증명 모음에 저장 된 암호를 사용 하는 샘플 응용 프로그램

이 문서에서는 보여줍니다 스택에서 Azure 주요 자격 증명 모음에서 키와 암호를 검색 하는 예제 응용 프로그램 (HelloKeyVault)를 실행 하는 방법.

## <a name="prerequisites"></a>필수 조건 

다음 필수 구성 요소에서 실행 하거나는 [개발 키트](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), 또는 Windows 기반 외부 클라이언트에서 있다면 [VPN을 통해 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* 설치 [Azure 스택 호환 Azure PowerShell 모듈](azure-stack-powershell-install.md)합니다.  
* 다운로드는 [Azure 스택을 사용 하는 데 필요한 도구](azure-stack-powershell-download.md)합니다. 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>만들기 및 주요 자격 증명 모음 및 응용 프로그램 설정 가져오기

먼저, Azure 스택의 주요 자격 증명 모음 만들기를 Azure Active Directory (Azure AD)에 응용 프로그램을 등록 합니다. 수 만들고 Azure 포털 또는 PowerShell을 사용 하 여 주요 자격 증명 모음을 등록 합니다. 이 문서에서는 PowerShell 방식으로 태스크를 수행 합니다. 기본적으로이 PowerShell 스크립트는 Active Directory에 새 응용 프로그램을 만듭니다. 그러나 기존 응용 프로그램 중 하나 사용할 수도 있습니다. 에 대 한 값을 제공 해야는 `aadTenantName` 및 `applicationPassword` 변수입니다. 에 대 한 값을 지정 하지 않으면는 `applicationPassword` 변수를이 스크립트 임의의 암호를 생성 합니다. 

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

# Create a new resource group and a key vault within that resource group.
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

다음 스크린 샷에서 앞의 스크립트의 출력을 보여 줍니다.

![응용 프로그램 구성](media/azure-stack-kv-sample-app/settingsoutput.png)

메모는 **VaultUrl**, **AuthClientId**, 및 **AuthClientSecret** 앞의 스크립트에서 반환 된 값입니다. 이러한 값을 사용 하 여 HelloKeyVault 응용 프로그램을 실행 합니다.

## <a name="download-and-run-the-sample-application"></a>다운로드 하 여 샘플 응용 프로그램 실행

Azure에서 주요 자격 증명 모음 샘플을 다운로드 [키 자격 증명 모음 클라이언트 샘플](https://www.microsoft.com/en-us/download/details.aspx?id=45343) 페이지. 개발 워크스테이션에.zip 파일의 압축을 풉니다. 샘플 폴더 내에서 두 개의 샘플 있습니다. 이 문서의 내용에 HellpKeyVault 샘플을 사용 합니다. 찾아는 **Microsoft.Azure.KeyVault.Samples** > **샘플** > **HelloKeyVault** 폴더를 연 HelloKeyVault 응용 프로그램 Visual studio 합니다. 

HelloKeyVault\App.config 파일을 열고 값을 바꿀는 <appSettings> 인 요소는 **VaultUrl**, **AuthClientId**, 및 **AuthClientSecret** 값 앞의 스크립트에서 반환 합니다. 기본적으로 App.config에 대 한 자리 표시자 *AuthCertThumbprint*를 사용 하지만 *AuthClientSecret* 대신 합니다. 설정을 대체 한 후 솔루션을 다시 작성 하 고 응용 프로그램을 시작 합니다.

![앱 설정](media/azure-stack-kv-sample-app/appconfig.png)
 
응용 프로그램 Azure AD에 로그인 하 고 해당 토큰을 사용 하 여 Azure 스택의 주요 자격 증명 모음에 인증할 수 있습니다. 응용 프로그램 만들기, 암호화, 줄 바꿈 및 키 및 키 자격 증명 모음의 암호에서 삭제와 같은 작업을 수행 합니다. 특정 매개 변수를 같은 전달할 수도 있습니다 *암호화* 및 *해독* 응용 프로그램에 하면 응용 프로그램 자격 증명 모음에 대 한 작업만 실행 합니다. 


## <a name="next-steps"></a>다음 단계
[Key Vault 암호를 사용하여 VM 배포](azure-stack-kv-deploy-vm-with-secret.md)

[주요 자격 증명 모음 인증서를 사용 하 여 VM 배포](azure-stack-kv-push-secret-into-vm.md)




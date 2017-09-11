---
title: "PowerShell을 사용하여 Azure 앱에 대한 ID 만들기 | Microsoft Docs"
description: "Azure PowerShell을 사용하여 Azure Active Directory 응용 프로그램 및 서비스 주체를 만들고 역할 기반 액세스 제어를 통해 리소스에 대한 액세스를 부여하는 방법을 설명합니다. 암호 또는 인증서를 사용하여 응용 프로그램을 인증하는 방법을 보여 줍니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/28/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 9d4ab890c35eebb2e59a9f4fa96843c854636272
ms.contentlocale: ko-kr
ms.lasthandoff: 08/29/2017

---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기

리소스에 액세스해야 하는 앱 또는 스크립트가 있는 경우 앱에 대한 ID를 설정하고 자체 자격 증명으로 앱을 인증할 수 있습니다. 이 ID를 서비스 주체라고 합니다. 이 접근 방법을 사용하면 다음을 수행할 수 있습니다.

* 자체 사용 권한과 다른 앱 ID에 대한 사용 권한을 할당합니다. 일반적으로 이러한 권한은 정확히 앱 실행에 필요한 것으로 제한됩니다.
* 무인 스크립트를 실행할 때 인증을 위해 인증서를 사용합니다.

이 토픽에서는 [Azure PowerShell](/powershell/azure/overview) 을 사용하여 응용 프로그램을 자체 자격 증명 및 ID로 실행하는 데 필요한 모든 항목을 설정하는 방법을 보여 줍니다.

## <a name="required-permissions"></a>필요한 사용 권한
이 항목을 완료하려면 Azure Active Directory와 Azure 구독에 대한 충분한 권한이 있어야 합니다. 특히, Azure Active Directory에서 앱을 만들고 역할에 서비스 주체를 할당할 수 있어야 합니다. 

계정에 적절한 사용 권한이 있는지를 확인하는 가장 쉬운 방법은 포털을 통하는 것입니다. [필요한 사용 권한 확인](resource-group-create-service-principal-portal.md#required-permissions)을 참조하세요.

이제 다음을 사용하여 인증을 받기 위한 섹션을 계속 진행합니다.

* [암호](#create-service-principal-with-password)
* [자체 서명된 인증서](#create-service-principal-with-self-signed-certificate)
* [인증 기관의 인증서](#create-service-principal-with-certificate-from-certificate-authority)

## <a name="powershell-commands"></a>PowerShell 명령

서비스 주체를 설정하려면 다음 항목을 사용합니다.

| 명령 | 설명 |
| ------- | ----------- | 
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Azure Active Directory 서비스 주체 만들기 |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | 지정된 범위에서 지정된 보안 주체에 지정된 RBAC 역할을 할당합니다. |


## <a name="create-service-principal-with-password"></a>암호를 사용하여 서비스 주체 만들기

구독에 대해 참여자 역할을 가진 서비스 주체를 만들려면 다음 항목을 사용합니다. 

```powershell
Login-AzureRmAccount
$sp = New-AzureRmADServicePrincipal -DisplayName exampleapp -Password "{provide-password}"
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

이 예제는 새 서비스 주체가 Azure Active Directory 전체에 전파될 시간을 허용하기 위해 20분간 대기합니다. 스크립트가 대기하는 시간이 충분히 길지 않으면 “PrincipalNotFound: 보안 주체 {ID}이(가) 디렉터리에 없습니다.”라는 오류 메시지가 표시됩니다.

다음 스크립트를 통해 기본 구독 이외의 범위를 지정하고 오류가 발생하는 경우 역할 할당을 다시 시도할 수 있습니다.

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
)

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 
 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

스크립트에 대해 다음 사항을 알아두세요.

* ID에기본 구독에 대한 액세스 권한을 부여하기 위해 ResourceGroup 또는 SubscriptionId 매개 변수를 제공할 필요는 없습니다.
* 역할 할당의 범위를 리소스 그룹으로 제한하려는 경우에만 ResourceGroup 매개 변수를 지정합니다.
*  이 예제에서는 참가자 역할에 서비스 주체를 추가합니다. 다른 역할에 대해서는 [RBAC: 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)을 참조하세요.
* 이 스크립트는 새 서비스 주체가 Azure Active Directory 전체에 전파될 시간을 허용하기 위해 15분간 대기합니다. 스크립트가 대기하는 시간이 충분히 길지 않으면 “PrincipalNotFound: 보안 주체 {ID}이(가) 디렉터리에 없습니다.”라는 오류 메시지가 표시됩니다.
* 서비스 주체에게 더 많은 구독 또는 리소스 그룹에 대한 액세스 권한을 부여해야 할 경우 다른 범위를 지정해서 `New-AzureRMRoleAssignment` cmdlet을 다시 실행합니다.


### <a name="provide-credentials-through-powershell"></a>PowerShell을 통해 자격 증명 제공
이제 응용 프로그램으로 로그인하여 작업을 수행해야 합니다. 사용자 이름으로 응용 프로그램에 대해 만든 `ApplicationId`를 사용합니다. 암호의 경우 계정을 만들 때 지정한 암호를 사용합니다. 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-ID}
```

테넌트 ID는 대/소문자를 구분하지 않으므로 스크립트에 직접 포함할 수 있습니다. 테넌트 ID를 검색해야 할 경우 다음을 사용합니다.

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>자체 서명된 인증서를 사용하여 서비스 주체 만들기

구독에 대해 자체 서명된 인증서 및 참여자 역할을 가진 서비스 주체를 만들려면 다음 항목을 사용합니다. 

```powershell
Login-AzureRmAccount
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

이 예제는 새 서비스 주체가 Azure Active Directory 전체에 전파될 시간을 허용하기 위해 20분간 대기합니다. 스크립트가 대기하는 시간이 충분히 길지 않으면 “PrincipalNotFound: 보안 주체 {ID}이(가) 디렉터리에 없습니다.”라는 오류 메시지가 표시됩니다.

다음 스크립트를 통해 기본 구독 이외의 범위를 지정하고 오류가 발생하는 경우 역할 할당을 다시 시도할 수 있습니다. Windows 10 또는 Windows Server 2016에서 Azure PowerShell 2.0이 있어야 합니다.

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

스크립트에 대해 다음 사항을 알아두세요.

* ID에기본 구독에 대한 액세스 권한을 부여하기 위해 ResourceGroup 또는 SubscriptionId 매개 변수를 제공할 필요는 없습니다.
* 역할 할당의 범위를 리소스 그룹으로 제한하려는 경우에만 ResourceGroup 매개 변수를 지정합니다.
* 이 예제에서는 참가자 역할에 서비스 주체를 추가합니다. 다른 역할에 대해서는 [RBAC: 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)을 참조하세요.
* 이 스크립트는 새 서비스 주체가 Azure Active Directory 전체에 전파될 시간을 허용하기 위해 15분간 대기합니다. 스크립트가 대기하는 시간이 충분히 길지 않으면 “PrincipalNotFound: 보안 주체 {ID}이(가) 디렉터리에 없습니다.”라는 오류 메시지가 표시됩니다.
* 서비스 주체에게 더 많은 구독 또는 리소스 그룹에 대한 액세스 권한을 부여해야 할 경우 다른 범위를 지정해서 `New-AzureRMRoleAssignment` cmdlet을 다시 실행합니다.

**Windows 10 또는 Windows Server 2016 Technical Preview**사용자가 아니라면 Microsoft Script Center에서 [Self-signed certificate generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) 를 다운로드해야 합니다. 해당 내용을 추출하고 필요한 cmdlet을 가져옵니다.

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
스크립트에서 다음 두 줄을 바꾸어 인증서를 생성합니다.
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>자동화된 PowerShell 스크립트를 통해 인증서 제공
서비스 주체로 로그인할 때마다 AD 앱에 디렉터리의 테넌트 ID를 제공해야 합니다. 테넌트는 Azure Active Directory의 인스턴스입니다. 구독이 하나만 있는 경우 다음을 사용할 수 있습니다.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

응용 프로그램 ID 및 테넌트 ID는 대/소문자를 구분하지 않으므로 스크립트에 직접 포함할 수 있습니다. 테넌트 ID를 검색해야 할 경우 다음을 사용합니다.

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

응용 프로그램 ID를 검색해야 할 경우 다음을 사용합니다.

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>인증 기관의 인증서를 사용하여 서비스 주체 만들기
인증 기관에서 발급한 인증서를 사용해서 서비스 주체를 만들려면 다음 스크립트를 사용합니다.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -SubscriptionId $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

스크립트에 대해 다음 사항을 알아두세요.

* 해당 구독으로 액세스 범위가 지정됩니다.
* 이 예제에서는 참가자 역할에 서비스 주체를 추가합니다. 다른 역할에 대해서는 [RBAC: 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)을 참조하세요.
* 이 스크립트는 새 서비스 주체가 Azure Active Directory 전체에 전파될 시간을 허용하기 위해 15분간 대기합니다. 스크립트가 대기하는 시간이 충분히 길지 않으면 “PrincipalNotFound: 보안 주체 {ID}이(가) 디렉터리에 없습니다.”라는 오류 메시지가 표시됩니다.
* 서비스 주체에게 더 많은 구독 또는 리소스 그룹에 대한 액세스 권한을 부여해야 할 경우 다른 범위를 지정해서 `New-AzureRMRoleAssignment` cmdlet을 다시 실행합니다.

### <a name="provide-certificate-through-automated-powershell-script"></a>자동화된 PowerShell 스크립트를 통해 인증서 제공
서비스 주체로 로그인할 때마다 AD 앱에 디렉터리의 테넌트 ID를 제공해야 합니다. 테넌트는 Azure Active Directory의 인스턴스입니다.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

응용 프로그램 ID 및 테넌트 ID는 대/소문자를 구분하지 않으므로 스크립트에 직접 포함할 수 있습니다. 테넌트 ID를 검색해야 할 경우 다음을 사용합니다.

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

응용 프로그램 ID를 검색해야 할 경우 다음을 사용합니다.

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>자격 증명 변경

보안 위협 또는 자격 증명 만료 때문에 AD 앱에 대한 자격 증명을 변경하려면 [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) 및 [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential) cmdlet을 사용합니다.

응용 프로그램에 대한 자격 증명을 모두 제거하려면 다음을 사용합니다.

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

암호를 추가하려면 다음을 사용합니다.

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

인증서 값을 추가하려면 이 항목에 설명된 대로 자체 서명된 인증서를 만듭니다. 그 후 다음을 사용합니다.

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>액세스 토큰을 저장하여 로그인 단순화
로그인해야 할 때마다 서비스 주체 자격 증명을 제공하는 것을 피하기 위해 액세스 토큰을 저장할 수 있습니다.

이후 세션에서 현재 액세스 토큰을 사용하기 위해 프로필을 저장합니다.
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
프로필을 열고 해당 내용을 검사합니다. 액세스 토큰을 포함하는지 확인합니다. 수동으로 다시 로그인하는 대신 프로필을 로드합니다.
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> 토큰이 유효한 한 저장된 프로필을 사용해야만 작동하므로 액세스 토큰이 만료됩니다.
>  

또는 PowerShell에서 REST 작업을 호출하여 로그인할 수 있습니다. 인증 응답에서 다른 작업에 사용할 액세스 토큰을 검색할 수 있습니다. REST 작업을 호출하여 액세스 토큰을 검색하는 예제는 [액세스 토큰 생성하기](resource-manager-rest-api.md#generating-an-access-token)를 참조하세요.

## <a name="debug"></a>디버그

서비스 주체를 만들 때 다음과 같은 오류가 발생할 수 있습니다.

* **"Authentication_Unauthorized"** 또는 **"컨텍스트에서 구독을 찾을 수 없습니다."** - 계정에 Azure Active Directory에서 앱을 등록하는 데 [필요한 권한](#required-permissions)이 없으면 이 오류가 발생합니다. 일반적으로 Azure Active Directory의 관리 사용자만 앱을 등록할 수 있고 사용자 계정은 관리자가 아니면 이 오류가 표시됩니다. 관리자 역할에 사용자를 할당하거나 사용자가 앱을 등록할 수 있게 설정하도록 관리자에게 요청합니다.

* 계정에 **"'/subscriptions/{guid}' 범위에 대해 'Microsoft.Authorization/roleAssignments/write' 작업을 수행할 수 있는 권한이 없습니다."** - 계정에 ID에 역할을 할당할 수 있는 충분한 권한이 없을 때 이 오류가 표시됩니다. 구독 관리자에게 사용자 액세스 관리자 역할에 사용자를 추가할 것을 요청합니다.

## <a name="sample-applications"></a>샘플 응용 프로그램
다른 플랫폼을 통해 응용 프로그램으로 로그인하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.JS](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>다음 단계
* 리소스 관리를 위해 Azure에 응용 프로그램을 통합하는 자세한 단계를 보려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.
* 응용 프로그램 및 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](../active-directory/active-directory-application-objects.md)를 참조하세요. 
* Azure Active Directory 인증에 대한 자세한 내용은 [Azure AD의 인증 시나리오](../active-directory/active-directory-authentication-scenarios.md)를 참조하세요.
* 권한이 부여되거나 사용자에 대해 거부될 수 있는 작업 목록은 [Azure Resource Manager 리소스 공급자 작업](../active-directory/role-based-access-control-resource-provider-operations.md)을 참조하세요.



---
title: "PowerShell을 사용하여 Azure 앱에 대한 ID 만들기 | Microsoft Docs"
description: "Azure PowerShell을 사용하여 Active Directory 응용 프로그램 및 서비스 주체를 만들고 역할 기반 액세스 제어를 통해 리소스에 대한 액세스를 부여하는 방법을 설명합니다. 암호 또는 인증서를 사용하여 응용 프로그램을 인증하는 방법을 보여 줍니다."
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
ms.date: 01/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 31495f402b810c524bd7b906498774302500b732


---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Azure CLI](resource-group-authenticate-service-principal-cli.md)
> * [포털](resource-group-create-service-principal-portal.md)
> 
> 

리소스에 액세스해야 하는 앱 또는 스크립트가 있는 경우 앱에 대한 ID를 설정하고 자체 자격 증명으로 인증할 수 있습니다. 이 방법은 다음의 이유로 사용자 고유의 자격 증명을 사용하여 앱을 실행하는 데 좋습니다.

* 자체 사용 권한과 다른 앱 ID에 대한 사용 권한을 할당할 수 있습니다. 일반적으로 이러한 권한은 정확히 앱 실행에 필요한 것으로 제한됩니다.
* 책임이 변경되면 앱의 자격 증명을 변경할 필요가 없습니다. 
* 무인 스크립트를 실행할 때 인증서를 사용하여 인증을 자동화할 수 있습니다.

이 토픽에서는 [Azure PowerShell](/powershell/azureps-cmdlets-docs) 을 사용하여 응용 프로그램을 자체 자격 증명 및 ID로 실행하는 데 필요한 모든 항목을 설정하는 방법을 보여 줍니다.

PowerShell을 사용하는 경우 AD 응용 프로그램을 인증하기 위한 다음&2;가지 옵션이 있습니다.

* password
* 인증서

이 항목에서는 PowerShell에서 두 가지 옵션을 사용하는 방법을 보여 줍니다. 프로그래밍 프레임워크(예: Python, Ruby 또는 Node.js)에서 Azure에 로그인하려는 경우 암호 인증이 가장 좋은 방법일 수 있습니다. 암호 또는 인증서 중 어떤 방법을 사용할지 결정하기 전에 [샘플 응용 프로그램](#sample-applications) 섹션에서 여러 다른 프레임워크에서 인증하는 방법의 예를 참조하세요.

## <a name="active-directory-concepts"></a>Active Directory 개념
이 문서에서는 AD(Active Directory) 응용 프로그램 및 서비스 주체라는 두 개체를 만듭니다. AD 응용 프로그램은 응용 프로그램에 대한 글로벌 표현입니다. 여기에는 자격 증명(응용 프로그램 ID 및 암호 또는 인증서)이 포함되어 있습니다. 서비스 주체는 Active Directory의 응용 프로그램에 대한 로컬 표현입니다. 여기에는 역할 할당이 포함됩니다. 이 토픽에서는 응용 프로그램을 하나의 조직 내에서만 실행하게 되는 단일 테넌트 응용 프로그램을 중점적으로 다룹니다. 일반적으로 단일 조직 내에서 실행되는 LOB(기간 업무) 응용 프로그램에 대해 단일 테넌트 응용 프로그램을 사용하게 됩니다. 단일 테넌트 응용 프로그램에는 하나의 AD 앱과 하나의 서비스 주체가 있습니다.

두 개체가 모두 필요한 이유는 무엇일까요? 이 접근 방식은 다중 테넌트 응용 프로그램을 고려할 때 유용하기 때문입니다. 일반적으로 응용 프로그램이 여러 구독에서 실행되는 SaaS(Software-as-a-Service) 응용 프로그램에 대해 다중 테넌트 응용 프로그램을 사용합니다. 다중 테넌트 응용 프로그램의 경우 하나의 AD 앱과 여러 서비스 주체(앱에 액세스하는 Active Directory에 대해 하나씩)가 있습니다. 다중 테넌트 응용 프로그램을 설정하려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.

## <a name="required-permissions"></a>필요한 사용 권한
이 항목을 완료하려면 Azure Active Directory와 Azure 구독에 대한 충분한 권한이 있어야 합니다. 특히, Active Directory에서 앱을 만들고 역할에 서비스 주체를 할당할 수 있어야 합니다. 

계정에 적절한 사용 권한이 있는지를 확인하는 가장 쉬운 방법은 포털을 통하는 것입니다. [필요한 사용 권한 확인](resource-group-create-service-principal-portal.md#required-permissions)을 참조하세요.

이제 [암호](#create-service-principal-with-password) 또는 [인증서](#create-service-principal-with-certificate) 인증에 대한 섹션을 계속 진행합니다.

## <a name="create-service-principal-with-password"></a>암호를 사용하여 서비스 주체 만들기
이 섹션에서 수행하는 단계는 다음과 같습니다.

* 암호를 사용하여 AD 응용 프로그램 만들기
* 서비스 주체 만들기
* 읽기 권한자 역할을 서비스 주체에 할당

이러한 단계를 신속하게 수행하려면 다음 cmdlet을 참조하세요.

```powershell
$app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
Start-Sleep 15
New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
```

이 스크립트는 새 서비스 주체가 Active Directory 전체에 전파될 시간을 허용하기 위해 15분간 대기합니다. 스크립트가 대기하는 시간이 충분히 길지 않으면 "PrincipalNotFound: 보안 주체 {id}이(가) 디렉터리에 없습니다."라는 오류 메시지가 표시됩니다. 이 오류가 표시되면 cmdlet을 다시 실행하여 주체를 역할에 다시 할당합니다.

프로세스를 이해하기 위해 이러한 단계를 더 신중하게 살펴 보겠습니다.

1. 계정에 로그인합니다.
   
   ```powershell
   Add-AzureRmAccount
   ```

2. 표시 이름, 응용 프로그램을 설명하는 URI, 응용 프로그램을 식별하는 URI, 응용 프로그램 ID에 대한 암호를 제공하여 새 Active Directory 응용 프로그램을 만듭니다.

   ```powershell   
   $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "{Your_Password}"
   ```

     단일 테넌트 응용 프로그램에서는 URI의 유효성이 검사되지 않습니다.
   
     Active Directory에 대한 [필수 권한](#required-permissions)이 계정에 없는 경우 "Authentication_Unauthorized" 또는 "No subscription found in the context"(컨텍스트에 구독이 없습니다.)라는 오류 메시지가 나타납니다.
3. 새 응용 프로그램 개체를 검사합니다. 
   
   ```powershell
   $app
   ```
   
     특히 서비스 주체 만들기, 역할 할당 및 액세스 토큰 획득을 위해서는 `ApplicationId` 속성이 필요합니다.
   
   ```powershell
   DisplayName             : exampleapp
   ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
   IdentifierUris          : {https://www.contoso.org/example}
   HomePage                : https://www.contoso.org
   Type                    : Application
   ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
   AvailableToOtherTenants : False
   AppPermissions          : 
   ReplyUrls               : {}
   ```
4. Active Directory 응용 프로그램의 응용 프로그램 ID를 전달하여 응용 프로그램에 대한 서비스 주체를 만듭니다.
   
   ```powershell
   New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
   ```

5. 서비스 사용자에게 구독에 대한 권한을 부여합니다. 이 예제에서는 구독에서 모든 리소스를 읽을 수 있는 권한이 부여된 읽기 권한자 역할에 서비스 주체를 추가합니다. 다른 역할에 대해서는 [RBAC: 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)을 참조하세요. `ServicePrincipalName` 매개 변수의 경우 응용 프로그램을 만들 때 사용한 `ApplicationId`를 제공합니다. 이 cmdlet을 실행하기 전에 새 서비스 주체가 Active Directory 전체에 전파될 어느 정도의 시간을 허용해야 합니다. 이러한 cmdlet을 수동으로 실행할 때 cmdlet 간에 충분한 시간이 경과됩니다. 스크립트에 cmdlet 사이에 대기하는 단계를 추가해야 합니다(예시 `Start-Sleep 15`). "PrincipalNotFound: 보안 주체 {id}이(가) 디렉터리에 없습니다"라는 오류가 표시되면 cmdlet을 다시 실행합니다.

   ```powershell   
   New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
   ```

    계정에 역할을 할당할 권한이 없는 경우 오류 메시지가 나타납니다. 이 메시지는 계정에 "/subscriptions/{guid}' 범위에 대해 'Microsoft.Authorization/roleAssignments/write' 작업을 수행할 권한이 없다"는 내용입니다. 

이것으로 끝입니다. AD 응용 프로그램 및 서비스 주체가 설정되었습니다. 다음 섹션에서는 PowerShell을 통해 인증서를 사용하여 로그인하는 방법을 보여 줍니다. 코드 응용 프로그램에서 자격 증명을 사용하려는 경우 [응용 프로그램 예제](#sample-applications)로 이동할 수 있습니다. 

### <a name="provide-credentials-through-powershell"></a>PowerShell을 통해 자격 증명 제공
이제 응용 프로그램으로 로그인하여 작업을 수행해야 합니다.

1. `Get-Credential` 명령을 실행하여 자격 증명을 포함하는 `PSCredential` 개체를 만듭니다. 이 명령을 실행하기 전에 `ApplicationId`가 필요하므로 붙여넣을 수 있게 준비해야 합니다.

   ```powershell   
   $creds = Get-Credential
   ```

2. 자격 증명을 입력하라는 메시지가 표시됩니다. 사용자 이름의 경우 응용 프로그램을 만들 때 사용한 `ApplicationId`를 사용합니다. 암호의 경우 계정을 만들 때 지정한 암호를 사용합니다.
   
     ![자격 증명 입력](./media/resource-group-authenticate-service-principal/arm-get-credential.png)
3. 서비스 주체로 로그인할 때마다 AD 앱에 디렉터리의 테넌트 ID를 제공해야 합니다. 테넌트는 Active Directory의 인스턴스입니다. 구독이 하나만 있는 경우 다음을 사용할 수 있습니다.

   ```powershell   
   $tenant = (Get-AzureRmSubscription).TenantId
   ```
   
     구독이 둘 이상 있는 경우 Active Directory가 상주하는 구독을 지정합니다. 자세한 내용은 [Azure 구독과 Azure Active Directory의 연관 관계](../active-directory/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.

   ```powershell
   $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
   ```

4. 이 계정이 서비스 주체임을 지정하고 자격 증명 개체를 제공하여 서비스 주체로 로그인합니다. 
   
   ```powershell
   Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
   ```
   
     이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

### <a name="save-access-token-to-simplify-log-in"></a>액세스 토큰을 저장하여 로그인 단순화
로그인해야 할 때마다 서비스 주체 자격 증명을 제공하는 것을 피하기 위해 액세스 토큰을 저장할 수 있습니다.

1. 이후 세션에서 현재 액세스 토큰을 사용하기 위해 프로필을 저장합니다.
   
   ```powershell
   Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
   ```
   
     프로필을 열고 해당 내용을 검사합니다. 액세스 토큰을 포함하는지 확인합니다. 
2. 수동으로 다시 로그인하는 대신 프로필을 로드하면 됩니다.
   
   ```powershell
   Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
   ```

  > [!NOTE]
  > 토큰이 유효한 한 저장된 프로필을 사용해야만 작동하므로 액세스 토큰이 만료됩니다.
  >  

또는 PowerShell에서 REST 작업을 호출하여 로그인할 수 있습니다. 인증 응답에서 다른 작업에 사용할 액세스 토큰을 검색할 수 있습니다. REST 작업을 호출하여 액세스 토큰을 검색하는 예제는 [액세스 토큰 생성하기](resource-manager-rest-api.md#generating-an-access-token)를 참조하세요.

## <a name="create-service-principal-with-certificate"></a>인증서를 사용하여 서비스 주체 만들기
이 섹션에서 수행하는 단계는 다음과 같습니다.

* 자체 서명된 인증서 만들기
* 인증서를 사용하여 AD 응용 프로그램 만들기
* 서비스 주체 만들기
* 읽기 권한자 역할을 서비스 주체에 할당

Windows 10 또는 Windows Server 2016 Technical Preview에서 Azure PowerShell 2.0으로 이러한 단계를 신속하게 수행하려면 다음 cmdlet을 참조하세요.

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
$app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
Start-Sleep 15
New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
```

이 스크립트는 새 서비스 주체가 Active Directory 전체에 전파될 시간을 허용하기 위해 15분간 대기합니다. 스크립트가 대기하는 시간이 충분히 길지 않으면 "PrincipalNotFound: 보안 주체 {id}이(가) 디렉터리에 없습니다."라는 오류 메시지가 표시됩니다. 이 오류가 표시되면 cmdlet을 다시 실행하여 주체를 역할에 다시 할당합니다.

프로세스를 이해하기 위해 이러한 단계를 더 신중하게 살펴 보겠습니다. 이 문서에는 이전 버전의 Azure PowerShell 또는 운영 체제를 사용하는 경우 작업을 수행하는 방법도 보여 줍니다.

### <a name="create-the-self-signed-certificate"></a>자체 서명된 인증서 만들기
Windows 10 및 Windows Server 2016 Technical Preview에서 사용 가능한 PowerShell 버전에는 자체 서명된 인증서를 생성하기 위한 업데이트된 `New-SelfSignedCertificate` cmdlet이 포함되어 있습니다. 이전 운영 체제에는 New-SelfSignedCertificate cmdlet이 포함되어 있지만 이 항목에 대해 필요한 매개 변수를 제공하지 않습니다. 대신, 인증서를 생성하는 모듈을 가져와야 합니다. 이 항목에서는 현재 운영 체제에 따라 인증서를 생성하는 두 방법을 모두 보여 줍니다. 

* **Windows 10 또는 Windows Server 2016 Technical Preview**사용자라면 다음 명령을 실행하여 자체 서명된 인증서를 생성하세요. 
   
  ```powershell
  $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
  ```
* **Windows 10 또는 Windows Server 2016 Technical Preview**사용자가 아니라면 Microsoft Script Center에서 [Self-signed certificate generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) 를 다운로드해야 합니다. 해당 내용을 추출하고 필요한 cmdlet을 가져옵니다.

  ```powershell  
  # Only run if you could not use New-SelfSignedCertificate
  Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  ```
  
     그런 다음 인증서를 생성합니다.
  
  ```powershell
  $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
  ```

인증서를 보유하면 AD 앱 만들기를 계속할 수 있습니다.

### <a name="create-the-active-directory-app-and-service-principal"></a>Active Directory 및 서비스 주체 만들기
1. 인증서에서 키 값을 검색합니다.
   
   ```powershell
   $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
   ```
2. Azure 계정에 로그인합니다.
   
   ```powershell
   Add-AzureRmAccount
   ```
3. 표시 이름, 응용 프로그램을 설명하는 URI, 응용 프로그램을 식별하는 URI, 응용 프로그램 ID에 대한 암호를 제공하여 새 Active Directory 응용 프로그램을 만듭니다.
   
     Azure PowerShell 2.0(2016년 8월 이후)을 사용하는 경우 다음 cmdlet을 사용합니다.

   ```powershell   
   $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
   ```
   
    Azure PowerShell 1.0을 사용하는 경우 다음 cmdlet을 사용합니다.

   ```powershell
   $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore
   ```
   
    단일 테넌트 응용 프로그램에서는 URI의 유효성이 검사되지 않습니다.
   
    Active Directory에 대한 [필수 권한](#required-permissions)이 계정에 없는 경우 "Authentication_Unauthorized" 또는 "No subscription found in the context"(컨텍스트에 구독이 없습니다.)라는 오류 메시지가 나타납니다.
   
    새 응용 프로그램 개체를 검사합니다. 
   
   ```powershell
   $app
   ```
   
    서비스 주체 만들기, 역할 할당 및 액세스 토큰 획득을 위해서는 **ApplicationId** 속성이 필요합니다.

   ```powershell
   DisplayName             : exampleapp
   ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
   IdentifierUris          : {https://www.contoso.org/example}
   HomePage                : https://www.contoso.org
   Type                    : Application
   ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
   AvailableToOtherTenants : False
   AppPermissions          : 
   ReplyUrls               : {}
   ```
4. Active Directory 응용 프로그램의 응용 프로그램 ID를 전달하여 응용 프로그램에 대한 서비스 주체를 만듭니다.
   
   ```powershell
   New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
   ```
5. 서비스 사용자에게 구독에 대한 권한을 부여합니다. 이 예제에서는 구독에서 모든 리소스를 읽을 수 있는 권한이 부여된 읽기 권한자 역할에 서비스 주체를 추가합니다. 다른 역할에 대해서는 [RBAC: 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)을 참조하세요. `ServicePrincipalName` 매개 변수의 경우 응용 프로그램을 만들 때 사용한 `ApplicationId`를 제공합니다. 이 cmdlet을 실행하기 전에 새 서비스 주체가 Active Directory 전체에 전파될 어느 정도의 시간을 허용해야 합니다. 이러한 cmdlet을 수동으로 실행할 때 cmdlet 간에 충분한 시간이 경과됩니다. 스크립트에 cmdlet 사이에 대기하는 단계를 추가해야 합니다(예시 `Start-Sleep 15`). "PrincipalNotFound: 보안 주체 {id}이(가) 디렉터리에 없습니다"라는 오류가 표시되면 cmdlet을 다시 실행합니다.
   
   ```powershell
   New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
   ```
   
    계정에 역할을 할당할 권한이 없는 경우 오류 메시지가 나타납니다. 이 메시지는 계정에 "/subscriptions/{guid}' 범위에 대해 'Microsoft.Authorization/roleAssignments/write' 작업을 수행할 권한이 없다"는 내용입니다.

이것으로 끝입니다. AD 응용 프로그램 및 서비스 주체가 설정되었습니다. 다음 섹션에서는 PowerShell을 통해 인증서를 사용하여 로그인하는 방법을 보여 줍니다.

### <a name="provide-certificate-through-automated-powershell-script"></a>자동화된 PowerShell 스크립트를 통해 인증서 제공
서비스 주체로 로그인할 때마다 AD 앱에 디렉터리의 테넌트 ID를 제공해야 합니다. 테넌트는 Active Directory의 인스턴스입니다. 구독이 하나만 있는 경우 다음을 사용할 수 있습니다.

```powershell
$tenant = (Get-AzureRmSubscription).TenantId
```

구독이 둘 이상 있는 경우 Active Directory가 상주하는 구독을 지정합니다. 자세한 내용은 [Azure AD 디렉터리 관리](../active-directory/active-directory-administer.md)를 참조하세요.

```powershell
$tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

스크립트에서 인증하려면 계정이 서비스 주체인지를 지정하고 인증서 지문, 응용 프로그램 ID 및 테넌트 ID를 제공합니다. 스크립트를 자동화하려면 이러한 값을 환경 변수로 저장하고 실행 중에 검색하거나 스크립트에 포함할 수 있습니다.

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant
```

이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

## <a name="change-credentials"></a>자격 증명 변경

보안 위협 또는 자격 증명 만료 때문에 AD 앱에 대한 자격 증명을 변경하려면 [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) 및 [New-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermadappcredential) cmdlet을 사용합니다.

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

## <a name="sample-applications"></a>샘플 응용 프로그램
다음 예제 응용 프로그램에서는 서비스 주체로 로그인하는 방법을 보여 줍니다.

**.NET**

* [.NET에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [.NET을 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [리소스 사용 시작 - Azure Resource Manager 템플릿을 사용하여 배포 - Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [리소스 사용 시작 - 리소스 그룹 관리 - Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Python에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Python을 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

* [Node.js에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Node.js를 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Ruby에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Ruby를 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>다음 단계
* 리소스 관리를 위해 Azure에 응용 프로그램을 통합하는 자세한 단계를 보려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.
* 응용 프로그램 및 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](../active-directory/active-directory-application-objects.md)를 참조하세요. 
* Active Directory 인증에 대한 자세한 내용은 [Azure AD의 인증 시나리오](../active-directory/active-directory-authentication-scenarios.md)를 참조하세요.




<!--HONumber=Jan17_HO4-->



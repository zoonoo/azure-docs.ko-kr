<properties
   pageTitle="PowerShell을 사용하여 AD 응용 프로그램 만들기 | Microsoft Azure"
   description="Azure PowerShell을 사용하여 Active Directory 응용 프로그램을 만들고 역할 기반 액세스 제어를 통해 리소스에 대한 액세스를 부여하는 방법을 설명합니다. 암호 또는 인증서를 사용하여 응용 프로그램을 인증하는 방법을 보여 줍니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# Azure PowerShell을 사용하여 리소스에 액세스하는 Active Directory 응용 프로그램 만들기

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [포털](resource-group-create-service-principal-portal.md)


이 항목에서는 [Azure PowerShell](powershell-install-configure.md)을 자동화된 프로세스, 응용 프로그램 또는 서비스와 같이 다른 구독의 리소스에 액세스할 수 있는 AD(Active Directory) 응용 프로그램을 만드는 방법을 보여 줍니다. Azure Resource Manager에서 역할 기반 액세스 제어를 사용하여 응용 프로그램에 대해 허용된 작업 권한을 부여할 수 있습니다.

이 문서에서는 AD 응용 프로그램 및 서비스 주체라는 두 개체를 만듭니다. AD 응용 프로그램은 앱이 등록되는 테넌트에 상주하며 실행할 프로세스를 정의합니다. 서비스 주체는 AD 응용 프로그램의 ID를 포함하고 사용 권한을 할당하는 데 사용됩니다. AD 응용 프로그램에서 많은 서비스 주체를 만들 수 있습니다. 응용 프로그램 및 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](./active-directory/active-directory-application-objects.md)를 참조하세요. Active Directory 인증에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](./active-directory/active-directory-authentication-scenarios.md)를 참조하세요.

응용 프로그램을 인증하기 위한 2가지 옵션이 있습니다.

 - 암호 - 실행 중에 사용자가 대화형으로 로그인하는 경우 적합합니다.
 - 인증서 - 사용자 상호 작용 없이 인증해야 하는 무인 모드 스크립트에 적합합니다.

## 암호를 사용하여 AD 응용 프로그램 만들기

이 섹션에서는 암호를 사용하여 AD 응용 프로그램을 만드는 단계를 수행합니다.

1. 계정에 로그인합니다.

        Add-AzureRmAccount

1. 응용 프로그램의 표시 이름, 응용 프로그램을 설명하는 페이지에 대한 링크(이 링크는 확인되지 않음), 응용 프로그램을 식별하는 URI, 응용 프로그램 ID에 대한 암호를 제공하여 새 Active Directory 응용 프로그램을 만듭니다.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     새 응용 프로그램 개체를 검사합니다.

        $azureAdApplication
        
     특히 서비스 주체 만들기, 역할 할당 및 액세스 토큰 획득을 위해서는 **ApplicationId** 속성이 필요합니다.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}


### 서비스 주체를 만들고 역할에 할당합니다.

AD 응용 프로그램에서 서비스 주체를 만들고 역할을 할당해야 합니다.

1. Active Directory 응용 프로그램의 응용 프로그램 ID를 전달하여 응용 프로그램에 대한 서비스 주체를 만듭니다.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. 서비스 주체에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 주체에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 제공합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### PowerShell을 통해 수동으로 자격 증명 제공

해당 응용 프로그램에 대한 Active Directory 응용 프로그램 및 서비스 주체를 만들었습니다. 역할에 서비스 주체를 할당했습니다. 이제, 응용 프로그램으로 로그인하여 작업을 수행해야 합니다. 주문형 스크립트 또는 명령을 실행할 경우 응용 프로그램에 대한 자격 증명을 수동으로 제공할 수 있습니다.

1. **Get-Credential** 명령을 실행하여 자격 증명을 포함하는 새 **PSCredential** 개체를 만듭니다.

        $creds = Get-Credential

2. 자격 증명을 입력하라는 메시지가 표시됩니다. 사용자 이름의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 사용합니다. 암호의 경우 계정을 만들 때 지정한 암호를 사용합니다.

     ![자격 증명 입력](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

3. 역할 할당을 만든 구독을 검색합니다. 서비스 주체의 역할 할당이 상주하는 테넌트의 **TenantId**를 가져오는 데 이 구독을 사용합니다.

        $subscription = Get-AzureRmSubscription

     계정이 하나 이상의 구독에 연결된 경우 구독 이름 또는 ID를 지정하여 사용하려는 구독을 가져옵니다.
     
        $subscription = Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate"

4. 이 계정이 서비스 주체임을 지정하고 자격 증명 개체를 제공하여 서비스 주체로 로그인합니다.

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId
        
     이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

5. 이후 세션에서 현재 액세스 토큰을 사용하기 위해 프로필을 저장할 수 있습니다.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     프로필을 열고 해당 내용을 검사할 수 있습니다. 액세스 토큰을 포함하는지 확인합니다.
        
6. 다시 로그인하는 대신 다음에 코드를 서비스 주체로 실행하려면 프로필을 로드합니다.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] 토큰이 유효한 한 저장된 프로필을 사용해야만 작동하므로 액세스 토큰이 만료됩니다. 영구적으로 무인 모드 스크립트를 실행하려면 인증서를 사용합니다.
        
## 인증서를 사용하여 AD 응용 프로그램 만들기

이 섹션에서는 인증서를 사용하여 AD 응용 프로그램을 만드는 단계를 수행합니다.

1. 자체 서명된 인증서를 만듭니다. Windows 10 또는 Windows Server 2016 Technical Preview 사용자라면 다음 명령을 실행합니다. 

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     지문을 포함하는 인증서에 대한 정보를 받게 됩니다.
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

     Windows 10 또는 Windows Server 2016 Technical Preview 사용자가 아니라면 [Self-signed certificate generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6) PowerShell 스크립트를 다운로드합니다. 다음 명령을 실행하여 인증서를 생성합니다.
     
        Import-Module -Name c:\New-SelfSignedCertificateEx.ps1
        New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
        $cert = Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp

2. 인증서에서 키 값을 검색합니다.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

3. Azure 계정에 로그인합니다.

        Add-AzureRmAccount

4. 디렉터리에서 응용 프로그램을 만듭니다.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
        
    새 응용 프로그램 개체를 검사합니다.

        $azureAdApplication

    서비스 주체 만들기, 역할 할당 및 액세스 토큰 획득을 위해서는 **ApplicationId** 속성이 필요합니다.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}    


### 서비스 주체를 만들고 역할에 할당합니다.

1. Active Directory 응용 프로그램의 응용 프로그램 ID를 전달하여 응용 프로그램에 대한 서비스 주체를 만듭니다.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. 서비스 주체에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 주체에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 제공합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### 스크립트에 대한 값을 준비합니다.

스크립트에서 서비스 주체로 로그인하는 데 필요한 세 가지 값을 전달합니다. 다음 정보가 필요합니다.

- 응용 프로그램 ID
- 테넌트 ID 
- 인증서 지문

이전 단계에서 응용 프로그램 ID 및 인증서 지문을 확인했습니다. 그러나 나중에 이러한 값을 검색해야 하는 경우 명령은 아래와 같이 테넌트 ID를 가져오는 명령과 함께 표시됩니다.

1. 테넌트 ID를 검색하려면 다음을 사용합니다.

        (Get-AzureRmSubscription).TenantId 

    또는 구독이 두 개 이상인 경우 구독 이름을 지정합니다.

        (Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate").TenantId
        
2. 응용 프로그램 ID를 검색하려면 다음을 사용합니다.

        (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
3. 인증서 지문을 검색하려면 다음을 사용합니다.

        (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

### 자동화된 PowerShell 스크립트를 통해 인증서 제공

해당 응용 프로그램에 대한 Active Directory 응용 프로그램 및 서비스 주체를 만들었습니다. 역할에 서비스 주체를 할당했습니다. 이제 서비스 주체로 작업을 수행하는 서비스 주체로 로그인해야 합니다.

스크립트에서 인증하려면 계정이 서비스 주체인지를 지정하고 인증서 지문, 응용 프로그램 ID 및 테넌트 ID를 제공합니다.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint {thumbprint} -ApplicationId {applicationId} -TenantId {tenantid}

이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

## 다음 단계
  
- .NET 인증 예제의 경우 [.NET용 Azure Resource Manager SDK](resource-manager-net-sdk.md)를 참조하세요.
- Java 인증 예제의 경우 [Java용 Azure Resource Manager SDK](resource-manager-java-sdk.md)를 참조하세요. 
- Python 인증 예제의 경우 [Python에 대한 리소스 관리 인증](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html)을 참조하세요.
- REST 인증 예제의 경우 [REST APIs용 Resource Manager SDK](resource-manager-rest-api.md)를 참조하세요.
- 리소스 관리를 위해 Azure에 응용 프로그램을 통합하는 자세한 단계를 보려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.

<!---HONumber=AcomDC_0615_2016-->
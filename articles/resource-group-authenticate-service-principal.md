<properties
   pageTitle="PowerShell을 사용하여 Azure 서비스 주체 만들기 | Microsoft Azure"
   description="Azure PowerShell을 사용하여 Active Directory 응용 프로그램 및 서비스 주체를 만들고 역할 기반 액세스 제어를 통해 리소스에 대한 액세스를 부여하는 방법을 설명합니다. 암호 또는 인증서를 사용하여 응용 프로그램을 인증하는 방법을 보여 줍니다."
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
   ms.date="07/18/2016"
   ms.author="tomfitz"/>

# Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [포털](resource-group-create-service-principal-portal.md)

리소스에 액세스해야 하는 응용 프로그램이나 스크립트가 있는 경우 이 프로세스를 사용자의 자격 증명에 따라 실행하지 않으려고 할 수 있습니다. 해당 사용자가 프로세스에 할당하려고 하는 권한과 다른 권한을 가질 수 있으며 사용자의 역할 책임도 달라질 수 있기 때문입니다. 대신 인증 자격 증명 및 역할 할당을 포함하는 응용 프로그램에 대한 ID를 만들 수 있습니다. 응용 프로그램은 실행될 때마다 이 ID로 로그인합니다. 이 항목에서는 [Azure PowerShell](powershell-install-configure.md)을 사용하여 응용 프로그램을 자체 자격 증명 및 ID로 실행하는 데 필요한 모든 항목을 설정하는 방법을 보여 줍니다.

이 문서에서는 AD(Active Directory) 응용 프로그램 및 서비스 주체라는 두 개체를 만듭니다. AD 응용 프로그램에는 자격 증명(응용 프로그램 ID 및 암호 또는 인증서)이 포함되어 있습니다. 서비스 주체에는 역할 할당이 포함되어 있습니다. AD 응용 프로그램에서 많은 서비스 주체를 만들 수 있습니다. 이 항목에서는 응용 프로그램을 하나의 조직 내에서만 실행하게 되는 단일 테넌트 응용 프로그램을 중점적으로 다룹니다. 일반적으로 단일 조직 내에서 실행되는 LOB(기간 업무) 응용 프로그램에 대해 단일 테넌트 응용 프로그램을 사용하게 됩니다. 많은 조직에서 응용 프로그램을 실행해야 하는 경우 다중 테넌트 응용 프로그램을 만들 수도 있습니다. 일반적으로 SaaS(software-as-a-service) 응용 프로그램에 대해 다중 테넌트 응용 프로그램을 사용합니다. 다중 테넌트 응용 프로그램을 설정하려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.

Active Directory를 사용할 때 이해해야 할 여러 가지 개념이 있습니다. 응용 프로그램 및 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](./active-directory/active-directory-application-objects.md)를 참조하세요. Active Directory 인증에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](./active-directory/active-directory-authentication-scenarios.md)를 참조하세요.

PowerShell을 사용하는 경우 AD 응용 프로그램을 인증하기 위한 다음 2가지 옵션이 있습니다.

 - password
 - 인증서

AD 응용 프로그램을 설정한 다음 다른 프로그래밍 프레임워크(예: Python, Ruby 또는 Node.js)에서 Azure에 로그인하려는 경우 암호 인증이 가장 좋은 방법일 수 있습니다. 암호 또는 인증서 중 어떤 방법을 사용할지 결정하기 전에 [샘플 응용 프로그램](#sample-applications) 섹션에서 여러 다른 프레임워크에서 인증하는 방법의 예를 참조하세요.

## 테넌트 ID 가져오기

서비스 주체로 로그인할 때마다 AD 앱에 디렉터리의 테넌트 ID를 제공해야 합니다. 테넌트는 Active Directory의 인스턴스입니다. 암호 또는 인증서 인증을 위해 해당 값이 필요하므로 이제부터 해당 값을 구해 보겠습니다.

1. 계정에 로그인합니다.

        Add-AzureRmAccount

2. 구독이 하나만 있는 경우 다음을 사용할 수 있습니다.

        $tenant = (Get-AzureRmSubscription).TenantId
    
     구독이 둘 이상 있는 경우 AD 앱에 사용할 구독을 지정합니다. Active Directory가 있는 구독을 선택합니다. 자세한 내용은 [Azure AD 디렉터리 관리](./active-directory/active-directory-administer.md)를 참조하세요.

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

이제 [암호](#create-service-principal-with-password) 또는 [인증서](#create-service-principal-with-certificate) 인증에 대한 아래 섹션을 계속 진행합니다.

## 암호를 사용하여 서비스 주체 만들기

이 섹션에서는 암호를 사용하여 AD 응용 프로그램 및 서비스 주체를 만드는 단계를 수행합니다.

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


     AD 응용 프로그램에서 서비스 주체를 만들고 역할을 할당해야 합니다.

2. Active Directory 응용 프로그램의 응용 프로그램 ID를 전달하여 응용 프로그램에 대한 서비스 주체를 만듭니다.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

3. 서비스 주체에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 주체에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 제공합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요. 역할을 할당하려면 [소유자](./active-directory/role-based-access-built-in-roles.md#owner) 역할 또는 [사용자 액세스 관리자](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) 역할을 통해 부여된 `Microsoft.Authorization/*/Write` 액세스 권한이 있어야 합니다.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

이것으로 끝입니다. AD 응용 프로그램 및 서비스 주체가 설정되었습니다. 다음 섹션에서는 PowerShell을 통해 자격 증명으로 로그인하는 방법을 보여 줍니다. 그러나 코드 응용 프로그램에서 자격 증명을 사용하려는 경우 이 항목을 계속 진행할 필요가 없습니다. [샘플 응용 프로그램](#sample-applications)으로 이동하여 응용 프로그램 ID 및 암호를 사용하여 로그인하는 예제를 참조할 수 있습니다.

### PowerShell을 통해 자격 증명 제공

이제, 응용 프로그램으로 로그인하여 작업을 수행해야 합니다.

1. **Get-Credential** 명령을 실행하여 자격 증명을 포함하는 새 **PSCredential** 개체를 만듭니다. 이 명령을 실행하기 전에 붙여넣을 수 있게 **ApplicationId** 또는 **IdentifierUris**를 확인해야 합니다.

        $creds = Get-Credential

2. 자격 증명을 입력하라는 메시지가 표시됩니다. 사용자 이름의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 사용합니다. 암호의 경우 계정을 만들 때 지정한 암호를 사용합니다.

     ![자격 증명 입력](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

4. 이 계정이 서비스 주체임을 지정하고 자격 증명 개체를 제공하여 서비스 주체로 로그인합니다. [테넌트 ID 가져오기](#get-tenant-id)에서 검색한 테넌트 ID가 필요합니다.

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

5. 이후 세션에서 현재 액세스 토큰을 사용하기 위해 프로필을 저장할 수 있습니다.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     프로필을 열고 해당 내용을 검사할 수 있습니다. 액세스 토큰을 포함하는지 확인합니다.
        
6. 수동으로 다시 로그인하는 대신 다음에 코드를 서비스 주체로 실행하려면 프로필을 로드합니다.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] 토큰이 유효한 한 저장된 프로필을 사용해야만 작동하므로 액세스 토큰이 만료됩니다.
        
## 인증서를 사용하여 서비스 주체 만들기

이 섹션에서는 인증서를 사용하여 AD 응용 프로그램 및 서비스 주체를 만드는 단계를 수행합니다.

1. 자체 서명된 인증서를 만듭니다. **Windows 10 또는 Windows Server 2016 Technical Preview** 사용자라면 다음 명령을 실행합니다.

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     변수에는 지문을 포함하는 인증서에 대한 정보가 포함됩니다.
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

     Windows 10 또는 Windows Server 2016 Technical Preview가 **없는 경우** **New-SelfSignedCertificate** Cmdlet을 사용할 수 없습니다. 대신 [자체 서명된 인증서 생성기](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6) PowerShell 스크립트를 다운로드하고 다음 명령을 실행하여 인증서를 생성합니다. 이 단계는 이전 예제에서 인증서를 이미 만든 경우에는 필요하지 않습니다.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\New-SelfSignedCertificateEx.ps1
        New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
        $cert = Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp

2. 인증서에서 키 값을 검색합니다.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

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


5. Active Directory 응용 프로그램의 응용 프로그램 ID를 전달하여 응용 프로그램에 대한 서비스 주체를 만듭니다.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

6. 서비스 주체에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 주체에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 제공합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요. 역할을 할당하려면 [소유자](./active-directory/role-based-access-built-in-roles.md#owner) 역할 또는 [사용자 액세스 관리자](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) 역할을 통해 부여된 `Microsoft.Authorization/*/Write` 액세스 권한이 있어야 합니다.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

이것으로 끝입니다. AD 응용 프로그램 및 서비스 주체가 설정되었습니다. 다음 섹션에서는 PowerShell을 통해 인증서를 사용하여 로그인하는 방법을 보여 줍니다.

### 자동화된 PowerShell 스크립트를 통해 인증서 제공

스크립트에서 인증하려면 계정이 서비스 주체인지를 지정하고 인증서 지문, 응용 프로그램 ID 및 테넌트 ID를 제공합니다. 변수 **$azureAdApplication.ApplicationId**, **$cert.Thumbprint** 및 **$tenant**에 이미 이러한 값이 있습니다. 스크립트를 자동화하려면 이러한 값을 환경 변수로 저장하고 실행 중에 검색하거나 스크립트에 포함할 수 있습니다.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint 000000 -ApplicationId 000000 -TenantId 0000000

이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

나중에 응용 프로그램 ID를 검색해야 할 경우 다음을 사용합니다.

    (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
나중에 인증서 지문을 검색해야 할 경우 다음을 사용합니다.

    (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

나중에 테넌트 ID를 검색해야 할 경우 [테넌트 ID 가져오기](#get-tenant-id)를 참조하세요.

## 샘플 응용 프로그램

다음 예제 응용 프로그램에서는 서비스 주체로 로그인하는 방법을 보여 줍니다.

**.NET**

- [.NET에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [.NET을 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [리소스 사용 시작 - Azure Resource Manager 템플릿을 사용하여 배포 - Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [리소스 사용 시작 - 리소스 그룹 관리 - Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Python에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Python을 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

- [Node.js에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Node.js를 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Ruby에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Ruby를 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## 다음 단계
  
- 리소스 관리를 위해 Azure에 응용 프로그램을 통합하는 자세한 단계를 보려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.

<!---HONumber=AcomDC_0720_2016-->
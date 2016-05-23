<properties
   pageTitle="Azure Resource Manager를 사용하여 서비스 주체 인증 | Microsoft Azure"
   description="역할 기반 액세스 제어를 통해 서비스 주체에게 액세스 권한을 부여하고 서비스 주체를 인증하는 방법을 설명합니다. PowerShell 및 Azure CLI를 사용하여 이러한 작업을 수행하는 방법을 보여 줍니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="tomfitz"/>

# Azure 리소스 관리자를 사용하여 서비스 주체 인증

이 항목에서는 서비스 주체(예: 자동화된 프로세스, 응용 프로그램 또는 서비스)에게 구독 내의 다른 리소스에 액세스하도록 허용하는 방법을 보여 줍니다. Azure 리소스 관리자에서 역할 기반 액세스 제어를 사용하여 서비스 주체에게 허용된 작업을 수락하고 서비스 주체를 인증할 수 있습니다.

이 항목에서는 Mac, Linux 및 Windows용 Azure PowerShell 또는 Azure CLI를 사용하여 응용 프로그램 및 서비스 주체를 만들고 서비스 주체에 역할을 할당하고 서비스 주체로 인증하는 방법을 보여 줍니다. Azure PowerShell을 설치하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](./powershell-install-configure.md)을 참조하세요. Azure CLI를 설치하지 않은 경우 [Azure CLI 설치 및 구성](xplat-cli-install.md)을 참조하세요. 포털을 사용하여 이러한 단계를 수행하는 방법에 대한 자세한 내용은 [포털을 사용하여 Active Directory 응용 프로그램 및 서비스 주체 만들기](resource-group-create-service-principal-portal.md)를 참조하세요.

## 개념
1. Azure Active Directory - 클라우드에 대한 ID 및 액세스 관리 서비스입니다. 자세한 내용은 [Azure Active Directory란](active-directory/active-directory-whatis.md)을 참조하세요.
2. 서비스 주체 - 다른 리소스에 액세스해야 하는 디렉터리의 응용 프로그램 인스턴스입니다.
3. Active Directory 응용 프로그램 - AAD에 응용 프로그램을 식별하는 디렉터리 레코드입니다.

응용 프로그램 및 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](active-directory/active-directory-application-objects.md)를 참조하세요. Active Directory 인증에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](active-directory/active-directory-authentication-scenarios.md)를 참조하세요.

이 항목에서는 Active Directory 응용 프로그램을 만들고 해당 응용 프로그램을 인증하기 위한 4개의 경로를 보여 줍니다. 경로는 인증에 암호 또는 인증서를 사용하려는지 여부 및 PowerShell 또는 Azure CLI를 사용하려는지 여부에 따라 다릅니다. 경로는 다음과 같습니다.

- [암호를 사용하여 인증 - PowerShell](#authenticate-with-password---powershell)
- [인증서를 사용하여 인증 - PowerShell](#authenticate-with-certificate---powershell)
- [암호를 사용하여 인증 - Azure CLI](#authenticate-with-password---azure-cli)
- [인증서를 사용하여 인증 - Azure CLI](#authenticate-with-certificate---azure-cli)

## 암호를 사용하여 인증 - PowerShell

이 섹션에서는 Azure Active Directory 응용 프로그램용 서비스 주체를 만들고, 서비스 주체에 역할을 할당하고, 응용 프로그램 식별자와 암호를 제공하여 서비스 주체로 인증하는 단계를 수행합니다.

1. 계정에 로그인합니다.

        PS C:\> Login-AzureRmAccount

1. **New-AzureRmADApplication** 명령을 실행하여 새 Active Directory 응용 프로그램을 만듭니다. 응용 프로그램의 표시 이름, 응용 프로그램을 설명하는 페이지에 대한 링크(이 링크는 확인되지 않음), 응용 프로그램을 식별하는 URI, 응용 프로그램 ID에 대한 암호를 제공합니다.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     새 응용 프로그램 개체를 검사합니다. 서비스 주체 만들기, 역할 할당 및 액세스 토큰 획득을 위해서는 **ApplicationId** 속성이 필요합니다.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}

2. Active Directory 응용 프로그램의 응용 프로그램 ID를 전달하여 응용 프로그램에 대한 서비스 주체를 만듭니다.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     이제 디렉터리에서 서비스 주체를 만들었지만, 아직은 서비스에 할당된 권한 또는 범위가 없습니다. 서비스 주체에게 일부 범위에서 작업을 수행할 수 있는 권한을 명시적으로 부여해야 합니다.

3. 서비스 주체에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 주체에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 제공합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

해당 응용 프로그램에 대한 Active Directory 응용 프로그램 및 서비스 주체를 만들었습니다. 역할에 서비스 주체를 할당했습니다. 이제 서비스 주체로 작업을 수행하는 서비스 주체로 로그인해야 합니다. 세 가지 옵션은 이 항목에 나와 있습니다.

- [PowerShell을 통해 수동으로 자격 증명 제공](#manually-provide-credentials-through-powershell)
- [자동화된 PowerShell 스크립트를 통해 자격 증명 제공](#provide-credentials-through-automated-powershell-script)
- [응용 프로그램의 코드를 통해 자격 증명 제공](#provide-credentials-through-code-in-an-application)

<a id="manually-provide-credentials-through-powershell" />
### PowerShell을 통해 수동으로 자격 증명 제공

주문형 스크립트 또는 명령을 실행할 때 서비스 주체에 대한 자격 증명을 수동으로 제공할 수 있습니다.

1. **Get-Credential** 명령을 실행하여 자격 증명을 포함하는 새 **PSCredential** 개체를 만듭니다.

        PS C:\> $creds = Get-Credential

2. 자격 증명을 입력하라는 메시지가 표시됩니다. 사용자 이름의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 사용합니다. 암호의 경우 계정을 만들 때 지정한 암호를 사용합니다.

     ![자격 증명 입력][1]

3. 역할 할당을 만든 구독을 검색합니다. 이 구독은 서비스 주체의 역할이 있는 테넌트의 **TenantId**를 가져오는 데 사용됩니다.

        PS C:\> $subscription = Get-AzureRmSubscription

     현재 선택된 구독이 아닌 다른 구독에서 역할 할당을 만든 경우 **SubscriptoinId** 또는 **SubscriptionName** 매개 변수를 지정하여 다른 구독을 검색할 수 있습니다.

4. **Login-AzureRmAccount** cmdlet을 사용하여 서비스 주체로 로그인하지만 자격 증명 개체를 제공하고 이 계정이 서비스 주체인지를 지정합니다.

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

<a id="provide-credentials-through-automated-powershell-script" />
### 자동화된 PowerShell 스크립트를 통해 자격 증명 제공

이 섹션에서는 수동으로 자격 증명을 입력하지 않고도 서비스 주체로 로그인하는 방법을 보여 줍니다. 키 자격 증명 모음에서 검색되기 때문에 수동으로 암호를 제공할 필요가 없습니다.

> [AZURE.NOTE] PowerShell 스크립트에 직접 암호를 포함하는 것은 암호가 텍스트로 노출되기 때문에 안전하지 않습니다. 대신 키 자격 증명 모음과 같은 서비스를 사용하여 암호를 저장하고 스크립트를 실행할 때 검색합니다.

이러한 단계에서는 키 자격 증명 모음과 암호를 저장하는 암호를 설정했다고 가정합니다. 템플릿을 통해 키 자격 증명 모음 및 암호를 배포하려면 [키 자격 증명 모음 템플릿 형식]()을 참조하세요. 키 자격 증명 모음에 대해 알아보려면 [Azure 키 자격 증명 모음 시작](./key-vault/key-vault-get-started.md)을 참조하세요.

1. 키 자격 증명 모음에서 암호를 검색합니다(아래 예제에서는 **appPassword**라는 이름으로 암호로 저장).

        PS C:\> $secret = Get-AzureKeyVaultSecret -VaultName examplevault -Name appPassword
        
2. Active Directory 응용 프로그램을 가져옵니다. 로그인할 때 응용 프로그램 ID가 필요합니다.

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"

3. 자격 증명으로 응용 프로그램 ID 및 암호를 제공하여 새 **PSCredential** 개체를 만듭니다.

        PS C:\> $creds = New-Object System.Management.Automation.PSCredential ($azureAdApplication.ApplicationId, $secret.SecretValue)
    
4. 역할 할당을 만든 구독을 검색합니다. 이 구독은 나중에 서비스 주체의 역할이 있는 테넌트의 **TenantId**를 가져오는 데 사용됩니다.

        PS C:\> $subscription = Get-AzureRmSubscription

     현재 선택된 구독이 아닌 다른 구독에서 역할 할당을 만든 경우 **SubscriptoinId** 또는 **SubscriptionName** 매개 변수를 지정하여 다른 구독을 검색할 수 있습니다.

5. **Login-AzureRmAccount** cmdlet을 사용하여 서비스 주체로 로그인하지만 자격 증명 개체를 제공하고 이 계정이 서비스 주체인지를 지정합니다.
    
        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId

이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

<a id="provide-credentials-through-code-in-an-application" />
### 응용 프로그램의 코드를 통해 자격 증명 제공

.NET 응용 프로그램에서 인증하려면 다음 코드를 포함합니다. Active Directory 응용 프로그램에 대한 응용 프로그램 ID, 암호 및 구독에 대한 테넌트 ID가 필요합니다. 액세스 토큰을 검색한 후 구독에서 리소스와 함께 사용할 수 있습니다.

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }


## 인증서를 사용하여 인증 - PowerShell

이 섹션에서는 Azure Active Directory 응용 프로그램용 서비스 주체를 만들고, 서비스 주체에 역할을 할당하고, 인증서를 제공하여 서비스 주체로 인증하는 단계를 수행합니다. 이 항목에서는 인증서가 발급되었다고 가정합니다.

인증서로 작업하는 두 가지 방법, 키 자격 증명 및 값을 보여 줍니다. 두 가지 방법 중 하나를 사용할 수 있습니다.

첫째, 응용 프로그램을 만들 때 나중에 사용할 PowerShell의 일부 값을 설정해야 합니다.

1. 계정에 로그인합니다.

        PS C:\> Login-AzureRmAccount

1. 두 방법 모두에 대해, 인증서에서 X509Certificate2 개체를 만들고 키 값을 검색합니다. 인증서 경로 및 인증서 암호를 사용합니다.

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. 키 자격 증명을 사용하는 경우 키 자격 증명 개체를 만들고 그 값을 이전 단계의 `$keyValue`로 설정합니다. 아래 예제는 `Add-Type` 호출을 포함하여 어셈블리에서 형식을 추가합니다. 예제에 표시된 경로는 사용자의 경로와 유사해야 하지만 약간 다를 수 있습니다.

        Add-Type -Path 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ResourceManager\AzureResourceManager\AzureRM.Resources\Microsoft.Azure.Commands.Resources.dll'
        $currentDate = Get-Date
        $endDate = $currentDate.AddYears(1)
        $keyId = [guid]::NewGuid()
        $keyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $keyCredential.StartDate = $currentDate
        $keyCredential.EndDate= $endDate
        $keyCredential.KeyId = $keyId
        $keyCredential.Type = "AsymmetricX509Cert"
        $keyCredential.Usage = "Verify"
        $keyCredential.Value = $keyValue

3. 디렉터리에서 응용 프로그램을 만듭니다. 첫 번째 명령은 키 값을 사용하는 방법을 보여줍니다.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    또는 두 번째 예제를 사용하여 키 자격 증명을 할당합니다.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "example" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyCredentials $keyCredential

    새 응용 프로그램 개체를 검사합니다. 서비스 주체 만들기, 역할 할당 및 액세스 토큰 획득을 위해서는 **ApplicationId** 속성이 필요합니다.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}       

4. Active Directory 응용 프로그램의 응용 프로그램 ID를 전달하여 응용 프로그램에 대한 서비스 주체를 만듭니다.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    이제 디렉터리에서 서비스 주체를 만들었지만, 아직은 서비스에 할당된 권한 또는 범위가 없습니다. 서비스 주체에게 일부 범위에서 작업을 수행할 수 있는 권한을 명시적으로 부여해야 합니다.

5. 서비스 주체에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 주체에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 제공합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

해당 응용 프로그램에 대한 Active Directory 응용 프로그램 및 서비스 주체를 만들었습니다. 역할에 서비스 주체를 할당했습니다. 이제 서비스 주체로 작업을 수행하는 서비스 주체로 로그인해야 합니다. 두 가지 옵션은 이 항목에 나와 있습니다.

- [자동화된 PowerShell 스크립트를 통해 인증서 제공](#provide-certificate-through-automated-powershell-script)
- [응용 프로그램의 코드를 통해 인증서 제공](#provide-certificate-through-code-in-an-application)

<a id="provide-certificate-through-automated-powershell-script" />
### 자동화된 PowerShell 스크립트를 통해 인증서 제공

1. Active Directory 응용 프로그램을 가져옵니다. 로그인할 때 응용 프로그램 ID가 필요합니다.

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"
        
2. 역할 할당을 만든 구독을 검색합니다. 이 구독은 나중에 서비스 주체의 역할이 있는 테넌트의 **TenantId**를 가져오는 데 사용됩니다.

        PS C:\> $subscription = Get-AzureRmSubscription

     현재 선택된 구독이 아닌 다른 구독에서 역할 할당을 만든 경우 **SubscriptoinId** 또는 **SubscriptionName** 매개 변수를 지정하여 다른 구독을 검색할 수 있습니다.

3. 인증에 사용할 인증서를 가져옵니다.

        PS C:\> $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")

4. PowerShell을 인증하려면 인증서 지문, 응용 프로그램 ID 및 테넌트 ID를 제공합니다.

        PS C:\> Login-AzureRmAccount -CertificateThumbprint $cert.Thumbprint -ApplicationId $azureAdApplication.ApplicationId -ServicePrincipal -TenantId $subscription.TenantId

이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

<a id="provide-certificate-through-code-in-an-application" />
### 응용 프로그램의 코드를 통해 인증서 제공

.NET 응용 프로그램에서 인증하려면 다음 코드를 포함합니다. 클라이언트를 검색한 후 구독에서 리소스에 액세스할 수 있습니다.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred);
    // If using the new resource manager package like "Microsoft.Azure.ResourceManager" version="1.0.0-preview" use below
    var creds = new TokenCredentials(token.AccessToken); 
    // Else if using older package versions like Microsoft.Azure.Management.Resources" version="3.4.0-preview" use below
    // var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken);
    var client = new ResourceManagementClient(creds); 
        

## 암호를 사용하여 인증 - Azure CLI

먼저 서비스 주체를 만듭니다. 이렇게 하려면 디렉터리에서 응용 프로그램 만들기를 사용해야 합니다. 이 섹션에서는 디렉터리에서 새 응용 프로그램을 만드는 방법을 안내합니다.

1. Azure 리소스 관리자 모드로 전환한 다음 계정에 로그인합니다.

        azure config mode arm
        azure login

2. **azure ad app create** 명령을 실행하여 새 Active Directory 응용 프로그램을 만듭니다. 응용 프로그램의 표시 이름, 응용 프로그램을 설명하는 페이지에 대한 링크(이 링크는 확인되지 않음), 응용 프로그램을 식별하는 URI, 응용 프로그램 ID에 대한 암호를 제공합니다.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Active Directory 응용 프로그램이 반환됩니다. 서비스 주체 만들기, 역할 할당 및 액세스 토큰 획득을 위해서는 AppId 속성이 필요합니다.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

3. 응용 프로그램에 대한 서비스 주체를 만듭니다. 이전 단계에서 반환된 응용 프로그램 id를 제공합니다.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    새 서비스 주체가 반환됩니다. 사용 권한을 부여할 때 개체 ID가 필요합니다.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

    이제 디렉터리에서 서비스 주체를 만들었지만, 아직은 서비스에 할당된 권한 또는 범위가 없습니다. 서비스 주체에게 일부 범위에서 작업을 수행할 수 있는 권한을 명시적으로 부여해야 합니다.

4. 서비스 주체에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 주체에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

해당 응용 프로그램에 대한 Active Directory 응용 프로그램 및 서비스 주체를 만들었습니다. 역할에 서비스 주체를 할당했습니다. 이제 서비스 주체로 작업을 수행하는 서비스 주체로 로그인해야 합니다. 세 가지 옵션은 이 항목에 나와 있습니다.

- [Azure CLI를 통해 수동으로 자격 증명 제공](#manually-provide-credentials-through-azure-cli)
- [자동화된 Azure CLI 스크립트를 통해 자격 증명 제공](#provide-credentials-through-automated-azure-cli-script)
- [응용 프로그램의 코드를 통해 자격 증명 제공](#provide-credentials-through-code-in-an-application-cli)

<a id="manually-provide-credentials-through-Azure-cli" />
### Azure CLI를 통해 수동으로 자격 증명 제공

서비스 주체로 수동으로 로그인하려는 경우 **azure login** 명령을 사용할 수 있습니다. 테넌트 ID, 응용 프로그램 ID 및 암호를 제공해야 합니다. 스크립트에 직접 암호를 포함하는 것은 암호가 파일에 저장되기 때문에 안전하지 않습니다. 자동화된 스크립트를 실행할 때 더 나은 옵션에 대해 다음 섹션을 참조하세요.

1. 서비스 주체를 포함하는 구독에 대해 **TenantId**를 확인합니다. 현재 인증된 구독에 대한 테넌트 ID를 검색하는 경우 매개 변수로 구독 ID를 제공할 필요가 없습니다. **-r** 스위치는 따옴표 없이 값을 검색합니다.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. 사용자 이름의 경우 서비스 주체를 만들 때 사용한 **AppId**를 사용합니다. 응용 프로그램 ID를 검색해야 할 경우 다음 명령을 사용합니다. **search** 매개 변수에 Active Directory 응용 프로그램의 이름을 제공합니다.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. 서비스 주체로 로그인합니다.

        azure login -u "$appId" --service-principal --tenant "$tenantId"

대화 상자가 나타나면 계정을 만들 때 지정한 암호를 제공합니다.

    info:    Executing command login
    Password: ********

이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

<a id="provide-credentials-through-automated-azure-cli-script" />
### 자동화된 Azure CLI 스크립트를 통해 자격 증명 제공

이 섹션에서는 수동으로 자격 증명을 입력하지 않고도 서비스 주체로 로그인하는 방법을 보여 줍니다.

> [AZURE.NOTE] Azure CLI 스크립트에 암호를 포함하는 것은 암호가 텍스트로 노출되기 때문에 안전하지 않습니다. 대신 키 자격 증명 모음과 같은 서비스를 사용하여 암호를 저장하고 스크립트를 실행할 때 검색합니다.

이러한 단계에서는 키 자격 증명 모음과 암호를 저장하는 암호를 설정했다고 가정합니다. 템플릿을 통해 키 자격 증명 모음 및 암호를 배포하려면 [키 자격 증명 모음 템플릿 형식]()을 참조하세요. 주요 자격 증명 모음에 대해 알아보려면 [Azure 주요 자격 증명 모음 시작](./key-vault/key-vault-get-started.md)을 참조하세요.

1. 키 자격 증명 모음에서 암호를 검색합니다(아래 예제에서는 **appPassword**라는 이름을 가진 암호로 저장). json 출력에서 반환되는 시작 및 끝 큰따옴표를 제거하는 **-r** 스위치를 포함합니다.

        secret=$(azure keyvault secret show --vault-name examplevault --secret-name appPassword --json | jq -r '.value')
    
2. 서비스 주체를 포함하는 구독에 대해 **TenantId**를 확인합니다. 현재 인증된 구독에 대한 테넌트 ID를 검색하는 경우 매개 변수로 구독 ID를 제공할 필요가 없습니다.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

3. 사용자 이름의 경우 서비스 주체를 만들 때 사용한 **AppId**를 사용합니다. 응용 프로그램 ID를 검색해야 할 경우 다음 명령을 사용합니다. **search** 매개 변수에 Active Directory 응용 프로그램의 이름을 제공합니다.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

4. 응용 프로그램 ID, 키 자격 증명 모음의 암호, 테넌트 ID를 제공하여 서비스 주체로 로그인합니다.

        azure login -u "$appId" -p "$secret" --service-principal --tenant "$tenantId"
    
이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

<a id="provide-credentials-through-code-in-an-application-cli" />
### 응용 프로그램의 코드를 통해 자격 증명 제공

.NET 응용 프로그램에서 인증하려면 다음 코드를 포함합니다. Active Directory 응용 프로그램에 대한 응용 프로그램 ID, 암호 및 구독에 대한 테넌트 ID가 필요합니다. 액세스 토큰을 검색한 후 구독에서 리소스와 함께 사용할 수 있습니다.

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }

## 인증서를 사용하여 인증 - Azure CLI

이 섹션에서는 인증에 인증서를 사용하는 Azure Active Directory 응용 프로그램에 대한 서비스 주체를 만드는 단계를 수행합니다. 이 항목에서는 인증서가 발급되었으며, [OpenSSL](http://www.openssl.org/)을 설치했다고 가정합니다.

1. 다음을 사용하여 **.pem** 파일을 만듭니다.

        openssl pkcs12 -in C:\certificates\examplecert.pfx -out C:\certificates\examplecert.pem -nodes

2. **.pem** 파일을 열고 인증서 데이터를 복사합니다. **---BEGIN CERTIFICATE---**와 **---END CERTIFICATE---** 사이의 긴 시퀀스 문자를 찾습니다.

3. **azure ad app create** 명령을 실행하여 새 Active Directory 응용 프로그램을 만들고, 이전 단계에서 키 값으로 복사한 인증서 데이터를 제공합니다.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    Active Directory 응용 프로그램이 반환됩니다. 서비스 주체 만들기, 역할 할당 및 액세스 토큰 획득을 위해서는 AppId 속성이 필요합니다.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

4. 응용 프로그램에 대한 서비스 주체를 만듭니다. 이전 단계에서 반환된 응용 프로그램 id를 제공합니다.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    새 서비스 주체가 반환됩니다. 사용 권한을 부여할 때 개체 ID가 필요합니다.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
5. 서비스 주체에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 주체에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

해당 응용 프로그램에 대한 Active Directory 응용 프로그램 및 서비스 주체를 만들었습니다. 역할에 서비스 주체를 할당했습니다. 이제 서비스 주체로 작업을 수행하는 서비스 주체로 로그인해야 합니다. 두 가지 옵션은 이 항목에 나와 있습니다.

- [자동화된 Azure CLI 스크립트를 통해 인증서 제공](#provide-certificate-through-automated-azure-cli-script)
- [응용 프로그램의 코드를 통해 인증서 제공](#provide-certificate-through-code-in-an-application-cli)

<a id="provide-certificate-through-automated-azure-cli-script" />
### 자동화된 Azure CLI 스크립트를 통해 인증서 제공

1. 인증서 지문을 검색하고 불필요한 문자를 제거해야 합니다.

        cert=$(openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g')
    
     다음과 유사한 지문 값을 반환합니다.

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. 서비스 주체를 포함하는 구독에 대해 **TenantId**를 확인합니다. 현재 인증된 구독에 대한 테넌트 ID를 검색하는 경우 매개 변수로 구독 ID를 제공할 필요가 없습니다. **-r** 스위치는 따옴표 없이 값을 검색합니다.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

3. 사용자 이름의 경우 서비스 주체를 만들 때 사용한 **AppId**를 사용합니다. 응용 프로그램 ID를 검색해야 할 경우 다음 명령을 사용합니다. **search** 매개 변수에 Active Directory 응용 프로그램의 이름을 제공합니다.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

4. Azure CLI를 인증하려면 인증서 지문, 인증서 파일, 응용 프로그램 ID 및 테넌트 ID를 제공합니다.

        azure login --service-principal --tenant "$tenantId" -u "$appId" --certificate-file C:\certificates\examplecert.pem --thumbprint "$cert"

이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

<a id="provide-certificate-through-code-in-an-application-cli" />
### 응용 프로그램의 코드를 통해 인증서 제공

.NET 응용 프로그램에서 인증하려면 다음 코드를 포함합니다. 클라이언트를 검색한 후 구독에서 리소스에 액세스할 수 있습니다.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
    // If using the new resource manager package like "Microsoft.Azure.ResourceManager" version="1.0.0-preview" use below
    var creds = new TokenCredentials(token.AccessToken); 
    // Else if using older package versions like Microsoft.Azure.Management.Resources" version="3.4.0-preview" use below
    // var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken);
    var client = new ResourceManagementClient(creds); 
       
인증서 및 Azure CLI 사용에 대한 자세한 내용은 [Linux 명령줄에서 Azure 서비스 보안 주체로 인증서 기반 인증](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)을 참조하세요.

## 다음 단계
  
- 서비스 주체로 포털 사용에 대한 자세한 내용은 [Azure 포털을 사용하여 새 Azure 서비스 주체 만들기](./resource-group-create-service-principal-portal.md)를 참조하세요.  
- Azure 리소스 관리자에서 보안 구현에 대한 지침은 [Azure 리소스 관리자에 대한 보안 고려 사항](best-practices-resource-manager-security.md)을 참조하세요.


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_0511_2016-->
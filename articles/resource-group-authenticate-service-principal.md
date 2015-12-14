<properties
   pageTitle="Azure 리소스 관리자를 사용하여 서비스 사용자 인증"
   description="역할 기반 액세스 제어를 통해 서비스 사용자에게 액세스 권한을 부여하고 서비스 사용자를 인증하는 방법을 설명합니다. PowerShell 및 Azure CLI를 사용하여 이러한 작업을 수행하는 방법을 보여 줍니다."
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
   ms.date="11/18/2015"
   ms.author="tomfitz"/>

# Azure 리소스 관리자를 사용하여 서비스 주체 인증

이 항목에서는 서비스 사용자(예: 자동화된 프로세스, 응용 프로그램 또는 서비스)에게 구독 내의 다른 리소스에 액세스하도록 허용하는 방법을 보여 줍니다. Azure 리소스 관리자에서 역할 기반 액세스 제어를 사용하여 서비스 사용자에게 허용된 작업을 수락하고 서비스 사용자를 인증할 수 있습니다. 이 항목에서는 PowerShell 및 Azure CLI를 사용하여 서비스 사용자에게 역할을 할당하고 서비스 사용자를 인증하는 방법을 보여 줍니다.

사용자 이름 및 암호 또는 인증서를 사용하여 인증하는 방법을 보여줍니다.

Mac, Linux 및 Windows용 Azure PowerShell 또는 Azure CLI 중 하나를 사용할 수 있습니다. Azure PowerShell을 설치하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](./powershell-install-configure.md)을 참조하세요. Azure CLI를 설치하지 않은 경우 [Azure CLI 설치 및 구성](xplat-cli-install.md)을 참조하세요. 포털을 사용하여 이러한 단계를 수행하는 방법에 대한 자세한 내용은 [포털을 사용하여 Active Directory 응용 프로그램 및 서비스 주체 만들기](resource-group-create-service-principal-portal.md)를 참조하세요.

## 개념
1. AAD(Azure Active Directory) - 클라우드에 대한 ID 및 액세스 관리 서비스입니다. 자세한 내용은 [Azure Active Directory란](active-directory/active-directory-whatis.md)을 참조하세요.
2. 서비스 사용자 - 다른 리소스에 액세스해야 하는 디렉터리의 응용 프로그램 인스턴스입니다.
3. AD 응용 프로그램 - AAD에 응용 프로그램을 식별하는 디렉터리 레코드입니다. 자세한 내용은 [Azure AD의 인증 기본 사항](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth)을 참조하세요.

## 암호로 서비스 주체 인증 - PowerShell

이 섹션에서는 Azure Active Directory 응용 프로그램용 서비스 주체를 만들고, 서비스 주체에 역할을 할당하고, 응용 프로그램 식별자와 암호를 제공하여 서비스 주체로 인증하는 단계를 수행합니다.

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

1. 계정에 로그인합니다.

        PS C:\> Login-AzureRmAccount

1. **New-AzureRmADApplication** 명령을 실행하여 새 AAD 응용 프로그램을 만듭니다. 응용 프로그램의 표시 이름, 응용 프로그램을 설명하는 페이지에 대한 링크(이 링크는 확인되지 않음), 응용 프로그램을 식별하는 URI, 응용 프로그램 ID에 대한 암호를 제공합니다.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     새 응용 프로그램 개체를 검사합니다. 서비스 사용자 만들기, 역할 할당 및 JWT 토큰 획득을 위해서는 **ApplicationId** 속성이 필요합니다.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}


2. 응용 프로그램에 대한 서비스 사용자를 만듭니다.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     이제 디렉터리에서 서비스 사용자를 만들었지만, 아직은 서비스에 할당된 권한 또는 범위가 없습니다. 서비스 사용자에게 일부 범위에서 작업을 수행할 수 있는 권한을 명시적으로 부여해야 합니다.

3. 서비스 사용자에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 사용자에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 제공합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [리소스에 대한 액세스 관리 및 감사](resource-group-rbac.md)를 참조하세요.

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. 역할 할당을 만든 구독을 검색합니다. 이 구독은 나중에 서비스 사용자의 역할이 있는 테넌트의 **TenantId**를 가져오는 데 사용됩니다.

        PS C:\> $subscription = Get-AzureRmSubscription

     현재 선택된 구독이 아닌 다른 구독에서 역할 할당을 만든 경우 **SubscriptoinId** 또는 **SubscriptionName** 매개 변수를 지정하여 다른 구독을 검색할 수 있습니다.

5. PowerShell을 통해 서비스 주체로 로그인하려면 **Get-Credential** 명령을 실행하여 자격 증명을 포함하는 새 **PSCredential** 개체를 만듭니다.

        PS C:\> $creds = Get-Credential

     자격 증명을 입력하라는 메시지가 표시됩니다.

     ![][1]

     사용자 이름의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 사용합니다. 암호의 경우 계정을 만들 때 지정한 암호를 사용합니다.

     **Login-AzureRmAccount** cmdlet에 입력한 자격 증명을 사용하여 서비스 주체를 로그인합니다.

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

     이제 사용자는 작성한 AAD 응용 프로그램에 대한 서비스 사용자로 인증됩니다.

6. 응용 프로그램에서 인증하려면 다음 .NET 코드를 포함합니다. 토큰을 검색한 후 구독에서 리소스에 액세스할 수 있습니다.

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



## 인증서로 서비스 주체 인증 - PowerShell

이 섹션에서는 Azure Active Directory 응용 프로그램용 서비스 주체를 만들고, 서비스 주체에 역할을 할당하고, 인증서를 제공하여 서비스 주체로 인증하는 단계를 수행합니다. 이 항목에서는 인증서가 발급되었다고 가정합니다.

인증서로 작업하는 두 가지 방법, 키 자격 증명 및 값을 보여 줍니다. 두 가지 방법 중 하나를 사용할 수 있습니다.

첫째, 응용 프로그램을 만들 때 나중에 사용할 PowerShell의 일부 값을 설정해야 합니다.

1. 계정에 로그인합니다.

        PS C:\> Login-AzureRmAccount

1. 두 방법 모두에 대해, 인증서에서 X509Certificate 개체를 만들고 키 값을 검색합니다. 인증서 경로 및 인증서 암호를 사용합니다.

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. 키 자격 증명을 사용하는 경우 키 자격 증명 개체를 만들고 그 값을 이전 단계의 `$keyValue`로 설정합니다.

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

        $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    또는 두 번째 예제를 사용하여 키 자격 증명을 할당합니다.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyCredentials $keyCredential

    새 응용 프로그램 개체를 검사합니다. 서비스 사용자 만들기, 역할 할당 및 JWT 토큰 획득을 위해서는 **ApplicationId** 속성이 필요합니다.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : 76fa8d97-f07e-4b9a-b871-a57a7acd777a
        ApplicationObjectId     : c36b7b57-a949-4401-b381-18a5210aff10
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in
                          user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "9f13c6c6-35ba-43d6-b8b3-6a87aa641388",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}

4. 응용 프로그램에 대한 서비스 사용자를 만듭니다.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    이제 디렉터리에서 서비스 사용자를 만들었지만, 아직은 서비스에 할당된 권한 또는 범위가 없습니다. 서비스 사용자에게 일부 범위에서 작업을 수행할 수 있는 권한을 명시적으로 부여해야 합니다.

5. 서비스 사용자에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 사용자에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 제공합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [리소스에 대한 액세스 관리 및 감사](resource-group-rbac.md)를 참조하세요.

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

6. 응용 프로그램에서 인증하려면 다음 .NET 코드를 포함합니다. 클라이언트를 검색한 후 구독에서 리소스에 액세스할 수 있습니다.

        string clientId = "<Application ID for your AAD app>"; 
        var subscriptionId = "<Your Azure SubscriptionId>"; 
        string tenant = "<Tenant id or tenant name>"; 

        var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

        X509Certificate2 cert = null; 
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
        string certName = "examplecert"; 
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
        var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
        var client = new ResourceManagementClient(creds); 
        

## 암호로 서비스 주체 인증 - Azure CLI

먼저 서비스 사용자를 만듭니다. 이렇게 하려면 디렉터리에서 응용 프로그램 만들기를 사용해야 합니다. 이 섹션에서는 디렉터리에서 새 응용 프로그램을 만드는 방법을 안내합니다.

1. Azure 리소스 관리자 모드로 전환한 다음 계정에 로그인합니다.

        azure config mode arm
        azure login

2. **Azure AD 앱 만들기** 명령을 실행하여 새 AAD 응용 프로그램을 만듭니다. 응용 프로그램의 표시 이름, 응용 프로그램을 설명하는 페이지에 대한 링크(이 링크는 확인되지 않음), 응용 프로그램을 식별하는 URI, 응용 프로그램 ID에 대한 암호를 제공합니다.

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    Azure AD 응용 프로그램이 반환됩니다. 서비스 사용자 만들기, 역할 할당 및 JWT 토큰 획득을 위해서는 ApplicationId 속성이 필요합니다.

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

3. 응용 프로그램에 대한 서비스 사용자를 만듭니다. 이전 단계에서 반환된 응용 프로그램 id를 제공합니다.

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    새 서비스 주체가 반환됩니다. 사용 권한을 부여할 때 개체 ID가 필요합니다.
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    이제 디렉터리에서 서비스 사용자를 만들었지만, 아직은 서비스에 할당된 권한 또는 범위가 없습니다. 서비스 사용자에게 일부 범위에서 작업을 수행할 수 있는 권한을 명시적으로 부여해야 합니다.

4. 서비스 사용자에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 사용자에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId** 또는 **IdentifierUris**를 제공합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [리소스에 대한 액세스 관리 및 감사](resource-group-rbac.md)를 참조하세요.

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

5. 계정을 나열하고 출력에서 **TenantId**를 조사하여 서비스 사용자의 역할 할당이 있는 테넌트의 **TenantId** 결정합니다.

        azure account list --json

6. 서비스 사용자를 ID로 사용하여 로그인합니다. 사용자 이름의 경우 응용 프로그램을 만들 때 사용한 **ApplicationId**를 사용합니다. 암호의 경우 계정을 만들 때 지정한 암호를 사용합니다.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    이제 사용자는 작성한 AAD 응용 프로그램에 대한 서비스 사용자로 인증됩니다.

## 인증서로 서비스 주체 인증 - Azure CLI

이 섹션에서는 인증에 인증서를 사용하는 Azure Active Directory 응용 프로그램에 대한 서비스 주체를 만드는 단계를 수행합니다. 이 항목에서는 인증서가 발급되었으며, [OpenSSL](http://www.openssl.org/)을 설치했다고 가정합니다.

1. 다음을 사용하여 **.pem** 파일을 만듭니다.

        openssl.exe pkcs12 -in examplecert.pfx -out examplecert.pem -nodes

2. **.pem** 파일을 열고 인증서 데이터를 복사합니다.

3. **azure ad app create** 명령을 실행하여 새 AAD 응용 프로그램을 만들고, 이전 단계에서 키 값으로 복사한 인증서 데이터를 제공합니다.

        azure ad app create -n "<your application name>" --home-page "<https://YourApplicationHomePage>" -i "<https://YouApplicationUri>" --key-value <certificate data>

## 다음 단계
  
- 역할 기반 액세스 제어에 대한 개요는 [리소스에 대한 액세스 관리 및 감사](resource-group-rbac.md)를 참조하세요.  
- 서비스 주체로 포털 사용에 대한 자세한 내용은 [Azure 포털을 사용하여 새 Azure 서비스 주체 만들기](./resource-group-create-service-principal-portal.md)를 참조하세요.  
- Azure 리소스 관리자에서 보안 구현에 대한 지침은 [Azure 리소스 관리자에 대한 보안 고려 사항](best-practices-resource-manager-security.md)을 참조하세요.


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_1203_2015-->
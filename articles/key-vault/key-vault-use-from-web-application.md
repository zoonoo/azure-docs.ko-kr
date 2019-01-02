---
title: 웹 애플리케이션에서 Azure Key Vault 사용 자습서 | Microsoft Docs
description: 이 자습서에서는 웹 애플리케이션에서 Azure Key Vault를 사용하는 방법을 알아볼 수 있습니다.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2018
ms.author: barclayn
ms.openlocfilehash: b66c9912ba0b6508c2beb786d2327efa779c6645
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079466"
---
# <a name="tutorial-use-azure-key-vault-from-a-web-application"></a>자습서: 웹 애플리케이션에서 Azure Key Vault 사용

이 자습서에서는 Azure의 웹 애플리케이션에서 Azure Key Vault를 사용하는 방법을 알아볼 수 있습니다. 웹 애플리케이션에서 사용할 Azure Key Vault의 암호에 액세스하는 과정을 보여줍니다. 그런 다음, 자습서는 프로세스를 빌드하고 클라이언트 암호 대신 인증서를 사용합니다. 이 자습서는 Azure에서 웹 애플리케이션을 만들기 위한 기본 사항을 잘 알고 있는 웹 개발자를 대상으로 합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * web.config 파일에 애플리케이션 설정 추가
> * 액세스 토큰을 가져오는 메서드 추가
> * 애플리케이션 시작 시 토큰 검색
> * 인증서 사용 인증

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 항목이 필요합니다.

* Azure Key Vault의 암호에 대한 URI
* 주요 자격 증명 모음에 액세스할 수 있는, Azure Active Directory에 등록된 웹 애플리케이션의 클라이언트 ID 및 클라이언트 암호
* 웹 애플리케이션. 이 자습서에서는 Azure에 웹앱으로 배포된 ASP.NET MVC 애플리케이션에 대한 단계를 보여줍니다.

[Azure Key Vault 시작](key-vault-get-started.md) 단계를 완료하여 암호, 클라이언트 ID, 클라이언트 암호에 대한 URI를 가져오고 응용 프로그램을 등록합니다. 웹 애플리케이션은 자격 증명 모음에 액세스하며 Azure Active Directory에 반드시 등록되어야 합니다. 또한 Key Vault에 대한 액세스 권한이 있어야 합니다. 그렇지 않으면 시작 자습서의 애플리케이션 등록으로 돌아가서 나열된 단계를 반복합니다. Azure Web Apps을 만드는 방법에 대한 자세한 내용은 [Web Apps 개요](../app-service/app-service-web-overview.md)를 참조하세요.

이 샘플은 Azure Active Directory ID를 수동으로 프로비전하는 방법에 따라 다릅니다. 하지만 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 대신 사용해야 합니다. 이 샘플은 Azure AD ID를 자동으로 프로비전합니다. 자세한 내용은 [GitHub의 샘플](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) 및 관련 [App Service 및 Functions 자습서](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity)를 참조하세요. Key Vault에 해당하는 [Key Vault에서 비밀을 읽도록 Azure 웹 애플리케이션 구성 자습서](tutorial-web-application-keyvault.md)를 참조할 수도 있습니다.

## <a id="packages"></a>NuGet 패키지 추가

웹 애플리케이션을 위해 설치해야 하는 2개의 패키지가 있습니다.

* Active Directory 인증 라이브러리 - Azure Active Directory를 조작하고 사용자 ID를 관리하기 위한 메서드를 포함합니다.
* Azure Key Vault 라이브러리 - Azure Key Vault를 조작하기 위한 메서드를 포함합니다.

패키지 관리자 콘솔에서 Install-Package 명령을 사용하여 이러한 패키지를 모두 설치할 수 있습니다.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory 
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>web.config 수정

다음과 같이 web.config 파일에 추가해야 하는 3개의 애플리케이션 설정이 있습니다. 보안 강화를 위해 Azure Portal에서 실제 값을 추가합니다.

```xml
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
    <!-- If you aren't hosting your app as an Azure Web App, then you should use the actual ClientId, Client Secret, and Secret URI values -->
```



## <a id="gettoken"></a>액세스 토큰을 가져오는 메서드 추가

주요 자격 증명 모음 API를 사용하려면 액세스 토큰이 필요합니다. 주요 자격 증명 모음 클라이언트가 주요 자격 증명 모음 API 호출을 처리하지만 액세스 토큰을 가져오는 함수를 제공해야 합니다. 다음 예제는 Azure Active Directory에서 액세스 토큰을 가져오는 코드입니다. 이 코드는 애플리케이션의 아무 곳에나 배치할 수 있습니다. 이 경우 Utils 또는 EncryptionHelper 클래스를 추가하겠습니다.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
// Using Client ID and Client Secret is a way to authenticate an Azure AD application.
// Using it in your web application allows for a separation of duties and more control over your key management. 
// However, it does rely on putting the Client Secret in your configuration settings.
// For some people, this can be as risky as putting the secret in your configuration settings.
```

## <a id="appstart"></a>응용 프로그램 시작 시 암호 검색

이제 주요 자격 증명 모음 API를 호출하고 암호를 검색하는 코드가 필요합니다. 다음 코드는 사용하기 전에 호출되기만 하면 아무 곳에나 배치할 수 있습니다. 이 경우 시작 시 한 번 실행되어 애플리케이션에서 암호를 사용할 수 있도록 Global.asax의 애플리케이션 시작 이벤트에 이 코드를 배치했습니다.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Azure Portal에서 앱 설정 추가

Azure 웹앱에서 이제 Azure Portal에서 AppSettings의 실제 값을 추가할 수 있습니다. 이 단계를 수행하면 실제 값이 web.config에 포함되지 않고 별도 액세스 제어 기능이 있는 포털을 통해 보호됩니다. 이러한 값은 web.config에 입력된 값을 대체합니다. 이름이 같아야 합니다.

![Azure Portal에 표시되는 애플리케이션 설정][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>클라이언트 암호 대신 인증서로 인증

이제 클라이언트 ID 및 클라이언트 암호를 사용하여 Azure AD 앱을 인증하는 방법을 이해했으므로 클라이언트 ID 및 인증서를 사용해 보겠습니다. Azure 웹앱에서 인증서를 사용하려면 다음 단계를 사용합니다.

1. 인증서 얻기 또는 만들기
2. 인증서를 Azure AD 애플리케이션에 연결
3. 인증서를 사용하도록 웹앱에 코드 추가
4. 웹앱에 인증서 추가

### <a name="get-or-create-a-certificate"></a>인증서 얻기 또는 만들기

 이 자습서에 대한 테스트 인증서를 만듭니다. 자체 서명된 인증서를 만드는 스크립트는 다음과 같습니다. 인증서 파일을 만들 디렉터리로 변경합니다.  인증서의 시작 및 종료 날짜는 현재 날짜 더하기 1년을 사용하면 됩니다.

```powershell
#Create self-signed certificate and export pfx and cer files 
$PfxFilePath = 'KVWebApp.pfx'
$CerFilePath = 'KVWebApp.cer'
$DNSName = 'MyComputer.Contoso.com'
$Password = 'MyPassword"'

$StoreLocation = 'CurrentUser' #be aware that LocalMachine requires elevated privileges
$CertBeginDate = Get-Date
$CertExpiryDate = $CertBeginDate.AddYears(1)

$SecStringPw = ConvertTo-SecureString -String $Password -Force -AsPlainText 
$Cert = New-SelfSignedCertificate -DnsName $DNSName -CertStoreLocation "cert:\$StoreLocation\My" -NotBefore $CertBeginDate -NotAfter $CertExpiryDate -KeySpec Signature
Export-PfxCertificate -cert $Cert -FilePath $PFXFilePath -Password $SecStringPw 
Export-Certificate -cert $Cert -FilePath $CerFilePath 
```

.pfx에 대한 종료 날짜와 암호를 메모해 둡니다(이 예제에서는 2019년 5월 15일 및 MyPassword). 아래 스크립트에서 필요합니다. 
### <a name="associate-the-certificate-with-an-azure-ad-application"></a>인증서를 Azure AD 애플리케이션에 연결

인증서를 만들었으니 이제 Azure AD 애플리케이션에 연결해야 합니다. PowerShell을 통해 연결을 완료할 수 있습니다. 다음 명령을 실행하여 인증서를 Azure AD 애플리케이션과 연결합니다.

```powershell
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

이러한 명령을 실행하면 Azure AD에서 애플리케이션을 볼 수 있습니다. 앱 등록을 검색할 때 검색 대화 상자에서 "모든 앱" 대신 **내 앱**을 선택해야 합니다. 

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>인증서를 사용하도록 웹앱에 코드 추가

이제 웹앱에 코드를 추가하여 인증서에 액세스하고 인증에 사용합니다. 

먼저 인증서에 액세스하는 코드가 있습니다. StoreLocation은 LocalMachine이 아닌, CurrentUser입니다. 테스트 인증서를 사용 중이므로 Find 메서드에 'false'를 입력합니다.

```cs
//Add this using statement
using System.Security.Cryptography.X509Certificates;  

public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```



다음은 CertificateHelper를 사용하고 인증에 필요한 ClientAssertionCertificate를 만드는 코드입니다.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

다음은 액세스 토큰을 가져오는 새로운 코드입니다. 이 코드는 앞의 예제에서 GetToken 메서드를 대체합니다. 편의상 다른 이름을 부여했습니다. 사용 편의를 위해 이 모든 코드를 웹앱 프로젝트의 Utils 클래스에 넣어 두었습니다.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```



마지막 코드 변경은 Application_Start 메서드입니다. 먼저 GetCert() 메서드를 호출하여 ClientAssertionCertificate를 로드해야 합니다. 그런 다음 새 KeyVaultClient를 만들 때 제공한 콜백 메서드를 변경합니다. 이 코드는 앞의 예제에서 사용한 코드를 대체합니다.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Azure Portal을 통해 웹앱에 인증서 추가

웹앱에 인증서를 추가하는 것은 간단한 두 단계 프로세스입니다. 먼저 Azure Portal로 이동하여 웹앱으로 이동합니다. 웹앱에 대한 설정에서 **SSL 설정**에 대한 항목을 클릭합니다. 항목을 열 때 앞의 예제에서 만든 인증서 KVWebApp.pfx를 업로드합니다. pfx에 대한 암호를 기억해야 합니다.

![Azure Portal에서 웹앱에 인증서 추가][2]

마지막으로 수행해야 하는 일은 애플리케이션 설정을 이름이 WEBSITE\_LOAD\_CERTIFICATES이고 값이 *인 웹앱에 추가하는 것입니다. 이 단계는 모든 인증서가 로드되었는지 확인합니다. 업로드한 인증서만 로드하려면 해당 지문의 쉼표로 구분된 목록을 입력하면 됩니다.


## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 자습서에 사용된 앱 서비스, 키 자격 증명 모음 및 Azure AD 애플리케이션을 삭제합니다.  


## <a id="next"></a>다음 단계
> [!div class="nextstepaction"]
>[Azure Key Vault 관리 API 참조](/dotnet/api/overview/azure/keyvault/management)


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 
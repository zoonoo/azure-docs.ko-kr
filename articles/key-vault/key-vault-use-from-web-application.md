---
title: "웹 응용 프로그램에서 Azure Key Vault 사용 | Microsoft Docs"
description: "이 자습서에서는 웹 응용 프로그램에서 Azure 주요 자격 증명 모음을 사용하는 방법을 알아볼 수 있습니다."
services: key-vault
documentationcenter: 
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: adhurwit
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d41a332d0d4265bc2802be65c7f89aa07e46ae97


---
# <a name="use-azure-key-vault-from-a-web-application"></a>웹 응용 프로그램에서 Azure 주요 자격 증명 모음 사용
## <a name="introduction"></a>소개
이 자습서에서는 Azure의 웹 응용 프로그램에서 Azure 주요 자격 증명 모음을 사용하는 방법을 알아볼 수 있습니다. 웹 응용 프로그램에서 사용할 수 있도록 Azure 주요 자격 증명 모음의 암호에 액세스하는 과정을 안내합니다.

**예상 완료 시간:** 15분

Azure 키 자격 증명 모음에 대한 개요는 [Azure 키 자격 증명 모음이란?](key-vault-whatis.md)

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.

* Azure 주요 자격 증명 모음의 암호에 대한 URI
* 주요 자격 증명 모음에 액세스할 수 있는, Azure Active Directory에 등록된 웹 응용 프로그램의 클라이언트 ID 및 클라이언트 암호
* 웹 응용 프로그램. Azure에 웹앱으로 배포된 ASP.NET MVC 응용 프로그램에 대한 단계를 살펴보겠습니다.

> [!NOTE]
> 이 자습서에 대해 [Azure 주요 자격 증명 모음 시작](key-vault-get-started.md) 에 나열된 단계를 완료하여 웹 응용 프로그램의 클라이언트 ID 및 클라이언트 암호에 대한 URI가 있어야 합니다.
> 
> 

Azure Active Directory에 등록되고 주요 자격 증명 모음에 대한 액세스 권한이 부여된 웹 응용 프로그램에서 주요 자격 증명 모음에 액세스합니다. 그렇지 않은 경우 시작 자습서의 응용 프로그램 등록으로 돌아가서 나열된 단계를 반복합니다.

이 자습서는 Azure에서 웹 응용 프로그램을 만들기 위한 기본 사항을 잘 알고 있는 웹 개발자를 대상으로 합니다. Azure 웹앱에 대한 자세한 내용은 [웹앱 개요](../app-service-web/app-service-web-overview.md)를 참조하세요.

## <a name="a-idpackagesaadd-nuget-packages"></a><a id="packages"></a>NuGet 패키지 추가
웹 응용 프로그램을 위해 설치해야 하는 2개의 패키지가 있습니다.

* Active Directory 인증 라이브러리 - Azure Active Directory를 조작하고 사용자 ID를 관리하기 위한 메서드를 포함합니다.
* Azure 주요 자격 증명 모음 라이브러리 - Azure 주요 자격 증명 모음을 조작하기 위한 메서드를 포함합니다.

패키지 관리자 콘솔에서 Install-Package 명령을 사용하여 이러한 패키지를 모두 설치할 수 있습니다.

    // this is currently the latest stable version of ADAL
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault


## <a name="a-idwebconfigamodify-webconfig"></a><a id="webconfig"></a>Web.Config 수정
다음과 같이 web.config 파일에 추가해야 하는 3개의 응용 프로그램 설정이 있습니다.

    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


응용 프로그램을 Azure 웹앱으로 호스트하지 않으려는 경우 web.config에 실제 ClientId, 클라이언트 암호 및 암호 URI 값을 추가해야 합니다. 그렇지 않으면 보안 강화를 위해 Azure 포털에서 실제 값을 추가할 것이므로 이러한 더미 값을 그대로 둡니다.

## <a name="a-idgettokenaadd-method-to-get-an-access-token"></a><a id="gettoken"></a>액세스 토큰을 가져오는 메서드 추가
주요 자격 증명 모음 API를 사용하려면 액세스 토큰이 필요합니다. 주요 자격 증명 모음 클라이언트가 주요 자격 증명 모음 API 호출을 처리하지만 액세스 토큰을 가져오는 함수를 제공해야 합니다.  

다음은 Azure Active Directory에서 액세스 토큰을 가져오는 코드입니다. 이 코드는 응용 프로그램의 아무 곳에나 배치할 수 있습니다. 이 경우 Utils 또는 EncryptionHelper 클래스를 추가하겠습니다.  

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

> [!NOTE]
> 클라이언트 ID 및 클라이언트 암호를 사용하는 것은 Azure AD 응용 프로그램을 인증하는 가장 쉬운 방법입니다. 웹 응용 프로그램에서 클라이언트 ID 및 클라이언트 암호를 사용하여 의무를 분리하고 키 관리를 보다 세밀하게 제어할 수 있습니다. 하지만 이 방법은 클라이언트 암호를 구성 설정에 배치하는 것을 기반으로 하며 이것은 보호할 암호를 구성 설정에 배치하는 것 만큼이나 위험할 수 있습니다. 클라이언트 ID 및 클라이언트 암호 대신 클라이언트 ID 및 인증서를 사용하여 Azure AD 응용 프로그램을 인증하는 방법에 대한 설명은 다음을 참조하세요.
> 
> 

## <a name="a-idappstartaretrieve-the-secret-on-application-start"></a><a id="appstart"></a>응용 프로그램 시작 시 암호 검색
이제 주요 자격 증명 모음 API를 호출하고 암호를 검색하는 코드가 필요합니다. 다음 코드는 사용하기 전에 호출되기만 하면 아무 곳에나 배치할 수 있습니다. 이 경우 시작 시 한 번 실행되어 응용 프로그램에서 암호를 사용할 수 있도록 Global.asax의 Application Start 이벤트에 이 코드를 배치했습니다.

    //add these using statements
    using Microsoft.Azure.KeyVault;
    using System.Web.Configuration;

    // I put my GetToken method in a Utils class. Change for wherever you placed your method.
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

    var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;

    //I put a variable in a Utils class to hold the secret for general  application use.
    Utils.EncryptSecret = sec;



## <a name="a-idportalsettingsaadd-app-settings-in-the-azure-portal-optional"></a><a id="portalsettings"></a>Azure 포털에서 앱 설정 추가(선택 사항)
Azure 웹앱이 있는 경우 이제 Azure 포털에서 AppSettings의 실제 값을 추가할 수 있습니다. 이 작업을 수행하면 실제 값이 web.config에 포함되지 않고 별도 액세스 제어 기능이 있는 포털을 통해 보호됩니다. 이러한 값은 web.config에 입력된 값을 대체합니다. 이름이 같아야 합니다.

![Azure 포털에 표시되는 응용 프로그램 설정][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>클라이언트 암호 대신 인증서로 인증
클라이언트 ID 및 클라이언트 암호 대신 클라이언트 ID 및 인증서를 사용하여 Azure AD 응용 프로그램을 인증하는 다른 방법입니다. Azure 웹앱에서 인증서를 사용하는 단계는 다음과 같습니다.

1. 인증서 얻기 또는 만들기
2. 인증서를 Azure AD 응용 프로그램에 연결
3. 인증서를 사용하도록 웹앱에 코드 추가
4. 웹앱에 인증서 추가

**인증서 얻기 또는 만들기** 학습을 위한 목적으로 테스트 인증서를 만듭니다. 다음은 개발자 명령 프롬프트에서 인증서를 만들기 위해 사용할 수 있는 몇 가지 명령입니다. 인증서 파일을 만들 디렉터리로 변경합니다.

    makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
    pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

.pfx에 대한 종료 날짜와 암호를 메모해 둡니다(이 예에서는 07/31/2016 및 test123). 아래에서 필요합니다.

테스트 인증서 만들기에 대한 자세한 내용은 [방법: 사용자 고유의 테스트 인증서 만들기](https://msdn.microsoft.com/library/ff699202.aspx)

**인증서를 Azure AD 응용 프로그램에 연결** 이제 인증서를 만들었고 Azure AD 응용 프로그램에 연결해야 합니다. 하지만 Azure 관리 포털에서는 당장은 이 기능을 지원하지 않습니다. 대신 Powershell을 사용해야 합니다. 다음은 실행해야 하는 명령입니다.

    $x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2

    PS C:\> $x509.Import("C:\data\KVWebApp.cer")

    PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())

    PS C:\> $now = [System.DateTime]::Now

    # this is where the end date from the cert above is used
    PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31")

    PS C:\> $adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow

    PS C:\> $sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId

    PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToSecrets all -ResourceGroupName 'contosorg'

    # get the thumbprint to use in your app settings
    PS C:\>$x509.Thumbprint

이러한 명령을 실행하면 Azure AD에서 응용 프로그램을 볼 수 있습니다. 먼저 응용 프로그램이 표시되지 않으면 "회사에서 사용하는 응용 프로그램" 대신 "회사에서 소유하고 있는 응용 프로그램"을 검색합니다.

Azure AD 응용 프로그램 개체 및 ServicePrincipal 개체에 대해 자세히 알아보려면 [응용 프로그램 개체 및 서비스 주체 개체](../active-directory/active-directory-application-objects.md)

**인증서를 사용하도록 웹앱에 코드 추가** 이제 웹앱에 코드를 추가하여 인증서에 액세스하고 인증에 사용합니다.

먼저 인증서에 액세스하기 위한 코드가 있습니다.

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


StoreLocation은 LocalMachine이 아닌, CurrentUser입니다. 테스트 인증서를 사용 중이므로 Find 메서드에 'false'를 입력합니다.

다음은 CertificateHelper를 사용하고 인증에 필요한 ClientAssertionCertificate를 만드는 코드입니다.

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


다음은 액세스 토큰을 가져오는 새로운 코드입니다. 이 코드는 위의 GetToken 메서드를 대체합니다. 편의를 위해 다른 이름을 부여했습니다.

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

사용 편의를 위해 이 모든 코드를 웹앱 프로젝트의 Utils 클래스에 넣어 두었습니다.

마지막 코드 변경은 Application_Start 메서드입니다. 먼저 GetCert() 메서드를 호출하여 ClientAssertionCertificate를 로드해야 합니다. 그런 다음 새 KeyVaultClient를 만들 때 제공한 콜백 메서드를 변경합니다. 이 코드는 위의 코드를 대체합니다.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Azure Portal을 통해 Web App에 인증서 추가** Web App에 인증서를 추가하는 과정은 간단한 두 단계로 이루어집니다. 먼저 Azure 포털로 이동하여 웹앱을 탐색합니다. 웹앱에 대한 설정 블레이드에서 "사용자 지정 도메인 및 SSL" 항목을 클릭합니다. 열리는 블레이드에서 위에서 만든 인증서인 KVWebApp.pfx를 업로드할 수 있습니다. pfx에 대한 암호는 기억하고 있어야 합니다.

![Azure 포털에서 웹앱에 인증서 추가][2]

마지막으로 수행해야 하는 일은 응용 프로그램 설정을 이름이 WEBSITE\_LOAD\_CERTIFICATES이고 값이 *인 웹앱에 추가하는 것입니다. 이렇게 하면 모든 인증서가 로드됩니다. 업로드한 인증서만 로드하려면 지문 복사의  쉼표로 구분된 목록을 입력하면 됩니다.

웹앱에 인증서 추가에 대해 자세히 알아보려면 [Azure 웹 사이트 응용 프로그램에서 인증서 사용](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

**주요 자격 증명 모음에 암호로 인증서 추가** 웹앱 서비스에 인증서를 직접 업로드하는 대신, 암호로 주요 자격 증명 모음에 저장하고 해당 위치에서 배포할 수 있습니다. 다음은 다음 블로그 게시물 [주요 자격 증명 모음을 통해 Azure 웹앱 인증서 배포](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a name="a-idnextanext-steps"></a><a id="next"></a>다음 단계
프로그래밍 참조는 [Azure 주요 자격 증명 모음 C# 클라이언트 API 참조](https://msdn.microsoft.com/library/azure/dn903628.aspx)를 참조하세요.

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png



<!--HONumber=Nov16_HO3-->



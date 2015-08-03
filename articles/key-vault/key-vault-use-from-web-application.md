<properties pageTitle="웹 응용 프로그램에서 Azure 키 자격 증명 모음 사용 | 개요" description="이 자습서를 사용하여 웹 응용 프로그램에서 Azure 키 자격 증명 모음을 사용하는 방법에 대해 배웁니다." services="key-vault" documentationCenter="" authors="adamhurwitz" manager="" tags="azure-resource-manager"//>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="adhurwit"/>

# 웹 응용 프로그램에서 Azure 주요 자격 증명 모음 사용 #

## 소개  
이 자습서에서는 Azure의 웹 응용 프로그램에서 Azure 주요 자격 증명 모음을 사용하는 방법을 알아볼 수 있습니다. 웹 응용 프로그램에서 사용할 수 있도록 Azure 주요 자격 증명 모음의 암호에 액세스하는 과정을 안내합니다.

**예상 완료 시간:** 15분


Azure 키 자격 증명 모음에 대한 개요는 [Azure 키 자격 증명 모음이란?](key-vault-whatis.md)을 참조하세요.

## 필수 조건 

이 자습서를 완료하려면 다음이 필요합니다.

- Azure 주요 자격 증명 모음의 암호에 대한 URI
- 주요 자격 증명 모음에 액세스할 수 있는, Azure Active Directory에 등록된 웹 응용 프로그램의 클라이언트 ID 및 클라이언트 암호
- 웹 응용 프로그램. Azure에 웹앱으로 배포된 ASP.NET MVC 응용 프로그램에 대한 단계를 살펴보겠습니다. 

> [AZURE.NOTE]이 자습서에 대해 [Azure 주요 자격 증명 모음 시작](key-vault-get-started.md)에 나열된 단계를 완료하여 웹 응용 프로그램의 클라이언트 ID 및 클라이언트 암호에 대한 URI가 있어야 합니다.

Azure Active Directory에 등록되고 주요 자격 증명 모음에 대한 액세스 권한이 부여된 웹 응용 프로그램에서 주요 자격 증명 모음에 액세스합니다. 그렇지 않은 경우 시작 자습서의 응용 프로그램 등록으로 돌아가서 나열된 단계를 반복합니다.

이 자습서는 Azure에서 웹 응용 프로그램을 만들기 위한 기본 사항을 잘 알고 있는 웹 개발자를 대상으로 합니다. Azure 웹앱에 대한 자세한 내용은 [웹앱 개요](../app-service-web-overview.md)를 참조하세요.



## <a id="packages"></a>NuGet 패키지 추가 ##
웹 응용 프로그램을 위해 설치해야 하는 3개의 패키지가 있습니다.

- Active Directory 인증 라이브러리 - Azure Active Directory를 조작하고 사용자 ID를 관리하기 위한 메서드를 포함합니다.
- Azure 주요 자격 증명 모음 라이브러리 - Azure 주요 자격 증명 모음을 조작하기 위한 메서드를 포함합니다.


패키지 관리자 콘솔에서 Install-Package 명령을 사용하여 3개의 패키지를 모두 설치할 수 있습니다.

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	//this is a preview version of the Key Vault Library
	Install-Package Microsoft.Azure.KeyVault -Pre


## <a id="webconfig"></a>Web.Config 수정 ##
다음과 같이 web.config 파일에 추가해야 하는 3개의 응용 프로그램 설정이 있습니다.

	<!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

	<!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


응용 프로그램을 Azure 웹앱으로 호스트하지 않으려는 경우 web.config에 실제 ClientId, 클라이언트 암호 및 암호 URI 값을 추가해야 합니다. 그렇지 않으면 보안 강화를 위해 Azure 포털에서 실제 값을 추가할 것이므로 이러한 더미 값을 그대로 둡니다.


## <a id="gettoken"></a>액세스 토큰을 가져오는 메서드 추가 ##
주요 자격 증명 모음 API를 사용하려면 액세스 토큰이 필요합니다. 주요 자격 증명 모음 클라이언트가 주요 자격 증명 모음 API 호출을 처리하지만 액세스 토큰을 가져오는 함수를 제공해야 합니다.

다음은 Azure Active Directory에서 액세스 토큰을 가져오는 코드입니다. 이 코드는 응용 프로그램의 아무 곳에나 배치할 수 있습니다. 이 경우 Utils 또는 EncryptionHelper 클래스를 추가하겠습니다.

	//add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Web.Configuration;
	
	//this is an optional property to hold the secret after it is retrieved
	public static string EncryptSecret { get; set; }

	//the method that will be provided to the KeyVaultClient
	public async static Task<string> GetToken(string authority, string resource, string scope)
    {
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	    
	    if (result == null)
	    	throw new InvalidOperationException("Failed to obtain the JWT token");
	    
	    return result.AccessToken;
    }

## <a id="appstart"></a>응용 프로그램 시작 시 암호 검색 ##
이제 주요 자격 증명 모음 API를 호출하고 암호를 검색하는 코드가 필요합니다. 다음 코드는 사용하기 전에 호출되기만 하면 아무 곳에나 배치할 수 있습니다. 이 경우 시작 시 한 번 실행되어 응용 프로그램에서 암호를 사용할 수 있도록 Global.asax의 Application Start 이벤트에 이 코드를 배치했습니다.

	//add these using statements
	using Microsoft.Azure.KeyVault;
	using System.Web.Configuration;

	// I put my GetToken method in a Utils class. Change for wherever you placed your method. 
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

	var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;
	
	//I put a variable in a Utils class to hold the secret for general  application use. 
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Azure 포털에서 앱 설정 추가(선택 사항) ##
Azure 웹앱이 있는 경우 이제 Azure 포털에서 AppSettings의 실제 값을 추가할 수 있습니다. 이 작업을 수행하면 실제 값이 web.config에 포함되지 않고 별도 액세스 제어 기능이 있는 포털을 통해 보호됩니다. 이러한 값은 web.config에 입력된 값을 대체합니다. 이름이 같아야 합니다.

![Azure 포털에 표시되는 응용 프로그램 설정][1]



## <a id="next"></a>다음 단계 ##


프로그래밍 참조는 [Azure 주요 자격 증명 모음 C# 클라이언트 API 참조](https://msdn.microsoft.com/library/azure/dn903628.aspx)를 참조하세요.


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
 

<!---HONumber=July15_HO4-->
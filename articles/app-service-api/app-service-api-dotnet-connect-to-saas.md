<properties 
	pageTitle="Azure 앱 서비스에서 사용자 지정 SaaS 커넥터 ASP.NET API 앱 만들기" 
	description="API 앱에서 SaaS 플랫폼에 연결되는 코드를 작성하는 방법과 .NET 클라이언트에서 API 앱을 호출하는 방법에 알아봅니다." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="tdykstra"/>

# Azure 앱 서비스의 ASP.NET API 앱에서 SaaS 플랫폼에 연결

## 개요

이 자습서에서는 [앱 서비스 API 앱 SDK for.NET](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/)을 사용하여 [SaaS(Software-as-a-Service) 플랫폼](../app-service/app-service-authentication-overview.md#obotosaas)에 연결되는 [API 앱](app-service-api-apps-why-best-platform.md)을 코딩하고 구성하는 방법을 보여 줍니다. 또한 [앱 서비스 SDK for .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService)을 사용하여 .NET 클라이언트에서 API 앱을 호출하는 방법도 나와 있습니다. 이 자습서의 끝에는 Azure 앱 서비스에서 실행되는 .NET API 앱을 호출하는 .NET 콘솔 앱 클라이언트가 제공됩니다. 이 API 앱은 Dropbox API를 호출하고 사용자의 Dropbox 계정에 있는 파일 및 폴더 목록을 반환합니다.

사용자 지정 API 앱에서 직접 SaaS API를 호출하는 코드를 작성하는 대신, 미리 패키지된 [커넥터 API 앱](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md)을 호출할 수 있습니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [SaaS 커넥터 API 앱 배포 및 구성](app-service-api-connnect-your-app-to-saas-connector.md)을 참조하세요.

이 자습서에서는 다음 단계를 단계별로 안내합니다.

* Visual Studio에서 API 앱 프로젝트를 만듭니다. 
* API 앱을 사용하여 Dropbox 서비스에 연결할 수 있도록 *apiapp.json* 파일을 구성합니다.
* Dropbox를 호출하고 결과를 반환하는 코드를 추가합니다.
* Azure에서 새 API 앱을 만듭니다.
* API 앱에 프로젝트를 배포합니다.
* API 앱을 구성합니다.
* 게이트웨이 구성
* 테스트 클라이언트를 만듭니다.
* 테스트 클라이언트를 실행합니다.

## 필수 조건

이 자습서에서는 사용자가 다음과 같은 요건을 충족한다고 가정합니다.

* [API 앱 만들기](app-service-dotnet-create-api-app.md) 및 [API 앱 배포](app-service-dotnet-deploy-api-app.md) 자습서를 완료했습니다.
  
* [API 앱 및 모바일 앱 인증](app-service-authentication-overview.md)에 제공된 대로 인증을 위한 Azure 앱 서비스 게이트웨이 아키텍처를 기본적으로 이해하고 있습니다.

* [API 앱 및 게이트웨이 블레이드](app-service-api-manage-in-portal.md#navigate)에 설명된 것처럼 Azure Preview 포털에서 API 앱을 사용하는 방법을 알고 있습니다.

## API 앱 프로젝트 만들기
 
프로젝트의 이름을 입력하라는 지침이 있으면 *SimpleDropbox*를 입력합니다.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

## *apiapp.json* 파일 구성

API 앱이 SaaS 플랫폼으로 보내는 호출을 수행하려면 *apiapp.json* 파일에 SaaS 플랫폼이 지정되어야 합니다.

1. *apiapp.json* 파일을 열고 여기에 나오는 것처럼 `authentication` 속성을 추가합니다(속성을 입력한 후 쉼표를 추가해야 함).

		"authentication": [
		  {
		    "type": "dropbox"
		  }
		]

	전체 apiapp.json 파일은 다음 예와 비슷합니다.

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "SimpleDropBox",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "SimpleDropBox",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    },
		    "authentication": [
		      {
		        "type": "dropbox"
		      }
		    ]
		}

2. 파일을 저장합니다.

`authentication` 속성을 설정하면 다음과 같은 몇 가지 결과가 나타납니다.

* 포털의 API 앱 블레이드에는 SaaS 플랫폼의 클라이언트 ID 및 클라이언트 암호 값을 입력할 수 있는 UI가 표시됩니다.

	![](./media/app-service-api-dotnet-connect-to-saas/authblade.png)

* 이렇게 하면 API 앱이 게이트웨이에서 SaaS 공급자의 API를 호출할 때 사용할 SaaS 공급자 액세스 토큰을 검색할 수 있습니다.

`authentication` 속성은 배열이지만 이 미리 보기 릴리스는 여러 공급자의 지정을 지원하지 않습니다.

지원되는 플랫폼 목록은 [다른 SaaS 플랫폼을 액세스하기 위한 사용자 동의 얻기](../app-service/app-service-authentication-overview.md#obotosaas)를 참조하세요.

다음 예제와 같이 범위를 지정할 수도 있습니다.

		"authentication": [
		  {
		    "type": "google",
		    "scopes": ["https://www.googleapis.com/auth/userinfo.email", "https://www.googleapis.com/auth/userinfo.profile"]
		  }
		]

사용 가능한 범위는 SaaS 공급자별로 정의되며 공급자의 개발자 포털에서 찾을 수 있습니다.

## Dropbox를 호출하는 코드 추가

1. SimpleDropbox 프로젝트에 [DropboxRestAPI](https://www.nuget.org/packages/DropboxRestAPI) NuGet 패키지를 설치합니다.

	* **도구** 메뉴에서 **Nuget 패키지 관리자 > 패키지 관리자 콘솔**을 클릭합니다.

	* **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.
	 
			install-package DropboxRestAPI  

1. *Controllers\\ValuesController.cs*를 열고 파일의 모든 코드를 다음 코드로 바꿉니다.

		using DropboxRestAPI;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace SimpleDropBox2.Controllers
		{
		    public class ValuesController : ApiController
		    {
		        public async Task<IEnumerable<string>> Get()
		        {
		            // Retrieve the token from the gateway
		            var runtime = Runtime.FromAppSettings(Request);
		            var dropboxTokenResult = await runtime.CurrentUser.GetRawTokenAsync("dropbox");
		
		            // Create a Dropbox client object that will send the token
		            // with REST API calls to Dropbox.
		            var dropboxClient = new Client(
		                new Options
		                {
		                    AccessToken = dropboxTokenResult.Properties["AccessToken"]
		                }
		            );
		
		            // Call the Dropbox API
		            var metadata = await dropboxClient.Core.Metadata.MetadataAsync("/");
		
		            // Return a list of files and folders.
		            return metadata.contents.Select(md => md.path);
		        }
		    }
		}

	클라이언트에서 이 메서드를 호출하기 전에 사용자가 Dropbox에 로그인하고 API 앱에서 사용자의 Dropbox 계정에 액세스할 수 있도록 동의해야 합니다. Dropbox는 앱 서비스 게이트웨이에 액세스 토큰을 제공하여 해당 동의를 승인합니다. 아래 다이어그램의 6, 7단계에 표시된 것처럼 이 코드는 게이트웨이에서 토큰을 검색한 후 Dropbox API에 대한 호출에 포함합니다.

	![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

2. 프로젝트를 빌드합니다.

## Azure에서 API 앱 만들기

이 섹션에서는 Visual Studio **웹 게시**를 사용하여 Azure에서 API 앱을 만듭니다. API 앱의 이름을 입력하라는 지침이 있으면 *SimpleDropbox*를 입력합니다.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## 코드 배포

동일한 **웹 게시** 마법사를 사용하여 새 API 앱에 코드를 배포합니다.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## 들어오는 호출에 대한 인증 구성

Azure 앱 서비스가 API 앱에서 인증된 호출을 보내도록 허용하려면 API 앱도 인증된 사용자로부터 호출이 들어오도록 요구해야 합니다. 이것은 일반적인 OAuth 2.0 요구 사항이 아니지만 현재 구현되어 있는 앱 서비스 게이트웨이 아키텍처의 요구 사항입니다.

이 섹션의 스크린샷은 ContactsList API 앱을 보여 주지만 프로세스는 이 자습서에서 사용자가 만드는 SimpleDropbox API 앱의 경우와 동일합니다.

### 들어오는 호출의 인증을 요구하도록 API 앱 구성

[AZURE.INCLUDE [app-service-api-config-auth](../../includes/app-service-api-config-auth.md)]

### 게이트웨이에 ID 공급자 구성

[AZURE.INCLUDE [app-service-api-gateway-config-auth](../../includes/app-service-api-gateway-config-auth.md)]

## 나가는 호출에 대한 인증 구성

API 앱이 Dropbox API를 호출할 수 있게 하려면 API 앱과 Dropbox 개발자 사이트에서 만드는 Dropbox 앱 간에 설정을 교환해야 합니다.

### Dropbox.com 사이트에서 Dropbox 앱 만들기

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### Dropbox와 API 앱 간에 설정 교환

다음 단계에서는 Dropbox 커넥터 API 앱을 참조하지만 절차와 UI는 이 자습서에서는 만드는 SimpleDropbox API 앱의 경우와 동일합니다.

> **참고:** 스크린샷에 표시된 대로 SimpleDropbox API 앱의 **인증** 블레이드에서 Dropbox 클라이언트 ID 및 클라이언트 암호에 대한 필드가 표시되지 않으면 API 앱 프로젝트를 API 앱에 배포한 후에 지시에 따라 게이트웨이를 다시 시작해야 합니다. 이전에 배포한 *apiapp.json* 파일의 `authentication` 속성에 포함된 "dropbox" 값이 이러한 필드를 표시하도록 포털을 트리거합니다.

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

## 테스트 클라이언트 만들기

이 섹션에서는 Visual Studio에서 SimpleDropbox API 앱을 호출하기 위해 생성하는 클라이언트 코드를 사용하는 콘솔 앱 프로젝트를 만듭니다. 콘솔 앱은 Windows Forms 브라우저 컨트롤을 인스턴스화하여 사용자와 게이트웨이 및 Dropbox 로그인 웹 페이지 간의 상호 작용을 처리합니다.

### 프로젝트 만들기

1. Visual Studio에서 콘솔 응용 프로그램 프로젝트를 만들고 *SimpleDropboxTest*라고 이름을 지정합니다.

2. System.Windows.Forms에 대한 참조를 설정합니다.
 
	* **솔루션 탐색기**에서 **참조**를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 클릭합니다.

	* **System.Windows.Forms** 왼쪽의 확인란을 선택하고 **확인**을 클릭합니다.
	 
	![](./media/app-service-api-dotnet-connect-to-saas/setref.png)

	콘솔 응용 프로그램은 사용자가 게이트웨이 및 Dropbox에 로그인할 수 있게 해야 할 경우 Windows Forms 어셈블리를 사용하여 브라우저 컨트롤을 인스턴스화합니다.

### 생성된 클라이언트 코드 추가

이 섹션의 스크린샷에는 ContactsList 프로젝트 및 API 앱이 나와 있지만, 이 자습서를 진행하려면 SimpleDropboxTest 프로젝트 및 SimpleDropbox API 앱을 선택하세요.

[AZURE.INCLUDE [app-service-api-dotnet-add-generated-client](../../includes/app-service-api-dotnet-add-generated-client.md)]

### API 앱을 호출하는 코드 추가

3. *Program.cs*를 열고 코드를 다음 코드로 바꿉니다.
		
		using Microsoft.Azure.AppService;
		using Newtonsoft.Json;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Windows.Forms;
		
		namespace SimpleDropboxTest
		{
		    enum Step
		    {
		        GatewayLogin,
		        GetSaaSConsentLink,
		        GetUserConsent
		    }
		
		    class Program
		    {
		        private const string GATEWAY_URL = @"{gateway url}";
		        private const string URL_TOKEN = "#token=";
		        private const string SAAS_URL = "dropbox.com";
		        private static Form frm = new Form();
		        private static string responseURL = "";
		        private static Step step;
		        [STAThread]
		        static void Main(string[] args)
		        {
		            // Create the web browser control
		            WebBrowser browser = new WebBrowser();
		            browser.Dock = DockStyle.Fill;
		            browser.Navigated += CheckResponseURL;
		            frm.Controls.Add(browser);
		            frm.Width = 640;
		            frm.Height = 480;
		
		            // Create the gateway and API app clients.
		            AppServiceClient appServiceClient = new AppServiceClient(GATEWAY_URL);
		            SimpleDropbox simpleDropboxClient = appServiceClient.CreateSimpleDropbox();
		
		            // Navigate browser to gateway login URL for configured identity provider.
		            // Identity provider for this example is Azure Active Directory.
		            step = Step.GatewayLogin;
		            browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));
		            frm.ShowDialog();
		            Console.WriteLine("Logged in to gateway, response URL=" + responseURL);
		
		            // Get user ID and Zumo token from return URL, then call 
		            // the gateway URL to log in the gateway client.
		            var encodedJson = responseURL.Substring(responseURL.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		            var decodedJson = Uri.UnescapeDataString(encodedJson);
		            var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		            string userId = result.user.userId;
		            string userToken = result.authenticationToken;
		            appServiceClient.SetCurrentUser(userId, userToken);
		
		            // Call gateway API to get consent link URL for target SaaS platform.
		            // SaaS platform for this example is Dropbox.
		            // See the tutorial for an explanation of
		            // the redirectURL parameter for GetConsentLinkAsync
		            var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;
		            Console.WriteLine("\nGot gateway consent link, URL=" + gatewayConsentLink);
		
		            // Navigate browser to consent link URL returned from gateway.
		            // Response URL will be the SaaS logon link
		            step = Step.GetSaaSConsentLink;
		            browser.Navigate(gatewayConsentLink);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot SaaS consent link response, URL=" + responseURL);
		
		            // Navigate browser to login/consent link for SaaS platform.
		            step = Step.GetUserConsent;
		            browser.Navigate(responseURL);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot Dropbox login response, URL=" + responseURL);
		
		            Console.WriteLine("\nResponse from SaaS Provider");
		            var response = simpleDropboxClient.Values.Get();
		            foreach (string s in response)
		            {
		                Console.WriteLine(s);
		            }
		            Console.Read();
		        }
		
		        static void CheckResponseURL(object sender, WebBrowserNavigatedEventArgs e)
		        {
		            if ((step == Step.GatewayLogin && e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		                || (step == Step.GetSaaSConsentLink && e.Url.AbsoluteUri.IndexOf(SAAS_URL) > -1)
		                || (step == Step.GetUserConsent && e.Url.AbsoluteUri.IndexOf(GATEWAY_URL) > -1))
		            {
		                responseURL = e.Url.AbsoluteUri;
		                frm.Close();
		            }
		        }
		
		    }
		}

1. {gateway url}을 게이트웨이의 실제 URL로 바꿉니다.
 
	포털의 **게이트웨이** 블레이드에서 게이트웨이 URL을 얻을 수 있습니다.

	![](./media/app-service-api-dotnet-connect-to-saas/gwurl.png)

		private const string GATEWAY_URL = @"https://sd1aeb4ae60b7cb4f3d966dfa43b660.azurewebsites.net";

	> **중요**: 게이트웨이 URL이 `http://`가 아니라 `https://`로 시작하는지 확인하세요. **포털에서 http://를 복사하는 경우 코드에 붙여 넣을 때 https://로 변경해야 합니다.**

### 코드 설명

이 콘솔 응용 프로그램은 최소한의 코드를 사용하여 클라이언트 앱이 진행해야 하는 단계를 보여 주도록 설계되었습니다. 프로덕션 응용 프로그램은 일반적으로 콘솔 응용 프로그램이 아니며 오류 처리 및 로깅을 구현합니다.

코드에서 수행하는 작업의 개요는 다음과 같습니다.

* 구성된 ID 공급자(이 경우 Azure Active Directory)에 대한 게이트웨이 로그인 URL로 브라우저를 엽니다. 
	 
* 사용자가 로그인한 후 예상 응답 URL을 처리합니다. 즉, 사용자 ID와 Zumo 토큰을 추출한 후 앱 서비스 클라이언트 개체에 제공합니다.

* 앱 서비스 클라이언트 개체를 사용하여 로그인 및 동의를 위해 Dropbox 링크로 리디렉션되는 게이트웨이 URL을 검색합니다. 다이어그램의 1단계입니다.

* 브라우저가 게이트웨이 동의 URL로 열립니다. 브라우저가 Dropbox 로그인 및 동의 링크로 리디렉션됩니다. 다이어그램의 2단계입니다.
	 
* 사용자가 Dropbox.com에서 로그인하고 동의를 제공하면 브라우저가 닫힙니다. 다이어그램의 3단계입니다.
 
* API 앱이 호출됩니다. 다이어그램에서 5단계입니다. 4단계는 사용자가 모르는 사이에 Dropbox.com과 게이트웨이 사이에서 진행되고 6, 7단계는 클라이언트가 아닌 API 앱에서 수행됩니다.

![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

추가 참고 사항:

* `Main` 메서드의 `STAThread` 특성은 웹 브라우저 컨트롤에 필요하고 API 앱 설정 또는 호출과 관련이 없습니다.

* 표시되는 게이트웨이 로그인 URL은 Azure Active Directory에 해당하는 `/aad`로 끝납니다.

		browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));

	다른 공급자에 대해 사용하는 값은 다음과 같습니다. * "microsoftaccount" * "facebook" * "twitter" * "google"<br/><br/>

* `GetConsentLinkAsync()` 메서드에 대한 두 번째 매개 변수는 사용자가 Dropbox에 로그인한 후 동의 서버가 리디렉션되는 콜백 URL로, 사용자 계정에 액세스할 수 있는 동의를 제공합니다.

		var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;

	클라이언트 응용 프로그램에서 사용자가 이동되어야 하는 다음 웹 페이지를 이 매개 변수로 지정할 수 있습니다. 이 데모 코드는 콘솔 앱에 있으므로 이동할 응용 프로그램 페이지는 없으며 이 코드는 게이트웨이 URL을 편리한 방문 페이지로 지정합니다.

	클라이언트 응용 프로그램은 이 URL로 리디렉션되는지와 오류 메시지가 없는지 확인해야 합니다. 로그인/동의 프로세스가 실패하면 리디렉션 URL의 querystring에 오류 메시지가 포함될 수 있습니다. 자세한 내용은 [문제 해결](#troubleshooting)섹션을 참조하세요.

## 테스트

1. SimpleDropboxTest 콘솔 응용 프로그램을 실행합니다.

2. 첫 번째 로그온 페이지에서 Azure Active Directory 자격 증명(또는 게이트웨이에서 다른 ID 공급자(예: Google 또는 Twitter)를 구성한 경우 해당 공급자의 자격 증명)을 사용하여 로그인합니다.

	![](./media/app-service-api-dotnet-connect-to-saas/aadlogon.png)

3. Dropbox.com 로그인 페이지에서 Dropbox 자격 증명을 사용하여 로그인합니다.

	![](./media/app-service-api-dotnet-connect-to-saas/dblogon.png)

4. Dropbox 동의 페이지에서 응용 프로그램에 데이터에 액세스하기 위한 권한을 제공합니다.

	![](./media/app-service-api-dotnet-connect-to-saas/dbconsent.png)

	콘솔 앱은 API 앱을 호출하고 Dropbox 계정의 파일 목록을 반환합니다.

	![](./media/app-service-api-dotnet-connect-to-saas/testclient.png)

## 문제 해결

이 섹션에는 다음 항목이 포함되어 있습니다.

* [게이트웨이 로그인 후 HTTP 오류 405 표시](#405)
* [Dropbox 로그인 페이지 대신 HTTP 오류 400 표시](#400)
* [API 앱을 호출할 때 HTTP 오류 403 표시](#403)

### <a id="405"></a>게이트웨이 로그인 후 HTTP 오류 405 표시

코드가 GetConsentLinkAsync를 호출할 때 HTTP 오류 405이 표시되면 게이트웨이 URL에 대해 http://가 아닌 https://를 사용했는지 확인합니다.

![](./media/app-service-api-dotnet-connect-to-saas/http405.png)

클라이언트가 비 SSL HTTP POST 요청을 시도하고, 게이트웨이가 *https://*으로 리디렉션하고, 이러한 리디렉션으로 인해 GET 요청을 발생하므로 405 메서드가 허용되지 않음 오류가 수신됩니다. 동의 링크를 검색하기 위한 URL은 POST 요청만 허용합니다.

### <a id="400"></a>Dropbox 로그인 페이지 대신 HTTP 오류 400 표시

API 앱의 **인증** 블레이드에 올바른 **클라이언트 ID**가 있는지와 선행 또는 후행 공백이 없는지 확인합니다.

### <a id="403"></a>API 앱을 호출할 때 HTTP 오류 403 표시

* API 앱의 **액세스 수준**이 **내부**가 아닌 **공용(인증)**으로 설정되어 있는지 확인합니다.

* API 앱의 **인증** 블레이드에 올바른 **클라이언트 암호**가 있는지와 선행 또는 후행 공백이 없는지 확인합니다.

Dropbox 로그인 이후에 나타나는 리디렉션 URL은 다음과 같을 수 있습니다.

	https://sd1aeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/?error=RmFpbGVkIHRvIGV4Y2hhbmdlIGNvZGUgZm9yIHRva2VuLiBEZXRhaWxzOiB7ImVycm9yX2Rlc2NyaXB0aW9uIjogIkludmFsaWQgY2xpZW50X2lkIG9yIGNsaWVudF9zZWNyZXQiLCAiZXJyb3IiOiAiaW52YWxpZF9jbGllbnQifQ%3d%3d

`error` querystring 값 끝에서 %3d%3d를 제거하면 유효한 base64 인코딩 문자열이 됩니다. 문자열을 디코딩하여 오류 메시지를 표시합니다.

	Failed to exchange code for token. Details: {"error_description": "Invalid client_id or client_secret", "error": "invalid_client"}

## 다음 단계

SaaS 플랫폼에 연결되는 API 앱을 코딩 및 구성하는 방법을 살펴보았습니다. API 앱에서 인증을 처리하는 방법에 대한 다른 자습서로 연결되는 링크를 보려면 [API 앱 및 모바일 앱에 대한 인증 - 다음 단계](../app-service/app-service-authentication-overview.md#next-steps)를 참조하세요.

[Azure preview portal]: https://portal.azure.com/
[Azure portal]: https://manage.windowsazure.com/

<!---HONumber=July15_HO3-->
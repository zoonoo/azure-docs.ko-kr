<properties 
	pageTitle="인증된 클라이언트에서 API 앱 호출" 
	description="Azure Active Directory에 의해 인증되는 웹앱 클라이언트에서 Azure API 앱을 호출하는 방법을 배웁니다." 
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
	ms.date="07/26/2015" 
	ms.author="tdykstra"/>

# Azure API 응용 프로그램을 Azure Active Directory에 의해 인증되는 웹 응용 프로그램 클라이언트에서 호출

## 개요

이 자습서에서는 AAD로도 보호되는 웹앱에서 나온 AAD(Azure Active Directory)로 보호되는 API 앱을 호출하는 방법을 보여줍니다. 이 자습서를 완료하면 웹앱 및 API 앱이 Azure 구독에서 실행됩니다. 웹앱에는 다음 화면에 표시된 대로 API 앱을 호출하여 가져온 데이터가 표시됩니다.

![](./media/app-service-api-authentication-client-flow/aboutpage.png)

사용자가 API 앱으로 로그인하라는 메시지가 다시 표시되지 않도록 웹 앱에서 API 앱으로 AAD 자격 증명을 전달하는 방법을 배웁니다.

다음 단계를 수행합니다.

- API 응용 프로그램을 만들고 AAD 인증을 사용하도록 구성합니다.
- 웹앱을 만들고 AAD 인증을 사용하도록 구성합니다.
- 웹앱에 코드를 추가하여 보호된 API 앱을 호출합니다.
- Azure에 웹앱 및 API 앱 모두를 배포합니다.
- 웹앱이 API 앱을 호출할 수 있는지 확인하기 위해 테스트합니다.

### 클라이언트 흐름 인증

웹앱에 추가하는 코드는 [클라이언트 흐름](../app-service/app-service-authentication-overview.md#client-flow) 인증이라는 과정을 구현합니다. 다음 다이어그램에서는 AAD 액세스 토큰을 가져오고 API 응용 프로그램 (Zumo) 토큰에 대해 교환하기 위한 과정을 보여줍니다.

![](./media/app-service-api-authentication-client-flow/clientflow.png)

API 앱에 대한 인증에서 API 앱 게이트웨이의 역할을 잘 모르는 경우[API 앱과 모바일 앱에 대한 인증](../app-service/app-service-authentication-overview.md)을 참조합니다.

## 필수 조건

이 자습서를 시작하기 전에 [Visual Studio 2015](https://www.visualstudio.com/) 및 [Azure SDK for.NET](http://go.microsoft.com/fwlink/?linkid=518003)이 설치되었는지 확인합니다. Visual Studio 2013에서도 유사한 지침이 작동합니다.

이 자습서에서는 Visual Studio에서 웹 프로젝트로 작업하는 방법을 알고 있다고 가정합니다.

## AAD를 사용하여 API 앱을 만들고 보안을 유지합니다.

1. API 앱 프로젝트를 만들거나 다운로드합니다.
 
	* 프로젝트를 만들려면 [API 앱 만들기](app-service-dotnet-create-api-app.md)의 지시에 따릅니다.

	* 프로젝트를 다운로드 하려면 [ContactsList GitHub 리포지토리](https://github.com/tdykstra/ContactsList)에서 프로젝트를 가져옵니다.

	이제 연락처 데이터를 반환하는 Web API 프로젝트가 준비되었습니다.

	![](./media/app-service-api-authentication-client-flow/swaggerlocal.png)

2. Azure에서 새 API 앱에 웹 API 프로젝트 배포

	[API 앱을 배포](app-service-dotnet-deploy-api-app.md)의 지시에 따릅니다.

	[Azure 미리 보기 포털]에서, 웹 API에 대한 **API 정의**에서는 배포한 웹 API의 Get 및 Post 메서드를 표시합니다.

	![](./media/app-service-api-authentication-client-flow/apiappinportal.png)

4. ID 공급자로서 ADD(Azure Active Directory)를 사용하여 API 앱을 보호합니다.
 
	[API 앱 보호](app-service-api-dotnet-add-authentication.md)의 AAD에 대한 지침에 따릅니다. 자습서의 **Post 요청을 보내기 위해 Postman 사용** 섹션을 수행하지 않아도 됩니다.

	완료하면 [Azure 미리 보기 포털]에서 API 앱의 **Azure Active Directory** 블레이드에는 AAD 응용 프로그램의 클라이언트 ID 및 AAD 테넌트가 있습니다.

	![](./media/app-service-api-authentication-client-flow/aadblade.png)

	[Azure 포털]의 AAD 응용 프로그램 **구성** 탭에는 API 앱의 앱 ID URL 및 회신 URL이 있습니다.

	![](./media/app-service-api-authentication-client-flow/aadconfig.png)

## AAD를 사용하여 웹앱을 만들고 보안을 유지합니다.

이 섹션에서는 AAD 인증을 위해 설정하는 웹 프로젝트를 다운로드 및 구성합니다. 프로젝트에는 로그인 된 사용자에 대한 클레임 정보를 표시하는 **정보** 페이지가 있습니다.

![](./media/app-service-api-authentication-client-flow/aboutpagestart.png)

API 앱에서 검색하는 연락처 정보 표시에 대한 섹션을 추가하여 나중에 이 페이지를 수정 합니다.

1. [WebApp-GroupClaims-DotNet 리포지토리](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/)에서 웹 프로젝트를 다운로드합니다.
 
2. [추가 정보 파일](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md)에서 **샘플을 실행하는 방법**에 대한 지침에 따릅니다. 다음 사항은 예외입니다.
 
	* 추가 정보 파일에는 Visual Studio 2013을 사용한다고 표시 되지만 Visual Studio 2015를 사용할 수 있습니다. 

	* 새로 만드는 대신 이미 만든 AAD 응용 프로그램을 사용합니다.
 
	* AAD 응용 프로그램에 대해 이미 가지고 있는 **앱 ID URI**를 그대로 유지합니다. (추가 정보 파일에 지정된 형식에 대해 바꾸지 마십시오.)

웹앱 및 API 앱 모두에 동일한 AAD 액세스를 사용할 수 있도록 API 앱에 대해 만든 동일한 앱 ID URI를 유지하고 있습니다. 추가 정보에 정의된 형식에 대해 앱 ID URI를 변경한 경우에는 웹 앱에 대한 엑세스에는 작동하지만 API 앱에 대해서는 작동하지 않습니다. 게이트웨이에서는 게이트웨이에 "/login/aad"를 더한 API ID URI에 대한 토큰이 예상되므로 Zumo 게이트웨이를 가져오기 위해 API 앱 게이트웨이에 대한 AAD 토큰을 전달할 수 없습니다.

## API 앱에 대해 생성된 클라이언트 코드 추가

이 섹션에서는 API 앱 호출에 형식화 된 인터페이스에 대해 자동으로 생성된 코드를 추가 합니다.

8.	Visual studio **솔루션 탐색기**에서 WebApp-GroupClaims-DotNet 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 > Azure API 앱 클라이언트**를 클릭합니다.

9.	**Microsoft Azure API 앱 클라이언트 추가 ** 대화 상자에서 AAD를 사용하여 보호된 API 앱을 선택합니다.

	코드 생성이 완료되고 나면 **솔루션 탐색기**에 API 앱의 이름으로 만들어진 새 폴더가 표시됩니다. 이 폴더에는 클라이언트 클래스 및 데이터 모델을 구현하는 코드가 들어 있습니다.

	![](./media/app-service-api-authentication-client-flow/aboutpagestart.png)

10. *ContactsList/ContactsExtensions.cs*: `Task.Factory.StartNew` 에서 `System.Threading.Tasks.Task.Factory.StartNew`까지 두 인스턴스를 변경에 생성된 코드에 의해 발생하는 모호한 참조를 수정합니다.
 
## Zumo 토큰에 대해 AAD 토큰을 교환하는 코드를 추가합니다.

1.	Homecontroller.cs에서 `using` 앱 서비스 SDK 및 JSON serializer에 대한 문을 추가합니다.

		using Microsoft.Azure.AppService;
		using Newtonsoft.Json.Linq;

2. AAD 앱의 게이트웨이 URL 및 앱 ID URI에 대한 상수를 추가 합니다. 게이트웨이 URL이 http가 아니라 https 인지 확인합니다.

		private const string GATEWAY_URL = "https://clientflowgroupaeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string APP_ID_URI = GATEWAY_URL + "login/aad";

2.	API 앱을 호출하기 위한 클라이언트 개체를 가져오는 메서드를 추가합니다.

		public async Task<AppServiceClient> GetAppServiceClient()
		{
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    string userObjectID = ClaimsPrincipal.Current.FindFirst
		        ("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		
		    var authContext = new AuthenticationContext
		        (ConfigHelper.Authority, new TokenDbCache(userObjectID));
		
		    ClientCredential credential = new ClientCredential
		        (ConfigHelper.ClientId, ConfigHelper.AppKey);
		
		    // Get the AAD token.
		    AuthenticationResult result = authContext.AcquireToken(APP_ID_URI, credential);
		    var aadToken = new JObject();
		    aadToken["access_token"] = result.AccessToken;
		
		    // Send the AAD token to the gateway and get a Zumo token
		    var appServiceUser = await appServiceClient.LoginAsync
		        ("aad", aadToken).ConfigureAwait(false);
		
		    return appServiceClient;
		}

	이 코드에서는 `ConfigHelper.Authority` "https://login.microsoftonline.com/{테넌트}"로 확인됩니다. 예: "https://login.microsoftonline.com/contoso.onmicrosoft.com"

2.	API 앱을 호출하는 `About` 방법의 끝에 있는 `return View()` 문 바로 앞에 즉시 코드를 추가합니다. (다음 단계에서 `About` 반환된 데이터를 표시하는 보기에 코드를 추가합니다.)

		var appServiceClient = await GetAppServiceClient();
		var client = appServiceClient.CreateContactsList();
		var contacts = client.Contacts.Get();
		ViewData["contacts"] = contacts;

3. *Views/Home/About.cshtml*에서, 연락처 정보를 표시 하는 `h2`머리글 뒤에 즉시 코드를 추가합니다.

		<h3>Contacts</h3>
		<table class="table table-striped table-bordered table-condensed table-hover">
		    <tr>
		        <th>Name</th>
		        <th>Email</th>
		    </tr>
		
		    @foreach (WebAppGroupClaimsDotNet.Models.Contact contact in (IList<WebAppGroupClaimsDotNet.Models.Contact>)ViewData["contacts"])
		    {
		        <tr>
		            <td>@contact.Name</td>
		            <td>@contact.EmailAddress</td>
		        </tr>
		    }
		
		</table>


3. 응용 프로그램 Web.config 파일에서 `<assemblyBinding>` 태그를 연 후 다음의 바인딩 리디렉션을 추가합니다.

		<dependentAssembly>
		  <assemblyIdentity name="System.Net.Http.Primitives" publicKeyToken="b03f5f7f11d50a3a" culture="neutral"/>
		  <bindingRedirect oldVersion="0.0.0.0-4.2.28.0" newVersion="4.2.28.0"/>
		</dependentAssembly>

	이 바인딩 리디렉션을 하지 않으면 앱 서비스 SDK 버전에 System.Net.Http.Primitives 버전 1.5.0.0 에 대한 종속성이 포함되지만 프로젝트에 의해 사용되는 버전은 4.2.28.0 이기 때문에 앱은 작동하지 않습니다.
 
3. [추가 정보 파일](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/)의 **이 샘플을 Azure에 배포** 지침에 따릅니다.

5. 앱을 실행합니다.

	![](./media/app-service-api-authentication-client-flow/homepage.png)

6. **로그인**을 클릭한 다음 이 응용 프로그램에 대해 사용 중인 AAD 도메인에서 사용자의 자격 증명을 입력합니다.

	![](./media/app-service-api-authentication-client-flow/signedin.png)

7. **정보**를 클릭합니다.

	정보 컨트롤러 및 보기에 추가한 코드는 API 앱에 성공적으로 인증하고 해당 Get 메서드 호출하였음을 실행하고 보여줍니다.

	![](./media/app-service-api-authentication-client-flow/aboutpage.png)

## 문제 해결

### HTTP 400 오류 

게이트웨이 URL이 http가 아니라 https 인지 확인합니다. Azure 미리 보기 포털에서 직접 복사 하는 경우 http를 지정할 수 있습니다.
 
### SQL Server 오류

응용 프로그램은 GroupClaimContext 연결 문자열에 의해 식별되는 SQL Server 데이터베이스에 액세스해야 합니다. 배포된 사이트의 연결 문자열이 SQL 데이터베이스 인스턴스를 가리키는지 확인합니다. 배포된 Web.config에 또는 Azure 런타임 구성 설정에 올바른 연결 문자열을 넣을 수 있습니다.

## 승인

이 자습서를 개발하는 데 도움을 주신 Govind S. Yadav ([@govindsyadav](https://twitter.com/govindsyadav))께 감사드립니다.

## 다음 단계

앱 서비스 API 앱에 대해 클라이언트 흐름 인증하는 방법을 살펴보았습니다. API 앱에서 인증을 사용하는 방법에 대한 자세한 내용은 [API 앱 및 모바일 앱 인증](../app-service/app-service-authentication-overview.md)을 참조하세요.

[Azure 포털]: https://manage.windowsazure.com/
[Azure 미리 보기 포털]: https://portal.azure.com/

<!---HONumber=August15_HO8-->
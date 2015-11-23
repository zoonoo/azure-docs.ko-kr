<properties 
	pageTitle=".NET 클라이언트의 Azure 앱 서비스에서 내부 API 앱 사용" 
	description="앱 서비스 SDK를 사용하여 동일한 리소스 그룹의 .NET API 앱에서 API 앱을 사용하는 방법을 알아봅니다." 
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
	ms.date="10/30/2015" 
	ms.author="bradyg"/>

# .NET 클라이언트의 Azure 앱 서비스에서 내부 API 앱 사용 

## 개요

이 자습서에서는 **내부** 액세스 수준에 대해 구성된 다른 API 앱을 호출하는 ASP.NET [API 앱](app-service-api-apps-why-best-platform.md)에 대한 코드를 작성하는 방법을 보여 줍니다. 두 API 앱 모두 동일한 리소스 그룹에 있어야 합니다. 동일한 코드를 사용하여 [모바일 앱](../app-service-mobile/app-service-mobile-value-prop-preview.md)에서 내부 API 앱을 호출할 수 있습니다.

ContactNames Web API를 빌드합니다. Web API의 Get 메서드는 ContactsList API 앱을 호출하고 ContactsList API 앱에서 제공하는 연락처 정보 중 이름만 반환합니다. ContactNames Get 메서드 호출에 성공한 경우의 Swagger UI 화면은 다음과 같습니다.

![](./media/app-service-api-dotnet-consume-internal/tryitout.png)

**공용(익명)** 또는 **공용(인증)** 액세스 수준에 대해 구성된 API 앱을 호출하는 방법은 [.NET 클라이언트의 Azure 앱 서비스에서 API 앱 사용](app-service-api-dotnet-consume.md)을 참조하세요.

## 필수 조건

이 자습서에서는 프로젝트를 만들고 Visual Studio에서 프로젝트에 코드를 추가하는 방법 및 [Azure Preview포털에서 API 앱을 관리하는](../app-service-api-apps-manage-in-portal.md) 방법을 잘 알고 있다고 가정합니다.

이 문서의 프로젝트 및 코드 샘플은 다음 문서에서 만들고 배포하는 API 앱 프로젝트를 기반으로 합니다.

- [API 앱 만들기](app-service-dotnet-create-api-app.md)
- [API 앱 배포](app-service-dotnet-deploy-api-app.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

이 자습서에는 Azure SDK for .NET 버전 2.6 이상이 필요합니다.

### 대상 API 앱 설정

1. [API 앱 배포](app-service-dotnet-deploy-api-app.md) 자습서에 따라 Azure 구독에서 API 앱에 ContactsList 샘플 프로젝트를 배포합니다(아직 배포하지 않은 경우).

2. [Azure Preview 포털](https://portal.azure.com/)의 이전에 배포한 ContactsList API 앱에 대한 **API 앱** 블레이드에서 **설정 > 응용 프로그램 설정**을 클릭하고 **액세스 수준**을 **내부**로 설정한 후 **저장**을 클릭합니다.

	![](./media/app-service-api-dotnet-consume-internal/setinternal.png)
 
## 기존 API 앱을 호출하는 새 API 앱 만들기

- Visual Studio에서 Azure API 앱 프로젝트 템플릿을 사용하여 ContactNames라는 API 앱 프로젝트를 만듭니다.

	[API 앱 만들기](app-service-dotnet-create-api-app.md)와 프로세스가 동일하지만 이 자습서의 뒷부분에서는 프로젝트에 다른 코드를 추가합니다.
 
## 앱 서비스 SDK에서 생성된 클라이언트 코드 추가

다음 단계는 [.NET 클라이언트의 Azure 앱 서비스에서 API 앱 사용](app-service-api-dotnet-consume.md)에 자세히 설명되어 있습니다.

3. **솔루션 탐색기**에서 프로젝트(솔루션이 아님)를 마우스 오른쪽 단추로 클릭하고 **추가 > Azure API 앱 클라이언트**를 선택합니다. 

3. **Azure API 앱 클라이언트 추가** 대화 상자에서 **Azure API 앱에서 다운로드**를 클릭합니다.

5. 드롭다운 목록에서 호출할 API 앱을 선택합니다. 이 자습서에서는 이전에 만든 ContactsList API 앱을 선택합니다.

7. **확인**을 클릭합니다.

## Swagger UI 사용

기본적으로 API 앱 프로젝트는 자동 [Swagger](http://swagger.io/ "공식 Swagger 정보") 메타데이터 생성을 통해 사용하도록 설정되지만 Azure API 앱 new-project 템플릿에서 API 테스트 페이지를 사용하지 않도록 설정합니다. 이 섹션에서는 테스트 페이지를 사용하도록 설정합니다.

1. *App\_Start/SwaggerConfig.cs* 파일을 열고 **EnableSwaggerUI**를 검색합니다.

2. 다음 코드 줄의 주석 처리를 제거합니다.

	        })
	    .EnableSwaggerUi(c =>
	        {

## 컨트롤러 만들기

5. **Controllers** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 > 컨트롤러**를 선택합니다. 

6. **스캐폴드 추가** 대화 상자에서 **Web API 2 컨트롤러 - 비어 있음** 옵션을 선택하고 **추가**를 클릭합니다.

7. 컨트롤러 이름을 **ContactNamesController**로 지정하고 **추가**를 클릭합니다.

## API 앱을 호출하는 코드 추가

해당 액세스 수준을 **내부**로 설정하여 보호된 API 앱을 호출하려면 HTTP 요청에 내부 인증 헤더를 추가해야 합니다. 이러한 헤더는 대상 API 앱에 호출 원본이 동일한 리소스 그룹 내에서 호출되는 피어 API 앱임을 알립니다.

앱 서비스 SDK는 API 앱을 호출하기 위해 작성하는 코드를 간소화하는 클라이언트 클래스를 생성합니다. **공용(익명)** API 앱을 호출하려면 다음 예제와 같이 클라이언트 개체를 만들고 이 개체에서 메서드를 호출하기만 하면 됩니다.

		var client = new ContactsList();
		var contacts = await client.Contacts.GetAsync();

그러나 인증 헤더를 추가하려면 `HttpRequestMessage` 개체에 대한 액세스 권한이 필요한데, 이 예제에서는 이러한 권한이 없습니다. 요청에 대한 액세스 권한을 얻고 헤더를 추가하려면 `DelegatingHandler` 클래스를 만들어 해당 인스턴스를 생성된 클라이언트의 생성자에 전달해야 합니다.

1. *InternalCredentialHandler.cs*라는 클래스 파일을 프로젝트에 추가하고 템플릿 코드를 다음 코드로 바꿉니다.

		using Microsoft.Azure.AppService.ApiApps.Service;
		using System.Net.Http;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace ContactNames
		{
		    public class InternalCredentialHandler : DelegatingHandler
		    {
		        protected override Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		        {
		            Runtime.FromAppSettings(request).SignHttpRequest(request);
		            return base.SendAsync(request, cancellationToken);
		        }
		    }
		}

	이 코드는 `SignHttpRequest`를 호출하여 생성된 클라이언트 클래스에서 보내는 모든 요청에 인증 헤더를 추가합니다.

		Runtime.FromAppSettings(this.Request).SignHttpRequest

1. *ContactNamesController.cs*에서 템플릿 코드를 다음 코드로 바꿉니다.

		using ContactNames.Models;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactNames.Controllers
		{
		    public class ContactNamesController : ApiController
		    {
		        [HttpGet]
		        public async Task<IEnumerable<string>> Get()
		        {
		            var names = new List<string>();

		            var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });
		            var contacts = await client.Contacts.GetAsync();
		
		            foreach (Contact contact in contacts)
		            {
		                names.Add(contact.Name);
		            }		
		            return names;
		        }
		    }
		}

	이 코드는 생성된 클라이언트 클래스의 생성자에 처리기를 전달합니다.

		var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });

### 배포

로컬로 실행하여 테스트할 수 없습니다. 코드를 배포하고 Azure API 앱에서 실행해야 합니다. 그렇지 않으면 올바른 인증 헤더를 추가할 수 없으므로 호출이 거부됩니다.

다음 배포 단계는 [API 앱 배포](app-service-dotnet-deploy-api-app.md)에 자세히 설명되어 있습니다.

1. ContactNames API 앱을 만듭니다.

	* **솔루션 탐색기**에서 솔루션이 아니라 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다. 

	* **프로필** 탭을 클릭한 다음 **Microsoft Azure API 앱**을 클릭합니다.

	* **새로 만들기**를 클릭하여 Azure 구독에서 새 API 앱을 프로비전합니다.

	* **API 앱 만들기** 대화 상자에서 **API 앱 이름**으로 ContactNames를 입력합니다.

	* **API 앱 만들기** 대화 상자의 다른 값에는 이전에 [API 앱 배포](app-service-dotnet-deploy-api-app.md)에 대해 입력한 것과 동일한 값을 입력합니다.

		특히, 호출하려는 API 앱과 동일한 리소스 그룹에 새 API 앱을 만들어야 합니다.

	* **확인**을 클릭합니다.

2. 새 API 앱에 코드를 배포합니다.

	* API 앱이 프로비전되고 나면 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭하여 게시 대화 상자를 다시 엽니다.

	* **웹 게시** 대화 상자에서 **게시**를 클릭하여 배포 프로세스를 시작합니다.

### 테스트

이 섹션에서는 Swagger UI를 사용하여 새 API 앱을 테스트하고 이 앱에서 이전에 만든 API 앱을 호출할 수 있는지 확인합니다.

1. 브라우저를 열고 새 API 앱의 URL로 이동합니다.

	기본 게시 설정을 사용할 경우 Visual Studio에서 게시 프로세스를 완료하면 자동으로 브라우저가 열리고 API 앱의 URL로 이동합니다. 그렇지 않거나 해당 브라우저 창을 닫은 경우 다음 단계에 따라 동일한 URL로 이동할 수 있습니다.

	* Azure Preview 포털에서 새 ContactsName API 앱에 대한 API 앱 블레이드로 이동합니다.

	* **URL**을 클릭합니다.

		![](./media/app-service-api-dotnet-consume-internal/clickurl.png)
  
5. 브라우저 주소 표시줄에서 URL의 끝에 `/swagger`를 추가한 후 Enter 키를 누릅니다.

	예를 들어 결과 URL은 다음과 같습니다.

		https://microsoft-apiapp214f26e673e5449a214f26e673e5449a.azurewebsites.net/swagger

1. Swagger UI 페이지에서 **ContactNames > Get > Try it out!**을 클릭합니다.

	![](./media/app-service-api-dotnet-consume-internal/tryitout.png)
  
	페이지의 Response Body 섹션에 연락처 이름이 표시되며, 이는 ContactNames API 앱이 ContactsList API 앱에서 데이터를 성공적으로 검색했음을 확인합니다.

	**내부** 설정이 ContactsList API 앱을 보호하는지 확인하려면 *ContactNamesController.cs*에서 `SignHttpRequest` 호출을 주석 처리하고 다시 배포한 후 Swagger **Try it now**를 다시 실행합니다. 그러면 오류 메시지가 나타납니다.


## HttpClient를 사용하여 API 앱을 호출하는 코드를 추가합니다.
 
앱 서비스 SDK는 Swagger API 정의에 따라 클라이언트 클래스를 생성합니다. Swagger API 정의를 구현하지 않은 API 앱을 호출하려면 `HttpClient`를 사용하면 됩니다. 이 경우 `SignHttpRequest` 메서드를 계속 사용하지만 `HttpRequestMessage` 개체를 직접 호출합니다.

1. *ContactNamesController.cs*에서 `Get` 메서드의 `return names;` 문 앞에 다음 코드를 추가합니다.

		var httpClient = new HttpClient();
		HttpRequestMessage httpRequest = new HttpRequestMessage();
		httpRequest.Method = HttpMethod.Get;
		httpRequest.RequestUri = new Uri("https://{yourapiappurl}/api/contacts");
		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);
		var response = await httpClient.SendAsync(httpRequest); 
		var contacts2 = await response.Content.ReadAsAsync<List<Contact>>();
		foreach (Contact contact in contacts2)
		{
		    names.Add(contact.Name);
		}

	이 코드는 나가는 요청 개체를 서명하기 위해 들어오는 요청 개체를 `SignHttpRequest` 메서드로 전달합니다.

		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);

2. Azure Preview 포털에서 ContactsName API 앱에 대한 API 앱 블레이드로 이동하여 URL을 복사합니다.

	![](./media/app-service-api-dotnet-consume-internal/clurl.png)
 
4. 컨트롤러 코드의 자리 표시자 문자열 "{yourapiappurl}"을 실제 URL로 바꿉니다. 완료되면 이 코드 줄이 다음 예제와 같이 표시됩니다.

		httpRequest.RequestUri = new Uri("https://microsoft-apiappd99e684d99e684d99e684d99e684.azurewebsites.net/api/contacts");

### 배포 및 테스트

1. 이전에 API 앱 코드를 배포할 때와 동일한 절차를 따릅니다.

	**힌트:** **웹 게시** 대화 상자를 무시하고 도구 모음에 있는 단일 단추를 클릭하여 다시 배포할 수 있습니다. Visual Studio에서 **보기 > 도구 모음**을 클릭하고 **한 번 클릭으로 웹 게시** 도구 모음을 활성화합니다.
 
2. 이전에 Swagger UI를 사용할 때와 동일한 절차를 따릅니다.

	HttpClient 코드를 그대로 두었기 때문에 이 출력에는 중복된 이름 집합이 표시됩니다.

	![](./media/app-service-api-dotnet-consume-internal/dupnames.png)
  
## 다음 단계

이 문서에서는 .NET 클라이언트에서 내부 API 앱을 사용하는 방법을 알아보았습니다. **공용(익명)** 또는 **공용(인증)** 액세스 수준으로 설정된 API 앱을 사용하는 방법은 [.NET 클라이언트의 Azure 앱 서비스에서 API 앱 사용](app-service-api-dotnet-consume.md)을 참조하세요.

.NET 클라이언트에서 API 앱을 호출하는 코드에 대한 추가 예제를 보려면 [Azure Cards](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample) 샘플 응용 프로그램을 다운로드하세요.

앱 서비스의 인증에 대한 자세한 내용은 [API 앱 및 모바일 앱 인증](../app-service/app-service-authentication-overview.md)을 참조하세요.
 

<!---HONumber=Nov15_HO3-->
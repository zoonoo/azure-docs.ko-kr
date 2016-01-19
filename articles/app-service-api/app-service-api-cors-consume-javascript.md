<properties
	pageTitle="CORS를 사용하여 JavaScript에서 API 앱 사용 | Microsoft Azure"
	description="CORS를 사용하여 JavaScript 클라이언트에서 Azure 앱 서비스의 API 앱을 사용하는 방법을 알아봅니다."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/05/2016"
	ms.author="tdykstra"/>

# CORS를 사용하여 JavaScript에서 API 앱 사용

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 개요

이 자습서에는 API 앱이 아닌 다른 도메인에서 제공되는 웹 사이트의 JavaScript 코드에서 API 앱을 사용하는 방법을 보여 줍니다. 샘플 클라이언트는 AngularJS를 사용합니다.

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
이 자습서는 Azure 앱 서비스에서 API 앱으로 작업에 대해 보여 주는 자습서 시리즈의 두 번째 자습서입니다. 이 시리즈의 처음으로 이동하려면 페이지 맨 위에 있는 **항목** 드롭다운 목록에서 첫 번째 항목을 선택합니다.

## Azure 앱 서비스에서 CORS 지원

보안상의 이유로 브라우저의 기본 동작에서는 JavaScript가 해당 JavaScript와 함께 제공되는 도메인 이외의 다른 도메인을 호출하는 것을 방지합니다. 예를 들어 contoso.com 웹 페이지에서는 contoso.com API 끝점을 호출할 수 있지만 fabrikam.com 끝점을 호출할 수는 없습니다. CORS(원본 간 리소스 공유)는 이러한 도메인 간 API 호출이 필요한 시나리오를 지원하도록 설계된 인터넷 프로토콜입니다. Azure 앱 서비스에서 이러한 시나리오의 예를 들면 JavaScript 클라이언트가 웹앱에서 실행되는 동안 API는 API 앱에서 실행되는 경우입니다.

Azure 앱 서비스는 API 앱을 호출할 수 있는 도메인을 구성하는 간편한 방법을 제공하고 CORS 기능은 API 앱 서비스가 지원하는 모든 언어(예: Java 및 Node.js)에 대해 동일하게 작동합니다.

## 이 자습서를 진행하는 방법

이 자습서는 [API 앱 및 ASP.NET 시작 시리즈의 첫 번째 자습서](app-service-api-dotnet-get-started.md)에서 다운로드하여 만든 API 앱에 대한 응용 프로그램 예제에서 작동합니다.

Java 또는 Node.JS 시작 자습서를 수행하는 경우 모든 API 앱에 적용되는 일반적인 지침에 대한 [CORS 구성 섹션](#corsconfig)으로 이동합니다.

## ContactsList.Angular 샘플 프로젝트

[ContactsList 응용 프로그램 예제](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list)에서 ContactsList.Angular 프로젝트는 ContactsList.API Web API 프로젝트의 간단한 AngularJS 클라이언트입니다.

이 API를 호출하는 AngularJS JavaScript 코드는 ContactsList.Angular 프로젝트의 *index.html* 파일에 있습니다. 이 코드는 아래와 같이 함수를 정의하고 이를 `$scope` 개체에 추가합니다. 여기서 API의 Get 메서드는 `$scope.refresh()`로 정의됩니다.

		angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
		    $scope.baseurl = 'http://localhost:51864';
		
		    $scope.refresh = function () {
		        $scope.status = "Refreshing Contacts...";
		        $http({
		            method: 'GET',
		            url: $scope.baseUrl + '/api/contacts',
		            headers: {
		                'Content-Type': 'application/json'
		            }
		        }).then(function (results) {
		            $scope.contacts = results.data;
		            $scope.status = "Contacts loaded";
		        }, function (err) {
		            $scope.status = "Error loading contacts";
		        });
		    };
		
		    // POST and DELETE not shown
		
		    $scope.refresh();
		});

이 코드는 페이지가 로드(위에 표시된 코드 조각의 끝부분)되고 UI의 **새로 고침** 단추에 연결되면 $scope.refresh() 메서드를 호출합니다.

		<th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>

## 로컬로 AngularJS 프로젝트 실행

이 섹션에서는 API가 로컬로 실행되는 동안 클라이언트를 로컬로 실행하고 해당 API를 호출할 수 있는지 확인합니다.

**참고:** 이러한 지침은 해당 브라우저에서 `http://localhost` URL에서/로 크로스-원본 JavaScript 호출을 허용하므로 Internet Explorer 및 Edge 브라우저에서 작동합니다. Chrome을 사용하는 경우 `--disable-web-security` 스위치로 브라우저를 시작합니다. Firefox를 사용하는 경우 이 섹션을 건너뛰십시오.

1. ContactsList.API가 ContactsList.Angular보다 먼저 시작되도록 지정하여 ContactsList.API 및 ContactsList.Angular 프로젝트를 시작 프로젝트로 설정합니다. 

2. F5 키를 눌러 프로젝트를 시작합니다.

	AngularJS UI는 로컬로 저장된 연락처를 표시하므로 이 UI를 사용하여 연락처를 추가 및 삭제할 수 있습니다.

	![](./media/app-service-api-cors-consume-javascript/homepagelocal.png)

3. 브라우저 창을 닫습니다.

## Azure API 앱을 가리키도록 AngularJS 프로젝트 변경 

다음으로, 클라우드에서 AngularJS 프런트 엔드를 실행하고 클라우드에서 실행 중인 API 백 엔드를 호출합니다. 이때 코드에서 이전에 만든 Azure API 앱이 호출되도록 프런트 엔드를 Azure에 배포하기 전에 AngularJS 프로젝트에서 API 끝점을 변경해야 합니다.

1. ContactsList.Angular 프로젝트에서 *index.html* 을 엽니다.

2. `baseUrl`을 localhost URL로 설정하는 줄을 주석 처리하고 `baseUrl`을 azurewebsites.net URL로 설정하는 줄의 주석을 제거한 후 자리 표시자를 이전에 만든 API 앱의 실제 이름으로 바꿉니다. API 앱 이름을 ContactsListAPI로 지정하면 코드가 다음 예제와 같이 표시됩니다.

		$scope.baseUrl = 'https://ContactsListAPI.azurewebsites.net';
		//$scope.baseUrl = 'http://localhost:51864';

### 웹앱에 ContactsList.Angular 프로젝트 배포

AngularJS 프로젝트를 배포할 새 웹앱을 만들 수 있지만 이 자습서에서는 이전 자습서에서 만든 동일한 웹앱에 배포합니다. 웹앱 이름에는 원래 ASP.NET MVC 프로젝트를 배포했다는 사실이 반영될 수 있지만 이 배치 후에는 AngularJS 코드가 실행됩니다.

8. **솔루션 탐색기**에서 ContactsList.Angular 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

9. **프로필** 탭을 클릭합니다.

3.  **웹 게시** 마법사의 **프로필** 단계에서 **Microsoft Azure 앱 서비스**를 클릭합니다.

4. **앱 서비스** 대화 상자에서 구독을 선택합니다.

5. **보기**를 기본값인 **리소스 그룹**으로 설정한 상태에서 이 자습서 시리즈에 대해 만든 리소스 그룹을 확장합니다.

7. 첫 번째 자습서에서 만든 웹앱을 선택하고(API 앱을 선택하지 않도록 해야 함) **확인**을 클릭합니다.

8. **설정** 탭을 클릭합니다.

9. **파일 게시 옵션**을 확장하고 **대상에서 추가 파일 제거**를 선택합니다.

	![](./media/app-service-api-cors-consume-javascript/removeadditionalfiles.png)

	일반적으로 웹 프로젝트를 기존 앱 서비스 웹앱에 배포할 때는 변경 내용이 업데이트 또는 새 파일이므로 "추가 파일 제거" 옵션을 선택하지 않습니다. 이 예제에서는 동일한 웹앱에 다른 프로젝트를 배포하므로 이전 배포의 많은 파일이 새 배포에 필요하지 않을 수 있습니다.

10. **게시**를 클릭합니다.

	Visual Studio에서 ContactsList.Angular 프로젝트를 웹앱에 배포하고 해당 웹앱의 URL로 브라우저를 엽니다. 이 브라우저에는 로컬로 실행할 때와 동일한 AngularJS UI가 표시되지만 지금은 프런트 엔드가 백 엔드(API 앱 URL)와 다른 도메인(웹앱 URL)에서 실행되기 때문에 브라우저가 실패합니다.

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

## <a id="corsconfig"></a> Azure에서 대상 API 앱에 대한 CORS 구성

8. 다른 브라우저 창에서 [Azure 포털](https://portal.azure.com/)로 이동합니다.

9. **찾아보기 > API 앱**을 클릭한 후 대상 API 앱을 선택합니다. 이 자습서에서는 ContactsList.API 프로젝트에 대한 첫 번째 자습서에서 만든 API 앱입니다.

10. **API 앱** 블레이드에서 **설정**을 클릭합니다.

11. **API** 섹션을 찾은 다음 **CORS**를 클릭합니다.

12. 텍스트 상자에서 호출을 허용하려는 URL을 입력합니다. 예를 들어 ContactsListMVC라는 웹앱에 JavaScript 응용 프로그램을 배포한 경우 "http://contactslistmvc.azurewebsites.net"를 입력합니다.

	URL을 입력하는 대신 별표(*)를 입력하여 모든 원본 도메인이 허용되도록 지정할 수 있습니다.

13. **Save**를 클릭합니다.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. AngularJS 클라이언트가 표시된 브라우저 창으로 이동하여 페이지를 새로 고치거나 **새로 고침** 단추를 클릭합니다.

	이제 Azure API 앱의 파일 시스템에 저장된 연락처가 페이지에 표시됩니다.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

### Azure 리소스 관리자 도구에서 CORS

Azure PowerShell, CLI 또는 [리소스 탐색기](https://resources.azure.com/) 등의 Azure 리소스 관리자 도구를 사용하여 API 앱에 대한 CORS를 구성할 수도 있습니다.

Microsoft.Web/sites/config 리소스 종류에서 <site name>/web 리소스에 대해 `cors` 속성을 설정합니다. 예를 들어, **리소스 탐색기**에서 **구독 > {구독} > resourceGroups > {리소스 그룹} > 공급자 > Microsoft.Web > 사이트 > {사이트} > 구성 > 웹**으로 이동하면 cors 속성이 표시됩니다.

		"cors": {
		    "allowedOrigins": [
		        "contactslistmvc.azurewebsites.net"
		    ]
		}

## Web API 코드에서 CORS 지원

Web API 프로젝트에서는 [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet 패키지를 설치하여 API가 JavaScript 호출을 허용할 도메인을 코드에서 지정할 수 있습니다.
 
Web API CORS 지원은 앱 서비스 CORS 지원보다 유연성이 뛰어납니다. 예를 들어 코드에서 다른 작업 메서드에 대해 다른 허용된 원본을 지정할 수 있는 반면 앱 서비스 CORS의 경우 모든 API 앱의 메서드에 대해 허용된 원본 집합을 지정합니다.

### Web API CORS보다 우선하는 앱 서비스 CORS

하나의 API 앱에서 Web API CORS와 앱 서비스 CORS를 함께 사용하지 마십시오. 앱 서비스 CORS가 우선적으로 적용되며 Web API CORS는 아무 효과가 없습니다. 예를 들어 앱 서비스에서 하나의 원본 도메인을 사용하도록 설정하고 Web API 코드에서 모든 원본 도메인을 사용하도록 설정한 경우 Azure API 앱은 Azure에서 지정한 도메인의 호출만 허용합니다.

### Web API 코드에서 CORS를 설정하는 방법

다음 단계는 Web API CORS 지원 설정에 대한 프로세스를 간략하게 설명합니다. 자세한 내용은 [ASP.NET Web API 2에서 크로스-원본 리소스 요청 사용](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)을 참조하세요.

1. Web API 프로젝트에서 다음 예제와 같이 **WebApiConfig**의 **Register** 메서드에 `config.EnableCors()` 코드 줄을 포함합니다. 

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
		        // The following line enables you to control CORS by using Web API code
				config.EnableCors();
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. Web API 컨트롤러에서 `EnableCors` 특성을 `ContactsController` 클래스 또는 개별 작업 메서드에 추가합니다. 다음 예제에서 CORS 지원은 전체 컨트롤러에 적용됩니다.

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	> **참고**: `EnableCors` 특성에서 모든 매개 변수에 와일드카드를 사용한 것은 데모만을 위한 것이며, 모든 원본 및 모든 HTTP 요청까지 API를 개방합니다. 이 특성을 사용할 때 주의하세요.

## 다음 단계 

이 자습서에서는 클라이언트 JavaScript 코드가 다른 도메인에서 API를 호출할 수 있도록 앱 서비스 CORS 지원을 사용하도록 설정하는 방법을 살펴보았습니다. API 앱 시작 시리즈의 다음 문서에서는 [앱 서비스 API 앱에 대한 인증](app-service-api-authentication.md)에 대해 알아봅니다.

<!---HONumber=AcomDC_0114_2016-->
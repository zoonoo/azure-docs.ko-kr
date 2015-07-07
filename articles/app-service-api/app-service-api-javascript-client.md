<properties 
	pageTitle="HTML 및 JavaScript를 사용하여 Azure API 앱 액세스" 
	description="HTML 및 JavaScript를 사용하여 API 앱 백 엔드에 액세스하는 방법을 알아봅니다." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="bradygaster"/>

# HTML 및 JavaScript를 사용하여 Azure API 앱 사용

## 개요

이 문서에서는 [Azure 앱 서비스](/documentation/services/app-service/)에서 [API 앱](app-service-api-apps-why-best-platform.md)용 HTML 및 JavaScript 클라이언트를 만드는 방법을 보여 줍니다. 이 문서에서는 HTML 및 JavaScript에 대한 실무 지식이 있다고 가정하며, [AngularJS](https://angularjs.org/) JavaScript 프레임워크를 사용하여 API 앱에 대한 REST 호출을 수행하합니다.

이 작업에 앞서 시작하는 데 도움이 되는 몇 가지 문서는 다음과 같습니다.

1. [API 앱 만들기](app-service-dotnet-create-api-app.md)에서는 API 앱 프로젝트를 만들었습니다.
2. [API 앱 배포](app-service-dotnet-deploy-api-app.md)에서는 API 앱을 Azure 구독에 배포합니다.
3. [API 앱 디버그](../app-service-dotnet-remotely-debug-api-app.md)에서는 Visual Studio를 사용하여 Azure에서 코드가 실행되는 동안 코드를 원격으로 디버그합니다.

이 문서는 이러한 이전 문서를 기반으로 하여 HTML 응용 프로그램에서 JavaScript를 사용하여 백 엔드 API 앱에 액세스하는 방법을 보여 줍니다.

## CORS 사용

일반적으로 CORS(크로스-원본 자원 공유)는 API 자체와 다른 호스트에서 제공되는 HTML 응용 프로그램에 필요합니다. API 앱에서 CORS를 사용하도록 설정하는 데에는 두 가지 이상의 옵션이 있습니다. 이 섹션에서는 이러한 옵션에 대해 간략히 설명합니다.

### API 앱 게이트웨이에 대해 CORS 설정

Azure Preview 포털에서 CORS를 사용하도록 API 앱 게이트웨이를 구성할 수 있습니다. **MS_CrossDomainOrigins** *appSetting*을 추가하면 API 앱을 호출할 수 있는 URL을 지정할 수 있습니다. 이 섹션에서는 이 *appSetting*을 사용하여 API 게이트웨이 수준에서 CORS를 사용하도록 설정하는 방법을 설명합니다.

1. CORS를 사용하도록 설정할 API 앱의 Azure Preview 포털 블레이드로 이동합니다. 여기에서 API 앱에 대한 *게이트웨이* 아이콘을 클릭합니다. 

	![API 앱 게이트웨이 단추 클릭](./media/app-service-api-javascript-client/19-api-app-blade.png)

1. 포털 블레이드에서 **게이트웨이 호스트** 링크를 클릭합니다.

	![API 앱 게이트웨이 호스트 링크 클릭](./media/app-service-api-javascript-client/20-gateway-host-blade.png)

1. 포털 블레이드에서 **모든 설정** 링크를 클릭합니다.

	![게이트웨이 모든 설정 링크](./media/app-service-api-javascript-client/21-gateway-blade-all-settings.png)

1. 포털 블레이드에서 **응용 프로그램 설정** 단추를 클릭합니다.

	![게이트웨이 응용 프로그램 설정](./media/app-service-api-javascript-client/22-gateway-app-settings-blade.png)

1. **MS_CrossDomainOrigins** 응용 프로그램 설정을 추가합니다. 설정 값을 API 앱에 대한 액세스를 제공할 HTTP 호스트의 쉼표로 구분된 목록으로 지정합니다. 여러 호스트에 대한 액세스를 제공하려는 경우 *appSetting* 값을 아래 코드와 같이 설정할 수 있습니다.

		http://foo.azurewebsites.net, https://foo.azurewebsites.net, http://contactlistwebapp.azurewebsites.net

	아래 섹션에서는 API 앱과 동일한 Azure 리소스 그룹에서 실행되는 웹앱에서 API 앱을 호출하는 간단한 HTML 페이지가 포함된 별도의 웹 응용 프로그램을 만드는 프로세스를 안내합니다. 이 호스트에서만 API 앱에 액세스할 수 있으므로 하나의 호스트만 포함하도록 설정됩니다.

		http://contactlistwebapp.azurewebsites.net

	아래 스크린샷에서는 Azure Preview 포털에서 이 설정을 저장한 후의 화면을 보여 줍니다.

	![](./media/app-service-api-javascript-client/23-app-settings-set.png)

**MS_CrossDomainOrigins** 응용 프로그램 설정은 [Azure 모바일 서비스 .NET 업데이트](http://azure.microsoft.com/blog/2014/07/28/azure-mobile-services-net-updates/) 블로그 게시물에 자세히 설명되어 있으므로 설정에 대한 자세한 내용은 이 게시물을 참조하세요.

### Web API 코드에서 CORS 설정

Web API에서 CORS를 사용하도록 설정하는 프로세스는 ASP.NET 문서 [ASP.NET Web API 2에서 크로스-원본 요청 설정](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)에 자세히 설명되어 있습니다. ASP.NET Web API를 사용하여 빌드된 API 앱의 경우 프로세스가 정확히 동일하지만 아래에 요약되어 있습니다. CORS를 이미 사용하도록 설정한 경우에는 API 앱이 올바르게 설정되어 있으므로 이 섹션을 건너뛰세요.

1. CORS 기능은 [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet 패키지에서 제공됩니다. **패키지 관리자 콘솔**을 열어 설치하고 다음 PowerShell 스크립트를 실행합니다. 

		Install-Package Microsoft.AspNet.WebApi.Cors

1. 명령을 실행하고 나면 패키지 관리자 콘솔 및 **packages.config**에 추가된 새 NuGet 패키지가 반영됩니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/01-cors-installed.png)

1. *App_Start/WebApiConfig.cs* 파일을 엽니다. 이 파일에서 **WebApiConfig** 클래스의 **Register** 메서드에 아래 코드 줄을 추가합니다.

		config.EnableCors();

	파일이 업데이트되면 코드가 다음과 같이 변경됩니다.

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
				config.EnableCors(); /* -- NEW CODE -- */
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. CORS를 사용하도록 설정하는 마지막 단계는 사용할 개별 작업 메서드를 구분하는 것입니다. 아래 코드에 설명된 대로 각 메서드 또는 전체 컨트롤러에서 **EnableCors** 특성을 추가합니다.

	> **참고**: EnableCors 특성에서 모든 매개 변수에 와일드카드를 사용한 것은 데모만을 위한 것이며, 모든 원본 및 모든 HTTP 요청에 API를 개방합니다. 따라서 이 특성을 사용할 때는 주의해야 하며 의미를 이해해야 합니다.

		using ContactList.Models;
		using System.Collections.Generic;
		using System.Web.Http;
		using System.Web.Http.Cors;
	
		namespace ContactList.Controllers
		{
		    [EnableCors(origins:"*", headers:"*", methods: "*")] /* -- NEW CODE -- */
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]
		            {
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
		            };
		        }
		
		        [HttpPost]
		        public Contact Post([FromBody] Contact contact)
		        {
		            return contact;
		        }
		    }
		}
1. CORS 지원을 추가하기 전에 API 앱을 Azure에 이미 배포한 경우 Azure에서 호스트되는 API에서 CORS를 사용하도록 설정할 수 있게 코드를 다시 배포합니다. 

## API 앱을 사용하는 웹앱 만들기

이 섹션에서는 비어 있는 새 웹 응용 프로그램을 만들고 여기에 AngularJS를 설치하고 사용하며 간단한 HTML 프런트 엔드를 API 앱에 바인딩합니다. 사용하는 웹앱을 Azure 앱 서비스에 배포합니다. HTML 웹앱은 API 앱에 바인딩되고 API 앱에서 검색한 데이터를 표시하며, 사용자에게 Contacts API에 대한 간단한 UI를 제공합니다.

1. [API 앱 만들기](app-service-dotnet-create-api-app.md)에서 이전에 만든 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가 -> 새 프로젝트**를 선택합니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/02-add-project.png)

1. **ASP.NET 웹 응용 프로그램** 템플릿을 선택합니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/03-new-web-project.png)

1. One ASP.NET 대화 상자에서 **비어 있음** 템플릿을 선택합니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/04-empty-web.png)

1. **패키지 관리자** 또는 **NuGet 패키지 관리** 상황에 맞는 메뉴 항목을 사용하여 [AngularJS](https://www.nuget.org/packages/angularjs) NuGet 패키지를 설치합니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/05-install-angular.png)

1. **패키지 관리자** 또는 **NuGet 패키지 관리** 상황에 맞는 메뉴 항목을 사용하여 [Bootstrap](https://www.nuget.org/packages/bootstrap) NuGet 패키지를 설치합니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/05-install-bootstrap.png)

1. 웹앱 프로젝트에 새 HTML 파일을 추가합니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/06-new-html-file.png)

1. 파일 이름을 *index.html*로 지정합니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/07-index-html.png)

1. 부트스트랩 CSS 및 AngularJS JavaScript 파일을 HTML 페이지에 추가하고 간단한 부트스트랩 템플릿([참조 예제](http://getbootstrap.com/examples/starter-template/))을 사용하며 빈 스크립트 태그를 만들어 페이지를 준비합니다.
	
	> 참고: 아래 HTML 및 JavaScript 코드의 주석은 이 섹션의 후속 단계를 나타냅니다.

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Contacts HTML Client</title>
		    <link href="Content/bootstrap.css" rel="stylesheet" />
		    <style type="text/css">
		        body {
		            margin-top: 60px;
		        }
		    </style>
		</head>
		<body>
		
		    <nav class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
		                    <span class="sr-only">Toggle navigation</span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                <a class="navbar-brand" href="#">Contacts</a>
		            </div>
		            <div id="navbar" class="collapse navbar-collapse">
		                <ul class="nav navbar-nav"></ul>
		            </div>
		        </div>
		    </nav>
		
		    <div class="container">
		        <!-- contacts ui here -->
		    </div>
		
		    <script src="Scripts/angular.js" type="text/javascript"></script>
		    <script type="text/javascript">
		        /* client javascript code here */
		    </script>
		
		</body>
		</html>

1. 아래에 표시된 HTML 테이블 코드를 HTML의 **container** *div* 요소에 추가합니다.

		<!-- contacts ui here -->
        <table class="table table-striped" ng-app="myApp" ng-controller="contactListCtrl">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr ng-repeat="con in contacts">
                    <td>[[con.Id]]</td>
                    <td>[[con.Name</td>
                    <td>[[con.EmailAddress]]</td>
                    <td></td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <th>Create a new Contact</th>
                    <th colspan="2">API Status: [[status]]</th>
                    <th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>
                </tr>
                <tr>
                    <td><input type="text" placeholder="ID" ng-model="newId" /></td>
                    <td><input type="text" placeholder="Name" ng-model="newName" /></td>
                    <td><input type="text" placeholder="Email Address" ng-model="newEmail" /></td>
                    <td><button class="btn btn-sm btn-success" ng-click="create()">Create</button></td>
                </tr>
            </tfoot>
        </table>

1. `tbody`과 `tfoot`에서 요소는 [ with { and each ] with }를 각각 대체합니다. (이 사이트는 현재 코드 블록에서 이중 둥근 괄호 표현을 표시할 수 없습니다.)

2. *index.html* 파일을 마우스 오른쪽 단추로 클릭하고 **시작 페이지로 설정**을 선택합니다.

3. *index.html* 파일을 마우스 오른쪽 단추로 클릭하고 **브라우저에서 보기**를 클릭합니다.

	HTML 출력의 템플릿 핸들 모음을 확인합니다. 다음 단계에서 AngularJS를 사용하여 이러한 HTML 요소를 데이터 바인딩합니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/09-template-ui.png)

1. 아래 JavaScript 코드를 *index.html* 파일에 추가하여 API를 호출하고 HTML UI를 API 출력에 데이터 바인딩합니다.

		/* client javascript code here */
        angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
            $scope.baseUrl = 'http://localhost:1578';

            $scope.refresh = function () {
                $scope.status = "Refreshing Contacts...";
                $http({
                    method: 'GET',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    }
                }).success(function (data) {
                    $scope.contacts = data;
                    $scope.status = "Contacts loaded";
                }).error(function (data, status) {
                    $scope.status = "Error loading contacts";
                });
            };

            $scope.create = function () {
                $scope.status = "Creating a new contact";

                $http({
                    method: 'POST',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    data: {
                        'Id': $scope.newId,
                        'Name': $scope.newName,
                        'EmailAddress': $scope.newEmail
                    }
                }).success(function (data) {
                    $scope.status = "Contact created";
                    $scope.refresh();
                    $scope.newId = 0;
                    $scope.newName = '';
                    $scope.newEmail = '';
                });
            };

            $scope.refresh();
        });

1, index.html에 방금 추가한 코드에서 기본 URL(`http://localhost:1578`)의 포트 번호를 API 프로젝트에 대한 실제 포트 번호로 바꿉니다.

	> **Note** Don't use the port number of the HTML client project. You can right-click the API project and click **Debug > Start New Instance** to get a browser window that shows the port number.

1. HTML 클라이언트를 실행할 때 API 앱 프로젝트도 실행 중인지 또는 JavaScript HTML이 제대로 작동하지 않는지 확인합니다. 솔루션을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. 그런 다음 두 개의 웹 프로젝트를 모두 **디버깅하지 않고 시작**으로 설정하면 API 프로젝트가 먼저 실행됩니다. 

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/10-run-both-web-projects.png)

1. 솔루션을 실행하면 HTML/JavaScript 클라이언트가 API 앱 프로젝트에 연결되고 해당 프로젝트의 데이터를 표시합니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/11-web-client-running.png)

## 웹앱 배포

이 섹션에서는 HTML/JavaScript 클라이언트를 앱 서비스 웹앱으로 배포합니다. 배포가 완료되면 JavaScript에서 배포된 API 앱을 소비하기 위해 사용하는 URL을 변경합니다.

> 참고:이 섹션에서는 [API 앱 배포](app-service-dotnet-deploy-api-app.md) 문서를 읽고 완료하거나 이전에 사용자 고유의 API 앱을 배포했다고 가정합니다.

1. Azure Preview포털에서 API 앱의 블레이드를 엽니다. 블레이드에서 URL을 클릭하여 브라우저에서 엽니다. URL이 열리면 브라우저 주소 표시줄에서 API 앱의 URL을 복사합니다. 

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/12-open-api-app-from-blade.png)

1. API 앱의 URL을 붙여 넣어 JavaScript 코드에서 **$scope.baseUrl** 속성의 이전 값을 덮어씁니다.

		$scope.baseUrl = 'https://microsoft-apiappf7e042ba8e5233ab4312021d2aae5d86.azurewebsites.net';

	URL이 HTTPS을 지정합니다. HTTPS 사용은 선택 사항이 아닙니다. API 앱은 HTTP를 지원하지 않습니다.

1. HTML/JavaScript 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 상황에 맞는 메뉴 항목을 선택합니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/13-publish-web-app.png)

1. 웹 게시 대화 상자에서 **Microsoft Azure 웹앱** 옵션을 선택합니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/14-publish-web-dialog.png)

1. **새로 만들기** 단추를 클릭하여 새 웹앱을 만듭니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/15-new-web-app.png)

1. API 앱이 이미 실행 중인 동일한 앱 호스팅 계획 및 리소스 그룹을 선택합니다.

	> **참고**: 이는 요구 사항이 아니라 데모용이며, 모든 항목이 하나의 리소스 그룹에 포함된 경우 나중에 Azure 리소스를 보다 쉽게 정리하기 위한 것입니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/16-new-web-app-creation-dialog.png)

1. 웹 게시 단계를 완료하여 HTML/JavaScript 클라이언트를 앱 서비스 웹앱에 배포합니다.
1. 웹앱을 배포하면 웹앱이 웹 브라우저에서 자동으로 열리며 API 앱의 데이터를 표시합니다. 

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/17-web-app-running-in-ie.png)

1. 이제 리소스 그룹을 탐색하면 새 웹앱이 API 앱과 함께 실행되고 있는 것을 볼 수 있습니다.

	![솔루션 탐색기의 apiapp.json 및 Metadata](./media/app-service-api-javascript-client/18-web-app-visible-in-resource-group.png)

## 요약 
이 예제에서는 AngularJS를 API 앱 백 엔드에 액세스하기 위한 JavaScript 플랫폼으로 사용하는 방법을 알아보았습니다. REST 액세스 기능을 변경하여 다른 JavaScript 프레임워크를 사용할 수 있습니다.

 

<!---HONumber=62-->
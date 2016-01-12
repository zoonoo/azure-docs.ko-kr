<properties
	pageTitle="Azure 앱 서비스의 API 앱에 대한 사용자 인증 | Microsoft Azure"
	description="인증된 사용자에게만 액세스를 허용하여 Azure 앱 서비스에서 API 앱을 보호하는 방법을 알아봅니다."
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
	ms.topic="hero-article"
	ms.date="11/30/2015"
	ms.author="tdykstra"/>

# Azure 앱 서비스의 API 앱에 대한 사용자 인증

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 개요

이 자습서에서는 Azure 앱 서비스의 인증 및 권한 부여 기능을 사용하여 API 앱을 보호하는 방법 및 최종 사용자 대신 API 앱을 사용하는 방법을 보여 줍니다. 이 자습서에 표시된 인증 공급자는 Azure Active Directory이며 API는 ASP.NET Web API이고 클라이언트는 브라우저에서 실행되는 AngularJS 단일 페이지 응용 프로그램입니다.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
## Azure 앱 서비스의 인증 및 권한 부여

이 자습서에 사용된 인증 기능에 대한 개요는 시리즈의 이전 자습서인 [Azure 앱 서비스의 API 앱에 대한 인증 및 권한 부여](app-service-api-authentication.md)를 참조하세요.

## 이 자습서를 진행하는 방법

이 자습서는 [API 앱 및 ASP.NET 시작 시리즈의 첫 번째 자습서](app-service-api-dotnet-get-started.md)에서 다운로드하여 만든 API 앱에 대한 응용 프로그램 예제를 기반으로 작성되었습니다.

## ContactsList.Angular.AAD 샘플 프로젝트

[ContactsList 샘플 응용 프로그램](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list)에서 ContactsList.Angular.AAD 프로젝트는 Azure Active Directory와 함께 작동하는 코드가 포함된 AngularJS 클라이언트입니다. 이 코드는 [Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi) 리포지토리에서 찾을 수 있는 AAD 샘플을 기반으로 합니다.

ContactsList.Angular.AAD 프로젝트의 코드는 보다 간단한 ContactsLists.Angular 프로젝트와 다르게 구성됩니다. API를 호출하는 코드는 ContactsList.Angular.AAD 프로젝트의 *app/scripts/contactsSvc.js* 파일에 있습니다.

		angular.module('contactsListApp')
		.factory('contactsSvc', ['$http', function ($http) {
		    //var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		    var apiEndpoint = "https://localhost:44300";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems: function () {
		            return $http.get(apiEndpoint + '/api/contacts');
		        },
		        getItem : function(id){
		            return $http.get(apiEndpoint + '/api/contacts/' + id);
		        },
		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/contacts', item);
		        },
		        putItem : function(item){
		            return $http.put(apiEndpoint + '/api/contacts/', item);
		        },
		        deleteItem : function(id){
		            return $http({
		                method: 'DELETE',
		                url: apiEndpoint + '/api/contacts/' + id
		            });
		        }
		    };
		}]);

여기서 `Get` 메서드는 `getItems`로 레이블이 지정됩니다. 컨트롤러(*app/scripts/contactsCtrl.js*)에서 `getItems`는 `$scope.populate`에 연결됩니다.

		$scope.populate = function () {
		    contactsSvc.getItems().then(function (results) {
		        $scope.contactsList = results.data;
		        $scope.loadingMessage = "";
		    }, function (err) {
		        $scope.loadingMessage = "";
		        $scope.error = "Error: " + err;
		    });
		};

뷰(*app/views/Contacts.html*)에서 $scope.populate는 초기화 시 호출됩니다.

		<div ng-init="populate()">

API 요청을 통한 로깅 및 인증 토큰을 포함하기 위한 추가 코드는 *adal.js* 및 *adal angular.js* 파일의 [JavaScript용 Azure Active Directory 인증 라이브러리](https://github.com/AzureAD/azure-activedirectory-library-for-js)에 의해 제공됩니다.

*app.js* 파일에서 코드는 구성 정보 및 `$http` 공급자를 `adalProvider.init` 함수에 전달합니다. 구성 정보는 각 API 끝점에 관련된 AAD 응용 프로그램 클라이언트 ID 및 이 AngularJS 앱에 관련된 클라이언트 ID를 포함합니다. `init` 함수는 인터셉터를 `$http` 공급자에 추가하며 이는 권한 부여 토큰을 요청에 추가합니다.

		var endpoints = { 
		    //"https://{your api app name}.azurewebsites.net/": "{your client id}"
		    "https://localhost:44300/": "{your client id}"
		};

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: '{your tenant url}',
		        clientId: '{your client id}',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		        //cacheLocation: 'localStorage', // enable this for 
		    },
		    $httpProvider
		    );

## Azure에서 인증 및 권한 부여 설정

1. [Azure 포털](https://portal.azure.com/)에서 보호하려는 API 앱의 **API 앱** 블레이드로 이동하여 인증된 사용자만 호출할 수 있습니다. (이 자습서에서는 ContactsList.API 프로젝트를 배포할 API 앱을 선택합니다.)

2. **설정**을 클릭합니다.

2. **기능** 섹션을 찾아서 **인증/권한 부여**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. **인증/권한 부여** 블레이드에서 **설정**을 클릭합니다.

4. **요청이 인증되지 않은 경우에 수행할 동작** 드롭다운 목록에서 **Azure Active Directory를 사용하여 로그인**을 선택합니다.

5. **인증 공급자** 아래에서 **Azure Active Directory**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. **Azure Active Directory 설정** 블레이드에서 **Express**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	여기서 "Express"는 AAD 테넌트에 AAD 응용 프로그램이 자동으로 만들어진다는 의미입니다. 나중에 새 AAD 응용 프로그램의 클라이언트 ID를 가져오기 위해 클래식 Azure 포털로 이동할 경우 선택할 것이므로 새 AAD 응용 프로그램의 이름을 적어 둡니다.

7. **확인**을 클릭합니다.

10. **인증/권한 부여** 블레이드에서 **저장**을 클릭합니다.

8. 이제 API 앱이 보호되는지 확인하기 위해 첫 번째 자습서에서 Swagger UI를 사용하기 위해 수행한 것처럼 API 앱의 URL+`/swagger`로 이동합니다.

	이번에는 로그온 페이지로 리디렉션됩니다.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 **Azure Active Directory**로 이동합니다.

	액세스해야 하는 특정 Azure Active Directory 설정을 아직 현재 Azure 포털에서 사용할 수 없으므로 클래식 포털로 이동해야 합니다.

12. **디렉터리** 탭에서 AAD 테넌트를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. **응용 프로그램 > 회사 소유 응용 프로그램**을 클릭한 다음 확인 표시를 클릭합니다.

	페이지를 새로 고쳐야 새 응용 프로그램이 나타날 수도 있습니다.

15. 응용 프로그램 목록에서 API 앱에 대한 인증을 활성화할 때 만들어진 응용 프로그램의 이름을 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. **Configure**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. 페이지 아래쪽에서 **매니페스트 관리 > 매니페스트 다운로드**를 클릭하고 편집할 수 있는 위치에 파일을 저장합니다.

16. 다운로드한 매니페스트 파일에서 `oauth2AllowImplicitFlow` 속성을 검색합니다. 이 속성의 값을 `false`에서 `true`로 변경하고 파일을 저장합니다.

	이 설정은 JavaScript 단일 페이지 응용 프로그램에서 액세스를 위해 필요합니다. Oauth 2.0 전달자 토큰이 URL 조각에 반환되게 할 수 있습니다.

16. **매니페스트 관리 > 매니페스트 업로드**를 클릭하고 이전 단계에서 업데이트한 파일을 업로드합니다.

17. 자습서의 이후 단계에서 값을 복사하고 붙여 넣으며 페이지에서 값을 업데이트할 수 있도록 이 페이지를 열어 둡니다.

## Azure API 앱을 호출하도록 ContactsList.Angular.AAD 프로젝트 구성

다음 지침에서는 Azure에 응용 프로그램을 배포하고 실행하는 방법을 설명하지만 약간의 변경을 통해 로컬로 실행할 수 있습니다. 샘플 코드는 localhost URL 끝점을 포함합니다. 로컬에서 실행하려는 경우 SSL에 대한 프로젝트를 설정하고 프로젝트 코드에서 localhost SSL URL을 사용하며 AAD 응용 프로그램 구성에 localhost SSL URL을 사용합니다. 로컬에서 실행하는 동안 AngularJS 코드는 로그온한 사용자만 API를 호출하도록 하지만 다른 클라이언트에서 인증되지 않은 호출자가 API를 호출할 수 있습니다.

1. ContactsList.Angular.AAD 프로젝트에서 *app/scripts/app.js* 파일을 엽니다.

8. `endpoints` 변수를 설정하는 코드에서 localhost 끝점을 주석 처리하고 Azure 끝점의 주석을 제거합니다.

10. "yourclientid"을 AAD 응용 프로그램에 대한 클래식 포털의 **구성** 탭에서 AAD 응용 프로그램의 클라이언트 ID 실제 값으로 바꿉니다.

2. "{your api app name}"을 ContactsList.API 프로젝트를 배포한 API 앱의 이름으로 바꿉니다.

	이제 코드는 다음 예제와 유사합니다.

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. 또한 *app.js* 의 `adalProvider.init`에 대한 호출에서 "{your tenant url}" 및 "{your client id}"를 실제 값으로 바꿉니다.

	코드는 다음 예제와 유사합니다.

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

1. *app/scripts/contactsSvc.js* 파일의 경우 localhost에서 *app.js* 에서 이전에 수행한 API 앱 URL로 동일한 끝점 URL을 변경합니다.

	이제 코드는 다음 예제와 유사합니다.

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";

## Azure 웹앱에 대한 AAD 응용 프로그램 구성

1. 클래식 포털의 AAD 응용 프로그램에 대한 **구성** 탭에 있는 **로그온 URL** 필드에서 이미 존재하는 URL을 삭제하고 후행 슬래시를 포함하여 웹앱의 기본 URL로 바꿉니다. (API 앱의 URL이 아닌 AngularJS 코드를 실행하는 웹앱의 URL입니다.)

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. **회신 URL** 필드에서 이미 존재하는 URL을 웹앱의 기본 URL로 바꿉니다.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. **Save**를 클릭합니다.

## Azure에 ContactsList.Angular.AAD 프로젝트 배포

8. **솔루션 탐색기**에서 ContactsList.Angular.AAD 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

9. **Microsoft Azure 앱 서비스**를 클릭합니다.

10. **앱 서비스** 대화 상자의 **구독** 드롭다운 목록에서 구독을 선택합니다.

11. 이 자습서에 대해 만든 리소스 그룹을 확장하고 두 번째 자습서에서 만든 웹앱을 선택합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. **확인**을 클릭합니다.

12. **웹 게시** 마법사에서 **연결** 탭을 클릭하고 **대상 URL** 상자에서 `http://`를 `https://`로 변경합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

	이 설정은 성공적으로 배포된 후에 기본 브라우저를 열 수 있는 URL을 결정합니다.

12. **웹 게시** 마법사에서 **설정** 탭을 클릭하고 **파일 게시 옵션**을 확장한 다음 **대상에서 추가 파일 제거** 확인란을 선택합니다.

7. **게시**를 클릭합니다.

	Visual Studio에서 프로젝트를 배포하고 앱의 홈 페이지로 브라우저를 엽니다.

## Azure에서 AngularJS 웹앱 테스트

8. **연락처** 탭을 클릭합니다.

	로그인하라는 메시지가 표시됩니다.

9. AAD 테넌트의 사용자 자격 증명으로 로그인합니다.

10. **연락처** 페이지가 나타납니다.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

이제 프런트 엔드에서 인증된 사용자 대신 API를 호출할 수 있지만 인증되지 않은 사용자는 API를 호출할 수 없습니다.

## 다음 단계

이 자습서에서는 인증된 사용자만 호출할 수 있도록 앱 서비스 인증/권한 부여를 사용하여 API 앱에 대한 액세스를 제한했습니다. 시리즈의 다음 자습서에서는 [서비스 간 시나리오에 대해 API 앱에 대한 액세스를 제한](app-service-api-dotnet-service-principal-auth.md)하는 방법을 알아봅니다.

<!---HONumber=AcomDC_1217_2015-->

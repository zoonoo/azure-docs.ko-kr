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

이 자습서에서는 Azure 앱 서비스의 인증 및 권한 부여 기능을 사용하여 API 앱을 보호하는 방법 및 최종 사용자 대신 API 앱을 사용하는 방법을 보여 줍니다. 이 자습서에 표시된 인증 공급자는 Azure Active Directory이며, 샘플 클라이언트는 브라우저에서 실행되는 AngularJS 단일 페이지 응용 프로그램입니다.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
이 자습서는 Azure 앱 서비스에서 API 앱으로 작업하는 방법을 보여 주는 자습서 시리즈의 세 번째 자습서입니다. 시리즈에 대한 자세한 내용은 [Azure 앱 서비스에서 API 앱 및 ASP.NET 시작](app-service-api-dotnet-get-started.md)을 참조하세요.

## Azure 앱 서비스의 인증 및 권한 부여

Azure 앱 서비스는 인증 및 권한 부여에 대한 기본 제공 서비스를 제공합니다. 사용자 고유의 코드에서 인증을 자유롭게 처리할 수 있지만 작성하고 유지 관리해야 하는 코드의 양을 최소화하려는 경우 앱 서비스에서 턴키 솔루션을 제공합니다.

앱 서비스에서 지원되는 5개 인증 공급자(Azure Active Directory, Facebook, Google, Twitter 및 Microsoft 계정) 중 하나를 사용할 수 있습니다. 또한 앱 서비스에서 지원되는 모든 언어로 작성된 API를 보호할 수 있습니다. API에서 코드를 작성하지 않고 사용자 로그인 또는 액세스 토큰을 요구할 수 있습니다.

Azure 앱 서비스는 인증을 처리하고 권한 부여를 사용자 코드에서 처리하도록 둡니다. 앱 서비스를 구성하여 인증된 사용자만 API를 호출하도록 허용하거나, 모든 호출자를 허용할 수 있습니다. 두 경우 모두 앱 서비스는 HTTP 헤더에서 사용자 앱에 인증 정보를 전달합니다. 그러면 사용자의 코드에서 이 정보를 사용하여 권한 부여를 선택할 수 있습니다.

* .NET API에서 `Authorize` 특성을 사용할 수 있으며, 세분화된 권한 부여의 경우 클레임을 기반으로 코드를 쉽게 작성할 수 있습니다. 클레임 정보는 .NET 클래스에 자동으로 채워집니다.

* 다른 언어로 작성된 API의 경우 앱 서비스는 HTTP 요청의 인증 헤더에 JWT 토큰에 전달합니다. 또한 Azure에서는 가장 중요한 클레임에 보다 쉽게 액세스할 수 있도록 몇 가지 특별한 헤더(예: `x-ms-client-principal-id`)를 설정합니다.

Azure 앱 서비스의 인증 및 권한 부여 서비스에 대한 자세한 내용은 [앱 서비스 인증/권한 부여 확장](/blog/announcing-app-service-authentication-authorization/) 및 [앱 서비스 API 앱 - 변경된 내용](app-service-api-whats-changed.md)을 참조하세요.

## ContactsList.Angular.AAD 샘플 프로젝트

이 자습서에서는 이 시리즈의 첫 번째 자습서에서 다운로드한 샘플 프로젝트 및 [첫 번째 자습서](app-service-api-dotnet-get-started.md)에서 만든 Azure 리소스(API 앱 및 웹앱)를 사용합니다.

ContactsList.Angular.AAD 프로젝트는 Azure Active Directory와 함께 작동하는 코드가 포함된 AngularJS 클라이언트입니다. 이 코드는 [Azure-Samples/active-directory-angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) 리포지토리에서 찾을 수 있는 AAD 샘플을 기반으로 합니다.

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

여기서 `Get` 메서드에는 `getItems`라는 레이블이 지정되고, 컨트롤러(*app/scripts/contactsCtrl.js*)에서 `getItems`는 `$scope.populate`에 연결됩니다.

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

## Azure에서 인증 및 권한 부여 설정

1. [Azure 포털](https://portal.azure.com/)에서 첫 번째 자습서에서 만든 API 앱의 **API 앱** 블레이드로 이동하여 **설정**을 클릭합니다.

2. **기능** 섹션을 찾아서 **인증/권한 부여**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. **인증/권한 부여** 블레이드에서 **설정**을 클릭합니다.

4. **요청이 인증되지 않은 경우에 수행할 동작** 드롭다운 목록에서 **Azure Active Directory를 사용하여 로그인**을 선택합니다.

5. **인증 공급자** 아래에서 **Azure Active Directory**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. **Azure Active Directory 설정** 블레이드에서 **Express**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	AAD 테넌트에 AAD 응용 프로그램이 자동으로 만들어집니다. 나중에 새 AAD 응용 프로그램의 클라이언트 ID를 가져오기 위해 클래식 Azure 포털로 이동할 경우 선택할 것이므로 새 AAD 응용 프로그램의 이름을 적어 둡니다.

7. **확인**을 클릭합니다.

10. **인증/권한 부여** 블레이드에서 **저장**을 클릭합니다.

8. 이제 API 앱이 보호되는지 확인하기 위해 첫 번째 자습서에서 Swagger UI를 사용하기 위해 수행한 것처럼 API 앱의 URL+`/swagger`로 이동합니다.

	이번에는 로그온 페이지로 리디렉션됩니다.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 **Azure Active Directory**로 이동합니다.

12. 디렉터리 탭에서 AAD 테넌트를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. **응용 프로그램 > 회사 소유 응용 프로그램**을 클릭한 다음 확인 표시를 클릭합니다.

	페이지를 새로 고쳐야 새 응용 프로그램이 나타날 수도 있습니다.

15. 응용 프로그램 목록에서 API 앱에 대한 인증을 활성화할 때 만들어진 응용 프로그램의 이름을 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. **Configure**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. 페이지 아래쪽에서 **매니페스트 관리 > 매니페스트 다운로드**를 클릭하고 편집할 수 있는 위치에 파일을 저장합니다.

16. 다운로드한 매니페스트 파일에서 `oauth2AllowImplicitFlow` 속성을 검색합니다. 이 속성의 값을 `false`에서 `true`로 변경하고 파일을 저장합니다.

16. **매니페스트 관리 > 매니페스트 업로드**를 클릭하고 이전 단계에서 업데이트한 파일을 업로드합니다.

17. 자습서의 이후 단계에서 값을 복사하고 붙여 넣으며 페이지에서 값을 업데이트할 수 있도록 이 페이지를 열어 둡니다.

## HTTPS에 대한 Visual Studio 프로젝트 설정

1. **솔루션 탐색기**에서 ContactList.API 프로젝트를 클릭한 다음 **속성** 창에서**SSL 사용**을 **True**로 변경합니다.

2. SSL URL을 복사합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/enablessl.png)

3. ContactsList.API 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.

5. **웹** 탭을 클릭하고 SSL URL을 **프로젝트 Url** 필드에 붙여 넣은 다음 변경 내용을 저장합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/setprojecturl.png)

1. 동일한 절차에 따라 ContactsList.Angular.AAD 프로젝트에 대해 SSL을 사용하도록 설정합니다.

2. ContactsList.API 프로젝트 및 ContactsList.Angular.AAD 프로젝트를 시작 프로젝트로 설정하고 ContactsList.API 프로젝트를 먼저 시작하도록 설정합니다.

## ContactsList.Angular.AAD 프로젝트에서 끝점 URL 및 AAD 설정 업데이트

7. ContactsList.Angular.AAD 프로젝트에서 *app/scripts/app.js* 파일을 엽니다.

8. `endpoints` 변수를 설정하는 코드에서 ContactsList.API 프로젝트에 대한 올바른 SSL URL이 있는지 확인하고, 클래식 포털의 AAD 응용 프로그램에 대한 **구성** 탭에서 "yourclientid"의 두 인스턴스 모두를 AAD 응용 프로그램의 클라이언트 ID로 바꿉니다. 끝점 URL은 슬래시로 끝나야 합니다.

	코드는 다음 예제와 유사합니다.

		var endpoints = {
		    //"https://{your api app name}.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    "https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. 또한 *app.js*의 `adalProvider.init` 코드에서 "{your tenant url}" 및 "{your client id}"를 실제 값으로 바꿉니다.

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

10. *app/scripts/contactsSvc.js*에서 apiEndpoint가 ContactsList.API 프로젝트에 대한 올바른 SSL URL로 설정되어 있는지 확인합니다.

		//var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		var apiEndpoint = "https://localhost:44300";

## ContactsList.API 프로젝트에서 AAD 설정 업데이트

1. ContactsList.API 프로젝트에서 응용 프로그램 *Web.config* 파일을 엽니다.

2. 다음 예제와 같이 appSettings 요소에서 ida:Authority를 "https://login.windows.net/{your tenant url}" 값으로 설정하고 ida:ClientId를 AAD 응용 프로그램의 클라이언트 ID로 설정합니다.

		<appSettings>
		  <add key="ida:Authority" value="https://login.windows.net/contoso.onmicrosoft.com" />
		  <add key="ida:ClientId" value="1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3" />
		</appSettings>

## localhost에 대한 AAD 응용 프로그램 구성 

1. 클래식 포털의 AAD 응용 프로그램에 대한 **구성** 탭에서 **로그온 URL** 필드에 ContactsList.Angular.AAD 프로젝트 SSL URL을 붙여 넣고 후행 슬래시를 제거합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/signonurl.png)

3. **구성** 탭 아래쪽의 **회신 URL** 필드에 ContactsList.Angular.AAD 프로젝트 SSL URL을 붙여 넣습니다. 이때 기존 값을 바꾸고 후행 슬래시는 그대로 둡니다.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurl.png)

4. **Save**를 클릭합니다.

## 로컬로 API 및 클라이언트 프로젝트 실행

HTTPS를 로컬로 실행할 경우 localhost의 IIS Express SSL 인증서에 대한 경고 메시지가 나타납니다. 로컬에서 실행하려면 이러한 메시지를 클릭하면 됩니다. 원하는 경우 이 섹션을 건너뛰고 다음 섹션으로 바로 이동하여 Azure에서 응용 프로그램 및 API를 실행하도록 준비할 수 있습니다.

로그온하는 데 문제가 있으면 다른 브라우저를 사용해 보거나 AngularJS 프로젝트의 URL(예: `https://localhost:44301`)로 Incognito 또는 InPrivate 창을 열어 보세요.

5. Visual Studio에서 F5 키를 눌러 API 및 AngularJS 프로젝트를 로컬로 실행합니다.

	AngularJS 응용 프로그램의 홈 탭이 나타납니다.

10. **로그인** 탭을 클릭합니다.

	로그인하라는 메시지가 표시됩니다.

7. AAD 테넌트의 사용자에 대한 사용자 자격 증명으로 로그인합니다.

10. **연락처** 탭을 클릭합니다.

	**연락처** 페이지가 나타납니다.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspagelocal.png)

11. 브라우저 창을 닫습니다.

ContactsList.Angular.AAD 프로젝트 코드에서 API를 호출하고 연락처를 표시하려면 로그온해야 합니다. 그러나 인증되지 않은 호출자가 API를 호출하지 못하도록 할 수 있는 방법은 없습니다. ContactsList.API 프로젝트 SSL URL로 열린 브라우저 창에서 Swagger UI를 실행하여 이를 확인할 수 있습니다. API는 Azure 앱 서비스에서 실행될 때만 인증되지 않은 호출자로부터 보호됩니다.

다음 섹션에서는 Azure 앱 서비스에서 클라이언트 및 API를 실행하도록 프로젝트 및 AAD를 구성한 후 Azure에 프로젝트를 배포하고 Azure에서 이를 테스트합니다.

## Azure API 앱을 호출하도록 ContactsList.Angular.AAD 프로젝트 구성

1. ContactsList.Angular.AAD 프로젝트에서 *app/scripts/app.js* 파일을 엽니다.

2. localhost 끝점을 주석 처리하고, Azure 끝점의 주석을 제거하고, "{your api app name}"을 API 앱 이름으로 바꿉니다.

	이제 코드는 다음 예제와 유사합니다.

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

1. *app/scripts/contactsSvc.js* 파일에서 동일한 끝점 URL을 변경합니다.

	이제 코드는 다음 예제와 유사합니다.

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";


## Azure 웹앱에 대한 AAD 응용 프로그램 구성

1. 클래식 포털의 AAD 응용 프로그램에 대한 **구성** 탭에 있는 **로그온 URL** 필드에서 ContactsList.Angular.AAD 프로젝트 SSL URL을 삭제하고 후행 슬래시 없이 웹앱의 기준 URL로 바꿉니다. 이는 API 앱의 URL이 아니라 웹앱의 URL입니다.

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. **회신 URL** 필드에서 ContactsList.Angular.AAD 프로젝트 SSL URL을 웹앱의 기준 URL로 바꿉니다. 이때 후행 슬래시는 그대로 둡니다.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. **Save**를 클릭합니다.

## Azure에 ContactsList.API 프로젝트 배포

8. **솔루션 탐색기**에서 ContactsList.API 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

	이 프로젝트에 마지막으로 사용한 게시 프로필로 **웹 게시** 마법사가 열립니다. 이 프로필은 이전에 만든 API 앱에 배포하는 데 사용됩니다.

7. **게시**를 클릭합니다.

8. 자동으로 열린 브라우저 창을 닫습니다.

## Azure에 ContactsList.Angular.AAD 프로젝트 배포

8. **솔루션 탐색기**에서 ContactsList.Angular.API 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

9. **Microsoft Azure 앱 서비스**를 클릭합니다.

10. **앱 서비스** 대화 상자의 **구독** 드롭다운 목록에서 구독을 선택합니다.

11. 이 자습서에 대해 만든 리소스 그룹을 확장하고 두 번째 자습서에서 만든 웹앱을 선택합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. **확인**을 클릭합니다.

12. **웹 게시** 마법사에서 **연결** 탭을 클릭하고 **대상 URL** 상자에서 `http://`를 `https://`로 변경합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

12. **웹 게시** 마법사에서 **설정** 탭을 클릭하고 **파일 게시 옵션**을 확장한 다음 **대상에서 추가 파일 제거** 확인란을 선택합니다.

7. **게시**를 클릭합니다.

	Visual Studio에서 프로젝트를 배포하고 앱의 홈 페이지로 브라우저를 엽니다.

## Azure에서 AngularJS 웹앱 테스트

8. **연락처** 탭을 클릭합니다.

	로그인하라는 메시지가 표시됩니다.

9. AAD 테넌트의 사용자 자격 증명으로 로그인합니다.

10. **연락처** 페이지가 나타납니다.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

11. API 앱이 보호되는지 확인하기 위해 InPrivate 또는 Incognito 브라우저 창에서 해당 Swagger UI URL로 이동합니다.

	로그온 페이지로 리디렉션됩니다.

	이제 프런트 엔드에서 인증된 사용자 대신 API를 호출할 수 있지만 인증되지 않은 사용자는 API를 호출할 수 없습니다.

## 다음 단계

이 자습서에서는 인증된 사용자만 호출할 수 있도록 앱 서비스 인증/권한 부여를 사용하여 API 앱에 대한 액세스를 제한했습니다. 시리즈의 다음 자습서에서는 [서비스 간 시나리오에 대해 API 앱에 대한 액세스를 제한](app-service-api-dotnet-service-principal-auth.md)하는 방법을 알아봅니다.

<!---HONumber=AcomDC_1203_2015-->
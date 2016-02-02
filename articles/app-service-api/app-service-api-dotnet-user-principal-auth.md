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
	ms.date="01/26/2016"
	ms.author="tdykstra"/>

# Azure 앱 서비스의 API 앱에 대한 사용자 인증

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 개요

이 문서에서는 다음에 대해 알아봅니다.

* 인증된 사용자만 호출할 수 있도록 앱 서비스 API 앱을 보호하는 방법
* 인증 공급자 구성 방법 및 Azure AD(Azure Active Directory)에 대한 세부 정보
* [JavaScript용 ADAL(Azure AD 인증 라이브러리)](https://github.com/AzureAD/azure-activedirectory-library-for-js)을 사용하여 보호되는 API 앱 사용 방법

이 문서에는 두 섹션이 포함되어 있습니다.

* [Azure 앱 서비스에서 사용자 인증을 구성하는 방법](#authconfig) 섹션은 모든 API 앱에 사용자 인증을 구성하는 방법을 일반적으로 설명하며 .NET, Node.js 및 Java 등, 앱 서비스에서 지원하는 모든 프레임워크에 동일하게 적용됩니다.

* [문서의 나머지 부분](#tutorialstart)에서는 사용자 인증에 Azure Active Directory를 사용할 수 있게 앱 서비스를 실행하는 .NET 샘플 응용 프로그램을 구성하는 단계를 안내합니다.

## <a id="authconfig"></a>Azure 앱 서비스의 사용자 인증 구성 방법

이 섹션에서는 모든 API 앱에 적용되는 일반적인 지침을 제공합니다. 수행 목록 .NET 샘플 응용 프로그램에 특정한 단계는 [.NET 시작 자습서 계속](#tutorialstart)으로 이동합니다.

1. [Azure 포털](https://portal.azure.com/)에서 보호할 **API 앱** 블레이드로 이동한 다음 **설정**을 클릭합니다.

2. **설정** 블레이드의 **기능** 섹션에서 **인증/권한 부여**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. **인증/권한 부여** 블레이드에서 **설정**을 클릭합니다.

4. **요청이 인증되지 않은 경우에 수행할 동작** 드롭다운 목록에서 옵션 중 하나를 선택합니다.

	* 인증된 호출만 API 앱에 도달하게 하려면 **로그인 방법...** 옵션 중 하나를 선택합니다. 이 옵션을 사용하면 안에서 실행되는 코드를 작성하지 않고도 API 앱을 보호할 수 있습니다.

	* 모든 호출이 API 앱에 도달하게 하려면 **요청 허용(작업 없음)**을 선택합니다. 이 옵션을 사용하여 인증되지 않은 호출자를 선택된 인증 공급자에게 전달할 수 있습니다. 이 옵션에서는 권한 부여를 처리하는 코드를 작성해야 합니다.

	자세한 내용은 [Azure 앱 서비스의 API 앱에 대한 인증 및 권한 부여](app-service-api-authentication.md#multiple-protection-options)를 참조하세요.

5. 하나 이상의 **인증 공급자**를 선택합니다.

	아래 이미지는 Azure AD에서 모든 호출자를 인증하도록 하는 선택 항목을 보여줍니다.
 
	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

	인증 공급자를 선택하면 해당 공급자에 대해 구성 블레이드가 포털에 표시됩니다.

	인증 공급자 구성 블레이드를 구성하는 방법을 설명하는 자세한 지침은 [Azure Active Directory 로그인을 사용하도록 앱 서비스 응용 프로그램을 구성하는 방법](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)을 참조하세요. 이 링크는 Azure AD에 대한 문서로 이동하지만 문서 자체에 다른 인증 공급자에 대한 글로 연결되는 탭이 포함되어 있습니다.

7. 인증 공급자 구성 블레이드를 마치면 **확인**을 클릭합니다.

7. **인증/권한 부여** 블레이드에서 **저장**을 클릭합니다.

이 작업을 마치면 앱 서비스는 API 호출이 API 앱에 도달하기 전에 모든 API 호출을 인증합니다. 인증 서비스는 .NET, Node.js, Java 등, API 서비스가 지원하는 모든 언어에 동일하게 작동합니다.

인증된 API를 호출하려면 호출자가 인증 공급자의 OAuth 2.0 전달자 토큰을 HTTP 요청의 Authorization 헤더에 포함해야 합니다. 이 토큰은 인증 공급자의 SDK를 사용하여 받을 수 있습니다.

## <a id="tutorialstart"></a> .NET 시작 자습서 계속

API 앱에 대한 Node.js 또는 Java 시작 시리즈를 진행 중인 경우 다음 글인 [API 앱용 서비스 주체 인증](app-service-api-dotnet-service-principal-auth.md)으로 건너뜁니다.

API 앱에 대한 .NET 시작 시리즈를 따르고 있고 이미 [첫 번째](app-service-api-dotnet-get-started.md) 및 [두 번째](app-service-api-cors-consume-javascript.md) 자습서에서 지시한 대로 샘플 응용 프로그램을 배포한 경우 [인증 구성](#azureauth) 섹션으로 건너뜁니다.

첫 번째 및 두 번째 자습서를 설치하지 않았고 이 문서를 따르려는 경우 시리즈에 대한 모든 [필수 구성 요소](app-service-api-dotnet-get-started.md#prerequisites)가 준비되었는지부터 확인합니다. 이후 다음 단계를 통해 샘플 응용 프로그램을 다운로드하여 배포합니다. 이 단계는 처음 두 자습서에서 수행한 항목과 중복되지만 여기서는 요약한 지침을 제공합니다.

1. 샘플 응용 프로그램을 다운로드합니다.

	a. [Azure-Samples/app-service-api-dotnet-todo-list](https://github.com/Azure-Samples/app-service-api-dotnet-to-do-list) 리포지토리에서 다운로드합니다.

	a. ToDoList 솔루션을 Visual Studio 2015에서 열고 솔루션을 빌드하여 NuGet 패키지를 복원합니다.

2. 새 API 앱에 ToDoListDataAPI 프로젝트를 배포합니다.

	a. ToDoListDataAPI 프로젝트에서 *App\_Start/SwaggerConfig.cs* 파일을 열고 **EnableSwaggerUi** 코드의 주석 처리를 해제합니다.

	b. **솔루션 탐색기**에서 ToDoListDataAPI 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

	c. **웹 게시** 마법사의 **프로필** 단계에서 **Microsoft Azure 앱 서비스**를 클릭합니다.

	d. **앱 서비스** 대화 상자에서 사용할 Azure **구독**을 선택하고 **새로 만들기**를 클릭합니다.

	e. **앱 서비스 만들기** 대화 상자의 **호스팅** 탭에서 **형식 변경**을 클릭한 다음 **API 앱**을 클릭합니다.

	f. ToDoListDataAPI와 숫자 등, *azurewebsites.net* 도메인에서 고유한 **API 앱 이름**을 입력합니다(예: ToDoListDataAPI1230).

	g. **리소스 그룹** 드롭다운 목록에서 TodoListGroup 같은 이름을 입력하여 새 리소스 그룹을 만듭니다.

	h. **앱 서비스 계획** 드롭다운에서 **새로 만들기**를 클릭하고 **앱 서비스 계획 구성** 대화 상자에 필요한 정보를 입력합니다.

	i. **만들기**를 클릭합니다.

	j. **게시**를 클릭합니다.

3. 새 API 앱에 ToDoListAPI 프로젝트를 배포합니다.

	a. ToDoListAPI 프로젝트에서 *Controllers\\ToDoListController.cs*를 열고 `http://localhost:45914`를 `https://{your ToDoListDataAPI app name}.azurewebsites.net`으로 변경합니다.

	b. ToDoListDataAPI 프로젝트에 대해 수행한 ToDoListAPI 프로젝트를 배포하기 위해 동일한 절차를 따릅니다. 형식을 **API 앱**으로 반드시 변경합니다.

4. 새 웹앱에 ToDoListAngular 프로젝트를 배포합니다.

	a. ToDoListAngular 프로젝트에서 *app/scripts/todoListSvc.js*를 엽니다.

	b. `apiEndpoint`을 localhost URL로 설정하는 줄을 주석 처리하고 `apiEndPoint`을 azurewebsites.net URL로 설정하는 줄의 주석을 제거한 후 자리 표시자를 ToDoListAPI에 대해 만든 API 앱의 실제 이름으로 바꿉니다.

	c. ToDoListDataAPI 프로젝트에 대해 수행한 ToDoListAPI 프로젝트를 배포하기 위해 동일한 절차를 따릅니다. **단, 웹 앱의 형식을 API 앱으로 변경하지 않습니다.**

5. Azure에서 API 앱에 대한 CORS를 구성합니다.

	a. [Azure 포털](https://portal.azure.com/)로 이동한 다음ToDoListAPI 프로젝트에 대해 만든 API 앱으로 이동합니다.

	b. **API 앱** 블레이드에서 **설정**을 클릭합니다.

	c. **API** 섹션을 찾은 다음 **CORS**를 클릭합니다.

	d. 텍스트 상자에 호출을 허용할 출처의 URL을 입력합니다. 이 자습서에서는 ToDoListAngular 프로젝트에서 만든 웹앱의 URL입니다. 예를 들어 "https://todolistangular.azurewebsites.net"을 입력합니다.

	e. **Save**를 클릭합니다.

6. 브라우저를 열어 웹앱의 HTTPS URL로 이동한 다음 할 일 항목을 보고, 추가하고, 편집하고, 삭제할 수 있는지 확인합니다.

## <a id="azureauth"></a> Azure에서 인증 설정

이 시점에는 사용자 인증을 요구하지 않고 Azure 앱 서비스를 실행하는 응용 프로그램이 있습니다. 이 섹션에서는 다음 작업을 수행하여 인증을 추가합니다.

* 중간 계층 API 앱을 호출하기 위해 Azure AD(Active Directory) 인증을 요구하는 앱 서비스를 구성합니다.
* Azure AD 응용 프로그램을 만듭니다.
* 로그온 후 AngularJS 프런트 엔드에 로그온한 후 전달자 토큰을 보내도록 Azure AD 응용 프로그램을 구성합니다. 

### 앱 서비스에서 인증 구성

1. [Azure 포털](https://portal.azure.com/)에서 ToDoListAPI 프로젝트에 대해 만든 **API 앱** 블레이드로 이동합니다.

2. **설정**을 클릭합니다.

2. **설정** 블레이드의 **기능** 섹션에서 **인증/권한 부여**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. **인증/권한 부여** 블레이드에서 **설정**을 클릭합니다.

4. **요청이 인증되지 않은 경우에 수행할 동작** 드롭다운 목록에서 **Azure Active Directory를 사용하여 로그인**을 선택합니다.

	이 옵션을 사용하면 인증되지 않은 요청이 API 앱에 도달할 수 없습니다.

5. **인증 공급자** 아래에서 **Azure Active Directory**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. **Azure Active Directory 설정** 블레이드에서 **Express**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	**Express** 옵션을 사용하면 Azure AD [테넌트](https://msdn.microsoft.com/ko-KR/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)에서 앱 서비스가 자동으로 Azure AD 응용 프로그램을 만듭니다.

	자동으로 모든 Azure 계정에 하나씩 있기 때문에 테넌트를 만들 필요가 없습니다.

7. **관리 모드**에서 **새 AD 앱 만들기**를 클릭합니다.

	포털이 **앱 만들기** 입력 상자에 기본값을 채웁니다.
	
	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

8. **앱 만들기** 입력 상자에 있는 값을 기록해 둡니다. 나중에 Azure 클래식 포털에서 이 AAD 응용 프로그램을 찾아 보겠습니다.

	Azure AD 테넌트에 Azure AD 응용 프로그램이 자동으로 만들어집니다. 기본적으로 Azure AD 응용 프로그램의 이름은 API 앱과 동일합니다. 원하는 경우 다른 이름을 입력합니다.
 
7. **확인**을 클릭합니다.

7. **인증/권한 부여** 블레이드에서 **저장**을 클릭합니다.

이제 Azure AD 테넌트에 있는 사용자만 API 앱을 호출할 수 있습니다.

### 선택 사항: API 앱 테스트

1. 브라우저에서 API 앱의 URL로 이동: Azure 포털에서 **API 앱** 블레이드에서 **URL** 아래 링크를 클릭합니다.  

	인증되지 않은 요청은 API 앱에 도달할 수 없으므로 로그인 화면으로 리디렉션됩니다.

	브라우저가 "만들기 성공" 페이지로 이동하면 브라우저에서 이미 로그온했을 수 있습니다. 이 경우 InPrivate 또는 Incognito 창을 열고 API 앱의 URL로 이동합니다.

2. Azure AD의 사용자에 대한 자격 증명으로 로그온합니다.

	로그온하면 "만들기 성공" 페이지가 브라우저에 표시됩니다.

9. 브라우저를 닫습니다.

### Azure AD 응용 프로그램 구성

Azure AD 인증을 구성하면 앱 서비스가 사용자에 대한 Azure AD 응용 프로그램을 만듭니다. 기본적으로 새 Azure AD 응용 프로그램은 API 앱의 URL에 전달자 토큰을 제공하도록 구성되었습니다. 이 섹션에서는 전달자 토큰을 중간 계층 API 앱에 직접 전달하는 대신 AngularJS 프런트 엔드에 제공하도록 Azure AD 응용 프로그램을 구성합니다. AngularJS 프런트 엔드는 API 앱을 호출할 때 API 앱에 토큰을 보냅니다.

11. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 **Azure Active Directory**로 이동합니다.

	액세스해야 하는 특정 Azure Active Directory 설정을 아직 현재 Azure 포털에서 사용할 수 없으므로 클래식 포털을 사용해야 합니다.

12. **디렉터리** 탭에서 AAD 테넌트를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. **응용 프로그램 > 회사 소유 응용 프로그램**을 클릭한 다음 확인 표시를 클릭합니다.

	페이지를 새로 고쳐야 새 응용 프로그램이 나타날 수도 있습니다.

15. 응용 프로그램 목록에서 API 앱에 대한 인증을 활성화할 때 만들어진 응용 프로그램의 이름을 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. **Configure**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. **로그온 URL**을 AngularJS 웹앱의 URL로 설정합니다. 마지막 슬래시는 제외합니다.

	예: https://todolistangular.azurewebsites.net

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. **회신 URL**을 웹앱의 URL로 설정합니다. 마지막 슬래시는 제외합니다.

	예: https://todolistsangular.azurewebsites.net

16. **Save**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. 페이지의 아래쪽에서 **매네페스트 관리 > 매니페스트 다운로드**를 클릭합니다.

	![](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. 편집할 수 있는 위치에 파일을 다운로드합니다.

16. 다운로드한 매니페스트 파일에서 `oauth2AllowImplicitFlow` 속성을 검색합니다. 이 속성의 값을 `false`에서 `true`로 변경하고 파일을 저장합니다.

	이 설정은 JavaScript 단일 페이지 응용 프로그램에서 액세스를 위해 필요합니다. Oauth 2.0 전달자 토큰이 URL 조각에 반환되게 할 수 있습니다.

16. **매니페스트 관리 > 매니페스트 업로드**를 클릭하고 이전 단계에서 업데이트한 파일을 업로드합니다.

17. **클라이언트 ID** 값을 복사하여 나중에 가져올 수 있는 위치에 복사합니다.

## 인증을 사용하도록 ToDoListAngular 프로젝트 구성

이 섹션에서는 JS에 ADAL(Active Directory 인증 라이브러리)을 사용하여 Azure AD로부터 로그온한 사용자의 전달자 토큰을 획득하도록 AngularJS 프런트 엔드를 변경합니다. 이 코드는 다음 다이어그램에서처럼 중간 계층으로 보낸 HTTP 요청에 토큰을 포함합니다.

![](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

ToDoListAngular 프로젝트의 파일을 다음과 같이 변경합니다.

1. *index.html* 파일을 엽니다.

2. ADAL(Active Directory 인증 라이브러리)를 참조하는 줄의 주석 처리를 해제합니다.

		<script src="app/scripts/adal.js"></script>
		<script src="app/scripts/adal-angular.js"></script>

1. *app/scripts/app.js* 파일을 엽니다.

2. "without authentication"으로 표시된 코드 블록을 주석 처리하고 "with authentication"으로 표시된 코드 블록의 주석 처리를 해제합니다.

	이 변경에서는 ADAL JS 인증 공급자를 참조하며 해당 항목에 구성 값을 공급합니다. 다음 단계에서는 API 앱 및 Azure AD 응용 프로그램에 대 한 구성 값을 설정합니다.

8. `endpoints` 변수를 설정하는 코드에서 API URL을, ToDoListAPI에 대해 만든 API 앱의 URL로 변경하고 Azure AD 응용 프로그램 ID를 Azure 클래식 포털에서 복사한 클라이언트 ID로 변경합니다.

	이제 코드는 다음 예제와 유사합니다.

		var endpoints = {
		    "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. `adalProvider.init` 호출에서 `tenant`를 해당 테넌트 이름으로 설정하고 `clientId`는 이전 단계에서 사용한 것과 같은 값으로 설정합니다.

	이제 코드는 다음 예제와 유사합니다.

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

	이러한 `app.js` 변경은 호출하는 코드와 호출되는 API가 동일한 Azure AD 응용 프로그램에 있도록 지정합니다.

1. *app/scripts/homeCtrl.js* 파일을 엽니다.

2. "without authentication"으로 표시된 코드 블록을 주석 처리하고 "with authentication"으로 표시된 코드 블록의 주석 처리를 해제합니다.

1. *app/scripts/indexCtrl.js* 파일을 엽니다.

2. "without authentication"으로 표시된 코드 블록을 주석 처리하고 "with authentication"으로 표시된 코드 블록의 주석 처리를 해제합니다.

### Azure에 ToDoListAngular 프로젝트 배포

8. **솔루션 탐색기**에서 ToDoListAngular 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

9. **게시**를 클릭합니다.

	Visual Studio가 프로젝트를 배포하고 웹앱의 기본 URL로 브라우저를 엽니다.

	응용 프로그램을 테스트하려면 여전히 중간 계층 API 앱을 변경해야 합니다.

10. 브라우저를 닫습니다.

## 인증을 사용하도록 ToDoListAPI프로젝트 구성

현재 ToDoListAPI 프로젝트는 "*"를 `owner` 값으로 ToDoListDataAPI에 보냅니다. 이 섹션에서는 로그온한 사용자의 ID를 보내도록 코드를 변경합니다.

ToDoListAPI 프로젝트를 다음과 같이 변경합니다.

1. *Controllers/ToDoListController.cs* 파일을 열고 `owner`를 Azure AD `NameIdentifier` 클레임 값으로 설정하는 각 작업 메서드의 줄에 대한 주석 처리를 해제합니다. 예:

		owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

### Azure에 ToDoListAPI 프로젝트배포

8. **솔루션 탐색기**에서 ToDoListAPI 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

9. **게시**를 클릭합니다.

	Visual Studio가 프로젝트를 배포하고 API 앱의 기본 URL로 브라우저를 엽니다.

10. 브라우저를 닫습니다.

### 응용 프로그램 테스트

9. **HTTP가 아닌 HTTPS를 사용 하여** 웹앱의 URL로 이동합니다.

8. **To Do List** 탭을 클릭합니다.

	로그인하라는 메시지가 표시됩니다.

9. AAD 테넌트의 사용자 자격 증명으로 로그인합니다.

10. **To Do List** 페이지가 나타납니다.

	![](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

	지금까지는 모두 소유자 "*"에 대한 것이기 때문에 할 일 항목이 표시되지 않습니다. 이제 중간 계층에서 로그온한 사용자에 대한 항목을 요청하고, 아직은 아무 것도 만들지 않았습니다.

11. 응용 프로그램이 작동하고 있는지 확인하려면 새 할 일 항목을 추가합니다.

12. 다른 브라우저 창에서 ToDoListDataAPI API 앱에 대한 Swagger UI URL로 이동하고 **ToDoList > 가져오기**를 클릭합니다. `owner` 매개 변수에 별표를 입력하고 **사용해 보기**를 클릭합니다.

	응답에 보면 새 할 일 항목의 소Owner 속성에 실제 Azure AD 사용자 ID가 있습니다.

	![](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## 처음부터 프로젝트 빌드

**Azure API 앱** 프로젝트 템플릿을 사용하고 기본 Values 컨트롤러를 ToDoList 컨트롤러로 대체하여 두 개의 웹 API 프로젝트가 만들어졌습니다.

웹 API 2 백엔드를 통한 AngularJS 단일 페이지 응용 프로그램을 만드는 방법에 대한 내용은 [Hands On Lab: ASP.NET 웹 API 및 Angular.js를 통해 단일 페이지 응용 프로그램 빌드](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs)를 참조하세요. Azure AD 인증 코드를 추가하는 방법에 대한 내용은 다음 리소스를 참조하세요.

* [Azure AD를 사용하여 AngularJS 단일 페이지 앱 보안 유지](../active-directory/active-directory-devquickstarts-angular.md)
* [ADAL JS v1 소개](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## 문제 해결

인증 없이 성공적으로 실행했던 응용 프로그램이 인증 구현 후 제대로 작동하지 않을 경우 대부분 문제의 원인은 구성 설정이 부정확하거나 일관되지 않아서입니다. 먼저 Azure 앱 서비스와 Azure Active Directory의 모든 설정을 꼼꼼하게 확인합니다. 몇 가지 특정 제안 사항은 다음과 같습니다.

* Azure AD **구성** 탭에서 **회신 URL**을 꼼꼼하게 확인합니다.
* Azure AD에서 매니페스트를 다운로드하고 `oauth2AllowImplicitFlow`가 `true`로 제대로 변경되었는지 확인합니다. 
* AngularJS 소스 코드에서 중간 계층 API 앱 URl과 Azure AD 클라이언트 ID를 꼼꼼히 확인합니다.
* 프로젝트에서 코드를 구성한 후 프로젝트를 다른 항목에 속하지 않게 재배포했는지 확인합니다.
* 브라우저에서 HTTP URL이 아닌 HTTPS URL을 사용하는지 확인합니다.
* 중간 계층 API 앱에서 CORS가 계속 활성화되어 프런트 엔드 HTTPS URL에서 중간 계층에 대한 호출을 허용하는지 확인합니다. 문제가 CORS와 관련이 있는지 잘 모르겠으면 "*"를 다음 원래의 URL로 사용해 봅니다.
* [customErrors 모드를 Off](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview)로 설정하여 오류 메시지에서 가능한 많은 정보를 확인할 수 있도록 합니다.
* 브라우저의 개발자 도구 콘솔 탭에서 제공하는 정보에 오류가 있는 경우가 있습니다. HTTP 요청은 네트워크 탭에서 조사할 수 있습니다.

## 다음 단계

이 자습서에서는 API 앱에 앱 서비스 인증을 사용하는 방법과, ADAL JS 라이브러리를 사용하여 API 앱을 호출하는 방법을 배웠습니다. 다음 자습서에서는 [서비스 간 시나리오에 대해 API 앱에 대한 액세스를 보호](app-service-api-dotnet-service-principal-auth.md)하는 방법을 알아봅니다.

<!---HONumber=AcomDC_0128_2016-->
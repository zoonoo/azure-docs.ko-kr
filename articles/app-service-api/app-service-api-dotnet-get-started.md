<properties
	pageTitle="Azure 앱 서비스에서 API 앱 및 ASP.NET 시작 | Microsoft Azure"
	description="Visual Studio 2015를 사용하여 Azure 앱 서비스에서 ASP.NET API 앱을 만들고, 배포하고, 사용하는 방법을 알아봅니다."
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
	ms.date="01/05/2016"
	ms.author="tdykstra"/>

# Azure 앱 서비스에서 API 앱 및 ASP.NET 시작

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 개요

이 자습서에서는 [앱 서비스 API 앱](app-service-api-apps-why-best-platform.md)을 만들고, ASP.NET Web API를 API 앱에 배포하고 ASP.NET MVC 클라이언트에서 API 앱을 사용합니다. 이 자습서에서는 ASP.NET을 잘 알고 있지만 Microsoft Azure에 대한 경험은 없는 것으로 가정합니다. 이 자습서를 완료하면 클라우드에서 Web API 및 클라이언트 응용 프로그램을 실행할 수 있습니다.

샘플 응용 프로그램은 간단한 연락처 목록입니다. 다음 그림에서는 MVC 앱이 API에서 받은 데이터를 표시하는 방법을 보여 줍니다.

![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

## 학습할 내용

Azure 앱 서비스의 세 가지 기능은 API를 개발 및 호스팅하는 데 특히 유용합니다.

* API 메타데이터에 대한 통합 지원
* CORS 지원
* 인증 및 권한 부여 지원
 
이러한 기능을 소개하는 시리즈의 첫 번째 자습서입니다. 이 자습서는 API 메타데이터를 중점적으로 다루며 두 번째 자습서는 CORS를, 나머지 자습서는 인증 및 권한 부여를 중점적으로 다룹니다.

이러한 자습서에서 학습할 내용은 다음과 같습니다.

* Azure SDK for .NET을 설치하여 Azure 개발용 컴퓨터를 준비하는 방법
* Visual Studio 2015의 기본 제공 도구를 사용하여 Azure 앱 서비스에서 API 앱 및 웹앱으로 작업하는 방법
* Swashbuckle NuGet 패키지를 사용하여 Swagger API 정의 JSON을 동적으로 생성하는 방식으로 API 검색을 자동화하는 방법
* 자동으로 생성된 클라이언트 코드를 사용하여 .NET 클라이언트에서 API 앱을 사용하는 방법
* Azure 포털을 사용하여 API 앱 메타데이터에 대한 끝점 구성하는 방법
* 클라이언트가 API와 다른 도메인에 있는 경우 CORS를 사용하여 JavaScript 클라이언트에서 API 앱을 호출하는 방법
* Azure AD(Azure Active Directory)를 사용하여 인증되지 않은 액세스로부터 API를 보호하는 방법
* Azure AD에 로그인한 사용자에 대해 보호된 API를 사용하는 방법
* Azure AD 서비스 주체를 사용하여 보호된 API를 사용하는 방법

## 필수 조건

[AZURE.INCLUDE [필수 조건](../../includes/app-service-api-dotnet-get-started-prereqs.md)]

[AZURE.INCLUDE [set-up-dev-environment](../../includes/install-sdk-2015-2013.md)]

이 자습서에는 Azure SDK for .NET 버전 2.8.1 이상이 필요합니다.

## 샘플 응용 프로그램 개요

이 자습서에서 사용할 API 앱 및 웹앱을 배포하는 코드는 [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) GitHub 리포지토리에 있습니다. ContactsList Visual Studio 솔루션에는 이 자습서에서 사용되는 다음 프로젝트가 포함되어 있습니다.

* **ContactsList.API** - 이름 및 전자 메일 주소 목록을 반환하는 ASP.NET Web API 프로젝트입니다. 처음에 Get 메서드를 호출하면 하드 코드된 연락처 3개가 반환되고, 이후에 Put, Post 및 Delete 메서드를 호출하면 변경 내용이 로컬 JSON 파일에 저장됩니다.
* **ContactsList.MVC** - ContactsList API에 대한 ASP.NET MVC 클라이언트입니다.

이후의 자습서는 동일한 솔루션의 다른 프로젝트를 사용합니다.

* **ContactsList.Angular** - CORS 지원 설명을 위한 AngularJS 클라이언트입니다.
* **ContactsList.Angular.AAD** - 사용자 인증 설명을 위한 AngularJS 클라이언트입니다.
* **CompanyContacts.API** - 서비스 계정 인증 설명을 위한 ASP.NET Web API 프로젝트입니다.  

## 샘플 응용 프로그램 다운로드 

1. [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) 리포지토리를 다운로드합니다.

	[.zip 파일을 다운로드](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list/archive/master.zip)하거나 로컬 컴퓨터에서 리포지토리를 복제할 수 있습니다.

2. Visual Studio 2015 또는 2013에서 ContactsList 솔루션을 엽니다.

2. NuGet 패키지를 복원할 솔루션을 빌드합니다.

## Swagger 메타데이터 및 UI 사용

Azure 앱 서비스에서는 기본적으로 [Swagger](http://swagger.io/) 2.0 API 메타데이터를 지원합니다. 각 API 앱은 API에 대한 메타데이터를 Swagger JSON 형식으로 반환하는 URL 끝점을 정의할 수 있습니다. 해당 끝점에서 반환한 메타데이터는 클라이언트 코드를 생성하는 데 사용할 수 있습니다.

ASP.NET Web API 프로젝트에 대한 Swagger 2.0 메타데이터를 제공하려면 [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet 패키지를 설치합니다. Swashbuckle은 리플렉션을 사용하여 메타데이터를 동적으로 생성합니다. Swashbuckle NuGet 패키지는 다운로드한 ContactsList.API 프로젝트에 이미 설치되어 있으며 **Azure API 앱** 프로젝트 템플릿을 사용하여 새 프로젝트를 만들 때 미리 설치됩니다. (Visual Studio에서: **파일 > 새로 만들기 > 프로젝트 > ASP.NET 웹 응용 프로그램 > Azure API 앱**.)

자습서의 이 섹션에서는 생성된 Swagger 2.0 메타데이터를 살펴본 다음 Swagger 메타데이터를 기반으로 하는 테스트 UI를 사용합니다.

2. ContactsList.API 프로젝트를 시작 프로젝트로 설정합니다. (CompanyContacts.API 프로젝트 아님, 해당 프로젝트는 이후 자습서 중 하나에 사용됨.) 
 
4. F5 키를 눌러 디버그 모드에서 프로젝트를 실행합니다.

	브라우저가 열리고 403 사용할 수 없음 페이지가 표시됩니다.

	![](./media/app-service-api-dotnet-get-started/403.png)

12. 브라우저의 주소 표시줄에서 줄 끝에 `swagger/docs/v1`을 추가한 다음 Enter 키를 누릅니다. URL은 `http://localhost:51864/swagger/docs/v1`입니다.

	이는 Swashbuckle에서 API에 대한 Swagger 2.0 JSON 메타데이터를 반환하는 데 사용하는 기본 URL입니다. Internet Explorer를 사용하는 경우 브라우저에서 v1.json 파일을 다운로드하라는 메시지가 표시됩니다.

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Chrome 또는 에지를 사용하는 경우 브라우저 창에 JSON이 표시됩니다.

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	다음 샘플에서는 Get 메서드에 대한 정의와 함께 API에 대한 Swagger 메타데이터의 첫 번째 섹션을 보여 줍니다. 이 메타데이터는 다음 단계에서 사용할 Swagger UI를 구동하며, 자습서의 이후 섹션에서 클라이언트 코드를 자동으로 생성하는 데 사용됩니다.

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ContactsList.API"
		  },
		  "host": "localhost:51864",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/Contacts": {
		      "get": {
		        "tags": [ "Contacts" ],
		        "operationId": "Contacts_Get",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/Contact" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. 브라우저를 닫습니다.

3. **솔루션 탐색기**의 ContactsList.API 프로젝트에서 *App\_Start\\SwaggerConfig.cs* 파일을 열고 아래의 다음 코드로 스크롤한 후 이 코드의 주석 처리를 제거합니다.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	SwaggerConfig.cs 파일은 프로젝트에서 Swashbuckle 패키지를 설치할 때 생성됩니다. 이 파일은 Swashbuckle을 구성하는 다양한 방법을 제공합니다.

	주석을 제거한 코드는 다음 단계에서 사용할 Swagger UI를 사용하도록 설정합니다. API 앱 프로젝트 템플릿을 사용하여 Web API 프로젝트를 만들 때는 하나의 보안 조치로 이 코드가 기본적으로 주석 처리됩니다.

5. 프로젝트를 다시 실행합니다.

3. 브라우저의 주소 표시줄에서 줄 끝에 `swagger`를 추가한 다음 Enter 키를 누릅니다. URL은 `http://localhost:51864/swagger`입니다.

4. Swagger UI 페이지가 나타나면 **Contacts**를 클릭하여 사용 가능한 메서드를 확인합니다.

	![](./media/app-service-api-dotnet-get-started/contactsmethods.png)

5. **Get > Try it out**을 클릭합니다.

	Swagger UI에서 ContactsList Get 메서드를 호출하고 JSON 결과를 표시합니다.

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. **Post**를 클릭한 다음 **Model Schema** 아래의 상자를 클릭합니다.

	모델 스키마를 클릭하면 입력 상자가 미리 채워집니다. 여기서 Post 메서드의 매개 변수 값을 지정할 수 있습니다.

	![](./media/app-service-api-dotnet-get-started/post.png)

7. `contact` 매개 변수 입력 상자에서 다음 예제와 같이 표시되도록 JSON을 변경하거나, 사용자 고유의 이름 및 전자 메일 주소로 대체합니다.

		{
		  "CreatedBy": "",
		  "EmailAddress": "carson@contoso.com",
		  "Id": 4,
		  "Name": "Alexander Carson"
		} 

10. **Try it out**을 클릭합니다.

	ContactsList API에서 HTTP 200 및 추가된 항목을 확인하는 응답 본문을 반환합니다.

11. **Get > Try it out**을 클릭합니다.

	이제 Get 메서드 응답에 새 연락처가 포함됩니다.

12. Put, Delete 및 Get by ID 메서드도 사용해 본 다음 브라우저를 닫습니다.

Swashbuckle은 모든 ASP.NET Web API 프로젝트에서 작동합니다. Swagger 메타데이터 생성을 기존 프로젝트에 추가하려면 Swashbuckle 패키지를 설치하기만 하면 됩니다. 새 프로젝트를 만들려면 다음 그림처럼 ASP.NET **Azure API 앱** 프로젝트 템플릿을 사용합니다.

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

이 템플릿은 Swashbuckle이 설치된 Web API 프로젝트를 만듭니다.

**참고:** 기본적으로 Swashbuckle은 컨트롤러 메서드에 대한 중복 Swagger 작업 ID를 생성할 수 있습니다. 컨트롤러에 오버로드된 HTTP 메서드가 있는 경우 이러한 동작이 발생합니다(예: `Get()` 및 `Get(id)`). 오버로드를 처리하는 방법에 대한 자세한 내용은 [Swashbuckle 생성 API 정의 사용자 지정](app-service-api-dotnet-swashbuckle-customize.md)을 참조하세요. Azure API 앱 템플릿을 사용하여 Visual Studio에서 Web API 프로젝트를 만들면 고유한 작업 ID를 생성하는 코드가 *SwaggerConfig.cs* 파일에 자동으로 추가됩니다.

## Azure에서 API 앱을 만들고 이 앱에 ContactsList.API 프로젝트 배포

이 섹션에서는 Visual Studio **웹 게시** 마법사에 통합된 Azure 도구를 사용하여 Azure에서 새 API 앱을 만듭니다. 그런 다음 새 API 앱에 ContactsList.API 프로젝트를 배포하고, 이번에는 클라우드에서 실행되는 동안 Swagger UI를 다시 실행하여 API를 호출합니다.

1. **솔루션 탐색기**에서 ContactsList.API 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

3.  **웹 게시** 마법사의 **프로필** 단계에서 **Microsoft Azure 앱 서비스**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Azure 계정에 로그인(아직 로그인하지 않은 경우)하거나 자격 증명을 새로 고칩니다(만료된 경우).

4. 앱 서비스 대화 상자에서 사용할 Azure **구독**을 선택하고 **새로 만들기**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

3. **앱 서비스 만들기** 대화 상자의 **호스팅** 탭에서 **형식 변경**을 클릭한 다음 **API 앱**을 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/apptype.png)

	**API 앱**으로 형식 변경은 새 앱에 사용할 수 있는 기능을 결정하지 않습니다. API 정의 URL(이 자습서의 뒷부분에서 다룸), CORS 지원(다음 자습서에서 다룸) 및 인증(이 시리즈의 마지막 3개의 자습서에 다룸)은 API 앱 뿐만 아니라 웹앱 및 모바일 앱에 사용할 수 있습니다. API 앱으로 앱 만들기는 다음과 같은 효과가 있습니다.

	a. Azure 포털에서 앱 유형 아이콘 또는 텍스트는 블레이드 제목 및 앱의 목록에 표시되며 **설정** 블레이드에서 API 섹션은 다른 앱 형식에 비해 API 앱 목록의 앞에 표시됩니다.

	b. Azure SDK for.NET 2.8.1을 사용하는 Visual Studio에서 Visual Studio는 다른 앱 형식이 아닌 새 ASP.NET API 앱을 만드는 동안 API 정의 URL을 설정합니다.

4. *azurewebsites.net* 도메인에서 고유한 **API 앱 이름**을 입력합니다.

	Visual Studio에서 프로젝트 이름에 날짜-시간 문자열을 추가하여 고유한 이름을 제안합니다. 원하는 경우 이 이름을 적용할 수 있습니다.

	다른 사용자가 이미 사용한 이름을 입력한 경우 녹색 확인 표시 대신 오른쪽에 빨간색 느낌표가 표시되며, 다른 이름을 입력해야 합니다.

	Azure에서는 응용 프로그램의 URL에 대한 접두사로 이 이름을 사용합니다. 전체 URL은 이 이름과 *.azurewebsites.net*으로 구성됩니다. 예를 들어 이름이 `ContactsListAPI`이면 URL은 `contactslistapi.azurewebsites.net`이 됩니다.

6. **리소스 그룹** 드롭다운에서 "ContactsListGroup" 또는 원하는 경우 다른 이름을 입력합니다.

	이 상자에서 기존 [리소스 그룹](../azure-preview-portal-using-resource-groups.md)을 선택하거나 구독의 기존 리소스 그룹과 다른 이름을 입력하여 새 리소스 그룹을 만들 수 있습니다.

	이 자습서에서는 새 리소스 그룹을 만드는 것이 가장 좋습니다. 자습서에서 만든 모든 Azure 리소스를 한 번에 쉽게 삭제할 수 있기 때문입니다.

4. **앱 서비스 계획** 드롭다운 옆의 **새로 만들기** 단추를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/createas.png)

	앱 서비스 계획에 대한 자세한 내용은 [앱 서비스 계획 개요](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요.

5. **앱 서비스 계획 구성** 대화 상자에서 "ContactsListPlan" 또는 원하는 경우 다른 이름을 입력합니다.

5. **위치** 드롭다운 목록에서 가장 가까운 위치를 선택합니다.

	이 설정은 앱이 실행되는 Azure 데이터 센터를 지정합니다. 이 자습서에서는 어떤 지역이든 선택할 수 있으며 지역에 따른 뚜렷한 차이는 없습니다. 그러나 프로덕션 앱의 경우 [대기 시간](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)을 최소화하기 위해 액세스하는 클라이언트와 최대한 가깝게 서버를 배치할 수 있습니다.

5. **크기** 드롭다운에서 **무료**를 클릭합니다.

	이 자습서의 경우 무료 가격 책정 계층으로도 충분한 성능이 제공됩니다.

6. **앱 서비스 계획 구성** 대화 상자에서 **확인**을 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. **앱 서비스 만들기** 대화 상자에서 **만들기**를 클릭합니다.

	Visual Studio에서 API 앱을 만들고 새 API 앱에 필요한 모든 설정이 있는 게시 프로필을 만듭니다. 다음 단계에서는 새 게시 프로필을 사용하여 프로젝트를 배포합니다.
 
	**참고:** Azure 앱 서비스에서 API 앱을 만드는 다른 방법도 있습니다. Visual Studio에서는 새 프로젝트를 만드는 동안 동일한 대화 상자를 사용할 수 있습니다. Azure 포털, [Windows PowerShell용 Azure cmdlet](../powershell-install-configure.md) 또는 [플랫폼 간 명령줄 인터페이스](../xplat-cli.md)를 사용하여 API 앱을 만들 수도 있습니다.

8. **웹 게시** 마법사의 **연결** 탭에서 **게시**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio에서 ContactsList.Angular 프로젝트를 새 API 앱에 배포하고 해당 API 앱의 URL로 브라우저를 엽니다. "만들기 성공" 페이지가 브라우저에 표시됩니다.

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. 브라우저 주소 표시줄에서 URL에 "swagger"를 추가한 다음 Enter 키를 누릅니다. URL은 `http://{apiappname}.azurewebsites.net/swagger`입니다.

	이전에 본 것과 동일한 Swagger UI가 브라우저에 표시되지만 이번에는 클라우드에서 실행됩니다. Get 메서드를 사용해 보면 기본 연락처 3개가 반환되는 것을 볼 수 있습니다. 이전에 적용한 변경 내용이 로컬 파일에 저장되었기 때문입니다. 이번에 적용한 변경 내용은 Azure API 앱의 파일 시스템에 저장됩니다.

12. [Azure 포털](https://portal.azure.com/)을 엽니다.
 
14. **찾아보기 > API 앱 > {새 API 앱}**을 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

16. **설정**을 클릭한 다음 **설정** 블레이드에서 API 섹션을 찾아 **API 정의**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	API 정의 블레이드에서 JSON 형식으로 Swagger 2.0 메타데이터를 반환하는 URL을 지정할 수 있습니다. Visual Studio에서 API 앱을 만든 경우 이전에 본 기본값으로 API 정의 URL이 설정됩니다. 즉, API 앱의 기준 URL에 `/swagger/docs/v1`이 추가됩니다.

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	클라이언트 코드를 생성할 API 앱을 선택한 경우 Visual Studio는 이 URL에서 메타데이터를 검색합니다.

### Azure 리소스 관리자 도구에서 API 정의 URL

Azure PowerShell, CLI 또는 [리소스 탐색기](https://resources.azure.com/) 등의 Azure 리소스 관리자 도구를 사용하여 API 앱에 대한 API 정의 URL을 구성할 수도 있습니다.

`<site name>/web` 리소스에 대해 `Microsoft.Web/sites/config` 리소스 유형에서 `apiDefinition` 속성을 설정합니다. 예를 들어, **리소스 탐색기**에서 **구독 > {구독} > resourceGroups > {리소스 그룹} > 공급자 > Microsoft.Web > 사이트 > {사이트} > 구성 > 웹**으로 이동하면 `apiDefinition` 속성이 표시됩니다.

		"apiDefinition": {
		  "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
		}

## <a id="codegen"></a> 생성된 클라이언트 코드를 사용하여 .NET 클라이언트에서 사용

Azure API 앱에 Swagger를 통합할 경우의 장점 중 하나는 자동 코드 생성입니다. 생성된 클라이언트 클래스는 API 앱을 호출하는 코드를 더욱 쉽게 작성하도록 합니다.

이 섹션에서는 ASP.NET MVC 웹앱에서 API 앱을 사용하는 방법을 알아봅니다. 먼저 MVC 클라이언트와 Web API를 로컬로 실행한 다음 Azure 앱 서비스에서 새 웹앱에 클라이언트를 배포하고 클라우드에서 실행합니다.

### 클라이언트 코드 생성

Visual Studio를 사용하거나 명령줄에서 API 앱에 대한 클라이언트 코드를 생성할 수 있습니다. 이 자습서에서는 Visual Studio를 사용합니다. 명령줄에서 이 작업을 수행하는 방법에 대한 자세한 내용은 GitHub.com에서 [Azure/autorest](https://github.com/azure/autorest) 리포지토리의 추가 정보 파일을 참조하세요.

ContactsList.MVC 프로젝트에 생성된 클라이언트 코드가 이미 있지만 이를 삭제하고 다시 생성하여 사용자 고유의 API 앱에 기본 대상 URL을 설정합니다.

1. Visual Studio **솔루션 탐색기**의 ContactsList.MVC 프로젝트에서 *ContactsList.API* 폴더를 삭제합니다.

	이 폴더는 진행하려는 코드 생성 프로세스를 사용하여 만들어집니다.

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. ContactsList.MVC 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 > REST API 클라이언트**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. **REST API 클라이언트 추가** 대화 상자에서 **Microsoft Azure API 앱에서 다운로드**를 클릭한 다음 **찾아보기**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. **앱 서비스** 대화 상자에서 **ContactsListGroup** 리소스 그룹을 확장하고 API 앱을 선택한 다음 **확인**을 클릭합니다.

	스크롤할 API 앱이 너무 많은 경우 이 대화 상자에서 여러 가지 방법으로 목록의 API 앱을 구성할 수 있습니다. 또한 검색 문자열을 입력하여 이름별로 API 앱을 필터링할 수 있습니다.

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	목록에 API 앱이 보이지 않으면 API 앱을 만들 때 웹앱에서 API 앱으로 형식을 변경하는 단계를 실수로 생략했을 수 있습니다. 이 경우 앞서 수행한 단계를 반복하여 새 API 앱을 만들면 됩니다. 포털로 이동하여 웹앱을 먼저 삭제하지 않는 경우 API 앱에 다른 이름을 선택해야 합니다.

	**REST API 클라이언트 추가** 대화 상자로 돌아가면 이전에 포털에서 본 API 정의 URL 값으로 텍스트 상자가 채워져 있는 것을 볼 수 있습니다.

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	코드 생성에 대한 메타데이터를 가져오는 다른 방법은 찾아보기 대화 상자를 사용하는 대신 URL을 직접 입력하는 것입니다. 예를 들어 웹앱에 API를 배포했지만 찾아보기 대화 상자에 표시되지 않는 경우 여기에 Swagger 메타데이터를 반환하는 URL을 수동으로 입력할 수 있습니다.

	메타데이터를 가져오는 다른 방법은 **기존 Swagger 메타데이터 파일 선택** 옵션을 사용하는 것입니다. 예를 들어 Azure에 배포하기 전에 클라이언트 코드를 생성하려는 경우 로컬로 실행하고 Swagger JSON 파일을 다운로드하여 여기에서 선택할 수 있습니다.

9. **REST API 클라이언트 추가** 대화 상자에서 **확인**을 클릭합니다.

	Visual Studio에서 API 앱의 이름을 딴 폴더를 만들고 클라이언트 클래스를 생성합니다.

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. *Controllers\\ContactsController.cs*를 열어 생성된 클라이언트를 사용하여 API를 호출하는 코드를 표시합니다.

	다음 코드 조각은 클라이언트 개체를 인스턴스화하고 Get 메서드를 호출하는 방법을 보여 줍니다.

		private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	이 코드는 MVC 웹 프로젝트 및 API 프로젝트를 로컬로 실행할 수 있도록 API 프로젝트의 로컬 IIS Express URL로 전달된 다음 클라이언트 클래스 생성자로 전달됩니다. 생성자 매개 변수를 생략한 경우 코드를 생성한 URL이 기본 끝점이 됩니다.

6. 클라이언트 클래스는 API 앱 이름을 기반으로 다른 이름으로 생성됩니다. 형식 이름이 프로젝트에서 생성된 이름과 일치하도록 이 코드를 변경하세요. 예를 들어 API 앱을 ContactsListAPIContoso로 명명한 경우 코드는 다음 예제와 같습니다.

		private ContactsListAPIContoso db = new ContactsListAPIContoso(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

7. 솔루션을 빌드하십시오.

MVC 프로젝트의 컨트롤러 및 뷰는 Entity Framework에 대해 스캐폴드된 컨트롤러 및 뷰와 유사합니다. Entity Framework 데이터 모델을 스캐폴드한 다음 Entity Framework 데이터베이스 컨텍스트 대신 REST API 클라이언트를 사용하도록 약간 변경하여 만들어졌기 때문입니다.

### 로컬 실행

1. ContactsList.API가 ContactsList.MVC보다 먼저 시작되도록 지정하여 ContactsList.API 및 ContactsList.MVC 프로젝트를 시작 프로젝트로 설정합니다. 솔루션을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭한 다음 **여러 개의 시작 프로젝트**를 클릭하고 각 프로젝트를 **시작**으로 설정합니다. 위쪽/아래쪽 화살표 아이콘을 사용하여 ContactsList.API가 목록의 첫 번째 항목이 되도록 설정합니다. 

2. F5 키를 눌러 프로젝트를 시작합니다.

	하나의 브라우저에는 API에 대한 403 페이지가 표시되고, 또 하나의 브라우저에는 MVC 앱의 홈 페이지가 표시됩니다.

3. MVC 앱의 홈 페이지가 표시된 브라우저의 메뉴 모음에서 **연락처**를 클릭합니다.

	MVC UI는 로컬로 저장된 연락처를 표시하므로 이 UI를 사용하여 연락처를 추가 및 삭제할 수 있습니다.

	![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

### Azure에서 웹앱을 만들고 이 앱에 ContactsList.MVC 프로젝트 배포

이 섹션에서는 이전에 API 앱을 만들 때와 동일한 방법을 사용하여 웹앱을 만들고 동일한 방법으로 Azure 웹앱에 웹 프로젝트를 배포합니다.

#### Azure API 앱을 가리키도록 MVC 프로젝트 변경 

Azure에 배포하기 전에 코드가 배포되면 localhost 대신 이전에 만든 Azure API 앱이 호출되도록 MVC 프로젝트에서 API 끝점을 변경합니다.

1. ContactsList.MVC 프로젝트에서 *Controllers\\ContactsController.cs*를 엽니다.

2. API 기준 URL을 localhost URL로 설정하는 줄을 주석 처리하고, 생성자 매개 변수가 없는 줄의 주석을 제거합니다. 이제 두 줄 모두에 API 앱의 이름을 반영하는 클래스 이름이 있는 것을 제외하고 코드가 다음 예제와 같이 표시됩니다.

		private ContactsListAPI db = new ContactsListAPI();
		//private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));

	Azure API 앱에서 코드를 생성했기 때문에 기본 대상 URL은 Azure API 앱입니다. 다른 방법으로 코드를 생성한 경우 로컬 URL을 지정할 때와 동일한 방식으로 Azure API 앱 URL을 지정해야 할 수도 있습니다.

#### MVC 사이트를 호스트하는 웹앱 만들기

1. **솔루션 탐색기**에서 ContactsList.MVC 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

2. **웹 게시** 마법사에서 **프로필** 탭을 클릭합니다.

3.  **웹 게시** 마법사의 **프로필** 탭에서 **Microsoft Azure 앱 서비스**를 클릭합니다.

5. **앱 서비스** 대화 상자에서 **새로 만들기**를 클릭합니다.

3. **앱 서비스 만들기** 대화 상자의 **호스팅** 탭에서 **형식 변경**을 클릭한 다음 형식이 **웹앱**인지 확인합니다.

4. *azurewebsites.net* 도메인에서 고유한 **웹앱 이름**을 입력합니다.

5. 사용할 Azure **구독**을 선택합니다.

6. **리소스 그룹** 드롭다운에서 이전에 만든 리소스 그룹을 선택합니다.

4. **앱 서비스 계획** 드롭다운에서 이전에 만든 계획을 선택합니다.

7. **만들기**를 클릭합니다.

	Visual Studio에서 웹앱을 만들고, 해당 게시 프로필을 만든 다음, **웹 게시** 마법사의 **연결** 단계를 표시합니다.

### 새 웹앱에 ContactsList.Web 프로젝트 배포

3.  **웹 게시** 마법사의 **연결** 단계에서 **게시**를 클릭합니다.

	Visual Studio에서 ContactsList.MVC 프로젝트를 새 웹앱에 배포하고 해당 웹앱의 URL로 브라우저를 엽니다. 로컬로 실행할 때 본 것과 동일한 MVC UI가 표시되지만 이번에는 Azure API 앱의 파일 시스템에 저장된 연락처가 표시됩니다.

	![](./media/app-service-api-dotnet-get-started/codegencontacts.png)

## 다음 단계

이 자습서에서는 API 앱을 만들고, 이 앱에 코드를 배포하고, 클라이언트 코드를 생성하고, .NET 클라이언트에서 이를 사용하는 방법을 살펴보았습니다. API 앱 시작 시리즈의 다음 자습서에서는 [CORS를 사용하여 JavaScript 클라이언트에서 API 앱을 사용](app-service-api-cors-consume-javascript.md)하는 방법을 보여 줍니다.

<!---HONumber=AcomDC_0114_2016-->
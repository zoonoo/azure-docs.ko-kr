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
	ms.date="03/09/2016"
	ms.author="tdykstra"/>

# Azure 앱 서비스에서 API 앱 및 ASP.NET 시작

[AZURE.INCLUDE [선택기](../../includes/app-service-api-get-started-selector.md)]

## 개요

다음은 API를 개발 및 호스팅하는 데 Azure 앱 서비스의 기능을 유용하게 사용하는 방법을 보여주는 자습서 시리즈의 첫 번째 항목입니다.

* API 메타데이터에 대한 통합 지원
* CORS 지원
* 인증 및 권한 부여 지원

Azure 앱 서비스에서 두 개의 [API 앱](app-service-api-apps-why-best-platform.md) 및 웹앱에 샘플 응용 프로그램을 배포합니다. 샘플 응용 프로그램은 다이어그램에 표시된 것처럼 AngularJS SPA(단일 페이지 응용 프로그램) 프런트 엔드, ASP.NET Web API 중간 계층 및 ASP.NET Web API 데이터 계층이 있는 할 일 목록입니다.

![](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

SPA 프런트 엔드의 스크린샷은 다음과 같습니다.

![](./media/app-service-api-dotnet-get-started/todospa.png)

이 자습서를 완료하려면 두 개의 Web API를 사용하고 앱 서비스 API 앱을 실행합니다. 다음 자습서를 완료한 후에 앱 서비스 웹앱의 SPA를 사용하여 클라우드에서 실행 중인 전체 응용 프로그램이 있습니다. 이후 자습서에서는 인증 및 권한 부여를 추가합니다.

## 학습할 내용

이 자습서에서는 다음에 대해 알아봅니다.

* Visual Studio 2015의 기본 제공 도구를 사용하여 Azure 앱 서비스에서 API 앱 및 웹앱으로 작업하는 방법
* Swashbuckle NuGet 패키지를 사용하여 Swagger API 정의 JSON을 동적으로 생성하는 방식으로 API 검색을 자동화하는 방법
* 자동으로 생성된 클라이언트 코드를 사용하여 .NET 클라이언트에서 API 앱을 사용하는 방법
* Azure 포털을 사용하여 API 앱 메타데이터에 대한 끝점 구성하는 방법

## 필수 조건

[AZURE.INCLUDE [필수 조건](../../includes/app-service-api-dotnet-get-started-prereqs.md)]

## 샘플 응용 프로그램 다운로드 

1. [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) 리포지토리를 다운로드합니다.

	**ZIP 다운로드** 단추를 클릭하거나 로컬 컴퓨터에서 리포지토리를 복제할 수 있습니다.

2. Visual Studio 2015 또는 2013에서 ToDoList 솔루션을 엽니다.

	Visual Studio 솔루션은 설명 및 소유자로 구성된 간단한 할 일 목록으로 작동하는 샘플 응용 프로그램입니다.

		public class ToDoItem 
		{ 
		    public int ID { get; set; } 
		    public string Description { get; set; } 
		    public string Owner { get; set; } 
		} 
 
	이 솔루션에는 다음과 같은 세 가지 프로젝트가 포함되어 있습니다.

	![](./media/app-service-api-dotnet-get-started/projectsinse.png)

	* **ToDoListAngular** - 프런트 엔드: 중간 계층을 호출하는 AngularJS SPA입니다. 

	* **ToDoListAPI** - 중간 계층: 할 일 항목에서 CRUD 작업을 수행하는 데이터 계층을 호출하는 ASP.NET Web API 프로젝트입니다.

	* **ToDoListDataAPI** - 데이터 계층: 할 일 항목에서 CRUD 작업을 수행하는 ASP.NET Web API 프로젝트입니다. 할 일 항목은 메모리에 저장되며 응용 프로그램이 다시 시작할 때마다 모든 변경 내용이 손실됩니다.

	중간 계층은 데이터 계층을 호출하는 경우 `Owner` 필드의 사용자 ID를 제공합니다. 다운로드한 코드에서 사용자 ID는 항상 "*"입니다. 이후의 자습서에서 인증을 추가하면 중간 계층은 데이터 계층에 실제 사용자 ID를 제공합니다.

2. NuGet 패키지를 복원할 솔루션을 빌드합니다.

### 선택 사항: 로컬로 응용 프로그램 실행

이 섹션에서는 API가 로컬로 실행되는 동안 클라이언트를 로컬로 실행하고 해당 API를 호출할 수 있는지 확인합니다.

**참고:** 이러한 지침은 해당 브라우저에서 `http://localhost` URL에서/로 교차 원본 JavaScript 호출을 허용하므로 Internet Explorer 및 Edge 브라우저에서 작동합니다. Chrome을 사용하는 경우 `--disable-web-security` 스위치로 브라우저를 시작합니다. Firefox를 사용하는 경우 이 섹션을 건너뛰십시오.

1. 먼저 ToDoListDataAPI로 시작한 다음 ToDoListAPI 및 ToDoListAngular를 사용하여 세 개의 프로젝트 모두를 시작 프로젝트로 설정합니다. (**솔루션 탐색기**에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭하며 **여러 개의 시작 프로젝트**를 선택하고 프로젝트를 올바른 순서로 배치한 다음 각각에 대해 **작업**을 **시작**으로 설정합니다.)  

2. F5 키를 눌러 프로젝트를 시작합니다.

	세 개의 브라우저 창이 열립니다. 두 개의 브라우저 창은 웹 API 프로젝트의 일반적인 HTTP 403 오류 페이지(디렉터리 검색은 허용되지 않음)를 보여줍니다. 세 번째 브라우저 창은 AngularJS UI를 표시합니다.

3. AngularJS UI를 보여주는 브라우저 창에서 **할 일 목록** 탭을 클릭합니다.

	UI에는 두 개의 기본 할 일 항목이 표시됩니다.

	![](./media/app-service-api-dotnet-get-started/todospa.png)

4. 응용 프로그램의 작동 방식을 알아보기 위해 할 일 항목을 추가, 편집 및 삭제합니다.

	변경 내용은 메모리에 저장되고 응용 프로그램을 다시 시작한 경우 손실됩니다.

3. 브라우저 창을 닫습니다.

## Swagger 메타데이터 및 UI 사용

Azure 앱 서비스에서는 기본적으로 [Swagger](http://swagger.io/) 2.0 API 메타데이터를 지원합니다. 각 API 앱은 API에 대한 메타데이터를 Swagger JSON 형식으로 반환하는 URL 끝점을 지정합니다. 해당 끝점에서 반환한 메타데이터는 클라이언트 코드를 생성하는 데 사용할 수 있습니다.

ASP.NET Web API 프로젝트는 [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet 패키지를 사용하여 Swagger 메타데이터를 동적으로 생성할 수 있습니다. Swashbuckle NuGet 패키지는 다운로드한 ToDoListDataAPI 및 ToDoListAPI 프로젝트에 이미 설치되어 있습니다.

자습서의 이 섹션에서는 생성된 Swagger 2.0 메타데이터를 살펴본 다음 Swagger 메타데이터를 기반으로 하는 테스트 UI를 사용합니다.

2. ToDoListDataAPI 프로젝트를 시작 프로젝트로 설정합니다. 
 
4. F5 키를 눌러 디버그 모드에서 프로젝트를 실행합니다.

	브라우저가 열리고 HTTP 403 오류 페이지가 표시됩니다.

12. 브라우저의 주소 표시줄에서 줄 끝에 `swagger/docs/v1`을 추가한 다음 Enter 키를 누릅니다. URL은 `http://localhost:45914/swagger/docs/v1`입니다.

	이는 Swashbuckle에서 API에 대한 Swagger 2.0 JSON 메타데이터를 반환하는 데 사용하는 기본 URL입니다.

	Internet Explorer를 사용하는 경우 브라우저에서 *v1.json* 파일을 다운로드하라는 메시지가 표시됩니다.

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Chrome, Firefox 또는 에지를 사용하는 경우 브라우저 창에 JSON이 표시됩니다.

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	다음 샘플에서는 Get 메서드에 대한 정의와 함께 API에 대한 Swagger 메타데이터의 첫 번째 섹션을 보여 줍니다. 이 메타데이터는 다음 단계에서 사용할 Swagger UI를 구동하며, 자습서의 이후 섹션에서 클라이언트 코드를 자동으로 생성하는 데 사용됩니다.

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ToDoListDataAPI"
		  },
		  "host": "localhost:45914",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/ToDoList": {
		      "get": {
		        "tags": [ "ToDoList" ],
		        "operationId": "ToDoList_GetByOwner",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "parameters": [
		          {
		            "name": "owner",
		            "in": "query",
		            "required": true,
		            "type": "string"
		          }
		        ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/ToDoItem" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. 브라우저를 닫습니다.

3. **솔루션 탐색기**의 ToDoListDataAPI 프로젝트에서 *App\_Start\\SwaggerConfig.cs* 파일을 열고 아래의 다음 코드로 스크롤한 후 이 코드의 주석 처리를 제거합니다.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	*SwaggerConfig.cs* 파일은 프로젝트에서 Swashbuckle 패키지를 설치할 때 생성됩니다. 이 파일은 Swashbuckle을 구성하는 다양한 방법을 제공합니다.

	주석을 제거한 코드는 다음 단계에서 사용할 Swagger UI를 사용하도록 설정합니다. API 앱 프로젝트 템플릿을 사용하여 Web API 프로젝트를 만들 때는 하나의 보안 조치로 이 코드가 기본적으로 주석 처리됩니다.

5. 프로젝트를 다시 실행합니다.

3. 브라우저의 주소 표시줄에서 줄 끝에 `swagger`을 추가한 다음 Enter 키를 누릅니다. URL은 `http://localhost:45914/swagger`입니다.

4. Swagger UI 페이지가 나타나면 **ToDoList**를 클릭하여 사용 가능한 메서드를 확인합니다.

	![](./media/app-service-api-dotnet-get-started/methods.png)

5. **가져오기**를 클릭합니다.

6. `owner` 매개 변수의 값에 별표를 입력하고 **사용해 보기**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/gettryitout1.png)

	Swagger UI에서 ToDoList Get 메서드를 호출하고 응답 코드와 JSON 결과를 표시합니다.

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. **Post**를 클릭한 다음 **Model Schema** 아래의 상자를 클릭합니다.

	모델 스키마를 클릭하면 입력 상자가 미리 채워집니다. 여기서 Post 메서드의 매개 변수 값을 지정할 수 있습니다. (Internet Explorer에서 작동하지 않으면 다른 브라우저를 사용하거나 다음 단계에서 매개 변수 값을 수동으로 입력합니다.)

	![](./media/app-service-api-dotnet-get-started/post.png)

7. `contact` 매개 변수 입력 상자에서 다음 예제와 같이 표시되도록 JSON을 변경하거나, 사용자 고유의 설명 텍스트로 대체합니다.

		{
		  "ID": 2,
		  "Description": "buy the dog a toy",
		  "Owner": "*"
		}

10. **Try it out**을 클릭합니다.

	ToDoList API는 성공 여부를 나타내는 HTTP 204 응답 코드를 반환합니다.

11. **Get > Try it out**을 클릭합니다.

	가져오기 메서드 응답은 이제 새 할 일 항목을 포함합니다.

12. Put, Delete 및 Get by ID 메서드도 사용해봅니다.

14. 브라우저를 닫습니다.

Swashbuckle은 모든 ASP.NET Web API 프로젝트에서 작동합니다. Swagger 메타데이터 생성을 기존 프로젝트에 추가하려면 Swashbuckle 패키지를 설치하기만 하면 됩니다.

**참고:** Swagger 메타데이터에는 각 API 작업에 대한 고유 ID가 있습니다. 기본적으로 Swashbuckle은 Web API 컨트롤러 메서드에 대한 중복 Swagger 작업 ID를 생성할 수 있습니다. 컨트롤러에 오버로드된 HTTP 메서드가 있는 경우 이러한 동작이 발생합니다(예: `Get()` 및 `Get(id)`). 오버로드를 처리하는 방법에 대한 자세한 내용은 [Swashbuckle 생성 API 정의 사용자 지정](app-service-api-dotnet-swashbuckle-customize.md)을 참조하세요. Azure API 앱 템플릿을 사용하여 Visual Studio에서 Web API 프로젝트를 만들면 고유한 작업 ID를 생성하는 코드가 *SwaggerConfig.cs* 파일에 자동으로 추가됩니다.

## Azure에서 API 앱을 만들고 이 앱에 ToDoListAPI 프로젝트 배포

이 섹션에서는 Visual Studio **웹 게시** 마법사에 통합된 Azure 도구를 사용하여 Azure에서 새 API 앱을 만듭니다. 그런 다음 새 API 앱에 ToDoListDataAPI 프로젝트를 배포하고, 이번에는 클라우드에서 실행되는 동안 Swagger UI를 다시 실행하여 API를 호출합니다.

1. **솔루션 탐색기**에서 ToDoListDataAPI 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/pubinmenu.png)

3.  **웹 게시** 마법사의 **프로필** 단계에서 **Microsoft Azure 앱 서비스**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Azure 계정에 로그인(아직 로그인하지 않은 경우)하거나 자격 증명을 새로 고칩니다(만료된 경우).

4. 앱 서비스 대화 상자에서 사용할 Azure **구독**을 선택하고 **새로 만들기**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

	**앱 서비스 만들기** 대화 상자의 **호스팅** 탭이 나타납니다.

	Swashbuckle이 설치된 Web API 프로젝트를 배포하기 때문에 Visual Studio는 API 앱을 만들려 한다고 가정합니다. 이는 **API 앱 이름** 제목과 **변경 형식** 드롭다운 목록이 **API 앱**으로 설정되었음을 나타냅니다.

	![](./media/app-service-api-dotnet-get-started/apptype.png)

	<a id="apptype"></a> 앱 형식은 새 API 앱, 웹 앱 또는 모바일 앱에 대해 사용할 수 있는 기능을 결정하지 않습니다. 이 자습서에 표시된 API 앱의 모든 기능을 모두 세 가지 형식에 대해 사용 수 있습니다. 유일한 차이점은 앱 형식을 식별하기 위해 Azure 포털이 표시하는 아이콘 및 텍스트와 포털의 일부 페이지에 나열되는 기능의 순서입니다. 자습서의 후반부에서 Azure 포털을 확인할 수 있습니다. 이는 Azure 리소스를 관리하기 위한 웹 인터페이스입니다.

	이 자습서의 경우 SPA 프런트 엔드가 웹 앱에서 실행되고 각 웹 API 백 엔드가 API 앱에서 실행되지만, 세 개 모두 웹 앱이거나 세 개 모두 API 앱인 경우에는 모두 똑같은 방식으로 작동합니다. 또한 단일 API 앱 또는 웹 앱이 SPA 프런트 엔드와 중간 계층 백 엔드를 모두 호스트할 수 있습니다.

4. 도메인을 고유하게 만드는 ToDoListDataAPI와 숫자 등 *azurewebsites.net* 도메인에서 고유한 **API 앱 이름**을 입력합니다.

	Visual Studio에서 프로젝트 이름에 날짜-시간 문자열을 추가하여 고유한 이름을 제안합니다. 원하는 경우 이 이름을 적용할 수 있습니다.

	다른 사용자가 이미 사용한 이름을 입력한 경우 녹색 확인 표시 대신 오른쪽에 빨간색 느낌표가 표시되며, 다른 이름을 입력해야 합니다.

	Azure에서는 응용 프로그램의 URL에 대한 접두사로 이 이름을 사용합니다. 전체 URL은 이 이름과 *.azurewebsites.net*으로 구성됩니다. 예를 들어 이름이 `ToDoListDataAPI`이면 URL은 `todolistdataapi.azurewebsites.net`이 됩니다.

6. **리소스 그룹** 드롭다운에서 **새로 만들기**를 클릭한 다음, "ToDoListGroup" 또는 원하는 이름을 입력합니다.

	리소스 그룹은 API 앱, 데이터베이스, VM 등 Azure 리소스의 컬렉션입니다. 이 자습서에서는 새 리소스 그룹을 만드는 것이 가장 좋습니다. 자습서에서 만든 모든 Azure 리소스를 한 번에 쉽게 삭제할 수 있기 때문입니다.

	이 상자에서 기존 [리소스 그룹](../azure-portal/resource-group-portal.md)을 선택하거나 구독의 기존 리소스 그룹과 다른 이름을 입력하여 새 리소스 그룹을 만들 수 있습니다.

4. **앱 서비스 계획** 드롭다운 옆의 **새로 만들기** 단추를 클릭합니다.

	스크린샷은 **API 앱 이름**, **구독** 및 **리소스 그룹**에 대한 샘플 값을 보여줍니다. 값은 달라질 수 있습니다.

	![](./media/app-service-api-dotnet-get-started/createas.png)

	다음 단계에서는 새 리소스 그룹에 대한 앱 서비스 계획을 만듭니다. 앱 서비스 계획은 API 앱이 실행되는 계산 리소스를 지정합니다. 예를 들어, 무료 계층을 선택한 경우 API 앱은 공유 VM에서 실행되지만, 일부 유료 계층에의 경우 전용 VM에서 실행됩니다. 앱 서비스 계획에 대한 자세한 내용은 [앱 서비스 계획 개요](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요.

5. **앱 서비스 계획 구성** 대화 상자에서 "ToDoListPlan" 또는 원하는 경우 다른 이름을 입력합니다.

5. **위치** 드롭다운 목록에서 가장 가까운 위치를 선택합니다.

	이 설정은 앱이 실행되는 Azure 데이터 센터를 지정합니다. 이 자습서에서는 어떤 지역이든 선택할 수 있으며 지역에 따른 뚜렷한 차이는 없습니다. 그러나 프로덕션 앱의 경우 [대기 시간](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)을 최소화하기 위해 액세스하는 클라이언트와 최대한 가깝게 서버를 배치할 수 있습니다.

5. **크기** 드롭다운에서 **무료**를 클릭합니다.

	이 자습서의 경우 무료 가격 책정 계층으로도 충분한 성능이 제공됩니다.

6. **앱 서비스 계획 구성** 대화 상자에서 **확인**을 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. **앱 서비스 만들기** 대화 상자에서 **만들기**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/clickcreate.png)

	Visual Studio는 API 앱을 만듭니다.

	**참고:** Azure 앱 서비스에서 API 앱을 만드는 다른 방법도 있습니다. 예를 들어 Visual Studio에서 새 프로젝트를 만드는 경우 기존 프로젝트에서 봤던 것과 같은 방식으로 Azure 리소스를 만들 수 있습니다. [Azure 포털](https://portal.azure.com/), [Windows PowerShell용 Azure cmdlet](../powershell-install-configure.md) 또는 [플랫폼 간 명령줄 인터페이스](../xplat-cli.md)를 사용하여 API 앱을 만들 수도 있습니다.

	Visual Studio에서 API 앱을 만들고 나면 새 API 앱에 필요한 모든 설정이 있는 게시 프로필을 만듭니다. 다음 단계에서는 새 게시 프로필을 사용하여 프로젝트를 배포합니다.

8. **웹 게시** 마법사의 **연결** 탭에서 **다음**을 클릭합니다.

	대신에 그냥 진행하고 **게시**를 클릭하면 즉시 새 API 앱에 배포할 수 있지만, 자습서의 경우 이 대화 상자의 다른 탭을 통해 수행할 수 있는 작업을 볼 수 있습니다.

	![](./media/app-service-api-dotnet-get-started/connnext.png)

	다음 탭은 **설정** 탭입니다. 여기에서 빌드 구성 탭을 변경하여 [원격 디버깅](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)을 위한 디버그 빌드를 배포할 수 있습니다. 이 탭은 몇 가지 **파일 게시 옵션**도 제공합니다.

	* 대상에 있는 추가적인 파일을 제거합니다.
	* 게시 중 미리 컴파일합니다.
	* App\_Data 폴더에서 파일을 실행합니다.

	이 자습서의 경우 이런 것들이 필요하지 않습니다. 옵션을 통해 수행하는 작업에 대한 자세한 설명은 [Visual Studio의 One-Click 게시를 사용하여 웹 프로젝트 배포 방법](https://msdn.microsoft.com/library/dd465337.aspx)을 참조하세요.

14. **다음**을 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/settingsnext.png)

	**미리 보기** 탭은 어떤 파일이 프로젝트에서 API 앱으로 복사되는지 볼 수 있습니다. 이전에 배포한 API 앱에 프로젝트를 배포하는 경우, 변경된 파일만 복사됩니다. 복사될 파일 목록을 표시하려면 **미리 보기 시작** 단추를 클릭할 수 있습니다.

15. **게시**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio는 새 API 앱에 ToDoListDataAPI 프로젝트를 배포합니다. **출력** 창은 성공한 배포를 기록하고 API 앱의 URL에 열린 브라우저 창에 "성공적으로 만들어진" 페이지가 나타납니다.

	![](./media/app-service-api-dotnet-get-started/deploymentoutput.png)

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. 브라우저 주소 표시줄에서 URL에 "swagger"를 추가한 다음 Enter 키를 누릅니다. URL은 `http://{apiappname}.azurewebsites.net/swagger`입니다.

	이전에 본 것과 동일한 Swagger UI가 브라우저에 표시되지만 이번에는 클라우드에서 실행됩니다. 가져오기 메서드를 사용하면 이전에 만든 변경 내용이 로컬 컴퓨터의 메모리에 저장되기 때문에 기본 2가지 할 일 항목으로 다시 돌아옵니다.

12. [Azure 포털](https://portal.azure.com/)을 엽니다.

	Azure 포털은 API 앱 같은 Azure 리소스 관리를 위한 웹 인터페이스입니다.
 
14. **찾아보기 > 앱 서비스**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/browseas.png)

15. **앱 서비스** 블레이드에서 새 API 앱을 찾아 클릭합니다. (Azure 포털에서는 오른쪽으로 열리는 창을 *블레이드*라고 합니다.)

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

	두 블레이드가 열리는 데, 하나에는 API 앱의 개요가 있고 다른 하나에는 보고 변경할 수 있는 설정의 긴 목록이 있습니다.

16. **설정** 블레이드에서 **API** 섹션을 찾아 **API 정의**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	**API 정의** 블레이드에서 JSON 형식으로 Swagger 2.0 메타데이터를 반환하는 URL을 지정할 수 있습니다. Visual Studio에서 API 앱을 만든 경우, API 정의 URL을 이전에 본 Swashbuckle 생성 메타데이터의 기본값으로 설정됩니다. 즉, API 앱의 기준 URL에 `/swagger/docs/v1`이(가) 추가됩니다.

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	클라이언트 코드를 생성할 API 앱을 선택한 경우 Visual Studio는 이 URL에서 메타데이터를 검색합니다.

## <a id="codegen"></a> 생성된 클라이언트 코드를 사용하여 API 앱 사용

Azure API 앱에 Swagger를 통합할 경우의 장점 중 하나는 자동 코드 생성입니다. 생성된 클라이언트 클래스는 API 앱을 호출하는 코드를 더욱 쉽게 작성하도록 합니다.

이 섹션에서는 ASP.NET Web API 코드에서 API 앱을 사용하는 방법을 알아봅니다.

### 클라이언트 코드 생성

Visual Studio를 사용하거나 명령줄에서 API 앱에 대한 클라이언트 코드를 생성할 수 있습니다. 이 자습서에서는 Visual Studio를 사용합니다. 명령줄에서 이 작업을 수행하는 방법에 대한 자세한 내용은 GitHub.com에서 [Azure/autorest](https://github.com/azure/autorest) 리포지토리의 추가 정보 파일을 참조하세요.

ToDoListAPI 프로젝트에는 생성된 클라이언트 코드가 이미 있지만 이를 삭제하고 다시 생성하여 수행되는 방법을 확인합니다.

1. Visual Studio **솔루션 탐색기**의 ToDoListAPI 프로젝트에서 *ToDoListDataAPI* 폴더를 삭제합니다.

	이 폴더는 진행하려는 코드 생성 프로세스를 사용하여 만들어집니다.

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. ToDoListAPI 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 > REST API 클라이언트**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. **REST API 클라이언트 추가** 대화 상자에서 **Swagger URL**을 클릭한 다음 **Azure 자산 선택**을 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. **앱 서비스** 대화 상자에서 이 자습서에 사용하는 리소스 그룹을 확장하고 API 앱을 선택한 다음 **확인**을 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	스크롤할 API 앱이 너무 많은 경우 이 대화 상자에서 여러 가지 방법으로 목록의 API 앱을 구성할 수 있습니다. 또한 검색 문자열을 입력하여 이름별로 API 앱을 필터링할 수 있습니다.

	**REST API 클라이언트 추가** 대화 상자로 돌아가면 이전에 포털에서 본 API 정의 URL 값으로 텍스트 상자가 채워져 있는 것을 볼 수 있습니다.

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	코드 생성에 대한 메타데이터를 가져오는 다른 방법은 찾아보기 대화 상자를 사용하는 대신 URL을 직접 입력하는 것입니다. 다른 방법은 **기존 Swagger 메타데이터 파일 선택** 옵션을 사용하는 것입니다. 예를 들어 Azure에 배포하기 전에 클라이언트 코드를 생성하려는 경우 로컬로 Web API 프로젝트를 실행하고, Swagger JSON 파일을 제공하는 URL로 이동한 다음, 파일을 저장하여 여기에서 선택합니다.

9. **REST API 클라이언트 추가** 대화 상자에서 **확인**을 클릭합니다.

	Visual Studio에서 API 앱의 이름을 딴 폴더를 만들고 클라이언트 클래스를 생성합니다.

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. ToDoListAPI 프로젝트에서 *Controllers\\ToDoListController.cs*를 열어 생성된 클라이언트를 사용하여 API를 호출하는 코드를 표시합니다.

	다음 코드 조각은 코드가 클라이언트 개체를 인스턴스화하고 가져오기 메서드를 호출하는 방법을 보여줍니다.

		private ToDoListDataAPI db = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	생성자 매개 변수는 `toDoListDataAPIURL` 앱 설정에서 끝점 URL을 가져옵니다. Web.config 파일에서 해당 값은 응용 프로그램을 로컬로 실행할 수 있도록 API 프로젝트의 로컬 IIS Express URL에 설정됩니다. 생성자 매개 변수를 생략한 경우 코드를 생성한 URL이 기본 끝점이 됩니다.

6. 클라이언트 클래스는 API 앱 이름을 기반으로 다른 이름으로 생성됩니다. 형식 이름이 프로젝트에서 생성된 이름과 일치하도록 *Controllers\\ToDoListController.cs*에서 코드를 변경합니다. 예를 들어 API 앱을 ToDoListDataAPI0121로 명명한 경우 코드는 다음 예제와 같습니다.

		private ToDoListDataAPI0121 db = new ToDoListDataAPI0121(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

### 중간 계층을 호스트하는 API 앱 만들기

1. **솔루션 탐색기**에서 ToDoListAPI 프로젝트(ToDoListDataAPI 아님)를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

3.  **웹 게시** 마법사의 **프로필** 탭에서 **Microsoft Azure 앱 서비스**를 클릭합니다.

5. **앱 서비스** 대화 상자에서 **새로 만들기**를 클릭합니다.

3. **앱 서비스 만들기** 대화 상자의 **호스팅** 탭에 *azurewebsites.net* 도메인에서 고유한 **API 앱 이름**을 입력합니다.

5. 사용할 Azure **구독**을 선택합니다.

6. **리소스 그룹** 드롭다운에서 이전에 만든 리소스 그룹을 선택합니다.

4. **앱 서비스 계획** 드롭다운에서 이전에 만든 계획을 선택합니다. 해당 값으로 초기화됩니다.

7. **만들기**를 클릭합니다.

	Visual Studio에서 API 앱을 만들고 해당 게시 프로필을 만든 다음, **웹 게시** 마법사의 **연결** 단계를 표시합니다.

3.  **웹 게시** 마법사의 **연결** 단계에서 **게시**를 클릭합니다.

	Visual Studio에서 ToDoListAPI 프로젝트를 새 API 앱에 배포하고 해당 API 앱의 URL로 브라우저를 엽니다. "성공적으로 만들었습니다." 페이지가 나타납니다.

### 중간 계층 API 앱에서 데이터 계층 API 앱 URL 설정

지금 중간 계층 API 앱을 호출한 경우 Web.config 파일에 있는 localhost URL을 사용하여 데이터 계층을 호출하려고 합니다. 이 섹션에서 데이터 계층 API 앱 URL을 중간 계층 API 앱의 환경 설정에 입력합니다. 중간 계층 API 앱의 코드가 데이터 계층 URL 설정을 검색하는 경우 환경 설정은 Web.config 파일의 내용을 재정의합니다.
 
1. [Azure 포털](https://portal.azure.com/)로 이동한 다음, TodoListAPI(중간 계층) 프로젝트를 호스트하기 위해 만든 API 앱용 **API 앱** 블레이드로 이동합니다.

2. API 앱의 **설정** 블레이드에서 **앱 설정**을 클릭합니다.
 
4. API 앱의 **응용 프로그램 설정** 블레이드에서 **앱 설정** 섹션까지 아래로 스크롤하고 다음 키와 값을 추가합니다.

	| **키** | toDoListDataAPIURL |
	|---|---|
	| **값** | https://{your 데이터 계층 API 앱 이름}.azurewebsites.net |
	| **예제** | https://todolistdataapi0121.azurewebsites.net |

4. **Save**를 클릭합니다.

	![](./media/app-service-api-dotnet-get-started/asinportal.png)

	코드가 Azure에서 실행되면 이 값은 이제 Web.config 파일에 있는 localhost URL을 재정의합니다.

### ToDoListAPI가 ToDoListDataAPI를 호출하는지 확인하는 테스트

11. 브라우저 창에서 방금 만든 새 중간 계층 API 앱의 URL로 이동합니다(포털에서 API 앱의 주 블레이드에 있는 URL을 클릭하여 이동할 수 있습니다).

13. 브라우저 주소 표시줄에서 URL에 "swagger"를 추가한 다음 Enter 키를 누릅니다. URL은 `http://{apiappname}.azurewebsites.net/swagger`입니다.

	브라우저가 ToDoListDataAPI에 대해 앞서 살펴본 동일한 Swagger UI를 표시하지만 중간 계층 API 앱이 해당 값을 데이터 계층 API 앱에 보내고 있기 때문에 이제 `owner`은 가져오기 작업에서 필수 필드가 아닙니다. (인증 자습서를 수행하는 경우 중간 계층은 `owner` 매개 변수에 대한 실제 사용자 ID를 보냅니다. 지금은 별표를 하드 코딩하고 있습니다.)

12. 가져오기 메서드 및 다른 메서드를 사용하여 중간 계층 API 앱이 데이터 계층 API 앱을 성공적으로 호출하는지 유효성을 검사합니다.

	![](./media/app-service-api-dotnet-get-started/midtierget.png)

생성된 클라이언트에 대한 자세한 내용은 [AutoRest GitHub 리포지토리](https://github.com/azure/autorest)를 참조하세요. 생성된 클라이언트 사용과 관련하여 도움이 필요하면 [AutoRest 리포지토리의 문제](https://github.com/azure/autorest/issues)를 참조하세요.

## <a id="creating"></a> 선택 사항: 처음부터 API 앱 프로젝트 만들기

이 자습서에서는 처음부터 새 프로젝트를 만들지 않고 ASP.NET Web API 프로젝트를 다운로드하여 앱 서비스에 배포합니다. API 앱에 배포하기 위해 프로젝트를 만들려면 일반적인 Web API 프로젝트를 만들고 Swashbuckle 패키지를 설치하거나 **Azure API 앱** 새 프로젝트 템플릿을 사용할 수 있습니다. 해당 템플릿을 사용하려면 **파일 > 새로 만들기 > 프로젝트 > ASP.NET 웹 응용 프로그램 > Azure API 앱**을 클릭합니다.

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

**Azure API 앱** 프로젝트 템플릿은 **빈** ASP.NET 4.5.2 템플릿을 선택하고, 확인란을 클릭하여 Web API 지원을 추가하고, Swashbuckle 패키지를 설치하는 것과 동일합니다. 또한 탬플릿은 중복 Swagger 작업 ID가 만들어지지 않도록 하기 위해 설계된 일부 Swashbuckle 구성 코드를 추가합니다.

## 선택 사항: 앱 형식 변경

[앞에서](#apptype) 설명한 대로 API 앱, 웹 앱 및 모바일 앱의 유일한 차이점은 포털에 표시되는 방식입니다. 모두 동일한 기능을 가지고 있기 때문에 앱 형식을 변경할 필요가 없습니다.

그러나 포털 표시를 변경하려는 경우 쉽게 변경할 수는 있습니다. 예를 들어, 다음 단계를 수행하여 방금 만든 API 앱 중 하나를 웹앱으로 변경할 수 있습니다.

1. 리소스 탐색기를 엽니다.

2. 왼쪽 탐색 창에서 **구독**을 확장한 다음, 작업하고 있던 구독을 확장합니다.

4. **resourceGroups**을 확장한 다음, 작업하고 있던 리소스 그룹을 확장합니다.

5. **Microsoft.Web**을 확장하고 **사이트**를 확장한 다음 변경하려는 API 앱을 선택합니다.

6. **편집**을 클릭합니다.

8. `kind` 속성을 찾아 "api"에서 "WebApp"으로 변경합니다.

	![](./media/app-service-api-dotnet-get-started/resexp.png)

9. **배치**를 클릭합니다.

10. Azure 포털로 이동하면 새 앱 형식을 반영하는 변경된 아이콘이 표시됩니다.

## 선택 사항: Azure Resource Manager 탬플릿에서 API 정의 URL

이 자습서에서는 Visual Studio와 Azure 포털에서 API 정의 URL을 확인했습니다. 또한 [Azure PowerShell](../powershell-install-configure.md)과 [Azure CLI](../xplat-cli-install.md) 등 명령줄 도구의 [Azure Resource Manager 도구](../resource-group-authoring-templates.md)를 사용하여 API 앱에 대한 API 정의 URL을 구성할 수 있습니다.

API 정의 속성을 설정하는 Azure Resource Manager 템플릿의 예를 보려면 [이 자습서의 샘플 응용 프로그램에 대한 리포지토리에 있는 azuredeploy.json 파일](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json)을 엽니다. 다음 예제와 같은 탬플릿의 섹션을 찾습니다.

		"apiDefinition": {
		  "url": "https://todolistdataapi.azurewebsites.net/swagger/docs/v1"
		}

## 다음 단계

이 자습서에서는 API 앱을 만들고, 이 앱에 코드를 배포하고, 클라이언트 코드를 생성하고, .NET 클라이언트에서 이를 사용하는 방법을 살펴보았습니다. API 앱 시작 시리즈의 다음 자습서에서는 [CORS를 사용하여 JavaScript 클라이언트에서 API 앱을 사용](app-service-api-cors-consume-javascript.md)하는 방법을 보여 줍니다. 시리즈의 후반부 자습서에서는 인증 및 권한 부여를 구현하는 방법을 보여 줍니다.

<!---HONumber=AcomDC_0323_2016-->
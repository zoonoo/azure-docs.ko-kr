<properties 
	pageTitle="기존 API를 API 앱으로 변환" 
	description="Azure 앱 서비스에서 API 앱으로 배포하기 위해 Web API Visual Studio 프로젝트를 구성하는 방법을 알아봅니다." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# 기존 API를 API 앱으로 변환

## 개요

이 자습서에서는 Visual Studio에서 ASP.NET Web API 프로젝트를 만든 후 프로젝트를 Azure 앱 서비스에서 API 앱으로 게시 및 배포할 수 있게 해주는 NuGet 패키지 및 프로젝트 메타데이터를 추가합니다. 또한 이 자습서에서는 API 앱 메타데이터를 사용자 지정하는 방법도 설명합니다.

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Web API 프로젝트 만들기

1.  Visual Studio에서 **새 프로젝트** 대화 상자를 엽니다.

2.  **설치된 템플릿** 창에서 **클라우드** 노드를 선택한 후 **ASP.NET 웹 응용 프로그램** 템플릿을 선택합니다.

3.  프로젝트의 이름을 *ContactsList*로 지정한 후 **확인**을 클릭합니다.

	![New Project dialog](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate.png)

4. **새 ASP.NET 프로젝트** 대화 상자에서 **빈** 템플릿을 선택하고 **Web API** 확인란을 클릭한 후 **클라우드에 호스트** 확인란을 선택 취소하고 **확인**을 클릭합니다.

	![Azure API App template](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate2.png)

	Visual Studio에서 빈 Web API 프로젝트에 대한 프로젝트 파일을 만듭니다.

	![Web API files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/sewebapi.png)

3. **솔루션 탐색기**에서 **Models** 폴더를 마우스 오른쪽 단추로 클릭한 후 상황에 맞는 메뉴에서 **추가 > 클래스**를 클릭합니다. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-class-v2.png) 

4. 새 파일의 이름을 *Contact.cs*로 지정한 후 **추가**를 클릭합니다.

5. 새 파일 내용을 다음 코드로 바꿉니다. 

		namespace ContactsList.Models
		{
			public class Contact
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

5. **Controllers** 폴더를 마우스 오른쪽 단추로 클릭한 후 상황에 맞는 메뉴에서 **추가 > 컨트롤러**를 클릭합니다. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller.png)

6. **스캐폴드 추가** 대화 상자에서 **Web API 2 컨트롤러 - 비어 있음** 옵션을 선택한 후 **추가**를 클릭합니다. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-dialog.png)

7. 컨트롤러 이름을 **ContactsController**로 지정한 후 **추가**를 클릭합니다. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-name.png)

8. 새 컨트롤러 파일의 코드를 다음 코드로 바꿉니다. 

		using ContactsList.Models;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactsList.Controllers
		{
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]{
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"},
		            };
		        }
		    }
		}


이제 작동하는 Web API 프로젝트가 있습니다. 프로젝트가 작동하는지 확인하는 가장 간단한 방법은 브라우저에서 Get 메서드를 호출하는 것입니다.

6. Ctrl+F5를 눌러 프로젝트를 실행합니다.

	Get 메서드를 가리키는 완전한 URL 없이 열리므로 브라우저에 HTTP 403 오류가 표시됩니다.

7. 브라우저 주소 표시줄에서 URL에 "api/contacts/get/"를 추가한 후 Enter 키를 누릅니다.  최종 URL은 다음 예와 유사합니다.

		http://localhost:25735/api/contacts/get/

	브라우저마다 API 호출에 대한 응답 방식이 다릅니다. Internet Explorer를 사용하는 경우 브라우저 창 아래쪽에 다운로드 메시지가 표시됩니다.

	![](./media/app-service-dotnet-create-api-app-visual-studio/get-response.png)

## API 앱으로 배포하기 위한 응용 프로그램 구성

1. **솔루션 탐색기**에서 솔루션이 아니라 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **추가 > Azure API 앱 SDK**를 클릭합니다.

	![Add API app metadata](./media/app-service-dotnet-create-api-app-visual-studio/addapiappsdk.png)

2. **Choose API App Metadata source(API 앱 메타데이터 소스 선택)** 대화 상자에서 **Automatic Metadata Generation(자동 메타데이터 생성)**을 클릭합니다.

	![Choose automatic metadata](./media/app-service-dotnet-create-api-app-visual-studio/chooseswagger.png)

	이 선택은 자습서 뒷부분에서 보게 될 동적 Swagger UI를 사용할 수 있게 해줍니다. 또는 정적 Swagger API 정의 파일을 제공할 수 있습니다. 이렇게 하려면 **Specify a static JSON file containing Swagger metadata(Swagger 메타데이터가 포함된 정적 JSON 파일 지정)**를 선택합니다. 그러면 Visual Studio에 파일을 업로드하라는 메시지가 표시됩니다. 업로드한 파일이 *Metadata* 폴더에 *apiDefinition.Swagger.json*으로 저장됩니다.

3. **확인**을 클릭합니다.

	Visual Studio에 *apiapp.json* 파일 및 *Metadata* 폴더가 추가됩니다.

	![API App files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatainse.png)

	Visual Studio에서 Swashbuckle NuGet 패키지도 추가되고, 다음 단계에서 Swagger가 제공하는 동적 API 정의 UI를 테스트합니다. 

6. Ctrl+F5를 눌러 프로젝트를 실행합니다.

	앞서와 같이 브라우저에 HTTP 403 오류가 표시됩니다.

7. 브라우저 주소 표시줄에서 URL에 "swagger/"를 추가한 후 Enter 키를 누릅니다.  최종 URL은 다음 예와 유사합니다.

		http://localhost:25735/swagger/

8. Swagger 페이지에서 **Contacts**를 클릭하면 사용 가능한 메서드가 표시됩니다.
 
	Contacts 컨트롤러에서 만든 유일한 메서드가 `Get`이므로 이 메서드만 표시됩니다. 페이지에 샘플 응답 JSON이 표시됩니다.

9. Get 메서드를 클릭하면 샘플 JSON 응답 및 **Try it out** 단추가 표시됩니다.

 	![Swagger - expand Contacts](./media/app-service-dotnet-create-api-app-visual-studio/swagger1.png)

9. **Try it out**을 클릭합니다.

	ContactsController에서 코딩한 응답이 반환됩니다.

 	![Swagger - Try it out](./media/app-service-dotnet-create-api-app-visual-studio/swagger2.png)

이제 Web API 프로젝트를 Azure 앱 서비스에서 API 앱으로 배포할 준비가 되었습니다. 이 자습서의 다음 섹션에서는 API 앱을 사용자 지정하기 위해 변경할 수 있는 메타데이터에 대한 정보를 제공합니다. 

## apiapp.json 검토

 *apiapp.json* 파일의 설정은 API 앱을 식별하는 방법 및 Azure 마켓플레이스에 표시하는 방법을 결정합니다. 이 시험판 릴리스에서는 Visual Studio가 API 앱을 마켓플레이스에 게시하는 기능을 포함하지 않으므로 이러한 설정 중 많은 부분이 적용되지 않습니다.

![apiapp.json in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/apiappjsoninse.png)

**Azure API 앱 SDK** 메뉴 항목을 선택할 때 만들어지는 파일의 초기 내용은 다음 예와 유사합니다.

		{
		  "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		  "id": "ContactsList",
		  "namespace": "",
		  "gateway": "2015-01-14",
		  "version": "1.0.0",
		  "title": "ContactsList",
		  "summary": "",
		  "author": "",
		  "endpoints": {
		    "apiDefinition": "/swagger/docs/v1",
		    "status": null
		  }
		}

다음 표에는 파일에 포함된 필드 및 파일에 추가할 수 있는 추가 선택적 필드의 형식 및 사용법이 설명되어 있습니다. 

**참고:** 앞에서 언급한 것처럼, 이 시험판 릴리스의 경우 이러한 필드의 많은 부분에서 API 앱을 Azure 마켓플레이스에 표시하는 방법을 결정하지만 Visual Studio에 API 앱을 마켓플레이스에 게시하는 기능이 아직 포함되어 있지 않아 이러한 필드의 많은 부분이 적용되지 않습니다.

| 이름(굵게=필수) | 형식 | 설명 |
|:-----------|:------------|:------------|
|**id**           |string|이 패키지의 ID입니다. 네임스페이스 내에서 고유해야 하고 마침표, /, 또는 @ 문자를 포함해서는 안 됩니다. 프로젝트를 배포할 때 Visual Studio에서는 ID가 고유하고 사용자에게 ID를 변경할 기회를 제공하는지 확인합니다. 
|**namespace**    |string|**id** 속성과 함께 API 앱을 고유하게 식별하는 네임스페이스입니다.  이 속성은 필수이지만 값은 빈 문자열일 수 있습니다. <br/><br/>이 속성을 사용하면 contoso.com과 같은 도메인을 지정할 수 있습니다.  사용하려는 패키지 ID가 이미 사용 중인 경우 해당 패키지 ID를 사용할 수 있도록 도메인을 추가할 수 있습니다. 예를 들어 ContactsList가 네임스페이스 없이 API 앱 갤러리에 이미 있는 경우 contoso.com 네임스페이스를 사용하여 ContactsList 패키지를 추가할 수 있습니다. <br/><br/>도메인을 지정하는 또 다른 이유는 조직의 구성원만 액세스할 수 있는 API 앱 갤러리에 패키지를 추가하기 위해서입니다. <br/><br/>마침표를 하이픈으로 변환하고 하이픈을 두 개의 하이픈(--)으로 변환한 후 네임스페이스가 마켓플레이스에 판매자 이름으로 사용됩니다.<br/><br/>Microsoft에서 제공하는 API 앱의 경우 네임스페이스는 "microsoft.com"입니다.  
|**version**      |string|[Semver](http://docs.nuget.org/Create/Versioning) 형식입니다(예: 1.0.1, 1.1.0-alpha).
|**gateway**      |string|날짜로 표시되는 게이트웨이 버전입니다(예: 2015-01-14).  *gateway*는 리소스 그룹의 API 앱에 대한 모든 요청이 라우팅되는 특수 웹앱입니다. 주요 기능 중 하나는 인증을 처리하는 것입니다. 현재 유일한 게이트웨이 버전은 2015-01-14입니다. 나중에 새 게이트웨이 버전이 출시되면 이 속성을 통해 단절을 초래하는 변경을 방지하고 이전 게이트웨이 API를 계속 사용할 수 있습니다. 
|**title**        |string|API 앱의 표시 이름입니다.
|**summary**      |string|API 앱의 간단한 요약입니다(최대 100자).
|description      |string|API 앱의 전체 설명입니다. HTML을 포함할 수 있습니다(최대 1,500자).
|**author**       |string|API 앱의 만든 이입니다(최대 256자).
|homepage         |URI|API 앱의 홈페이지입니다.
|endpoints        |object[]|API 정의 끝점을 포함할 수 있는 한 요소의 배열입니다. 
|>>endpoints.apiDefinition|string|동적 Swagger API 정의 UI의 상대 URI(예: "/swagger/docs/v1") 또는 정적 Swagger API 정의 파일입니다. ASP.NET Web API의 경우 동적으로 생성된 Swagger UI는 일반적으로 최상의 선택이지만 API와 작동하지 않거나 ASP.NET Web API를 사용하지 않는 경우 정적 정의 파일을 제공할 수 있습니다. 정적 정의 파일을 제공하려면 여기에 해당 파일을 가리키는 상대 URI를 지정하거나, 이 속성을 비워 두고 Metadata 폴더에 정적 API 정의 파일을 [apiDefinition.swagger.json](#apidef)으로 포함하면 됩니다. 
|>>endpoints.status|URI|나중에 사용할 수 있도록 예약되었습니다.
|categories       |string[]|Azure 마켓플레이스에서 패키지를 표시할 위치를 결정합니다. 유효한 값은 social, enterprise, integration, protocol, app-datasvc, other입니다. 기본값: other입니다.
|license          |object|API 앱의 라이선스입니다.
|>>**license.type**|string|SPDX 라이선스 ID입니다(예: MIT).
|>>license.url    |url|전체 라이선스 텍스트를 가리키는 절대 URL입니다.
|>>license.requireAcceptance|bool|설치 전에 라이선스를 승인해야 하는지 여부입니다. 기본값: false입니다.
|links            |object[]|마켓플레이스 페이지에 추가할 링크의 배열입니다.
|>>**links.text** |string|링크의 텍스트입니다.
|>>**links.url**  |url|링크의 URL입니다.
|authentication|object[]|나가는 API 호출을 만들기 위해 이 API 앱에 필요한 인증의 종류를 나타내는 배열입니다.  예를 들어 DropBox Connector는 DropBox에 인증해야 하고 Salesforce Connector는 Salesforce에 인증해야 합니다.
|>>authentication.type|string|지원되는 값은 Box, DropBox, Facebook, GitHub, Google, Instagram, Marketo, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, SugarCRM, Twitter, Yammer입니다(대/소문자 구분 안 함). 이 값에 따라 포털은 어떤 구성 값(예: 클라이언트 ID 및 클라이언트 암호)이 필수인지 알 수 있습니다. 
|>>authentication.scopes|string[]|인증 유형과 관련된 범위의 배열입니다.
|copyright        |string|API 앱의 저작권 표시입니다.
|brandColor       |string|모든 CSS 호환 형식(예: #abc, red)으로 UI 환경을 구동하는 선택적 브랜드 색입니다.
|tags             |string[]|패키지와 관련된 태그의 목록입니다.

<!--종속성을 문서화할 준비가 된 경우 todo 추가
|dependencies    |object[]|패키지 종속성의 배열입니다.
|>>dependencies.id|string|종속성 패키지의 ID입니다.
|>>dependencies.domain|string|종속성 패키지의 도메인입니다.
|>>dependencies.version|string|종속성 패키지의 버전입니다.
-->

<!--상태 URI를 문서화할 준비가 된 경우 todo 추가
API 앱의 현재 상태를 나타내는 값을 반환하는 웹 서비스 Get 메서드에 대한 URI입니다. 이 URI를 제공하는 경우 포털에 API 앱의 현재 작동 상태와 API 앱에 대한 기타 정보가 함께 표시됩니다(예:  실행 중, 할당량을 거의 다 사용함, SSL 인증서 만료 등). 포털에서 수신될 것으로 예상하는 JSON의 형식은 아래에 표시되어 있습니다(이 표의 끝부분 뒤에 있음). endpoints.status URI를 제공하지 않으면 포털에 Azure 플랫폼 상태가 API 앱의 상태로 표시됩니다.
다음은 `endpoints.status`가 가리키는, Get 메서드의 JSON 응답의 필요한 형식을 보여 주는 예입니다.
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
 `name` 속성은 상태에 대한 간단한 설명이고,  `message`는 자세한 설명이며,  `level`은 "Error", "Warning" 또는 정상 상태를 나타내는 "Info"일 수 있습니다.
-->

## Metadata 폴더 검토

Metadata 폴더에는 API 앱 갤러리, API를 문서화하는 Swagger 파일 및 Azure 포털의 UI 구성에 대한 아이콘 및 스크린샷이 포함될 수 있습니다. 이러한 모든 정보는 선택 사항입니다.

<!-- todo: add later
an Azure Resource Manager template that specifies resources required, 
-->

![Metadata folder in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatafolderinse.png)

**참고:** 앞에서 *apiapps.json*에 대해 언급한 것처럼, Azure 마켓플레이스에서 API 앱의 표시와 관련된 메타데이터는 이 시험판 릴리스에서는 적용되지 않습니다. Visual Studio에 API 앱을 마켓플레이스에 게시하는 기능이 포함되어 있지 않기 때문입니다.

### Metadata/icons 폴더

갤러리에 표시할 아이콘을 제공할 수 있습니다. 
사용자 지정 아이콘을 제공하지 않는 경우 제네릭 API 앱 아이콘이 사용됩니다. 아이콘 파일은 PNG 형식이어야 하고 다음과 같은 이름 및 크기 규칙을 따라야 합니다.

<!--todo: also used in the workflow designer--> 

|파일 이름|크기
|:-----|:-----:
|small.png|40X40
|medium.png|90X90
|large.png|115X115
|hero.png|815X290
|wide.png|255X115

### Metadata/screenshots 폴더

갤러리에 표시할 최대 5개의 스크린샷을 제공할 수 있습니다. 이미지 파일은 PNG 형식(533x324)이어야 합니다.

### <a id="apidef"></a>Metadata/apiDefinition.swagger.json 파일

[Swagger 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md) 형식 파일을 사용하여 API 앱의 API 정의를 설명할 수 있습니다. 이렇게 하면 API 정의를 패키지에 정적으로 노출할 수 있습니다. 

<!--todo Explain why this is useful. Original text:
Static API definitions are required for workflow designers to understand the API App triggers and actions before provisioning.-->

<!-- todo: 이 값으로 바꿔야 할 항목이 있는지 확인하세요(deploymentTemplates 하위 폴더일 수 있음).
### resourceTemplate.delta.json 파일
API 앱을 배포하는 동안 실행되는 사용자 지정 Azure 리소스 관리자 템플릿을 지정할 수 있습니다. 이 델타 파일에 지정된 리소스는 기본적으로 API 앱에 대해 만들어지는 리소스에 추가됩니다. 예를 들어 API 앱에 SQL 데이터베이스 인스턴스가 필요한 경우 이 파일을 사용하여, API 앱 배포 시 데이터베이스가 자동으로 프로비전되고  연결 문자열이 구성 설정에 지정되도록 할 수 있습니다.
-->  

### Metadata/UIDefinition.json 파일

힌트 및 유효성 검사와 같은 UI 정보를 [Azure 마켓플레이스 형식](https://auxdocs.azurewebsites.net/ko-kr/documentation/articles/gallery-items)으로 제공할 수 있습니다.

### Metadata/deploymentTemplates 폴더

Visual Studio는 **Azure API 앱 SDK** 메뉴 항목 선택 시 이 폴더를 만들지만 이 시험판 릴리스에서는 이 폴더가 사용되지 않습니다.

## 다음 단계

이제 Web API 프로젝트를 API 앱으로 배포할 준비가 되었습니다. [API 앱 배포](app-service-dotnet-deploy-api-app.md) 자습서에 따라 이 작업을 수행할 수 있습니다.

자세한 내용은 [API 앱 정의](app-service-api-apps-why-best-platform.md)를 참조하세요.

<!--HONumber=49-->
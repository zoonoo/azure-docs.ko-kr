<properties 
	pageTitle="Azure 앱 서비스 API 앱 만들기" 
	description="이 문서에서는 Visual Studio 2013을 사용하여 Azure 앱 서비스에서 API 앱을 만드는 방법을 보여 줍니다." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Azure 앱 서비스에서 API 앱 만들기

## 개요

이 자습서는 3부로 구성된 자습서의 첫 번째 자습서로서 Azure 앱 서비스에서 API 앱을 시작합니다.

1. 이 자습서에서는 새 API 앱을 만들고 Azure 구독에 배포할 준비를 합니다. 
* [API 앱 배포](app-service-dotnet-create-api-app.md)에서는 API 앱을 Azure 구독에 배포합니다.
* [API 앱 디버그](app-service-dotnet-remotely-debug-api-app.md)에서는 Visual Studio를 사용하여 Azure에서 코드가 실행되는 동안 코드를 원격으로 디버그합니다.

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## 첫 번째 API 앱 만들기 ##

Visual Studio 2013을 열고 **파일 > 새 프로젝트**를 선택합니다. **ASP.NET 웹 응용 프로그램** 템플릿을 선택합니다.  프로젝트의 이름을 *ContactsList*로 지정한 후 **확인**을 클릭합니다.

![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

**Azure API 앱** 프로젝트 템플릿을 선택한 후 **확인**을 클릭합니다.

![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

Web API 프로젝트에서 **Models** 폴더를 마우스 오른쪽 단추로 클릭한 후 상황에 맞는 메뉴에서 **추가 > 클래스**를 선택합니다. 

![](./media/app-service-dotnet-create-api-app/03-add-new-class-v3.png) 

새 파일 이름을 *Contact.cs*로 지정한 후 **추가**를 클릭합니다. 

![](./media/app-service-dotnet-create-api-app/0301-add-new-class-dialog-v3.png) 

파일의 전체 내용을 다음 코드로 바꿉니다. 

	namespace ContactsList.Models
	{
		public class Contact
		{
			public int Id { get; set; }
			public string Name { get; set; }
			public string EmailAddress { get; set; }
		}
	}

**Controllers** 폴더를 마우스 오른쪽 단추로 클릭한 후 상황에 맞는 메뉴에서 **추가 > 컨트롤러**를 선택합니다. 

![](./media/app-service-dotnet-create-api-app/05-new-controller-v3.png)

**스캐폴드 추가** 대화 상자에서 **Web API 2 컨트롤러 - 비어 있음** 옵션을 선택하고 **추가**를 클릭합니다. 

![](./media/app-service-dotnet-create-api-app/06-new-controller-dialog-v3.png)

컨트롤러 이름을 **ContactsController**로 지정하고 **추가**를 클릭합니다. 

![](./media/app-service-dotnet-create-api-app/07-new-controller-name-v2.png)

이 파일의 코드를 아래 코드로 바꿉니다. 

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
                	new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
	            };
	        }
	    }
	}

API 앱 프로젝트가 자동 [Swagger](http://swagger.io/ "Official Swagger information") 메타데이터 생성 및 API 테스트 페이지를 사용하도록 설정됩니다. 기본적으로 API 테스트 페이지는 사용하지 않도록 설정됩니다. API 테스트 페이지를 사용하도록 설정하려면 *App_Start/SwaggerConfig.cs* 파일을 엽니다. **EnableSwaggerUI**를 검색합니다.

![](./media/app-service-dotnet-create-api-app/12-enable-swagger-ui-with-box.png)

다음 코드 줄의 주석 처리를 제거합니다.

        })
    .EnableSwaggerUi(c =>
        {

완료하면 Visual Studio 2013에서 파일이 다음과 같이 표시됩니다.

![](./media/app-service-dotnet-create-api-app/13-enable-swagger-ui-with-box.png)

API 테스트 페이지를 보려면 앱을 로컬로 실행하고 `/swagger`로 이동합니다. 

![](./media/app-service-dotnet-create-api-app/14-swagger-ui.png)

**Try it out** 단추를 클릭하면 API가 작동하고 예상 결과를 반환하는 것을 볼 수 있습니다. 

![](./media/app-service-dotnet-create-api-app/15-swagger-ui-post-test.png)

## 다음 단계

이제 API 앱을 배포할 준비가 되었으며 [API 앱 배포](app-service-dotnet-deploy-api-app.md) 자습서에 따라 API 앱을 배포할 수 있습니다.

자세한 내용은 [API 앱 정의](app-service-api-apps-why-best-platform.md)를 참조하세요.

<!--HONumber=49-->
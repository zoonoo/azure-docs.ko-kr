<properties 
	pageTitle="Azure 앱 서비스에서 API 앱 배포" 
	description="Azure 구독에 API 앱 프로젝트를 배포하는 방법을 알아봅니다." 
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
	ms.date="10/08/2015" 
	ms.author="tdykstra"/>

# Azure 앱 서비스에서 API 앱 배포 

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## 개요

이 자습서에서는 [이전 자습서](app-service-dotnet-create-api-app.md)에서 만든 Web API 프로젝트를 새로운 [API 앱](app-service-api-apps-why-best-platform.md)에 배포합니다. Visual Studio를 사용하여 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)에서 API 앱 리소스를 만들고 Web API 코드를 Azure API 앱에 배포합니다.

### 다른 배포 옵션

API 앱을 배포할 수 있는 다른 여러 방법이 있습니다. API 앱은 웹 서비스를 호스트하기 위한 추가 기능이 있는 [웹앱](../app-service-web/app-service-web-overview.md)으로, [웹앱에 사용할 수 있는 모든 배포 방법](../app-service-web/web-sites-deploy.md)은 API 앱에도 사용할 수 있습니다. API 앱을 호스트하는 웹앱을 Azure Preview 포털에서는 API 앱 호스트라고 하며, API 앱 호스트 포털 블레이드를 사용하여 배포를 구성할 수 있습니다. API 앱 호스트 블레이드에 대한 자세한 내용은 [API 앱 관리](app-service-api-manage-in-portal.md)를 참조하세요.

API 앱이 웹앱을 기반으로 한다는 사실은 ASP.NET 이외의 플랫폼용으로 작성된 코드를 API 앱에 배포할 수 있음을 의미하기도 합니다. Git를 사용하여 Node.js 코드를 API 앱에 배포하는 예제는 [Azure 앱 서비스에서 Node.js API 앱 만들기](app-service-api-nodejs-api-app.md)를 참조하세요.
 
## <a id="provision"></a>Azure에서 API 앱 만들기 

이 섹션에서는 Visual Studio **웹 게시** 마법사를 사용하여 Azure에서 API 앱을 만듭니다. API 앱의 이름을 입력하라는 지침이 있으면 *ContactsList*를 입력합니다.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>새 Azure API 앱에 코드 배포

동일한 **웹 게시** 마법사를 사용하여 새 API 앱에 코드를 배포합니다.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Azure API 앱 호출 

이전 자습서에서 Swagger UI를 사용할 수 있도록 했기 때문에 API 앱이 Azure에서 실행되고 있는지 확인하는 데 Swagger UI를 사용할 수 있습니다.

1. [Azure Preview 포털](https://portal.azure.com)에서 배포한 API 앱에 대한 **API 앱** 블레이드로 이동합니다.

2. API 앱의 URL을 클릭합니다.

	![URL을 클릭합니다.](./media/app-service-dotnet-deploy-api-app/clickurl.png)

	"API 앱을 성공적으로 만들었습니다." 페이지가 나타납니다.

3. 브라우저 주소 표시줄에서 URL의 끝에 "swagger/"를 추가합니다.

4. 표시되는 Swagger 페이지에서 **연락처 > 가져오기 > 사용해 보기**를 클릭합니다.

	![사용해보기](./media/app-service-dotnet-deploy-api-app/swaggerui.png)

## 포털에서 API 정의 보기

1. [Azure Preview 포털](https://portal.azure.com)에서 배포한 API 앱에 대한 **API 앱** 블레이드로 다시 돌아갑니다.

4. **API 정의**를 클릭합니다.
 
	앱의 **API 정의** 블레이드에 앱을 만들 때 정의한 API 작업 목록이 표시됩니다.

	![API 정의](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

다음으로 API 정의를 변경하고 포털에 반영된 변경 내용을 확인합니다.

5. Visual Studio의 프로젝트로 돌아가서 다음 코드를 **ContactsController.cs** 파일에 추가합니다.   

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	이 코드는 새 `Contact` 인스턴스를 API에 게시하는 데 사용할 수 있는 **Post** 메서드를 추가합니다.

	이제 연락처 클래스의 코드는 다음 예제와 같습니다.

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
		
		    [HttpPost]
		    public HttpResponseMessage Post([FromBody] Contact contact)
		    {
		        // todo: save the contact somewhere
		        return Request.CreateResponse(HttpStatusCode.Created);
		    }
		}

7. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

9. **미리 보기** 탭을 클릭합니다.

10. **미리 보기 시작**을 클릭하여 Azure에 복사될 파일을 볼 수 있습니다.

	![웹 게시 대화 상자](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. **게시**를 클릭합니다.

6. 처음으로 게시를 수행한 것 처럼 게이트웨이를 다시 시작합니다.

12. 게시 프로세스가 완료되면 포털로 돌아가서 **API 정의** 블레이드를 닫았다가 다시 엽니다. 방금 만들어 Azure 구독에 직접 배포한 새 API 끝점을 볼 수 있습니다.

	![API 정의](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## 다음 단계

Visual Studio의 직접 배포 기능을 통해 API가 올바르게 작동하는지 테스트하는 작업을 얼마나 쉽게 수행할 수 있는지 살펴봤습니다. [다음 자습서](../app-service-dotnet-remotely-debug-api-app.md)에서는 Azure에서 API 앱이 실행되는 동안 API 앱을 디버그하는 방법을 확인할 수 있습니다.

API 앱은 API 호스팅을 위한 추가 기능이 있는 웹앱입니다. 즉 웹 응용 프로그램을 사용하는 모든 배포 방법을 사용할 수 있습니다. 웹앱의 배포 옵션에 대한 자세한 내용은 [Azure 앱 서비스에서 웹앱 배포](../app-service-web/web-sites-deploy.md)를 참조하세요.

API 앱 기능에 대한 자세한 내용은 [API 앱 정의](app-service-api-apps-why-best-platform.md)를 참조하세요.

<!---HONumber=AcomDC_1203_2015-->
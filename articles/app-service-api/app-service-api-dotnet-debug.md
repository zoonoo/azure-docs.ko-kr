<properties 
	pageTitle="Azure 앱 서비스에서 API 앱 디버그" 
	description="Visual Studio를 사용하여 Azure 앱 서비스에서 실행되는 동안 API 앱을 디버그하는 방법을 알아봅니다." 
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
	ms.date="07/08/2015" 
	ms.author="bradyg;tarcher"/>

# Azure 앱 서비스에서 API 앱 디버그

## 개요

이 자습서에서는 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)의 [API 앱](app-service-api-apps-why-best-platform.md)에서 실행되도록 구성된 ASP.NET Web API 코드를 디버그하는 방법을 배우게 됩니다. (Azure에서 실행하는 동안) 로컬 및 원격으로 모두 디버깅합니다. 이 자습서는 이 시리즈의 이전 자습서에서 [만들고](app-service-dotnet-create-api-app.md) [배포](app-service-dotnet-deploy-api-app.md)한 API 앱을 사용합니다.

## API 앱을 원격으로 디버깅 

다음 단계에서는 Swagger UI를 테스트 클라이언트로 사용하여 API 앱을 클라우드에서 실행하는 동안 해당 앱을 디버깅할 수 있습니다.

1. Visual Studio **솔루션 탐색기**에서 [이전 자습서에서 배포된](app-service-dotnet-deploy-api-app.md) 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택하십시오.

	![프로젝트 게시](./media/app-service-api-dotnet-debug/rd-publish.png)

2. **웹 게시** 대화 상자에서 설정 탭을 선택하고 **디버그** 빌드 구성을 선택했는지 확인합니다. 완료되면 Azure 구독에 모든 변경 내용 푸시하기 위해 **게시**를 클릭합니다.

	![프로젝트 게시](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

3. 브라우저 창은 API 앱이 성공적으로 만들어졌는지 확인하는 메시지를 열고 표시해야 합니다.

4. 브라우저 주소 표시줄에서 URL의 끝에 /swagger를 추가하고 &lt;Enter>를 누릅니다. Swagger UI 클라이언트를 표시합니다.

	![Swagger UI](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. Visual Studio로 돌아가고, **보기** 메뉴에서 **서버 탐색기**를 선택합니다.

6. **서버 탐색기**에서 **Azure > 앱 서비스** 노드를 확장합니다.

7. API 앱을 배포할 때 만든 리소스 그룹을 찾습니다.

8. 리소스 그룹에서 API 앱 노드를 마우스 오른쪽 단추로 클릭하고 **디버거 연결**을 선택합니다.

	![디버거 연결](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	원격 디버거가 연결하려고 합니다. 연결을 설정하기 위해 **디버거 연결**을 다시 클릭해야 하는 경우도 있으므로 연결에 실패할 경우 다시 시도하세요.

	![디버거 연결](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. 연결이 설정된 후 API 앱 프로젝트에서 **ContactsController.cs** 파일을 열고 `Get` 및 `Post` 메서드에 중단점을 추가합니다. 처음에는 이러한 메서드가 활성 상태로 표시되지 않을 수 있지만 원격 디버거가 연결되면 디버그할 준비가 된 것입니다.

	![컨트롤러에 중단점 적용](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. 브라우저 세션으로 돌아가고, *연락처* 개체에 대한 스키마를 나타내는 **Get** 동사를 클릭한 다음 **사용해보십시오**를 클릭합니다. 컨트롤러의 **Get** 메서드에서 중단점을 설정하면 Visual Studio는 프로그램 실행을 중지하고 컨트롤러의 논리를 디버깅할 수 있습니다.

	![사용해보기](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## API 앱을 로컬로 디버깅 

예를 들면, 테스트/디버그 주기 동안 잠재적으로 느린 왕복을 방지하기 위해 API 앱을 로컬로 디버깅하려는 경우가 있을 수 있습니다. 다음 단계는 Swagger UI를 테스트 클라이언트로 사용하여 API 앱을 로컬로 디버깅하는 방법을 보여줍니다.

1. Visual Studio에서 API 앱 프로젝트의 *web.config* 파일을 엽니다. 
 
2. 브라우저에서 [Azure Preview 포털](https://portal.azure.com)로 이동합니다.

3. 사이드바에서 **찾아보기** 단추를 클릭하고 **API 앱**을 선택합니다.

	![Azure 포털의 옵션 찾아보기](./media/app-service-api-dotnet-debug/ld-browse.png)

4. 구독의 API 앱 목록에서 만든 API 앱을 선택합니다.

	![API 앱 목록](./media/app-service-api-dotnet-debug/ld-api-app-list.png)

5. API 앱 블레이드에서 **API 앱 호스트**를 클릭합니다.

	![API 앱 호스트](./media/app-service-api-dotnet-debug/ld-api-app-blade-api-app-host.png)

6. API 앱 호스트 블레이드에서 **모든 설정**을 클릭합니다.

	![API 앱 호스트 모든 설정](./media/app-service-api-dotnet-debug/ld-api-app-host-all-settings.png)

7. **설정** 블레이드에서 **응용 프로그램 설정**을 클릭합니다.

	![API 앱 호스트 응용 프로그램 설정](./media/app-service-api-dotnet-debug/ld-application-settings.png)

8. **웹 앱 설정** 블레이드에서 **앱 설정** 섹션으로 스크롤합니다.

	![로컬 디버깅용 API 앱 호스트 응용 프로그램 설정](./media/app-service-api-dotnet-debug/ld-app-settings-for-local-debugging.png)

9. **앱 설정**에서 다음 값을 각각 찾고 그것을 *web.config* 파일의 **appSettings** 섹션에 추가합니다.
	- **EMA_MicroserviceId**
	- **EMA_Secret**
	- **EMA_RuntimeUrl**

	작업을 마친 경우 *web.config*의 **appSettings** 섹션은 다음 스크린샷과 유사해야 합니다.

	![로컬 디버깅용 API 앱 호스트 응용 프로그램 설정](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**참고:** 이 섹션에서 *web.config* 파일에 추가한 *EMA_* 값은 중요한 인증 정보를 포함합니다. 따라서 이러한 비밀이 다른 사람에게 보여지기 때문에 (*github*와 같은) 공개 소스 제어 매체에 이 파일을 커밋할 때 주의하는 것이 좋습니다 자세한 내용은 [ASP.NET 및 Azure 앱 서비스에 암호 및 기타 중요한 데이터 배포를 위한 모범 사례](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 기사를 참조하세요.

10. (`Get`과 `Post` 메서드에서) API 앱의 컨트롤러 코드에 하나 이상의 중단점을 배치합니다.

	![중단점 설정](./media/app-service-api-dotnet-debug/ld-breakpoints.png)

11. F5 키를 눌러 Visual Studio 디버깅 세션을 시작합니다.
 
13.  API 앱의 액세스 수준이 **공용(익명)**으로 설정된 경우 Swagger UI 페이지를 사용하여 테스트할 수 있습니다.

	* 브라우저에서 페이지를 로드하는 경우 오류 메시지가 나타납니다. 브라우저의 주소 표시줄에서 URL의 끝에 */swagger*를 추가하고 Enter 키를 누릅니다.

	* Swagger UI가 로드되면, 브라우저 창에서 연락처 개체에 대한 스키마를 나타내는 **Get** 동사를 클릭한 다음 **사용해보십시오**를 클릭합니다.

		Visual Studio는 이전에 설정한 중단점에서 프로그램 실행을 중지하고 컨트롤러의 논리를 디버깅할 수 있습니다.

		![사용해보기](./media/app-service-api-dotnet-debug/ld-try-it-out.png)

14.	API 앱의 액세스 수준을 **공용(인증)**으로 설정한 경우 다음과 같이 [API 앱 보호](app-service-api-dotnet-add-authentication.md#use-postman-to-send-a-post-request)에 표시된 절차에 따라 인증을 받고 브라우저 도구를 사용해야 합니다.

	* 게이트웨이 로그인 URL로 이동한 다음 자격 증명을 입력하여 로그인합니다.
	* x-zumo-auth 쿠키에서 Zumo 토큰 값을 가져옵니다.
	* 사용자의 요청에 x-zumo-auth 헤더를 추가하고 해당 값을 x-zumo-auth 쿠키 값으로 설정합니다.
	* 요청을 제출합니다.

	**참고:** 로컬로 실행하는 경우 Azure는 인증된 사용자만 해당 메서드를 실행할 수 있도록 API 앱에 대한 액세스를 제어할 수 없습니다. Azure에서 실행될 경우 API 앱에 대한 모든 트래픽이 게이트웨이를 통해 라우팅되고 게이트웨이는 인증되지 않은 요청을 전달하지 않습니다. 로컬로 실행하는 경우에는 리디렉션이 수행되지 않습니다. 즉, 인증되지 않은 요청이 API 앱에 액세스할 수 있습니다. 위에서 설명한 것처럼 인증이 이루어지므로 로그온한 사용자에 대한 정보를 검색하는 코드와 같은 인증 관련 코드를 API 앱에서 성공적으로 실행할 수 있다는 장점이 있습니다. 게이트웨이가 API 앱에서 인증을 사용하는 방법에 대한 자세한 내용은 [API 앱 및 모바일 앱 인증](../app-service/app-service-authentication-overview.md#azure-app-service-gateway)을 참조하세요.

## 다음 단계

API 앱용 원격 디버깅을 사용하면 Azure 앱 서비스에서 코드가 어떻게 실행되고 있는지 쉽게 확인할 수 있습니다. Visual Studio IDE에서 바로 Azure API 앱에 대한 다양한 진단 및 디버깅 데이터를 사용할 수 있습니다.

앱 서비스 API 앱은 웹 서비스를 호스트하기 위한 추가 기능이 있는 앱 서비스 웹앱이므로 웹앱에 사용하는 것과 동일한 디버깅 및 문제 해결 도구를 API 앱에 사용할 수 있습니다. 자세한 내용은 [Visual Studio를 사용하여 Azure 앱 서비스에서 웹앱 문제 해결](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)을 참조하세요.

이 시리즈에서 만든 API 앱은 누구든지 공개적으로 호출할 수 있습니다. 인증된 사용자만 호출할 수 있게 API 앱을 보호하는 방법에 대한 자세한 내용은 [Azure 앱 서비스에서 API 앱 및 모바일 앱 인증](../app-service/app-service-authentication-overview.md)을 참조하세요.
 

<!---HONumber=July15_HO4-->
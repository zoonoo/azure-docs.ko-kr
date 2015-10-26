<properties 
	pageTitle="Azure API 앱 보호" 
	description="Visual Studio를 사용하여 Azure API 앱을 보호하는 방법에 대해 알아봅니다." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/05/2015" 
	ms.author="tdykstra"/>

# API 앱 보호: Azure Active Directory 또는 소셜 공급자 인증 추가

## 개요

이 자습서에서는 인증된 사용자만 API 앱에 액세스할 수 있도록 API 앱을 보호하는 방법을 보여 줍니다. 또한 자습서는 로그온한 사용자에 대 한 정보를 검색하는 ASP.NET API 앱에서 사용할 수 있는 코드를 보여줍니다.

다음 단계를 수행합니다.

- API 앱을 호출하여 작동하는지 확인
- API 앱에 인증 규칙 적용
- API 앱을 다시 호출하여 인증되지 않은 요청을 거부하는지 확인
- 구성된 공급자에 로그인
- API 앱을 다시 호출하여 인증된 액세스가 작동하는지 확인
- 로그온한 사용자에 대한 클레임을 검색하는 코드를 작성하고 테스트합니다.

Azure 앱 서비스의 인증에 대한 자세한 내용은 [API 앱 및 모바일 앱 인증](../app-service/app-service-authentication-overview.md)을 참조하세요.

## 필수 조건

이 자습서에서는 [API 앱 만들기](app-service-dotnet-create-api-app.md)에서 만들고 [API 앱 배포](app-service-dotnet-deploy-api-app.md)에서 배포한 API 앱을 사용합니다.

## 브라우저를 사용하여 API 앱 호출 

API 앱에 공개적으로 액세스할 수 있는지를 확인하는 가장 간단한 방법은 브라우저에서 API 앱을 호출하는 것입니다.

1. 브라우저에서 [Azure Preview 포털]로 이동합니다.

3. 홈페이지에서 **찾아보기 > API 앱**을 클릭한 후 보호하려는 API 앱의 이름을 클릭합니다.

	![찾아보기](./media/app-service-api-dotnet-add-authentication/browse.png)

	![API 앱 선택](./media/app-service-api-dotnet-add-authentication/select.png)

3. **API 앱** 블레이드에서 **URL**을 클릭하여 API 앱을 호출하는 브라우저 창을 엽니다.

	![API 앱 블레이드](./media/app-service-api-dotnet-add-authentication/chooseapiappurl.png)

2. 브라우저 주소 표시줄의 URL에 `/api/contacts/get/`를 추가합니다.

	예를 들어 API 앱의 URL이 다음과 같은 경우

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/

	전체 URL은 다음과 같습니다.

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/api/contacts/get/

	다양한 브라우저가 API 호출을 처리하는 방식은 각기 다릅니다. 이미지는 Chrome 브라우저에서의 성공적인 호출을 보여 줍니다.

	![Chrome Get 응답](./media/app-service-api-dotnet-add-authentication/chromeget.png)

2. 사용한 URL을 저장합니다. 자습서의 뒷부분에서 이 URL을 다시 사용합니다.

## API 앱 보호

API 앱을 배포할 때 리소스 그룹에 배포했습니다. 동일한 리소스 그룹에 웹앱과 다른 API 앱을 추가할 수 있으며 리소스 그룹 내의 각 API 앱의 접근성 설정은 다음의 세 가지 중 하나일 수 있습니다. <!--todo: diagram showing different accessibility settings-->

- **공용(익명)** - 모든 사용자가 로그인하지 않고 리소스 그룹 외부에서 API 앱을 호출할 수 있습니다.
- **공용(인증됨)** - 인증된 사용자만 리소스 그룹 외부에서 API 앱을 호출할 수 있습니다.
- **내부** - 동일한 리소스 그룹의 다른 API 앱만 API 앱을 호출할 수 있습니다. 웹앱에서의 호출은 웹앱이 동일한 리소스 그룹에 있는 경우에도 외부로 간주됩니다.

Visual Studio에서 리소스 그룹을 만들 때 *게이트웨이*도 만들어집니다. 게이트웨이는 대상이 리소스 그룹의 API 앱인 모든 요청을 처리하는 특수 웹앱입니다.

[Azure Preview 포털]에서 리소스 그룹의 블레이드로 이동하면 API 앱 및 다이어그램의 게이트웨이를 볼 수 있습니다.

![리소스 그룹 다이어그램](./media/app-service-api-dotnet-add-authentication/rgdiagram.png)

### <a id="apiapp"></a>인증을 요구하도록 API 앱 구성

API 앱이 인증된 요청만 수락하도록 구성하기 위해 해당 접근성을 **공용(인증됨)**으로 설정하고 Azure Active Directory, Google 또는 Facebook과 같은 공급자의 인증을 요구하도록 게이트웨이를 구성합니다.

[AZURE.INCLUDE [app-service-api-config-auth](../../includes/app-service-api-config-auth.md)]

이제 API 앱이 인증되지 않은 액세스로부터 보호됩니다. 그런 다음, 게이트웨이를 구성하여 사용할 인증 공급자를 지정해야 합니다.

### <a id="gateway"></a>인증 공급자를 사용하도록 게이트웨이 구성

[AZURE.INCLUDE [app-service-api-gateway-config-auth](../../includes/app-service-api-gateway-config-auth.md)]

## 인증이 작동하는지 확인

**참고:** 다음 단계를 수행할 때 로그인하는 데 문제가 있는 경우 비공개 또는 시크릿 창으로 여세요.
 
1. 브라우저 창을 열고 앞에서 한 것처럼 API 앱의 `Get` 메서드를 호출하는 URL을 주소 표시줄에 입력합니다.

	이번에는 API 앱에 액세스하려는 시도로 인해 오류 메시지가 표시됩니다.

	![Chrome Get 응답 실패](./media/app-service-api-dotnet-add-authentication/chromegetfail.png)

2. 브라우저에서 다음과 같이 로그인 URL로 이동합니다. URL은 다음 패턴을 따릅니다.

    	http://[gatewayurl]/login/[providername]

	[Azure Preview 포털]의 **게이트웨이** 블레이드에서 게이트웨이 URL을 얻을 수 있습니다. **게이트웨이** 블레이드에 액세스하려면 **리소스 그룹** 블레이드에 표시된 다이어그램에서 게이트웨이를 클릭합니다.

	![게이트웨이 URL](./media/app-service-api-dotnet-add-authentication/gatewayurl.png)

	[Providername]은 다음 값 중 하나여야 합니다.
	
	* "microsoftaccount"
	* "facebook"
	* "twitter"
	* "google"
	* "aad"

	다음은 Azure Active Directory에 대한 샘플 로그인 URL입니다.

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/login/aad/

	이전 URL과 달리 이 URL에는 API 앱 이름이 포함되지 않습니다. 게이트웨이는 사용자를 인증하는 것이지 API 앱을 인증하는 것이 아닙니다. 게이트웨이는 리소스 그룹의 모든 API 앱에 대한 인증을 처리합니다.

3. 브라우저에 로그인 페이지가 표시되면 자격 증명을 입력합니다.
 
	Azure Active Directory 로그인을 구성한 경우 [Azure 포털]의 Azure Active Directory 탭에서 만든 응용 프로그램의 **사용자** 탭에 나열된 사용자 중 하나를 사용합니다(예: admin@contoso.onmicrosoft.com).

	![AAD 사용자](./media/app-service-api-dotnet-add-authentication/aadusers.png)

	![로그인 페이지](./media/app-service-api-dotnet-add-authentication/ffsignin.png)

4. "로그인 완료" 메시지가 표시되면 API 앱의 Get 메서드 URL을 다시 입력합니다.

	이번에는 사용자가 인증되었으므로 호출에 성공합니다. 게이트웨이가 사용자를 인증된 사용자로 인식하고 요청을 API 앱에 전달합니다.

	![로그인 완료](./media/app-service-api-dotnet-add-authentication/logincomplete.png)

	![Chrome Get 응답](./media/app-service-api-dotnet-add-authentication/chromeget.png)

	Swagger UI를 사용하도록 설정한 경우 지금 Swagger UI 페이지로 이동할 수 있습니다. 그러나 페이지 오른쪽 맨 아래에 표시된 빨간색 **오류** 아이콘을 클릭하면 Swagger JSON 파일에 액세스할 수 없다는 메시지가 표시됩니다. Swagger가 Zumo 토큰을 포함하지 않고 AJAX를 호출하여 JSON 파일을 검색하려고 하기 때문입니다. 이 경우에도 Swagger UI 페이지는 잘 작동됩니다.

## Postman을 사용하여 Post 요청 보내기

게이트웨이에 로그인하면 게이트웨이가 인증 토큰을 다시 보냅니다. 이 토큰은 게이트웨이를 통과하는 외부 원본의 모든 요청에 포함되어야 합니다. 브라우저로 API에 액세스하는 경우 브라우저는 일반적으로 토큰을 쿠키에 저장하고 API에 대한 모든 후속 호출과 함께 토큰을 보냅니다.

따라서 백그라운드에서 수행되고 있는 작업을 볼 수 있으며, 자습서의 이 섹션에서는 브라우저 도구를 사용하여 Post 요청을 만들어 제출하고 쿠키에서 인증 토큰을 가져와 HTTP 헤더에 포함합니다. 이 섹션은 선택 사항입니다. 이전 섹션에서 API 앱이 인증된 액세스만 수락하는 것을 이미 확인했습니다.

이러한 지침에서는 Chrome 브라우저에서 Postman 도구를 사용하는 방법을 보여 주지만 모든 REST 클라이언트 도구 및 브라우저 개발자 도구를 사용해서도 동일한 작업을 수행할 수 있습니다.

1. Chrome 브라우저 창에서 이전 섹션에 표시된 단계를 거쳐 인증한 후 개발자 도구(F12 키)를 엽니다.

	![리소스 탭으로 이동](./media/app-service-api-dotnet-add-authentication/resources.png)

3. Chrome 개발자 도구의 **Resources** 탭에서 게이트웨이에 대한 쿠키를 찾아 **x-zumo-auth** 쿠키 값을 세 번 클릭하여 모두 선택합니다.

	**참고:** 쿠키의 값을 모두 가져와야 합니다. 두 번 클릭하면 첫 번째 부분만 가져올 수 있습니다.

5. **x-zumo-auth** 쿠키의 **Value**를 마우스 오른쪽 단추로 클릭한 후 **Copy**를 클릭합니다.

	![인증 토큰 복사](./media/app-service-api-dotnet-add-authentication/copyzumotoken.png)

4. 아직 Chrome 브라우저에 Postman 확장을 설치하지 않은 경우 설치합니다.

6. Postman 확장을 엽니다.

7. Request URL 필드에 이전에 사용한 API 앱의 Get 메서드 URL을 입력하되 끝에서 `get/`를 생략합니다.
 
		http://[apiappurl]/api/contacts
    
8. **Headers**를 클릭하고 *x-zumo-auth* 헤더를 추가합니다. 클립보드의 토큰 값을 **Value** 필드에 붙여넣습니다.

9. 값을 *application/json*으로 하여 *Content-Type* 헤더를 추가합니다.

10. **form-data**를 클릭하고 다음 값으로 *contact* 키를 추가합니다.

		{   "Id": 0,   "Name": "Li Yan",   "EmailAddress": "yan@contoso.com" }

11. Send를 클릭합니다.

	API 앱에서 *201 Created* 응답을 반환합니다.

	![헤더 및 본문 추가](./media/app-service-api-dotnet-add-authentication/addcontact.png)

12. 이 요청이 인증 토큰 없이는 작동하지 않는지 확인하려면 인증 헤더를 삭제하고 Send를 다시 클릭합니다.

	*403 Forbidden* 응답이 표시됩니다.

	![403 Forbidden 응답](./media/app-service-api-dotnet-add-authentication/403forbidden.png)

## 로그온한 사용자에 대한 정보 가져오기

이 섹션에서는 ContactsList API 앱의 코드를 변경하여 로그온한 사용자의 이름 및 전자 메일 주소를 검색하고 반환합니다.

1. Visual Studio에서는 [API 앱 배포](app-service-dotnet-deploy-api-app.md)에서 배포되고 이 자습서를 위해 호출된 API 앱 프로젝트를 엽니다.

3. Apiapp.json 파일을 열고 Azure Active Directory 인증을 사용하는 API 앱임을 나타내는 줄을 추가합니다.

		"authentication": [{"type": "aad"}]

	최종 apiapp.json 파일은 다음 예와 유사합니다:

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsList",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    },
		    "authentication": [{"type": "aad"}]
		}

	이 자습서에서는 예로 Azure Active Directory를 사용합니다. 다른 공급자에 대해 적절한 식별자를 사용하게 됩니다. 다음은 올바른 공급자 값입니다:

	* "aad"
	* "microsoftaccount"
	* "google"
	* "twitter"
	* "facebook". 

3. *ContactsController.cs* 파일의 맨 위에 `using` 문을 추가합니다.

		using Microsoft.Azure.AppService.ApiApps.Service;

2. `Get` 메서드의 코드를 다음 코드로 바꿉니다.

		var runtime = Runtime.FromAppSettings(Request);
		var user = runtime.CurrentUser;
		TokenResult token = await user.GetRawTokenAsync("aad");
		var name = (string)token.Claims["name"];
		var email = (string)token.Claims["http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"];
		return new Contact[]
		{
		    new Contact { Id = 1, EmailAddress = email, Name = name }
		};

	코드는 세 샘플 연락처 대신 로그온한 사용자에 대한 연락처 정보를 반환합니다.

	샘플 코드는 Azure Active Directory를 사용합니다. 다른 공급자에 대해 이전 단계에서와 같이 적절한 토큰 이름 및 클레임 식별자를 사용하게 됩니다.

	사용할 수 있는 Azure Active Directory 클레임에 대한 정보는 [지원 토큰 및 클레임 유형](https://msdn.microsoft.com/library/dn195587.aspx)을 참조하세요.

3. `Microsoft.Azure.AppService.ApiApps.Service`에 using 문을 추가합니다.

		using Microsoft.Azure.AppService.ApiApps.Service;

3. 프로젝트를 다시 배포합니다.

	Visual Studio에서는 [배포](app-service-dotnet-deploy-api-app.md) 자습서를 따르는 동안 프로젝트를 배포할 때 설정을 기억합니다. 프로젝트를 마우스 오른쪽 단추로 클릭하여, **게시**를 클릭하고 **웹 게시** 대화 상자의 **게시**를 클릭하세요.

6. 보호된 API 앱으로 Get 요청을 보내기에 앞서 수행한 절차를 따릅니다.

	응답 메시지는 로그인한 ID의 이름과 ID를 보여줍니다.

	![로그온한 사용자의 응답 메시지](./media/app-service-api-dotnet-add-authentication/chromegetuserinfo.png)

## 다음 단계

Azure Active Directory 또는 소셜 공급자 인증을 요구하여 Azure API 앱을 보호하는 방법을 살펴봤습니다. 자세한 내용은 [API 앱 및 모바일 앱 인증](../app-service/app-service-authentication-overview.md)을 참조하세요.

[Azure 포털]: https://manage.windowsazure.com/
[Azure Preview 포털]: https://portal.azure.com/

<!---HONumber=Oct15_HO3-->
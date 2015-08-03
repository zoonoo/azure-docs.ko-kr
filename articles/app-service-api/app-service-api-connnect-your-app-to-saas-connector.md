<properties 
	pageTitle="SaaS 커넥터 API 앱 배포 및 구성" 
	description="Azure 마켓플레이스에서 Azure 구독으로 설치하는 SaaS 커넥터를 구성하는 방법을 알아봅니다." 
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
	ms.date="07/08/2015" 
	ms.author="tdykstra"/>

# Azure 앱 서비스에서 SaaS 커넥터 API 앱을 배포 및 구성

## 개요

이 자습서에서는 [Azure 앱 서비스](/documentation/services/app-service/)에서 [SaaS(Software-as-a-Service) 커넥터](../app-service-logic-what-are-bizTalk-api-apps.md)를 설치 및 구성하고 모바일 앱 등에서의 프로그래밍 방식 호출을 테스트하는 방법을 보여 줍니다. SaaS 커넥터는 Office 365, Salesforce, Facebook 및 Dropbox와 같은 SaaS 플랫폼과의 상호 작용을 단순화하는 [API 앱](app-service-api-apps-why-best-platform.md)입니다. 미리 패키지된 커넥터를 사용하는 대신 사용자 지정 .NET API 앱을 만들려는 경우 [ASP.NET API 앱에서 SaaS 플랫폼에 연결](app-service-api-dotnet-connect-to-saas.md)을 참조하세요.

예를 들어 Dropbox 계정으로 파일을 읽고 쓰기 위해 HTTP 요청을 코딩하려는 경우 Dropbox에서 직접 작업하기 위한 인증 프로세스는 복잡합니다. 비즈니스 관련 코드 작성에 집중할 수 있도록 Dropbox 커넥터가 인증의 복잡성을 처리합니다.

> [AZURE.NOTE]논리 앱에서 SaaS 커넥터를 사용하려는 경우에는 지침을 요구하지 않습니다. 논리 앱 내에서 SaaS 커넥터를 사용하는 방법에 대한 자세한 내용은 [새 논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하십시오.
 
이 자습서에서는 DropBox 커넥터를 예로 사용하여 다음 단계를 안내합니다.

* Azure 구독의 [리소스 그룹](../resource-group-overview.md)에 Dropbox 커넥터를 설치합니다. 
* Dropbox 서비스에 연결할 수 있도록 Dropbox 커넥터를 구성합니다. 이 단계를 완료하려면 Dropbox 계정이 있어야 합니다.
* 인증된 사용자만 리소스 그룹에 포함된 API 앱에 액세스할 수 있도록 리소스 그룹을 구성합니다.
* 테스트를 통해 사용자 인증과 Dropbox 인증이 둘 다 작동하는지 확인합니다.

앱 서비스의 인증에 대한 자세한 내용은 [API 앱 및 모바일 앱 인증](../app-service/app-service-authentication-overview.md)을 참조하세요.

## Dropbox 커넥터 설치

1. [Azure Preview 포털] 홈페이지로 이동하여 **마켓플레이스**를 클릭합니다.

	![Azure Preview 포털의 마켓플레이스](./media/app-service-api-connnect-your-app-to-saas-connector/marketplace.png)

2. Dropbox를 검색한 다음 **Dropbox 커넥터** 아이콘을 클릭합니다.

	![Dropbox 커넥터 클릭](./media/app-service-api-connnect-your-app-to-saas-connector/searchdb.png)
 
3. **만들기**를 클릭합니다.

	![만들기 클릭](./media/app-service-api-connnect-your-app-to-saas-connector/clickcreate.png)
 
5. **Dropbox 커넥터** 블레이드의 **앱 서비스 계획** 아래에서 **새로 만들기**를 클릭한 다음 **새 앱 서비스 계획 만들기** 상자에 DropBoxPlan을 입력합니다.

	앱 서비스 계획에 대한 자세한 내용은 [Azure 앱 서비스 계획의 포괄 개요](azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요.

4. **리소스 그룹** 아래에서 **새로 만들기**를 클릭한 다음 **새 리소스 그룹 만들기** 상자에 DropboxRG를 입력합니다.

	리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../resource-group-overview.md)를 참조하세요.

7. 무료 **가격 책정 계층**을 선택합니다. 목록에 표시되지 않는 경우 **모두 보기**를 클릭합니다. **F1 무료**를 클릭한 후 **선택** 단추를 클릭합니다.

	유료 가격 책정 계층을 사용할 수도 있지만 이 자습서에는 필요하지 않습니다.
 
11. 가까운 **위치**를 선택합니다.

9. <a id="gateway"></a>기본값인 "DropboxConnector"를 커넥터의 **이름**으로 유지하고 **만들기**를 클릭합니다.

	![만들기 클릭](./media/app-service-api-connnect-your-app-to-saas-connector/createdropbox.png)

	Azure 앱 서비스가 리소스 그룹을 만든 다음, 리소스 그룹에 Dropbox 커넥터 API 앱과 *게이트웨이* 웹앱을 만듭니다. 게이트웨이의 기능은 리소스 그룹의 모든 API 앱에 대한 액세스를 관리하는 것입니다.

	Azure Preview 포털 홈페이지에서 **알림**을 클릭하면 리소스 만들기 진행 상황을 확인할 수 있습니다.

3. Azure가 커넥터 생성을 완료하면 **찾아보기 > 리소스 그룹 > DropboxRG**를 클릭합니다.
 
	DropboxRG에 대한 **리소스 그룹** 블레이드에 리소스 그룹의 커넥터와 게이트웨이가 표시됩니다.

	![리소스 그룹 다이어그램](./media/app-service-api-connnect-your-app-to-saas-connector/rgdiagram.png)

## Dropbox 계정 및 Dropbox 커넥터 구성

API가 Dropbox 계정에 액세스할 수 있게 하려면 Dropbox 개발자 사이트에서 Dropbox 앱을 만들어야 합니다. 그런 다음 Dropbox 앱의 클라이언트 ID 및 클라이언트 암호 값을 Dropbox 커넥터로 복사하고 인증된 요청만 수락하도록 커넥터를 설정합니다.

### <a id="createdbapp"></a>Dropbox 앱 만들기

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### <a id="copysettings"></a>Dropbox 앱 설정을 Azure Dropbox 커넥터로 복사하고 반대의 경우도 마찬가지입니다. 

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

### 인증된 액세스를 요구하도록 Dropbox 커넥터 설정

기본적으로 커넥터의 **액세스 수준**은 **내부**로 설정되므로 동일한 리소스 그룹의 다른 API 앱과 웹앱만 호출할 수 있습니다. 그러나 Dropbox는 인증된 사용자만 Dropbox 계정에 액세스할 수 있도록 허용하므로 사용자 인증을 요구하도록 액세스 수준 설정을 변경해야 합니다.

1. **설정** 블레이드로 돌아가서 **응용 프로그램 설정**을 클릭합니다.

2. **응용 프로그램 설정** 블레이드에서 **액세스 수준**을 **공용(인증됨)**으로 설정하고 **저장**을 클릭합니다.
	
	![공용(인증됨)으로 설정](./media/app-service-api-connnect-your-app-to-saas-connector/pubauth.png)

이제 나가는 호출이 Dropbox 계정에 액세스할 수 있고 들어오는 호출이 인증된 사용자로부터 시작되어야 하도록 Dropbox 커넥터를 구성했습니다. 다음 섹션에서는 사용자를 인증하는 데 사용할 인증 공급자를 지정합니다.

## 게이트웨이 구성

[앞에서](#gateway) 설명한 대로 게이트웨이는 리소스 그룹의 모든 API 앱에 대한 액세스를 관리하는 특수 웹앱입니다. 사용자를 인증할 게이트웨이를 설정하려면 Azure Active Directory, Google 또는 Twitter와 같은 인증 공급자를 선택합니다. 그러면 사용자가 선택된 공급자에 인증해야만 Dropbox 커넥터를 성공적으로 호출할 수 있습니다.

- 이 단계를 수행하려면 [API 앱 보호](app-service-api-dotnet-add-authentication.md) 자습서의 [게이트웨이 구성](app-service-api-dotnet-add-authentication.md#configure-the-gateway) 섹션으로 이동하여 해당 지침에 따라 DropboxRG 리소스 그룹의 게이트웨이를 구성합니다.

## 테스트를 통해 사용자 및 Dropbox 인증 확인

인증 공급자를 사용하도록 DropboxRG 리소스 그룹의 게이트웨이를 구성한 후 Dropbox 커넥터를 테스트할 수 있습니다.

대부분의 경우 코드에서 호출하여 커넥터를 사용하며, 방법을 보여 주는 자습서도 작성합니다. 그러나 앱의 다른 부분을 연결하기 전에 커넥터가 작동하는지 확인하려는 경우도 있습니다. 이 자습서에서는 브라우저 및 단순한 REST 클라이언트 도구를 사용하여 방금 설치 및 구성한 Dropbox 커넥터를 통해 Dropbox 서비스를 조작할 수 있는지 확인하는 방법을 보여 줍니다.

다음 지침은 Chrome 브라우저 개발자 도구 및 Postman REST 클라이언트 도구를 사용하여 이러한 단계를 수행하는 방법을 보여 줍니다. 이는 하나의 예일 뿐이며 다른 브라우저 및 도구로 동일한 절차를 수행할 수 있습니다. "고급 REST 클라이언트"도 사용할 수 있는 다른 Chrome 추가 기능 중 하나입니다.

### 최종 사용자로 로그인

새 브라우저 창에서 다음 단계를 수행합니다. 사용 중인 인증 공급자에 따라 비공개 또는 시크릿 창을 사용해야 할 수도 있습니다.

2. 구성한 게이트웨이 및 인증 공급자에 대한 로그인 URL로 이동합니다. URL은 다음 패턴을 따릅니다. 

    	http://[gatewayurl]/login/[providername]

	[Azure Preview 포털]의 **게이트웨이** 블레이드에서 게이트웨이 URL을 얻을 수 있습니다. **게이트웨이** 블레이드에 액세스하려면 **리소스 그룹** 블레이드에 표시된 다이어그램에서 게이트웨이를 클릭합니다.

	![게이트웨이 URL](./media/app-service-api-connnect-your-app-to-saas-connector/gatewayurl.png)

	[providername] 값은 Facebook의 경우 "facebook", Twitter의 경우 "twitter", Azure Active Directory의 경우 "aad" 등입니다.

	다음은 Azure Active Directory에 대한 샘플 로그인 URL입니다.

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/login/aad/

3. 브라우저에 로그인 페이지가 표시되면 자격 증명을 입력합니다.
 
	Azure Active Directory 로그인을 구성한 경우 [Azure 포털]의 Azure Active Directory 탭에서 만든 응용 프로그램의 **사용자** 탭에 나열된 사용자 중 하나로 로그인합니다(예: admin@contoso.onmicrosoft.com).

	로그인에 성공하면 "로그인 완료" 페이지가 표시됩니다.

	![](./media/app-service-api-connnect-your-app-to-saas-connector/logindone.png)

### Dropbox에 사용자 ID 제공

Dropbox API를 사용하기 위해 Dropbox 권한 부여를 받으려면 Dropbox에 사용자 자격 증명을 제공해야 합니다. Azure에서 해당 프로세스를 대신 시작하지만, 해당 프로세스를 트리거하려면 브라우저에서 특수 게이트웨이 URL로 이동해야 합니다. 해당 URL을 얻으려면 게이트웨이에 대해 HTTP Post 요청을 수행합니다.

게이트웨이에 대한 HTTP Post 요청에는 로그인 시 Azure에서 제공한 인증 토큰이 포함되어야 합니다. 브라우저 요청의 경우 토큰이 쿠키에 저장되어 있으므로 토큰이 자동으로 포함되지만, REST 클라이언트 도구를 사용한 HTTP Post 요청의 경우 쿠키에서 토큰을 가져와 HTTP Post 요청의 요청 헤더에 넣어야 합니다.

1. "로그인 완료" 메시지가 표시된 브라우저 창에서 브라우저의 개발자 도구로 이동하여 `x-zumo-auth` 쿠키를 찾습니다. 다음 단계에서 쿠키의 값을 복사할 수 있도록 이 창을 열어 둡니다.
 
	Chrome에서 쿠키 값을 가져오려면 다음 단계를 수행합니다.

	- F12 키를 눌러 개발자 도구를 엽니다.
	- **리소스** 탭으로 이동합니다.
	- 게이트웨이 사이트에 대한 쿠키를 찾은 다음 `x-zumo-auth` 쿠키의 **값**을 세 번 클릭하여 모두 선택합니다. 쿠키의 값을 모두 가져와야 합니다. 두 번 클릭하면 첫 번째 부분만 가져올 수 있습니다.  

	![토큰 복사](./media/app-service-api-connnect-your-app-to-saas-connector/copytoken.png)

4. 새 브라우저 탭 또는 창에서 HTTP Post 요청을 만든 후 게이트웨이로 보내 동의 URL을 요청합니다. `x-zumo-auth` 토큰을 HTTP 헤더로 포함합니다.

	URL은 다음 패턴을 따릅니다.

		[gatewayurl]/api/consent/list?api-version=2015-01-14&redirecturl=[a URL you want the browser to go to after you authenticate]

	예:

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/api/consent/list?api-version=2015-01-14&redirecturl=https://portal.azure.com

	Chrome의 Postman에서 요청을 보내려면 다음 단계를 수행합니다.

	- 위에서 설명한 대로 **요청 URL**을 입력합니다.
	- 메서드를 **Post**로 설정합니다.
	- `x-zumo-auth`라는 헤더를 추가합니다.
	- 헤더 **값** 필드에 `x-zumo-auth` 쿠키에서 복사한 값을 붙여넣습니다.
	- **보내기**를 클릭합니다.
	 
	다음 그림에서는 Chrome의 Postman 도구를 보여 줍니다.

	![동의 URL을 위해 보내기](./media/app-service-api-connnect-your-app-to-saas-connector/sendforconsent.png)

	응답에는 Dropbox에서 사용자의 로그인 프로세스를 시작하기 위해 사용할 URL이 포함됩니다. 메서드 드롭다운이 **Post**로 설정되어 있어도 Get 메서드가 지원되지 않음을 나타내는 오류 응답이 표시되는 경우 게이트웨이 URL이 HTTP가 아닌 HTTPS인지 확인합니다.

	![동의 URL](./media/app-service-api-connnect-your-app-to-saas-connector/getconsenturl.png)

2. HTTP Post 요청에 대한 응답으로 수신된 URL로 이동합니다.

	이 URL에 대한 응답은 브라우저를 Dropbox 사이트로 리디렉션합니다. 이 사이트에서 사용자는 로그인을 하고 앱에서 사용자의 계정에 액세스할 수 있도록 동의합니다.
	
	로그인 및 동의 프로세스가 완료되면 Dropbox는 사용자가 지정한 리디렉션 URL(예: 예제를 따르고 https://portal.azure.com)을 사용한 경우 Azure Preview 포털)로 브라우저를 리디렉션합니다. 웹앱에서 호출한다면 이 페이지는 웹앱에 표시되는 다음 페이지가 될 것입니다. 로그인 또는 동의 프로세스에서 오류가 발생하는 경우 리디렉션 URL에 `error` querystring 변수가 포함될 수 있으므로 앱은 URL을 확인합니다.

3. 다음 섹션에서 사용할 것이므로 이 브라우저 창을 열어 둡니다.

### Dropbox 커넥터 API 호출 

이제 Azure에서 다음 3개의 인증 토큰을 관리합니다.

- 게이트웨이에 대해 구성한 인증 공급자(예: Azure Active Directory)의 토큰
- Dropbox의 토큰
- Azure에서 만든 토큰("zumo" 토큰)

Dropbox 작업을 위해 HTTP 요청을 수행할 때는 zumo 토큰만 사용해도 됩니다. 백그라운드에서 Azure가 해당 토큰을 다른 두 토큰과 연결했으며, 커넥터가 Dropbox를 호출할 때 다른 두 토큰을 대신 제공합니다.

다음 단계에서는 Dropbox 커넥터에 대한 Get 요청을 수행하여 Dropbox 계정의 파일을 확인합니다. 브라우저 창을 Get 요청에 사용할 수 있으며 브라우저 창은 쿠키에 zumo 토큰이 이미 포함되어 있으므로 URL로 이동하여 Dropbox 데이터를 가져오기만 하면 됩니다.

1. Azure Preview 포털이 열려 있는 브라우저 창에서 Dropbox 커넥터에 대한 **API 앱** 블레이드로 돌아갑니다. 

2. API 앱의 URL을 복사합니다.
 
4. **API 정의** 아이콘을 클릭하여 커넥터에서 사용할 수 있는 API 메서드를 확인합니다.

	![API 앱 블레이드](./media/app-service-api-connnect-your-app-to-saas-connector/apiappblade.png)

	![API 정의](./media/app-service-api-connnect-your-app-to-saas-connector/apidef.png)

2. 게이트웨이에 인증하는 데 사용한 브라우저 창에서 폴더의 파일 이름을 검색하는 GET 메서드를 호출합니다. 다음은 URL 패턴입니다.

		[connectorurl]/folder/[foldername]
   
3. 예를 들어 커넥터 URL이 https://dropboxrg784237ad3e7.azurewebsites.net이고 블로그 폴더의 파일을 확인하려는 경우 URL은 다음과 같습니다.

		https://dropboxrg784237ad3e7.azurewebsites.net/folder/blog

	브라우저마다 API 호출에 다르게 응답합니다. Chrome을 사용하는 경우 다음 예제와 유사한 응답이 표시됩니다.

	![폴더 요청에 대한 응답 가져오기](./media/app-service-api-connnect-your-app-to-saas-connector/dbresponse.png)

## 다음 단계

SaaS 커넥터를 설치, 구성 및 테스트하는 방법을 살펴봤습니다. 자세한 내용은 다음 리소스를 참조하세요.

* [커넥터 사용](../app-service-logic/app-service-logic-connectors-list.md)
* [API 앱 및 모바일 앱 인증](../app-service/app-service-authentication-overview.md)  

[Azure Preview 포털]: https://portal.azure.com/
[Azure 포털]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO4-->
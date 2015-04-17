<properties 
	pageTitle="기존 SaaS 커넥터에 앱 연결" 
	description="이 문서에서는 기존 SaaS 커넥터에 연결하는 방법을 보여 줍니다." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="VinayaReddy" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="vinayr;tdykstra"/>

# 기존 SaaS 커넥터에 앱 연결

## 개요

이 자습서에서는 Azure 마켓플레이스에 있는 SaaS 커넥터를 사용하는 방법을 보여 줍니다. 

다음 단계를 수행합니다.
- Dropbox Connector 프로비전
- Dropbox API 앱 구성
- 게이트웨이 구성
- 토큰을 얻어 토큰 저장소에 저장 
- Dropbox API를 호출하고 인증된 액세스가 작동하는지 확인

## Dropbox Connector 프로비전

1. Preview 포털 홈페이지로 이동하여 마켓플레이스를 클릭합니다.

	![](./media/app-service-api-connect-your-app-to-saas-connector/01-Marketplace.png)

2. 마켓플레이스 갤러리에서 Dropbox를 검색합니다.

	![](./media/app-service-api-connect-your-app-to-saas-connector/02-Marketplace-search.png)
 
3. Dropbox 아이콘을 클릭하여 Dropbox Connector를 프로비전합니다. '만들기' 단추를 클릭하여 Dropbox Connector를 프로비전합니다. '만들기' 단추를 클릭하기 전에 이름 및 모든 필드에 대한 원하는 값을 입력했는지 확인합니다. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/03-Dropbox-Connector-Blade.png) 

4. [Preview 포털](https://portal.azure.com/)에서 리소스 그룹의 블레이드를 살펴봅니다. Dropbox Connector와 게이트웨이를 볼 수 있습니다. API 앱을 프로비전할 때 리소스 그룹의 일부가 되도록 프로비전했습니다. 포털에서 리소스 그룹을 만들 때 게이트웨이도 만들어집니다. 게이트웨이는 대상이 리소스 그룹의 API 앱인 모든 요청을 처리하는 특수 웹앱입니다.

	동일한 리소스 그룹에 웹앱과 다른 API 앱을 추가할 수 있으며 리소스 그룹 내의 각 API 앱의 액세스 가능성 설정은 다음의 세 가지 중 하나일 수 있습니다.

	* 공용(익명) - 모든 사용자가 로그인하지 않고 리소스 그룹 외부에서 API 앱을 호출할 수 있습니다.
	* 공용(인증됨) - 인증된 사용자만 리소스 그룹 외부에서 API 앱을 호출할 수 있습니다.
	* 내부 - 동일한 리소스 그룹의 다른 API 앱 또는 웹앱만 API 앱을 호출할 수 있습니다.

## Dropbox API 앱 구성

먼저 인증된 요청만 수락하도록 Dropbox를 구성합니다.  해당 액세스 가능성을 **공용(인증됨)**으로 설정하고 Azure Active Directory, Google 또는 Facebook과 같은 공급자의 인증을 요구하도록 게이트웨이를 구성합니다.

1.	Azure [Preview 포털](https://portal.azure.com/)에서 **찾아보기 > API 앱**을 클릭한 후 보호하려는 API 앱의 이름을 클릭합니다.

	![](./media/app-service-api-connect-your-app-to-saas-connector/04-Browse-API-Apps.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/05-Dropbox-Connector.png) 
 
2.	API 앱 블레이드에서 설정을 클릭한 후 응용 프로그램 설정을 클릭합니다.
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/06-Dropbox-connector-properties.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/07-dropbox-settings-dialog.png) 

3.	**응용 프로그램 설정** 블레이드에서 **액세스 수준**을 **공용(인증됨)**으로 변경합니다. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/08-public-auth-setting-blade.png) 

	이제 Dropbox Connector가 인증되지 않은 액세스로부터 보호됩니다. 이제 Dropbox 인증을 설정할 차례입니다. 아래에 표시된 UI에 *클라이언트 ID* 및 *클라이언트 암호* 세부 정보를 입력합니다([dropbox 개발자 계정](https://www.dropbox.com/developers/apps)에서 *클라이언트 ID*와 *클라이언트 암호*를 연결할 수 있음).

	![](./media/app-service-api-connect-your-app-to-saas-connector/09-Dropbox-authentication-settings.png) 

그런 다음, 게이트웨이를 구성하여 사용할 인증 공급자를 지정해야 합니다.

## 게이트웨이 구성

1. Dropbox API 앱 블레이드로 돌아가서 게이트웨이 링크를 클릭합니다.
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/10-dropbox-connector-gateway.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/11-gateway-all-properties.png)

	![](./media/app-service-api-connect-your-app-to-saas-connector/12-gateway-property-settings-blade.png) 

2. 사용할 ID 공급자를 선택하고 해당 문서의 단계에 따라 해당 공급자를 사용하여 API 앱을 구성합니다. 이러한 문서는 모바일 앱용으로 작성되었지만 절차는 API 앱에 대해서도 동일합니다. 일부 절차를 수행하려면 [관리 포털](https://manage.windowsazure.com/)과 [Preview 포털](https://portal.azure.com/)을 둘 다 사용해야 합니다.
  
	- [Microsoft 계정](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-microsoft-authentication-preview)
	- [Facebook 로그인](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-facebook-authentication-preview)
	- [Twitter 로그인](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-twitter-authentication-preview)
	- [Google 로그인](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-google-authentication-preview)
	- [Azure Active Directory](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-active-directory-authentication-preview)

	구성을 수행하는 방법에 대한 단계별 지침은 "API 앱 보호: Azure Active Directory 또는 소셜 공급자 인증 추가" 문서를 참조하세요. 

## OAuth 토큰을 얻어 토큰 저장소에 저장

1.	브라우저에서 다음과 같이 로그인 URL로 이동합니다. 

	게이트웨이 Active Directory 설치 블레이드에서 URL을 확보할 수 있습니다.
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-01-gateway-login-URL-portal.png) 
	
	URL은 다음과 같은 형식입니다.
	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	예를 들어 리소스 그룹 이름을 'MyResource'로 지정하고 Azure Active Directory 인증에 대해 게이트웨이를 구성한 경우 URL은 다음과 같습니다.

		http://Myresourcegateway.azurewebsites.net/login/aad
	
	브라우저에 URL을 입력하고 Enter 키를 누르기 전에 먼저 개발자 도구를 시작해야 합니다. 로그인 완료 메시지가 표시됩니다. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-02-gateway-login-URL-Browser.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-03-gateway-login-confirmation.png) 

	네트워크 탭(Chrome)에서 Zumo 인증 토큰 및 값을 확보합니다.
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-04-Acquire-Zumo-Auth-Token.png) 

	Chrome에서 Postman 확장을 표시합니다. 게이트웨이 동의 URL과 Zumo 인증 토큰(헤더) 및 값을 입력하고 POST 요청을 만듭니다. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/15-login-to-the-connector.png) 
 
	Post 요청에서 반환되는 리디렉션 URL을 확보하고 리디렉션 URL이 작동하는지 확인합니다. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/16-redirect-url-validate.png)


	리디렉션 URL이 작동하는 경우 이는 토큰이 유효함을 의미합니다. SaaS 커넥터에 대해 만들어진 모든 호출에서는 우리가 설정하고 확인한 토큰을 사용합니다. 이 토큰은 게이트웨이를 통과하는 외부 원본의 모든 요청에 포함되어야 합니다. 브라우저로 API에 액세스하는 경우 브라우저는 일반적으로 토큰을 쿠키에 저장하고 API에 대한 모든 후속 호출과 함께 토큰을 보냅니다.

동일한 흐름이 Safesforce, Facebook 등과 같은 모든 SaaS 커넥터에 적용됩니다. 

## Dropbox API를 호출하고 인증된 액세스가 작동하는지 확인

1. API 앱 목록으로 돌아가서 Dropbox Connector를 선택합니다. 

1. 맨 위쪽의 URL에 주목합니다.

2. 그림에 표시된 것처럼 Dropbox Connector를 클릭하여 지원되는 모든 API를 표시합니다.

	![](./media/app-service-api-connect-your-app-to-saas-connector/13-dropbox-api-app-operations.png) 

	API 정의 블레이드에서 API 앱에서 지원되는 작업을 확인할 수 있습니다. 강력한 형식의 클라이언트를 생성하기 위해 Visual Studio에서 사용할 수 있는 swagger를 다운로드할 수 있습니다. Sienna 및 PowerApp studio에서 사용할 수 있는 swaddle도 다운로드할 수 있습니다. 

2. 브라우저 창에서, 포털에서 복사한 URL을 입력하고 Dropbox 계정의 파일 또는 기타 세부 정보에 액세스하는 데 지원되는 API를 추가합니다. 

	형식: `<URL>`/Operation

	예를 들면 다음과 같습니다.

		https://dropboxconnector7b47555bd6784237ad3e7736da769ffc.azurewebsites.net/folder/photos
   
	이미 올바른 인증을 설정했으므로 위의 호출은 성공하고 브라우저에 사진 폴더 세부 정보를 표시합니다. 
	![](./media/app-service-api-connect-your-app-to-saas-connector/17-call-dropbox-method-from-browser.png) 

<!--todo<브라우저의 이미지 복사>-->

## 다음 단계

API 앱을 보호하고 토큰 저장소를 사용하여 SaaS 커넥터에 액세스하도록 게이트웨이를 구성하는 방법을 살펴봤습니다. 자세한 내용은 [API 앱 정의](app-service-api-apps-why-best-platform.md)를 참조하세요. 

<!--HONumber=49-->
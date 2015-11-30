<properties 
	pageTitle="Azure API 관리에서 API를 만드는 방법" 
	description="Azure API 관리에서 API를 만들고 구성하는 방법에 대해 알아봅니다." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="sdanie"/>

# Azure API 관리에서 API를 만드는 방법

API 관리에서 API는 클라이언트 응용 프로그램이 호출할 수 있는 작업 집합을 나타냅니다. 새 API가 게시자 포털에서 생성되고 필요한 작업이 추가됩니다. 작업이 추가되면 API가 제품에 추가되므로, 이 API를 게시할 수 있습니다. API가 게시되면 구독자와 개발자가 해당 API를 사용할 수 있습니다.

이 가이드에서는 프로세스의 첫 번째 단계인 API 관리에서 새 API를 만들고 구성하는 방법을 보여 줍니다. 작업 추가 및 제품 게시에 대한 자세한 내용은 [API에 작업을 추가하는 방법][] 및 [제품을 만들고 게시하는 방법][]을 참조하세요.

## <a name="create-new-api"> </a>새 API 만들기

게시자 포털에서 API를 만들고 구성합니다. 게시자 포털에 액세스하려면 API 관리 서비스에 대해 Azure 포털에서 **관리**를 클릭합니다.

![게시자 포털][api-management-management-console]

>아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][] 자습서의 [API 관리 서비스 인스턴스 만들기][]를 참조하세요.

왼쪽의 **API 관리** 메뉴에서 **API**를 클릭한 다음 **API 추가**를 클릭합니다.

![API 만들기][api-management-create-api]

**새 API 추가** 창을 사용하여 새 API를 구성합니다.

![새 API 추가][api-management-add-new-api]

새 API를 구성하는 데 다음 세 개의 필드가 사용됩니다.

-	**Web API 제목**은 API를 설명하는 고유한 이름을 제공합니다. 개발자 및 관리 포털에 표시됩니다.
-	**웹 서비스 URL**은 API를 구현하는 HTTP 서비스를 참조합니다. API 관리는 이 주소로 요청을 전달합니다.
-	**Web API URL 접미사**는 API 관리 서비스의 기준 URL에 추가됩니다. 기본 URL은 API 관리 서비스 인스턴스에서 호스트되는 모든 API에 공통으로 사용됩니다. API 관리는 접미사를 사용하여 API를 구분하므로, 접미사는 지정된 게시자의 모든 API에 대해 고유해야 합니다.
-	**Web API URL 구성표**는 API에 액세스하는 데 사용할 수 있는 프로토콜을 결정합니다. 기본적으로 HTTPS가 지정됩니다.

세 값이 구성되면 **저장**을 클릭합니다. 새 API가 만들어지면 API에 대한 요약 페이지가 관리 포털에 표시됩니다.

![API 요약][api-management-api-summary]

## <a name="configure-api-settings"> </a>API 설정 구성

**설정** 탭을 사용하여 API의 구성을 확인 및 편집할 수 있습니다. **Web API 제목**, **웹 서비스 URL** 및 **Web API URL 접미사**는 초기에 API가 생성될 때 설정되며, 이 탭에서 수정할 수 있습니다. **설명**은 선택적 설명을 제공하고, **Web API URL 구성표**는 API에 액세스하는 데 사용할 수 있는 프로토콜을 결정합니다.

![API 설정][api-management-api-settings]

API를 구현하는 웹 서비스에 대해 **게이트웨이 인증**을 구성하려면 **보안** 탭을 선택합니다. **자격 증명 포함** 드롭다운을 사용하여 **기본 인증** 또는 **상호 인증서** 인증을 구성할 수 있습니다. 기본 인증을 사용하려면 원하는 자격 증명을 입력합니다. 상호 인증서 인증을 사용하는 방법에 대한 자세한 내용은 [Azure API 관리에서 상호 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법][]을 참조하세요.

**보안** 탭에서 OAuth 2.0을 사용하여 **사용자 권한 부여**를 구성할 수도 있습니다. 자세한 내용은 [Azure API 관리에서 OAuth 2.0을 사용하여 개발자 계정에 권한을 부여하는 방법][]을 참조하세요.

![기본 인증 설정][api-management-api-settings-credentials]

**저장**을 클릭하여 API 설정에 대한 변경 내용을 저장합니다.

## <a name="next-steps"> </a>다음 단계

API를 만들고 설정을 구성한 후 다음 단계는 API에 작업을 추가하고, 제품에 API를 추가하고, 개발자가 사용할 수 있도록 게시하는 것입니다. 자세한 내용은 다음 두 안내서를 참조하세요.

-	[API에 작업을 추가하는 방법][]
-	[제품을 만들고 게시하는 방법][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[API에 작업을 추가하는 방법]: api-management-howto-add-operations.md
[제품을 만들고 게시하는 방법]: api-management-howto-add-products.md

[API 관리 서비스 인스턴스 만들기]: api-management-get-started.md
[Azure API 관리 시작]: api-management-get-started.md#create-service-instance
[Azure API 관리에서 상호 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법]: api-management-howto-mutual-certificates.md
[Azure API 관리에서 OAuth 2.0을 사용하여 개발자 계정에 권한을 부여하는 방법]: api-management-howto-oauth2.md

<!---HONumber=Nov15_HO4-->
<properties 
	pageTitle="Azure API 관리 시작" 
	description="API와 작업을 만들고 API 관리를 시작하는 방법에 대해 알아봅니다.." 
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
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Azure API 관리 시작

이 가이드에서는 빠르게 API 관리를 사용하기 시작하고 첫 번째 API 호출을 만드는 방법을 보여 줍니다.

> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][]을 참조하세요.

API 관리 작업의 첫 번째 단계는 서비스 인스턴스를 만드는 것입니다. [관리 포털][]에 로그인하고 **새로 만들기**, **앱 서비스**, **API 관리**, **만들기**를 클릭합니다.

![API Management new instance][api-management-create-instance-menu]

**URL**에서 서비스 URL에 사용할 고유한 하위 도메인 이름을 지정합니다.

서비스 인스턴스에 대해 원하는 **구독** 및 **지역**을 선택합니다. 선택한 후에 다음 단추를 클릭합니다.

![New API Management service][api-management-create-instance-step1]

**조직 이름**으로 **Contoso Ltd.**를 입력하고 관리자 메일 필드에 메일 주소를 입력합니다.

>이 전자 메일 주소는 API 관리 시스템에서 알림을 보내는 데 사용됩니다. 자세한 내용은 [알림 구성][]을 참조하세요.

![New API Management service][api-management-create-instance-step2]

API 관리 서비스 인스턴스는 두 계층 즉, 개발자 및 표준으로 제공됩니다. 기본적으로 새 API 관리 서비스 인스턴스는 개발자 계층을 사용하여 만듭니다. 표준 계층을 선택하려면 **고급 설정** 확인란을 선택하고 다음 화면에서 표준 계층을 선택합니다.

>Microsoft Azure는 API 관리 서비스를 실행할 수 있는 두 계층 개발자 및 표준으로 제공됩니다. 개발자 계층은 고가용성이 문제가 되지 않는 개발, 테스트 및 파일럿 API 프로그램용입니다. 표준 계층에서는 더 많은 트래픽을 처리하도록 예약 단위 수를 확장할 수 있습니다. 표준 계층은 대부분의 처리 능력 및 성능을 사용하여 API 관리 서비스를 제공합니다. 이 자습서는 두 계층 중 어느 계층을 사용하든 완료할 수 있습니다. API 관리 계층에 대한 자세한 내용은 [API 관리 가격][]을 참조하세요.

확인란을 클릭하여 서비스 인스턴스를 만듭니다.

![New API Management service][api-management-instance-created]

서비스 인스턴스를 만든 후 다음 단계는 API를 만드는 것입니다.

## <a name="create-api"> </a>API 만들기

API는 클라이언트 응용 프로그램에서 호출할 수 있는 작업 집합으로 구성됩니다. API 작업은 기존 웹 서비스로 프록시 처리됩니다.

각 API 관리 서비스 인스턴스는 HTTP 동사를 호출할 수 있는 샘플 Echo API로 미리 구성된 상태로 제공되며 반환 값은 사용자가 보낸 헤더 및 본문과 같습니다. 이 자습서에서는 Echo API용 백 엔드 웹 서비스를 사용하여 API 관리에서 **My Echo 서비스**라는 새로운 API를 만듭니다.

API는 API 관리 콘솔에서 생성 및 구성되며, 이 콘솔에는 Azure 관리 포털을 통해 액세스합니다. API 관리 콘솔에 액세스하려면 Azure 포털에서 API 관리 서비스의 **관리 콘솔**을 클릭합니다.

![New API Management console][api-management-management-console]

**My Echo API**를 만들려면 왼쪽의 **API 관리** 메뉴에서 **API**를 클릭한 다음 **API 추가**를 클릭합니다.

![Create API][api-management-create-api]

![Add new API][api-management-add-new-api]

새 API를 구성하는 데 다음 세 개의 필드가 사용됩니다.

-	**Web API 제목** 텍스트 상자에 **My Echo API**를 입력합니다. **Web API 제목**은 API를 설명하는 고유한 이름을 제공합니다. 개발자 및 관리 포털에 표시됩니다.
-	**웹 서비스 URL**에 **http://echoapi.cloudapp.net/api**를 입력합니다. **웹 서비스 URL**은 API를 구현하는 HTTP 서비스를 참조합니다. API 관리는 이 주소로 요청을 전달합니다.
-	**Web API URL 접미사**에 **myecho**를 입력합니다. **Web API URL 접미사**는 API 관리 서비스의 기준 URL에 추가됩니다. API는 기준 URL을 공유하며 기준 URL 뒤에 추가되는 고유 접미사에 의해 구분됩니다.
-	**Web API URL 구성표**는 API에 액세스하는 데 사용할 수 있는 프로토콜을 결정합니다. 기본적으로 HTTPS가 지정됩니다.

**저장**을 클릭하여 API를 만듭니다. 새 API가 만들어지면 API에 대한 요약 페이지가 관리 포털에 표시됩니다.

![API summary][api-management-new-api-summary]


>샘플 Echo API는 인증을 사용하지 않지만, 인증 구성에 대한 자세한 내용을 보려면 [API 설정 구성][]을 참조하세요.


## <a name="add-operation"> </a>작업 추가

**작업**을 클릭하여 API의 작업 창을 표시합니다. 작업 정의는 수신 요청의 유효성을 검사하고 자동으로 문서를 생성하는 데 사용됩니다. 아직 작업을 추가하지 않았으므로 아무 것도 표시되지 않습니다.

![Operations][api-management-myecho-operations]

새 작업을 추가하려면 **작업 추가**를 클릭합니다. **새 작업** 창이 표시되고 **서명** 탭이 기본적으로 선택됩니다.

![Operation signature][api-management-operation-signature]

이 예제에서는 Echo 서비스에 대한 GET 작업을 지정합니다. **서명** 탭에서 다음 값을 필드에 입력합니다.

-	**HTTP 동사** 텍스트 상자에 **GET**을 입력합니다. 입력하기 시작하면 표시된 HTTP 동사 목록에서 **GET**을 선택할 수 있습니다.
-	**URL 템플릿** 텍스트 상자에 **/resource**를 입력합니다.
-	**표시** 이름 텍스트 상자에 **GET resource**를 입력합니다.
-	**A demonstration of a GET call on a sample resource. It is handled by an "echo" backend which returns a response equal to the request (the supplied headers and body are being returned as received).**를 **설명** 텍스트 상자에 입력합니다. 이 설명은 개발자가 이 API를 사용할 때 이 작업에 대한 문서를 생성하는 데 사용됩니다.

**매개 변수**를 클릭하여 이 작업의 쿼리 문자열 매개 변수를 구성합니다. 쿼리 매개 변수를 추가하려면 **쿼리 매개 변수 추가**를 클릭하고 다음 값을 지정합니다.

-	**이름** 텍스트 상자에 **param1**을 입력합니다.
-	**설명** 텍스트 상자에 **A sample parameter that is required.**를 입력합니다.
-	**형식** 필드를 클릭하고 목록에서 **문자열**을 선택합니다. 지원되는 형식은 **문자열**, **숫자**, **부울** 및 **날짜/시간**입니다.
-	**값** 필드를 클릭하고 텍스트 상자에 **sample**을 입력한 후 더하기 기호를 클릭하여 매개 변수에 기본값 텍스트를 추가합니다. 기본 텍스트를 추가한 후에는 **값** 필드 외부의 아무 곳이나 클릭하여 값 추가 창을 끝냅니다.
-	**필수** 확인란을 선택합니다.

**저장**을 클릭하여 API에 새로 구성된 작업을 추가합니다.


## <a name="add-api-to-product"> </a>제품에 새 API 추가

개발자는 API 호출을 수행하려면 먼저 제품을 구독해야 합니다. 제품을 통해 하나 이상의 API에 액세스할 수 있으며 제품에는 사용 할당량, 속도 제한 등과 같은 액세스 제한이 포함될 수 있습니다. 이 자습서 단계에서는 기존 제품에 My Echo API를 추가합니다.

왼쪽에 있는 **API 관리** 메뉴에서 **제품**을 클릭하여 이 API 인스턴스에서 사용할 수 있는 제품을 보고 구성합니다.

![Products][api-management-list-products]

기본적으로 각 API 관리 인스턴스는 두 개의 샘플 제품과 함께 제공됩니다.

-	**Starter**
-	**Unlimited**

이 자습서에서는 **Starter** 제품을 사용합니다. **Starter**를 클릭하면 이 제품과 연관된 API를 포함하여 설정이 표시됩니다.

![Add API][api-management-add-api-to-product]

**제품에 API 추가**를 클릭합니다.

![Add API][api-management-add-myechoapi-to-product]

**My Echo API**에 해당하는 상자를 선택하고 **저장**을 클릭합니다.

![API added][api-management-api-added-to-product]

**My Echo API**가 제품과 연결되었으며, 이제 개발자는 제품을 구독하고 API 사용을 시작할 수 있습니다.

>이 자습서 단계에서는 미리 구성되어 바로 사용할 수 있는 **Starter** 제품을 사용합니다. 새 제품 만들기 및 게시에 대한 단계별 가이드는 [제품을 만들고 게시하는 방법][]을 참조하세요.

관리자 사용자는 모든 제품을 자동으로 구독하며 액세스 권한을 제공하는 API에 액세스할 수 있습니다. 따라서 방금 만든 제품을 호출하기 전에 수동으로 구독할 필요가 없습니다.

## <a name="call-operation"> </a>개발자 포털에서 작업 호출

개발자 포털에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다. 이 자습서 단계에서는 **My Echo API**에 추가된 Get 메서드를 호출합니다. 관리 포털의 오른쪽 위에 있는 메뉴에서 **개발자 포털**을 클릭합니다.

![Developer portal][api-management-developer-portal-menu]

맨 위 메뉴에서 **API**를 클릭하고 **My Echo API**를 클릭하여 사용 가능한 작업을 확인합니다.

![Developer portal][api-management-developer-portal-myecho-api]

작업을 만들 때 추가한 설명 및 매개 변수가 표시되어 이 작업을 사용할 개발자에게 안내합니다.

**GET Resource**을 클릭한 다음 **콘솔 시작**을 클릭합니다. 

![Operation console][api-management-developer-portal-myecho-api-console]

매개 변수의 값을 몇 가지 입력하고 개발자 키를 지정한 후 **HTTP Get**을 클릭합니다.

€

작업 호출 후에는 개발자 포털에 백 엔드 서비스의 **요청된 URL**, **응답 상태**, **응답 헤더** 및 **응답 콘텐츠**가 표시됩니다. 

![Response][api-management-invoke-get-response]



## <a name="next-steps"> </a>다음 단계

-   정책 구성
-   개발자 포털 사용자 지정
-   API 검사기를 사용하여 호출 추적

[Azure 무료 평가판]: http://azure.microsoft.com/pricing/free-trial/

[API 관리 인스턴스 만들기]: #create-service-instance
[API 만들기]: #create-api
[작업 추가]: #add-operation
[제품에 새 API 추가]: #add-api-to-product
[API가 포함된 제품 구독]: #subscribe
[개발자 포털에서 작업 호출]: #call-operation
[분석 보기]: #view-analytics
[다음 단계]: #next-steps

[API 설정 구성]: ../api-management-howto-create-apis/#configure-api-settings
[알림 구성]: ../api-management-howto-configure-notifications
[응답]: ../api-management-howto-add-operations/#responses
[제품을 만들고 게시하는 방법]: ../api-management-howto-add-products
[고급 API 구성 시작]: ../api-management-get-started-advanced
[API 관리 가격]: http://azure.microsoft.com/pricing/details/api-management/
[관리 포털]: https://manage.windowsazure.com/

[정책 구성]: ../api-management-howto-policies
[개발자 포털 사용자 지정]: ../api-management-customize-portal
[API 검사기를 사용하여 호출 추적]: ../api-management-howto-api-inspector

[api-management-management-console]: ./media/api-management-get-started-b/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started-b/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started-b/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started-b/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started-b/api-management-instance-created.png
[api-management-create-api]: ./media/api-management-get-started-b/api-management-create-api.png
[api-management-add-new-api]: ./media/api-management-get-started-b/api-management-add-new-api.png
[api-management-new-api-summary]: ./media/api-management-get-started-b/api-management-new-api-summary.png
[api-management-myecho-operations]: ./media/api-management-get-started-b/api-management-myecho-operations.png
[api-management-operation-signature]: ./media/api-management-get-started-b/api-management-operation-signature.png
[api-management-list-products]: ./media/api-management-get-started-b/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started-b/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started-b/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started-b/api-management-api-added-to-product.png



[api-management-developer-portal-menu]: ./media/api-management-get-started-b/api-management-developer-portal-menu.png
[api-management-developer-portal-myecho-api]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api.png
[api-management-developer-portal-myecho-api-console]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started-b/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started-b/api-management-invoke-get-response.png

<!--HONumber=35.2-->

<!--HONumber=46--> 

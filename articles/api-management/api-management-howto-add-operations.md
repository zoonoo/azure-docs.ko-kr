<properties 
	pageTitle="Azure API 관리에서 API에 작업을 추가하는 방법" 
	description="Azure API 관리에서 API에 작업을 추가하는 방법에 대해 알아봅니다." 
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
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# Azure API 관리에서 API에 작업을 추가하는 방법

API 관리에서 API를 사용하려면 먼저 작업을 추가해야 합니다. 이 가이드에서는 API 관리에서 다양한 유형의 작업을 API에 추가하고 구성하는 방법을 보여 줍니다.

## <a name="add-operation"> </a>작업 추가

게시자 포털에서 작업을 API에 추가하고 구성합니다. 게시자 포털에 액세스하려면 API 관리 서비스에 대해 Azure 포털에서 **관리**를 클릭합니다.

![게시자 포털][api-management-management-console]

>아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][] 자습서의 [API 관리 서비스 인스턴스 만들기][]를 참조하세요.

API 관리 포털에서 원하는 API를 선택한 다음 **작업** 탭을 선택합니다.

![작업][api-management-operations]

새 작업을 추가하려면 **작업 추가**를 클릭합니다. **새 작업**이 표시되고 **서명** 탭이 기본으로 선택됩니다.

![작업 추가][api-management-add-operation]

드롭다운 목록에서 **HTTP 동사**를 선택하여 지정합니다.

![HTTP 메서드][api-management-http-method]

하나 이상의 URL 경로 세그먼트 및 0개 이상의 쿼리 문자열 매개 변수로 구성된 URL 조각을 입력하여 URL 템플릿을 정의합니다. API의 기준 URL에 추가되는 URL 템플릿은 단일 HTTP 작업을 식별합니다. 여기에는 둥근 괄호로 식별되는 하나 이상의 명명된 변수 부분을 포함할 수 있습니다. 이러한 변수 부분을 템플릿 매개 변수라고 하며 요청을 API 관리 플랫폼으로 처리할 때 요청의 URL에서 추출하여 동적으로 할당되는 값입니다.

![URL 템플릿][api-management-url-template]

필요한 경우 **URL 템플릿 다시 쓰기**를 지정하세요. 그렇게 하면 다시 쓰기 템플릿에 따라 변환된 URL을 통해 백 엔드를 호출하면서 프런트 엔드의 들어오는 요청을 처리하는 표준 URL 템플릿을 사용할 수 있습니다. URL 템플릿의 템플릿 매개 변수는 다시 쓰기 템플릿에서 사용해야 합니다. 다음 예제에서는 위 예제에서 웹 서비스의 패스 세그먼트로 인코딩된 콘텐츠 유형을, URL 템플릿을 사용하여 API 관리 플랫폼을 통해 게시된 API에서 쿼리 매개 변수로 제공할 수 있는 방법을 보여 줍니다.

![URL 템플릿 다시 쓰기][api-management-url-template-rewrite]

작업의 호출자는 `/customers?customerid=ALFKI` 형식을 사용하며 백 엔드 서비스를 호출할 때 이 형식은 `/Customers('ALFKI')`에 매핑됩니다.


**표시** 이름 및 **설명**은 작업에 대한 설명을 제공하며, 개발자 포털에서 API를 사용하는 개발자에게 문서를 제공하는 데 사용됩니다.

![설명][api-management-description]

**설명** 입력란에 작업 설명을 일반 텍스트 또는 HTML로 지정할 수 있습니다.

## <a name="operation-caching"> </a>작업 캐싱

응답 캐싱은 API 소비자에 인식되는 대기 시간을 줄이고, 대역폭 소비를 낮추고, API를 구현하는 HTTP 웹 서비스의 부하를 낮춥니다.

작업에 대한 캐싱을 쉽고 빠르게 사용하려면 **캐싱** 탭을 선택하고 **사용** 확인란을 선택합니다.

![구성][api-management-caching-tab]

**기간**은 작업 응답이 캐시에 남아 있는 기간을 지정합니다. 기본값은 3600초 또는 1시간입니다.

여러 캐시 키에 해당하는 응답이 별도의 캐시 값을 가져오도록 캐시 키는 응답 간을 구분하는 데 사용됩니다. 선택적으로, 각기 **쿼리 문자열 매개 변수에 따라 다름** 및 **헤더에 따라 다름** 입력란의 캐시 키 값을 계산하는 데 사용할 특정 쿼리 문자열 매개 변수 및/또는 HTTP 헤더를 입력합니다. 아무것도 지정하지 않으면 전체 요청 URL과 HTTP 헤더 값 **Accept** 및 **Accept-Charset**가 캐시 키 생성에 사용됩니다.

>캐싱 및 캐싱 정책에 대한 자세한 내용은 [Azure API 관리에서 작업 결과를 캐시하는 방법][]을 참조하세요.


## <a name="request-parameters"> </a>요청 매개 변수

작업 매개 변수는 매개 변수 탭에서 관리합니다. **서명** 탭의 **URL 템플릿**에 지정된 매개 변수는 자동으로 추가되며, URL 템플릿을 편집하여 이 매개 변수를 변경할 수 있습니다. 추가 매개 변수는 수동으로 입력할 수 있습니다.

새 쿼리 매개 변수를 추가하려면 **쿼리 매개 변수 추가**를 클릭하고 다음 정보를 입력합니다.

-	**이름** - 매개 변수 이름입니다.
-	**설명** - 매개 변수에 대한 짧은 설명입니다(선택 사항).
-	**유형** - 매개 변수 유형이며, 드롭다운에서 선택합니다.
-	**값** - 이 매개 변수에 할당할 수 있는 값입니다. 값 중 하나를 기본값으로 표시할 수 있습니다(선택 사항).
-	**필수** - 확인란을 선택하여 매개 변수를 필수로 설정합니다. 

![요청 매개 변수][api-management-request-parameters]

## <a name="request-body"> </a>요청 본문

작업에서 본문을 허용하거나(예: PUT, POST) 필요로 하는 경우 지원되는 모든 표시 형식(예: json, XML)으로 본문의 예제를 제공할 수 있습니다.

>요청 본문은 설명용으로만 제공될 뿐이며 유효성이 검증되지 않았습니다.

요청 본문을 입력하려면 **본문** 탭으로 전환합니다.

**표시 추가**를 클릭하고, 원하는 콘텐츠 형식 이름(예: 응용 프로그램/json)을 입력하고, 드롭다운에서 해당 이름을 선택하고, 선택한 형식의 필요한 요청 본문 예제를 입력란에 붙여넣습니다.

![요청 본문][api-management-request-body]

표시 외에도, **설명** 입력란에 설명을 선택적으로 지정할 수 있습니다.

## <a name="responses"> </a>응답

작업에서 생성될 수 있는 모든 상태 코드에 대한 응답 예제를 제공할 수 있습니다. 각 상태 코드에는 지원되는 콘텐츠 형식별로 하나씩, 두 개 이상의 응답 본문 예제가 있습니다.

응답을 추가하려면 **추가**를 클릭하고 원하는 상태 코드를 입력합니다. 이 예제에서 상태 코드는 **200 OK**입니다. 드롭다운에 표시되는 코드를 선택하면 응답 코드가 생성되어 작업에 추가됩니다.

![응답 코드][api-management-response-code]

**표시 추가**를 클릭하고, 원하는 콘텐츠 형식 이름(예: 응용 프로그램/json)을 입력한 다음, 드롭다운에서 해당 이름을 선택합니다.

![본문 콘텐츠 형식][api-management-response-body-content-type]

선택한 형식의 응답 본문 예제를 입력란에 붙여넣습니다.

![응답 본문][api-management-response-body]

필요한 경우 **설명** 입력란에 선택적인 설명을 추가합니다.

작업이 구성되면 **저장**을 클릭합니다.


## <a name="next-steps"> </a>다음 단계

작업이 API에 추가되면 다음 단계는 제품과 API를 연결하고 개발자가 작업을 호출할 수 있도록 게시하는 것입니다.

-	[제품을 만들고 게시하는 방법][]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[API 관리 서비스 인스턴스 만들기]: api-management-get-started.md
[Azure API 관리 시작]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[제품을 만들고 게시하는 방법]: api-management-howto-add-products.md
[Azure API 관리에서 작업 결과를 캐시하는 방법]: api-management-howto-cache.md

<!---HONumber=July15_HO4-->
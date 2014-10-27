<properties pageTitle="Get started with Azure API Management" metaKeywords="" description="Learn how to create APIs, operations, and get started with API Management." metaCanonical="" services="" documentationCenter="API Management" title="Get started with Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Azure API 관리 시작

이 가이드에서는 빠르게 API 관리를 사용하기 시작하고 첫 번째 API 호출을 만드는 방법을 보여 줍니다.

## 항목 내용

-   [API 관리 인스턴스 만들기][API 관리 인스턴스 만들기]
-   [API 만들기][API 만들기]
-   [작업 추가][작업 추가]
-   [제품에 새 API 추가][제품에 새 API 추가]
-   [API가 포함된 제품 구독][API가 포함된 제품 구독]
-   [개발자 포털에서 작업 호출][개발자 포털에서 작업 호출]
-   [분석 보기][분석 보기]
-   [다음 단계][다음 단계]

## <a name="create-service-instance"> </a>API 관리 인스턴스 만들기

> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][Azure 무료 평가판]을 참조하세요.

API 관리 작업의 첫 번째 단계는 서비스 인스턴스를 만드는 것입니다. [관리 포털][관리 포털]에 로그인하고 **새로 만들기**, **앱 서비스**, **API 관리**, **만들기**를 클릭합니다.

![API 관리 새 인스턴스][API 관리 새 인스턴스]

**URL**에서 서비스 URL에 사용할 고유한 하위 도메인 이름을 지정합니다.

서비스 인스턴스에 대해 원하는 **가격 계층**, **구독** 및 **지역**을 선택합니다. 이 자습서에서 모든 가격 계층을 사용할 수 있습니다. 선택한 후에 다음 단추를 클릭합니다.

![새 API 관리 서비스][새 API 관리 서비스]

**조직 이름**으로 **Contoso Ltd.**를 입력하고 관리자 전자 메일 필드에 전자 메일 주소를 입력합니다.

> 이 전자 메일 주소는 API 관리 시스템에서 알림을 보내는 데 사용됩니다. 자세한 내용은 [알림 구성][알림 구성]을 참조하세요.

확인란을 클릭하여 서비스 인스턴스를 만듭니다.

![새 API 관리 서비스][새 API 관리 서비스]

![새 API 관리 서비스][새 API 관리 서비스]

서비스 인스턴스를 만든 후 다음 단계는 API를 만드는 것입니다.

## <a name="create-api"> </a>API 만들기

API는 클라이언트 응용 프로그램에서 호출할 수 있는 작업 집합으로 구성됩니다. API 작업은 기존 웹 서비스로 프록시 처리됩니다.

각 API 관리 서비스 인스턴스는 전송받은 입력을 다시 반환하는 샘플 Echo API가 미리 구성되어 제공됩니다. 이 API를 사용하려면 HTTP 동사를 호출하면 됩니다. 반환 값은 보낸 헤더 및 본문과 같습니다.

이 자습서에서는 <http://echoapi.cloudapp.net/api> 웹 서비스를 사용하여 API 관리에서 이름이 **My Echo Service**인 API를 만듭니다.

API는 API 관리 콘솔에서 생성 및 구성되며, 이 콘솔에는 Azure 관리 포털을 통해 액세스합니다. API 관리 콘솔에 액세스하려면 Azure 포털에서 API 관리 서비스의 **관리 콘솔**을 클릭합니다.

![새 API 관리 콘솔][새 API 관리 콘솔]

**My Echo API**를 만들려면 왼쪽의 **API 관리** 메뉴에서 **API**를 클릭한 다음 **API 추가**를 클릭합니다.

![API 만들기][API 만들기]

![새 API 추가][새 API 추가]

새 API를 구성하는 데 다음 세 개의 필드가 사용됩니다.

-   **Web API 제목** 텍스트 상자에 **My Echo API**를 입력합니다. **Web API 제목**은 API를 설명하는 고유한 이름을 제공합니다. 개발자 및 관리 포털에 표시됩니다.
-   **웹 서비스 URL**에 **<http://echoapi.cloudapp.net/api>**를 입력합니다. **웹 서비스 URL**은 API를 구현하는 HTTP 서비스를 참조합니다. API 관리는 이 주소로 요청을 전달합니다.
-   **Web API URL 접미사**에 **myecho**를 입력합니다. **Web API URL 접미사**는 API 관리 서비스의 기준 URL에 추가됩니다. API는 기준 URL을 공유하며 기준 URL 뒤에 추가되는 고유 접미사에 의해 구분됩니다.

**저장**을 클릭하여 API를 만듭니다. 새 API가 만들어지면 API에 대한 요약 페이지가 관리 포털에 표시됩니다.

![API 요약][API 요약]

API 섹션에는 네 개의 탭이 있습니다. **요약** 탭은 API에 대한 기본 메트릭과 정보를 표시합니다. **설정** 탭은 백 엔드 서비스에 대한 인증 자격 증명을 포함하여 API의 구성을 보고 편집하는 데 사용됩니다. **작업** 탭은 API의 작업을 관리하는 데 사용되며 이 자습서의 다음 단계에서 사용됩니다. **문제** 탭을 사용하면 개발자가 API를 사용해 보고한 문제점을 볼 수 있습니다.

> 샘플 Echo API는 인증을 사용하지 않지만, 인증 구성에 대한 자세한 내용은 보려면 [API 설정 구성][API 설정 구성](영문)을 참조하세요.

API를 만들고 설정을 구성한 후 다음 단계는 API에 작업을 추가하는 것입니다. 작업 정의는 수신 요청의 유효성을 검사하고 자동으로 문서를 생성하는 데 사용됩니다.

## <a name="add-operation"> </a>작업 추가

**작업**을 클릭하여 API의 작업 창을 엽니다. 아직 작업을 추가하지 않았으므로 아무 것도 표시되지 않습니다.

![작업][작업]

새 작업을 추가하려면 **작업 추가**를 클릭합니다. **새 작업** 창이 표시되고 **서명** 탭이 기본으로 선택됩니다.

![작업 서명][작업 서명]

이 예제에서는 Echo 서비스에 대한 GET 작업을 지정합니다. **서명** 탭에서 다음 값을 필드에 입력합니다.

-   **HTTP 동사** 텍스트 상자에 **GET**을 입력합니다. 입력하기 시작하면 표시된 HTTP 동사 목록에서 **GET**을 선택할 수 있습니다.
-   **URL 서식 파일** 텍스트 상자에 **/resource**.
-   **표시 이름** 텍스트 상자에 **GET resource**를 입력합니다.
-   **A demonstration of a GET call on a sample resource. It is handled by an "echo" backend which returns a response equal to the request (the supplied headers and body are being returned as received).**를 **설명** 텍스트 상자에 입력합니다. 이 설명은 개발자가 이 API를 사용할 때 이 작업에 대한 문서를 생성하는 데 사용됩니다.

**매개 변수**를 클릭하여 이 작업의 쿼리 문자열 매개 변수를 구성합니다. 이 예제에서는 두 개의 쿼리 문자열 매개 변수가 있습니다. 쿼리 매개 변수를 추가하려면 **쿼리 매개 변수 추가**를 클릭하고 다음 값을 지정합니다.

첫 번째 쿼리 매개 변수에 대해 다음 값을 구성합니다.

-   **이름** 텍스트 상자에 **param1**을 입력합니다.
-   **설명** 텍스트 상자에 **A sample parameter that is required.**를 입력합니다.
-   **형식** 필드를 클릭하고 목록에서 **문자열**을 선택합니다. 지원되는 형식은 **문자열**, **숫자**, **부울** 및 **날짜/시간**입니다.
-   **값** 필드를 클릭하고 텍스트 상자에 **sample**을 입력한 후 더하기 기호를 클릭하여 매개 변수에 기본값 텍스트를 추가합니다. 기본 텍스트를 추가한 후에는 **값** 필드 외부의 아무 곳이나 클릭하여 값 추가 창을 끝냅니다.
-   **필수** 확인란을 선택합니다.

두 번째 쿼리 매개 변수에 대해 다음 값을 입력합니다.

-   **이름**: **param2**
-   **설명**: **Another sample parameter, set to not required.**
-   **형식**: **숫자**

작업에서 생성될 수 있는 모든 상태 코드에 대한 응답 예제를 제공할 수 있습니다. 각 상태 코드에는 지원되는 콘텐츠 형식별로 하나씩, 두 개 이상의 응답 본문 예제가 있습니다. 이 자습서에서는 **200 OK** 응답 코드를 추가합니다.

응답 섹션에서 **추가**를 클릭하고 텍스트 상자에 **200**을 입력하기 시작한 후 드롭다운 목록에서 **200 OK**를 선택합니다.

![응답 추가][응답 추가]

**200 OK**를 선택하면 새 응답 코드가 작업에 추가되고 응답 창이 표시됩니다. **설명** 텍스트 상자에 **Returned in all cases.**를 입력합니다.

![응답 추가][응답 추가]

> **표시 추가**는 여러 표시로 응답을 구성하는 데 사용됩니다. 자세한 내용은 [응답][응답]을 참조하세요.

**저장**을 클릭하여 API에 새로 구성된 작업을 추가합니다.

## <a name="add-api-to-product"> </a>제품에 새 API 추가

개발자는 API 호출을 수행하려면 먼저 제품을 구독해야 합니다. 제품을 통해 하나 이상의 API에 액세스할 수 있으며 제품에는 사용 할당량, 속도 제한 등과 같은 액세스 제한이 포함될 수 있습니다. 이 자습서 단계에서는 기존 제품에 My Echo API를 추가합니다.

왼쪽에 있는 **API 관리** 메뉴에서 **제품**을 클릭하여 이 API 인스턴스에서 사용할 수 있는 제품을 보고 구성합니다.

![제품][제품]

기본적으로 각 API 관리 인스턴스는 두 개의 샘플 제품과 함께 제공됩니다.

-   **Starter**
-   **Unlimited**

이 자습서에서는 **Starter** 제품을 사용하겠습니다. **Starter**를 클릭하면 이 제품과 연관된 API를 포함하여 설정이 표시됩니다.

![API 추가][API 추가]

**제품에 API 추가**를 클릭합니다.

![API 추가][API 추가]

**My Echo API**에 해당하는 상자를 선택하고 **저장**을 클릭합니다.

![API 추가됨][API 추가됨]

**My Echo API**가 제품과 연결되었으며, 이제 개발자는 제품을 구독하고 API 사용을 시작할 수 있습니다.

> 이 자습서 단계에서는 미리 구성되어 바로 사용할 수 있는 Starter 제품을 사용합니다. 새 제품 생성 및 게시에 대한 단계별 가이드는 [제품을 만들고 게시하는 방법][제품을 만들고 게시하는 방법]을 참조하세요.

## <a name="subscribe"> </a>API가 포함된 제품 구독

API를 호출하려면 개발자는 먼저 API 액세스가 제공되는 제품을 구독해야 합니다. 개발자는 개발자 포털에서 제품을 구독할 수 있지만, 관리자가 관리 콘솔에서 개발자를 제품에 구독시킬 수도 있습니다. 우리는 이 자습서의 이전 단계에서 API 관리 인스턴스를 만들었기 때문에 관리자이며, **Starter** 제품에 계정을 구독시키게 됩니다.

왼쪽에 있는 **API 관리** 메뉴에서 **개발자**를 클릭하여 이 서비스 인스턴스의 개발자를 보고 구성합니다.

![개발자][개발자]

개발자 이름을 클릭하여 구독을 포함하여 사용자에 대한 설정을 구성합니다.

> 이 예제에서는 Clayton Gragg라는 개발자를 구독시킵니다. 개발자 계정을 만들지 않은 경우 관리자 계정을 구독시킬 수 있습니다. 개발자 계정 만들기에 대한 자세한 내용은 [Azure API 관리에서 개발자 계정을 관리하는 방법][Azure API 관리에서 개발자 계정을 관리하는 방법](영문)을 참조하세요.

![구독 추가][구독 추가]

**구독 추가**를 클릭합니다.

![구독 추가][구독 추가]

**Starter**의 상자를 선택하고 **구독**을 클릭합니다.

![구독 추가됨][구독 추가됨]

개발자 계정을 구독시킨 후에는 해당 제품의 API를 호출할 수 있습니다.

## <a name="call-operation"> </a>개발자 포털에서 작업 호출

개발자 포털에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다. 이 자습서 단계에서는 **My Echo API**에 추가된 Get 메서드를 호출하겠습니다. 관리 포털의 오른쪽 위에 있는 메뉴에서 **개발자 포털**을 클릭합니다.

![개발자 포털][개발자 포털]

맨 위 메뉴에서 **API**를 클릭하고 **My Echo API**를 클릭하여 사용 가능한 작업을 확인합니다.

![개발자 포털][개발자 포털]

작업을 만들 때 추가한 설명 및 매개 변수가 표시되어 이 작업을 사용할 개발자에게 안내합니다.

**GET Resource**을 클릭한 다음 **콘솔 시작**을 클릭합니다.

![작업 콘솔][작업 콘솔]

매개 변수의 값을 몇 가지 입력하고 개발자 키를 지정한 후 **HTTP Get**을 클릭합니다.

![HTTP Get][HTTP Get]

작업 호출 후에는 개발자 포털에 백 엔드 서비스의 **요청된 URL** 및 **응답 상태**, **응답 헤더**, **응답 콘텐츠**가 표시됩니다.

![응답][응답]

## <a name="view-analytics"> </a>분석 보기

**My Echo API**에 대한 분석을 보려면 개발자 포털의 오른쪽 위에 있는 메뉴에서 **관리**를 선택하여 관리 포털로 다시 전환합니다.

![관리][관리]

관리 포털의 기본 보기는 대시보드이며, 여기서 API 관리 인스턴스의 개요를 확인할 수 있습니다.

![대시보드][대시보드]

My Echo API에 대한 차트 위로 마우스를 가져가서 주어진 기간의 API 사용 관련 특정 메트릭을 표시합니다.

> 차트에서 일부 줄이 표시되지 않는 경우 개발자 포털로 다시 전환하여 API를 호출한 후 몇 분 정도 기다렸다가 대시보드로 다시 돌아옵니다.

![분석][분석]

**자세히 보기**를 클릭하여 API에 대한 요약 페이지를 표시합니다. 여기에는 더 크게 표시되는 메트릭 버전도 포함됩니다.

![요약][요약]

자세한 메트릭과 보고서를 보려면 왼쪽의 **API 관리** 메뉴에서 **분석**을 클릭하세요.

![개요][개요]

**분석** 섹션에는 다음과 같은 네 개의 탭이 있습니다.

-   **개요**에서는 전체적인 사용 및 상태 메트릭뿐만 아니라 상위 개발자, 상위 제품, 상위 API 및 상위 작업도 제공합니다.
-   **사용량**에서는 지리적 표현을 포함하여 API 호출 및 대역폭을 심도 있게 확인할 수 있습니다.
-   **상태**에서는 상태 코드, 캐시 성공률, 응답 시간, API 및 서비스 응답 시간을 위주로 보여 줍니다.
-   **활동**에서는 개발자의 특정 작업, 제품, API 및 작업에 대한 자세한 내용을 보여 주는 보고서를 제공합니다.

## <a name="next-steps"> </a>다음 단계

-   [고급 API 구성 시작][고급 API 구성 시작] 자습서에서 다른 항목을 확인하세요.

  [API 관리 인스턴스 만들기]: #create-service-instance
  [API 만들기]: #create-api
  [작업 추가]: #add-operation
  [제품에 새 API 추가]: #add-api-to-product
  [API가 포함된 제품 구독]: #subscribe
  [개발자 포털에서 작업 호출]: #call-operation
  [분석 보기]: #view-analytics
  [다음 단계]: #next-steps
  [Azure 무료 평가판]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=api_management_hero_a
  [관리 포털]: https://manage.windowsazure.com/
  [API 관리 새 인스턴스]: ./media/api-management-get-started/api-management-create-instance-menu.png
  [새 API 관리 서비스]: ./media/api-management-get-started/api-management-create-instance-step1.png
  [알림 구성]: ../api-management-howto-configure-notifications
  [1]: ./media/api-management-get-started/api-management-create-instance-step2.png
  [2]: ./media/api-management-get-started/api-management-instance-created.png
  [새 API 관리 콘솔]: ./media/api-management-get-started/api-management-management-console.png
  [3]: ./media/api-management-get-started/api-management-create-api.png
  [새 API 추가]: ./media/api-management-get-started/api-management-add-new-api.png
  [API 요약]: ./media/api-management-get-started/api-management-new-api-summary.png
  [API 설정 구성]: ../api-management-howto-create-apis/#configure-api-settings
  [작업]: ./media/api-management-get-started/api-management-myecho-operations.png
  [작업 서명]: ./media/api-management-get-started/api-management-operation-signature.png
  [응답 추가]: ./media/api-management-get-started/api-management-add-response.png
  [4]: ./media/api-management-get-started/api-management-add-response-window.png
  [응답]: ../api-management-howto-add-operations/#responses
  [제품]: ./media/api-management-get-started/api-management-list-products.png
  [API 추가]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [5]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [API 추가됨]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [제품을 만들고 게시하는 방법]: ../api-management-howto-add-products
  [개발자]: ./media/api-management-get-started/api-management-developers.png
  [Azure API 관리에서 개발자 계정을 관리하는 방법]: ../api-management-howto-create-or-invite-developers/
  [구독 추가]: ./media/api-management-get-started/api-management-add-subscription.png
  [6]: ./media/api-management-get-started/api-management-add-subscription-window.png
  [구독 추가됨]: ./media/api-management-get-started/api-management-subscription-added.png
  [개발자 포털]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [7]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [작업 콘솔]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [8]: ./media/api-management-get-started/api-management-invoke-get-response.png
  [관리]: ./media/api-management-get-started/api-management-manage-menu.png
  [대시보드]: ./media/api-management-get-started/api-management-dashboard.png
  [분석]: ./media/api-management-get-started/api-management-mouse-over.png
  [요약]: ./media/api-management-get-started/api-management-api-summary-metrics.png
  [개요]: ./media/api-management-get-started/api-management-analytics-overview.png
  [고급 API 구성 시작]: ../api-management-get-started-advanced

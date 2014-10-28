<properties pageTitle="Get started with Azure API Management" metaKeywords="" description="Learn how to create APIs, operations, and get started with API Management." metaCanonical="" services="" documentationCenter="API Management" title="Get started with Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

# Azure API 관리 시작

이 가이드에서는 빠르게 API 관리를 사용하기 시작하고 첫 번째 API 호출을 만드는 방법을 보여 줍니다.

> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][Azure 무료 평가판]을 참조하세요.

API 관리 작업의 첫 번째 단계는 서비스 인스턴스를 만드는 것입니다. [관리 포털][관리 포털]에 로그인하고 **새로 만들기**, **앱 서비스**, **API 관리**, **만들기**를 클릭합니다.

![API 관리 새 인스턴스][API 관리 새 인스턴스]

**URL**에서 서비스 URL에 사용할 고유한 하위 도메인 이름을 지정합니다.

서비스 인스턴스에 대해 원하는 **가격 계층**, **구독** 및 **지역**을 선택합니다. 이 자습서에서 모든 가격 계층을 사용할 수 있습니다. 선택한 후에 다음 단추를 클릭합니다.

![새 API 관리 서비스][새 API 관리 서비스]

**조직 이름**으로 **Contoso Ltd.**를 입력하고 관리자 전자 메일 필드에 전자 메일 주소를 입력합니다.

>이 전자 메일 주소는 API 관리 시스템에서 알림을 보내는 데 사용됩니다. 자세한 내용은 [알림 구성][알림 구성]을 참조하세요.

확인란을 클릭하여 서비스 인스턴스를 만듭니다.

![새 API 관리 서비스][1]

![새 API 관리 서비스][2]

서비스 인스턴스를 만든 후 다음 단계는 API를 만드는 것입니다.

## <a name="create-api"> </a>API 만들기

API는 클라이언트 응용 프로그램에서 호출할 수 있는 작업 집합으로 구성됩니다. API 작업은 기존 웹 서비스로 프록시 처리됩니다.

각 API 관리 서비스 인스턴스는 HTTP 동사를 호출할 수 있는 샘플 Echo API로 미리 구성된 상태로 제공되며 반환 값은 사용자가 보낸 헤더 및 본문과 같습니다. 이 자습서에서는 Echo API용 백 엔드 웹 서비스를 사용하여 API 관리에서 **My Echo 서비스**라는 새로운 API를 만듭니다.

API는 API 관리 콘솔에서 생성 및 구성되며, 이 콘솔에는 Azure 관리 포털을 통해 액세스합니다. API 관리 콘솔에 액세스하려면 Azure 포털에서 API 관리 서비스의 **관리 콘솔**을 클릭합니다.

![새 API 관리 콘솔][새 API 관리 콘솔]

**My Echo API**를 만들려면 왼쪽의 **API 관리** 메뉴에서 **API**를 클릭한 다음 **API 추가**를 클릭합니다.

![API 만들기][API 만들기]

![새 API 추가][새 API 추가]

새 API를 구성하는 데 다음 세 개의 필드가 사용됩니다.

- **Web API 제목** 텍스트 상자에 **My Echo API**를 입력합니다. **Web API 제목**은 API를 설명하는 고유한 이름을 제공합니다. 개발자 및 관리 포털에 표시됩니다.
- **웹 서비스 URL**에 **<http://echoapi.cloudapp.net/api>**를 입력합니다. **웹 서비스 URL**은 API를 구현하는 HTTP 서비스를 참조합니다. API 관리는 이 주소로 요청을 전달합니다.
- **Web API URL 접미사**에 **myecho**를 입력합니다. **Web API URL 접미사**는 API 관리 서비스의 기준 URL에 추가됩니다. API는 기준 URL을 공유하며 기준 URL 뒤에 추가되는 고유 접미사에 의해 구분됩니다.

**저장**을 클릭하여 API를 만듭니다. 새 API가 만들어지면 API에 대한 요약 페이지가 관리 포털에 표시됩니다.

![API 요약][API 요약]


>샘플 Echo API는 인증을 사용하지 않지만, 인증 구성에 대한 자세한 내용은 보려면 [API 설정 구성][API 설정 구성](영문)을 참조하세요.


## <a name="add-operation"></a>작업 추가

**작업**을 클릭하여 API의 작업 창을 엽니다. 작업 정의는 수신 요청의 유효성을 검사하고 자동으로 문서를 생성하는 데 사용됩니다. 아직 작업을 추가하지 않았으므로 아무 것도 표시되지 않습니다.

![작업][작업]

새 작업을 추가하려면 **작업 추가**를 클릭합니다. **새 작업** 창이 표시되고 **서명** 탭이 기본으로 선택됩니다.

![작업 서명][작업 서명]

이 예제에서는 Echo 서비스에 대한 GET 작업을 지정합니다. **서명** 탭에서 다음 값을 필드에 입력합니다.

- **HTTP 동사** 텍스트 상자에 **GET**을 입력합니다. 입력하기 시작하면 표시된 HTTP 동사 목록에서 **GET**을 선택할 수 있습니다.
- **URL 서식 파일** 텍스트 상자에 **/resource**.
- **표시 이름** 텍스트 상자에 **GET 리소스**를 입력합니다.
- **A demonstration of a GET call on a sample resource. It is handled by an "echo" backend which returns a response equal to the request (the supplied headers and body are being returned as received).**를 **설명** 텍스트 상자에 입력합니다. 이 설명은 개발자가 이 API를 사용할 때 이 작업에 대한 문서를 생성하는 데 사용됩니다.

**매개 변수**를 클릭하여 이 작업의 쿼리 문자열 매개 변수를 구성합니다. 쿼리 매개 변수를 추가하려면 **쿼리 매개 변수 추가**를 클릭하고 다음 값을 지정합니다.

- **이름** 텍스트 상자에 **param1**을 입력합니다.
- **설명** 텍스트 상자에 **A sample parameter that is required.**를 입력합니다.
- **형식** 필드를 클릭하고 목록에서 **문자열**을 선택합니다. 지원되는 형식은 **문자열**, **숫자**, **부울** 및 **날짜/시간**입니다.
- **값** 필드를 클릭하고 텍스트 상자에 **sample**을 입력한 후 더하기 기호를 클릭하여 매개 변수에 기본값 텍스트를 추가합니다. 기본 텍스트를 추가한 후에는 **값** 필드 외부의 아무 곳이나 클릭하여 값 추가 창을 끝냅니다.
- **필수** 확인란을 선택합니다.

**저장**을 클릭하여 API에 새로 구성된 작업을 추가합니다.

## <a name="add-api-to-product"></a>제품에 새 API 추가

개발자는 API 호출을 수행하려면 먼저 제품을 구독해야 합니다. 제품을 통해 하나 이상의 API에 액세스할 수 있으며 제품에는 사용 할당량, 속도 제한 등과 같은 액세스 제한이 포함될 수 있습니다. 이 자습서 단계에서는 기존 제품에 My Echo API를 추가합니다.

왼쪽에 있는 **API 관리** 메뉴에서 **제품**을 클릭하여 이 API 인스턴스에서 사용할 수 있는 제품을 보고 구성합니다.

![제품][제품]

기본적으로 각 API 관리 인스턴스는 두 개의 샘플 제품과 함께 제공됩니다.

- **Starter**
- **Unlimited**


이 자습서에서는 **Starter** 제품을 사용하겠습니다. **Starter**를 클릭하면 이 제품과 연관된 API를 포함하여 설정이 표시됩니다.

![API 추가][API 추가]

**제품에 API 추가**를 클릭합니다.

![API 추가][3]

**My Echo API**에 해당하는 상자를 선택하고 **저장**을 클릭합니다.

![API 추가됨][API 추가됨]

**My Echo API**가 제품과 연결되었으며, 이제 개발자는 제품을 구독하고 API 사용을 시작할 수 있습니다.

>이 자습서 단계에서는 미리 구성되어 바로 사용할 수 있는 **Starter** 제품을 사용합니다. 새 제품 생성 및 게시에 대한 단계별 가이드는 [제품을 만들고 게시하는 방법][제품을 만들고 게시하는 방법]을 참조하세요.

관리자 사용자는 모든 제품을 자동으로 구독하며 액세스 권한을 제공하는 API에 액세스할 수 있습니다. 따라서 방금 만든 제품을 호출하기 전에 수동으로 구독할 필요가 없습니다.

## <a name="call-operation"></a>개발자 포털에서 작업 호출

개발자 포털에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다. 이 자습서 단계에서는 **My Echo API**에 추가된 Get 메서드를 호출하겠습니다. 관리 포털의 오른쪽 위에 있는 메뉴에서 **개발자 포털**을 클릭합니다.

![개발자 포털][개발자 포털]

맨 위 메뉴에서 **API**를 클릭하고 **My Echo API**를 클릭하여 사용 가능한 작업을 확인합니다.

![개발자 포털][4]

작업을 만들 때 추가한 설명 및 매개 변수가 표시되어 이 작업을 사용할 개발자에게 안내합니다.

**GET 리소스**을 클릭한 다음 **콘솔 시작**을 클릭합니다.

![작업 콘솔][작업 콘솔]

매개 변수의 값을 몇 가지 입력하고 개발자 키를 지정한 후 **HTTP Get**을 클릭합니다.

![HTTP Get][HTTP Get]

작업 호출 후에는 개발자 포털에 백 엔드 서비스의 **요청된 URL** 및 **응답 상태**, **응답 헤더**, **응답 콘텐츠**가 표시됩니다.

![응답][응답]



## <a name="next-steps"></a>다음 단계

- 정책 구성
- 개발자 포털 사용자 지정
- API 검사기를 사용하여 호출 추적

[Azure 무료 평가판]: http://www.windowsazure.com/ko--kr/pricing/free-trial/
[관리 포털]: https://manage.windowsazure.com/
[API 관리 새 인스턴스]: ./media/api-management-get-started/api-management-create-instance-menu.png
[새 API 관리 서비스]: ./media/api-management-get-started/api-management-create-instance-step1.png
[알림 구성]: ../api-management-howto-configure-notifications
[1]: ./media/api-management-get-started/api-management-create-instance-step2.png
[2]: ./media/api-management-get-started/api-management-instance-created.png
[새 API 관리 콘솔]: ./media/api-management-get-started/api-management-management-console.png
[API 만들기]: ./media/api-management-get-started/api-management-create-api.png
[새 API 추가]: ./media/api-management-get-started/api-management-add-new-api.png
[API 요약]: ./media/api-management-get-started/api-management-new-api-summary.png
[API 설정 구성]: ../api-management-howto-create-apis/#configure-api-settings
[작업]: ./media/api-management-get-started/api-management-myecho-operations.png
[작업 서명]: ./media/api-management-get-started/api-management-operation-signature.png
[제품]: ./media/api-management-get-started/api-management-list-products.png
[API 추가]: ./media/api-management-get-started/api-management-add-api-to-product.png
[3]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[API 추가됨]: ./media/api-management-get-started/api-management-api-added-to-product.png
[제품을 만들고 게시하는 방법]: ../api-management-howto-add-products
[개발자 포털]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[4]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
[작업 콘솔]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
[HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
[응답]: ./media/api-management-get-started/api-management-invoke-get-response.png

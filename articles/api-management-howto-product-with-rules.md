<properties pageTitle="How to create and configure advanced product settings in Azure API Management" metaKeywords="" description="Learn how to configure a product with quota and rate limit policies." metaCanonical="" services="" documentationCenter="API Management" title="How to create and configure advanced product settings in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Azure API 관리에서 고급 제품 설정을 만들고 구성하는 방법

Azure API 관리(미리 보기)에서는 API 게시자 요구 사항에 부합하도록 제품을 매우 다양하게 구성할 수 있습니다. 이 항목에서는 속도 제한 및 할당량 정책을 포함하여 일부 고급 제품 설정을 구성하는 방법을 보여 줍니다.

이 자습서에서는 분당 최대 10개의 호출, 주당 최대 200개의 호출을 허용하는 무료 평가판 제품을 만들어 게시하고 속도 제한 정책을 테스트합니다.

## 항목 내용

-   [제품 만들기][제품 만들기]
-   [제품에 API 추가][제품에 API 추가]
-   [호출 속도 제한 및 할당량 정책 구성][호출 속도 제한 및 할당량 정책 구성]
-   [제품 게시][제품 게시]
-   [제품에 개발자 계정 구독][제품에 개발자 계정 구독]
-   [작업을 호출하여 속도 제한 테스트][작업을 호출하여 속도 제한 테스트]
-   [다음 단계][다음 단계]

## <a name="create-product"> </a>제품 만들기

이 단계에서는 구독 승인을 요구하지 않는 무료 평가판 제품을 만듭니다.

시작하려면 Azure 포털에서 API 관리 서비스에 대한 **관리 콘솔**을 클릭합니다. API 관리의 관리용 포털이 열립니다.

> 아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][Azure API 관리 시작] 자습서의 [API 관리 서비스 인스턴스 만들기][API 관리 서비스 인스턴스 만들기]를 참조하세요.

![API 관리 콘솔][API 관리 콘솔]

왼쪽의 **API 관리** 메뉴에서 **제품**을 클릭하여 **제품** 페이지를 표시합니다.

![제품 추가][제품 추가]

**제품 추가**를 클릭하여 **새 제품 추가** 팝업 창을 표시합니다.

![새 제품 추가][새 제품 추가]

**제목** 텍스트 상자에 **무료 평가판**을 입력합니다.

**설명** 텍스트 상자에 **Subscribers will be able to run 10 calls/minute up to a maximum of 200 calls/week after which access is denied.**를 입력합니다.

관리자가 이 제품에 대한 구독 시도를 검토하고 허용하거나 거부하도록 하려면 **구독 승인 필요**를 선택합니다. 상자의 선택을 취소하면 구독 시도가 자동으로 승인됩니다. 이 예제에서는 승인이 자동으로 승인되므로 이 상자를 선택하지 마세요.

모든 값을 입력한 후에는 **저장**을 클릭하여 제품을 만듭니다.

![제품 추가됨][제품 추가됨]

기본적으로 새 제품은 **관리자** 그룹의 사용자에게 표시됩니다. 여기서는 **개발자** 그룹에 추가하겠습니다. **무료 평가판**을 클릭하고 **표시 여부** 탭을 선택합니다.

> API 관리에서 그룹은 개발자에 대한 제품 표시 여부를 관리하는 데 사용됩니다. 제품은 그룹에 대한 표시 여부를 부여하고, 개발자는 자신이 속한 그룹에게 표시되는 제품을 보고 구독할 수 있습니다. 자세한 내용은 [Azure API 관리에서 그룹을 만들고 사용하는 방법][Azure API 관리에서 그룹을 만들고 사용하는 방법](영문)을 참조하세요.

![개발자 그룹 추가][개발자 그룹 추가]

**개발자** 그룹을 선택하고 **저장**을 클릭합니다.

## <a name="add-api"> </a>제품에 API 추가

이 자습서 단계에서는 새 무료 평가판 제품에 Echo API를 추가합니다.

> 각 API 관리 서비스 인스턴스는 실험해 보고 API 관리에 대해 알아보는 데 사용할 수 있는 Echo API가 미리 구성되어 제공됩니다. 자세한 내용은 [Azure API 관리 시작][Azure API 관리 시작]을 참조하세요.

왼쪽의 **API 관리** 메뉴에서 **제품**을 클릭하고 **무료 평가판**을 클릭하여 제품을 구성합니다.

![제품 구성][제품 구성]

**제품에 API 추가**를 클릭합니다.

![제품에 API 추가][1]

**Echo API** 옆에 있는 상자를 선택하고 **저장**을 클릭합니다.

![Echo API 추가][Echo API 추가]

## <a name="policies"> </a>호출 속도 제한 및 할당량 정책 구성

속도 제한 및 할당량은 정책 편집기에서 구성됩니다. 왼쪽의 **API 관리** 메뉴에서 **정책**을 클릭하고 **정책 범위 제품** 드롭다운에서 **무료 평가판**을 선택합니다.

![제품 정책][제품 정책]

**정책 추가**를 클릭하여 정책 템플릿을 가져오고 속도 제한 및 할당량 정책을 만들기 시작합니다.

![정책 추가][정책 추가]

정책을 삽입하려면 정책 템플릿의 **inbound** 또는 **outbound** 섹션에 커서를 놓습니다. 속도 제한 및 할당량 정책은 인바운드 정책이므로, 인바운드 요소에 커서를 놓습니다.

![정책 편집기][정책 편집기]

이 자습서에서 추가할 두 개의 정책은 **호출 속도 제한** and **사용 할당량 설정** 정책입니다.

![정책 설명][정책 설명]

**inbound** 정책 요소에 커서가 놓이면 **호출 속도 제한** 옆에 있는 화살표를 클릭하여 정책 템플릿을 삽입합니다.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**호출 속도 제한**은 제품 수준에서 사용할 수 있고 API 및 개별 작업 이름 수준에서도 사용할 수 있습니다. 이 자습서에서는 제품 수준 정책만 사용되므로, 다음 예제처럼 **rate-limit** 요소에서 **api** 및 **operation** 요소를 삭제합니다.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

**무료 평가판** 제품에서 최대 허용 호출 속도는 분당 호출 10개이므로 호출 특성 값으로 **10**을 입력하고 **renewal-period** 특성으로 **60**을 입력합니다.

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

**사용 할당량 설정** 정책을 구성하려면 **inbound** 요소에서 새로 추가된 **rate-limit** 요소 바로 아래에 커서를 놓고 **사용 할당량 설정** 왼쪽의 화살표를 클릭합니다.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

이 정책은 제품 수준에서도 사용하기 때문에 다음 예제처럼 **api** 및 **operation** 이름 요소를 삭제합니다.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

할당량은 간격이나 대역폭 기준 또는 간격과 대역폭 기준의 호출 수에 기반합니다. 이 자습서에서는 대역폭 기반으로 제한하지 않으므로 **bandwidth** 특성을 삭제합니다.

    <quota calls="number" renewal-period="seconds">
    </quota>

**무료 평가판** 제품에서 할당량은 주당 호출 200개입니다. calls 특성 값으로 **200**을 지정하고, renewal-period 값으로 **604800**을 지정합니다.

    <quota calls="200" bandwidth="kilobytes" renewal-period="604800">
    </quota>

> 정책 간격은 초 단위로 지정합니다. 일주일의 간격을 계산하려면 일수(7), 하루의 시간 수(24), 한 시간의 분 수(60) 및 일 분의 초 수(60)를 곱하면 됩니다. 7 \* 24 \* 60 \* 60 = 604800.

정책 구성을 마치면 다음 예제와 같아집니다.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />
        
    </inbound>
    <outbound>
        
        <base />
        
        </outbound>
    </policies>

원하는 정책이 구성되면 **저장**을 클릭합니다.

![정책 저장][정책 저장]

## <a name="publish-product"> </a>제품 게시

API를 추가하고 정책을 구성했으며 이제 개발자가 제품을 사용할 수 있습니다. 개발자가 제품을 사용하려면 먼저 제품을 게시해야 합니다. 왼쪽의 **API 관리** 메뉴에서 **제품**을 클릭하고 **무료 평가판**을 클릭하여 제품을 구성합니다.

![제품 구성][제품 구성]

**게시**를 클릭한 후 **지금 게시**를 클릭하여 확인합니다.

![제품 게시][2]

## <a name="subscribe-account"> </a>제품에 개발자 계정 구독

제품을 게시했으며 이제 제품을 구독하고 개발자가 제품을 사용할 수 있습니다.

> API 관리 인스턴스의 관리자는 모든 제품에 자동으로 구독 등록됩니다. 이 자습서 단계에서는 비관리자 개발자 계정 중 하나를 무료 평가판 제품에 구독시킵니다. 개발자 계정이 관리자 역할의 일부인 경우에는 이미 구독 등록되었더라도 이 단계를 따르면 됩니다.

왼쪽의 **API 관리** 메뉴에서 **개발자**를 클릭하고 개발자 계정의 이름을 클릭합니다. 이 예제에서는 **Clayton Gragg** 개발자 계정을 사용합니다.

![개발자 구성][개발자 구성]

**구독 추가**를 클릭합니다.

![구독 추가][구독 추가]

**무료 평가판** 옆에 있는 상자를 선택하고 **구독**을 클릭합니다.

![구독 추가][3]

## <a name="test-rate-limit"> </a>작업을 호출하여 속도 제한 테스트

무료 평가판 제품을 구성하고 게시했으며 이제 일부 작업을 호출하고 속도 제한 정책을 테스트할 수 있습니다. 오른쪽 위에 있는 메뉴에서 **개발자 포털**을 클릭하여 개발자 포털로 전환합니다.

![개발자 포털][개발자 포털]

상단 메뉴에서 **API**를 클릭하고 **Echo API**를 선택합니다.

![개발자 포털][4]

> API 한 개만 구성했거나 계정에 표시한 경우에는 API를 클릭하면 해당 API에 대한 작업으로 직접 연결됩니다.

**GET Resource** 작업을 선택하고 **콘솔 시작**을 클릭합니다.

![콘솔 시작][콘솔 시작]

기본 매개 변수 값을 유지하고 **무료 평가판** 제품의 구독 키를 선택합니다.

![구독 키][구독 키]

> 구독이 여러 개 있는 경우에는 **무료 평가판**의 키를 선택합니다. 그렇지 않은 경우 이전 단계에 구성한 정책이 적용되지 않습니다.

**HTTP Get**을 클릭하고 응답을 봅니다. **응답 상태** **200 OK**입니다.

![작업 결과][작업 결과]

속도 제한 정책인 분당 호출 10개를 초과하는 속도의 **HTTP Get**을 클릭합니다. 속도 제한 정책이 초과하면 응답 상태 **429 요청이 너무 많음**이 반환됩니다.

![작업 결과][5]

**응답 헤더** 및 **응답 콘텐츠**는 재시도에 성공하기 전의 남은 간격을 나타냅니다.

속도 제한 정책인 분당 호출 10개가 적용되는 경우 속도 제한이 초과하기 전에 처음 10개의 제품 호출에 성공한 후 60초가 경과할 때까지 후속 호출에 실패합니다. 이 예제에서는 남은 간격이 43초입니다.

## <a name="next-steps"> </a>다음 단계

-   [고급 API 구성 시작][고급 API 구성 시작] 자습서에서 다른 항목을 확인하세요.

  [제품 만들기]: #create-product
  [제품에 API 추가]: #add-api
  [호출 속도 제한 및 할당량 정책 구성]: #policies
  [제품 게시]: #publish-product
  [제품에 개발자 계정 구독]: #subscribe-account
  [작업을 호출하여 속도 제한 테스트]: #test-rate-limit
  [다음 단계]: #next-steps
  [Azure API 관리 시작]: ../api-management-get-started
  [API 관리 서비스 인스턴스 만들기]: ../api-management-get-started/#create-service-instance
  [API 관리 콘솔]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
  [제품 추가]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
  [새 제품 추가]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
  [제품 추가됨]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
  [Azure API 관리에서 그룹을 만들고 사용하는 방법]: ../api-management-howto-create-groups
  [개발자 그룹 추가]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
  [제품 구성]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
  [1]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
  [Echo API 추가]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
  [제품 정책]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
  [정책 추가]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
  [정책 편집기]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
  [정책 설명]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
  [정책 저장]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
  [2]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
  [개발자 구성]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
  [구독 추가]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
  [3]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
  [개발자 포털]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
  [4]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
  [콘솔 시작]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
  [구독 키]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
  [작업 결과]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
  [5]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
  [고급 API 구성 시작]: ../api-management-get-started-advanced

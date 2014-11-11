<properties pageTitle="How create and publish a product in Azure API Management" metaKeywords="" description="Learn how to create and publish products in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How create and publish a product in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Azure API 관리에서 제품을 만들고 게시하는 방법

Azure API 관리(미리 보기)에서 제품은 하나 이상의 API뿐만 아니라 사용 할당량 및 사용 약관을 포함합니다. 제품이 게시되면 개발자는 제품을 구독하고 제품의 API를 사용할 수 있습니다. 이 항목에서는 제품 생성, API 추가 및 개발자를 위한 게시를 안내해 줍니다.

## 항목 내용

- [제품 만들기][제품 만들기]
- [제품에 API 추가][제품에 API 추가]
- [제품에 설명적인 정보 추가][제품에 설명적인 정보 추가]
- [제품 게시][제품 게시]
- [개발자에게 제품 표시][개발자에게 제품 표시]
- [제품 구독자 보기][제품 구독자 보기]
- [다음 단계][다음 단계]

## <a name="create-product"></a>제품 만들기

관리 콘솔에서 작업을 API에 추가하고 구성합니다. 관리 콘솔에 액세스하려면 Azure 포털에서 API 관리 서비스에 대한 **관리 콘솔**을 클릭합니다.

>아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][Azure API 관리 시작] 자습서의 [API 관리 서비스 인스턴스 만들기][API 관리 서비스 인스턴스 만들기]를 참조하세요.

![API 관리 콘솔][API 관리 콘솔]

왼쪽 메뉴에서 **제품**을 클릭하여 **제품** 페이지를 표시하고 **제품 추가**를 클릭합니다.

![제품][제품]

![새 제품][새 제품]

**이름** 필드에 제품에 대한 설명적인 이름을 입력하고 **설명** 필드에 제품에 대한 설명을 입력합니다.

관리자가 이 제품에 대한 구독을 검토하고 허용하거나 거부하도록 하려면 **구독 승인 필요**를 선택합니다. 상자의 선택을 취소하면 구독 시도가 자동으로 승인됩니다. 구독에 대한 자세한 내용은 [제품 구독자 보기][제품 구독자 보기]를 참조하세요.

![제품][1]

>기본적으로 새 제품은 게시되지 않으며 **관리자** 그룹에만 표시됩니다.

제품을 구성하려면 **제품** 탭에서 제품 이름을 클릭합니다.

## <a name="add-apis"></a>제품에 API 추가

**제품** 페이지에는 구성할 수 있는 4개의 링크 즉, **요약**, **설정**, **표시** 및 **개발자**가 있습니다. **요약** 탭에서는 API를 추가하고 제품을 게시하거나 게시 취소할 수 있습니다.

![요약][요약]

제품을 게시하기 전에 API를 하나 이상 추가해야 합니다. 이렇게 하려면 **제품에 API 추가**를 클릭합니다.

![API 추가][API 추가]

원하는 API를 선택하고 **저장**을 클릭합니다.

## <a name="add-description"></a>제품에 설명적인 정보 추가

**설정** 탭에서 용도, 제품을 통해 액세스할 수 있는 API, 기타 유용한 정보 등의 자세한 정보를 제공할 수 있습니다. 콘텐츠는 API를 호출하는 개발자를 대상으로 하며 일반 텍스트 또는 HTML 표시로 작성할 수 있습니다.

![제품 설정][제품 설정]

모든 제품 구독 요청을 수동으로 승인하려면 **구독 승인 필요**를 선택합니다. 기본적으로 모든 제품 구독은 자동으로 부여됩니다.

선택적으로 구독자가 제품을 사용하기 위해 허용해야 하는 제품의 사용 약관을 설명하는 **사용 약관** 필드를 채웁니다.

## <a name="publish-product"></a>제품 게시

제품의 API를 호출하려면 먼저 제품을 게시해야 합니다. 제품의 **요약** 탭에서 **게시**를 클릭한 다음 **예, 게시**를 클릭하여 확인합니다. 이전에 게시한 제품을 비공개로 설정하려면 **게시 취소**를 클릭합니다.

![제품 게시][2]

## <a name="make-visible"></a>개발자에게 제품 표시

**표시** 탭에서, 개발자 포털의 제품을 보고 제품을 구독할 수 있는 역할을 선택할 수 있습니다.

![제품 표시][제품 표시]

그룹의 개발자에 대한 제품 표시를 사용하거나 사용하지 않으려면 그룹 옆에 있는 확인란을 선택하거나 선택 취소한 다음 **저장**을 클릭합니다.

>자세한 내용은 [Azure API 관리에서 개발자 계정을 관리하는 그룹을 만들고 사용하는 방법][Azure API 관리에서 개발자 계정을 관리하는 그룹을 만들고 사용하는 방법]을 참조하세요.

## <a name="view-subscribers"></a>제품 구독자 보기

**개발자** 탭에서는 그룹을 구독한 개발자를 나열합니다. 개발자 이름을 클릭하여 각 개발자에 대한 세부 정보 및 설정을 볼 수 있습니다. 이 예제에서는 제품을 구독하는 개발자가 아직 없습니다.

![개발자][개발자]

## <a name="next-steps"></a>다음 단계

원하는 API가 추가되고 제품이 게시되면 개발자는 제품을 구독하고 API를 호출할 수 있습니다. 이러한 항목 및 고급 제품 구성을 설명하는 자습서에 대해서는 [Azure API 관리에서 고급 제품 설정을 만들고 구성하는 방법][Azure API 관리에서 고급 제품 설정을 만들고 구성하는 방법]을 참조하세요.

[제품 만들기]: #create-product
[제품에 API 추가]: #add-apis
[제품에 설명적인 정보 추가]: #add-description
[제품 게시]: #publish-product
[개발자에게 제품 표시]: #make-visible
[제품 구독자 보기]: #view-subscribers
[다음 단계]: #next-steps
[Azure API 관리 시작]: ../api-management-get-started
[API 관리 서비스 인스턴스 만들기]: ../api-management-get-started/#create-service-instance
[API 관리 콘솔]: ./media/api-management-howto-add-products/api-management-management-console.png
[제품]: ./media/api-management-howto-add-products/api-management-products.png
[새 제품]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[1]: ./media/api-management-howto-add-products/api-management-products-page.png
[요약]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[API 추가]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[제품 설정]: ./media/api-management-howto-add-products/api-management-product-settings.png
[2]: ./media/api-management-howto-add-products/api-management-publish-product.png
[제품 표시]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[Azure API 관리에서 개발자 계정을 관리하는 그룹을 만들고 사용하는 방법]: ../api-management-howto-create-groups
[개발자]: ./media/api-management-howto-add-products/api-management-developer-list.png
[Azure API 관리에서 고급 제품 설정을 만들고 구성하는 방법]: ../api-management-howto-product-with-rules

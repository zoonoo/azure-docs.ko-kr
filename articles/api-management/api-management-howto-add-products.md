---
title: "Azure API 관리에서 제품을 만들고 게시하는 방법"
description: "Azure API 관리에서 제품을 만들고 게시하는 방법에 대해 알아봅니다."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 31de55cb-9384-490b-a2f2-6dfcf83da764
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: 1568bbba999018e26493654e0286a6b8d93f3ae7


---
# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Azure API 관리에서 제품을 만들고 게시하는 방법
Azure API 관리에서 제품은 하나 이상의 API뿐만 아니라 사용 할당량 및 사용 약관을 포함합니다. 제품이 게시되면 개발자는 제품을 구독하고 제품의 API를 사용할 수 있습니다. 이 항목에서는 제품 생성, API 추가 및 개발자를 위한 게시를 안내해 줍니다.

## <a name="create-product"> </a>제품 만들기
게시자 포털에서 작업을 API에 추가하고 구성합니다. 게시자 포털에 액세스하려면 API 관리 서비스에 대해 Azure Portal에서 **게시자 포털**을 클릭합니다.

![게시자 포털][api-management-management-console]

> 아직 API Management 서비스 인스턴스를 만들지 않은 경우 [Azure API Management 시작][Get started with Azure API Management] 자습서의 [API Management 서비스 인스턴스 만들기][Create an API Management service instance]를 참조하세요.
> 
> 

왼쪽 메뉴에서 **제품**을 클릭하여 **제품** 페이지를 표시하고 **제품 추가**를 클릭합니다.

![제품][api-management-products]

![새 제품][api-management-add-new-product]

**이름** 필드에 제품에 대한 설명적인 이름을 입력하고 **설명** 필드에 제품에 대한 설명을 입력합니다.

API Management에서 제품은 **개방형** 또는 **보호된** 제품일 수 있습니다. 보호된 제품은 사용하기 전에 구독해야 하는 반면, 개방형 제품은 구독하지 않고 사용할 수 있습니다. **구독 필요** 를 선택하여 구독이 필요한 보호된 제품을 만듭니다. 기본 설정입니다.

관리자가 이 제품에 대한 구독을 검토하고 허용하거나 거부하도록 하려면 **구독 승인 필요** 를 선택합니다. 상자의 선택을 취소하면 구독 시도가 자동으로 승인됩니다. 구독에 대한 자세한 내용은 [제품 구독자 보기][View subscribers to a product]를 참조하세요.

개발자 계정으로 제품을 여러 번 구독할 수 있도록 하려면 **여러 구독 허용** 확인란을 선택합니다. 이 확인란을 선택하지 않으면 각 개발자 계정은 제품을 한 번만 구독할 수 있습니다.

![무제한의 여러 구독][api-management-unlimited-multiple-subscriptions]

여러 동시 구독 수를 제한하려면 **동시 구독 제한 수** 확인란을 선택하고 구독 제한을 입력합니다. 다음 예제에서는 동시 구독이 개발자 계정당 4개로 제한됩니다.

![네 개의 여러 구독][api-management-four-multiple-subscriptions]

새 제품 옵션을 모두 구성한 다음에는 **저장** 을 클릭하여 새 제품을 만듭니다.

![제품][api-management-products-page]

> 기본적으로 새 제품은 게시되지 않으며 **관리자** 그룹에만 표시됩니다.
> 
> 

제품을 구성하려면 **제품** 탭에서 제품 이름을 클릭합니다.

## <a name="add-apis"> </a>제품에 여러 API 추가
**제품** 페이지에는 구성에 대한 4개의 링크인 **요약**, **설정**, **표시 여부** 및 **구독자** 링크가 포함됩니다. **요약** 탭에서는 API를 추가하고 제품을 게시하거나 게시를 취소할 수 있습니다.

![요약][api-management-new-product-summary]

제품을 게시하기 전에 API를 하나 이상 추가해야 합니다. 이렇게 하려면 **제품에 API 추가**를 클릭합니다.

![API 추가][api-management-add-apis-to-product]

원하는 API를 선택하고 **저장**을 클릭합니다.

## <a name="add-description"> </a>제품에 설명 정보 추가
**설정** 탭에서 용도, 제품을 통해 액세스할 수 있는 API, 기타 유용한 정보 등의 자세한 정보를 제공할 수 있습니다. 콘텐츠는 API를 호출하는 개발자를 대상으로 하며 일반 텍스트 또는 HTML 표시로 작성할 수 있습니다.

![제품 설정][api-management-product-settings]

**구독 필요** 를 선택하여 구독을 사용해야 하는 보호된 제품을 만들거나 확인란의 선택을 취소하여 구독 없이 호출할 수 있는 개방형 제품을 만듭니다.

모든 제품 구독 요청을 수동으로 승인하려면 **구독 승인 필요** 를 선택합니다. 기본적으로 모든 제품 구독은 자동으로 부여됩니다.

개발자 계정으로 제품을 여러 번 구독할 수 있도록 하려면 확인은 **여러 구독 허용** 확인란을 선택하고 필요에 따라 제한을 지정합니다. 이 확인란을 선택하지 않으면 각 개발자 계정은 제품을 한 번만 구독할 수 있습니다.

선택적으로 구독자가 제품을 사용하기 위해 허용해야 하는 제품의 사용 약관을 설명하는 **사용 약관** 필드를 채웁니다.

## <a name="publish-product"> </a>제품 게시
제품의 API를 호출하려면 먼저 제품을 게시해야 합니다. 제품의 **요약** 탭에서 **게시**를 클릭한 다음 **예, 게시**를 클릭하여 확인합니다. 이전에 게시한 제품을 비공개로 설정하려면 **게시 취소**를 클릭합니다.

![제품 게시][api-management-publish-product]

## <a name="make-visible"> </a>개발자에게 제품 표시
**표시** 탭에서, 개발자 포털의 제품을 보고 제품을 구독할 수 있는 역할을 선택할 수 있습니다.

![제품 표시][api-management-product-visiblity]

그룹의 개발자에 대한 제품 표시를 사용하거나 사용하지 않으려면 그룹 옆에 있는 확인란을 선택하거나 선택 취소한 다음 **저장**을 클릭합니다.

> 자세한 내용은 [Azure API Management에서 개발자 계정을 관리하는 그룹을 만들고 사용하는 방법][How to create and use groups to manage developer accounts in Azure API Management]을 참조하세요.
> 
> 

## <a name="view-subscribers"> </a>제품 구독자 보기
**구독자** 탭은 제품을 구독하는 개발자를 나열합니다. 개발자 이름을 클릭하여 각 개발자에 대한 세부 정보 및 설정을 볼 수 있습니다. 이 예제에서는 제품을 구독하는 개발자가 아직 없습니다.

![개발자][api-management-developer-list]

## <a name="next-steps"> </a>다음 단계
원하는 API가 추가되고 제품이 게시되면 개발자는 제품을 구독하고 API를 호출할 수 있습니다. 이러한 항목 및 고급 제품 구성을 설명하는 자습서에 대해서는 [AzureAPI Management에서 고급 제품 설정을 만들고 구성하는 방법][How create and configure advanced product settings in Azure API Management]을 참조하세요.

제품 사용에 대한 자세한 내용은 다음 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[How to create and use groups to manage developer accounts in Azure API Management]: api-management-howto-create-groups.md
[How create and configure advanced product settings in Azure API Management]: api-management-howto-product-with-rules.md 



<!--HONumber=Dec16_HO3-->



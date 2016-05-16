<properties 
	pageTitle="Azure API 관리에서 API, 작업 및 제품을 만드는 방법" 
	description="API 관리에서 API, 작업 및 제품을 만드는 방법을 알아봅니다." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2016" 
	ms.author="sdanie"/>

# Azure API 관리에서 API, 작업 및 제품을 만드는 방법

Azure API 관리에서 API 및 해당 작업이 제품에 추가되며, 이를 개발자가 사용하여 API를 사용하는 응용 프로그램을 작성할 수 있습니다. 이 섹션에서는 API를 만들고 이 API에 작업을 추가한 다음 API를 제품에 연결하고 개발자가 사용할 수 있도록 게시하는 방법을 안내합니다.

## <a name="create-apis"> </a>API를 만드는 방법

API 관리에서 API는 클라이언트 응용 프로그램이 호출할 수 있는 작업 집합을 나타냅니다. 새 API는 게시자 포털에서 만듭니다.

이 가이드에서는 API 관리에서 새 API를 만들고 구성하는 방법을 보여 줍니다.

-   [API를 만드는 방법][]

## <a name="add-operations"> </a>API에 작업을 추가하는 방법

API 관리에서 API를 사용하려면 먼저 작업을 추가해야 합니다. 이 가이드에서는 API 관리에서 다양한 유형의 작업을 API에 추가하고 구성하는 방법을 보여 줍니다.

-   [API에 작업을 추가하는 방법][]

API 및 해당 작업은 WADL 또는 Swagger 형식으로 한 번에 가져올 수도 있습니다.

-	[작업과 함께 API의 정의를 가져오는 방법][]

## <a name="add-product"> </a>제품을 만들고 게시하는 방법

API 관리에서 제품은 하나 이상의 API뿐만 아니라 사용 할당량 및 사용 약관을 포함합니다. 제품을 게시하고 나면 개발자는 제품을 구독하고 제품의 API를 사용할 수 있습니다. 이 항목에서는 제품을 만들고, API를 추가하고, 개발자를 위해 게시하는 방법에 대한 지침을 제공합니다.

-   [제품을 추가하고 게시하는 방법][]
-	[고급 제품 설정을 만들고 구성하는 방법][]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-]: ./media/

[API를 만드는 방법]: api-management-howto-create-apis.md
[API에 작업을 추가하는 방법]: api-management-howto-add-operations.md
[제품을 추가하고 게시하는 방법]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[작업과 함께 API의 정의를 가져오는 방법]: api-management-howto-import-api.md
[고급 제품 설정을 만들고 구성하는 방법]: api-management-howto-product-with-rules.md

<!---HONumber=AcomDC_0504_2016-->
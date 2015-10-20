<properties
	pageTitle="Azure API 관리에서 캐싱을 추가하여 성능 향상 | Microsoft Azure"
	description="대기 시간, 대역폭 사용 및 API 관리 서비스 호출에 대한 웹 서비스 부하를 개선하는 방법에 대해 알아봅니다."
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
	ms.topic="get-started-article"
	ms.date="08/05/2015"
	ms.author="sdanie"/>

# Azure API 관리에서 캐싱을 추가하여 성능 향상

응답 캐싱을 위해 API 관리의 작업을 구성할 수 있습니다. 응답 캐싱은 그다지 사용되지 않는 데이터에 대한 API 대기 시간, 대역폭 사용량 및 웹 서비스 부하를 상당히 줄일 수 있습니다.

이 가이드에서는 API에 대해 응답 캐싱을 추가하고 샘플 Echo API 작업에 대한 정책을 구성하는 방법을 보여 줍니다. 그런 다음 개발자 포털에서 작업을 호출하여 캐싱 작동을 확인할 수 있습니다.

## 필수 조건

이 가이드의 단계를 수행하기 전에 API와 제품이 구성된 API 관리 서비스 인스턴스가 있어야 합니다. 아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][] 자습서의 [API 관리 서비스 인스턴스 만들기][]를 참조하세요.

## <a name="configure-caching"> </a>캐싱을 위해 작업 구성

이 단계에서는 샘플 Echo API의 **GET Resource(캐시됨)** 작업에 대한 캐싱 설정을 검토합니다.

>[AZURE.NOTE]각 API 관리 서비스 인스턴스는 실험해 보고 API 관리에 대해 알아보는 데 사용할 수 있는 Echo API가 미리 구성되어 제공됩니다. 자세한 내용은 [Azure API 관리 시작][]을 참조하세요.

시작하려면 API 관리 서비스에 대해 Azure 포털에서 **관리**를 클릭합니다. API 관리 게시자 포털로 이동됩니다.

![게시자 포털][api-management-management-console]

왼쪽의 **API 관리** 메뉴에서 **API**를 클릭한 다음 **Echo API**를 클릭합니다.

![Echo API][api-management-echo-api]

**작업** 탭을 클릭한 다음 **작업** 목록에서 **GET Resource(캐시됨)** 작업을 클릭합니다.

![Echo API 작업][api-management-echo-api-operations]

**캐싱** 탭을 클릭하여 이 작업에 대한 캐싱 설정을 봅니다.

![캐싱 탭][api-management-caching-tab]

작업에 대한 캐싱을 사용하려면 **사용** 확인란을 선택합니다. 이 예제에서는 캐싱이 사용됩니다.

각 작업 응답은 **쿼리 문자열 매개 변수에 따라 다름** 및 **헤더에 따라 다름** 필드의 값을 기반으로 키가 지정됩니다. 쿼리 문자열 매개 변수 또는 헤더를 기반으로 하여 여러 응답을 캐시하려는 경우 이러한 두 필드에서 응답을 구성할 수 있습니다.

**기간**은 캐싱된 응답의 만료 간격을 지정합니다. 이 예제에서 간격은 1시간에 해당하는 **3600**초입니다.

이 예제에서 캐싱 구성을 사용하면 **GET Resource(캐시됨)** 작업에 대한 첫 번째 요청이 백 엔드 서비스의 응답을 반환합니다. 이 응답은 지정된 헤더 및 쿼리 문자열 매개 변수를 통해 캐시 및 입력됩니다. 일치하는 매개 변수를 사용하는, 작업에 대한 후속 호출은 캐시 기간 간격이 만료될 때까지 캐시된 응답을 반환합니다.

## <a name="caching-policies"> </a>캐싱 정책 검토

이 단계에서는 샘플 Echo API의 **GET Resource(캐시됨)** 작업에 대한 캐싱 설정을 검토합니다.

**캐싱** 탭에서 작업에 대한 캐싱 설정을 구성한 경우 작업에 대한 캐싱 정책이 추가됩니다. 정책 편집기에서 이러한 정책을 보고 편집할 수 있습니다.

왼쪽의 **API 관리** 메뉴에서 **정책**을 클릭하고 **작업** 드롭다운 목록에서 **Echo API/GET Resource(캐시됨)**를 선택합니다.

![정책 범위 작업][api-management-operation-dropdown]

그러면 정책 편집기에 이 작업에 대한 정책이 표시됩니다.

![API 관리 정책 편집기][api-management-policy-editor]

이 작업에 대한 정책 정의에는 이전 단계에서 **캐싱** 탭을 사용하여 검토한 캐싱 구성을 정의하는 정책이 포함됩니다.

	<policies>
		<inbound>
			<base />
			<cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
				<vary-by-header>Accept</vary-by-header>
				<vary-by-header>Accept-Charset</vary-by-header>
			</cache-lookup>
			<rewrite-uri template="/resource" />
		</inbound>
		<outbound>
			<base />
			<cache-store caching-mode="cache-on" duration="3600" />
		</outbound>
	</policies>

>[AZURE.NOTE]정책 편집기의 캐싱 정책 변경 내용은 작업의 **캐싱** 탭에 반영되며, 그 반대도 가능합니다.

## <a name="test-operation"> </a>작업 호출 및 캐싱 테스트

적용 중인 캐싱을 보려면 개발자 포털에서 작업을 호출할 수 있습니다. 오른쪽 위 메뉴에서 **개발자 포털**을 클릭합니다.

![개발자 포털][api-management-developer-portal-menu]

상단 메뉴에서 **API**를 클릭하고 **Echo API**를 선택합니다.

![Echo API][api-management-apis-echo-api]

>API 한 개만 구성했거나 계정에 표시한 경우에는 API를 클릭하면 해당 API에 대한 작업으로 직접 연결됩니다.

**GET Resource(캐시됨)** 작업을 선택하고 **콘솔 열기**를 클릭합니다.

![콘솔 시작][api-management-open-console]

콘솔을 통해 개발자 포털에서 직접 작업을 호출할 수 있습니다.

![콘솔][api-management-console]

**param1** 및 **param2**에 대한 기본값을 그대로 유지합니다.

**구독 키** 드롭다운 목록에서 원하는 키를 선택합니다. 계정에 구독이 하나만 있는 경우 이미 선택되어 있습니다.

**요청 헤더** 입력란에 **sampleheader:value1**을 입력합니다.

**HTTP Get**을 클릭하고 응답 헤더를 기록합니다.

**요청 헤더** 입력란에 **sampleheader:value2**를 입력하고 **HTTP Get**을 클릭합니다.

**sampleheader**의 값은 응답에서 여전히 **value1**입니다. 다른 값을 몇 가지 시도해도 첫 번째 호출에서 캐시된 응답이 반환됩니다.

**param2** 필드에 **25**를 입력하고 **HTTP Get**을 클릭합니다.

응답의 **sampleheader** 값은 이제 **value2**입니다. 작업 결과가 쿼리 문자열을 통해 입력되므로, 이전의 캐시된 응답은 반환되지 않았습니다.

## <a name="next-steps"> </a>다음 단계

-	[고급 API 구성 시작][] 자습서에서 다른 항목을 확인하세요.
-	캐싱 정책에 대한 자세한 내용은 [API 관리 정책 참조][]의 [캐싱 정책][]을 참조하세요.

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[API 관리 서비스 인스턴스 만들기]: api-management-get-started.md
[Azure API 관리 시작]: api-management-get-started.md
[고급 API 구성 시작]: api-management-get-started-advanced.md

[캐싱 정책]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[API 관리 정책 참조]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Azure API 관리 시작]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps

<!---HONumber=Oct15_HO3-->
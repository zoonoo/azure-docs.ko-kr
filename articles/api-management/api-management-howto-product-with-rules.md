<properties
	pageTitle="Azure API 관리로 API 보호 | Microsoft Azure"
	description="할당량 및 제한(속도 제한) 정책을 사용하여 API를 보호하는 방법을 알아봅니다."
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
	ms.topic="get-started-article"
	ms.date="08/24/2016"
	ms.author="sdanie"/>

# Azure API 관리를 사용하여 속도 제한으로 API 보호

이 가이드에서는 Azure API 관리로 속도 제한 및 할당량 정책을 구성하여 백엔드 API에 대한 보호를 추가하기가 얼마나 쉬운지 보여줍니다.

이 자습서에서는 개발자가 [구독별 호출 속도 제한](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) 및 [구독별 사용 할당량 설정](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) 정책을 사용하여 API를 분당 최대 10번 및 주당 최대 200번까지 호출할 수 있는 "무료 평가판" API 제품을 만들어 봅니다. 그런 다음 API를 게시하고 속도 제한 정책을 테스트합니다.

[rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) 및 [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) 정책을 사용하는 고급 제한 시나리오는 [Azure API 관리로 고급 요청 제한](api-management-sample-flexible-throttling.md)을 참조하세요.

## <a name="create-product"> </a>제품 만들기

이 단계에서는 구독 승인을 요구하지 않는 무료 평가판 제품을 만듭니다.

>[AZURE.NOTE] 이미 구성된 제품이 있어 이 자습서에 사용하려는 경우 무료 평가판 제품 대신 해당 제품을 사용하여 [호출 속도 제한 및 할당량 정책 구성][]으로 바로 이동한 다음 해당 단계에서부터 자습서를 진행할 수 있습니다.

시작하려면 API 관리 서비스에 대해 Azure 클래식에서 **관리**를 클릭합니다. API 관리 게시자 포털로 이동됩니다.

![게시자 포털][api-management-management-console]

>아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리의 첫 번째 API 관리][] 자습서에서 [API 관리 서비스 인스턴스 만들기][]를 참조하세요.

왼쪽의 **API 관리** 메뉴에서 **제품**을 클릭하여 **제품** 페이지를 표시합니다.

![제품 추가][api-management-add-product]

**제품 추가**를 클릭하여 **새 제품 추가** 대화 상자를 표시합니다.

![새 제품 추가][api-management-new-product-window]

**제목** 상자에 **무료 평가판**을 입력합니다.

**설명** 상자에 **구독자는 분당 10회, 1주일에 최대 200회의 호출을 실행할 수 있습니다. 그 이후에는 액세스가 거부됩니다.** 를 입력합니다.

API 관리의 제품은 보호되거나 개방될 수 있습니다. 사용하기 전에 먼저 보호된 제품을 구독할 수 있어야 합니다. 개방된 제품은 구독하지 않고 사용할 수 있습니다. 구독이 필요한 보호된 제품을 만들기 위해 **구독 필요**가 선택되었는지 확인하세요. 기본 설정입니다.

관리자가 이 제품에 대한 구독 시도를 검토하고 허용하거나 거부하도록 하려면 **구독 승인 필요**를 선택합니다. 확인란을 선택하지 않으면 구독 시도가 자동으로 승인됩니다. 이 예제에서는 승인이 자동으로 승인되므로 이 확인란을 선택하지 않습니다.

개발자 계정으로 새 제품을 여러 번 구독할 수 있도록 하려면 **여러 동시 구독 허용** 확인란을 선택합니다. 이 자습서는 여러 동시 구독을 활용하지 않으므로 해당 항목을 선택하지 않습니다.

모든 값을 입력한 후에는 **저장**을 클릭하여 제품을 만듭니다.

![제품 추가됨][api-management-product-added]

기본적으로 새 제품은 **관리자** 그룹의 사용자에게 표시됩니다. 여기서는 **개발자** 그룹에 추가하겠습니다. **무료 평가판**을 클릭한 다음 **표시 여부** 탭을 클릭합니다.

>API 관리에서 그룹은 개발자에 대한 제품 표시 여부를 관리하는 데 사용됩니다. 제품은 그룹에 대한 표시 여부를 부여하고, 개발자는 자신이 속한 그룹에게 표시되는 제품을 보고 구독할 수 있습니다. 자세한 내용은 [Azure API 관리에서 그룹을 만들고 사용하는 방법][]\(영문)을 참조하세요.

![개발자 그룹 추가][api-management-add-developers-group]

**개발자** 확인란을 선택한 다음, **저장**을 클릭합니다.

## <a name="add-api"> </a>제품에 API를 추가하려면

이 자습서 단계에서는 새 무료 평가판 제품에 Echo API를 추가합니다.

>각 API 관리 서비스 인스턴스는 실험해 보고 API 관리에 대해 알아보는 데 사용할 수 있는 Echo API가 미리 구성되어 제공됩니다. 자세한 내용은 [Azure API 관리에서 첫 번째 API 관리][]를 참조하세요.

왼쪽의 **API 관리** 메뉴에서 **제품**을 클릭한 다음 **무료 평가판**을 클릭하여 제품을 구성합니다.

![제품 구성][api-management-configure-product]

**제품에 API 추가**를 클릭합니다.

![제품에 API 추가][api-management-add-api]

**Echo API**를 선택하고 **저장**을 클릭합니다.

![Echo API 추가][api-management-add-echo-api]

## <a name="policies"> </a>호출 속도 제한 및 할당량 정책을 구성하려면

속도 제한 및 할당량은 정책 편집기에서 구성됩니다. 왼쪽의 **API 관리** 메뉴 아래에 있는 **정책**을 클릭합니다. **제품** 목록에서 **무료 평가판**을 클릭합니다.

![제품 정책][api-management-product-policy]

**정책 추가**를 클릭하여 정책 템플릿을 가져오고 속도 제한 및 할당량 정책을 만들기 시작합니다.

![정책 추가][api-management-add-policy]

정책을 삽입하려면 정책 템플릿의 **inbound** 또는 **outbound** 섹션에 커서를 놓습니다. 속도 제한 및 할당량 정책은 인바운드 정책이므로, 인바운드 요소에 커서를 놓습니다.

![정책 편집기][api-management-policy-editor-inbound]

이 자습서에서 추가할 두 개의 정책은 [구독당 호출 속도 제한](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) 및 [구독당 사용 할당량 설정](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) 정책입니다.

![정책 설명][api-management-limit-policies]

**인바운드** 정책 요소에 커서가 놓이면 **구독당 호출 속도 제한** 옆에 있는 화살표를 클릭하여 정책 템플릿을 삽입합니다.

	<rate-limit calls="number" renewal-period="seconds">
	<api name="name" calls="number">
	<operation name="name" calls="number" />
	</api>
	</rate-limit>

**구독당 호출 속도 제한**은 제품 수준에서 사용할 수 있고 API 및 개별 작업 이름 수준에서도 사용할 수 있습니다. 이 자습서에서는 제품 수준 정책만 사용되므로, 다음 예제처럼 **rate-limit** 요소에서 **api** 및 **operation** 요소를 삭제하여 외부 **rate-limit** 요소만 남게 합니다.

	<rate-limit calls="number" renewal-period="seconds">
	</rate-limit>

**무료 평가판** 제품에서 최대 허용 호출 속도는 분당 호출 10개이므로 호출 특성 값으로 **10**을 입력하고 **renewal-period** 특성으로 **60**을 입력합니다.

	<rate-limit calls="10" renewal-period="60">
	</rate-limit>

**구독당 사용 할당량 설정** 정책을 구성하려면 **인바운드** 요소 내에서 새로 추가된 **rate-limit** 요소 바로 아래에 커서를 놓은 다음, **구독당 사용 할당량 설정** 왼쪽의 화살표를 클릭합니다.

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

무료 평가판 제품에서 할당량은 주당 호출 200개입니다. **calls** 특성 값으로 **200**을 지정한 다음, **renewal-period** 값으로 **604800**을 지정합니다.

	<quota calls="200" renewal-period="604800">
	</quota>

>정책 간격은 초 단위로 지정합니다. 일주일의 간격을 계산하려면 일수(7), 하루의 시간 수(24), 한 시간의 분 수(60) 및 일 분의 초 수(60)를 곱하면 됩니다. 즉, 7 * 24 * 60 * 60 = 604800이 됩니다.

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

원하는 정책이 구성된 후 **저장**을 클릭합니다.

![정책 저장][api-management-policy-save]

## <a name="publish-product"> </a>제품을 게시하려면

이제 API를 추가하고 정책을 구성했으며, 개발자가 제품을 사용할 수 있도록 해당 제품을 게시해야 합니다. 왼쪽의 **API 관리** 메뉴에서 **제품**을 클릭한 다음 **무료 평가판**을 클릭하여 제품을 구성합니다.

![제품 구성][api-management-configure-product]

**게시**를 클릭한 후 **지금 게시**를 클릭하여 확인합니다.

![제품 게시][api-management-publish-product]

## <a name="subscribe-account"> </a>제품에 개발자 계정을 구독하려면

제품을 게시했으며 이제 제품을 구독하고 개발자가 제품을 사용할 수 있습니다.

>API 관리 인스턴스의 관리자는 모든 제품에 자동으로 구독 등록됩니다. 이 자습서 단계에서는 비관리자 개발자 계정 중 하나를 무료 평가판 제품에 구독시킵니다. 개발자 계정이 관리자 역할의 일부인 경우에는 이미 구독 등록되었더라도 이 단계를 따르면 됩니다.

왼쪽의 **API 관리** 메뉴에서 **사용자**를 클릭한 다음, 개발자 계정의 이름을 클릭합니다. 이 예제에서는 **Clayton Gragg** 개발자 계정을 사용합니다.

![개발자 구성][api-management-configure-developer]

**구독 추가**를 클릭합니다.

![구독 추가][api-management-add-subscription-menu]

**무료 평가판**을 선택한 다음, **구독**을 클릭합니다.

![구독 추가][api-management-add-subscription]

>[AZURE.NOTE] 이 자습서에서는 무료 평가판 제품에 대해 여러 동시 구독을 사용하도록 설정하지 않습니다. 사용하도록 설정한 경우 다음 예제처럼 구독의 이름을 묻는 메시지가 표시됩니다.

![구독 추가][api-management-add-subscription-multiple]

**구독**을 클릭하면 제품이 사용자의 **구독** 목록에 표시됩니다.

![구독 추가됨][api-management-subscription-added]

## <a name="test-rate-limit"> </a>작업을 호출하고 속도 제한을 테스트하려면

무료 평가판 제품을 구성하고 게시했으며 이제 일부 작업을 호출하고 속도 제한 정책을 테스트할 수 있습니다. 오른쪽 위에 있는 메뉴에서 **개발자 포털**을 클릭하여 개발자 포털로 전환합니다.

![개발자 포털][api-management-developer-portal-menu]

상단 메뉴에서 **API**를 클릭한 다음 **Echo API**를 클릭합니다.

![개발자 포털][api-management-developer-portal-api-menu]

**GET Resource**를 클릭한 다음 **사용해 보세요**를 클릭합니다.

![콘솔 시작][api-management-open-console]

기본 매개 변수 값을 유지한 다음, 무료 평가판 제품의 구독 키를 선택합니다.

![구독 키][api-management-select-key]

>[AZURE.NOTE] 구독이 여러 개 있는 경우에는 **무료 평가판**의 키를 선택합니다. 그렇지 않은 경우 이전 단계에 구성한 정책이 적용되지 않습니다.

**보내기**를 클릭한 다음 응답을 봅니다. **응답 상태** **200 OK**입니다.

![작업 결과][api-management-http-get-results]

속도 제한 정책인 분당 호출 10개를 초과하는 속도의 **보내기**를 클릭합니다. 속도 제한 정책을 초과하면 응답 상태 **429 요청이 너무 많음**이 반환됩니다.

![작업 결과][api-management-http-get-429]

**응답 콘텐츠**는 재시도에 성공하기 전의 남은 간격을 나타냅니다.

속도 제한 정책인 분당 호출 10개가 적용되는 경우 속도 제한이 초과하기 전에 처음 10개의 제품 호출에 성공한 후 60초가 경과할 때까지 후속 호출에 실패합니다. 이 예제에서는 남은 간격이 54초입니다.

## <a name="next-steps"> </a>다음 단계

-	다음 비디오에서 속도 제한 및 할당량을 설정하는 데모를 보세요.

> [AZURE.VIDEO rate-limits-and-quotas]


[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Azure API 관리에서 첫 번째 API 관리]: api-management-get-started.md
[Azure API 관리의 첫 번째 API 관리]: api-management-get-started.md
[Azure API 관리에서 그룹을 만들고 사용하는 방법]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[API 관리 서비스 인스턴스 만들기]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[호출 속도 제한 및 할당량 정책 구성]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota

<!---HONumber=AcomDC_0831_2016-->
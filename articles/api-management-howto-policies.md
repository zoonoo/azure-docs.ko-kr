<properties 
	pageTitle="Azure API 관리의 정책" 
	description="API 관리에서 정책을 만들고 편집하고 구성하는 방법에 대해 알아봅니다." 
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
	ms.date="1/28/2015" 
	ms.author="sdanie"/>

# Azure API 관리의 정책

Azure API 관리(미리 보기)에서, 정책은 게시자가 구성을 통해 API 동작을 변경할 수 있도록 하는 시스템의 강력한 기능입니다. 정책은 API의 요청이나 응답에 따라 순차적으로 실행되는 명령문의 컬렉션입니다. 많이 사용되는 명령문에는 XML에서 JSON으로 형식 변환, 개발자로부터 들어오는 호출의 양을 제한하는 호출 비율 제한 등이 포함됩니다. 다양한 다른 정책도 바로 사용할 수 있습니다.

정책 명령문 및 설정의 전체 목록은 [정책 참조][정책 참조]를 참조하세요.

정책은 API 소비자와 관리되는 API 간에 있는 프록시 내에서 적용됩니다. 프록시는 모든 요청을 수신하고 보통 변경하지 않은 상태로 기본 API에 전달합니다. 그러나 정책은 인바운드 요청과 아웃바운드 응답 모두에 변경 내용을 적용할 수 있습니다.

## 정책을 구성하는 방법

정책을 글로벌로 구성하거나 [제품][제품], [API][API] 또는 [작업][작업] 범위로 구성할 수 있습니다. 정책을 구성하려면 게시자 포털의 정책 편집기로 이동합니다.

![정책 메뉴][정책 메뉴]

정책 편집기는 3개의 주요 섹션, 즉, 정책 범위(상단), 정책을 편집하는 정책 정의(왼쪽), 명령문 목록(오른쪽)으로 구성됩니다.

![정책 편집기][정책 편집기]

정책을 구성하려면 먼저 정책을 적용할 범위를 선택해야 합니다. 아래 스크린샷에서는 15일 무료 평가판 제품이 선택되어 있습니다. 정책 이름 옆의 사각형 기호는 정책이 이 수준에서 이미 적용되었음을 나타냅니다.

![범위][범위]

정책이 이미 적용되었으므로 구성은 정의 뷰에 표시됩니다.

![구성][구성]

정책은 처음에 읽기 전용으로 표시됩니다. 정의를 편집하려면 정책 구성 작업을 클릭합니다.

![편집][편집]

정책 정의는 일련의 인바운드 및 아웃바운드 명령문을 설명하는 단순한 XML 문서입니다. 정의 창에서 XML을 직접 편집할 수 있습니다. 명령문 목록이 오른쪽에 제공되고 현재 범위에 적용할 수 있는 명령문이 사용 가능해지며 강조 표시됩니다. 위의 스크리샷에서는 호출 비율 제한 문이 표시되어 있습니다.

사용할 수 있는 명령문을 클릭하면 정의 뷰에서 커서의 위치에 적절한 XML이 추가됩니다.

정책 명령문 및 설정의 전체 목록은 [정책 참조][정책 참조]에서 확인할 수 있습니다.

예를 들어 들어오는 요청을 지정된 IP 주소로 제한하는 새로운 문을 추가하려면 "inbound" XML 요소의 콘텐츠 바로 안에 커서를 놓고 호출자 IP 제한 문을 클릭합니다.

![제한 정책][제한 정책]

그러면 명령문을 구성하는 방법에 대한 지침을 제공하는 XML 코드 조각이 "inbound" 요소에 추가됩니다.

	<ip-filter action="allow | forbid">
		<address>address</address>
		<address-range from="address" to="address"/>
	</ip-filter>

인바운드 요청을 제한하여 1.2.3.4의 IP 주소에서 들어오는 요청만 허용하려면 다음과 같이 XML을 수정하세요.

	<ip-filter action="allow">
		<address>1.2.3.4</address>
	</ip-filter>

![저장][저장]

정책에 대한 명령문을 구성한 후에는 저장을 클릭합니다. 그러면 변경 내용이 바로 API 관리 프록시에 전파됩니다.

# 정책 구성 이해

정책은 요청 및 응답의 순서로 실행되는 일련의 명령문입니다. 구성은 스크린샷과 같이 inbound(요청) 및 outbound(정책)으로 적절히 나뉩니다.

	<policies>
		<inbound>
			<!-- statements to be applied to the request go here -->
		</inbound>
		<outboud>
			<!-- statements to be applied to the response go here -->
		<outbound>
	</policies>

정책은 여러 수준(전역, 제품, api 및 작업)으로 지정할 수 있으므로, 구성을 통해 부모 정책과 관련하여 이 정의의 명령문이 실행되는 순서를 지정할 수 있습니다.

예를 들어 글로벌 수준의 정책 및 API에 대해 구성된 정책이 있는 경우 특정 API가 사용될 때마다 두 정책이 모두 적용됩니다. API 관리는 기본 요소를 통해 결합된 정책 명령문의 결정적인 순서를 허용합니다.

	<policies>
    	<inbound>
        	<cross-domain />
        	<base />
        	<find-and-replace from="xyz" to="abc" />
    	</inbound>
	</policies>

위의 정책 정의 예제에서, 상위 정책 전에 도메인 간 명령문이 실행된 다음 find-and-replace 정책이 실행됩니다.

참고: 글로벌 정책에 상위 정책이 없는 경우 *base* 요소는 항상 *no-op*이거나 아무런 영향도 주지 않습니다.


[정책 참조]: ../api-management-policy-reference
[제품]: ../api-management-howto-add-products
[API]: ../api-management-howto-add-products/#add-apis
[작업]: ../api-management-howto-add-operations
[정책 메뉴]: ./media/api-management-howto-policies/api-management-policies-menu.png
[정책 편집기]: ./media/api-management-howto-policies/api-management-policies-editor.png
[범위]: ./media/api-management-howto-policies/api-management-policies-scope.png
[구성]: ./media/api-management-howto-policies/api-management-policies-configure.png
[편집]: ./media/api-management-howto-policies/api-management-policies-edit.png
[제한 정책]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[저장]: ./media/api-management-howto-policies/api-management-policies-save.png

<!--HONumber=46--> 

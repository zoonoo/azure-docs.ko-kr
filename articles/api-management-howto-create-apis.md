<properties 
	pageTitle="Azure API 관리에서 API를 만드는 방법" 
	description="Azure API 관리에서 API를 만들고 구성하는 방법에 대해 알아봅니다." 
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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Azure API 관리에서 API를 만드는 방법

API 관리(미리 보기)에서 API는 클라이언트 응용 프로그램이 호출할 수 있는 작업 집합을 나타냅니다. 새 API가 관리 콘솔에서 생성되고 필요한 작업이 추가됩니다. 작업이 추가되면 API가 제품에 추가되므로, 이 API를 게시할 수 있습니다. API가 게시되면 구독자와 개발자가 해당 API를 사용할 수 있습니다.

이 가이드에서는 프로세스의 첫 번째 단계인 API 관리에서 새 API를 만들고 구성하는 방법을 보여 줍니다. 작업 추가 및 제품 게시에 대한 자세한 내용은 [API에 작업을 추가하는 방법][API에 작업을 추가하는 방법] 및 [제품을 만들고 게시하는 방법][제품을 만들고 게시하는 방법]을 참조하세요.

## 항목 내용

- [새 API 만들기][새 API 만들기]
- [API 설정 구성][API 설정 구성]
- [다음 단계][다음 단계]

## <a name="create-new-api"></a> 새 API 만들기

API를 만들고 구성하려면 Azure 포털에서 API 관리 서비스 인스턴스에 대한 **관리 콘솔**을 클릭합니다. API 관리의 관리용 포털이 열립니다.

>아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][Azure API 관리 시작] 자습서의 [API 관리 서비스 인스턴스 만들기][API 관리 서비스 인스턴스 만들기]를 참조하세요.

![관리 콘솔][관리 콘솔]

왼쪽의 **API 관리** 메뉴에서 **API**를 클릭한 다음 **API 추가**를 클릭합니다.

![API 만들기][API 만들기]

**새 API 추가** 창을 사용하여 새 API를 구성합니다.

![새 API 추가][새 API 추가]

새 API를 구성하는 데 다음 세 개의 필드가 사용됩니다.

- **Web API 제목**은 API를 설명하는 고유한 이름을 제공합니다. 개발자 및 관리 포털에 표시됩니다.
- **웹 서비스 URL**은 API를 구현하는 HTTP 서비스를 참조합니다. API 관리는 이 주소로 요청을 전달합니다.
- **Web API URL 접미사**는 API 관리 서비스의 기준 URL에 추가됩니다. 기본 URL은 API 관리 서비스 인스턴스에서 호스트되는 모든 API에 공통으로 사용됩니다. API 관리는 접미사를 사용하여 API를 구분하므로, 접미사는 지정된 게시자의 모든 API에 대해 고유해야 합니다.

세 값이 구성되면 **저장**을 클릭합니다. 새 API가 만들어지면 API에 대한 요약 페이지가 관리 포털에 표시됩니다.

![API 요약][API 요약]

## <a name="configure-api-settings"></a> API 설정 구성

**설정** 탭을 사용하여 API의 구성을 확인 및 편집할 수 있습니다. **Web API 제목**, **웹 서비스 URL** 및 **Web API URL 접미사**는 초기에 API가 생성될 때 설정되며, 이 탭에서 수정할 수 있습니다. **설명**은 선택적 설명을 제공하며, **자격 증명 사용**을 통해 기본 HTTP 인증을 구성할 수 있습니다.

![API 설정][API 설정]

API를 구현하는 웹 서비스에 대한 HTTP 기본 인증을 구성하려면 **자격 증명 사용** 드롭다운에서 **기본**을 선택하고 원하는 자격 증명을 입력합니다.

![기본 인증 설정][기본 인증 설정]

**저장**을 클릭하여 API 설정에 대한 변경 내용을 저장합니다.

## <a name="next-steps"></a> 다음 단계

API를 만들고 설정을 구성한 후 다음 단계는 API에 작업을 추가하고, 제품에 API를 추가하고, 개발자가 사용할 수 있도록 게시하는 것입니다. 자세한 내용은 다음 두 안내서를 참조하세요.

- [API에 작업을 추가하는 방법][API에 작업을 추가하는 방법]
- [제품을 만들고 게시하는 방법][제품을 만들고 게시하는 방법]





[API에 작업을 추가하는 방법]: ../api-management-howto-add-operations
[제품을 만들고 게시하는 방법]: ../api-management-howto-add-products
[새 API 만들기]: #create-new-api
[API 설정 구성]: #configure-api-settings
[다음 단계]: #next-steps
[Azure API 관리 시작]: ../api-management-get-started
[API 관리 서비스 인스턴스 만들기]: ../api-management-get-started/#create-service-instance
[관리 콘솔]: ./media/api-management-howto-create-apis/api-management-management-console.png
[API 만들기]: ./media/api-management-howto-create-apis/api-management-create-api.png
[새 API 추가]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[API 요약]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[API 설정]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[기본 인증 설정]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png

<!--HONumber=46--> 

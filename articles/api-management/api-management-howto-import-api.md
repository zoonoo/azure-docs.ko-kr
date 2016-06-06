<properties 
	pageTitle="API 관리 주요 개념" 
	description="API, 제품, 역할, 그룹 및 기타 API 관리의 주요 개념에 대해 알아봅니다." 
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
	ms.date="05/25/2016" 
	ms.author="sdanie"/>

# Azure API 관리에서 작업과 함께 API의 정의를 가져오는 방법

API 관리에서 새 API를 만들고 작업을 수동으로 추가하거나 API를 작업과 함께 한 번에 가져올 수 있습니다.

API 및 그 작업은 다음 형식으로 가져올 수 있습니다.

-	WADL
-	Swagger

이 가이드에서는 새로운 API를 만들고 그 작업과 함께 한번에 가져오는 방법을 보여 줍니다. API를 수동으로 만들고 작업을 추가하는 방법에 대해서는 [API를 만드는 방법][] 및 [API에 작업을 추가하는 방법][]을 참조하세요.

## <a name="import-api"> </a>API 가져오기

게시자 포털에서 API를 만들고 구성합니다. 게시자 포털에 액세스하려면 API 관리 서비스에 대해 Azure 클래식 포털에서 **관리**를 클릭합니다. 아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][] 자습서의 [API 관리 서비스 인스턴스 만들기][]를 참조하세요.

![게시자 포털][api-management-management-console]

왼쪽의 **API 관리** 메뉴에서 **API**를 클릭한 다음 **API 가져오기**를 클릭합니다.

![API 가져오기][api-management-import-apis]

**API 가져오기** 창에는 3개의 탭이 있으며 각기 API 사양을 제공하는 3가지 방법에 해당합니다.

-	**클립보드에서** 지정된 입력란에 API 사양을 붙여넣을 수 있습니다.
-	**파일에서** API 사양을 포함하는 파일로 이동하여 선택할 수 있습니다.
-	**URL에서** API의 사양에 URL을 제공할 수 있습니다.

![API 가져오기 형식][api-management-import-api-clipboard]

API 사양을 제공한 후 오른쪽의 라디오 단추를 사용하여 사양 형식을 표시할 수 있습니다. 다음 형식이 지원됩니다.

-	WADL
-	Swagger

이제 **Web API URL 접미사**를 입력합니다. 이 접미사는 API 관리 서비스의 기본 URL에 추가됩니다. 기본 URL은 API 관리 서비스의 각 인스턴스에서 호스트되는 모든 API에 공통으로 사용됩니다. API 관리는 접미사를 사용하여 API를 구분하므로, 접미사는 특정 API 관리 서비스 인스턴스의 모든 API에 대해 고유해야 합니다.

모든 값을 입력한 후에는 **저장**을 클릭하여 API 및 연결된 작업을 만듭니다.

>[AZURE.NOTE] Swagger 형식의 기본 계산기 API의 자습서는 [Azure API 관리에서 첫번째 API 관리](api-management-get-started.md)를 참조하세요.

## <a name="export-api"> </a>API 내보내기

새 API를 가져올 뿐만 아니라 게시자 포털에서 API에 대한 정의를 내보낼 수 있습니다. 그렇게 하려면 **API**의 **요약 탭**에서 **API 내보내기**를 클릭합니다.

![API 내보내기][api-management-export-api]

API는 WADL 또는 Swagger를 사용하여 내보낼 수 있습니다. 원하는 형식을 선택하고, **저장**을 클릭하고, 파일을 저장할 위치를 선택합니다.

![API 내보내기 형식][api-management-export-api-format]

## <a name="next-steps"> </a>다음 단계

API를 만들고 작업을 가져온 후 추가 설정을 검토 및 구성하고, 제품에 API를 추가하고, 개발자가 사용할 수 있도록 게시할 수 있습니다. 자세한 내용은 다음 가이드를 참조하세요.

-	[API 설정을 구성하는 방법][]
-	[제품을 만들고 게시하는 방법][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[API 관리 서비스 인스턴스 만들기]: api-management-get-started.md
[Azure API 관리 시작]: api-management-get-started.md#create-service-instance

[API에 작업을 추가하는 방법]: api-management-howto-add-operations.md
[제품을 만들고 게시하는 방법]: api-management-howto-add-products.md
[API를 만드는 방법]: api-management-howto-create-apis.md
[API 설정을 구성하는 방법]: api-management-howto-create-apis.md#configure-api-settings

<!---HONumber=AcomDC_0525_2016-->
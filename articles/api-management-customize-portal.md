<properties 
	pageTitle="<MORAVIA_TRANSLATEAzure API 관리에서 개발자 포털 사용자 지정" 
	description="Azure API 관리에서 개발자 포털을 사용자 지정하는 방법에 대해 알아봅니다." 
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

# Azure API 관리에서 개발자 포털 사용자 지정

이 가이드에서는 브랜드와 일관성을 유지하기 위해 API 관리에서 개발자 포털의 모양과 느낌을 수정하는 방법을 보여 줍니다.

## 항목 내용

-	[페이지 머리글의 텍스트/로고 변경][]
-	[머리글의 스타일 변경][]
-	[페이지 콘텐츠 편집][]
-	[다음 단계][]


## <a name="change-page-headers"> </a>페이지 머리글의 텍스트/로고 변경

포털 사용자 지정에서 중요한 측면 중 하나는 모든 페이지의 맨 위에 있는 텍스트를 회사 이름이나 로고로 바꾸는 것입니다.

개발자 포털 내의 콘텐츠는 게시자 포털을 통해 수정되며, 이 포털에는 Azure 관리 포털을 통해 액세스합니다. API 관리 콘솔에 액세스하려면 Azure 포털에서 API 관리 서비스의 **관리 콘솔**을 클릭합니다.

![Management console][api-management-management-console]

개발자 포털은 CMS(콘텐츠 관리 시스템)을 기반으로 합니다. 모든 페이지에 나타나는 머리글은 위젯이라고 하는 특수 형식의 콘텐츠입니다. 이 위젯의 콘텐츠를 편집하려면 왼쪽의 **개발자 포털** 메뉴에서 **위젯**을 클릭한 다음 목록에서 **머리글** 위젯을 선택합니다.

![Widgets header][api-management-widgets-header]

머리글의 콘텐츠는 **본문** 필드 내에서 편집할 수 있습니다. 텍스트를 "Fabrikam 개발자 포털"로 변경하고 페이지 아래에 있는 **저장**을 클릭합니다.

이제 개발자 포털 내의 모든 페이지에서 새 머리글을 볼 수 있습니다.

> 관리 콘솔에 있는 동안 개발자 포털을 열려면 위쪽 막대에서 **개발자 포털**을 클릭합니다.

## <a name="change-headers-styling"> </a>머리글의 스타일 변경

포털에서 페이지의 색, 글꼴, 크기, 간격 및 기타 스타일 관련 요소는 스타일 규칙에 따라 정의됩니다. 스타일을 편집하려면 게시자 포털의 **개발자 포털** 메뉴에서 **모양**을 클릭합니다. 그런 다음, **사용자 지정 시작**을 클릭하여 스타일 편집기를 사용합니다.

브라우저가 개발자 포털 내의 숨겨진 페이지로 전환되며, 이 페이지에는 사이트의 임의의 위치에서 사용되는 모든 스타일 규칙의 예제와 함께 콘텐츠 샘플이 들어 있습니다. 스타일 편집기를 열려면 페이지의 맨 왼쪽에 있는 가느다란 회색 세로선으로 커서를 가져갑니다. 편집기 도구 모음이 나타납니다.

![Customization toolbar][api-management-customization-toolbar]

스타일 규칙 편집의 두 가지 주요 모드가 있습니다. **모든 규칙 편집**은 임의의 위치에서 사용되는 모든 스타일 규칙의 목록을 표시하고, **요소 선택**은 현재 페이지에서 요소를 선택할 수 있게 하며 해당 요소에 대한 스타일만 표시합니다.

이 섹션에서는 머리글에 대해서만 스타일을 변경합니다. 편집기 도구 모음에서 **요소 선택** 옵션을 클릭한 후 **사용자 지정할 요소 선택**을 클릭합니다. 이제 마우스를 요소 위로 가져가면 요소가 강조 표시되어 클릭하면 어느 요소의 스타일부터 편집을 시작하게 되는지가 나타납니다. 머리글에서 회사 이름을 나타내는 텍스트(이전 섹션의 지침을 따른 경우 "Fabrikam 개발자 포털") 위로 마우스를 가져간 후 텍스트를 클릭합니다. 명명되고 분류된 스타일 규칙 집합이 스타일 편집기 내에 나타납니다.

각 규칙은 선택한 요소의 스타일 속성을 나타냅니다. 예를 들어 위에서 선택한 머리글 텍스트의 경우 텍스트의 크기는 @font-size-h1이고, 대체 항목이 있는 글꼴의 이름은 @headings-font-family입니다.

> [부트스트랩][](영문)에 익숙한 경우, 이 규칙은 실제로 개발자 포털에서 사용되는 부트스트랩 테마 내의 [LESS 변수][](영문)입니다.

이제 제목 텍스트의 색을 변경하겠습니다. **@headings-color** 필드에서 항목을 선택하고 #000000을 입력합니다. 이는 검정색의 16진 코드입니다. 이 과정에서 텍스트 상자 끝부분에 사각형 색 표시기가 나타나는 것이 확인됩니다. 이 표시기를 클릭하면 색 선택을 통해 색을 선택할 수 있습니다.

![Color picker][api-management-customization-toolbar-color-picker]

선택한 요소의 스타일 변경을 마치면 **변경 내용 미리 보기**를 클릭하여 화면에 결과를 표시합니다. 이때 결과는 관리자에게만 보입니다. 변경 내용을 모든 사람이 볼 수 있게 하려면 스타일 편집기에서 **게시** 단추를 클릭하고 변경 내용을 확인하세요.

![Publish menu][api-management-customization-toolbar-publish-form]

> 머리글의 경우와 동일한 절차를 따르는 페이지상의 다른 요소에 적용되는 스타일 규칙을 변경하려면 스타일 편집기에서 **요소 선택**을 클릭하고 원하는 요소를 선택한 후 화면에 표시되는 스타일 규칙의 값을 수정하기 시작합니다.

## <a name="edit-page-contents"> </a>페이지 콘텐츠 편집

개발자 포털은 API, 제품, 응용 프로그램, 문제 등과 같이 자동으로 생성되는 페이지 및 수동 작성 콘텐츠로 구성됩니다. 콘텐츠 관리 시스템을 기반으로 하므로, 필요에 따라 이러한 콘텐츠를 만들 수 있습니다.

모든 기존 콘텐츠 페이지 목록을 표시하려면 관리 콘솔의 **개발자 포털** 메뉴에서 **콘텐츠**를 클릭합니다.

![Manage content][api-management-customization-manage-content]

개발자 포털의 홈페이지에 표시되는 내용을 편집하려면 "시작" 페이지를 클릭합니다. 원하는 대로 변경하고 필요하면 미리 본 다음, **지금 게시**를 클릭하여 모든 사람에게 표시되도록 만듭니다.

> 홈페이지에서는 맨 위에 배너를 표시할 수 있는 특별한 레이아웃이 사용됩니다. 이 배너는 콘텐츠 섹션에서 편집할 수 없습니다. 이 배너를 편집하려면 **개발자 포털** 메뉴에서 **위젯**을 클릭하고 **현재 레이어** 드롭다운에서 **홈페이지**를 선택한 다음 추천 섹션에서 **배너** 항목을 엽니다. 이 위젯의 콘텐츠는 다른 페이지와 마찬가지로 편집할 수 있습니다.

## <a name="next-steps"> </a>다음 단계

-	[고급 API 구성 시작][] 자습서에서 다른 항목을 확인하세요.

[페이지 머리글의 텍스트/로고 변경]: #change-page-headers
[머리글의 스타일 변경]: #change-headers-styling
[페이지 콘텐츠 편집]: #edit-page-contents
[다음 단계]: #next-steps

[관리 포털]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[고급 API 구성 시작]: ../api-management-get-started-advanced
[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/

<!--HONumber=35.2-->

<!--HONumber=46--> 

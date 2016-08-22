<properties 
	pageTitle="Azure API 관리에서 개발자 포털의 모양과 느낌을 사용자 지정하는 방법" 
	description="Azure API 관리에서 개발자 포털의 모양과 느낌을 사용자 지정하는 방법" 
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
	ms.date="08/09/2016" 
	ms.author="sdanie"/>

# Azure API 관리에서 개발자 포털의 모양과 느낌을 사용자 지정하는 방법

개발자 포털 모양과 느낌과 관련된 색, 글꼴, 크기, 간격 및 기타 측면은 스타일 규칙으로 정의됩니다. 이러한 규칙 집합은 페이지의 각 구조 요소 즉, 머리글, 메뉴, 콘텐츠 본문, 페이지 제목 등에 적용됩니다. 이러한 방법에서 스타일 규칙을 수정하는 방법을 알아봅니다.

스타일 규칙을 편집하려면 게시자 포털의 **개발자 포털** 메뉴에서 **모양**을 클릭합니다. 그런 다음, **사용자 지정 시작**을 클릭하여 스타일 편집기를 사용합니다.

브라우저가 개발자 포털 내의 숨겨진 페이지로 전환되며, 이 페이지에는 사이트의 임의의 위치에서 사용되는 모든 스타일 규칙의 예제와 함께 콘텐츠 샘플이 들어 있습니다. 스타일 편집기를 열려면 페이지의 맨 왼쪽에 있는 가느다란 회색 세로선으로 커서를 가져갑니다. 편집기 도구 모음이 아래와 같이 나타납니다.

![사용자 지정 도구 모음][api-management-customization-toolbar]

스타일 규칙 편집의 두 가지 주요 모드가 있습니다. **모든 규칙 편집**은 임의의 위치에서 사용되는 모든 스타일 규칙의 목록을 표시하고, **요소 선택**은 현재 페이지에서 요소를 선택할 수 있게 하며 해당 특정 요소에 대한 스타일만 표시합니다.

이 섹션에서는 특정 요소(예: 페이지 머리글)의 스타일만 변경하겠습니다. 편집기 도구 모음에서 **요소 선택** 옵션을 클릭한 후 **사용자 지정할 요소 선택**을 클릭합니다. 이제 마우스로 가리키면 요소가 강조 표시되어 클릭할 경우 표시되는 요소의 스타일을 나타냅니다.

머리글의 페이지 제목을 나타내는 텍스트 위로 마우스를 가져가 클릭합니다. 명명되고 분류된 스타일 규칙 집합이 스타일 편집기 내에 나타납니다.

각 규칙은 선택한 요소의 스타일 속성을 나타냅니다. 예를 들어 위에서 선택한 머리글 텍스트의 경우 텍스트의 크기는 @font-size-h1이고, 대체 항목이 있는 글꼴의 이름은 @headings-font-family입니다.

> [부트스트랩](http://getbootstrap.com/)(영문)에 익숙한 경우, 이 규칙은 실제로 개발자 포털에서 사용되는 부트스트랩 테마 내의 [LESS 변수](http://getbootstrap.com/css/)(영문)입니다.

이제 제목 텍스트의 색을 변경하겠습니다. **@headings-color** 필드에서 항목을 선택하고 #000000을 입력합니다. 이는 검정색의 16진 코드입니다. 이 과정에서 텍스트 상자 끝부분에 사각형 색 표시기가 나타나는 것이 확인됩니다. 이 표시기를 클릭하면 색 선택을 통해 색을 선택할 수 있습니다.

![색 선택][api-management-customization-toolbar-color-picker]

선택한 요소의 스타일 변경을 마치면 **변경 내용 미리 보기**를 클릭하여 화면에 결과를 표시합니다. 이때 결과는 관리자에게만 보입니다. 변경 내용을 모든 사람이 볼 수 있게 하려면 스타일 편집기에서 **게시** 단추를 클릭하고 변경 내용을 확인하세요.

![게시 양식][api-management-customization-toolbar-publish-form]

> 머리글의 경우와 동일한 절차를 따르는 페이지상의 다른 요소에 적용되는 스타일 규칙을 변경하려면 스타일 편집기에서 **요소 선택**을 클릭하고 원하는 요소를 선택한 후 화면에 표시되는 스타일 규칙의 값을 수정하기 시작합니다.


[Next steps]: #next-steps


[api-management-customization-toolbar]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-publish-form.png

<!---HONumber=AcomDC_0810_2016-->
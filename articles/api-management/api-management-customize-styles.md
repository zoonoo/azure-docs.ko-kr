---
title: "Azure API Management에서 개발자 포털의 스타일 사용자 지정 | Microsoft Docs"
description: "Azure API Management에서 개발자 포털의 페이지에 사용되는 스타일을 수정하는 방법을 알아봅니다."
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
ms.openlocfilehash: 89baf60d0204a1701e93309f09b90bc94c4ca57b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-styling-of-the-developer-portal-in-azure-api-management"></a>Azure API Management에서 개발자 포털의 스타일 사용자 지정
Azure API Management에는 개발자 포털을 사용자 지정하는 기본적인 방법이 세 가지 있습니다.

* [페이지 레이아웃 요소 및 정적 페이지의 콘텐츠 편집][modify-content-layout]
* [개발자 포털 전반의 페이지 요소에 사용된 스타일 업데이트][customize-styles](이 가이드에 설명되어 있음)
* [포털에서 생성된 페이지에 사용된 템플릿 수정][portal-templates](예: API 문서, 제품, 사용자 인증 등)

## <a name="change-headers-styling"> </a>페이지 요소의 스타일 변경

포털에서 페이지의 색, 글꼴, 크기, 간격 및 기타 스타일 관련 요소는 스타일 규칙에 따라 정의됩니다. 

스타일 규칙 편집은 관리자로 로그인되어 있는 동안 **개발자 포털**에서 수행됩니다. 여기로 이동하려면 우선 Azure Portal을 열고 API Management 인스턴스의 서비스 도구 모음에서 **게시자 포털**을 클릭하세요.

![게시자 포털][api-management-management-console]

그런 다음 오른쪽 상단의 **개발자 포털**을 클릭합니다. 

![게시자 포털의 자 포털 링크][api-management-pp-dp-link]

사용자 지정 도구 모음을 열려면 사용자 지정 아이콘 위로 마우스를 이동(또는 선택)한 다음 도구 모음에서 “스타일”을 클릭합니다.

![사용자 지정 도구 모음 단추][api-management-customization-toolbar-button]

스타일 규칙 편집에는 두 가지 주요 방법이 있습니다. 기본적으로 표시되는 곳에 사용되는 모든 스타일 규칙의 목록을 확인하고 필요에 따라 스타일을 수정하거나, **페이지에서 요소 선택**을 선택한 다음 페이지에서 아무 곳이나 클릭하여 해당 요소에 대한 스타일만 확인할 수 있습니다.

![사용자 지정 도구 모음][api-management-customization-toolbar]

이 예제의 경우 **페이지에서 요소 선택** 옵션을 클릭합니다.  이제 마우스를 요소 위로 가져가면 요소가 강조 표시되어 클릭하면 어느 요소의 스타일부터 편집을 시작하게 되는지가 나타납니다. 머리글의 텍스트(일반적으로 회사 이름이 여기에 있음) 위로 마우스로 이동한 다음 클릭합니다. 명명되고 분류된 스타일 규칙 집합이 스타일 편집기 내에 나타납니다. 각 규칙은 선택한 요소의 스타일 속성을 나타냅니다. 예를 들어, 위에서 선택한 머리글 텍스트의 경우 텍스트의 크기는 @font-size-h1이고, 대체 항목이 있는 글꼴의 이름은 @headings-font-family입니다.

> [부트스트랩][bootstrap](영문)에 익숙한 경우 이 규칙은 실제로 개발자 포털에서 사용하는 부트스트랩 테마 내의 [LESS 변수][LESS variables](영문)입니다.
> 
> 

이제 제목 텍스트의 색을 변경하겠습니다. **@headings-color** 필드에서 항목을 선택하고 **#000000**을 입력합니다. 이는 검정색의 16진 코드입니다. 이 과정에서 텍스트 상자 끝부분에 사각형 색 표시기가 나타나는 것이 확인됩니다. 이 표시기를 클릭하면 색 선택을 통해 색을 선택할 수 있습니다.

![색 선택][api-management-customization-toolbar-color-picker]

변경 내용을 적용하면 실시간으로 미리 보기를 확인할 수 있지만 관리자에게만 표시됩니다. 변경 내용을 모든 사람이 볼 수 있게 하려면 스타일 편집기에서 **게시** 단추를 클릭하고 변경 내용을 확인하세요.

![게시 메뉴][api-management-customization-toolbar-publish-form]

> 페이지상의 다른 요소에 적용되는 스타일 규칙을 변경하려면 머리글의 경우와 동일한 절차를 따릅니다. 스타일 편집기에서 **페이지에서 요소 선택**을 클릭하고 원하는 요소를 선택한 후 화면에 표시되는 스타일 규칙의 값을 수정하기 시작합니다.
> 
> 


## <a name="next-steps"> </a>다음 단계
* [개발자 포털 템플릿](api-management-developer-portal-templates.md)을 사용하여 개발자 포털 페이지의 콘텐츠를 사용자 지정하는 방법 알아보기.

[Change the styling of the headers]: #change-headers-styling
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-styles/api-management-management-console.png
[api-management-pp-dp-link]: ./media/api-management-customize-styles/api-management-pp-dp-link.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-styles/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-styles/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-styles/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-styles/api-management-customization-toolbar-publish-form.png

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/

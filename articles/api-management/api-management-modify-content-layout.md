---
title: "Azure API Management에서 개발자 포털의 페이지 콘텐츠 수정 | Microsoft Docs"
description: "Azure API Management에서 개발자 포털의 페이지 콘텐츠를 편집하는 방법을 알아봅니다."
services: api-management
documentationcenter: 
author: antonba
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
translationtype: Human Translation
ms.sourcegitcommit: ecae1da20551d8372331124b07c4aca2e15f55bb
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.lasthandoff: 02/23/2017


---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Azure API Management에서 개발자 포털의 페이지 콘텐츠 및 레이아웃 수정
Azure API Management에는 개발자 포털을 사용자 지정하는 기본적인 방법이 세 가지 있습니다.

* [페이지 레이아웃 요소 및 정적 페이지의 콘텐츠 편집][modify-content-layout](이 가이드에 설명되어 있음)
* [개발자 포털 전반의 페이지 요소에 사용된 스타일 업데이트][customize-styles]
* [포털에서 생성된 페이지에 사용된 템플릿 수정][portal-templates](예: API 문서, 제품, 사용자 인증 등)

## <a name="page-structure"> </a>개발자 포털 페이지의 구조

개발자 포털은 콘텐츠 관리 시스템을 기반으로 합니다. 모든 페이지의 레이아웃은 위젯이라는 작은 페이지 요소의 집합을 기반으로 작성됩니다.

![개발자 포털 페이지 구조][api-management-customization-widget-structure]

모든 위젯은 편집이 가능합니다. 
* 각 페이지 고유의 핵심 콘텐츠는 "콘텐츠" 위젯에 있습니다. 페이지를 편집한다는 것은 이 위젯의 콘텐츠를 편집한다는 의미입니다.
* 모든 페이지 레이아웃 요소는 나머지 위젯에 포함됩니다. 이러한 위젯에 변경한 내용은 모든 페이지에 적용됩니다. 이것을 "레이아웃 위젯"이라고 합니다.

일상적인 페이지 편집에서 콘텐츠 위젯만 편집하면 각각의 개별적인 페이지에 콘텐츠가 달라집니다.

## <a name="modify-layout-widget"> </a>레이아웃 위젯의 콘텐츠 수정

개발자 포털 내의 콘텐츠는 Azure Portal을 통해 액세스할 수 있는 게시자 포털을 통해 수정됩니다. 여기에 도달하려면 API Management 인스턴스의 서비스 도구 모음에서 **게시자 포털**을 클릭하세요.

![게시자 포털][api-management-management-console]

이 위젯의 콘텐츠를 편집하려면 왼쪽의 **개발자 포털** 메뉴에서 **위젯**을 클릭합니다. 이 예제에서는 머리글 위젯의 콘텐츠를 수정하겠습니다. 목록에서 **머리글** 위젯을 선택합니다.

![위젯 머리글][api-management-widgets-header]

머리글의 콘텐츠는 **본문** 필드 내에서 편집할 수 있습니다. 필요에 따라 텍스트를 변경한 다음 페이지 아래쪽에서 **저장**을 클릭합니다.

이제 개발자 포털 내의 모든 페이지에서 새 머리글을 볼 수 있습니다.

> 게시자 포털에 있는 동안 개발자 포털을 열려면 위쪽 막대에서 **개발자 포털** 을 클릭합니다.
> 
> 

## <a name="edit-page-contents"> </a>페이지의 콘텐츠 편집

모든 기존 콘텐츠 페이지 목록을 표시하려면 게시자 포털의 **개발자 포털** 메뉴에서 **콘텐츠**를 클릭합니다.

![콘텐츠 관리][api-management-customization-manage-content]

개발자 포털의 홈페이지에 표시되는 내용을 편집하려면 **시작** 페이지를 클릭합니다. 원하는 대로 변경하고 필요하면 미리 본 다음, **지금 게시** 를 클릭하여 모든 사람이 볼 수 있도록 합니다.

> 홈페이지에서는 맨 위에 배너를 표시할 수 있는 특별한 레이아웃이 사용됩니다. 이 배너는 **콘텐츠** 섹션에서 편집할 수 없습니다. 이 배너를 편집하려면 **개발자 포털** 메뉴에서 **위젯**을 클릭하고 **현재 레이어** 드롭다운 목록에서 **홈페이지**를 선택한 다음 **추천 섹션**에서 **배너** 항목을 엽니다. 이 위젯의 콘텐츠는 다른 페이지와 마찬가지로 편집할 수 있습니다.
> 
> 

## <a name="next-steps"> </a>다음 단계
* [개발자 포털 전반의 페이지 요소에 사용된 스타일 업데이트][customize-styles]
* [포털에서 생성된 페이지에 사용된 템플릿 수정][portal-templates](예: API 문서, 제품, 사용자 인증 등)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png


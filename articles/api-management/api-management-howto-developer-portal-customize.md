---
title: 관리되는 개발자 포털에 액세스하고 사용자 지정 - Azure API 관리 | 마이크로 소프트 문서
description: API 관리에서 개발자 포털의 관리 버전을 사용하는 방법을 알아봅니다.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: af7c995c11322a538dd9e27a905f1ddbc723e8ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244096"
---
# <a name="access-and-customize-developer-portal"></a>개발자 포털 액세스 및 사용자 지정

개발자 포털은 API 설명서를 통해 자동으로 생성되고 완전히 사용자 지정가능한 웹 사이트입니다. API 소비자가 API를 검색하고, API를 사용하는 방법을 배우고, 액세스를 요청할 수 있는 곳입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 개발자 포털의 관리되는 버전에 액세스
> * 관리 인터페이스 탐색
> * 콘텐츠 사용자 지정
> * 변경 내용 게시
> * 게시된 포털 보기

자세한 내용은 Azure API 관리 개발자 포털 개요에서 개발자 [포털에서](api-management-howto-developer-portal.md)확인할 수 있습니다.

![API 관리 개발자 포털 - 관리자 모드](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>사전 요구 사항

- 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
- Azure API Management 인스턴스를 가져오고 게시합니다. 자세한 내용은 [가져오기 및 게시를](import-and-publish.md) 참조하십시오.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>관리자로 포털에 액세스

아래 단계에 따라 포털의 관리되는 버전에 액세스합니다.

1. Azure 포털의 API 관리 서비스 인스턴스로 이동합니다.
1. 상단 탐색 모음에서 **개발자 포털** 버튼을 클릭합니다. 포털의 관리 버전이 있는 새 브라우저 탭이 열립니다.

## <a name="understand-the-portals-administrative-interface"></a>포털의 관리 인터페이스 이해

### <a name="default-content"></a>기본 콘텐츠 

포털에 처음 액세스하는 경우 기본 콘텐츠가 백그라운드에서 자동으로 프로비전됩니다. 기본 콘텐츠는 포털의 기능을 보여주고 포털을 개인 설정하는 데 필요한 사용자 지정의 양을 최소화하도록 설계되었습니다. [Azure API 관리 개발자 포털 개요에서](api-management-howto-developer-portal.md)포털 콘텐츠에 포함된 내용에 대해 자세히 알아볼 수 있습니다.

### <a name="visual-editor"></a>비주얼 에디터

시각적 편집기와 포털의 콘텐츠를 사용자 지정할 수 있습니다. 왼쪽의 메뉴 섹션을 사용하면 페이지, 미디어, 레이아웃, 메뉴, 스타일 또는 웹 사이트 설정을 만들거나 수정할 수 있습니다. 하단의 메뉴 항목을 사용하면 뷰포트(예: 모바일 또는 데스크톱)를 전환하거나, 인증된 사용자 또는 익명 사용자에게 표시되는 포털 요소를 보거나, 작업을 저장하거나 실행 취소할 수 있습니다.

더하기 기호가 있는 파란색 아이콘을 클릭하여 페이지에 행을 추가할 수 있습니다. 위젯(예: 텍스트, 이미지 또는 API 목록)은 더하기 기호가 있는 회색 아이콘을 눌러 추가할 수 있습니다. 끌어서 놓기 상호 작용을 통해 페이지에서 항목을 다시 정렬할 수 있습니다. 

### <a name="layouts-and-pages"></a>레이아웃 및 페이지

![페이지 및 레이아웃](media/api-management-howto-developer-portal-customize/pages-layouts.png)

레이아웃은 페이지가 표시되는 방법을 정의합니다. 예를 들어 기본 콘텐츠에는 홈 페이지에 적용되고 다른 하나는 나머지 모든 페이지에 적용되는 두 개의 레이아웃이 있습니다.

URL 템플릿을 페이지의 URL과 일치시켜 레이아웃이 페이지에 적용됩니다. 예를 `/wiki/*` 들어 URL 템플릿이 있는 레이아웃은 URL의 `/wiki/` `/wiki/getting-started` `/wiki/styles`세그먼트가 있는 모든 페이지에 적용됩니다.

위의 이미지에서 레이아웃에 속한 콘텐츠는 파란색으로 표시되고 페이지는 빨간색으로 표시됩니다. 메뉴 섹션은 각각 표시됩니다.

### <a name="styling-guide"></a>스타일링 가이드

![스타일링 가이드](media/api-management-howto-developer-portal-customize/styling-guide.png)

스타일링 가이드는 디자이너를 염두에 두고 만든 패널입니다. 이를 통해 포털의 모든 시각적 요소를 감독하고 스타일을 지정할 수 있습니다. 스타일은 계층적입니다 - 많은 요소가 다른 요소의 속성을 상속합니다. 예를 들어 단추 요소는 텍스트 및 배경에 색상을 사용합니다. 단추의 색상을 변경하려면 원래 색상 변형을 변경해야 합니다.

변형을 편집하려면 변형을 클릭하고 그 위에 나타나는 연필 아이콘을 선택합니다. 팝업 창에서 변경한 후 닫습니다.

### <a name="save-button"></a>저장 단추

![저장 단추](media/api-management-howto-developer-portal-customize/save-button.png)

포털을 변경할 때마다 하단의 메뉴에 있는 **저장** 단추를 눌러 수동으로 저장해야 합니다. 변경 내용을 저장하면 수정된 콘텐츠가 API 관리 서비스에 자동으로 업로드됩니다.

## <a name="customize-the-portals-content"></a>포털 콘텐츠 사용자 지정

방문자가 포털을 사용할 수 있도록 설정하기 전에 자동으로 생성된 콘텐츠를 개인 설정해야 합니다. 권장되는 변경 사항에는 홈 페이지의 레이아웃, 스타일 및 콘텐츠가 포함됩니다.

> [!NOTE]
> 통합 고려 사항으로 인해 다음 페이지는 다른 URL에서 제거하거나 이동할 `/404` `/500`수 `/captcha` `/change-password` `/config.json`없습니다. `/confirm/invitation` `/confirm-v2/identities/basic/signup` `/confirm-v2/password` `/internal-status-0123456789abcdef` `/publish` `/signin` `/signin-sso` `/signup`

### <a name="home-page"></a>홈 페이지

기본 **홈** 페이지는 더미 콘텐츠로 채워져 있습니다. 콘텐츠가 있는 전체 섹션을 제거하거나 구조를 유지하고 요소를 하나씩 조정할 수 있습니다. 생성된 텍스트와 이미지를 사용자 고유의 텍스트와 이미지로 바꾸고 링크가 원하는 위치를 가리키는지 확인합니다.

### <a name="layouts"></a>레이아웃

탐색 모음에서 자동으로 생성된 로고를 사용자 고유의 이미지로 바꿉습니다.

### <a name="styling"></a>스타일 지정

스타일을 조정할 필요는 없지만 특정 요소를 조정하는 것이 좋습니다. 예를 들어 브랜드의 색상에 맞게 기본 색상을 변경합니다.

### <a name="customization-example"></a>사용자 지정 예제

아래 비디오에서는 포털의 콘텐츠를 편집하고, 웹 사이트의 모양을 사용자 지정하고, 변경 내용을 게시하는 방법을 보여 줍니다.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>포털 게시

포털 및 최신 변경 내용을 방문자가 사용할 수 있도록 하려면 게시해야 합니다.

1. **저장** 아이콘을 클릭하여 변경 내용을 저장했는지 확인합니다.
1. 메뉴의 **작업** 섹션에서 **웹 사이트 게시를** 클릭합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.  
    ![포털 게시](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> 사용자 지정 도메인 할당, ID 공급자 업데이트, 위임 설정, 로그인 및 제품 용어 지정 등과 같은 API Management 서비스 구성변경 후 포털을 다시 게시해야 합니다.

## <a name="visit-the-published-portal"></a>게시된 포털 방문

포털을 게시한 후 관리 패널과 동일한 URL에서 액세스할 수 `https://contoso-api.developer.azure-api.net`있습니다.(예: . 외부 방문자로 별도의 브라우저 세션 (시크릿 / 개인 정보 보호 브라우징 모드)에서 볼 수 있습니다.

## <a name="apply-the-cors-policy-on-apis"></a>API에 CORS 정책 적용

포털 방문자가 기본 제공 대화형 콘솔을 통해 API를 테스트할 수 있도록 API에서 CORS(원본 간 리소스 공유)를 사용하도록 설정해야 합니다. 자세한 내용은 [이 설명서 문서를](api-management-howto-developer-portal.md#cors) 참조하십시오.

## <a name="next-steps"></a>다음 단계

개발자 포털에 대해 자세히 알아보세요.

- [Azure API Management 개발자 포털 개요](api-management-howto-developer-portal.md)

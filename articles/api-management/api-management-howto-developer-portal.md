---
title: 새 개발자 포털 액세스 및 사용자 지정-Azure API Management | Microsoft Docs
description: API Management에서 새 개발자 포털을 사용 하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: 2756444db010e23f7114e99f474de9ce6614aaee
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837912"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Azure API Management에서 새 개발자 포털 액세스 및 사용자 지정

이 문서에서는 새 Azure API Management 개발자 포털에 액세스 하는 방법을 보여 줍니다. 웹 사이트의 모양을 사용자 지정 하는 것 외에도 시각적 편집기 환경 (콘텐츠 추가 및 편집)을 안내 합니다.

![새 API Management 개발자 포털](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a> 사전 요구 사항

- 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
- Azure API Management 인스턴스를 가져오고 게시합니다. 자세한 내용은 [가져오기 및 게시](import-and-publish.md)를 참조하세요.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> 새 개발자 포털은 현재 미리 보기로 제공 됩니다.

## <a name="managed-vs-self-hosted"></a>관리 및 자체 호스팅 버전

개발자 포털은 다음과 같은 두 가지 방법으로 빌드할 수 있습니다.

- **관리 버전** -포털을 편집 하 고 사용자 지정 합니다 .이 포털은 API Management 인스턴스에 빌드되고 URL `<your-api-management-instance-name>.developer.azure-api.net`을 통해 액세스할 수 있습니다.
- **자체 호스팅 버전** -API Management 인스턴스 외부에서 포털을 배포 하 고 자체 호스트 합니다. 이 방법을 사용 하면 포털의 코드 베이스를 편집 하 고 제공 된 핵심 기능을 확장할 수 있습니다. 세부 정보 및 지침은 [포털의 소스 코드를 사용 하 여 GitHub 리포지토리][1]를 참조 하세요.

## <a name="managed-access"></a>포털의 관리 되는 버전에 액세스

아래 단계에 따라 포털의 관리 되는 버전에 액세스 합니다.

1. Azure Portal에서 API Management 서비스 인스턴스로 이동 합니다.
1. 위쪽 탐색 모음에서 **새 개발자 포털 (미리 보기)** 단추를 클릭 합니다. 포털의 관리 버전을 포함 하는 새 브라우저 탭이 열립니다. 처음으로 포털에 액세스 하는 경우 기본 콘텐츠가 자동으로 프로 비전 됩니다.

## <a name="managed-tutorial"></a>포털의 관리 되는 버전 편집 및 사용자 지정

아래 비디오에서는 포털의 콘텐츠를 편집 하 고, 웹 사이트의 모양을 사용자 지정 하 고, 변경 내용을 게시 하는 방법을 보여 줍니다.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a>질문과 대답

이 섹션에서는 일반적인 특성상 새로운 개발자 포털에 대 한 일반적인 질문에 대답 합니다. 자체 호스팅 버전과 관련 된 질문은 [GitHub 리포지토리의 wiki 섹션](https://github.com/Azure/api-management-developer-portal/wiki)을 참조 하세요.

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>이전 개발자 포털에서 새 콘텐츠를 어떻게 마이그레이션할 수 있나요?

당신은 할 수 없어요. 포털이 호환 되지 않습니다.

### <a name="when-will-the-portal-become-generally-available"></a>포털이 일반 공급 될 시기

포털은 현재 미리 보기로 제공 되며, 일반적으로이 포털은 달력 년의 끝 (2019)으로 제공 됩니다. Preview 버전은 프로덕션 용도로는 사용할 수 없습니다.

### <a name="will-the-old-portal-be-deprecated"></a>이전 포털은 더 이상 사용 되지 않습니다.

예. 새 항목을 일반 공급 한 후에는 더 이상 사용 되지 않습니다. 문제가 있는 경우 [전용 GitHub 문제로](https://github.com/Azure/api-management-developer-portal/issues/121)발생 시킵니다.

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>새 포털에 이전 포털의 모든 기능이 있나요?

일반 공급의 목표는 기존 포털에서 시나리오 기반 기능 패리티를 제공 하는 것입니다. 그때 까지는 미리 보기 버전에서 구현 된 기능을 선택 하지 않았을 수 있습니다.

예외는 새 포털에서 사용할 수 없는 이전 포털의 *응용 프로그램* 및 *문제* 입니다. 이전 포털에서 *문제* 를 사용 하 고 새 포털에서이를 사용 하는 경우 [전용 GitHub 문제](https://github.com/Azure/api-management-developer-portal/issues/122)에 대 한 의견을 게시 합니다.

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>버그를 발견 했거나 기능을 요청 하 고 싶습니다.

잘하셨습니다. 사용자 의견을 제공 하거나 기능 요청을 제출 하거나 [GitHub 리포지토리의 문제 섹션](https://github.com/Azure/api-management-developer-portal/issues)을 통해 버그 보고서를 제출할 수 있습니다. 여기서는 `community` 레이블로 표시 된 문제에 대 한 의견을 보내주셔서 감사 합니다.

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>환경 간에 새 포털의 콘텐츠를 이동 하려고 합니다. 이 작업을 수행 하는 방법 및 자체 호스팅 버전을 사용 하려면 어떻게 해야 하나요?

이러한 작업은 두 포털 버전 (관리 및 자체 호스팅)에서 모두 수행할 수 있습니다. 새 개발자 포털은 API Management 서비스의 관리 API를 통해 콘텐츠 추출을 지원 합니다. Api는 [GitHub 리포지토리의 wiki 섹션에](https://github.com/Azure/api-management-developer-portal/wiki/)설명 되어 있습니다. 또한 시작 하는 데 도움이 될 수 있는 [스크립트](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat)를 작성 했습니다.

API Management DevOps resource kit를 사용 하 여이 프로세스를 정렬 하는 작업을 진행 중입니다.

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>새 *페이지*를 만들 때 *레이아웃* 을 선택 하려면 어떻게 해야 하나요?

*레이아웃* 은 url 템플릿과 *페이지* url을 일치 시켜 페이지에 적용 됩니다. 예를 들어 , URL 템플릿이 `/wiki/*` 인 레이아웃은 `/wiki/` 세그먼트가 있는 모든 `/wiki/getting-started` *페이지* (, `/wiki/styles`, 등)에 적용 됩니다.

### <a name="why-doesnt-the-interactive-developer-console-work"></a>대화형 개발자 콘솔이 작동 하지 않는 이유는 무엇 인가요?

CORS와 관련 된 것일 수 있습니다. 대화형 콘솔은 브라우저에서 클라이언트 쪽 API 요청을 만듭니다. API에 [cors 정책을](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) 추가 하 여 cors 문제를 해결할 수 있습니다. 모든 매개 변수를 수동으로 지정할 수 있습니다 (예: 다른 이름으로 https://contoso.com) 원본 사용 또는 와일드 `*` 카드 값 사용).

### <a name="where-can-i-file-bugs"></a>어디에서 버그를 파일에 저장할 수 있나요?

[GitHub 리포지토리에서](http://aka.ms/apimdevportal).

## <a name="next-steps"></a>다음 단계

새 개발자 포털에 대해 자세히 알아보세요.

- [소스 코드를 포함 하는 GitHub 리포지토리][1]
- [포털을 자체 호스팅하는 방법에 대 한 지침][2]
- [프로젝트의 공개 로드맵][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects
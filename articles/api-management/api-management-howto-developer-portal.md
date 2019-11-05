---
title: Azure API Management 개발자 포털 개요-Azure API Management | Microsoft Docs
description: API Management에서 개발자 포털에 대해 알아봅니다.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1311328dde6fc70202ce3c6271b33f79d52102cc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472225"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API Management 개발자 포털 개요

개발자 포털은 Api 설명서를 사용 하 여 자동으로 생성 되 고 완전히 사용자 지정할 수 있는 웹 사이트입니다. API 소비자는 api를 검색 하 고, Api를 사용 하 고, 액세스를 요청 하 고, 사용해 볼 수 있습니다.

이 문서에서는 API Management에서 개발자 포털 자체 호스팅 및 관리 되는 버전 간의 차이점을 설명 합니다. 또한 아키텍처를 설명 하 고 질문과 대답을 제공 합니다.

> [!WARNING]
> 새 개발자 포털이 현재 API Management 서비스에 롤아웃 되 고 있습니다.
> 서비스가 새로 만들어지거나 개발자 계층 서비스인 경우 최신 버전이 이미 있어야 합니다. 그렇지 않으면 게시 기능과 같은 문제가 발생할 수 있습니다. 기능 출시는 2019 년 11 월 11 일 월요일에 완료 됩니다.
>
> [미리 보기 버전에서 개발자 포털의 일반 공급 버전으로 마이그레이션하는 방법에 대해 알아봅니다](#preview-to-ga) .

![API Management 개발자 포털](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>관리 및 자체 호스팅 버전

개발자 포털은 다음과 같은 두 가지 방법으로 빌드할 수 있습니다.

- **관리 되는 버전** -포털을 편집 하 고 사용자 지정 합니다 .이 포털은 API Management 인스턴스에 기본 제공 되며 `<your-api-management-instance-name>.developer.azure-api.net`URL을 통해 액세스할 수 있습니다. 관리 되는 포털에 액세스 하 고 사용자 지정 하는 방법을 알아보려면 [이 설명서 문서](api-management-howto-developer-portal-customize.md) 를 참조 하세요.
- **자체 호스팅 버전** -API Management 인스턴스 외부에서 포털을 배포 하 고 자체 호스트 합니다. 이 방법을 사용 하면 포털의 코드 베이스를 편집 하 고 제공 된 핵심 기능을 확장할 수 있습니다. 또한 포털을 직접 최신 버전으로 업그레이드 해야 합니다. 세부 정보 및 지침은 [포털의 소스 코드를 사용 하 여 GitHub 리포지토리][1]를 참조 하세요. [관리 되는 버전에 대 한 자습서](api-management-howto-developer-portal-customize.md) 는 포털의 관리 패널을 안내 하며,이는 자체 호스팅 버전 에서도 제공 됩니다.

## <a name="portal-architectural-concepts"></a>포털 아키텍처 개념

포털 구성 요소는 *코드* 와 *내용*이라는 두 가지 범주로 논리적으로 나눌 수 있습니다.

*코드* 는 [GitHub 리포지토리][1] 에서 유지 관리 되며 다음을 포함 합니다.

- 위젯-시각적 요소를 나타내며 HTML, JavaScript, 스타일 기능, 설정 및 콘텐츠 매핑을 결합 합니다. 예를 들어 이미지, 텍스트 단락, 폼, Api 목록 등이 있습니다.
- 정의 스타일 지정-위젯의 스타일을 지정 하는 방법을 지정 합니다.
- 엔진-포털 콘텐츠에서 정적 웹 페이지를 생성 하 고 JavaScript로 작성 됩니다.
- 비주얼 편집기-브라우저 내 사용자 지정 및 제작 환경을 허용 합니다.

*콘텐츠* 는 *포털 콘텐츠* 및 *API Management 콘텐츠*의 두 하위 범주로 나뉩니다.

*포털 콘텐츠* 는 포털에만 적용 되며 다음을 포함 합니다.

- 페이지-예: 방문 페이지, API 자습서, 블로그 게시물
- 미디어-이미지, 애니메이션 및 기타 파일 기반 콘텐츠
- 레이아웃-URL에 대해 일치 하 고 페이지가 표시 되는 방식을 정의 하는 템플릿
- 스타일-스타일 정의의 값 (예: 글꼴, 색, 테두리)
- 설정-구성 (예: favicon, 웹 사이트 메타 데이터)

미디어를 제외한 *포털 콘텐츠*는 JSON 문서로 표시 됩니다.

*API Management 콘텐츠* 는 Api, 작업, 제품, 구독과 같은 엔터티를 포함 합니다.

포털은 [용지 비트 프레임 워크](https://paperbits.io/)의 조정 된 포크를 기반으로 합니다. 원래 용지 비트 기능은 API Management 특정 위젯 (예: Api 목록, 제품 목록) 및 콘텐츠 저장 및 검색을 위한 API Management 서비스에 대 한 커넥터를 제공 하도록 확장 되었습니다.

## <a name="faq"></a>질문과 대답

이 섹션에서는 일반적인 특성상 새로운 개발자 포털에 대 한 일반적인 질문에 답변 합니다. 자체 호스팅 버전과 관련 된 질문은 [GitHub 리포지토리의 wiki 섹션](https://github.com/Azure/api-management-developer-portal/wiki)을 참조 하세요.

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a>포털의 미리 보기 버전에서 마이그레이션할 수 있는 방법 <a id="preview-to-ga"/>

개발자 포털의 미리 보기 버전을 사용 하 여 API Management 서비스에서 미리 보기 콘텐츠를 프로 비전 했습니다. 사용자 환경을 개선 하기 위해 일반적으로 사용할 수 있는 버전에서 기본 콘텐츠가 크게 수정 되었습니다. 또한 새로운 위젯을 포함 합니다.

관리 되는 버전을 사용 중인 경우 **작업** 메뉴 섹션에서 **콘텐츠 다시 설정** 을 클릭 하 여 포털의 콘텐츠를 다시 설정 합니다. 이 작업을 확인 하면 포털의 모든 콘텐츠가 제거 되 고 새 기본 콘텐츠가 프로 비전 됩니다. 포털의 엔진이 API Management 서비스에서 자동으로 업데이트 되었습니다.

![포털 콘텐츠 다시 설정](media/api-management-howto-developer-portal/reset-content.png)

자체 호스팅 버전을 사용 하는 경우 `scripts/cleanup.bat` 사용 하 고 GitHub 리포지토리에서 `scripts/generate.bat` 하 여 기존 콘텐츠를 제거 하 고 새 콘텐츠를 프로 비전 합니다. 포털의 코드를 GitHub 리포지토리에서 최신 릴리스로 업그레이드 해야 합니다.

포털의 콘텐츠를 다시 설정 하지 않으려는 경우 페이지 전체에서 새로 사용 가능한 위젯을 사용 하는 것을 고려할 수 있습니다. 기존 위젯은 자동으로 최신 버전으로 업데이트 되었습니다.

일반 공급 발표 후에 포털을 프로 비전 한 경우 이미 새 기본 콘텐츠를 사용 해야 합니다. 사용자 측의 작업은 필요 하지 않습니다.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>이전 개발자 포털에서 새 개발자 포털로 마이그레이션하려면 어떻게 해야 하나요?

포털이 호환 되지 않으므로 콘텐츠를 수동으로 마이그레이션해야 합니다.

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>새 포털에 이전 포털의 모든 기능이 있나요?

새 개발자 포털은 *응용 프로그램* 및 *문제*를 지원 하지 않습니다. 이전 포털에서 *문제* 를 사용 하 고 새 포털에서이를 필요로 하는 경우 [전용 GitHub 문제](https://github.com/Azure/api-management-developer-portal/issues/122)에 대 한 의견을 게시 합니다.

### <a name="has-the-old-portal-been-deprecated"></a>이전 포털은 더 이상 사용 되지 않습니다.

이전 개발자 및 게시자 포털은 이제 *레거시* 기능이 며 보안 업데이트만 받게 됩니다. 새 기능은 새 개발자 포털 에서만 구현 됩니다.

레거시 포털의 사용 중단은 별도로 발표 될 예정입니다. 질문, 관심사 또는 의견이 있는 경우 [전용 GitHub 문제로](https://github.com/Azure/api-management-developer-portal/issues/121)제기 합니다.

### <a name="how-can-i-automate-portal-deployments"></a>포털 배포를 자동화 하려면 어떻게 해야 하나요?

관리 또는 자체 호스팅 버전을 사용 하는 경우와 관계 없이 REST API를 통해 프로그래밍 방식으로 개발자 포털의 콘텐츠를 액세스 하 고 관리할 수 있습니다.

이 API는 [GitHub 리포지토리의 wiki 섹션][2]에 설명 되어 있습니다. 환경 간의 포털 콘텐츠 (예: 테스트 환경에서 프로덕션 환경으로)의 마이그레이션을 자동화 하는 데에도 사용할 수 있습니다. 이 프로세스에 대 한 자세한 내용은 GitHub의 [이 문서 문서에서](https://aka.ms/apimdocs/migrateportal) 확인할 수 있습니다.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>포털이 Azure Resource Manager 템플릿을 지원 하나요? 아니면 API Management DevOps Resource Kit와 호환 되나요?

아니요.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>관리 되는 포털 종속성에 대 한 추가 VNET 연결을 사용 하도록 설정 해야 하나요?

아니요.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console-what-should-i-do"></a>대화형 콘솔을 사용 하는 경우 CORS 오류가 발생 합니다. 어떻게 해야 하나요?

대화형 콘솔은 브라우저에서 클라이언트 쪽 API 요청을 만듭니다. API에 [cors 정책을](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) 추가 하 여 cors 문제를 해결할 수 있습니다. 모든 매개 변수를 수동으로 지정 하거나 (예: https://contoso.com) 원본으로) 와일드 카드 `*` 값을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

새 개발자 포털에 대해 자세히 알아보세요.

- [관리 되는 개발자 포털 액세스 및 사용자 지정](api-management-howto-developer-portal-customize.md)
- [자체 호스트 된 포털 버전 설정][2]

다른 리소스 찾아보기:

- [소스 코드를 포함 하는 GitHub 리포지토리][1]
- [프로젝트의 공개 로드맵][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects
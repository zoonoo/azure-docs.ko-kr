---
title: 레거시 개발자 포털에서 새 개발자 포털로 마이그레이션
titleSuffix: Azure API Management
description: API Management에서 레거시 개발자 포털을 새 개발자 포털로 마이그레이션하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: f5105c685de4b3ccdffe69eec8ee8eeb32976c1f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326024"
---
# <a name="migrate-to-the-new-developer-portal"></a>새 개발자 포털로 마이그레이션

이 문서에서는 더 이상 사용 되지 않는 레거시 포털에서 API Management의 새 개발자 포털로 마이그레이션하기 위해 수행 해야 하는 단계를 설명 합니다.

> [!IMPORTANT]
> 이전 개발자 포털은 더 이상 사용 되지 않으며 보안 업데이트만 받게 됩니다. 모든 API Management 서비스에서 제거 될 때 2023 평상시와 같이 계속 해 서 사용할 수 있습니다.

![API Management 개발자 포털](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>새 개발자 포털의 향상 된 기능

새 개발자 포털은 더 이상 사용 되지 않는 포털의 많은 제한 사항을 해결 합니다. [콘텐츠 편집용 시각적 끌어서 놓기 편집기](api-management-howto-developer-portal-customize.md) 와 디자이너의 전용 패널에서 웹 사이트의 스타일을 적용할 수 있습니다. 페이지, 사용자 지정 및 구성은 API Management 서비스에 Azure Resource Manager 리소스로 저장 되므로 [포털 배포를 자동화할](api-management-howto-developer-portal.md#automate)수 있습니다. 마지막으로, 포털의 코드 베이스가 오픈 소스 이므로 [사용자 지정 기능을 사용 하 여 확장할 수](api-management-howto-developer-portal.md#managed-vs-self-hosted)있습니다.

## <a name="how-to-migrate-to-new-developer-portal"></a>새 개발자 포털로 마이그레이션하는 방법

새 개발자 포털은 더 이상 사용 되지 않는 포털과 호환 되지 않으며 자동화 된 마이그레이션은 가능 하지 않습니다. 콘텐츠 (페이지, 텍스트, 미디어 파일)를 수동으로 다시 만들고 새 포털의 모양을 사용자 지정 해야 합니다. 정확한 단계는 포털의 사용자 지정 및 복잡성에 따라 달라 집니다. 지침은 [개발자 포털 자습서](api-management-howto-developer-portal-customize.md) 를 참조 하십시오. Api, 제품, 사용자, id 공급자의 목록과 같은 나머지 구성은 두 포털에서 자동으로 공유 됩니다.

> [!IMPORTANT]
> 이전에 새 개발자 포털을 실행 했지만 변경 하지 않은 경우 [기본 콘텐츠를 다시 설정](api-management-howto-developer-portal.md#preview-to-ga) 하 여 최신 버전으로 업데이트 합니다.

사용 되지 않는 포털에서 마이그레이션하는 경우 다음 사항을 염두에 두어야 합니다.

- 사용자 지정 도메인을 통해 개발자 포털을 노출 하는 경우 새 개발자 포털에 [도메인을 할당](configure-custom-domain.md) 합니다. Azure Portal의 드롭다운에서 **개발자 포털** 옵션을 사용 합니다.
- Api에 [CORS 정책을 적용](api-management-howto-developer-portal.md#cors) 하 여 대화형 테스트 콘솔을 사용 하도록 설정 합니다.
- 사용자 지정 CSS를 삽입 하 여 포털의 스타일을 지정 하는 경우 [기본 제공 디자인 패널을 사용 하 여 스타일을 복제](api-management-howto-developer-portal-customize.md)해야 합니다. 새 포털에서는 CSS 삽입이 허용 되지 않습니다.
- [새 포털의 자체 호스팅 버전](api-management-howto-developer-portal.md#managed-vs-self-hosted)에만 사용자 지정 JavaScript를 삽입할 수 있습니다.
- API Management 가상 네트워크에 있고 Application Gateway를 통해 인터넷에 노출 되는 경우 정확한 구성 단계는 [이 설명서 문서를 참조 하세요](api-management-howto-integrate-internal-vnet-appgateway.md) . 그러려면 다음 작업을 수행해야 합니다.

    - API Management의 관리 끝점에 대 한 연결을 사용 하도록 설정 합니다.
    - 새 포털 끝점에 대 한 연결을 사용 하도록 설정 합니다.
    - 선택한 웹 응용 프로그램 방화벽 규칙을 사용 하지 않도록 설정 합니다.

- 명시적으로 정의 된 사용 되지 않는 포털 URL을 포함 하도록 기본 전자 메일 알림 템플릿을 변경한 경우 포털 URL 매개 변수를 사용 하거나 새 포털 URL을 가리키도록 변경 합니다. 템플릿에서 기본 제공 포털 URL 매개 변수를 대신 사용 하는 경우에는 변경이 필요 하지 않습니다.
- 새 개발자 포털에서는 *문제* 및 *응용 프로그램이* 지원 되지 않습니다.
- 새 개발자 포털에서는 Facebook, Microsoft, Twitter 및 Google과의 직접 통합을 id 공급자로 지원 하지 않습니다. Azure AD B2C를 통해 이러한 공급자와 통합할 수 있습니다.
- 위임을 사용 하는 경우 응용 프로그램에서 반환 URL을 변경 하 고 *SSO Url 생성* 끝점 대신 [ *공유 액세스 토큰 가져오기* API 끝점](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) 을 사용 합니다.
- Azure AD를 id 공급자로 사용 하는 경우:

    - 새 개발자 포털 도메인을 가리키도록 응용 프로그램의 반환 URL을 변경 합니다.
    - 응용 프로그램의 반환 URL 접미사를에서로 수정 합니다 `/signin-aad` `/signin` .

- Azure AD B2C을 id 공급자로 사용 하는 경우:

    - 새 개발자 포털 도메인을 가리키도록 응용 프로그램의 반환 URL을 변경 합니다.
    - 응용 프로그램의 반환 URL 접미사를에서로 수정 합니다 `/signin-aad` `/signin` .
    - 응용 프로그램 클레임에 *지정 된 이름*, *성*및 *사용자의 개체 ID* 를 포함 합니다.

- 대화형 테스트 콘솔에서 OAuth 2.0를 사용 하는 경우 새 개발자 포털 도메인을 가리키도록 응용 프로그램의 반환 URL을 변경 하 고 접미사를 수정 합니다.

    - `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` `/signin-oauth/code/callback/[serverName]` 인증 코드 부여 흐름에 대 한입니다.
    - `/docs/services/[serverName]/console/oauth2/implicit/callback` `/signin-oauth/implicit/callback` 암시적 허용 흐름의 경우에서로
- 대화형 테스트 콘솔에서 Openid connect Connect를 사용 하는 경우 새 개발자 포털 도메인을 가리키도록 응용 프로그램의 반환 URL을 변경 하 고 접미사를 수정 합니다.

    - `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` `/signin-oauth/code/callback/[serverName]` 인증 코드 부여 흐름에 대 한입니다.
    - `/docs/services/[serverName]/console/openidconnect/implicit/callback` `/signin-oauth/implicit/callback` 암시적 허용 흐름의 경우에서로

## <a name="next-steps"></a>다음 단계

개발자 포털에 대해 자세히 알아보세요.

- [Azure API Management 개발자 포털 개요](api-management-howto-developer-portal.md)
- [개발자 포털 액세스 및 사용자 지정](api-management-howto-developer-portal-customize.md)
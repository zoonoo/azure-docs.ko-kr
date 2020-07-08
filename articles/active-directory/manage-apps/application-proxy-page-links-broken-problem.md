---
title: 페이지의 링크가 애플리케이션 프록시 애플리케이션에서 작동하지 않습니다.
description: Azure AD와 통합한 애플리케이션 프록시 애플리케이션에 대한 링크가 끊어지는 문제를 해결하는 방법
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 050874b301628348b513b8b5b0c2b878a1dcecaa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760204"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>페이지의 링크가 애플리케이션 프록시 애플리케이션에서 작동하지 않습니다.

이 문서는 Azure Active Directory 애플리케이션 프록시 애플리케이션의 링크가 올바르게 작동하지 않는 문제를 해결하는 데 도움이 됩니다.

## <a name="overview"></a>개요 
애플리케이션 프록시 앱을 게시한 후에 애플리케이션에서 기본적으로 작동하는 유일한 링크는 게시된 루트 URL 내에 포함된 대상에 대한 링크입니다. 애플리케이션 내에 있는 링크가 작동하지 않으면 애플리케이션의 내부 URL은 아마도 애플리케이션 내에서 링크의 모든 대상을 포함하지 않습니다.

**이런 문제가 발생하는 이유는 무엇인가요?** 애플리케이션에서 링크를 클릭하면 애플리케이션 프록시는 동일한 애플리케이션 내에서 내부 URL 또는 외부에서 사용 가능한 URL을 확인하려고 합니다. 링크가 동일한 애플리케이션에 속하지 않는 내부 URL을 가리키는 경우 이러한 버킷 중 하나에 속하지 않고 찾을 수 없다는 오류가 발생합니다.

## <a name="ways-you-can-resolve-broken-links"></a>끊어진 링크 문제를 해결할 수 있는 방법

이 문제를 해결하는 방법은 세 가지가 있습니다. 아래 문제는 복잡성이 높은 순으로 나열됩니다.

1.  내부 URL이 애플리케이션의 모든 관련 링크를 포함하는 루트인지 확인합니다. 이렇게 하면 모든 링크를 동일한 애플리케이션 내에 게시된 콘텐츠로 해결할 수 있습니다.

    내부 URL을 변경하지만 사용자의 방문 페이지를 변경하지 않으려는 경우 홈 페이지 URL을 이전에 게시된 내부 URL로 변경합니다. "Azure Active Directory"-앱 등록으로 이동 하 여이 작업을 수행할 수 있습니다 &gt; &gt; . 응용 프로그램 브랜딩을 선택 &gt; 합니다. 브랜딩 섹션에는 원하는 방문 페이지로 조정할 수 있는 "홈페이지 URL" 필드가 표시 됩니다. 레거시 앱 등록 환경을 계속 사용 하는 경우 속성 탭에 "홈 페이지 URL" 정보가 표시 됩니다. 
    
    > [!IMPORTANT]
    > 위의 변경 내용을 적용 하기 위해 Azure AD에서 응용 프로그램 개체를 수정 하는 데 필요한 권한이 필요 합니다. 사용자에 게 Azure AD에서 사용자에 게 응용 프로그램 modificaion 권한을 부여 하는 [응용 프로그램 관리자](../users-groups-roles/roles-delegate-app-roles.md#assign-built-in-application-admin-roles) 역할을 할당 받아야 합니다.
    >

2.  애플리케이션에서 FQDN(정규화된 도메인 이름)을 사용하는 경우 [사용자 지정 도메인](application-proxy-configure-custom-domain.md)을 사용하여 애플리케이션을 게시할 수 있습니다. 이 기능을 사용하면 동일한 URL을 내부 및 외부에서 모두 사용할 수 있습니다.

    내부 URL에 대한 애플리케이션 내의 링크도 외부에서 인식하므로 이 옵션은 애플리케이션의 링크가 애플리케이션 프록시를 통해 외부에서 액세스될 수 있도록 합니다. 모든 링크는 아직 게시된 애플리케이션에 속해야 합니다. 그러나 이 옵션을 사용하면 링크가 동일한 애플리케이션에 속하지 않고 여러 애플리케이션에 속할 수 있습니다.

3.  이러한 옵션을 모두 실행할 수 없는 경우 인라인 링크 변환을 구현할 수 있는 여러 가지 옵션이 있습니다. 이러한 옵션에는 Intune Managed Browser, 내 앱 확장 사용 또는 애플리케이션의 링크 변환 설정 사용이 포함됩니다. 이러한 각 옵션 및 사용하도록 설정하는 방법을 자세히 알아보려면 [Azure AD 애플리케이션 프록시를 사용하여 게시된 앱에 대해 하드 코드된 링크 리디렉션](application-proxy-configure-hard-coded-link-translation.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md)


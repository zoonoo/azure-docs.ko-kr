---
title: Azure AD 조직에 대한 등록 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 특정 Azure Active Directory 조직에 대한 로그인을 설정합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5b21fcd2d3ec5560b01352b112e9ed1bb2404766
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678057"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 특정 Azure Active Directory 조직에 대한 로그인 설정

Azure AD B2C에서 Azure AD(Azure Active Directory)를 [ID 공급자](authorization-code-flow.md)로 사용하려면 해당 계정을 나타내는 애플리케이션을 만들어야 합니다. 이 문서에서는 Azure AD B2C의 사용자 흐름을 사용하여 특정 Azure AD 조직의 사용자에 대한 로그인을 설정하는 방법을 보여 줍니다.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>테넌트에서 Azure AD를 ID 공급자로 구성

1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트를 포함하는 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **ID 공급자를**선택한 다음 **새 OpenID 연결 공급자를**선택합니다.
1. **이름을**입력합니다. 예를 들어 *Contoso Azure AD*를 입력합니다.
1. **메타데이터 URL의**경우 Azure `{tenant}` AD 테넌트의 도메인 이름으로 대체하는 다음 URL을 입력합니다.

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`)을 입력합니다.

1. **클라이언트 ID의**경우 이전에 기록한 응용 프로그램 ID를 입력합니다.
1. **클라이언트 보안**의 경우 이전에 기록한 클라이언트 비밀을 입력합니다.
1. **범위의**경우 를 `openid profile`입력합니다.
1. **응답 유형**및 응답 모드에 대한 기본값을 그대로 **둡니다.**
1. (선택 사항) 도메인 **힌트를**보려면 `contoso.com`을 입력합니다. 자세한 내용은 [Azure Active Directory B2C를 사용하여 직접 로그인 설정](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요.
1. **ID 공급자 클레임 매핑에서**다음 클레임을 선택합니다.

    * **사용자 ID**: *oid*
    * **표시 이름**: *이름*
    * **주어진 이름**: *given_name*
    * **성**: *family_name*
    * **이메일**: *unique_name*

1. **저장**을 선택합니다.

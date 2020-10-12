---
title: Azure AD 조직에 대 한 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 특정 Azure Active Directory 조직에 대한 로그인을 설정합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e4fa4b64c6519df90d5883e8c5760b3ed2ce0337
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90004462"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 특정 Azure Active Directory 조직에 대한 로그인 설정

Azure AD B2C에서 Azure AD(Azure Active Directory)를 [ID 공급자](authorization-code-flow.md)로 사용하려면 해당 계정을 나타내는 애플리케이션을 만들어야 합니다. 이 문서에서는 Azure AD B2C의 사용자 흐름을 사용하여 특정 Azure AD 조직의 사용자에 대한 로그인을 설정하는 방법을 보여 줍니다.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>테넌트에서 Azure AD를 ID 공급자로 구성

1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **ID 공급자**를 선택한 다음, **새 OpenID Connect 공급자**를 선택합니다.
1. **이름**을 입력합니다. 예를 들어 *Contoso Azure AD*를 입력합니다.
1. **메타데이터 URL**에 대해 다음 URL을 입력합니다. 여기서 `{tenant}`은 Azure AD 테넌트의 도메인 이름으로 바꿉니다.

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    예들 들어 `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`입니다.
    예들 들어 `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`입니다.

1. **Client ID**에 대해 이전에 기록한 애플리케이션 ID를 입력합니다.
1. 이전에 기록해 두었던 클라이언트 암호를 **클라이언트 암호**에 입력합니다.
1. **범위**에 대해를 입력 `openid profile` 합니다.
1. **응답 유형**및 **응답 모드**에 대 한 기본값을 그대로 둡니다.
1. 필드 **도메인 힌트**에 대해를 입력 `contoso.com` 합니다. 자세한 내용은 [Azure Active Directory B2C를 사용하여 직접 로그인 설정](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요.
1. **Id 공급자 클레임 매핑**에서 다음 클레임을 선택 합니다.

    * **사용자 ID**: *oid*
    * **표시 이름**: *name*
    * **지정된 이름**: *given_name*
    * **성**: *family_name*
    * **이메일**: *unique_name*

1. **저장**을 선택합니다.

---
title: 인증
description: 인증 작동 방식 설명
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 35fd78a9d55dc684045fdb4b83691c1613801421
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97724886"
---
# <a name="configure-authentication"></a>인증 구성

Azure Remote Rendering은 [ASA(Azure Spatial Anchors)](../../spatial-anchors/concepts/authentication.md?tabs=csharp)와 동일한 인증 메커니즘을 사용합니다. 클라이언트는 REST API를 성공적으로 호출하기 위해 다음 중 *하나* 를 설정해야 합니다.

* **AccountKey**: Azure Portal의 Remote Rendering 계정에 대한 "키" 탭에서 가져올 수 있습니다. 계정 키는 개발/프로토타입에만 권장됩니다.
    ![계정 ID](./media/azure-account-primary-key.png)

* **AccountDomain**: Azure Portal의 Remote Rendering 계정에 대한 "개요" 탭에서 가져올 수 있습니다.
    ![계정 도메인](./media/azure-account-domain.png)

* **AuthenticationToken**: [MSAL 라이브러리](../../active-directory/develop/msal-overview.md)를 사용하여 가져올 수 있는 Azure AD 토큰입니다. 사용자 자격 증명을 수락하고 이러한 자격 증명을 사용하여 액세스 토큰을 가져오는 데 사용할 수 있는 여러 가지 흐름이 있습니다.

* **MRAccessToken**: Azure Mixed Reality STS(보안 토큰 서비스)에서 가져올 수 있는 MR 토큰입니다. 아래와 유사한 REST 호출을 사용하여 `https://sts.<accountDomain>` 엔드포인트에서 검색됩니다.

    ```rest
    GET https://sts.southcentralus.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.southcentralus.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    권한 부여 헤더의 형식은 `Bearer <Azure_AD_token>` 또는 `Bearer <accoundId>:<accountKey>`입니다. 전자는 보안을 위해 선호됩니다. 이 REST 호출에서 반환된 토큰은 MR 액세스 토큰입니다.

## <a name="authentication-for-deployed-applications"></a>배포된 애플리케이션에 대한 인증

계정 키는 개발 중에만 빠른 프로토타입을 위해 권장됩니다. 포함된 계정 키를 사용하여 애플리케이션을 프로덕션에 제공하지 않는 것이 좋습니다. 권장되는 접근 방식은 사용자 기반 또는 서비스 기반 Azure AD 인증 방식을 사용하는 것입니다.

 Azure AD 인증은 [ASA(Azure Spatial Anchors)](../../spatial-anchors/index.yml) 서비스의 [Azure AD 사용자 인증](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) 섹션에 설명되어 있습니다.

 자세한 내용은 [자습서: Azure Remote Rendering 및 모델 스토리지 보안 설정 - Azure Active Directory 인증](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)을 참조하세요.

## <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

서비스에 부여된 액세스 수준을 제어하려면 역할 기반 액세스 권한을 부여할 때 다음 역할을 사용합니다.

* **Remote Rendering 관리자**: 사용자에게 Azure Remote Rendering에 대한 변환, 세션 관리, 렌더링, 진단 기능을 제공합니다.
* **Remote Rendering 클라이언트**: 사용자에게 Azure Remote Rendering에 대한 세션 관리, 렌더링, 진단 기능을 제공합니다.

## <a name="next-steps"></a>다음 단계

* [계정 만들기](create-an-account.md)
* [인증을 위해 Azure Frontend API 사용](frontend-apis.md)
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
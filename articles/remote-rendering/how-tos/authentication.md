---
title: 인증
description: 인증이 작동 하는 방식을 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: dc325fdf68c5afbb122f9e77c5509a6a8053a12e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427470"
---
# <a name="configure-authentication"></a>인증 구성

Azure 원격 렌더링은 [Azure 공간 앵커 (global.asa)](../../spatial-anchors/concepts/authentication.md?tabs=csharp)와 동일한 인증 메커니즘을 사용 합니다. 클라이언트는 REST Api를 성공적으로 호출 하기 위해 다음 중 *하나* 를 설정 해야 합니다.

* **AccountKey**: Azure Portal의 원격 렌더링 계정에 대 한 "키" 탭에서 가져올 수 있습니다. 계정 키는 개발/프로토타입화에만 권장 됩니다.
    ![계정 ID](./media/azure-account-primary-key.png)

* **Authenticationtoken**:은 [msal 라이브러리](../../active-directory/develop/msal-overview.md)를 사용 하 여 가져올 수 있는 Azure AD 토큰입니다. 사용자 자격 증명을 수락 하 고 이러한 자격 증명을 사용 하 여 액세스 토큰을 가져오는 데 사용할 수 있는 여러 가지 흐름이 있습니다.

* **MRAccessToken**:는 MR 토큰으로, Azure MIXED Reality STS (보안 토큰 서비스)에서 가져올 수 있습니다. `https://sts.mixedreality.azure.com`아래 호출과 유사한 REST 호출을 사용 하 여 끝점에서 검색 됩니다.

    ```rest
    GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    권한 부여 헤더의 형식은 `Bearer <Azure_AD_token>` 또는 `Bearer <accoundId>:<accountKey>` 입니다. 이전에는 보안을 위한 것이 좋습니다. 이 REST 호출에서 반환 된 토큰은 MR 액세스 토큰입니다.

## <a name="authentication-for-deployed-applications"></a>배포 된 응용 프로그램에 대 한 인증

계정 키는 개발 하는 동안 빠른 프로토타입 제작에 권장 됩니다. 응용 프로그램에 포함 된 계정 키를 사용 하 여 프로덕션 환경에 응용 프로그램을 제공 하지 않는 것이 좋습니다. 권장 되는 방법은 사용자 기반 또는 서비스 기반 Azure AD 인증 방법을 사용 하는 것입니다.

 Azure AD 인증은 azure [공간 앵커 (global.asa)](../../spatial-anchors/index.yml) 서비스의 [azure ad 사용자 인증](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) 섹션에 설명 되어 있습니다.

 자세한 내용은 [자습서: Azure 원격 렌더링 및 모델 저장소 보안 설정-Azure Active Directory 인증](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication) 을 참조 하세요.

## <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

서비스에 부여 된 액세스 수준을 제어 하려면 역할 기반 액세스 권한을 부여할 때 다음 역할을 사용 합니다.

* **원격 렌더링 관리자**: Azure 원격 렌더링을 위한 변환, 관리 세션, 렌더링 및 진단 기능을 사용자에 게 제공 합니다.
* **원격 렌더링 클라이언트**: Azure 원격 렌더링을 위한 관리 세션, 렌더링 및 진단 기능을 사용자에 게 제공 합니다.

## <a name="next-steps"></a>다음 단계

* [계정 만들기](create-an-account.md)
* [인증을 위해 Azure Frontend API 사용](frontend-apis.md)
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
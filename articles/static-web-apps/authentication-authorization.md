---
title: Azure Static Web Apps에 대한 인증 및 권한 부여
description: 다른 권한 부여 공급자를 사용하여 정적 앱을 보호하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e95cd313d341844eabf4f5c5feae8a8ca3dc9c2e
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826538"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Azure Static Web Apps에 대한 인증 및 권한 부여 미리 보기

Azure Static Web Apps는 다음과 같은 공급자를 사용하여 인증을 관리하여 인증 환경을 간소화합니다.

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

공급자별 [초대](#invitations)는 사용자를 역할과 연결하고, 권한 있는 사용자는 _routes.json_ 파일에 정의된 규칙에 따라 [경로](routes.md)에 대한 액세스 권한을 부여 받습니다.

모든 인증 공급자는 기본적으로 사용하도록 설정됩니다. 인증 공급자를 제한하려면 사용자 지정 경로 규칙을 사용하여 [액세스를 차단](#block-an-authorization-provider)합니다.

인증 및 권한 부여에 대한 항목은 라우팅 개념과 크게 겹칩니다. 이 문서와 함께 [라우팅 가이드](routes.md)를 참조하세요.

## <a name="roles"></a>역할

정적 웹앱에 액세스하는 모든 사용자는 하나 이상의 역할에 속해 있습니다.  사용자가 속할 수 있는 두 가지 기본 제공 역할은 다음과 같습니다.

- **익명**: 모든 사용자는 _익명_ 역할에 자동으로 속합니다.
- **인증됨**: 로그인된 모든 사용자는 _인증됨_ 역할에 속합니다.

기본 제공 역할 외에 새 역할을 만들어 초대를 통해 사용자에게 할당하고, _routes.json_ 파일에서 참조할 수 있습니다.

## <a name="role-management"></a>역할 관리

### <a name="add-a-user-to-a-role"></a>역할에 사용자 추가

사용자를 웹 사이트에 추가하려면 특정 역할에 사용자를 연결할 수 있는 초대를 생성합니다. 역할은 _routes.json_ 파일에서 정의 및 유지 관리됩니다.

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>초대 만들기

초대는 개별 인증 공급자와 관련이 있으므로 지원할 공급자를 선택할 때 앱의 요구 사항을 고려해야 합니다. 일부 공급자는 사용자의 이메일 주소를 노출하지만 다른 공급자는 사이트의 사용자 이름만 제공합니다.

<a name="provider-user-details" id="provider-user-details"></a>

| 권한 부여 공급자 | 사용자의 다음 사항 노출  |
| ---------------------- | ----------------- |
| Azure Active Directory | 이메일 주소     |
| Facebook               | 이메일 주소     |
| GitHub                 | 사용자 이름          |
| Google<sup>1</sup>     | 이메일 주소     |
| Twitter                | 사용자 이름          |

1. [Azure Portal](https://portal.azure.com)에서 Static Web Apps 리소스로 이동합니다.
1. _설정_ 아래에서 **역할 관리**를 클릭합니다.
1. **초대** 단추를 클릭합니다.
1. 옵션 목록에서 _권한 부여 공급자_를 선택합니다.
1. _초대 대상자 정보_ 상자에 받는 사람의 사용자 이름 또는 이메일 주소를 추가합니다.
    - GitHub 및 Twitter의 경우 사용자 이름을 입력합니다. 다른 모든 경우에는 받는 사람의 이메일 주소를 입력합니다.
1. _도메인_ 드롭다운에서 정적 사이트의 도메인을 선택합니다.
    - 선택한 도메인은 초대에 표시되는 도메인입니다. 사이트와 연결된 사용자 지정 도메인이 있는 경우 사용자 지정 도메인을 선택하는 것이 좋습니다.
1. _역할_ 상자에 쉼표로 구분된 역할 이름 목록을 추가합니다.
1. 초대가 유효한 상태로 유지되도록 할 최대 시간을 입력합니다.
    - 가능한 최대 제한은 168시간(7일)입니다.
1. **생성** 단추를 클릭합니다.
1. _초대 링크_ 상자에서 링크를 복사합니다.
1. 앱에 대한 액세스를 허용하는 사람에게 초대 링크를 이메일로 보냅니다.

사용자가 초대의 링크를 클릭하면 해당 계정으로 로그인하라는 메시지가 표시됩니다. 성공적으로 로그인하면 사용자는 선택한 역할과 연결됩니다.

> [!CAUTION]
> 경로 규칙이 선택한 인증 공급자와 충돌하지 않는지 확인합니다. 경로 규칙을 사용하여 공급자를 차단하면 사용자가 초대를 수락하지 못합니다.

### <a name="update-role-assignments"></a>역할 할당 업데이트

1. [Azure Portal](https://portal.azure.com)에서 Static Web Apps 리소스로 이동합니다.
1. _설정_ 아래에서 **역할 관리**를 클릭합니다.
1. 목록에서 사용자를 클릭합니다.
1. _역할_ 상자에서 역할 목록을 편집합니다.
1. **업데이트** 단추를 클릭합니다.

### <a name="remove-user"></a>사용자 제거

1. [Azure Portal](https://portal.azure.com)에서 Static Web Apps 리소스로 이동합니다.
1. _설정_ 아래에서 **역할 관리**를 클릭합니다.
1. 목록에서 사용자를 찾습니다.
1. 사용자의 행에 대한 확인란을 선택합니다.
1. **삭제** 단추를 클릭합니다.

사용자를 제거하는 경우 다음 항목을 염두에 두어야 합니다.

1. 사용자를 제거하면 해당 권한이 무효화됩니다.
1. 전 세계 전파는 몇 분 정도 걸릴 수 있습니다.
1. 사용자를 앱에 다시 추가하면 [`userId`가 변경됩니다](user-information.md).

## <a name="remove-personal-identifying-information"></a>개인 식별 정보 제거

최종 사용자로 애플리케이션에 동의하면 애플리케이션은 ID 공급자에 따라 이메일 주소 또는 사용자 이름에 액세스할 수 있습니다. 이 정보가 제공되면 애플리케이션의 소유자는 개인 식별 정보를 관리하는 방법을 결정합니다.

최종 사용자는 개별 웹 앱의 관리자에 게 연락 하 여 해당 시스템에서이 정보를 해지 해야 합니다.

Azure Static Web Apps 플랫폼에서 개인 식별 정보를 제거하고, 플랫폼이 향후 요청에 대해 이 정보를 제공하지 않도록 하려면 URL을 사용하여 요청을 제출합니다.

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

플랫폼이 이후 요청에 대해 이 정보를 개별 앱에 제공하지 않도록 하려면 다음 URL에 요청을 제출합니다.

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>시스템 폴더

Azure Static Web Apps는 `/.auth` system 폴더를 사용하여 권한 부여 관련 API에 대한 액세스를 제공합니다. `/.auth` 폴더 아래의 경로를 최종 사용자에게 직접 노출하는 대신, 친숙한 URL을 만들기 위해 [라우팅 규칙](routes.md)을 만드는 것이 좋습니다.

## <a name="login"></a>로그인

공급자별 로그인 경로를 찾으려면 다음 표를 사용합니다.

| 권한 부여 공급자 | 로그인 경로             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHub                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

예를 들어 GitHub를 사용하여 로그인하려면 다음 코드 조각과 같은 로그인 링크를 포함할 수 있습니다.

```html
<a href="/.auth/login/github">Login</a>
```

둘 이상의 공급자를 지원하도록 선택한 경우에는 웹 사이트의 각 공급자별 링크를 노출해야 합니다.

[경로 규칙](routes.md)을 사용하여 기본 공급자를 _/login_과 같은 친숙한 경로에 매핑할 수 있습니다.

```json
{
  "route": "/login",
  "serve": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>사후 로그인 리디렉션

사용자가 로그인한 후 특정 페이지로 돌아가도록 하려면 `post_login_redirect_uri` 쿼리 문자열 매개 변수에 URL을 제공합니다.


## <a name="logout"></a>Logout

`/.auth/logout` 경로는 웹 사이트에서 사용자를 로그아웃합니다. 다음 예제와 같이 사용자가 로그아웃할 수 있도록 사이트 탐색에 링크를 추가할 수 있습니다.

```html
<a href="/.auth/logout">Log out</a>
```

[경로 규칙](routes.md)을 사용하여 _/logout_과 같은 친숙한 경로를 매핑할 수 있습니다.

```json
{
  "route": "/logout",
  "serve": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>사후 로그아웃 리디렉션

사용자가 로그아웃한 후 특정 페이지로 돌아가도록 하려면 `post_logout_redirect_uri` 쿼리 문자열 매개 변수에 URL을 제공합니다.

## <a name="block-an-authorization-provider"></a>권한 부여 공급자 차단

앱에서 권한 부여 공급자 사용을 제한하는 것이 좋습니다. 예를 들어 앱이 [이메일 주소를 노출하는 공급자](#provider-user-details)에서만 표준화하려고 할 수 있습니다.

공급자를 차단하기 위해 [경로 규칙](routes.md)을 만들어 차단된 공급자별 경로에 대한 요청에 대해 404를 반환할 수 있습니다. 예를 들어 Twitter를 공급자로 제한하려면 다음 경로 규칙을 추가합니다.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>제한

일반적인 제한 사항 및 제한 사항은 [할당량 문서](quotas.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 인증 및 권한 부여 데이터 액세스](user-information.md)

<sup>1</sup> 보류 중인 외부 인증

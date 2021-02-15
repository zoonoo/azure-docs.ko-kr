---
title: Microsoft id 플랫폼 범위, 사용 권한, & 동의
description: 범위, 사용 권한 및 동의를 포함 하 여 Microsoft id 플랫폼 끝점의 권한 부여에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 2658c088304eba457b25bb3dc421b356ba70b57f
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102481"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Microsoft ID 플랫폼의 권한 및 동의

Microsoft id 플랫폼과 통합 되는 응용 프로그램은 사용자와 관리자에 게 데이터에 액세스할 수 있는 방법을 제어 하는 권한 부여 모델을 따릅니다. 권한 부여 모델의 구현이 Microsoft id 플랫폼에서 업데이트 되었습니다. 앱이 Microsoft id 플랫폼과 상호 작용 해야 하는 방식을 변경 합니다. 이 문서에서는 범위, 사용 권한 및 동의를 포함하여 이 권한 부여 모델의 기본 개념에 대해 설명합니다.

## <a name="scopes-and-permissions"></a>범위 및 사용 권한

Microsoft ID 플랫폼은 [OAuth 2.0](active-directory-v2-protocols.md) 권한 부여 프로토콜을 구현합니다. OAuth 2.0은 사용자 대신 타사 앱에서 웹에 호스트된 리소스에 액세스할 수 있도록 하는 방법입니다. Microsoft id 플랫폼과 통합 되는 모든 웹 호스팅 리소스에는 리소스 식별자 또는 *응용 프로그램 ID URI* 가 있습니다. 

Microsoft 웹 호스팅 리소스의 몇 가지 예는 다음과 같습니다.

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 메일 API: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Microsoft ID 플랫폼과 통합된 타사 리소스의 경우도 마찬가지입니다. 이러한 리소스는 해당 리소스의 기능을 더 작은 청크로 나누는 데 사용할 수 있는 사용 권한 집합을 정의할 수도 있습니다. 예를 들어 [Microsoft Graph](https://graph.microsoft.com)는 특히 다음 작업을 수행할 수 있는 사용 권한을 정의했습니다.

* 사용자의 일정 읽기
* 사용자의 일정에 쓰기
* 사용자로 메일 보내기

이러한 권한 정의 유형 때문에 리소스는 해당 데이터를 세밀 하 게 제어 하 고 API 기능을 노출 하는 방법을 제공 합니다. 타사 앱은 사용자 및 관리자에게 이러한 사용 권한을 요청할 수 있고, 요청을 받은 사용자 또는 관리자가 요청을 승인해야만 앱이 사용자 대신 데이터에 액세스하거나 작업을 수행할 수 있습니다. 

리소스의 기능이 작은 사용 권한 집합으로 청크 되는 경우 해당 기능을 수행 하는 데 필요한 권한만 요청 하도록 타사 앱을 빌드할 수 있습니다. 사용자 및 관리자는 앱이 액세스할 수 있는 데이터를 알 수 있습니다. 그리고 앱이 악의적인 의도로 동작 하지 않을 수 있습니다. 개발자는 항상 최소 권한의 원칙을 준수 하 여 응용 프로그램이 작동 하는 데 필요한 권한만 요청 해야 합니다.

OAuth 2.0에서는 이러한 유형의 권한 집합을 *범위* 라고 합니다. 이러한 항목을 종종 *사용 권한* 이라고 합니다. Microsoft id 플랫폼에서 사용 권한은 문자열 값으로 표시 됩니다. Microsoft Graph 예제에서 각 사용 권한에 대 한 문자열 값은 다음과 같습니다.

* `Calendars.Read`를 사용하여 사용자의 일정 읽기
* `Calendars.ReadWrite`를 사용하여 사용자의 일정 쓰기
* `Mail.Send`을 사용하여 사용자로 메일 보내기

앱은 가장 일반적으로 Microsoft id 플랫폼 권한 부여 끝점에 대 한 요청에 범위를 지정 하 여 이러한 사용 권한을 요청 합니다. 그러나 일부 높은 권한 권한은 관리자의 동의를 통해서만 부여할 수 있습니다. [관리자 동의 끝점](#admin-restricted-permissions)을 사용 하 여 요청 하거나 부여할 수 있습니다. 자세히 알아보려면 계속 읽어 보세요.

## <a name="permission-types"></a>사용 권한 유형

Microsoft id 플랫폼은 *위임 된 권한* 및 *응용 프로그램 권한* 이라는 두 가지 권한 유형을 지원 합니다.

* **위임된 권한** 은 로그인한 사용자가 있는 앱에서 사용합니다. 이러한 앱에 대해 사용자 또는 관리자는 앱이 요청 하는 사용 권한을 동의 합니다. 앱은 대상 리소스를 호출할 때 로그인 한 사용자 역할을 할 수 있는 권한을 위임 합니다. 

    일부 위임 된 권한은 비관리자가 동의한 수 있습니다. 하지만 일부 권한이 높은 권한에는 [관리자 동의가](#admin-restricted-permissions)필요 합니다. 위임 된 권한에 동의할 수 있는 관리자 역할에 대 한 자세한 내용은 [Azure Active Directory (AZURE AD)의 관리자 역할 권한](../roles/permissions-reference.md)을 참조 하세요.

* **응용 프로그램 권한은** 로그인 한 사용자 (예: 백그라운드 서비스 또는 디먼로 실행 되는 앱) 없이 실행 되는 앱에서 사용 됩니다. 관리자만 응용 프로그램 사용 권한에 [동의할 수 있습니다](#requesting-consent-for-an-entire-tenant) .

_유효 사용 권한은_ 앱에서 대상 리소스에 대 한 요청을 만들 때 사용 하는 권한입니다. 위임 된 권한 및 응용 프로그램에 부여 되는 응용 프로그램 권한과 대상 리소스를 호출할 때 앱에 부여 된 유효 사용 권한 간의 차이점을 이해 하는 것이 중요 합니다.

- 위임 된 권한의 경우 앱의 _유효 사용 권한은_ 앱에 부여 된 권한 (동의 별) 및 현재 로그인 한 사용자의 권한에 대 한 최소 권한 교집합입니다. 앱은 로그인한 사용자보다 더 많은 권한을 가질 수 없습니다. 

   조직 내에서 로그인 한 사용자의 권한은 정책 또는 하나 이상의 관리자 역할의 멤버 자격에 의해 결정 될 수 있습니다. 위임된 권한에 동의할 수 있는 관리자 역할을 알아보려면 [Azure AD의 관리자 역할 권한](../roles/permissions-reference.md)을 참조하세요.

   예를 들어 앱에 _User.ReadWrite.All_ 위임된 권한이 부여되었다고 가정해 봅시다. 이 권한은 일반적으로 조직에 있는 모든 사용자의 프로필을 읽고 업데이트하는 앱 권한을 부여합니다. 로그인 한 사용자가 전역 관리자 인 경우 앱은 조직의 모든 사용자에 대 한 프로필을 업데이트할 수 있습니다. 그러나 로그인 한 사용자에 게 관리자 역할이 없으면 앱에서 로그인 한 사용자의 프로필만 업데이트할 수 있습니다. 을 대신 하 여 작업할 수 있는 권한이 있는 사용자에 게는 해당 권한이 없으므로 조직 내 다른 사용자의 프로필을 업데이트할 수 없습니다.

- 응용 프로그램 권한의 경우 앱의 _유효 사용 권한은_ 사용 권한에 의해 암시 되는 전체 권한 수준입니다. 예를 들어 _User.ReadWrite.All_ 애플리케이션 권한을 가진 앱은 조직에 있는 모든 사용자의 프로필을 업데이트할 수 있습니다.

## <a name="openid-connect-scopes"></a>OpenID Connect 범위

Openid connect Connect의 Microsoft id 플랫폼 구현에는,, 및 Microsoft Graph에도 호스트 되는 잘 정의 된 몇 가지 범위가 있습니다 `openid` `email` `profile` `offline_access` . `address`및 `phone` openid connect Connect 범위는 지원 되지 않습니다.

Openid connect Connect 범위와 토큰을 요청 하는 경우 사용자 [정보 끝점](userinfo.md)을 호출 하는 토큰을 가져옵니다.

### <a name="openid"></a>openid

앱이 [Openid connect Connect](active-directory-v2-protocols.md)를 사용 하 여 로그인 하는 경우 범위를 요청 해야 합니다 `openid` . `openid`범위는 작업 계정 동의 페이지에 **로그인** 권한으로 표시 됩니다. 개인 Microsoft 계정 동의 페이지에서 사용자의 프로필 보기로 표시 되 **고 Microsoft 계정 사용 권한을 사용 하 여 앱 및 서비스에 연결** 합니다. 

앱은이 사용 권한을 사용 하 여 클레임 형식으로 사용자에 대 한 고유 식별자를 받을 수 있습니다 `sub` . 또한이 권한은 앱이 UserInfo 끝점에 액세스할 수 있도록 합니다. `openid`범위는 Microsoft id 플랫폼 토큰 끝점에서 id 토큰을 획득 하는 데 사용할 수 있습니다. 앱은 인증에 이러한 토큰을 사용할 수 있습니다.

### <a name="email"></a>이메일

범위는 `email` `openid` 범위 및 다른 모든 범위와 함께 사용할 수 있습니다. 이는 앱이 `email` 클레임의 형식으로 사용자의 기본 전자 메일 주소에 액세스할 수 있도록 해줍니다. 

`email`클레임은 전자 메일 주소가 사용자 계정과 연결 된 경우에만 토큰에 포함 되며 항상 그렇지는 않습니다. 앱에서 범위를 사용 하는 경우 `email` 앱은 토큰에 클레임이 없는 경우를 처리할 수 있어야 합니다 `email` .

### <a name="profile"></a>profile

범위는 범위 `profile` 및 다른 범위와 함께 사용할 수 있습니다 `openid` . 사용자에 대 한 많은 양의 정보에 대 한 액세스 권한을 앱에 부여 합니다. 액세스할 수 있는 정보에는 사용자의 이름, 성, 기본 설정 된 사용자 이름 및 개체 ID가 포함 되지만이에 국한 되지 않습니다. 

`profile` `id_tokens` 특정 사용자에 대 한 매개 변수에서 사용할 수 있는 클레임의 전체 목록은 [ `id_tokens` 참조](id-tokens.md)를 참조 하세요.

### <a name="offline_access"></a>offline_access

[ `offline_access` 범위](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) 는 확장 된 시간 동안 사용자를 대신 하 여 리소스에 대 한 액세스 권한을 앱에 부여 합니다. 승인 페이지에서이 범위는 권한이 **부여 된 데이터에 대 한 액세스를 유지 관리** 로 표시 됩니다. 

사용자가 범위를 승인 하면 `offline_access` 앱은 Microsoft id 플랫폼 토큰 끝점에서 새로 고침 토큰을 받을 수 있습니다. 새로 고침 토큰은 장기적으로 존재합니다. 오래된 액세스 토큰이 만료되면 앱에서 새 액세스 토큰을 가져올 수 있습니다.

> [!NOTE]
> 이 권한은 현재 [암시적 흐름과](v2-oauth2-implicit-grant-flow.md)같은 새로 고침 토큰을 제공 하지 않는 흐름의 경우에도 모든 승인 페이지에 표시 됩니다. 이 설치 프로그램은 클라이언트가 암시적 흐름 내에서 시작 하 여 새로 고침 토큰이 필요한 코드 흐름으로 이동할 수 있는 시나리오를 해결 합니다.

Microsoft id 플랫폼 (v2.0 끝점에 대 한 요청)에서 앱은 `offline_access` 새로 고침 토큰을 받기 위해 범위를 명시적으로 요청 해야 합니다. 따라서 [OAuth 2.0 권한 부여 코드 흐름](active-directory-v2-protocols.md)에서 인증 코드를 사용 하는 경우 끝점에서 액세스 토큰만 받게 됩니다 `/token` . 

액세스 토큰은 짧은 시간 동안 유효합니다. 일반적으로 1 시간 후에 만료 됩니다. 이 시점에 앱은 사용자를 `/authorize` 엔드포인트로 다시 리디렉션하여 새 권한 부여 코드를 가져와야 합니다. 이 리디렉션 중에 앱 형식에 따라 사용자가 자격 증명을 다시 입력하거나 권한에 다시 동의해야 할 수 있습니다.

새로 고침 토큰을 가져오고 사용 하는 방법에 대 한 자세한 내용은 [Microsoft id 플랫폼 프로토콜 참조](active-directory-v2-protocols.md)를 참조 하세요.

## <a name="requesting-individual-user-consent"></a>개별 사용자의 동의 요청

[OpenID Connect 또는 OAuth 2.0](active-directory-v2-protocols.md) 권한 부여 요청에서 앱은 `scope` 쿼리 매개 변수를 사용하여 필요한 사용 권한을 요청할 수 있습니다. 예를 들어 사용자가 앱에 로그인 하면 앱이 다음 예제와 같은 요청을 보냅니다. 가독성을 위해 줄 바꿈이 추가 됩니다.

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` 매개 변수는 앱이 요청하는 공백으로 구분된 위임된 권한 범위 목록입니다. 각 사용 권한은 리소스의 식별자 (응용 프로그램 ID URI)에 권한 값을 추가 하 여 표시 됩니다. 요청 예제에서 앱에는 사용자의 일정을 읽고 사용자로 메일을 보낼 수 있는 권한이 필요합니다.

사용자가 자격 증명을 입력 한 후 Microsoft id 플랫폼은 *사용자 동의* 와 일치 하는 레코드를 확인 합니다. 사용자가 이전에 요청 된 사용 권한 중 하나에 동의한 하지 않은 경우 관리자가 전체 조직을 대신 하 여 이러한 권한을 동의한 하지 않은 경우 Microsoft id 플랫폼에서 요청 된 사용 권한을 부여 하 라는 메시지를 사용자에 게 표시 합니다.

현재 `offline_access` 응용 프로그램에 대 한 초기 동의에는 ("it에 대 한 액세스를 제공 하는 데이터 액세스 유지") 권한 및 `user.read` ("사용자 프로필에 대 한 로그인 및 읽기") 권한이 자동으로 포함 됩니다.  이러한 권한은 일반적으로 적절 한 앱 기능에 필요 합니다. `offline_access`사용 권한은 앱에서 네이티브 앱 및 웹 앱에 중요 한 토큰을 새로 고칠 수 있는 액세스 권한을 부여 합니다. `user.read`권한은 클레임에 대 한 액세스를 제공 합니다 `sub` . 클라이언트 또는 앱이 시간 경과에 따라 사용자를 올바르게 식별 하 고 기본적인 사용자 정보에 액세스할 수 있습니다.

![작업 계정 동의가 표시 되는 예제 스크린샷.](./media/v2-permissions-and-consent/work_account_consent.png)

사용자가 권한 요청을 승인 하면 동의가 기록 됩니다. 사용자는 나중에 응용 프로그램에 로그인 할 때 다시 동의할 필요가 없습니다.

## <a name="requesting-consent-for-an-entire-tenant"></a>전체 테넌트에 대한 동의 요청

조직에서 응용 프로그램에 대 한 라이선스 또는 구독을 구입 하는 경우 조직의 모든 구성원이 사용할 수 있도록 응용 프로그램을 사전에 설정 하려는 경우가 많습니다. 이러한 프로세스의 일부로, 관리자는 테넌트의 사용자를 대신하여 애플리케이션에 대한 동의를 부여할 수 있습니다. 관리자가 전체 테 넌 트에 대 한 동의를 부여 하는 경우 조직의 사용자는 응용 프로그램에 대 한 동의 페이지를 표시 하지 않습니다.

테넌트의 모든 사용자에 대한 위임된 권한을 요청하기 위해 앱에서 관리자 동의 엔드포인트를 사용할 수 있습니다.

또한 응용 프로그램은 관리 동의 끝점을 사용 하 여 응용 프로그램 권한을 요청 해야 합니다.

## <a name="admin-restricted-permissions"></a>관리 제한 권한

Microsoft 리소스의 일부 높은 권한 권한은 *관리 제한* 으로 설정할 수 있습니다. 이러한 종류의 사용 권한에 대 한 몇 가지 예는 다음과 같습니다.

* `User.Read.All`을 사용하여 모든 사용자의 전체 프로필 읽기
* `Directory.ReadWrite.All`를 사용하여 조직의 디렉터리에 데이터 쓰기
* `Groups.Read.All`을 사용하여 조직 디렉터리의 모든 그룹 읽기

소비자 사용자가 응용 프로그램에 이러한 종류의 데이터에 대 한 액세스 권한을 부여할 수도 있지만 조직 사용자는 동일한 중요 한 회사 데이터 집합에 대 한 액세스 권한을 부여할 수 없습니다. 애플리케이션이 조직 사용자에게 이러한 사용 권한 중 하나에 대한 액세스를 요청하는 경우 사용자에게는 앱의 사용 권한에 동의할 권한이 부여되지 않음을 나타내는 오류 메시지가 표시됩니다.

앱에 관리 제한 권한의 범위가 필요한 경우 조직의 관리자는 조직의 사용자를 대신 하 여 해당 범위에 동의 해야 합니다. 부여할 수 없는 권한에 대 한 동의를 요청 하는 사용자에 게 메시지를 표시 하지 않으려면 앱에서 관리자 동의 끝점을 사용할 수 있습니다. 관리자 동의 끝점은 다음 섹션에서 설명 합니다.

응용 프로그램에서 높은 권한 위임 된 권한을 요청 하 고 관리자가 관리자 동의 끝점을 통해 이러한 사용 권한을 부여 하는 경우 테 넌 트의 모든 사용자에 게 동의가 부여 됩니다.

응용 프로그램에서 응용 프로그램 권한을 요청 하 고 관리자가 관리자 동의 끝점을 통해 이러한 사용 권한을 부여 하는 경우이 부여는 특정 사용자를 대신 하 여 수행 되지 않습니다. 그 대신 클라이언트 애플리케이션에 *직접* 권한이 부여됩니다. 이러한 유형의 사용 권한은 디먼 서비스 및 백그라운드에서 실행 되는 기타 비 대화형 응용 프로그램 에서만 사용 됩니다.

## <a name="using-the-admin-consent-endpoint"></a>관리 동의 엔드포인트 사용

관리자 동의 끝점을 사용 하 여 관리자 동의를 부여 하면 작업이 완료 된 것입니다. 사용자는 추가 작업을 수행할 필요가 없습니다. 관리자 동의가 부여 되 면 사용자는 일반적인 인증 흐름을 통해 액세스 토큰을 가져올 수 있습니다. 결과 액세스 토큰에는 동의한 권한이 있습니다.

전역 관리자가 응용 프로그램을 사용 하 고 권한 부여 끝점으로 전송 되는 경우 Microsoft id 플랫폼에서 사용자의 역할을 검색 합니다. 전역 관리자가 요청 된 권한에 대해 전체 테 넌 트를 대신 하 여 동의할 지 묻는 메시지를 표시 합니다. 대신 전용 관리자 동의 끝점을 사용 하 여 전체 테 넌 트를 대신 하 여 권한을 부여 하도록 관리자에 게 사전에 요청할 수 있습니다. 이 끝점은 응용 프로그램 사용 권한을 요청 하는 데에도 필요 합니다. 권한 부여 끝점을 사용 하 여 응용 프로그램 사용 권한을 요청할 수 없습니다.

다음 단계를 따르는 경우 앱은 관리 제한 범위를 포함하여 테넌트의 모든 사용자에 대한 사용 권한을 요청할 수 있습니다. 이 작업은 높은 권한입니다. 시나리오에 필요한 경우에만 작업을 사용 합니다.

단계를 구현 하는 코드 샘플을 보려면 GitHub의 [관리 제한 범위 샘플](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) 을 참조 하세요.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>앱 등록 포털에서 사용 권한 요청

앱 등록 포털에서 응용 프로그램은 위임 된 권한 및 응용 프로그램 권한을 포함 하 여 필요한 권한을 나열할 수 있습니다. 이 설치 프로그램을 사용 하면 `/.default` 범위와 Azure Portal의 **관리자 동의 허용** 옵션을 사용할 수 있습니다.  

일반적으로 사용 권한은 지정 된 응용 프로그램에 대해 정적으로 정의 되어야 합니다. 앱이 동적으로 요청 하거나 점진적으로 요청 하는 사용 권한의 상위 집합 이어야 합니다.

> [!NOTE]
>응용 프로그램 권한은를 사용 하는 경우에만 요청할 수 있습니다 [`/.default`](#the-default-scope) . 따라서 앱에 응용 프로그램 권한이 필요한 경우 앱 등록 포털에 표시 되는지 확인 합니다.

애플리케이션에 대해 정적으로 요청된 권한 목록을 구성하려면:

1. <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure Portal-앱 등록</a> 빠른 시작 환경에서 응용 프로그램으로 이동 합니다.
1. 응용 프로그램을 선택 하거나 [앱을 만듭니다](quickstart-register-app.md) (아직 없는 경우).
1. 응용 프로그램의 **개요** 페이지에서 **관리** 아래에 있는 **API 권한**  >  **추가 권한 추가** 를 선택 합니다.
1. 사용 가능한 Api 목록에서 **Microsoft Graph** 을 선택 합니다. 그런 다음 앱에 필요한 사용 권한을 추가 합니다.
1. **권한 추가** 를 선택 합니다.

### <a name="recommended-sign-the-user-in-to-your-app"></a>사용자가 앱에 로그인하는 것이 좋습니다.

일반적으로 관리 동의 엔드포인트를 사용하는 애플리케이션을 빌드할 때 앱에는 관리자가 앱의 사용 권한을 승인할 수 있는 페이지 또는 보기가 필요합니다. 이 페이지는 다음과 같을 수 있습니다.

* 앱 등록 흐름의 일부입니다.
* 앱 설정의 일부입니다.
* 전용 "연결" 흐름입니다. 

대부분의 경우 사용자가 회사 Microsoft 계정 또는 학교 Microsoft 계정를 사용 하 여 로그인 한 후에만 앱에서이 "연결" 보기를 표시 하는 것이 좋습니다.

사용자를 앱에 로그인 하는 경우 필요한 권한을 승인 하도록 요청 하기 전에 관리자가 속해 있는 조직을 식별할 수 있습니다. 이 단계는 반드시 필요 하지는 않지만 조직 사용자에 게 보다 직관적인 환경을 만드는 데 도움이 될 수 있습니다. 

에서 사용자를 서명 하려면 [Microsoft id 플랫폼 프로토콜 자습서](active-directory-v2-protocols.md)를 따르세요.

### <a name="request-the-permissions-from-a-directory-admin"></a>디렉터리 관리에서 사용 권한 요청

조직의 관리자에 게 사용 권한을 요청할 준비가 되 면 Microsoft identity platform admin 동의 끝점으로 사용자를 리디렉션할 수 있습니다.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| 매개 변수        | 조건        | 설명                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | 필수 | 사용 권한을 요청하려는 디렉터리 테넌트입니다. GUID 또는 친숙 한 이름 형식으로 제공할 수 있습니다. 또는 예제에서 볼 수 있듯이 일반적으로 조직에서 참조할 수 있습니다. 개인 계정은 테 넌 트의 컨텍스트를 제외 하 고 관리자 동의를 제공할 수 없기 때문에 "common"은 사용 하지 마세요. 테 넌 트를 관리 하는 개인 계정과 가장 잘 호환 되도록 하려면 가능 하면 테 넌 트 ID를 사용 합니다. |
| `client_id` | 필수 | [Azure Portal – 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서 앱에 할당 한 응용 프로그램 (클라이언트) ID입니다. |
| `redirect_uri` | 필수 |리디렉션 URI는 처리할 앱에 응답을 전송하려는 위치입니다. 앱 등록 포털에 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. |
| `state` | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태를 사용하여 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩할 수 있습니다. |
|`scope`        | 필수        | 응용 프로그램에서 요청 하는 사용 권한 집합을 정의 합니다. 범위는 정적 (사용) 또는 동적이 될 수 있습니다 [`/.default`](#the-default-scope) .  이 집합에는 Openid connect Connect 범위 ( `openid` , `profile` ,)가 포함 될 수 있습니다 `email` . 응용 프로그램 권한이 필요한 경우를 사용 하 여 `/.default` 정적으로 구성 된 사용 권한 목록을 요청 해야 합니다.  |


이 시점에서 Azure AD는 테넌트 관리자에게 요청을 완료하기 위해 로그인하도록 요구합니다. 관리자는 매개 변수에서 요청한 모든 사용 권한을 승인 하 라는 메시지를 표시 `scope` 합니다.  Static () 값을 사용 하는 경우 `/.default` 이 값은 v 1.0 관리자 동의 끝점 및 앱에 필요한 사용 권한에 있는 모든 범위에 대 한 요청 동의 처럼 작동 합니다.

#### <a name="successful-response"></a>성공적인 응답

관리자가 앱에 대한 사용 권한을 승인하는 경우 성공적인 응답은 다음과 같습니다.

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 매개 변수 | 설명 |
| --- | --- |
| `tenant` | 디렉터리 테넌트는 GUID 형식으로 요청한 권한을 애플리케이션에 부여합니다. |
| `state` | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩하는 데 사용됩니다. |
| `admin_consent` | `True`로 설정합니다. |

#### <a name="error-response"></a>오류 응답

관리자가 앱에 대 한 사용 권한을 승인 하지 않는 경우 실패 한 응답은 다음과 같습니다.

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 매개 변수 | 설명 |
| --- | --- |
| `error` | 발생하는 오류 유형을 분류하는 데 사용할 수 있는 오류 코드 문자열입니다. 또한 오류에 대응 하는 데 사용할 수 있습니다. |
| `error_description` | 개발자가 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

관리 동의 엔드포인트에서 성공적인 응답을 받은 후 앱은 요청한 사용 권한을 얻게 됩니다. 이제 원하는 리소스에 대한 토큰을 요청할 수 있습니다.

## <a name="using-permissions"></a>사용 권한 사용

사용자가 앱에 대 한 사용 권한으로 동의 앱은 일부 용량의 리소스에 액세스할 수 있는 앱의 권한을 나타내는 액세스 토큰을 획득할 수 있습니다. 액세스 토큰은 단일 리소스에 대해서만 사용할 수 있습니다. 하지만 액세스 토큰 내에서 인코딩된 모든 사용 권한은 해당 리소스에 대해 앱이 부여 되었습니다. 액세스 토큰을 획득 하기 위해 앱은 다음과 같이 Microsoft id 플랫폼 토큰 끝점에 대 한 요청을 만들 수 있습니다.

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

리소스에 대한 HTTP 요청에 결과 액세스 토큰을 사용할 수 있습니다. 응용 프로그램에 특정 작업을 수행할 수 있는 적절 한 권한이 있음을 리소스에 안정적으로 나타냅니다.

OAuth 2.0 프로토콜 및 액세스 토큰을 가져오는 방법에 대 한 자세한 내용은 [Microsoft id 플랫폼 끝점 프로토콜 참조](active-directory-v2-protocols.md)를 참조 하세요.

## <a name="the-default-scope"></a>/.default 범위

범위를 사용 하 여 v2.0 `/.default` 끝점에서 Microsoft id 플랫폼 끝점으로 앱을 마이그레이션할 수 있습니다. `/.default`범위는 응용 프로그램 등록에 구성 된 사용 권한의 정적 목록을 참조 하는 모든 응용 프로그램에 대해 기본적으로 제공 됩니다. 

`scope`값은 `https://graph.microsoft.com/.default` `resource=https://graph.microsoft.com` v 1.0 끝점과 기능적으로 동일 합니다. `https://graph.microsoft.com/.default`응용 프로그램은 요청에서 범위를 지정 하 여 앱 등록 포털에서 앱에 대해 선택한 모든 Microsoft Graph 권한의 범위를 포함 하는 액세스 토큰을 요청 합니다. 범위는 리소스 URI 및를 사용 하 여 생성 됩니다 `/.default` . 따라서 리소스 URI가 인 경우 `https://contosoApp.com` 요청 된 범위는 `https://contosoApp.com/.default` 입니다.  토큰을 올바르게 요청 하기 위해 두 번째 슬래시를 포함 해야 하는 경우 [후행 슬래시에 대 한 섹션](#trailing-slash-and-default)을 참조 하세요.

`/.default`범위는 모든 OAuth 2.0 흐름에서 사용할 수 있습니다. 하지만 [흐름](v2-oauth2-on-behalf-of-flow.md) 및 [클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md)에서 필요 합니다. 또한 v2 관리자 동의 끝점을 사용 하 여 응용 프로그램 사용 권한을 요청할 때 필요 합니다.

클라이언트 `/.default` 는 단일 요청에서 정적 () 동의 및 동적 동의를 결합할 수 없습니다. 따라서 `scope=https://graph.microsoft.com/.default+mail.read` 범위 형식을 결합 하기 때문에 오류가 발생 합니다.

### <a name="default-and-consent"></a>/.default 및 동의

`/.default` 범위는 `prompt=consent`에 대한 v1.0 엔드포인트 동작도 트리거합니다. 리소스에 관계 없이 응용 프로그램이 등록 한 모든 사용 권한에 대 한 동의를 요청 합니다. 요청의 일부로 포함 된 경우 `/.default` 범위는 요청 된 리소스에 대 한 범위를 포함 하는 토큰을 반환 합니다.

### <a name="default-when-the-user-has-already-given-consent"></a>사용자가 이미 동의를 제공한 경우의 /.default

`/.default`범위는 `resource` 중심 v 1.0 끝점의 동작과 기능적으로 동일 합니다. 또한 v1.0 끝점의 동의 동작을 수행 합니다. 즉,는 `/.default` 사용자가 클라이언트와 리소스 사이에 권한이 부여 되지 않은 경우에만 동의 확인 프롬프트를 트리거합니다. 

이러한 동의가 있는 경우 반환 된 토큰에는 사용자가 해당 리소스에 대해 부여한 모든 범위가 포함 됩니다. 그러나 사용 권한이 부여 되지 않았거나 `prompt=consent` 매개 변수가 제공 된 경우 클라이언트 응용 프로그램이 등록 한 모든 범위에 대해 동의 확인 프롬프트가 표시 됩니다.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>예제 1: 사용자 또는 테 넌 트 관리자에 게 권한이 부여 됨

이 예제에서는 사용자 또는 테 넌 트 관리자에 게 `mail.read` `user.read` 클라이언트에 대 한 및 Microsoft Graph 권한이 부여 되었습니다. 

클라이언트에서 요청 하는 경우 `scope=https://graph.microsoft.com/.default` Microsoft Graph에 대 한 클라이언트 응용 프로그램의 등록 된 사용 권한의 내용에 관계 없이 동의 확인 프롬프트가 표시 되지 않습니다. 반환 된 토큰에는 및 범위가 포함 `mail.read` `user.read` 됩니다.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>예 2: 사용자에 게 클라이언트와 리소스 사이에 대 한 권한이 부여 되지 않았습니다.

이 예에서는 사용자에 게 클라이언트와 Microsoft Graph 간에 동의가 부여 되지 않았습니다. 클라이언트에서 및에 대 한 권한을 등록 했습니다 `user.read` `contacts.read` . 또한 Azure Key Vault 범위에 대해 등록 `https://vault.azure.net/user_impersonation` 됩니다. 

클라이언트에서에 대 한 토큰을 요청 하는 경우 `scope=https://graph.microsoft.com/.default` 사용자는 `user.read` 범위, 범위 및 Key Vault 범위에 대 한 동의 페이지를 표시 `contacts.read` `user_impersonation` 합니다. 반환 된 토큰에는 `user.read` 및 `contacts.read` 범위만 포함 됩니다. Microsoft Graph에 대해서만 사용할 수 있습니다.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>예 3: 사용자가 동의한 하 고 클라이언트에서 더 많은 범위를 요청 합니다.

이 예제에서는 사용자가 클라이언트에 대해를 이미 동의한 했습니다 `mail.read` . 클라이언트에서 범위에 대해를 등록 했습니다 `contacts.read` . 

클라이언트에서를 사용 하 여 토큰을 요청 하 고을 통해 동의 하는 경우 `scope=https://graph.microsoft.com/.default` `prompt=consent` 사용자는 응용 프로그램이 등록 한 사용 권한에 대 한 모든 권한 (및에만 해당)에 대 한 동의 페이지를 표시 합니다. `contacts.read`범위는 동의 페이지에 있지만는 `mail.read` 그렇지 않습니다. 반환 된 토큰은 Microsoft Graph입니다. 여기에는 및가 포함 `mail.read` `contacts.read` 됩니다.

### <a name="using-the-default-scope-with-the-client"></a>클라이언트에서 /.default 범위 사용

클라이언트에서 자체 범위를 요청할 수 있는 경우도 있습니다 `/.default` . 다음 예제에서는 이 시나리오를 보여 줍니다.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

이 코드 예제에서는 승인에 대 한 위의 설명이 적용 되 고 시나리오에 적용 되는 경우 등록 된 모든 권한에 대 한 동의 페이지를 생성 `/.default` 합니다. 그런 다음 코드는 `id_token` 액세스 토큰이 아닌를 반환 합니다.  

이 동작은 ADAL (Azure AD 인증 라이브러리)에서 MSAL (Microsoft 인증 라이브러리)로 이동 하는 일부 레거시 클라이언트를 수용 합니다. Microsoft id 플랫폼을 대상으로 하는 새 클라이언트에서는 *이 설치를 사용할 수 없습니다.*

### <a name="client-credentials-grant-flow-and-default"></a>클라이언트 자격 증명 부여 흐름 및 기본값  

의 또 다른 용도 `/.default` 는 [클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md) 부여 흐름을 사용 하 여 web API를 호출 하는 디먼 앱과 같은 비 대화형 응용 프로그램에서 응용 프로그램 사용 권한 (또는 *역할*)을 요청 하는 것입니다.

웹 API에 대 한 응용 프로그램 사용 권한 (역할)을 만들려면 [응용 프로그램에서 앱 역할 추가](howto-add-app-roles-in-azure-ad-apps.md)를 참조 하세요.

클라이언트 앱의 클라이언트 자격 증명 요청에는가 포함 *되어야* 합니다 `scope={resource}/.default` . 여기서 `{resource}` 는 앱이 호출 하는 웹 API입니다. 개별 응용 프로그램 사용 권한 (역할)을 사용 하 여 클라이언트 자격 증명 요청을 실행 하는 것은 지원 *되지 않습니다* . 해당 web API에 대해 부여 된 모든 응용 프로그램 사용 권한 (역할)은 반환 된 액세스 토큰에 포함 됩니다.

응용 프로그램에 대 한 관리자 동의 부여를 비롯 하 여 사용자가 정의 하는 응용 프로그램 사용 권한에 대 한 액세스 권한을 부여 하려면 [웹 API에 액세스 하도록 클라이언트 응용 프로그램 구성](quickstart-configure-app-access-web-apis.md)을 참조 하세요.

### <a name="trailing-slash-and-default"></a>후행 슬래시 및/sats 기본값

일부 리소스 Uri에는와 달리 후행 슬래시가 있습니다 `https://contoso.com/` `https://contoso.com` . 후행 슬래시는 토큰 유효성 검사와 관련 된 문제를 일으킬 수 있습니다. Azure Resource Manager ()에 대해 토큰이 요청 될 때 주로 문제가 발생 `https://management.azure.com/` 합니다. 이 경우 리소스 URI에서 후행 슬래시는 토큰이 요청 될 때 슬래시가 있어야 함을 의미 합니다.  따라서에 대 한 토큰을 요청 하 고를 사용 하는 경우 `https://management.azure.com/` `/.default` 를 요청 해야 합니다 `https://management.azure.com//.default` (이중 슬래시!). 일반적으로 토큰을 발급 하 고 있음을 확인 하는 경우 토큰을 허용 해야 하는 API가 토큰을 거부 하면 두 번째 슬래시를 추가 하 고 다시 시도 하는 것이 좋습니다. 

## <a name="troubleshooting-permissions-and-consent"></a>권한 및 동의 문제 해결

문제 해결 단계는 [응용 프로그램에 대 한 동의를 수행할 때 예기치 않은 오류](../manage-apps/application-sign-in-unexpected-user-consent-error.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Microsoft id 플랫폼의 ID 토큰](id-tokens.md)
* [Microsoft id 플랫폼의 액세스 토큰](access-tokens.md)

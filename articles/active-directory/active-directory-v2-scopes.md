<properties
	pageTitle="Azure AD v2.0 범위, 사용 권한 및 동의 | Microsoft Azure"
	description="범위, 사용 권한 및 동의를 포함하여 Azure AD v2.0 끝점의 권한 부여에 대한 설명입니다."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/26/2016"
	ms.author="dastrock"/>

# v2.0 끝점의 범위, 사용 권한 및 동의

Azure AD와 통합된 앱은 사용자가 앱이 데이터에 액세스하는 방법을 제어할 수 있는 특정 권한 부여 모델을 따릅니다. 이 권한 부여 모델의 v2.0 구현이 업데이트되어 앱이 Azure AD와 상호 작용하는 방법이 변경되었습니다. 이 항목에서는 범위, 사용 권한 및 동의를 포함하여 이 권한 부여 모델의 기본 개념에 대해 설명합니다.

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.

## 범위 및 사용 권한

Azure AD는 사용자 대신 타사 앱이 웹에 호스트된 리소스에 액세스할 수 있게 하는 방법인 [OAuth 2.0](active-directory-v2-protocols.md) 권한 부여 프로토콜을 구현합니다. Azure AD와 통합된 웹에 호스트된 리소스에는 리소스 식별자 또는 **앱 ID URI**가 있습니다. 예를 들어 웹에 호스트된 몇 가지 Microsoft 리소스는 다음과 같습니다.

- Office 365 통합 메일 API: `https://outlook.office.com`
- Azure AD Graph API: `https://graph.windows.net`
- Microsoft Graph: `https://graph.microsoft.com`

Azure AD와 통합된 타사 리소스의 경우도 마찬가지입니다. 이러한 리소스는 해당 리소스의 기능을 더 작은 청크로 나누는 데 사용할 수 있는 사용 권한 집합을 정의할 수도 있습니다. 예를 들어 Microsoft Graph는 몇 가지 사용 권한을 정의했습니다.

- 사용자의 일정 읽기
- 사용자의 일정에 쓰기
- 사용자로 메일 보내기
- [\+ 자세히](https://graph.microsoft.io)

이러한 사용 권한을 정의하면 리소스가 해당 데이터 및 외부 세계에 노출되는 방식을 세부적으로 제어할 수 있습니다. 타사 앱은 최종 사용자로부터 이러한 사용 권한을 요청할 수 있으며, 최종 사용자가 사용 권한을 승인해야 앱이 사용자 대신 작동할 수 있습니다. 리소스 기능을 더 작은 사용 권한 집합으로 나누면 업무를 수행하는 데 필요한 특정 권한만 요청하도록 타사 앱을 빌드할 수 있습니다. 또한 앱이 악의적인 의도로 동작하지 않는 것을 더 확신할 수 있도록 최종 사용자가 앱에서 데이터가 사용되는 방식을 정확하게 알 수 있습니다.

Azure AD 및 OAuth에서는 이러한 사용 권한을 **범위**라고 합니다. **oAuth2Permissions**라고 표시되는 경우도 있습니다. 범위는 Azure AD에서 문자열 값으로 표시됩니다. Microsoft Graph 예제를 계속하는 경우 각 사용 권한에 대한 범위 값은 다음과 같습니다.

- 사용자의 일정 읽기: `Calendar.Read`
- 사용자의 일정에 쓰기: `Mail.ReadWrite`
- 사용자로 메일 보내기: `Mail.Send`

앱은 아래에 설명된 대로 v2.0 끝점에 대한 요청에 범위를 지정하여 이러한 사용 권한을 요청할 수 있습니다.

## OpenId Connect 범위

OpenID Connect의 v2.0 구현에는 특정 리소스에 적용되지 않는 몇 가지의 잘 정의된 범위 `openid`, `email`, `profile` 및 `offline_access`가 있습니다.

#### OpenId

앱이 [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow)를 사용하여 로그인을 수행하는 경우 `openid` 범위를 요청해야 합니다. `openid` 범위는 작업 계정 동의 화면에서는 "로그인" 권한으로 표시되고 Microsoft 계정 동의 화면에서는 "Microsoft 계정을 사용하여 프로필 보기 및 앱과 서비스에 연결" 권한으로 표시됩니다. 이 사용 권한을 통해 앱이 `sub` 클레임 형식으로 사용자에 대한 고유 식별자를 받을 수 있습니다. 또한 앱이 사용자 정보 끝점에 액세스할 수 있도록 해줍니다. v2.0 토큰 끝점에서 `openid` 범위를 사용하여 id\_token을 획득할 수도 있습니다. 이 토큰을 사용하면 앱의 다양한 구성 요소 간 HTTP 호출의 보안을 유지할 수 있습니다.

#### Email

`email` 범위는 `openid` 범위 및 다른 모든 범위와 함께 포함될 수 있습니다. 이는 앱이 `email` 클레임의 형식으로 사용자의 기본 전자 메일 주소에 액세스할 수 있도록 해줍니다. 전자 메일 주소가 사용자 계정과 연결된 경우 `email` 클레임은 오직 토큰에만 포함되지만, 항상 그런 것은 아닙니다. `email` 범위를 사용하는 경우 앱에서 `email` 클레임이 토큰에 존재하지 않는 경우를 처리할 수 있도록 준비해야 합니다.

#### 프로필

`profile` 범위는 `openid` 범위 및 다른 모든 범위와 함께 포함될 수 있습니다. 이는 앱이 사용자에 대한 다양한 정보에 액세스할 수 있도록 해줍니다. 포함하지만 사용자 이름, 성, 기본 설정된 사용자 이름, 개체 ID 등에 제한되지 않습니다. 프로필 클레임의 전체 목록을 지정된 사용자에 대한 id\_token에서 사용할 수 있는 경우 [v2.0 토큰 참조](active-directory-v2-tokens.md)를 참조합니다.

#### Offline\_access

[`offline_access` 범위](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess)를 사용하면 앱이 오랜 기간 동안 사용자 대신 리소스에 액세스할 수 있습니다. 작업 계정 동의 화면에서는 이 범위가 "언제든지 데이터 액세스" 권한으로 표시됩니다. 개인 Microsoft 계정 동의 화면에서는 이 범위가 "언제든지 정보 액세스" 권한으로 표시됩니다. 사용자가 `offline_access` 범위를 승인하면 앱이 v2.0 토큰 끝점에서 새로 고침 토큰을 받을 수 있습니다. 새로 고침 토큰은 수명이 길며, 이전 암호가 만료되면 앱이 새 액세스 토큰을 획득할 수 있도록 합니다.

앱이 `offline_access` 범위를 요청하지 않으면 refresh\_token을 받을 수 없습니다. 즉, [OAuth 2.0 인증 코드 흐름](active-directory-v2-protocols.md#oauth2-authorization-code-flow)에서 authorization\_code를 교환하는 경우 `/token` 끝점에서 access\_token만 받게 됩니다. 해당 access\_token은 짧은 기간(일반적으로 1시간) 동안 유효하지만 결국 만료됩니다. 해당 시점에 앱은 사용자를 `/authorize` 끝점으로 다시 리디렉션하여 새 authorization\_code를 검색해야 합니다. 리디렉션 중에 앱 형식에 따라 사용자가 자격 증명을 다시 입력하거나 권한에 다시 동의해야 할 수도 있고 그렇지 않을 수도 있습니다.

새로 고침 토큰을 가져오고 사용하는 방법에 대한 자세한 내용은 [v2.0 프로토콜 참조](active-directory-v2-protocols.md)를 참조하세요.


## 개별 사용자의 동의 요청

[OpenID Connect 또는 OAuth 2.0](active-directory-v2-protocols.md) 권한 부여 요청에서 앱은 `scope` 쿼리 매개 변수를 사용하여 필요한 사용 권한을 요청할 수 있습니다. 예를 들어 사용자가 앱에 로그인하면 앱이 다음과 같은 요청을 보냅니다(읽기 쉽도록 줄 바꿈 포함).

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` 매개 변수는 앱이 요청하는 공백으로 구분된 범위 목록입니다. 각 개별 범위는 리소스 식별자(앱 ID URI)에 범위 값을 추가하여 표시됩니다. 위의 요청은 앱에 사용자의 일정을 읽고 사용자로 메일을 보낼 수 있는 권한이 필요함을 나타냅니다.

사용자가 자격 증명을 입력하면 v2.0 끝점이 **사용자 동의**와 일치하는 레코드를 확인합니다. 사용자가 이전에 요청된 사용 권한 중 하나에 동의하지 않은 경우 v2.0 끝점이 사용자에게 요청된 사용 권한을 부여하라는 메시지를 표시합니다.

![작업 계정 동의 스크린샷](../media/active-directory-v2-flows/work_account_consent.png)

사용자가 사용 권한을 승인하면 후속 로그인 시 다시 동의할 필요가 없도록 동의가 기록됩니다.

## 전체 테넌트에 대한 동의 요청

조직이 응용 프로그램에 대한 라이선스 또는 구독을 구입하는 경우 직원에게 완전히 프로비전하려고 합니다. 이 프로세스의 일부로 회사 관리자는 모든 직원을 대신하여 해당 응용 프로그램에 대한 동의를 부여할 수 있습니다. 전체 테넌트에 대한 동의를 부여하면 해당 조직의 직원은 해당 응용 프로그램에 대한 동의 화면을 거치지 않습니다.

테넌트에서 모든 사용자에 대한 동의를 요청하기 위해 앱은 아래에 설명한 **관리자 동의 끝점**을 사용할 수 있습니다.

## 관리 제한 범위

Microsoft 에코시스템에서 특정한 높은 수준 사용 권한을 **관리 제한**으로 표시할 수 있습니다. 이러한 범위의 예는 다음과 같습니다.

- 조직의 디렉터리 데이터 읽기: `Directory.Read`
- 조직의 디렉터리에 데이터 쓰기: `Directory.ReadWrite`
- 조직의 디렉터리에서 보안 그룹 읽기: `Groups.Read.All`

개별 사용자가 이러한 데이터에 대한 응용 프로그램 액세스 권한을 부여할 수 있는 반면 조직 사용자는 동일한 집합인 회사의 중요한 데이터에 대한 액세스 권한을 부여하지 않도록 제한됩니다. 응용 프로그램이 조직 사용자에게 이러한 사용 권한 중 하나에 대한 액세스를 요청하는 경우 사용자에게는 앱의 사용 권한에 동의할 권한이 부여되지 않음을 나타내는 오류 메시지가 표시됩니다.

또한 앱이 조직의 이러한 관리 제한 범위에 대한 액세스 권한을 필요로 하는 경우 아래에 설명한 **관리자 동의 끝점**을 사용하여 회사 관리자에게 직접 요청해야 합니다.

관리자가 관리 동의 끝점을 통해 이러한 사용 권한을 부여할 경우 위에서 설명한 대로 테넌트에서 모든 사용자에 대한 동의가 부여됩니다.

## 관리 동의 끝점 사용

앱은 다음 단계를 따라 지정된 테넌트에서 관리 제한 범위를 포함한 모든 사용자에 대한 사용 권한을 수집할 수 있습니다. 아래에 설명한 단계를 구현하는 코드 샘플을 보려면 [관리 제한 범위 샘플](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)을 참조하세요.

#### 앱 등록 포털에서 사용 권한 요청

- [apps.dev.microsoft.com](https://apps.dev.microsoft.com)에서 응용 프로그램으로 이동하거나 응용 프로그램이 없는 경우 [응용 프로그램을 만듭니다](active-directory-v2-app-registration.md).
- **Microsoft Graph 사용 권한** 섹션으로 이동하여 앱에 필요한 사용 권한을 추가합니다.
- 앱 등록을 **저장**해야 합니다.

#### 사용자가 앱에 로그인하는 것이 좋습니다.

일반적으로 관리 동의 끝점을 사용하는 응용 프로그램을 빌드할 때 앱에는 관리자가 앱의 사용 권한을 승인할 수 있는 페이지/ 보기가 포함되어야 합니다. 이 페이지는 앱의 등록 흐름, 앱의 설정 또는 전용 "연결" 흐름의 일부일 수 있습니다. 대부분의 경우에 사용자가 회사 또는 학교 Microsoft 계정으로 로그인한 후에 앱은 이 "연결" 보기만을 표시하게 됩니다.

사용자가 앱에 로그인하면 사용자에게 필요한 사용 권한을 승인하도록 요청하기 전에 관리자가 속해 있는 조직을 식별할 수 있습니다. 반드시 필요하지는 않지만 조직 사용자를 위한 직관적인 환경을 만들 수 있습니다. 사용자가 로그인하려면 [v2.0 프로토콜 자습서](active-directory-v2-protocols.md)를 수행합니다.

#### 디렉터리 관리에서 사용 권한 요청

회사의 관리에서 사용 권한을 요청할 준비가 되면 v 2.0 **관리 동의 끝점**에 사용자를 리디렉션할 수 있습니다.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissons
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 필수 | 사용 권한을 요청하려는 디렉터리 테넌트입니다. GUID 또는 친숙한 이름 형식으로 제공할 수 있습니다. |
| client\_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| redirect\_uri | 필수 | redirect\_uri는 처리할 앱에 응답을 전송하려는 위치입니다. 포털에서 등록한 redirect\_uri 중 하나와 정확히 일치해야 합니다. |
| state | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩하는 데 사용됩니다. |

이 시점에서 Azure AD는 테넌트 관리자가 요청을 완료하기 위해 로그인하도록 강제합니다. 관리자에게는 등록 포털에서 앱에 요청한 사용 권한을 모두 승인하라는 메시지가 표시됩니다.

##### 성공적인 응답
관리자가 응용 프로그램에 대한 사용 권한을 승인하는 경우 성공적인 응답은 다음과 같습니다.

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 디렉터리 테넌트는 GUID 형식으로 요청한 권한을 응용 프로그램에 부여합니다. |
| state | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩하는 데 사용됩니다. |
| admin\_consent | `True`로 설정합니다. |



##### 오류 응답
관리자가 응용 프로그램에 대한 사용 권한을 승인하지 않는 경우 실패한 응답은 다음과 같습니다.

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

관리 동의 끝점에서 성공적인 응답을 받았다면 앱은 요청한 사용 권한을 얻게 됩니다. 이제 아래에 설명한 대로 원하는 리소스에 대한 토큰을 요청하는 단계로 이동합니다.

## 사용 권한 사용

사용자가 앱에 대한 사용 권한에 동의하면 앱이 일부 용량으로 리소스에 액세스할 수 있는 앱의 권한을 나타내는 액세스 토큰을 획득할 수 있습니다. 지정된 액세스 토큰은 단일 리소스에만 사용할 수 있지만 해당 리소스에 대해 앱에 부여된 모든 사용 권한이 그 안에 인코드됩니다. 액세스 토큰을 획득하기 위해 앱이 v2.0 토큰 끝점을 요청할 수 있습니다.

```
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

그런 다음 리소스에 대한 HTTP 요청에 결과 액세스 토큰을 사용할 수 있습니다. 이는 앱에 지정된 작업을 수행할 수 있는 권한이 있음을 리소스에 안정적으로 표시합니다.

OAuth 2.0 프로토콜 및 액세스 토큰을 획득하는 방법에 대한 자세한 내용은 [v2.0 끝점 프로토콜 참조](active-directory-v2-protocols.md)를 참조하세요.

<!---HONumber=AcomDC_0928_2016-->
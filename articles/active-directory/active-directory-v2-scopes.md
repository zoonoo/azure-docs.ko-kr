<properties
	pageTitle="앱 모델 v2.0 범위, 사용 권한 및 동의 | Microsoft Azure"
	description="범위, 사용 권한 및 동의를 포함하여 Azure AD v2.0 앱 모델의 권한 부여에 대한 설명입니다."
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
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# 앱 모델 v2.0 미리 보기: 범위, 사용 권한 및 동의

Azure AD와 통합된 앱은 사용자가 앱이 데이터에 액세스하는 방법을 제어할 수 있는 특정 권한 부여 모델을 따릅니다. 앱 모델 v2.0에서는 이 권한 부여 모델의 구현이 업데이트되어 앱이 Azure AD와 상호 작용하는 방법이 변경되었습니다. 이 항목에서는 범위, 사용 권한 및 동의를 포함하여 이 권한 부여 모델의 기본 개념에 대해 설명합니다.

> [AZURE.NOTE]이 정보는 v2.0 앱 모델 공개 미리 보기에 적용됩니다. 일반 공급 Azure AD 서비스와 통합하는 방법에 대한 지침은 [Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)를 참조하세요.

## 범위 및 사용 권한

앱 모델 v2.0에서는 사용자 대신 타사 앱이 웹에 호스트된 리소스에 액세스할 수 있게 하는 방법인 [OAuth 2.0](active-directory-v2-protocols.md) 권한 부여 프로토콜을 구현합니다. Azure AD와 통합된 웹에 호스트된 리소스에는 리소스 식별자 또는 **앱 ID URI**가 있습니다. 예를 들어 웹에 호스트된 몇 가지 Microsoft 리소스는 다음과 같습니다.

- Office 365 통합 메일 API: `https://outlook.office.com`
- Azure 리소스 관리자 API: `https://management.azure.com`
- Azure AD Graph API: `https://graph.windows.net`

Azure AD와 통합된 타사 리소스의 경우도 마찬가지입니다. 이러한 리소스는 해당 리소스의 기능을 더 작은 청크로 나누는 데 사용할 수 있는 사용 권한 집합을 정의할 수도 있습니다. 한 예로 Office 365 통합 메일 API는 다음과 같은 기본 사용 권한을 정의했습니다.

- 사용자 사서함 읽기
- 사용자 사서함에 쓰기
- 사용자로 메일 보내기

이러한 사용 권한을 정의하면 리소스가 해당 데이터 및 외부 세계에 노출되는 방식을 세부적으로 제어할 수 있습니다. 타사 앱은 최종 사용자로부터 이러한 사용 권한을 요청할 수 있으며, 최종 사용자가 사용 권한을 승인해야 앱이 사용자 대신 작동할 수 있습니다. 리소스 기능을 더 작은 사용 권한 집합으로 나누면 업무를 수행하는 데 필요한 특정 권한만 요청하도록 타사 앱을 빌드할 수 있습니다. 또한 앱이 악의적인 의도로 동작하지 않는 것을 더 확신할 수 있도록 최종 사용자가 앱에서 데이터가 사용되는 방식을 정확하게 알 수 있습니다.

Azure AD 및 OAuth에서는 이러한 사용 권한을 **범위**라고 합니다. **oAuth2Permissions**라고 표시되는 경우도 있습니다. 범위는 Azure AD에서 문자열 값으로 표시됩니다. Office 365 통합 메일 API 예제에서 각 사용 권한에 대한 범위 값은 다음과 같습니다.

- 사용자 사서함 읽기: `Mail.Read`
- 사용자 사서함에 쓰기: `Mail.ReadWrite`
- 사용자로 메일 보내기: `Mail.Send`

앱은 아래에 설명된 대로 v2.0 끝점에 대한 요청에 범위를 지정하여 이러한 사용 권한을 요청할 수 있습니다.

## 동의

[OpenID Connect 또는 OAuth 2.0](active-directory-v2-protocols.md) 권한 부여 요청에서 앱은 `scope` 쿼리 매개 변수를 사용하여 필요한 사용 권한을 요청할 수 있습니다. 예를 들어 사용자가 앱에 로그인하면 앱이 다음과 같은 요청을 보냅니다(읽기 쉽도록 줄 바꿈 포함).

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Foutlook.office.com%2Fmail.read%20
https%3A%2F%2Foutlook.office.com%2Fmail.send
&state=12345
```

`scope` 매개 변수는 앱이 요청하는 공백으로 구분된 범위 목록입니다. 각 개별 범위는 리소스 식별자(앱 ID URI)에 범위 값을 추가하여 표시됩니다. 위의 요청은 앱에 사용자 사서함을 읽고 사용자로 메일을 보낼 수 있는 권한이 필요함을 나타냅니다.

사용자가 자격 증명을 입력하면 v2.0 끝점이 **사용자 동의**와 일치하는 레코드를 확인합니다. 사용자가 이전에 요청된 사용 권한 중 하나에 동의하지 않은 경우 v2.0 끝점이 사용자에게 요청된 사용 권한을 부여하라는 메시지를 표시합니다.

![작업 계정 동의 스크린샷](../media/active-directory-v2-flows/work_account_consent.png)

사용자가 사용 권한을 승인하면 후속 로그인 시 다시 동의할 필요가 없도록 동의가 기록됩니다.

## 증분 동의

앱이 초기 사용자 로그인 또는 등록 시 필요한 모든 사용 권한을 요청할 필요는 없습니다. 요청별로 범위를 지정할 수 있으므로 앱에서 "증분 동의"를 수행하고 사용자에게 동의를 요청할 최적 시간을 선택할 수 있습니다. 앱이 모든 사용 권한을 동시에 요청하지 않는 몇 가지 일반적인 이유는 다음과 같습니다.

- 많은 권한이 필요한 앱. 앱이 다양한 리소스에 액세스하며 각 리소스에서 풍부한 기능을 수행하는 경우 앱에 필요한 사용 권한 목록이 매우 길어질 수 있습니다. 경험상, 사용 권한 목록이 길면 사용자가 앱에 쉽게 등록하지 않고 사용자 채택이 감소했습니다. 이러한 사용 권한을 동시에 모두 요청하는 대신 앱이 초기 로그인 시 하위 집합을 요청한 다음 사용자가 고급 기능을 사용하려고 할 때 증분 방식으로 추가 사용 권한을 요청할 수 있습니다.
- 시간이 지남에 따라 확장되는 앱. 거의 모든 앱은 작은 기능 집합으로 시작한 후 시간이 지남에 따라 새로운 기능을 통합하기 위해 확장됩니다. 증분 동의를 사용하면 앱을 쉽게 변경하고 사용자로부터 새 사용 권한을 원활하게 요청할 수 있습니다. 권한 부여 요청에 추가 범위를 보내도록 코드를 업데이트하면 됩니다.
- 관리자 전용 사용 권한. 일부 리소스는 **오직** 조직의 관리자만 동의 또는 승인할 수 있는 사용 권한을 정의합니다. 예를 들어 Azure AD Graph API는 특히 앱이 사용자 및 그룹을 만들고, 업데이트 및 삭제할 수 있도록 하는 `Directory.Write` 권한을 정의합니다. 해당 사용 권한에 높은 권한을 가진 관리자의 승인이 필요한 이유를 짐작할 수 있을 것입니다. 증분 동의를 사용하면 관리자 승인 없이 사용자가 등록할 수 있는 기본 기능 집합을 앱에서 노출할 수 있습니다. 그러나 높은 권한이 필요한 작업을 수행하려면 하면 관리자 전용 권한을 요청하고 앱의 해당 부분에 액세스하기 전에 관리자가 등록하도록 요구할 수 있습니다.

## 사용 권한 사용

사용자가 앱에 대한 사용 권한에 동의하면 앱이 일부 용량으로 리소스에 액세스할 수 있는 앱의 권한을 나타내는 액세스 토큰을 획득할 수 있습니다. 지정된 액세스 토큰은 단일 리소스에만 사용할 수 있지만 해당 리소스에 대해 앱에 부여된 모든 사용 권한이 그 안에 인코드됩니다. 액세스 토큰을 획득하기 위해 앱이 v2.0 토큰 끝점을 요청할 수 있습니다.

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

그런 다음 리소스에 대한 HTTP 요청에 결과 액세스 토큰을 사용할 수 있습니다. 이는 앱에 지정된 작업을 수행할 수 있는 권한이 있음을 리소스에 안정적으로 표시합니다.

OAuth 2.0 프로토콜 및 액세스 토큰을 획득하는 방법에 대한 자세한 내용은 [앱 모델 v2.0 프로토콜 참조](active-directory-v2-protocols.md)를 참조하세요.

## OpenId 및 Offline\_Access

앱 모델 v2.0에는 특정 리소스에 적용되지 않는 두 개의 잘 정의된 범위 `openid` 및 `offline_access`가 있습니다.

#### OpenId

앱이 [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow)를 사용하여 로그인을 수행하는 경우 `openid` 범위를 요청해야 합니다. `openid` 범위는 작업 계정 동의 화면에서는 "로그인" 권한으로 표시되고 Microsoft 계정 동의 화면에서는 "Microsoft 계정을 사용하여 프로필 보기 및 앱과 서비스에 연결" 권한으로 표시됩니다. 이 사용 권한을 통해 앱이 OpenID Connect 사용자 정보 끝점에 액세스할 수 있으므로 사용자 승인이 필요합니다. v2.0 토큰 끝점에서 `openid` 범위를 사용하여 id\_token을 획득할 수도 있습니다. 이 토큰을 사용하면 앱의 다양한 구성 요소 간 HTTP 호출의 보안을 유지할 수 있습니다.

#### Offline\_Access

`offline_access` 범위를 사용하면 앱이 오랜 기간 동안 사용자 대신 리소스에 액세스할 수 있습니다. 작업 계정 동의 화면에서는 이 범위가 "언제든지 데이터 액세스" 권한으로 표시됩니다. 개인 Microsoft 계정 동의 화면에서는 이 범위가 "언제든지 정보 액세스" 권한으로 표시됩니다. 사용자가 `offline_access` 범위를 승인하면 앱이 v2.0 토큰 끝점에서 새로 고침 토큰을 받을 수 있습니다. 새로 고침 토큰은 수명이 길며, 이전 암호가 만료되면 앱이 새 액세스 토큰을 획득할 수 있도록 합니다.

앱이 `offline_access` 범위를 요청하지 않으면 refresh\_token을 받을 수 없습니다. 즉, [OAuth 2.0 인증 코드 흐름](active-directory-v2-protocols.md#oauth2-authorization-code-flow)에서 authorization\_code를 교환하는 경우 `/token` 끝점에서 access\_token만 받게 됩니다. 해당 access\_token은 짧은 기간(일반적으로 1시간) 동안 유효하지만 결국 만료됩니다. 해당 시점에 앱은 사용자를 `/authorize` 끝점으로 다시 리디렉션하여 새 authorization\_code를 검색해야 합니다. 앱 형식에 따라 이 리디렉션 중에 사용자가 자격 증명을 다시 입력하거나 사용 권한에 다시 동의해야 할 수도 있고 그렇지 않을 수도 있습니다.

새로 고침 토큰을 가져오고 사용하는 방법에 대한 자세한 내용은 [앱 모델 v2.0 프로토콜 참조](active-directory-v2-protocols.md)를 참조하세요.

<!---HONumber=Oct15_HO3-->
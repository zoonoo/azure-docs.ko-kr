---
title: Azure AD v2.0 엔드포인트와 v1.0 엔드포인트 비교 | Microsoft Docs
description: Azure AD v2.0 엔드포인트와 v1.0 엔드포인트의 차이점 이해하기
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2018
ms.author: celested
ms.reviewer: hirsin, andret, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, dadobali, negoe
ms.custom: aaddev
ms.openlocfilehash: 6c11062dbae18eed618fe7dde95daa55024d2ddd
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444854"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-the-v10-endpoint"></a>Azure AD v2.0 엔드포인트와 v1.0 엔드포인트 비교

새 애플리케이션을 개발하는 경우에는 Azure AD(Azure Active Directory) v1.0 엔드포인트와 v2.0 엔드포인트의 차이점을 아는 것이 중요합니다. 이 문서에서는 엔드포인트 간 주요 차이점과 v2.0 엔드포인트의 기존 제한 사항을 설명합니다.

> [!NOTE]
> v2.0 엔드포인트는 일부 Azure AD 시나리오 및 기능만 지원합니다. v2.0 엔드포인트를 사용해야 하는지 확인하려면 [v2.0 제한 사항](#limitations)을 참조하세요.

## <a name="who-can-sign-in"></a>로그인할 수 있는 사용자

![v1.0 및 v2.0 엔드포인트에 로그인할 수 있는 사용자](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* v1.0 엔드포인트의 경우 회사 및 학교 계정만 응용 프로그램(Azure AD)에 로그인할 수 있습니다.
* v2.0 엔드포인트의 경우 Azure AD의 회사 및 학교 계정과 개인 Microsoft 계정(MSA)(hotmail.com, outlook.com 및 msn.com)으로 로그인할 수 있습니다.
* v1.0과 v2.0 엔드포인트 둘 다 테넌트별 엔드포인트(`https://login.microsoftonline.com/{TenantId_or_Name}`)를 가리키도록 구성된 *[단일 테넌트](single-and-multi-tenant-apps.md)* 또는 *다중 테넌트* 응용 프로그램으로 구성된 응용 프로그램에 대해 Azure AD 디렉터리의 *[게스트 사용자](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* 가 로그인할 수 있습니다.

v2.0 엔드포인트를 사용하면 개인 Microsoft 계정과 회사 및 학교 계정에서 로그인을 허용하는 앱을 작성할 수 있습니다. 따라서 완전히 계정에 제약이 없는 앱을 작성할 수 있습니다. 예를 들어 앱이 [Microsoft Graph](https://graph.microsoft.io)를 호출하는 경우 일부 추가 기능 및 데이터를 해당 SharePoint 사이트 또는 디렉터리 데이터와 같은 회사 계정에서 사용할 수 있습니다. 단, [사용자의 이메일 읽기](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages)와 같은 많은 작업에서 동일한 코드가 개인 계정과 회사 및 학교 계정 이메일 모두에 액세스할 수 있습니다.

v2.0 엔드포인트의 경우 MSAL(Microsoft 인증 라이브러리)을 사용하여 소비자, 교육 및 엔터프라이즈 환경에 대한 액세스 권한을 얻을 수 있습니다. Azure AD v1.0 엔드포인트는 회사 및 학교 계정에서만 로그인을 허용합니다.

## <a name="incremental-and-dynamic-consent"></a>증분 및 동적 동의

Azure AD v1.0 엔드포인트를 사용하는 앱은 필수 OAuth 2.0 권한을 사전에 지정해야 합니다. 예를 들면:

![권한 등록 UI](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

응용 프로그램 등록에서 직접 설정한 권한은 **정적**입니다. Azure Portal에 정의된 앱의 정적 권한은 코드를 멋지고 간단하게 유지했지만, 개발자에게 몇 가지 가능한 문제를 줄 수 있습니다.

* 사용자가 처음 로그인할 때 앞으로 앱에서 필요로 하게 될 모든 권한을 요청해야 합니다. 이로 인해 권한 목록이 매우 길어서 최종 사용자는 처음 로그인 시 앱의 액세스를 승인하지 않을 수 있습니다.

* 앱이 액세스할 수도 있는 모든 리소스를 미리 알아야 합니다. 리소스의 임의 개수에 액세스할 수 있는 앱을 만들기 어려웠습니다.

v2.0 엔드포인트를 사용하면 Azure Portal의 앱 등록 정보에 정의된 정적 권한을 무시하고 권한을 증분식으로 요청할 수 있습니다. 이는 최소한의 권한을 미리 요청하고 고객이 추가적인 앱 기능을 사용함에 따라 시간이 지나면서 더 많이 누적한다는 의미입니다. 이렇게 하려면 애플리케이션 등록 정보에 미리 정의할 필요 없이, 액세스 토큰을 요청할 때 `scope` 매개 변수에 새 범위를 포함시켜서 언제든지 앱에 필요한 범위를 지정할 수 있습니다. 사용자가 요청에 추가된 새 범위에 아직 동의하지 않은 경우에는 새 권한에만 동의하라는 메시지가 표시됩니다. 자세한 내용은 [권한, 동의 및 범위](v2-permissions-and-consent.md)를 참조하세요.

`scope` 매개 변수를 통해 앱이 동적으로 권한을 요청할 수 있도록 허용하면 개발자가 사용자 환경을 완전히 제어할 수 있습니다. 초기 단계에 동의 환경을 배치하고 하나의 초기 권한 부여 요청으로 모든 권한을 요청할 수도 있습니다. 앱이 많은 권한을 요청하는 경우 시간의 경과에 따라 앱의 특정 기능을 사용하도록 시도하는 것처럼 점진적으로 사용자로부터 권한을 수집할 수 있습니다.

조직 대신 관리자가 동의를 수행하려면 앱에 등록된 정적 권한이 필요하므로, 전체 조직을 대신하여 관리자가 동의해야 하는 경우 앱 등록 포털에서 앱에 대한 권한을 설정해야 합니다. 이렇게 하면 조직 관리자가 애플리케이션을 설정하는 데 필요한 주기가 줄어듭니다.

## <a name="scopes-not-resources"></a>리소스가 아닌 범위

v1.0 엔드포인트를 사용하는 앱은 **리소스** 또는 토큰 수신자로 작동할 수 있습니다. 리소스는 많은 **범위** 또는 이해할 수 있는 **oAuth2Permissions**를 정의할 수 있으며, 클라이언트 앱이 범위의 특정 집합에 대한 리소스에서 토큰을 요청하도록 허용합니다. Azure AD Graph API를 리소스의 예로 생각해 볼 경우:

* 리소스 식별자 또는 `AppID URI`: `https://graph.windows.net/`
* 범위 또는 `oAuth2Permissions`: `Directory.Read`, `Directory.Write` 등

이는 v2.0 엔드포인트에서도 유효합니다. 앱은 여전히 리소스로 작동하고, 범위를 정의하고, URI로 식별될 수 있습니다. 클라이언트 앱은 여전히 해당 범위에 액세스 요청할 수 있습니다. 그러나 클라이언트가 권한을 요청하는 방식이 변경되었습니다. 

v1.0 엔드포인트의 경우, Azure AD에 대한 OAuth 2.0 인증 요청은 다음과 같습니다.

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

여기서 **resource** 매개 변수는 클라이언트 앱이 권한 부여를 요청하는 리소스를 나타냅니다. Azure AD는 Azure Portal의 정적 구성을 기준으로 앱에 필요한 권한을 계산하고, 이에 따라 토큰을 발급합니다. 

v2.0 엔드포인트를 사용하는 응용 프로그램의 경우 동일한 OAuth 2.0 인증 요청은 다음과 같습니다.

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

여기서 **scope** 매개 변수는 앱이 권한 부여를 요청하는 리소스와 권한을 나타냅니다. 원하는 리소스는 여전히 요청에 있으며, scope 매개 변수의 각 값에 포함되어 있습니다. 이러한 방식의 범위 매개 변수 사용은 v2.0 엔드포인트가 OAuth 2.0 사양과 더욱 잘 호환되도록 해주고 일반적인 업계 실무와 더욱 긴밀하게 연합될 수 있도록 합니다. 또한 앱이 [증분식 동의](#incremental-and-dynamic-consent)(미리 요청하는 것이 아니라 애플리케이션에 필요할 때만 권한을 요청)를 수행할 수 있도록 합니다.

## <a name="well-known-scopes"></a>잘 알려진 범위

### <a name="offline-access"></a>오프라인 액세스

v2.0 엔드포인트를 사용하는 앱은 잘 알려진 새 권한(`offline_access` 범위)을 앱에 사용해야 할 수 있습니다. 모든 앱이 연장된 기간 동안 사용자를 대신하여 리소스에 액세스해야 할 경우, 사용자가 앱을 자주 쓰지 않는 경우에도 이 권한을 요청해야 합니다. `offline_access` 범위는 사용자에게 동의 대화 상자에 **언제든지 데이터에 액세스**로 표시되며 사용자는 여기에 반드시 동의해야 합니다. `offline_access` 권한 요청은 웹앱이 OAuth 2.0 refresh_token을 v2.0 엔드포인트에서 받을 수 있도록 해줍니다. 새로 고침 토큰은 수명이 길며, 오랜 기간 액세스하기 위해 새로운 OAuth 2.0 액세스 토큰으로 교환할 수 있습니다.

앱이 `offline_access` 범위를 요청하지 않으면 새로 고침 토큰을 받지 못합니다. 즉, OAuth 2.0 권한 부여 코드 흐름에서 인증 코드를 사용하면 `/token` 엔드포인트에서 액세스 토큰만 수신하게 됩니다. 이 액세스 토큰은 짧은 기간(일반적으로 1시간) 동안 유효하지만 결국 만료됩니다. 해당 시점에 앱은 사용자를 `/authorize` 엔드포인트로 다시 리디렉션하여 새 인증 코드를 검색해야 합니다. 리디렉션 중에 앱 유형에 따라 사용자가 자격 증명을 다시 입력하거나 권한에 다시 동의해야 할 수도 있고 그렇지 않을 수도 있습니다.

OAuth 2.0, `refresh_tokens` 및 `access_tokens`에 대해 자세히 알아보려면 [v2.0 프로토콜 참조](active-directory-v2-protocols.md)를 참조하세요.

### <a name="openid-profile-and-email"></a>OpenID, 프로필 및 메일

지금까지 Azure AD에서 가장 기본적인 OpenID Connect 로그인 흐름은 결과 *id_token*의 사용자에 대해 많은 정보를 제공했습니다. id_token의 클레임에는 사용자의 이름, 기본 설정된 사용자 이름, 전자 메일 주소, 개체 ID 등이 있습니다.

이제 `openid` 범위를 통해 앱의 액세스가 허용되는 정보가 제한됩니다. `openid` 범위는 앱이 사용자를 로그인하고 해당 사용자의 앱 특정 식별자를 수신하는 작업만 허용합니다. 앱에 있는 사용자의 개인 데이터를 가져오려면 앱이 사용자로부터 추가 권한을 요청해야 합니다. 두 개의 새 범위(`email` 및 `profile` 범위)를 통해 추가 권한을 요청할 수 있습니다.

* `email` 범위는 사용자에게 주소 지정 가능한 메일 주소가 있다고 가정하여 앱이 id_token의 `email` 클레임을 통해 사용자의 기본 메일 주소에 액세스할 수 있도록 해줍니다. 
* `profile` 범위는 앱이 id_token의 사용자 이름, 기본 설정된 사용자 이름, 개체 ID 등 사용자에 관한 모든 기타 기본 정보에 액세스할 수 있도록 해줍니다.

이러한 범위를 사용하면 최소한의 공개로 앱을 코딩할 수 있으며, 작업 수행에 필요한 정보만 사용자에게 요청할 수 있습니다. 이러한 범위에 대한 자세한 내용은 [v2.0 범위 참조](v2-permissions-and-consent.md)를 참조하세요.

## <a name="token-claims"></a>토큰 클레임

v2.0 엔드포인트는 페이로드를 작게 유지하기 위해 기본적으로 토큰에서 더 작은 클레임 세트를 발행합니다. v2.0 토큰에서 더 이상 기본적으로 제공되지 않는 v1.0 토큰의 특정 클레임에 종속된 앱 및 서비스가 있는 경우, 해당 클레임을 포함하도록 [선택적 클레임](active-directory-optional-claims.md)을 사용하는 것이 좋습니다.

## <a name="limitations"></a>제한 사항

v2.0을 사용할 때 고려해야 할 몇 가지 제한 사항이 있습니다.

Microsoft ID 플랫폼과 통합되는 응용 프로그램을 빌드하는 경우, v2.0 엔드포인트 및 인증 프로토콜이 사용자 요구를 충족하는지 여부를 판단해야 합니다. v1.0 엔드포인트 및 플랫폼은 여전히 완벽하게 지원되며 어떤 면에서는 v2.0보다 더 풍부한 기능을 제공합니다. 그러나 v2.0은 개발자에게 [상당한 혜택을 소개](azure-ad-endpoint-comparison.md)합니다.

다음은 현 시점에서 개발자를 위한 간단한 권장 사항입니다.

* 애플리케이션에서 개인 Microsoft 계정을 지원하기를 원하거나 지원해야 하는 경우 또는 새 애플리케이션을 작성하는 경우 v2.0을 사용합니다. 그러나 그 전에 이 문서에 설명된 제한 사항을 이해해야 합니다.
* SAML을 사용하는 애플리케이션을 마이그레이션하거나 업데이트하는 경우 v2.0을 사용할 수 없습니다. 대신 [v1.0 가이드](v1-overview.md)를 참조하세요.

v2.0 엔드포인트가 좀 더 발전하면 여기에 나열된 제한 사항이 사라질 것이므로 v2.0 엔드포인트 외에는 신경 쓸 필요가 없게 될 것입니다. 그 전까지는 이 문서를 사용하여 v2.0 엔드포인트가 본인에게 적합한지 확인하세요. v2.0 엔드포인트의 현재 상태를 반영하도록 이 문서를 계속 업데이트할 예정이므로 다시 확인하여 v2.0 기능에 대한 요구 사항을 다시 평가하세요.

### <a name="restrictions-on-app-registrations"></a>앱 등록에 대한 제한 사항

v2.0 엔드포인트와 통합하려는 각 앱에 대해 [Microsoft 애플리케이션 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 앱 등록을 만들 수 있습니다. 또는 Azure Portal에서 [앱 등록(미리 보기) **환경**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)을 사용하여 앱을 등록할 수 있습니다. 기존 Microsoft 계정 앱은 미리 보기 포털과 호환되지 않지만, 모든 AAD 앱은 등록된 위치와 시기에 관계없이 호환됩니다. 

회사 및 학교 계정과 개인 계정을 지원하는 앱 등록에는 다음과 같은 주의 사항이 있습니다.

* 애플리케이션 ID당 두 개의 앱 비밀만 허용됩니다.
* 테넌트에 등록되지 않은 애플리케이션은 등록한 계정에서만 관리할 수 있습니다. 다른 개발자와 공유할 수 없습니다. 이는 앱 등록 포털에서 개인 Microsoft 계정을 사용하여 등록된 대부분의 앱에 적용됩니다. 여러 개발자와 앱 등록을 공유하려면 Azure Portal의 **앱 등록(미리 보기)** 섹션을 사용하여 테넌트에 애플리케이션을 등록하세요.
* 허용된 리디렉션 URL 형식에 대한 몇 가지 제한 사항이 있습니다. 리디렉션 URL에 대한 자세한 내용은 다음 섹션을 참조하세요.

### <a name="restrictions-on-redirect-urls"></a>리디렉션 URI에 대한 제한

v2.0에 등록된 앱은 제한된 리디렉션 URL 값 세트로 제한됩니다. 웹앱 및 서비스에 대한 리디렉션 URL은 스키마 `https`로 시작해야 하고 모든 리디렉션 URL 값은 단일 DNS 도메인을 공유해야 합니다.  등록 시스템은 기존 리디렉션 URL의 전체 DNS 이름을 추가하려는 리디렉션 URL의 DNS 이름과 비교합니다. `http://localhost`도 리디렉션 URL로 지원됩니다.  

다음 조건 중 하나라도 충족되는 경우 DNS 이름을 추가하는 요청이 실패하게 됩니다.  

* 새 리디렉션 URL의 전체 DNS 이름이 기존 리디렉션 URL의 DNS 이름과 일치하지 않는 경우
* 새 리디렉션 URL의 전체 DNS 이름이 기존 리디렉션 URL의 하위 도메인이 아닌 경우

#### <a name="example-1"></a>예 1

앱에 `https://login.contoso.com`의 리디렉션 URL이 있는 경우 다음 예제와 같이 DNS 이름이 정확히 일치하는 리디렉션 URL을 추가할 수 있습니다.

`https://login.contoso.com/new`

또는 다음 예제와 같이 login.contoso.com의 DNS 하위 도메인을 참조할 수 있습니다.

`https://new.login.contoso.com`

#### <a name="example-2"></a>예 2

`login-east.contoso.com` 및 `login-west.contoso.com`을 리디렉션 URL로 사용하는 앱이 필요하면 리디렉션 URL을 다음 순서로 추가해야 합니다.

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

뒤의 두 개는 첫 번째 리디렉션 URL contoso.com의 하위 도메인이므로 추가할 수 있습니다. 이 제한은 향후 릴리스에서 제거될 예정입니다.

또한 특정 애플리케이션의 회신 URL은 20개만 포함할 수 있습니다. 이 제한은 등록이 지원하는 모든 앱 형식(SPA, 네이티브 클라이언트, 웹앱 및 서비스)에 적용됩니다.  

v2.0에서 사용하기 위해 앱을 등록하는 방법을 알아보려면 다음 빠른 시작을 참조하세요.

* [애플리케이션 등록 포털을 사용하여 앱 등록](quickstart-v2-register-an-app.md)
* [앱 등록(미리 보기) 환경을 사용하여 앱 등록](quickstart-register-app.md)

### <a name="restrictions-on-libraries-and-sdks"></a>라이브러리 및 SDK에 대한 제한 사항

현재 v2.0 엔드포인트에 대한 라이브러리 지원은 제한적입니다. 프로덕션 애플리케이션에서 v2.0 엔드포인트를 사용하려는 경우 다음 옵션이 있습니다.

* 웹 애플리케이션을 작성하는 경우 일반적으로 사용 가능한 서버 쪽 미들웨어를 안전하게 사용하여 로그인 및 토큰 유효성 검사를 수행할 수 있습니다. 여기에는 ASP.NET용 OWIN Open ID Connect 미들웨어 및 Node.js Passport 플러그 인이 포함됩니다. Microsoft 미들웨어를 사용하는 코드 샘플은 [v2.0 시작](v2-overview.md#getting-started) 섹션을 참조하세요.
* 데스크톱 또는 모바일 애플리케이션을 작성하는 경우 미리 보기 MSAL(Microsoft 인증 라이브러리) 중 하나를 사용할 수 있습니다. 이러한 라이브러리는 프로덕션 지원 미리 보기이므로 프로덕션 애플리케이션에서 안전하게 사용할 수 있습니다. 미리 보기 조건 및 사용 가능한 라이브러리에 대한 자세한 내용은 [인증 라이브러리 참조](reference-v2-libraries.md)에서 확인할 수 있습니다.
* Microsoft 라이브러리가 적용되지 않는 플랫폼의 경우 애플리케이션 코드에서 프로토콜 메시지를 직접 전송 및 수신하여 v2.0 엔드포인트와 통합할 수 있습니다. v2.0 OpenID Connect 및 OAuth 프로토콜은 [명시적으로 문서화](active-directory-v2-protocols.md)되어 있어 이러한 통합을 수행하는 데 도움이 됩니다.
* 마지막으로, v2.0 엔드포인트와 통합하는 데 오픈 소스 Open ID Connect 및 OAuth 라이브러리를 사용할 수 있습니다. v2.0 엔드포인트는 변경되지 않고 다양한 오픈 소스 프로토콜 라이브러리와 호환되어야 합니다. 이러한 라이브러리의 사용 가능 여부는 언어 및 플랫폼마다 다릅니다. [OpenID Connect](https://openid.net/connect/) 및 [OAuth 2.0](https://oauth.net/2/) 웹 사이트는 주요 구현 목록을 유지 관리합니다. 자세한 내용은 [Active Directory v2.0 및 인증 라이브러리](reference-v2-libraries.md)와 v2.0 엔드포인트로 테스트한 오픈 소스 클라이언트 라이브러리 및 샘플 목록을 참조하세요.
* 참고로, v2.0 common 엔드포인트의 `.well-known` 엔드포인트는 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`입니다. `common`을 테넌트 ID로 바꾸어 테넌트와 관련된 데이터를 가져오세요.  

### <a name="protocol-changes"></a>프로토콜 변경 내용

v2.0 엔드포인트는 SAML 또는 WS-Federation을 지원하지 않으며 OpenID Connect 및 OAuth 2.0만 지원합니다.  v1.0 엔드포인트에서 OAuth 2.0 프로토콜의 주목할 만한 변경 내용은 다음과 같습니다. 

* `email` 클레임은 선택적 클레임이 구성되어 있는 경우 **또는** 요청에서 범위=메일로 지정된 경우 반환됩니다. 
* 이제 `resource` 매개 변수 대신 `scope` 매개 변수가 지원됩니다.  
* OAuth 2.0 사양을 보다 잘 준수하도록 많은 응답이 수정되었습니다(예: `expires_in`을 string 대신 int로 올바르게 반환).  

v2.0 엔드포인트에서 지원되는 프로토콜 기능의 범위를 더 잘 이해하려면 [OpenID Connect 및 OAuth 2.0 프로토콜 참조](active-directory-v2-protocols.md)를 참조하세요.

#### <a name="saml-restrictions"></a>SAML 제한 사항

Windows 애플리케이션에서 ADAL(Active Directory 인증 라이브러리)을 사용한 경우 SAML(Security Assertion Markup Language) 어설션 권한 부여를 사용하는 Windows 통합 인증을 활용했을 수 있습니다. 이 권한 부여를 통해 페더레이션된 Azure AD 테넌트의 사용자는 자격 증명을 입력하지 않고도 해당 온-프레미스 Active Directory 인스턴스로 자동으로 인증할 수 있습니다. SAML 어설션 권한 부여는 v2.0 엔드포인트에서 지원되지 않습니다.

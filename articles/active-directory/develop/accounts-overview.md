---
title: Android에 있는 Microsoft ID 플랫폼 계정 및 테넌트 프로필 | Azure
description: Android용 Microsoft ID 플랫폼 계정 개요
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev, devx-track-java, has-adal-ref
ms.reviewer: shoatman
ms.openlocfilehash: e70dc252ac23f1e25e53a4d4d2bac6c499894e93
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122568093"
---
# <a name="accounts--tenant-profiles-android"></a>계정 및 테넌트 프로필(Android)

이 문서에서는 Microsoft ID 플랫폼에 있는 `account` 항목에 대한 개요를 제공합니다.

MSAL(Microsoft 인증 라이브러리) API는 *사용자* 라는 용어를 *계정* 으로 대체합니다. 한 가지 이유는 사용자(사람이나 소프트웨어 에이전트)가 여러 계정을 과거에 사용한 적이 있거나 앞으로 사용할 수 있기 때문입니다. 이러한 계정은 사용자의 조직 내 있거나 사용자가 구성원인 다른 조직에 있을 수 있습니다.

Microsoft ID 플랫폼의 계정은 다음으로 구성됩니다.

- 고유 식별자입니다.  
- 계정 소유권/제어를 시연하는 데 사용되는 하나 이상의 자격 증명입니다.
- 다음과 같은 특성으로 구성된 하나 이상의 프로필입니다.
  - 사진, 지정된 이름, 성, 제목, 사무실 위치
- 계정에는 기관 또는 레코드 시스템의 원본이 있습니다. 계정이 만들어지고 해당 계정과 연결된 자격 증명이 저장된 시스템입니다. Microsoft ID 플랫폼과 같은 다중 테넌트 시스템의 레코드 시스템은 계정을 만든 `tenant`입니다. 해당 테넌트는 `home tenant`라고도 합니다.
- Microsoft ID 플랫폼의 계정에는 다음과 같은 레코드 시스템이 있습니다.
  - Azure Active Directory B2C를 포함하는 Azure Active Directory입니다.
  - Microsoft 계정(라이브)입니다.
- Microsoft ID 플랫폼 외부에 있는 레코드 시스템의 계정은 다음을 비롯한 Microsoft ID 플랫폼 내에 표시됩니다.
  - 연결된 온-프레미스 디렉터리(Windows Server Active Directory)의 ID
  - LinkedIn, GitHub 등의 외부 ID.
  이러한 경우 계정에는 원본 시스템 레코드와 Microsoft ID 플랫폼 내의 레코드 시스템이 모두 있습니다.
- Microsoft ID 플랫폼을 사용하면 한 계정을 사용하여 여러 조직(Azure Active Directory 테넌트)에 속하는 리소스에 액세스할 수 있습니다.
  - 한 레코드 시스템(AAD 테넌트 A)의 계정이 다른 레코드 시스템(AAD 테넌트 B)의 리소스에 액세스할 수 있도록 기록하려면 리소스가 정의된 테넌트에서 계정이 표시되어야 합니다. 시스템 B에서 시스템 A의 계정에 대한 로컬 레코드를 만들어 이 작업을 수행합니다.
  - 계정을 대표하는 이 로컬 레코드는 원래 계정에 바인딩됩니다.
  - MSAL은 이 로컬 레코드를 `Tenant Profile`로 노출합니다.
  - 테넌트 프로필은 직함, 사무실 위치, 연락처 정보 등 로컬 컨텍스트에 적합한 다양한 특성을 가질 수 있습니다.
- 하나 이상의 테넌트에 계정이 있을 수 있으므로 계정에는 프로필이 둘 이상 있을 수 있습니다.

> [!NOTE]
> MSAL은 Microsoft 계정 시스템(Live, MSA)을 Microsoft ID 플랫폼 내의 다른 테넌트로 처리합니다. Microsoft 계정 테넌트의 테넌트 ID는 `9188040d-6c67-4c5b-b112-36a304b66dad`입니다.

## <a name="account-overview-diagram"></a>계정 개요 다이어그램

![계정 개요 다이어그램](./media/accounts-overview/accounts-overview.svg)

위의 다이어그램:

- 이 계정 `bob@contoso.com`이 온-프레미스 Windows Server Active Directory(레코드의 원본 온-프레미스 시스템)에 만들어집니다.
- 계정 `tom@live.com`이 Microsoft 계정 테넌트에 만들어집니다.
- `bob@contoso.com`에는 다음 Azure Active Directory 테넌트에 있는 하나 이상의 리소스에 대한 액세스 권한이 있습니다.
  - contoso.com(레코드의 클라우드 시스템 - 레코드의 온-프레미스 시스템에 연결됨)
  - fabrikam.com
  - woodgrovebank.com
  - `bob@contoso.com`에 대한 테넌트 프로필은 각 테넌트에 존재합니다.
- `tom@live.com`에는 다음 Microsoft 테넌트의 리소스에 대한 액세스 권한이 있습니다.
  - contoso.com
  - fabrikam.com
  - `tom@live.com`에 대한 테넌트 프로필은 각 테넌트에 존재합니다.
- 다른 테넌트의 Tom 및 Bob에 대한 정보는 레코드 시스템의 정보와는 다를 수 있습니다. 작업 제목, 사무실 위치 등의 특성에 따라 달라질 수 있습니다. 각 조직(Azure Active Directory 테넌트) 내에 있는 그룹 및/또는 역할의 멤버일 수 있습니다. 이 정보는 bob@contoso.com 테넌트 프로필이라고 합니다.

다이어그램에서 bob@contoso.com 및 tom@live.com은 서로 다른 Azure Active Directory 테넌트의 리소스에 액세스할 수 있습니다. 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](../external-identities/add-users-administrator.md)를 참조하세요.

## <a name="accounts-and-single-sign-on-sso"></a>계정 및 SSO(Single Sign-On)

MSAL 토큰 캐시는 계정당 *1개의 새로 고침 토큰* 을 저장합니다. 이 새로 고침 토큰을 사용하여 여러 Microsoft ID 플랫폼 테넌트의 액세스 토큰을 자동으로 요청할 수 있습니다. broker가 디바이스에 설치되면 해당 계정은 broker에서 관리되며 디바이스 차원의 Single Sign-On이 가능합니다.

> [!IMPORTANT]
> B2C(Business to Consumer) 계정 및 새로 고침 토큰 동작은 Microsoft ID 플랫폼의 나머지 부분과 다릅니다. 자세한 내용은 [B2C 정책 및 계정](#b2c-policies--accounts)을 참조하세요.

## <a name="account-identifiers"></a>계정 식별자

MSAL 계정 ID는 계정 개체 ID가 아닙니다. MSAL 계정 ID는 Microsoft ID 플랫폼 내에서 고유성을 나타내려는 목적으로만 사용되며 이외의 목적으로 구문 분석 및/또는 의존되지 않습니다.

Azure ADAL(AD 인증 라이브러리)과의 호환성을 위해 ADAL에서 MSAL로 쉽게 마이그레이션할 수 있도록 MSAL은 MSAL 캐시에서 사용할 수 있는 계정에 대한 유효한 식별자를 사용하여 계정을 조회할 수 있습니다.  예를 들어 다음은 각 식별자가 유효하기 때문에 tom@live.com에 대해 항상 동일한 계정 개체를 검색합니다.

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>계정에 대한 클레임 액세스

MSAL은 액세스 토큰을 요청하는 것 외에도 항상 각 테넌트의 ID 토큰을 요청합니다. 이를 위해 항상 다음 범위를 요청합니다.

- openid
- profile

ID 토큰에는 클레임 목록이 포함되어 있습니다. `Claims`은 계정에 대한 이름/값 쌍이며, 요청을 만드는 데 사용됩니다.

앞에서 설명한 것처럼 계정이 존재하는 각 테넌트는 작업 제목, 사무실 위치 등의 특성을 포함하되 이에 국한되지 않고 계정에 대한 다른 정보를 저장할 수 있습니다.

계정이 여러 조직에서 구성원 또는 게스트가 될 수 있지만, MSAL은 서비스를 쿼리하여 해당 계정이 구성원인 테넌트 목록을 가져옵니다. 대신, MSAL은 만들어진 토큰 요청의 결과로 계정이 있는 테넌트 목록을 작성합니다.

계정 개체에 노출되는 클레임은 항상 계정에 대한 'home tenant’/{authority}의 클레임입니다. 해당 계정이 홈 테넌트에 대한 토큰을 요청하는 데 사용되지 않은 경우 MSAL은 계정 개체를 통해 클레임을 제공할 수 없습니다.  예를 들면 다음과 같습니다.

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> 계정 개체에서 사용할 수 있는 클레임 목록을 보려면 [id_token의 클레임](./id-tokens.md#claims-in-an-id-token)을 참조하세요.

> [!TIP]
> id_token에 추가 클레임을 포함하려면 [방법: Azure AD 앱에 선택적 클레임 제공](./active-directory-optional-claims.md)의 선택적 클레임 설명서를 참조하세요.

### <a name="access-tenant-profile-claims"></a>테넌트 프로필 클레임 액세스

다른 테넌트에 표시되는 계정에 대한 클레임에 액세스하려면 먼저 계정 개체를 `IMultiTenantAccount`로 캐스트해야 합니다. 모든 계정은 다중 테넌트일 수 있지만 MSAL을 통해 사용할 수 있는 테넌트 프로필 수는 현재 계정을 사용하여 토큰을 요청한 테넌트를 기반으로 합니다.  예를 들면 다음과 같습니다.

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C 정책 및 계정

계정에 대한 새로 고침 토큰은 B2C 정책 간에 공유되지 않습니다. 따라서 토큰을 사용한 Single Sign-On은 사용할 수 없습니다. 그러나 이는 Single Sign-On 자체가 불가능함을 의미하지 않습니다. Single Sign-On을 사용하도록 설정하는 데 쿠키를 사용할 수 있는 대화형 환경을 사용해야 Single Sign-On을 사용할 수 있다는 의미입니다.

즉, MSAL의 경우 다른 B2C 정책을 사용하여 토큰을 획득한다면 이러한 토큰은 각각 고유한 식별자를 가진 별도의 계정으로 취급됩니다. `acquireTokenSilent`를 사용하여 토큰을 요청하는 계정을 사용하려는 경우 토큰 요청에 사용하는 정책과 일치하는 계정 목록에서 계정을 선택해야 합니다. 예:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```
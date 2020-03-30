---
title: 안드로이드에 & 테넌트 프로필을 마이크로 소프트 아이덴티티 플랫폼 계정 | Azure
description: 안드로이드에 대한 마이크로 소프트 ID 플랫폼 계정의 개요
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
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: d0497ad68e7b29e6d8c83dd860ba8f509e229579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611872"
---
# <a name="accounts--tenant-profiles-android"></a>계정 및 테넌트 프로필(Android)

이 문서에서는 Microsoft ID `account` 플랫폼에 있는 내용에 대한 개요를 제공합니다.

MSAL(Microsoft 인증 라이브러리) API는 *용어 사용자를* *용어 계정으로*대체합니다. 한 가지 이유는 사용자(사용자 또는 소프트웨어 에이전트)가 여러 계정을 사용하거나 사용할 수 있기 때문입니다. 이러한 계정은 사용자의 자체 조직 및/또는 사용자가 구성원인 다른 조직에 있을 수 있습니다.

Microsoft ID 플랫폼의 계정은 다음으로 구성됩니다.

- 고유 식별자입니다.  
- 계정의 소유권/제어를 입증하는 데 사용되는 하나 이상의 자격 증명입니다.
- 다음과 같은 속성으로 구성된 하나 이상의 프로필:
  - 사진, 주어진 이름, 가족 이름, 제목, 사무실 위치
- 계정에는 기록 기관의 출처가 있습니다. 계정이 생성되고 해당 계정과 연결된 자격 증명이 저장되는 시스템입니다. Microsoft ID 플랫폼과 같은 다중 테넌트 시스템에서는 레코드 시스템이 계정이 만들어진 `tenant` 위치입니다. 이 테넌트를 은 `home tenant`라고도 합니다.
- Microsoft ID 플랫폼의 계정에는 다음과 같은 레코드 시스템이 있습니다.
  - Azure Active 디렉터리 B2C를 포함한 Azure Active Directory.
  - 마이크로 소프트 계정 (라이브).
- Microsoft ID 플랫폼 외부의 레코드 시스템의 계정은 다음을 포함하여 Microsoft ID 플랫폼 내에 표시됩니다.
  - 연결된 온-프레미스 디렉터리(Windows 서버 Active Directory)의 ID
  - 링크드 인, GitHub 등의 외부 ID를 사용합니다.
  이러한 경우 계정에는 Microsoft ID 플랫폼 내의 원본 레코드 시스템과 레코드 시스템이 모두 있습니다.
- Microsoft ID 플랫폼을 사용하면 하나의 계정을 사용하여 여러 조직(Azure Active Directory 테넌자)에 속한 리소스에 액세스할 수 있습니다.
  - 한 레코드 시스템(AAD 테넌트 A)의 계정이 다른 레코드 시스템(AAD 테넌트 B)의 리소스에 액세스할 수 있음을 기록하려면 리소스가 정의된 테넌트에 계정을 표시해야 합니다. 이 작업은 시스템 B의 시스템 A에서 계정의 로컬 레코드를 만들어 수행됩니다.
  - 계정의 표현인 이 로컬 레코드는 원래 계정에 바인딩됩니다.
  - MSAL은 이 로컬 레코드를 로 `Tenant Profile`노출합니다.
  - 테넌트 프로필에는 직책, 사무실 위치, 연락처 정보 등과 같이 로컬 컨텍스트에 적합한 다양한 특성이 있을 수 있습니다.
- 계정이 하나 이상의 테넌트에 있을 수 있으므로 계정에 프로필이 두 개 이상 있을 수 있습니다.

> [!NOTE]
> MSAL은 Microsoft 계정 시스템(Live, MSA)을 Microsoft ID 플랫폼 내의 다른 테넌트로 처리합니다. Microsoft 계정 테넌트의 테넌트 ID는 다음과 같다.`9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>계정 개요 다이어그램

![계정 개요 다이어그램](./media/accounts-overview/accounts-overview.svg)

위의 다이어그램:

- 계정은 `bob@contoso.com` 온-프레미스 Windows 서버 활성 디렉터리(원본 온-프레미스 레코드 시스템)에서 만들어집니다.
- 계정은 `tom@live.com` Microsoft 계정 테넌트에서 만들어집니다.
- `bob@contoso.com`다음 Azure Active Directory 테넌트에서 하나 이상의 리소스에 액세스할 수 있습니다.
  - contoso.com(기록의 클라우드 시스템 - 온-프레미스 레코드 시스템에 연결)
  - fabrikam.com
  - woodgrovebank.com
  - 이러한 각 `bob@contoso.com` 테넌트에 대한 테넌트 프로필이 있습니다.
- `tom@live.com`다음 Microsoft 테넌트는 리소스에 액세스할 수 있습니다.
  - contoso.com
  - fabrikam.com
  - 이러한 각 `tom@live.com` 테넌트에 대한 테넌트 프로필이 있습니다.
- 다른 테넌자의 Tom 및 Bob에 대한 정보는 레코드 시스템의 정보와 다를 수 있습니다. 직칭, 사무실 위치 등과 같은 속성에 따라 다를 수 있습니다. 각 조직 내의 그룹 및/또는 역할(Azure Active Directory 테넌트)의 구성원일 수 있습니다. 이 정보를 테넌트 프로필이라고 bob@contoso.com 합니다.

다이어그램에서 bob@contoso.com tom@live.com 다른 Azure Active Directory 테넌자의 리소스에 액세스할 수 있습니다. 자세한 내용은 [Azure 포털의 Azure Active Directory B2B 공동 작업 사용자 추가를](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)참조하십시오.

## <a name="accounts-and-single-sign-on-sso"></a>계정 및 단일 사인온(SSO)

MSAL 토큰 캐시는 계정당 *단일 새로 고침 토큰을* 저장합니다. 새로 고침 토큰은 여러 Microsoft ID 플랫폼 테넌에서 액세스 토큰을 자동으로 요청하는 데 사용할 수 있습니다. 브로커가 장치에 설치되면 브로커가 계정을 관리하고 장치 전체의 단일 사인온이 가능합니다.

> [!IMPORTANT]
> B2C(비즈니스 대 소비자) 계정 및 새로 고침 토큰 동작은 나머지 Microsoft ID 플랫폼과 다릅니다. 자세한 내용은 [계정에 & B2C 정책을](#b2c-policies--accounts)참조하십시오.

## <a name="account-identifiers"></a>계정 식별자

MSAL 계정 ID는 계정 개체 ID가 아닙니다. Microsoft ID 플랫폼 내에서 고유성 이외의 다른 것을 전달하기 위해 구문 분석 및/또는 의존하는 것은 아닙니다.

Azure AD 인증 라이브러리(ADAL)와의 호환성을 위해 ADAL에서 MSAL로의 마이그레이션을 용이하게 하기 위해 MSAL 캐시에서 사용할 수 있는 계정에 대한 유효한 식별자를 사용하여 계정을 조회할 수 있습니다.  예를 들어, 다음은 각 식별자가 유효하기 tom@live.com 때문에 항상 동일한 계정 개체를 검색합니다.

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>계정에 대한 소유권 주장 액세스

액세스 토큰을 요청하는 것 외에도 MSAL은 항상 각 테넌트에서 ID 토큰을 요청합니다. 항상 다음 범위를 요청하여 이 작업을 수행합니다.

- openid
- 프로필

ID 토큰에는 클레임 목록이 포함되어 있습니다. `Claims`은 계정에 대한 이름/값 쌍이며 요청을 하는 데 사용됩니다.

앞에서 설명한 것처럼 계정이 있는 각 테넌트는 직책, 사무실 위치 등과 같은 특성을 포함하되 이에 국한되지 않는 계정에 대한 다른 정보를 저장할 수 있습니다.

계정이 여러 조직의 구성원 또는 게스트일 수 있지만 MSAL은 계정이 구성원인 테넌트 목록을 가져오는 서비스를 쿼리하지 않습니다. 대신 MSAL은 토큰 요청의 결과로 계정이 있는 테넌자 목록을 작성합니다.

계정 개체에 노출된 클레임은 항상 계정에 대한 '홈 테넌트'/{기관}의 클레임입니다. 해당 계정이 홈 테넌트에 대한 토큰을 요청하는 데 사용되지 않은 경우 MSAL은 계정 개체를 통해 클레임을 제공할 수 없습니다.  예를 들어:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> 계정 개체에서 사용할 수 있는 클레임 목록을 보려면 [id_token 클레임을](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token) 참조하십시오.

> [!TIP]
> id_token 추가 클레임을 포함하려면 [Azure AD 앱에 선택적 클레임 을 제공하는 방법:](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) 옵션 클레임 설명서를 참조하십시오.

### <a name="access-tenant-profile-claims"></a>테넌트 프로필 클레임 액세스

다른 테넌에 표시되는 계정에 대한 소유권 주장에 액세스하려면 먼저 계정 `IMultiTenantAccount`개체를 에 캐스팅해야 합니다. 모든 계정은 다중 테넌트일 수 있지만 MSAL을 통해 사용할 수 있는 테넌트 프로필 수는 현재 계정 사용에서 토큰을 요청한 테넌트를 기반으로 합니다.  예를 들어:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>계정 & B2C 정책

계정에 대한 새로 고침 토큰은 B2C 정책에서 공유되지 않습니다. 따라서 토큰을 사용한 단일 사인온이 불가능합니다. 그렇다고 해서 단일 사인온이 불가능한 것은 아닙니다. 즉, 단일 사인온은 단일 사인온을 활성화하기 위해 쿠키를 사용할 수 있는 대화형 환경을 사용해야 합니다.

즉, MSAL의 경우 다른 B2C 정책을 사용하여 토큰을 획득하면 각각 고유한 식별자가 있는 별도의 계정으로 처리됩니다. 을 사용하여 `acquireTokenSilent`토큰을 요청하려면 토큰 요청과 함께 사용하는 정책과 일치하는 계정 목록에서 계정을 선택해야 합니다. 예를 들어:

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

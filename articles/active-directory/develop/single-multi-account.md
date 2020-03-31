---
title: 단일 및 다중 계정 공용 클라이언트 앱 | Azure
description: 단일 및 다중 계정 공용 클라이언트 앱의 개요입니다.
services: active-directory
documentationcenter: ''
author: shoatman
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: f2ce993b8fbf2a1b04ea4ad9d992ba278dbc964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701419"
---
# <a name="single-and-multiple-account-public-client-apps"></a>단일 및 다중 계정 공용 클라이언트 앱

이 문서에서는 단일 계정 공용 클라이언트 앱에 중점을 두고 단일 계정 및 여러 계정 공용 클라이언트 앱에 사용되는 형식을 이해하는 데 도움이 됩니다. 

Azure Active 디렉터리 인증 라이브러리(ADAL)는 서버를 모델합니다.  대신 MSAL(Microsoft 인증 라이브러리)이 클라이언트 응용 프로그램을 모델로 합니다.  대부분의 Android 앱은 공용 클라이언트로 간주됩니다. 공용 클라이언트는 비밀을 안전하게 유지할 수 없는 앱입니다.  

MSAL은 한 번에 `PublicClientApplication` 하나의 계정만 사용할 수 있는 앱의 개발 환경을 단순화하고 명확히 하기 위한 API 표면을 전문으로 합니다. `PublicClientApplication`및 `MultipleAccountPublicClientApplication`에 의해 `SingleAccountPublicClientApplication` 하위 분류됩니다.  다음 다이어그램은 이러한 클래스 간의 관계를 보여 주며 있습니다.

![SingleAccountPublicClient응용 프로그램 UML 클래스 다이어그램](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>단일 계정 공용 클라이언트 응용 프로그램

이 `SingleAccountPublicClientApplication` 클래스를 사용하면 한 번에 하나의 계정만 로그인할 수 있는 MSAL 기반 앱을 만들 수 있습니다. `SingleAccountPublicClientApplication` 와 `PublicClientApplication` 의 차이점은 다음과 같습니다.

- MSAL은 현재 로그인한 계정을 추적합니다.
  - 앱이 브로커(Azure Portal 앱 등록 중 기본값)를 사용하고 브로커가 있는 장치에 설치된 경우 MSAL은 해당 계정이 장치에서 계속 사용할 수 있는지 확인합니다.
- `signIn`을 사용하면 요청 범위와 별도로 명시적으로 계정에 로그인할 수 있습니다.
- `acquireTokenSilent`계정 매개 변수가 필요하지 않습니다.  계정을 제공하고 제공한 계정이 MSAL에서 추적한 현재 계정과 일치하지 않으면 계정이 `MsalClientException` throw됩니다.
- `acquireToken`사용자는 계정을 전환할 수 없습니다. 사용자가 다른 계정으로 전환하려고 하면 예외가 throw됩니다.
- `getCurrentAccount`다음을 제공하는 결과 개체를 반환합니다.
  - 계정이 변경되었는지 여부를 나타내는 부울입니다. 예를 들어 장치에서 제거된 결과로 계정이 변경될 수 있습니다.
  - 이전 계정입니다. 이 기능은 계정에서 계정이 제거되거나 새 계정이 로그인되어 있을 때 로컬 데이터 정리를 수행해야 하는 경우에 유용합니다.
  - 현재계정.
- `signOut`장치에서 클라이언트와 연결된 모든 토큰을 제거합니다.  

Microsoft 인증자 또는 Intune 회사 포털과 같은 Android 인증 브로커가 장치에 설치되고 앱이 `signOut` 브로커를 사용하도록 구성된 경우 장치에서 계정을 제거하지 않습니다.

## <a name="single-account-scenario"></a>단일 계정 시나리오

다음 의사 코드는 을 `SingleAccountPublicClientApplication`사용하는 것을 보여 줍니다.

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>여러 계정 공용 클라이언트 응용 프로그램

클래스는 `MultipleAccountPublicClientApplication` 여러 계정을 동시에 로그인할 수 있는 MSAL 기반 앱을 만드는 데 사용됩니다. 다음과 같이 계정을 얻고, 추가하고, 제거할 수 있습니다.

### <a name="add-an-account"></a>계정 추가

`acquireToken` 하나 이상의 호출을 사용하여 응용 프로그램에서 하나 이상의 계정을 사용합니다.  

### <a name="get-accounts"></a>계정 받기

- 특정 `getAccount` 계정을 얻으려면 전화하십시오.
- 호출을 `getAccounts`통해 현재 앱에 알려진 계정 목록을 가져옵니다.

앱은 브로커 앱에 알려진 장치에서 모든 Microsoft ID 플랫폼 계정을 등록할 수 없습니다. 앱에서 사용한 계정만 열거할 수 있습니다.  장치에서 제거된 계정은 이러한 기능에서 반환되지 않습니다.

### <a name="remove-an-account"></a>계정 제거

계정 식별자를 `removeAccount` 호출하여 계정을 삭제합니다.

앱이 브로커를 사용하도록 구성되어 있고 브로커가 장치에 설치된 경우 에 문의할 `removeAccount`때 브로커에서 계정이 제거되지 않습니다.  클라이언트와 연결된 토큰만 제거됩니다.

## <a name="multiple-account-scenario"></a>여러 계정 시나리오

다음 의사 코드는 여러 계정 앱을 만들고, 장치에 계정을 나열하고, 토큰을 획득하는 방법을 보여 주며, 토큰을 획득하는 방법을 보여 주며, 여러 계정 앱을 생성합니다.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```

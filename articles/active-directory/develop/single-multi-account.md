---
title: 단일 및 다중 계정 퍼블릭 클라이언트 앱 | Azure
description: 단일 및 다중 계정 퍼블릭 클라이언트 앱의 개요입니다.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 5bea1753c87c11094e78f95a1bbadb02fb0b95e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752983"
---
# <a name="single-and-multiple-account-public-client-apps"></a>단일 및 다중 계정 퍼블릭 클라이언트 앱

이 문서는 단일 계정 퍼블릭 클라이언트 앱에 중점을 두고 단일 및 다중 계정 퍼블릭 클라이언트 앱에서 사용되는 형식을 이해하는 데 도움이 됩니다. 

ADAL(Azure Active Directory 인증 라이브러리)은 서버를 모델링합니다.  MSAL(Microsoft 인증 라이브러리)은 클라이언트 애플리케이션을 모델링합니다.  대다수 Android 앱은 퍼블릭 클라이언트로 간주합니다. 퍼블릭 클라이언트는 비밀을 안전하게 유지할 수 없는 앱입니다.  

MSAL은 한 번에 하나의 계정만 사용할 수 있는 앱의 개발 환경을 간소화하고 명확하게 지정하기 위해 `PublicClientApplication`의 API 표면을 특수화합니다. `PublicClientApplication`은 `SingleAccountPublicClientApplication` 및 `MultipleAccountPublicClientApplication`에 의해 서브클래싱됩니다.  다음 다이어그램에서는 관련 리소스 간 관계를 보여 줍니다.

![SingleAccountPublicClientApplication UML 클래스 다이어그램](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>단일 계정 퍼블릭 클라이언트 애플리케이션

`SingleAccountPublicClientApplication` 클래스를 사용하면 한 번에 단일 계정만 로그인할 수 있는 MSAL 기반 앱을 만들 수 있습니다. `SingleAccountPublicClientApplication` 와 `PublicClientApplication` 의 차이점은 다음과 같습니다.

- MSAL은 현재 로그인한 계정을 추적합니다.
  - 앱이 broker(Azure Portal 앱 등록 중에 기본값)를 사용하고 broker가 있는 디바이스에 설치된 경우 MSAL은 디바이스에서 계정을 계속 사용할 수 있는지 확인합니다.
- `signIn`을 사용하여 요청하는 범위에서 명시적으로 및 개별적으로 로그인할 수 있습니다.
- `acquireTokenSilent`에는 계정 매개 변수가 필요하지 않습니다.  계정을 제공하는데 제공하는 계정이 MSAL에서 추적한 현재 계정과 일치하지 않으면 `MsalClientException`이 throw됩니다.
- `acquireToken`을 사용하면 사용자가 계정을 전환할 수 없습니다. 사용자가 다른 계정으로 전환하려고 하면 예외가 throw됩니다.
- `getCurrentAccount`는 다음을 제공하는 결과 개체를 반환합니다.
  - 계정이 변경되었는지 여부를 나타내는 부울입니다. 예를 들어, 디바이스에서 제거된 결과로 계정이 변경될 수 있습니다.
  - 이전 계정입니다. 이는 계정이 디바이스에서 제거되거나 새 계정이 로그인될 때 로컬 데이터 정리를 수행해야 하는 경우에 유용합니다.
  - currentAccount입니다.
- `signOut`은 디바이스에서 클라이언트와 연결된 모든 토큰을 제거합니다.  

Microsoft Authenticator 또는 Intune 회사 포털과 같은 Android 인증 broker가 디바이스에 설치되어 있고 앱이 broker를 사용하도록 구성된 경우 `signOut`은 디바이스에서 계정을 제거하지 않습니다.

## <a name="single-account-scenario"></a>단일 계정 시나리오

다음 의사 코드에서는 `SingleAccountPublicClientApplication`을 사용하는 방법을 보여 줍니다.

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

## <a name="multiple-account-public-client-application"></a>다중 계정 퍼블릭 클라이언트 애플리케이션

`MultipleAccountPublicClientApplication` 클래스는 여러 계정이 동시에 로그인할 수 있는 MSAL 기반 앱을 만드는 데 사용됩니다. 이를 통해 다음과 같이 계정을 가져오고, 추가하고, 제거할 수 있습니다.

### <a name="add-an-account"></a>계정 추가

`acquireToken`을 한 번 이상 호출하여 애플리케이션에서 하나 이상의 계정을 사용합니다.  

### <a name="get-accounts"></a>계정 가져오기

- `getAccount`를 호출하여 특정 계정을 가져옵니다.
- `getAccounts`를 호출하여 현재 앱에 알려진 계정 목록을 가져옵니다.

앱은 broker 앱에 알려진 디바이스에서 일부 Microsoft ID 플랫폼 계정을 열거할 수 없습니다. 앱에서 사용된 계정만 열거할 수 있습니다.  디바이스에서 제거된 계정은 해당 함수에서 반환되지 않습니다.

### <a name="remove-an-account"></a>계정 제거

계정 식별자로 `removeAccount`를 호출하여 계정을 제거합니다.

앱이 broker를 사용하도록 구성되고 broker가 디바이스에 설치된 경우에는 `removeAccount`를 호출할 때 계정이 broker에서 제거되지 않습니다.  클라이언트와 연결된 토큰만 제거됩니다.

## <a name="multiple-account-scenario"></a>다중 계정 시나리오

다음 의사 코드는 다중 계정 앱을 만들고, 디바이스에 계정을 나열하고, 토큰을 획득하는 방법을 보여 줍니다.

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

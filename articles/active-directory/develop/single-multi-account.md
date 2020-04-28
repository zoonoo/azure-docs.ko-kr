---
title: 단일 및 여러 계정 공용 클라이언트 앱 | Microsoft
description: 단일 및 여러 계정 공용 클라이언트 앱에 대 한 개요입니다.
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
ms.openlocfilehash: 89a383aabf3487a0938604bc28ddb06c0541d13e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80881353"
---
# <a name="single-and-multiple-account-public-client-apps"></a>단일 및 여러 계정 공용 클라이언트 앱

이 문서를 통해 단일 계정 공용 클라이언트 앱에서 사용 되는 형식을 이해 하는 데 도움을 줄 수 있으며 단일 계정 공용 클라이언트 앱에 집중 합니다. 

ADAL (Azure Active Directory 인증 라이브러리)은 서버를 모델링 합니다.  대신 MSAL (Microsoft 인증 라이브러리)은 클라이언트 응용 프로그램을 모델링 합니다.  대부분의 Android 앱은 공용 클라이언트로 간주 됩니다. 공용 클라이언트는 안전 하 게 암호를 유지할 수 없는 앱입니다.  

MSAL은의 `PublicClientApplication` API 화면을 전문적으로 하 여 한 번에 하나의 계정만 사용할 수 있도록 하는 앱에 대 한 개발 환경을 단순화 하 고 명확 하 게 합니다. `PublicClientApplication`는 및 `MultipleAccountPublicClientApplication`에 `SingleAccountPublicClientApplication` 의해 서브클래싱 됩니다.  다음 다이어그램에서는 이러한 클래스 간의 관계를 보여 줍니다.

![SingleAccountPublicClientApplication UML 클래스 다이어그램](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>단일 계정 공용 클라이언트 응용 프로그램

클래스 `SingleAccountPublicClientApplication` 를 사용 하면 한 번에 하나의 계정만 로그인 할 수 있는 msal 기반 앱을 만들 수 있습니다. `SingleAccountPublicClientApplication` 와 `PublicClientApplication` 의 차이점은 다음과 같습니다.

- MSAL은 현재 로그인 된 계정을 추적 합니다.
  - 앱이 broker를 사용 하는 경우 (Azure Portal 기본값) broker가 있는 장치에 설치 된 경우 MSAL은 해당 계정을 장치에서 계속 사용할 수 있는지 확인 합니다.
- `signIn`요청 범위에서 별도로 계정에 로그인 할 수 있습니다.
- `acquireTokenSilent`계정 매개 변수는 필요 하지 않습니다.  계정을 제공 하 고 제공 하는 계정이 MSAL에서 추적 하는 현재 계정과 일치 하지 않으면 `MsalClientException` 이 throw 됩니다.
- `acquireToken`사용자가 계정을 전환할 수 없습니다. 사용자가 다른 계정으로 전환 하려고 하면 예외가 throw 됩니다.
- `getCurrentAccount`다음을 제공 하는 결과 개체를 반환 합니다.
  - 계정이 변경 되었는지 여부를 나타내는 부울입니다. 예를 들어 장치에서 제거 된 결과로 계정이 변경 될 수 있습니다.
  - 이전 계정. 이는 계정이 장치에서 제거 되거나 새 계정이 로그인 될 때 로컬 데이터 정리를 수행 해야 하는 경우에 유용 합니다.
  - CurrentAccount입니다.
- `signOut`장치에서 클라이언트와 연결 된 모든 토큰을 제거 합니다.  

Microsoft Authenticator 또는 Intune 회사 포털와 같은 Android 인증 브로커가 장치에 설치 되어 있고 앱이 broker를 사용 하도록 구성 된 경우 장치에서 계정을 `signOut` 제거 하지 않습니다.

## <a name="single-account-scenario"></a>단일 계정 시나리오

다음 의사 코드에서는를 사용 `SingleAccountPublicClientApplication`하는 방법을 보여 줍니다.

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

`MultipleAccountPublicClientApplication` 클래스는 여러 계정을 동시에 로그인 할 수 있도록 하는 msal 기반 응용 프로그램을 만드는 데 사용 됩니다. 다음과 같이 계정을 가져오고 추가 하 고 제거할 수 있습니다.

### <a name="add-an-account"></a>계정 추가

한 번 이상 호출 `acquireToken` 하 여 응용 프로그램에서 하나 이상의 계정을 사용 합니다.  

### <a name="get-accounts"></a>계정 가져오기

- 을 `getAccount` 호출 하 여 특정 계정을 가져옵니다.
- 을 `getAccounts`호출 하 여 현재 앱에 알려진 계정 목록을 가져옵니다.

앱은 broker 앱에 알려진 장치에서 모든 Microsoft id 플랫폼 계정을 열거할 수 없습니다. 앱에서 사용 된 계정만 열거할 수 있습니다.  장치에서 제거 된 계정은 이러한 함수에서 반환 되지 않습니다.

### <a name="remove-an-account"></a>계정 제거

계정 식별자를 사용 하 `removeAccount` 여를 호출 하 여 계정을 제거 합니다.

Broker를 사용 하도록 앱을 구성 하 고 장치에 broker를 설치 하는 경우를 호출할 `removeAccount`때 broker에서 계정이 제거 되지 않습니다.  클라이언트와 연결 된 토큰만 제거 됩니다.

## <a name="multiple-account-scenario"></a>여러 계정 시나리오

다음 의사 코드는 여러 계정 앱을 만들고, 장치에서 계정을 나열 하 고, 토큰을 획득 하는 방법을 보여 줍니다.

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

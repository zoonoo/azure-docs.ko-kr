---
title: Microsoft ID 플랫폼 Android 빠른 시작 | Azure
description: Android 애플리케이션에서 Microsoft ID 플랫폼 엔드포인트를 통해 액세스 토큰을 요구하는 API를 호출하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e11e47952f70ce0cd212ca93eff1c38f2b3993a8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678045"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>빠른 시작: Android 앱에서 사용자 로그인 및 Microsoft Graph API 호출

이 빠른 시작에서는 코드 샘플을 사용하여 Android 애플리케이션에서 개인, 회사 또는 학교 계정에 로그인한 다음, 액세스 토큰을 가져오고 Microsoft Graph API를 호출하는 방법을 보여 줍니다.

![샘플 앱의 스크린샷](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **필수 구성 요소**
> * Android Studio 
> * Android 16 이상이 필요합니다.

## <a name="step-1-get-the-sample-app"></a>1단계: 샘플 앱 가져오기

[코드를 복제합니다](https://github.com/Azure-Samples/ms-identity-android-java.git).

## <a name="step-2-register-your-application"></a>2단계: 애플리케이션 등록

애플리케이션 개체를 등록하고 해당 애플리케이션 개체의 등록 정보를 샘플 프로젝트에 수동으로 추가하려면 다음 단계를 수행합니다.

1. [Azure 포털](https://aka.ms/MobileAppReg)로 이동합니다.
1. [앱 등록 블레이드](https://portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)를 열고 **+새 등록**을 클릭합니다.
1. 앱 등록의 **이름**을 입력한 다음, 리디렉션 URI를 설정하지 않고 **등록**을 클릭합니다.
1. **관리** 섹션에서 **인증** >  **+ 플랫폼 추가** > **Android**를 차례로 선택합니다. (이 화면을 표시하려면 블레이드의 위쪽 근처에서 **새 환경을 체험해 보세요.** 를 선택해야 할 수도 있습니다.)
1. 프로젝트의 **패키지 이름**(`com.azuresamples.msalandroidapp`)을 입력합니다.
1. **Android 앱 구성** 페이지의 **서명 해시** 섹션에서 **개발 서명 해시 생성**을 클릭하고, Android 앱을 개발하는 데 사용하는 플랫폼에 사용할 KeyTool 명령을 복사합니다.

   > [!Note]
   > KeyTool.exe는 JDK(Java Development Kit)의 일부로 설치됩니다. 또한 OpenSSL 도구를 설치하여 KeyTool 명령도 실행해야 합니다.  경로에 keytool 및 OpenSSL\bin 디렉터리가 필요합니다.

1. 터미널 창에서 포털에서 복사한 keytool 명령을 실행합니다.
1. 생성된 서명 해시를 포털의 **서명 해시** 아래에 입력합니다.
1. `Configure`를 클릭하고, **MSAL 구성**의 복사본을 만듭니다. 다음 단계에서 이를 복사하여 구성 파일에 붙여넣습니다. **Done**을 클릭합니다.

## <a name="step-3-add-your-app-registration"></a>3단계: 앱 등록 추가

1. Android Studio에서 샘플 프로젝트를 엽니다.
1. **app** > **res** > **raw** 내에서 **auth_config_multiple_account.json**을 엽니다.  MSAL 구성의 내용을 붙여넣습니다. 그러면 포털에서 클라이언트 ID, 테넌트 ID 및 redirect_uri가 추가됩니다. 다음과 비슷하지만 클라이언트 ID, 테넌트 ID 및 redirect_uri에 대한 값이 채워져 있습니다.

    ```json
    {
      "client_id" : "<your_client_id_here>",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "<your_redirect_uri_here>",
      "account_mode" : "MULTIPLE",
      "broker_redirect_uri_registered": true,
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
    ```

1. **app** > **res** > **raw**를 차례로 열고, **auth_config_single_account.json**을 열고, MSAL 구성의 내용을 붙여넣습니다. 위의 **auth_config_multiple_account.json** 파일과 같습니다.
1. **앱** > **매니페스트** > **AndroidManifest.xml**에서 `BrowserTabActivity` 활동을 찾습니다. 이 항목은 다음과 같이 Microsoft에서 인증을 완료한 후 애플리케이션을 콜백할 수 있게 해줍니다.

    ```xml
    ...
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
    
                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />
    
                    <!--
                        Add in your scheme/host from registered redirect URI
                        note that the leading "/" is required for android:path
                        For Example:
                        <data
                            android:host="com.azuresamples.msalandroidapp"
                            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
                            android:scheme="msauth" />
                    -->
    
                    <data
                        android:host="YOUR_PACKAGE_NAME - must be registered at https://aka.ms/MobileAppReg"
                        android:path="/YOUR_DECODED_SIGNATURE_HASH - must be registered at https://aka.ms/MobileAppReg"
                        android:scheme="msauth" />
                </intent-filter>
            </activity>
    ```
    
1. `android:host=` 값을 Azure Portal에서 등록한 패키지 이름으로 바꿉니다.  이 경우 `com.azuresamples.msalandroidapp`이 됩니다.

    > [!IMPORTANT]
    > **android:path** 값은 선행 "/" 문자가 **있어야 합니다**. 그렇지 않으면 값 아래에 빨간색 선이 표시되고 샘플 앱이 실행되지 않습니다.
     
1. `android:path=` 값을 이전에 keytool을 실행하여 가져오고 Azure Portal에 입력한 키 해시로 바꿉니다. 서명 해시는 URL로 인코딩되면 안 됩니다.

## <a name="step-4-run-the-sample-app"></a>4단계: 샘플 앱 실행

Android Studio의 **사용 가능한 디바이스** 드롭다운에서 에뮬레이터 또는 디바이스를 선택하고, 앱을 실행합니다.

샘플 앱은 **단일 계정 모드** 화면에서 시작합니다. 기본 범위인 **user.read**는 기본적으로 제공되며, Microsoft Graph API 호출 중에 사용자 고유의 프로필 데이터를 읽을 때 사용됩니다. Microsoft Graph API 호출에 대한 URL은 기본적으로 제공됩니다. 원하는 경우 이 두 가지를 모두 변경할 수 있습니다.

![단일 및 다중 계정 사용을 보여 주는 MSAL 샘플 앱](./media/quickstart-v2-android/quickstart-sample-app.png)

앱 메뉴를 사용하여 단일 계정 모드 및 다중 계정 모드 간에 변경합니다.

단일 계정 모드에서 작업 또는 홈 계정을 사용하여 로그인합니다.

1. 사용자에게 자격 증명을 입력하라는 메시지를 표시하려면 **대화형으로 그래프 데이터 가져오기**를 선택합니다. 화면의 아래쪽에 Microsoft Graph API 호출의 출력이 표시됩니다.
2. 로그인한 후에 사용자에게 자격 증명을 다시 입력하라는 메시지를 표시하지 않고 Microsoft Graph API를 호출하려면 **자동으로 그래프 데이터 가져오기**를 선택합니다. 화면의 아래쪽에 Microsoft Graph API 호출의 출력이 표시됩니다.

다중 계정 모드에서는 동일한 단계를 반복할 수 있습니다.  또한 로그인한 계정을 제거할 수 있으며, 해당 계정에 대해 캐시된 토큰도 제거할 수 있습니다.

## <a name="how-the-sample-works"></a>샘플 작동 방법

코드는 단일 및 다중 계정 MSAL 앱을 작성하는 방법을 보여 주는 조각으로 구성됩니다. 코드 파일은 다음과 같이 구성됩니다.

| 파일  | 데모  |
|---------|---------|
| MainActivity | UI를 관리합니다. |
| MSGraphRequestWrapper  | MSAL에서 제공하는 토큰을 사용하여 Microsoft Graph API를 호출합니다. |
| MultipleAccountModeFragment  | 다중 계정 애플리케이션을 초기화하고, 사용자 계정을 로드하며, Microsoft Graph API를 호출하는 토큰을 가져옵니다. |
| SingleAccountModeFragment | 단일 계정 애플리케이션을 초기화하고, 사용자 계정을 로드하며, Microsoft Graph API를 호출하는 토큰을 가져옵니다. |
| res/auth_config_multiple_account.json  | 다중 계정 구성 파일입니다. |
| res/auth_config_single_account.json  | 단일 계정 구성 파일입니다. |
| Gradle Scripts/build.grade(Module: app) | 이 파일에는 MSAL 라이브러리 종속성이 추가됩니다. |

이제 이러한 파일을 자세히 살펴보고, 각 파일에 있는 MSAL 관련 코드를 호출합니다.

### <a name="add-msal-to-the-app"></a>앱에 MSAL 추가

MSAL([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal))은 사용자를 로그인하고 Microsoft ID 플랫폼으로 보호되는 API 액세스에 사용되는 토큰을 요청할 때 사용되는 라이브러리입니다. Gradle 3.0 이상에서는 다음을 **Gradle Scripts** > **build.gradle(Module: app)** 의 **Dependencies**(종속성) 아래에 추가하면 라이브러리를 설치합니다.

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

샘플 프로젝트의 build.gradle(Module: app)에서 다음을 확인할 수 있습니다.

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.0-RC7'
    ...
}
```

여기서는 maven central에서 MSAL을 다운로드하고 빌드하도록 Gradle에 지시합니다.

### <a name="msal-imports"></a>MSAL 가져오기

MSAL 라이브러리와 관련된 가져오기는 `com.microsoft.identity.client.*`입니다.  예를 들어 퍼블릭 클라이언트 애플리케이션을 나타내는 `PublicClientApplication` 클래스의 네임스페이스인 `import com.microsoft.identity.client.PublicClientApplication;`이 표시됩니다.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

이 파일은 단일 계정 MSAL 앱을 만들고 Microsoft Graph API를 호출하는 방법을 보여 줍니다.

단일 계정 앱은 단일 사용자만 사용합니다.  예를 들어 매핑 앱에 로그인하는 하나의 계정만 있을 수 있습니다.

#### <a name="single-account-msal-initialization"></a>단일 계정 MSAL 초기화

`onCreateView()`에서 `auth_config_single_account.json` 파일에 저장된 구성 정보를 사용하여 `PublicClientApplication` 단일 계정을 만듭니다.  단일 계정 MSAL 앱에서 사용할 수 있도록 MSAL 라이브러리를 초기화하는 방법은 다음과 같습니다.

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                    * This test app assumes that the app is only going to support one account.
                    * This requires "account_mode" : "SINGLE" in the config json file.
                    **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>사용자 로그인

사용자를 로그인하는 코드는 `initializeUI()`의 `signInButton` 클릭 처리기에 있습니다.

토큰 획득을 시도하기 전에 `signIn()`을 호출합니다. `acquireToken()`이 호출된 것처럼 `signIn()`이 작동하여 사용자가 로그인할 수 있는 대화형 프롬프트가 표시됩니다.

사용자 로그인은 비동기 작업입니다. 사용자가 로그인하면 Microsoft Graph API를 호출하고 UI를 업데이트하는 콜백이 전달됩니다.

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>사용자 로그아웃

사용자를 로그아웃하는 코드는 `initializeUI()`의 `signOutButton` 클릭 처리기에 있습니다.  사용자 로그아웃은 비동기 작업입니다. 사용자가 로그아웃하면 해당 계정에 대한 토큰 캐시도 지워집니다. 사용자 계정이 로그아웃되면 UI를 업데이트하는 콜백이 만들어집니다.

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>대화형 또는 자동으로 토큰 가져오기

사용자에게 가장 적은 수의 프롬프트를 표시하려면 일반적으로 토큰을 자동으로 가져옵니다. 그런 다음, 오류가 있으면 토큰을 대화형으로 가져오려고 시도합니다. 앱에서 `signIn()`을 처음 호출하면 `acquireToken()`에 대한 호출로 효과적으로 작동하여 사용자에게 자격 증명을 입력하라는 메시지가 표시됩니다.

사용자에게 계정을 선택하거나, 자격 증명을 입력하거나, 앱에서 요청한 권한에 동의하라는 메시지가 표시될 수 있는 몇 가지 상황은 다음과 같습니다.

* 사용자가 애플리케이션에 처음 로그인하는 경우
* 사용자가 자신의 암호를 다시 설정하는 경우 해당 자격 증명을 입력해야 합니다.
* 동의가 해지된 경우
* 앱에서 동의를 명시적으로 요구하는 경우
* 애플리케이션에서 처음으로 리소스에 대한 액세스를 요청하는 경우
* MFA 또는 기타 조건부 액세스 정책이 필요한 경우

대화형으로, 즉 사용자가 참여하는 UI를 사용하여 토큰을 가져오는 코드는 `initializeUI()`의 `callGraphApiInteractiveButton` 클릭 처리기에 있습니다.

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

사용자가 이미 로그인한 경우 `acquireTokenSilentAsync()`를 통해 `initializeUI()`의 `callGraphApiSilentButton` 클릭 처리기에서 표시한 대로 앱에서 토큰을 자동으로 요청할 수 있습니다.

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>계정 로드

계정을 로드하는 코드는 `loadAccount()`에 있습니다.  사용자 계정을 로드하는 것은 비동기 작업이므로 계정 로드, 변경 또는 오류가 발생하는 경우를 처리할 콜백이 MSAL에 전달됩니다.  다음 코드는 계정이 제거되고, 사용자가 다른 계정으로 변경되는 등의 경우에 발생하는 `onAccountChanged()`도 처리합니다.

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Microsoft Graph 호출

사용자가 로그인하면 `callGraphAPI()`의 HTTP 요청을 통해 Microsoft Graph에 대한 호출이 수행됩니다. 이 함수는 `authenticationResult`에서 액세스 토큰을 가져오고, MSGraphRequestWrapper에 대한 호출을 패키지하고, 호출 결과를 표시하는 것과 같은 몇 가지 작업을 수행하여 샘플을 간소화하는 래퍼입니다.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

단일 계정을 사용하는 MSAL 앱에 대한 구성 파일입니다.

이러한 필드에 대한 설명은 [Android MSAL 구성 파일 이해](msal-configuration.md)를 참조하세요.

이 앱에서 단일 계정을 사용하도록 구성하는 `"account_mode" : "SINGLE"`이 있는지 확인합니다.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

이 파일은 다중 계정 MSAL 앱을 만들고 Microsoft Graph API를 호출하는 방법을 보여 줍니다. 

다중 계정 앱의 예로 여러 사용자 계정(예: 회사 계정 및 개인 계정)을 사용할 수 있는 메일 앱이 있습니다.

#### <a name="multiple-account-msal-initialization"></a>다중 계정 MSAL 초기화

`onCreateView()`에서 `auth_config_multiple_account.json file`에 저장된 구성 정보를 사용하여 다중 계정 앱 개체(`IMultipleAccountPublicClientApplication`)를 만듭니다.

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

만든 `MultipleAccountPublicClientApplication` 개체는 MSAL 라이브러리와 상호 작용하여 토큰을 획득하고 사용자 계정을 로드 및 제거하는 데 사용할 수 있도록 클래스 멤버 변수에 저장됩니다.

#### <a name="load-an-account"></a>계정 로드

다중 계정 앱은 일반적으로 `GetAccounts()`를 호출하여 MSAL 작업에 사용할 계정을 선택합니다. 계정을 로드하는 코드는 `loadAccount()`에 있습니다.  사용자 계정을 로드하는 것은 비동기 작업입니다. 따라서 콜백은 계정이 로드되거나 변경되거나 오류가 발생하는 상황을 처리합니다.

```java
/**
    * Load the currently signed-in account, if there's any.
    * In the shared device mode, if the user is signed out from the device, the app can also perform the clean-up work in onAccountChanged().
    */
private void loadAccount() {
    ...
    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>대화형 또는 자동으로 토큰 가져오기

사용자에게 계정을 선택하거나, 자격 증명을 입력하거나, 앱에서 요청한 권한에 동의하라는 메시지가 표시될 수 있는 몇 가지 상황은 다음과 같습니다.

* 처음으로 사용자가 애플리케이션에 로그인한 경우
* 사용자가 자신의 암호를 다시 설정하는 경우 해당 자격 증명을 입력해야 합니다. 
* 동의가 해지된 경우 
* 앱에서 동의를 명시적으로 요구하는 경우 
* 애플리케이션에서 처음으로 리소스에 대한 액세스를 요청하는 경우
* MFA 또는 기타 조건부 액세스 정책이 필요한 경우

다중 계정 앱은 일반적으로 대화형으로, 즉 사용자가 참여하는 UI를 사용하여 `acquireToken()`에 대한 호출을 통해 토큰을 획득해야 합니다.  토큰을 대화형으로 가져오는 코드는 `initializeUI()`의 `callGraphApiInteractiveButton` 클릭 처리기에 있습니다.

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

앱에서 사용자가 토큰을 요청할 때마다 로그인하도록 요구하지 않아야 합니다. 사용자가 이미 로그인한 경우 `acquireTokenSilentAsync()`를 통해 `initializeUI()`의 `callGraphApiSilentButton` 클릭 처리기에서 표시한 대로 앱에서 사용자에게 메시지를 표시하지 않고 토큰을 요청할 수 있습니다.

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
accountList.get(accountListSpinner.getSelectedItemPosition()),
AUTHORITY,
getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>계정 제거

계정을 제거하는 코드와 계정에 캐시된 모든 토큰은 `initializeUI()`의 계정 제거 단추 처리기에 있습니다. 계정을 제거하려면 먼저 `getAccounts()` 및 `acquireToken()`과 같은 MSAL 함수에서 가져오는 계정 개체가 필요합니다. 계정을 제거하는 것은 비동기 작업이므로 UI를 업데이트하는 `onRemoved` 콜백이 제공됩니다.

```java
/**
  * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
  */
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccount();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

다중 계정을 사용하는 MSAL 앱에 대한 구성 파일입니다.

이러한 필드에 대한 설명은 [Android MSAL 구성 파일 이해](msal-configuration.md)를 참조하세요.

다중 계정 앱이므로 이 구성 파일에는 [auth_config_single_account.json](#auth_config_single_accountjson) 구성 파일과 달리 `"account_mode" : "SINGLE"` 대신 `"account_mode" : "MULTIPLE"`이 있습니다.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>이 빠른 시작에 사용되는 애플리케이션을 만드는 단계 알아보기

이 빠른 시작의 전체 설명을 포함하여 애플리케이션 및 새로운 기능 빌드에 대한 완전한 단계별 가이드를 제공하는 Android 자습서를 사용해 보세요.

> [!div class="nextstepaction"]
> [Graph API Android 자습서 호출](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Android용 MSAL 라이브러리 wiki

Android용 MSAL 라이브러리에 대한 자세한 정보를 읽어보세요.

> [!div class="nextstepaction"]
> [Android용 MSAL 라이브러리 wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Microsoft ID 플랫폼을 개선할 수 있도록 도와주세요. 간단한 두 가지 설문 조사를 완료하여 의견을 알려주세요.

> [!div class="nextstepaction"]
> [Microsoft ID 플랫폼 설문 조사](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

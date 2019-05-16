---
title: Android 시작 - Microsoft ID 플랫폼 | Azure
description: Android 앱에서 액세스 토큰을 얻는 방법 및 Microsoft ID 플랫폼의 액세스 토큰이 필요한 Microsoft Graph API를 호출하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2914d6b1f4a6c94d7e3d4456c8255c1563a71b3e
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406649"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Android 앱에서 사용자 로그인 및 Microsoft Graph 호출

이 자습서에서는 Android 앱을 Microsoft ID 플랫폼에 통합하는 방법을 알아봅니다. 구체적으로 말씀드리면, 이 앱은 사용자를 로그인하고, Microsoft Graph API를 호출하기 위한 액세스 토큰을 가져오고, Microsoft Graph API를 요청합니다.  

이 가이드를 완료했으면 애플리케이션에서 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 또는 학교 계정뿐만 아니라 개인 Microsoft 계정(outlook.com, live.com 등)의 로그인을 수락하게 됩니다.

## <a name="how-this-tutorial-works"></a>이 자습서의 작동 방식

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

이 샘플의 앱에서는 사용자로 로그인하고 사용자를 대신하여 데이터를 가져옵니다.  이 데이터는 권한 부여가 필요한 보호된 API(여기서는 Microsoft Graph API)를 통해 액세스됩니다.

더 구체적으로 살펴보면 다음과 같습니다.

* 앱이 브라우저 또는 Microsoft Authenticator와 Intune 회사 포털을 통해 사용자를 로그인합니다.
* 최종 사용자는 애플리케이션에서 요청한 권한을 수락합니다. 
* 앱에서 Microsoft Graph API에 대한 액세스 토큰을 발급합니다.
* 액세스 토큰은 웹 API에 대한 HTTP 요청에 포함됩니다.
* Microsoft Graph 응답을 처리합니다.

이 샘플에서는 MSAL(Android용 Microsoft 인증 라이브러리)을 사용하여 인증을 구현합니다. MSAL은 자동으로 토큰을 갱신하고, 디바이스의 다른 앱 간에 SSO를 제공하고, 계정을 관리합니다.

## <a name="prerequisites"></a>필수 조건

* 이 안내식 설정에서는 Android Studio를 사용합니다.
* Android 16 이상이 필요합니다(19 이상 권장).

## <a name="library"></a>라이브러리

이 가이드에서는 다음 인증 라이브러리를 사용합니다.

|라이브러리|설명|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|MSAL(Microsoft 인증 라이브러리)|

## <a name="set-up-your-project"></a>프로젝트 설정

이 자습서에서는 새 프로젝트를 만듭니다. 새 프로젝트를 만드는 대신 완료된 자습서를 다운로드하려면 [코드를 다운로드](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)합니다.

### <a name="create-a-new-project"></a>새 프로젝트 만들기

1. Android Studio를 열고, **새 Android Studio 프로젝트 시작**을 선택합니다.
    - Android Studio가 이미 열려 있는 경우 **파일** > **새로 만들기** > **새 프로젝트**를 선택합니다.
2. **빈 활동**을 그대로 유지하고 **다음**을 선택합니다.
3. 애플리케이션 이름을 지정하고, `Minimum API level`을 **API 19 이상**으로 설정하고, **마침**을 누릅니다.
5. `app/build.gradle`에서 `targetedSdkVersion`을 27로 설정합니다. 

## <a name="register-your-application"></a>애플리케이션 등록

다음 두 섹션에 설명된 대로 두 가지 방법 중 하나로 애플리케이션을 등록할 수 있습니다.

### <a name="register-your-app"></a>앱 등록

1. [Azure Portal](https://aka.ms/MobileAppReg)로 이동하여 `New registration`을 선택합니다. 
2. 앱 **이름**을 입력하고 `Register`로 이동합니다. **이 단계에서는 리디렉션 URI를 설정하지 마세요**. 
3. `Manage` 섹션에서 `Authentication` > `Add a platform` > `Android`로 이동합니다.
    - 프로젝트의 패키지 이름을 입력합니다. 코드를 다운로드한 경우 이 값은 `com.azuresamples.msalandroidapp`입니다. 
    - 디버그/개발 서명 해시를 입력합니다. 포털에서 KeyTool 명령을 사용하여 서명 해시를 생성합니다. 
4. 나중에 사용할 수 있도록 `Configure`를 누르고 ***MSAL 구성***을 저장합니다. 

## <a name="build-your-app"></a>앱 빌드

### <a name="configure-your-android-app"></a>Android 앱 구성

1. **리소스** > **새로 만들기** > **폴더** > **원시 리소스 폴더**를 마우스 오른쪽 단추로 클릭합니다.
2. **앱** > **리소스** > **원시**에서 `auth_config.json`이라는 새 JSON 파일을 만들고 ***MSAL구성***을 붙여넣습니다. 자세한 내용은 [MSAL 구성](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app)을 참조하세요.
   <!-- Workaround for Docs conversion bug -->
3. **앱** > **매니페스트** > **AndroidManifest.xml**에서, 아래의 `BrowserTabActivity` 작업을 추가합니다. 이 항목은 다음과 같이 Microsoft에서 인증을 완료한 후 애플리케이션을 콜백할 수 있게 해줍니다.

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    사용된 서명 해시는 **AndroidManifest.xml**에서 URL로 인코딩되면 안 됩니다. 

4. **AndroidManifest.xml** 내부에서 `<application>` 태그 바로 위에 다음 권한을 추가합니다.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. `BrowserTabActivity`에서 ***패키지 이름*** 및 ***서명 해시***를 Azure Portal에 등록된 값으로 바꿉니다.

### <a name="create-the-apps-ui"></a>앱의 UI 만들기

1. **res** > **레이아웃**으로 이동한 후 **activity_main.xml**을 엽니다.
2. `android.support.constraint.ConstraintLayout` 등에서 `LinearLayout`으로 작업 레이아웃을 변경합니다.
3. `android:orientation="vertical"` 속성을 `LinearLayout` 노드에 추가합니다.
4. 다음 코드를 `LinearLayout` 노드에 붙여넣어 현재 내용을 바꿉니다.

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>프로젝트에 MSAL 추가

1. Android Studio에서 **Gradle Scripts** > **build.gradle(Module: app)** 을 선택합니다.
2. **종속성** 아래에 다음 코드를 붙여넣습니다.

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>MSAL 사용 

다음 몇 개 섹션에서는 `MainAcitivty.java` 내부에서 변경 작업을 수행할 것입니다. 앱에서 MSAL을 추가하고 사용하는 데 필요한 각 단계를 살펴보겠습니다.

#### <a name="required-imports"></a>꼭 가져와야 하는 항목

다음 항목을 프로젝트로 가져옵니다. 

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiating-msal"></a>MSAL 인스턴스화 

`MainActivity` 클래스 내부에서 액세스할 범위 및 웹 API를 포함하여 앱이 할 일에 대한 몇 가지 구성과 함께 MSAL을 인스턴스화해야 합니다. 

다음 변수를 `MainActivity` 내부에 복사합니다.

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

이제 MSAL을 인스턴스화하기 위해 다음 코드를 `onCreate(...)` 메서드 내부에 복사합니다.

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

위의 코드 블록은 사용자가 `getAccounts(...)`를 통해 애플리케이션을 열면 자동으로 사용자를 로그인하려고 시도하고, 성공하면 `acquireTokenSilentAsync(...)`를 시도합니다.  다음 몇 개 섹션에서는 로그인된 계정이 없는 경우를 대비한 콜백 처리기를 구현하겠습니다. 

#### <a name="use-msal-to-get-tokens"></a>MSAL을 사용하여 토큰 가져오기

이제 MSAL을 통해 대화형으로 앱의 UI 처리 논리를 구현하고 토큰을 가져올 수 있습니다. 

MSAL은 토큰을 가져오기 위한 두 가지 기본 메서드 `acquireTokenSilentAsync` 및 `acquireToken`을 공개합니다.  

`acquireTokenSilentAsync`는 계정이 있는 경우 사용자 개입 없이 사용자를 로그인하고 토큰을 가져옵니다. 이것이 성공하면 MSAL이 앱에 토큰을 전달하고, 실패하면 `MsalUiRequiredException`을 생성합니다.  이 예외가 생성되거나 사용자에게 대화형 로그인 환경을 제공하려는 경우(자격 증명, mfa 또는 기타 조건부 액세스 정책이 필요할 수도 있고 필요하지 않을 수도 있음) `acquireToken`을 사용하면 됩니다.  

`acquireToken`은 사용자를 로그인하고 토큰을 가져오려고 시도할 때 항상 UI를 표시합니다. 하지만 브라우저의 세션 쿠키 또는 Microsoft Authenticator의 계정을 사용하여 대화형 SSO 환경을 제공할 수도 있습니다. 

시작하려면 먼저 `MainActivity` 클래스 내부에 다음 세 가지 UI 메서드를 만듭니다.

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

다음으로, 현재 작업을 가져와서 자동 및 대화형 콜백을 처리하는 다음과 같은 메서드를 추가합니다.

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>로그아웃에 MSAL 사용

다음으로, 앱에 로그아웃 지원을 추가하겠습니다. 

MSAL을 사용하여 로그아웃하면 사용자에 대해 알려진 모든 정보가 이 애플리케이션에서 제거되지만, 사용자의 디바이스에서는 여전히 활성 세션이 유지됩니다. 사용자가 다시 로그인하려고 시도할 때 자격 증명을 입력하라는 상호 작용이 표시될 수도 있지만, 디바이스 세션이 활성 상태이므로 다시 입력할 필요가 없습니다. 

로그아웃을 추가하려면 모든 계정을 순환하면서 제거하는 다음 메서드를 앱에 복사합니다.

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API 호출

토큰을 성공적으로 얻은 후에는 Microsoft Graph API를 요청할 수 있습니다. 액세스 토큰은 인증 콜백의 `onSuccess(...)` 메서드 내부에 있는 `AuthenticationResult` 내부에 있습니다. 권한 있는 요청을 생성하려면 다음과 같이 앱이 HTTP 헤더에 액세스 토큰을 추가해야 합니다.

| 헤더 키    | 값                 |
| ------------- | --------------------- |
| 권한 부여 | 전달자 <access-token> |

코드에서 이렇게 하려면 다음 두 메서드를 앱에 추가하여 그래프를 호출하고 UI를 업데이트합니다. 

```java
    /* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

[Microsoft Graph API](https://graph.microsoft.com)에 대해 자세히 알아보세요.

#### <a name="multi-account-applications"></a>다중 계정 애플리케이션

이 앱은 단일 계정 시나리오를 위해 빌드됩니다. MSAL은 다중 계정 시나리오도 지원하지만, 그러려면 앱에서 몇 가지 추가 작업을 수행해야 합니다. 사용자가 토큰이 필요한 각 작업에 어떤 계정을 사용할 것인지 선택할 수 있는 UI를 만들어야 합니다. 또는 앱에서 `getAccounts(...)` 메서드를 통해 어떤 계정을 사용할 것인지 선택하는 경험적 접근을 구현할 수 있습니다. 

## <a name="test-your-app"></a>앱 테스트

### <a name="run-locally"></a>로컬 실행

위의 코드를 따라 했으면 앱을 빌드하고 배포하여 디바이스 또는 에뮬레이터를 테스트합니다. 로그인하여 Azure AD 계정 또는 Microsoft 개인 계정의 토큰을 가져올 수 있어야 합니다. 사용자가 로그인하면 이 앱은 Microsoft Graph `/me` 엔드포인트에서 반환된 데이터를 표시합니다. 

이슈가 있는 경우 이 문서나 MSAL 라이브러리에서 이슈를 제기하여 알려주세요. 

### <a name="consent-to-your-app"></a>앱 사용 권한 동의

사용자가 앱에 처음으로 로그인하면 Microsoft ID는 사용자에게 요청된 권한에 동의할 것을 요구합니다.  대부분의 사용자가 동의할 수 있지만, 일부 Azure AD 테넌트에서는 사용자 동의를 해제했습니다. 이 경우 관리자가 사용자 대신 동의해야 합니다.  이 시나리오를 지원하려면 Azure Portal에서 앱의 범위를 등록해야 합니다.

## <a name="help-and-support"></a>도움말 및 지원

이 자습서 또는 Microsoft ID 플랫폼과 관련하여 문제가 있나요? [도움말 및 지원](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)을 참조하세요.

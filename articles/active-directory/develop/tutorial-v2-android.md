---
title: 사용자 로그인 및 Microsoft Graph(Android) 호출 - Microsoft ID 플랫폼 | Azure
description: 액세스 토큰을 가져오고 Microsoft ID 플랫폼(Android)의 액세스 토큰이 필요한 Microsoft Graph 또는 API를 호출합니다.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7feefc368815b1bfe57b67db2cd94702db799d78
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961560"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>자습서: Android 앱에서 사용자 로그인 및 Microsoft Graph 호출

> [!NOTE]
> 이 자습서는 Android 버전 1.0 라이브러리용 MSAL로 작동하도록 아직 업데이트되지 않았습니다. 이 자습서에 구성한 대로 이전 버전에서 작동합니다.

이 자습서에서는 Android 앱을 Microsoft ID 플랫폼에 통합하는 방법을 알아봅니다. 개발자의 앱은 사용자를 로그인하고, Microsoft Graph API를 호출하기 위한 액세스 토큰을 가져오고, Microsoft Graph API를 요청합니다.  

> [!div class="checklist"]
> * Android 앱과 Microsoft ID 플랫폼 통합
> * 사용자 로그인
> * Microsoft Graph API를 호출할 액세스 토큰 가져오기
> * Microsoft Graph API를 호출합니다.  

이 자습서를 완료했으면 애플리케이션에서 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 또는 학교 계정뿐만 아니라 개인 Microsoft 계정(outlook.com, live.com 등 포함)의 로그인을 수락하게 됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="how-this-tutorial-works"></a>이 자습서의 작동 방식

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

이 자습서의 앱에서는 사용자로 로그인하고 사용자를 대신하여 데이터를 가져옵니다.  이 데이터는 권한 부여가 필요하고 Microsoft ID 플랫폼으로 보호되는 API(Microsoft Graph API)를 통해 액세스됩니다.

더 구체적으로 살펴보면 다음과 같습니다.

* 앱이 브라우저 또는 Microsoft Authenticator와 Intune 회사 포털을 통해 사용자를 로그인합니다.
* 최종 사용자는 애플리케이션에서 요청한 권한을 수락합니다.
* 앱에서 Microsoft Graph API에 대한 액세스 토큰을 발급합니다.
* 액세스 토큰은 웹 API에 대한 HTTP 요청에 포함됩니다.
* Microsoft Graph 응답을 처리합니다.

이 샘플에서는 MSAL(Android용 Microsoft 인증 라이브러리)을 사용하여 [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal) 인증을 구현합니다.

 MSAL은 자동으로 토큰을 갱신하고, 디바이스의 다른 앱 간에 SSO(Single Sign-On)를 제공하고, 계정을 관리합니다.

## <a name="prerequisites"></a>필수 조건

* 이 자습서에는 Android Studio 버전 3.5가 필요합니다.

## <a name="create-a-project"></a>프로젝트 만들기

이 자습서에서는 새 프로젝트를 만듭니다. 새 프로젝트를 만드는 대신 완료된 자습서를 다운로드하려면 [코드를 다운로드](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)합니다.

1. Android Studio를 열고 **새 Android Studio 프로젝트 시작**을 선택합니다.
2. **기본 작업**을 선택하고 **다음**을 선택합니다.
3. 애플리케이션의 이름을 지정합니다.
4. 패키지 이름을 저장합니다. 나중에 Azure Portal에 입력할 것입니다.
5. 언어를 **Kotlin**에서 **Java**로 변경합니다.
6. **최소 API 수준**을 **API 19** 이상으로 설정하고 **마침**을 클릭합니다.
7. 프로젝트 보기의 드롭다운 목록에서 **프로젝트**를 선택하여 소스 및 소스가 아닌 프로젝트 파일을 표시하고, **app/build.gradle**을 열고, `targetSdkVersion`을 `28`로 설정합니다.

## <a name="register-your-application"></a>애플리케이션 등록

1. [Azure 포털](https://aka.ms/MobileAppReg)로 이동합니다.
2. [앱 등록 블레이드](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)를 열고 **+새 등록**을 클릭합니다.
3. 앱의 **이름**을 입력한 다음, 리디렉션 URI를 설정하지 않고 **등록**을 클릭합니다.
4. 나타나는 창의 **관리** 섹션에서 **인증** >  **+ 플랫폼 추가** > **Android**를 선택합니다. (이 섹션을 보려면 블레이드 상단 근처에 있는 "새 환경으로 전환"을 선택해야 할 수도 있습니다.)
5. 프로젝트의 패키지 이름을 입력합니다. 코드를 다운로드한 경우 이 값은 `com.azuresamples.msalandroidapp`입니다.
6. **Android 앱 구성** 페이지의 **서명 해시** 섹션에서 **개발 서명 해시 생성**을 클릭합니다. 플랫폼에 사용할 KeyTool 명령을 복사합니다.

   > [!Note]
   > KeyTool.exe는 JDK(Java Development Kit)의 일부로 설치됩니다. 또한 OpenSSL 도구를 설치하여 KeyTool 명령도 실행해야 합니다.

7. KeyTool에서 생성된 **서명 해시**를 입력합니다.
8. `Configure`를 클릭하고 **Android 구성** 페이지에 나타나는 **MSAL 구성**을 저장합니다. 그러면 나중에 앱을 구성할 때 이 구성을 입력할 수 있습니다.  **Done**을 클릭합니다.

## <a name="build-your-app"></a>앱 빌드

### <a name="add-your-app-registration"></a>앱 등록 추가

1. Android Studio의 프로젝트 창에서 **app\src\main\res**로 이동합니다.
2. **res**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기** > **디렉터리**를 선택합니다. 새 디렉터리 이름으로 `raw`를 입력하고 **확인**을 클릭합니다.
3. **app** > **src** > **main** > **res** > **raw**에서 `auth_config.json`이라는 새 JSON 파일을 만들고, 앞에서 저장한 MSAL 구성을 붙여넣습니다. 자세한 내용은 [MSAL 구성](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app)을 참조하세요.
4. **app** > **src** > **main** > **AndroidManifest.xml**에서 아래의 `BrowserTabActivity` 작업을 애플리케이션 본문에 추가합니다. 이 항목은 다음과 같이 Microsoft에서 인증을 완료한 후 애플리케이션을 콜백할 수 있게 해줍니다.

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

    `android:host=` 값을 Azure Portal에서 등록한 패키지 이름으로 바꿉니다.
    `android:path=` 값을 Azure Portal에서 등록한 키 해시로 바꿉니다. 서명 해시는 URL로 인코딩되면 안 됩니다.

5. **AndroidManifest.xml** 내부에서 `<application>` 태그 바로 위에 다음 권한을 추가합니다.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>앱의 UI 만들기

1. Android Studio 프로젝트 창에서 **app** > **src** > **main** > **res** > **layout**으로 이동하여 **activity_main.xml**을 연 다음, **텍스트** 보기를 엽니다.
2. 작업 레이아웃을 변경합니다(예: `<androidx.coordinatorlayout.widget.CoordinatorLayout`에서 `<androidx.coordinatorlayout.widget.DrawerLayout`으로 변경). 
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

1. Android Studio 프로젝트 창에서 **app** > **src** > **build.gradle**로 이동합니다.
2. **종속성** 아래에 다음 코드를 붙여넣습니다.

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3+'
    ```

### <a name="use-msal"></a>MSAL 사용

앱에 MSAL을 추가하여 사용하도록 `MainActivity.java` 내부에서 변경 작업을 수행합니다.
Android Studio 프로젝트 창에서 **app** > **src** > **main** > **java** > **com.example.(앱)** 로 이동하여 `MainActivity.java`를 엽니다.

#### <a name="required-imports"></a>꼭 가져와야 하는 항목

`MainActivity.java`의 위쪽에 다음 가져오기를 추가합니다.

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

#### <a name="instantiate-msal"></a>MSAL 인스턴스화

`MainActivity` 클래스 내부에서 액세스할 범위 및 웹 API를 포함하여 앱이 할 일에 대한 몇 가지 구성과 함께 MSAL을 인스턴스화해야 합니다.

다음 변수를 `MainActivity` 클래스 내부에 복사합니다.

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

`onCreate()`의 콘텐츠를 다음 코드로 바꿔서 MSAL을 인스턴스화합니다.

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

위의 코드는 사용자가 `getAccounts()`를 통해 애플리케이션을 열면 자동으로 사용자를 로그인하려고 시도하고, 성공하면 `acquireTokenSilentAsync()`를 시도합니다.  다음 몇 개 섹션에서는 로그인된 계정이 없는 경우를 대비한 콜백 처리기를 구현하겠습니다.

#### <a name="use-msal-to-get-tokens"></a>MSAL을 사용하여 토큰 가져오기

이제 MSAL을 통해 대화형으로 앱의 UI 처리 논리를 구현하고 토큰을 가져올 수 있습니다.

MSAL은 토큰을 가져오기 위한 두 가지 기본 메서드 `acquireTokenSilentAsync()` 및 `acquireToken()`을 공개합니다.  

`acquireTokenSilentAsync()`는 계정이 있는 경우 사용자 개입 없이 사용자를 로그인하고 토큰을 가져옵니다. 이것이 성공하면 MSAL이 앱에 토큰을 전달하고, 실패하면 `MsalUiRequiredException`을 생성합니다.  이 예외가 생성되거나 사용자에게 대화형 로그인 환경을 제공하려는 경우(자격 증명, mfa 또는 기타 조건부 액세스 정책이 필요할 수도 있고 필요하지 않을 수도 있음) `acquireToken()`을 사용합니다.  

`acquireToken()`은 사용자를 로그인하고 토큰을 가져오려고 시도할 때 UI를 표시합니다. 그러나 대화형 SSO 환경을 제공하기 위해 브라우저에서 세션 쿠키를 사용하거나 Microsoft 인증자에서 계정을 사용할 수 있습니다.

`MainActivity` 클래스 내부에 다음 세 가지 UI 메서드를 만듭니다.

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

현재 작업을 가져와서 자동 및 대화형 콜백을 처리하는 다음 메서드를 추가합니다.

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

다음으로, 로그아웃에 대한 지원을 추가합니다.

> [!Important]
> MSAL을 사용하여 로그아웃하면 사용자에 대해 알려진 모든 정보가 이 애플리케이션에서 제거되지만, 사용자의 디바이스에서는 여전히 활성 세션이 유지됩니다. 사용자가 다시 로그인하려고 시도할 때 로그인 UI가 표시될 수 있지만, 디바이스 세션이 여전히 활성 상태이므로 자격 증명을 다시 입력할 필요가 없습니다.

로그아웃 기능을 추가하려면 `MainActivity` 클래스 내부에 다음 메서드를 추가합니다. 이 메서드는 모든 계정을 순환하고 제거합니다.

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

토큰을 수신한 후에는 [Microsoft Graph API](https://graph.microsoft.com)를 요청할 수 있습니다. 액세스 토큰은 인증 콜백의 `onSuccess()` 메서드 내부에 있는 `AuthenticationResult` 내부에 배치됩니다. 권한 있는 요청을 생성하려면 다음과 같이 앱이 HTTP 헤더에 액세스 토큰을 추가해야 합니다.

| 헤더 키    | 값                 |
| ------------- | --------------------- |
| 권한 부여 | 전달자 \<access-token> |

다음 두 메서드를 `MainActivity` 클래스 내부에 추가하여 그래프를 호출하고 UI를 업데이트합니다.

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

#### <a name="multi-account-applications"></a>다중 계정 애플리케이션

이 앱은 단일 계정 시나리오를 위해 빌드됩니다. MSAL은 다중 계정 시나리오도 지원하지만, 그러려면 앱에서 몇 가지 추가 작업을 수행해야 합니다. 사용자가 토큰이 필요한 각 작업에 어떤 계정을 사용할 것인지 선택할 수 있는 UI를 만들어야 합니다. 또는 앱에서 `getAccounts()` 메서드를 통해 어떤 계정을 사용할 것인지 선택하는 경험적 접근을 구현할 수 있습니다.

## <a name="test-your-app"></a>앱 테스트

### <a name="run-locally"></a>로컬 실행

앱을 빌드하여 테스트용 디바이스 또는 에뮬레이터에 배포합니다. 로그인하여 Azure AD 계정 또는 Microsoft 개인 계정의 토큰을 가져올 수 있어야 합니다.

로그인하면 이 앱은 Microsoft Graph `/me` 엔드포인트에서 반환된 데이터를 표시합니다.

### <a name="consent"></a>동의

사용자가 앱에 처음으로 로그인하면 Microsoft ID는 사용자에게 요청된 권한에 동의할 것을 요구합니다.  대부분의 사용자가 동의할 수 있지만, 사용자 동의가 해제된 일부 Azure AD 테넌트에서는 관리자가 모든 사용자를 대신하여 동의해야 합니다. 이 시나리오를 지원하려면 Azure Portal에서 앱의 범위를 등록해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [애플리케이션 등록 단계](#register-your-application)에서 만든 애플리케이션 개체를 삭제합니다.

## <a name="get-help"></a>도움말 보기

이 자습서 또는 Microsoft ID 플랫폼과 관련하여 문제가 있는 경우 [도움말 및 지원](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)을 방문하세요.

Microsoft ID 플랫폼을 개선할 수 있도록 도와주세요. 간단한 두 가지 설문 조사를 완료하여 의견을 알려주세요.

> [!div class="nextstepaction"]
> [Microsoft ID 플랫폼 설문 조사](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

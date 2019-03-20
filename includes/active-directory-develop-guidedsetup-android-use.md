---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: a0724cd743a841808d8aa7e5061ddfdaaac9a13c
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203718"
---
## <a name="use-msal-to-get-a-token"></a>MSAL을 사용하여 토큰 가져오기

1. **앱** > **java** > **{domain}.{appname}** 아래에서 `MainActivity`를 엽니다. 
2. 다음 가져오기를 추가합니다.

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
    ```

3. `MainActivity` 클래스를 다음 코드로 바꿉니다.

    ```java
    public class MainActivity extends AppCompatActivity {

        final static String CLIENT_ID = "[Enter the application Id here]";
        final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
        final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

        /* UI & Debugging Variables */
        private static final String TAG = MainActivity.class.getSimpleName();
        Button callGraphButton;
        Button signOutButton;

        /* Azure AD Variables */
        private PublicClientApplication sampleApp;
        private AuthenticationResult authResult;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
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
            sampleApp = null;
            if (sampleApp == null) {
                sampleApp = new PublicClientApplication(
                        this.getApplicationContext(),
                        CLIENT_ID);
            }

    /* Attempt to get a user and acquireTokenSilent
    * If this fails we do an interactive request
    */
            List<User> users = null;

            try {
                users = sampleApp.getUsers();

                if (users != null && users.size() == 1) {
            /* We have 1 user */

                    sampleApp.acquireTokenSilentAsync(SCOPES, users.get(0), getAuthSilentCallback());
                } else {
            /* We have no user */

            /* Let's do an interactive request */
                    sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
                }
            } catch (MsalClientException e) {
                Log.d(TAG, "MSAL Exception Generated while getting users: " + e.toString());

            } catch (IndexOutOfBoundsException e) {
                Log.d(TAG, "User at this position does not exist: " + e.toString());
            }

        }

    //
    // App callbacks for MSAL
    // ======================
    // getActivity() - returns activity so we can acquireToken within a callback
    // getAuthSilentCallback() - callback defined to handle acquireTokenSilent() case
    // getAuthInteractiveCallback() - callback defined to handle acquireToken() case
    //

        public Activity getActivity() {
            return this;
        }

        /* Callback method for acquireTokenSilent calls 
        * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
        * else errors that we need to do an interactive request.
        */
        private AuthenticationCallback getAuthSilentCallback() {
            return new AuthenticationCallback() {
                @Override
                public void onSuccess(AuthenticationResult authenticationResult) {
                /* Successfully got a token, call Graph now */
                    Log.d(TAG, "Successfully authenticated");

                /* Store the authResult */
                    authResult = authenticationResult;

                /* call graph */
                    callGraphAPI();

                /* update the UI to post call Graph state */
                    updateSuccessUI();
                }

                @Override
                public void onError(MsalException exception) {
                /* Failed to acquireToken */
                    Log.d(TAG, "Authentication failed: " + exception.toString());

                    if (exception instanceof MsalClientException) {
                    /* Exception inside MSAL, more info inside MsalError.java */
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
                public void onSuccess(AuthenticationResult authenticationResult) {
                /* Successfully got a token, call graph now */
                    Log.d(TAG, "Successfully authenticated");
                    Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

                /* Store the auth result */
                    authResult = authenticationResult;

                /* call Graph */
                    callGraphAPI();

                /* update the UI to post call Graph state */
                    updateSuccessUI();
                }

                @Override
                public void onError(MsalException exception) {
                /* Failed to acquireToken */
                    Log.d(TAG, "Authentication failed: " + exception.toString());

                    if (exception instanceof MsalClientException) {
                    /* Exception inside MSAL, more info inside MsalError.java */
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

        /* Set the UI for successful token acquisition data */
        private void updateSuccessUI() {
            callGraphButton.setVisibility(View.INVISIBLE);
            signOutButton.setVisibility(View.VISIBLE);
            findViewById(R.id.welcome).setVisibility(View.VISIBLE);
            ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
                    authResult.getUser().getName());
            findViewById(R.id.graphData).setVisibility(View.VISIBLE);
        }

        /* Use MSAL to acquireToken for the end-user
        * Callback will call Graph api w/ access token & update UI
        */
        private void onCallGraphClicked() {
            sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
        }

        /* Handles the redirect from the System Browser */
        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            sampleApp.handleInteractiveRequestRedirect(requestCode, resultCode, data);
        }

    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>자세한 정보

#### <a name="get-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기

`AcquireTokenAsync` 메서드를 호출하면 사용자에게 로그인하거나 계정을 선택하라는 메시지를 표시하는 창이 실행됩니다. 일반적으로 애플리케이션은 사용자에게 초기 조작을 요청해야 하지만 해당 지점부터는 자동으로 작동할 수 있습니다. 

#### <a name="get-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기

`AcquireTokenSilentAsync` 메서드는 사용자 조작 없이 토큰을 가져옵니다.  `AcquireTokenSilentAsync`는 최선의 요청으로 처리될 수 있으며, 사용자가 다시 로그인하거나 다단계 인증과 같은 추가 권한 부여를 수행해야 하는 경우 `AcquireTokenAsync`로 대체됩니다. 

`AcquireTokenSilentAsync`가 실패하면 `MsalUiRequiredException`을 생성합니다. 애플리케이션에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

* `AcquireTokenAsync`를 즉시 호출합니다. 이 호출로 인해 사용자에게 로그인하라는 메시지가 표시됩니다. 이 패턴은 사용자가 사용할 수 있는 오프라인 콘텐츠가 없는 온라인 애플리케이션에서 사용됩니다. 이 자습서에서 생성된 샘플은 이 패턴을 사용합니다. 이 패턴은 샘플을 처음 실행할 때 작동되는 항목을 확인할 수 있습니다.
* 대화형 로그인이 필요하다는 시각적 표시를 사용자에게 제공합니다. 사용자가 준비되면 `AcquireTokenAsync`를 호출합니다.
* 나중에 `AcquireTokenSilentAsync`를 다시 시도합니다. 이 패턴은 사용자가 중단 없이 다른 애플리케이션 기능을 사용할 수 있는 경우(예: 오프라인 콘텐츠를 애플리케이션에서 사용할 수 있는 경우)에 자주 사용됩니다. 네트워크가 일시적으로 사용할 수 없게 된 후에 복원된 경우 애플리케이션이 `AcquireTokenSilentAsync`를 다시 시도하도록 결정할 수 있습니다. 
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API 호출

`MainActivity` 클래스에 다음 메서드를 추가합니다.

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
        public Map<String, String> getHeaders() throws AuthFailureError {
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

/* Sets the Graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = (TextView) findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>보호되는 API에 대한 REST 호출에 관한 추가 정보

이 애플리케이션 예제에서 `callGraphAPI()`는 `getAccessToken()`을 사용하여 새 액세스 토큰을 가져옵니다.  앱은 Microsoft Graph API에 대한 HTTP `GET` 요청에 토큰을 사용합니다. 
<!--end-collapse-->

## <a name="set-up-sign-out"></a>로그아웃 설정

`MainActivity` 클래스에 다음 메서드를 추가합니다.

```java
/* Clears a user's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 */
private void onSignOutClicked() {

    /* Attempt to get a user and remove their cookies from cache */
    List<User> users = null;

    try {
        users = sampleApp.getUsers();

        if (users == null) {
            /* We have no users */

        } else if (users.size() == 1) {
            /* We have 1 user */
            /* Remove from token cache */
            sampleApp.remove(users.get(0));
            updateSignedOutUI();

        }
        else {
            /* We have multiple users */
            for (int i = 0; i < users.size(); i++) {
                sampleApp.remove(users.get(i));
            }
        }

        Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
                .show();

    } catch (MsalClientException e) {
        Log.d(TAG, "MSAL Exception Generated while getting users: " + e.toString());

    } catch (IndexOutOfBoundsException e) {
        Log.d(TAG, "User at this position does not exist: " + e.toString());
    }
}

/* Set the UI for signed-out user */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");
}
```
<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>사용자 로그아웃에 대한 자세한 내용

`onSignOutClicked()` 메서드는 MSAL 캐시에서 사용자를 제거합니다. MSAL에는 로그인한 사용자의 상태가 더 이상 없으며, 애플리케이션에서 로그아웃됩니다. 

### <a name="more-information-on-multi-account-scenarios"></a>다중 계정 시나리오에 대한 자세한 정보

MSAL은 여러 계정이 동시에 로그인하는 시나리오도 지원합니다. 예를 들어, 많은 메일 앱은 여러 계정이 동시에 앱에 로그인할 수 있도록 허용합니다. 
<!--end-collapse-->

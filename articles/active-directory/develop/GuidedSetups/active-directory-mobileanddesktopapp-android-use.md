---
title: "Azure AD v2 Android 시작 - 사용 | Microsoft Docs"
description: "Android 앱이 액세스 토큰을 얻고 Azure Active Directory v2 끝점에서 액세스 토큰이 필요한 Microsoft Graph API를 한 개 이상 호출할 수 있는 방법"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 7963a07a2b9d529e89302f32e5ffd56c51687ffa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>MSAL(Microsoft 인증 라이브러리)를 사용하여 Microsoft Graph API에 대한 토큰 가져오기

1.  (`app` > `java` > `{domain}.{appname}`에서) `MainActivity`를 엽니다.
2.  다음 가져오기를 추가합니다.

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
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
아래 내용으로 `MainActivity`를 바꿉니다.
</li>
</ol>

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
            /* User canceled the authentication */
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
            /* User canceled the authentication */
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
### <a name="more-information"></a>추가 정보
#### <a name="getting-a-user-token-interactive"></a>대화형 사용자 토큰 가져오기
`AcquireTokenAsync` 메서드를 호출하면 사용자에게 로그인하라는 창이 표시됩니다. 사용자가 처음으로 보호되는 리소스에 액세스해야 하거나 토큰 획득을 위한 자동 작업에 실패한 경우(예: 사용자의 암호 만료) 일반적으로 사용자는 응용 프로그램에서 대화식으로 로그인해야 합니다.

#### <a name="getting-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기
`AcquireTokenSilentAsync`는 토큰 획득 및 갱신을 자동으로 처리합니다. `AcquireTokenAsync`가 처음으로 실행된 후 요청에 대한 호출 또는 토큰 갱신이 자동으로 수행되므로 `AcquireTokenSilentAsync`는 후속 호출을 위해 보호되는 리소스에 액세스하려고 토큰을 얻는 데 일반적으로 사용되는 메서드가 됩니다.
결국 `AcquireTokenSilentAsync`에 실패합니다(예: 사용자 로그아웃 또는 다른 장치에서 사용자가 암호 변경). MSAL이 대화형 작업을 요구해 이 문제를 해결할 수 있다고 감지하면 `MsalUiRequiredException`을 실행합니다. 응용 프로그램에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

1.  즉시 `AcquireTokenAsync`에 대한 호출을 수행합니다. 그러면 사용자에게 로그인하라는 메시지가 표시됩니다. 이 패턴은 응용 프로그램에 사용자가 사용할 수 있는 오프라인 콘텐츠가 없는 온라인 응용 프로그램에서 일반적으로 사용됩니다. 이 안내식 설정에서 생성한 샘플에서는 이 패턴을 사용합니다. 여러분이 처음으로 샘플을 실행하면 로그인하라는 메시지를 볼 수 있습니다. 지금까지 이 응용 프로그램을 사용한 사용자가 없기 때문에 `PublicClientApp.Users.FirstOrDefault`에는 null 값이 포함되어 있고 `MsalUiRequiredException` 예외가 발생합니다. 샘플의 코드는 `AcquireTokenAsync`를 호출해 예외를 처리하여 사용자에게 로그인하라는 메시지가 표시됩니다.
2.  또한 응용 프로그램에서는 대화형 로그인이 필요하다는 시각적 표시를 사용자에게 보여줍니다. 따라서 사용자가 로그인할 적절한 시간을 선택하거나 이후에 응용 프로그램이 `AcquireTokenSilentAsync`를 다시 시작할 수 있습니다. 이는 사용자가 중단 없이 응용 프로그램의 기능에 액세스할 수 있는 경우(예: 응용 프로그램에 사용 가능한 오프라인 콘텐츠가 있는 경우) 일반적으로 사용됩니다. 이 경우 사용자가 보호되는 리소스에 액세스하거나 오래된 정보를 새로 고치기 위해 로그인할 시점을 결정하거나 응용 프로그램이 일시적으로 사용할 수 없게 된 후 네트워크가 복원된 경우 `AcquireTokenSilentAsync`를 다시 시도하도록 결정할 수 있습니다.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>방금 가져온 토큰을 사용하여 Microsoft Graph API 호출
1.  `MainActivity` 클래스에 다음 메서드를 추가합니다.

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

이 샘플 응용 프로그램에서는 `callGraphAPI`가 `getAccessToken`을 호출한 다음 토큰을 필요로 하고 콘텐츠를 반환하는 리소스에 대해 HTTP `GET` 요청을 수행합니다. 이 메서드는 *HTTP 인증 헤더*에 획득된 토큰을 추가합니다. 이 샘플에서 리소스는 사용자 프로필 정보를 표시하는 Microsoft Graph API *me* 끝점입니다.
<!--end-collapse-->

## <a name="setup-sign-out"></a>로그아웃 설정

1.  `MainActivity` 클래스에 다음 메서드를 추가합니다.

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
### <a name="more-information"></a>자세한 정보

위의 `onSignOutClicked`는 MSAL 사용자 캐시에서 사용자를 제거하여 MSAL에 현재 사용자를 잊으라고 효율적으로 전달합니다. 따라서 대화식으로 수행되는 경우에만 토큰 획득을 위한 이후 요청에 성공하게 됩니다.
이 샘플의 응용 프로그램이 단일 사용자를 지원하더라도 MSAL은 동시에 여러 계정에 로그인할 수 있는 시나리오를 지원합니다(예: 사용자 한 명이 여러 계정을 가질 수 있는 메일 응용 프로그램).
<!--end-collapse-->

<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="elioda" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda"></tags>

# Azure 알림 허브 사용자에게 알림

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/ko-kr/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows 범용">Windows 범용</a><a href="/ko-kr/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
        <a href="/ko-kr/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android" class="current">Android</a>
</div>

Azure의 푸시 알림 지원을 통해 사용하기 쉬운 다중 플랫폼 및 규모 확장 푸시 인프라에 액세스할 수 있어, 모바일 플랫폼용 소비자 응용 프로그램 및 엔터프라이즈 응용 프로그램 모두에 대한 푸시 알림을 매우 간단하게 구현할 수 있습니다. 이 자습서에서는 Azure 알림 허브를 사용하여 특정 장치에서 특정 앱 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 지침 항목 [앱 백 엔드에서 등록][앱 백 엔드에서 등록]에 나와 있는 대로 ASP.NET WebAPI 백 엔드는 클라이언트를 인증하고 알림을 생성하는 데 사용됩니다. 이 자습서는 **알림 허브 시작** 자습서에서 만든 알림 허브를 기반으로 합니다.

> [AZURE.NOTE] 이 자습서에서는 [알림 허브 시작(Android)][알림 허브 시작(Android)]에 설명된 대로 알림 허브를 만들고 구성했다고 가정합니다.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers][notification-hubs-aspnet-backend-notifyusers]]

## Android 프로젝트 만들기

다음은 Android 응용 프로그램을 만드는 단계입니다.

1.  [알림 허브 시작(Android)][알림 허브 시작(Android)] 자습서에 따라 앱을 만들고 GCM에서 푸시 알림을 받도록 구성합니다.

2.  res/layout/activity\_main.xml 파일을 열고 콘텐츠를 다음으로 대체합니다.

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.notifyusers.MainActivity"
            android:orientation="vertical">
            <EditText
                android:id="@+id/usernameText"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:hint="@string/usernameHint" >
            </EditText>
            <EditText
                android:id="@+id/passwordText"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:hint="@string/passwordHint"
                android:inputType="textPassword" />
            <Button
                android:id="@+id/buttonLogin"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/loginButton"
                android:onClick="login" />
            <Button
                android:id="@+id/buttonSend"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/sendButton"
                android:onClick="sendPush" />
        </LinearLayout>

3.  res/values/strings.xml 파일을 열고 다음 줄을 추가합니다.

        <string name="usernameHint">Username</string>
        <string name="passwordHint">Password</string>
        <string name="loginButton">1. Log in</string>
        <string name="sendButton">2. Send push</string>

    main\_activity.xml 그래픽 레이아웃은 다음과 같이 표시되어야 합니다.

    ![][]

4.  이제 **MainActivity** 클래스와 같은 패키지에서 **RegisterClient** 클래스를 만듭니다. `{backend endpoint}`를 이전 섹션에서 얻은 백 엔드 끝점으로 바꿔야 합니다.

        import java.io.IOException;
        import java.io.UnsupportedEncodingException;
        import java.util.Set;

        import org.apache.http.HttpResponse;
        import org.apache.http.HttpStatus;
        import org.apache.http.client.ClientProtocolException;
        import org.apache.http.client.HttpClient;
        import org.apache.http.client.methods.HttpPost;
        import org.apache.http.client.methods.HttpPut;
        import org.apache.http.client.methods.HttpUriRequest;
        import org.apache.http.entity.StringEntity;
        import org.apache.http.impl.client.DefaultHttpClient;
        import org.apache.http.util.EntityUtils;
        import org.json.JSONArray;
        import org.json.JSONException;
        import org.json.JSONObject;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.util.Log;

        public class RegisterClient {
            private static final String PREFS_NAME = "ANHSettings";
            private static final String REGID_SETTING_NAME = "ANHRegistrationId";
            private static final String BACKEND_ENDPOINT = "{backend endpoint}/api/register";
            SharedPreferences settings;
            protected HttpClient httpClient;
            private String authorizationHeader;

            public RegisterClient(Context context) {
                super();
                this.settings = context.getSharedPreferences(PREFS_NAME, 0);
                httpClient =  new DefaultHttpClient();
            }

            public String getAuthorizationHeader() {
                return authorizationHeader;
            }

            public void setAuthorizationHeader(String authorizationHeader) {
                this.authorizationHeader = authorizationHeader;
            }

            public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
                String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

                JSONObject deviceInfo = new JSONObject();
                deviceInfo.put("Platform", "gcm");
                deviceInfo.put("Handle", handle);
                deviceInfo.put("Tags", new JSONArray(tags));

                int statusCode = upsertRegistration(registrationId, deviceInfo);

                if (statusCode == HttpStatus.SC_OK) {
                    return;
                } else if (statusCode == HttpStatus.SC_GONE){
                    settings.edit().remove(REGID_SETTING_NAME).commit();
                    registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                    statusCode = upsertRegistration(registrationId, deviceInfo);
                    if (statusCode != HttpStatus.SC_OK) {
                        Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                        throw new RuntimeException("Error upserting registration");
                    }
                } else {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            }

            private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                    throws UnsupportedEncodingException, IOException,
                    ClientProtocolException {
                HttpPut request = new HttpPut(BACKEND_ENDPOINT+"/"+registrationId);
                request.setEntity(new StringEntity(deviceInfo.toString()));
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                request.addHeader("Content-Type", "application/json");
                HttpResponse response = httpClient.execute(request);
                int statusCode = response.getStatusLine().getStatusCode();
                return statusCode;
            }

            private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
                if (settings.contains(REGID_SETTING_NAME))
                    return settings.getString(REGID_SETTING_NAME, null);

                HttpUriRequest request = new HttpPost(BACKEND_ENDPOINT+"?handle="+handle);
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                HttpResponse response = httpClient.execute(request);
                if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                    throw new RuntimeException("Error creating Notification Hubs registrationId");
                }
                String registrationId = EntityUtils.toString(response.getEntity());
                registrationId = registrationId.substring(1, registrationId.length()-1);

                settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();

                return registrationId;
            }
        }

    이 구성 요소는 푸시 알림을 등록하기 위해 앱 백 엔드에 접속하는 데 필요한 REST 호출을 구현합니다. 또한 [앱 백 엔드에서 등록][앱 백 엔드에서 등록]에 설명된 대로 알림 허브에서 생성된 *registrationId*를 로컬로 저장합니다. 이 구성 요소는 **로그인 및 등록** 단추를 클릭할 때 로컬 저장소에 저장된 인증 토큰을 사용합니다.

5.  **MainActivity** 클래스에서 **NotificationHub**에 대한 Private 필드를 제거하고 **Notifications**에 대한 필드를 추가합니다.

        //private NotificationHub hub;
        private RegisterClient registerClient;

6.  **onCreate** 메서드에서 **hub** 필드 및 **registerWithNotificationHubs** 메서드의 초기화를 제거합니다. **RegisterClient** 클래스 인스턴스를 초기화하는 다음 줄을 추가합니다. 메서드에는 다음 줄이 포함되어야 합니다.

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);

            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);
            gcm = GoogleCloudMessaging.getInstance(this);

            registerClient = new RegisterClient(this);

            setContentView(R.layout.activity_main);
        }

7.  그리고 나서 다음 메서드를 추가하여 `{backend endpoint}`를 이전 섹션에서 얻은 백 엔드 끝점으로 바꿔야 합니다.

        @Override
        protected void onStart() {
            super.onStart();
            Button sendPush = (Button) findViewById(R.id.buttonSend);
            sendPush.setEnabled(false);
        }

        public void login(View view) throws UnsupportedEncodingException {
            this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

            final Context context = this;
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(SENDER_ID);
                        registerClient.register(regid, new HashSet<String>());
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    Button sendPush = (Button) findViewById(R.id.buttonSend);
                    sendPush.setEnabled(true);
                    Toast.makeText(context, "Logged in and registered.",
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

        public void sendPush(View view) throws ClientProtocolException, IOException {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpPost("{backend endpoint}/api/notifications");
                        request.addHeader("Authorization", "Basic "+getAuthorizationHeader());
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error sending notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error sending notification");
                        }
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to send notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
            return basicAuthHeader;
        }

    **로그인**에 대한 콜백은 입력 사용자 이름과 암호(이는 인증 체계에서 사용하는 모든 토큰을 나타냄)를 기반으로 기본 인증 토큰을 생성하고 나서 `RegisterClient`를 사용하여 백 엔드를 호출합니다. **푸시 보내기**에 대한 콜백은 백 엔드를 호출하여 이 사용자의 모든 장치로 보안 알림을 트리거합니다.

## 응용 프로그램 실행

응용 프로그램을 실행하려면 다음을 수행합니다.

1.  **AppBackend**가 Azure에 배포되었는지 확인합니다. Visual Studio를 사용할 경우 **AppBackend** Web API 응용 프로그램을 실행합니다. ASP.NET 웹 페이지가 표시됩니다.

2.  Eclipse에서는 실제 Android 장치나 에뮬레이터에서 앱을 실행합니다.

3.  Android 앱 UI에서 사용자 이름과 암호를 입력합니다. 이는 임의 문자열일 수 있지만 같은 값이어야 합니다.

4.  Android 앱 UI에서 **로그인**을 클릭합니다. 그리고 나서 **푸시 보내기**를 클릭합니다.

  [Windows 범용]: /ko-kr/documentation/articles/notification-hubs-windows-dotnet-notify-users/ "Windows 범용"
  [iOS]: /ko-kr/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/ "iOS"
  [Android]: /ko-kr/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/ "Android"
  [앱 백 엔드에서 등록]: http://msdn.microsoft.com/en-us/library/dn743807.aspx
  [알림 허브 시작(Android)]: /ko-kr/documentation/articles/notification-hubs-android-get-started/
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  []: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users1.PNG

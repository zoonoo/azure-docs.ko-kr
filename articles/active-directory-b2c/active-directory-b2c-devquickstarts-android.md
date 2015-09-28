<properties pageTitle="Azure AD B2C Preview: Calling a Web API from an Android application | Microsoft Azure" description="This article will show you how to create an Android "To-Do List" app that calls a node.js web API using OAuth 2.0 bearer tokens. Android 앱과 Web API 모두는 Azure AD B2C를 사용하여 사용자 ID를 관리하고 사용자를 인증합니다." services="active-directory-b2c" documentationCenter="android" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="brandwe"/>

# Azure AD B2C 미리 보기: Android 응용 프로그램에서 Web API 호출하기

Azure AD B2C를 사용하여 몇가지 간단한 단계에서 강력한 셀프 서비스 ID 관리 기능을 Android 앱 및 Web API에 추가할 수 있습니다. 이 문서에서는 OAuth 2.0 전달자 토큰을 사용하여 node.js Web API를 호출하는 Android "할 일 목록" 앱을 만드는 방법을 보여줍니다. Android 앱 및 Web API는 Azure AD B2C를 사용하여 사용자 ID를 관리하고 사용자를 인증합니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]
	이 빠른 시작이 완벽하게 작동하기 위해 B2C를 사용하여 Azure AD에서 Web API를 보호하는 필수 구성 요소를 포함합니다. .Net 및 node.js 모두에 사용할 수 있도록 만들었습니다. 
	이 연습에서는 node.js Web API 샘플 구성되었다고 가정합니다. [Node.js 자습서에 대한 Azure AD B2C Web API](active-directory-b2c-devquickstarts-api-node.md)를 참조하세요.

> [AZURE.NOTE]이
	문서는 Azure AD B2C를 사용하여 등록, 로그인 및 프로필 관리를 구현하는 방법을 다루지 않습니다. 사용자를 인증한 후에 Web API를 호출하는 데 집중합니다.
    아직 준비되지 않은 경우 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)로 시작하여 Azure AD B2C의 기본 사항에 대해 알아봅니다.

보호된 리소스에 액세스해야 하는 Android 클라이언트의 경우 Azure AD는 Active Directory 인증 라이브러리 또는 ADAL을 제공합니다. ADAL의 유일한 용도는 앱이 쉽게 액세스 토큰을 가져오도록 하는 것입니다. 액세스 토큰을 얼마나 쉽게 가져올 수 있는지 보여 주기 위해 다음을 수행하는 Android To-Do List 응용 프로그램을 빌드해 보겠습니다.

-	[OAuth 2.0 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn645545.aspx)을 사용하여 To-Do List API를 호출하기 위한 액세스 토큰을 가져옵니다.
-	사용자의 할 일 목록을 가져옵니다.
-	사용자를 로그아웃합니다.



### 1단계: Azure AD B2C 디렉터리 가져오기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 디렉터리가 없는 경우 넘어가기 전에 [B2C 디렉터리 만들기](active-directory-b2c-get-started.md)로 이동합니다.

### 2단계: 응용 프로그램 만들기

이제 B2C 디렉터리에 앱을 만들어야 하며 Azure AD가 앱과 안전하게 통신해야 한다는 일부 정보를 제공합니다. 이 경우 하나의 논리 앱을 구성하기 때문에 앱과 Web API 모두는 단일 **응용 프로그램 ID**에서 표현됩니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 반드시

- 응용 프로그램에서 **웹앱/Web API** 포함
- `http://localhost:3000/auth/openid/return`을 **회신 URL**로 입력 - 이 코드 샘플에 대한 기본 URL입니다.
- 응용 프로그램에 **응용 프로그램 암호**를 만들고 복사합니다. 곧 필요합니다.
- 앱에 할당된 **응용 프로그램 ID**를 적복사합니다. 또한 곧 필요합니다.

### 3단계: 정책 만들기

Azure AD B2C에서 모든 사용자 환경을[**정책**](active-directory-b2c-reference-policies.md)에서 정의합니다. 이 앱은 등록, 로그인 및 Facebook으로 로그인 등 세 가지 ID 환경을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)에서 설명한 대로 각 형식에 하나의 정책을 만들어야 합니다. 세 가지 정책을 만들 때 다음을 확인합니다.

- 등록 정책에서 **표시 이름** 및 다른 몇가지 등록 특성을 선택합니다.
- 모든 정책에서 **표시 이름** 및 **개체 ID** 응용 프로그램 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
- 각 정책을 만든 후에 **이름**을 복사합니다. 접두사`b2c_1_`가 있어야 합니다. 이러한 정책 이름이 곧 필요합니다. 

세 가지 정책을 성공적으로 만들었다면 앱을 빌드할 준비가 되었습니다.

이 문서는 방금 만든 정책을 사용하는 방법을 다루지 않습니다. Azure AD B2C에서 정책 작동 방법을 알아보지 못했다면 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)로 시작해야 합니다.

### 4단계: 코드 다운로드

이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android)에서 유지 관리됩니다. 진행하면서 샘플을 빌드하려면 [골격 프로젝트를 .zip으로 다운로드](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip)하거나 골격을 복제합니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**이 자습서를 완료하려면 골격을 다운로드해야 합니다.** Android에서 완벽하게 작동하는 응용 프로그램 구현의 복잡성으로 인해 다음 자습서를 완료하면 **골격**은 실행할 UX 코드를 실행합니다. 개발자를 위한 시간 절약 측정입니다. UX 코드는 B2C를 Android 응용 프로그램에 추가하는 항목과 밀접한 관련이 없습니다.

또한 완성된 앱은 [.zip으로 사용할 수 있거나](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) 동일한 리포지토리의 `complete`분기에 사용할 수 있습니다.


Maven으로 빌드하려면 최상위 수준에서 pom.xml을 사용할 수 있습니다.


  * [Android용 maven 설정을 위한 필수 구성 요소 섹션](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)의 단계를 수행합니다.
  * SDK 21을 사용하여 에뮬레이터 설치
  * 리포지토리를 복제한 루트 폴더로 이동합니다.
  * mvn clean install 명령을 실행합니다.
  * cd samples\\hello를 실행하여 빠른 시작 샘플로 디렉터리를 변경합니다.
  * mvn android:deploy android:run 명령을 실행합니다.
  * 앱이 시작되는 것을 볼 수 있습니다.
  * 테스트 사용자 자격 증명을 입력하여 사용해 보세요.

aar 패키지 외에 Jar 패키지도 제출됩니다.

### 5단계: Android ADAL을 다운로드한 후 Eclipse 작업 영역에 추가

사용자가 Android 프로젝트에서 이 라이브러리를 사용하기 위한 여러 옵션을 사용할 수 있도록 구현했습니다.

* 소스 코드를 사용하여 Eclipse에 이 라이브러리를 가져온 후 응용 프로그램에 연결할 수 있습니다.
* Android Studio를 사용하는 경우 *aar* 패키지 형식을 사용하고 이 이진 파일을 참조할 수 있습니다.

####옵션 1: Git를 통해 소스 가져오기

git를 통해 SDK 소스 코드를 가져오려면 다음을 입력합니다.

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"

####옵션 2: Gradle을 통해 이진 파일 가져오기

Maven 중앙 리포지토리에서 이진 파일을 가져올 수 있습니다. AAR 패키지는 AndroidStudio의 프로젝트에 다음과 같이 포함할 수 있습니다.

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:2.0-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0-alpha
}
```

####옵션 3: Maven 통해 aar 가져오기

Eclipse의 m2e 플러그 인을 사용하는 경우 pom.xml 파일에 대한 종속성을 지정할 수 있습니다.

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0-alpha</version>
    <type>aar</type>
</dependency>
```


####옵션 5: libs 폴더 안에 jar 패키지 가져오기
maven에서 리포지토리로 jar 파일을 가져와 프로젝트의 *libs* 폴더에 놓을 수 있습니다. Jar 패키지에는 필수 리소스가 들어 있지 않으므로 이러한 필수 리소스를 프로젝트에 복사해야 합니다.


### 6단계: 프로젝트에 Android ADAL에 대한 참조 추가


2. 프로젝트에 대한 참조를 추가하고 Android 라이브러리로 지정합니다. 이 작업 방법을 잘 모르는 경우 [여기를 클릭하여 자세한 내용을 확인하세요](http://developer.android.com/tools/projects/projects-eclipse.html).

3. 디버깅하기 위해 프로젝트 종속성을 프로젝트 설정에 추가합니다.

4. 다음을 포함하도록 프로젝트의 AndroidManifest.xml 파일을 업데이트합니다.

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

### 7단계: 코드를 추가하여 Android용 ADAL 호출

주요 활동을 만들고 이를 `ToDoActivity`라고 합니다.

활동을 시작하고 UI을 제어하는 일부 단추를 추가합니다. 앞에서 Android 코드를 작성했다면 이 역시 매우 간단하고 친숙합니다.

```
public class ToDoActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();
```

같은 클래스에서(대괄호를 아직 닫지 말 것) UI에 단추를 추가합니다. Facebook 로그인 및 전자 메일 로그인에 단추를 추가한 것을 볼 수 있습니다. `constants.java` 파일에 정의된 정책을 사용합니다.

```
        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

    ```
Create an instance of AuthenticationContext at your main Activity:

    ```Java
 		 mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false, InMemoryCacheStore.getInstance());
            mAuthContext.getCache().removeAll();

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
    ```
  * NOTE: mContext is a field in your activity
 
 Let's also define our Result method `onActivityResult` that will be called when we have a result from the callback we'll write later:
 
 ```
 @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
 ```

Next, let's define our callback:

    ```Java
    mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY, Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getAccessToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
        }
        Toast.makeText(getApplicationContext(), TAG + "done", Toast.LENGTH_SHORT).show();
    }
        ```
        
 You may notice that this relies on methods we haven't written yet, such as `updateLoggedInUser()` and `getTasks()`. We'll write those below.	You can safely ignore the errors in Android Studio for now.

Explanation of the parameters:

  * ***SCOPES*** is required and is the scopes you are trying to reqeust access for. For the B2C preview this is the same as the Clientid but will change in the future.
  * ***POLICY*** is the policy for while you wish to authenticate the user. 
  * ***CLIENT_ID*** is required and comes from the AzureAD Portal.
  * You can setup redirectUri as your packagename. It is not required to be provided for the acquireToken call.
  * ***USER_HINT*** is the way we look up if the user is already in the cache and prompt the user if they are not found or the access token is invalid.
  * ***PROMPT*** helps to ask for credentials to skip cache and cookie.
  * ***Callback*** will be called after authorization code is exchanged for a token.

  The Callback will have an object of AuthenticationResult which has accesstoken, date expired, and idtoken info.

> [AZURE.NOTE]	Microsoft Intune's Company portal app provides the broker component and may be installed on the user's device. Developers should be prepared to allow for it's use as it provides SSO across all applications on the device. ADAL for Android will use the broker account if there is one user account created in the Authenticator. To use the broker, the developer needs to register a special redirectUri for broker usage. RedirectUri is in the format of msauth://packagename/Base64UrlencodedSignature. You can get your redirecturi for your app using the script `brokerRedirectPrint.ps1` or use API call `mContext.getBrokerRedirectUri()`. The signature is related to your signing certificates from the Google Play store.

 A Developer can skip the broker user with:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.WARNING] In order to reduce the complexity of this B2C Quickstart, we have opted in our sample to skip the broker. However, this is not recommended.

Next, let's create some helper methods that will get the token alone during our authentication calls to the Task API:

```
private void getToken(final AuthenticationCallback callback) {

        // one of the acquireToken overloads
        mAuthContext.acquireToken(onnstants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY,
                Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                "nux=1&" + Constants.EXTRA_QP, callback);
    }

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {
        Constants.CURRENT_RESULT = newToken;
    }
    
```

Finally, Your app manifest should have permissions to use AccountManager accounts: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


### Step 8. Call the Task API

Now that we have our Activity wired up and ready to go to do the heavy lifting of grabbing tokens, let's write our API to access the task server.

Our `getTasks` provides an array that represents the tasks in our server 
Our `addTask` and `deleteTask` do the subsequent action and return TRUE or FALSE if it was successful.

Let's write our `getTask` first:

```
private void getTasks() { if (Constants.CURRENT\_RESULT == null || Constants.CURRENT\_RESULT.getAccessToken().isEmpty()) return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(Constants.CURRENT_RESULT.getAccessToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }
 ```
 
 또한 테이블에서 처음으로 실행하는 메서드를 작성합니다.
 
 ```
     private void initAppTables() {
        try {
            // Get the Mobile Service Table instance to use
//            mToDoTable = mClient.getTable(WorkItem.class);
//            mToDoTable.TABvLES_URL = "/api/";
            //mTextNewToDo = (EditText)findViewById(R.id.listViewToDo);

            // Create an adapter to bind the items with the view
            //mAdapter = new WorkItemAdapter(ToDoActivity.this, R.layout.listViewToDo);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 You'll see that this code requires some additional methods to do it's work. Let's write those now.
 
 ### Create endpoint URL generator
 
 We need to generate the endpoint URL that we'll be connecting to. Let's do that in the same class file:
 
 
 ```
     private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

    ```


Note that we add the access token to the request in the following code:

### Step 9: Let's write some UX methods

Android requires us to handle some callbacks in order to operate the app. These are `createAndShowDialog` and `onResume()`. This is pretty simple and very familiar if you've written Android code before. 

Let's write those now:

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }
    
```

And now manage our dialog callbacks:


```
/** * 대화 상자 만들기 및 표시 * * @param exception 대화 상자에 표시되는 예외 * @param title 대화 상자 제목 */ private void createAndShowDialog(Exception exception, String title) { createAndShowDialog(exception.toString(), title); }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }
    
 ```
    


### 10단계: 샘플 앱 실행

마지막으로 Android Studio 나 Eclipse에서 두 앱을 빌드 및 실행합니다. 앱에 등록 또는 로그인하고 로그인된 사용자에 대한 작업을 만듭니다. 해당 사용자에 대한 작업을 만들면서 로그아웃하고 다른 사용자 권한으로 다시 로그인합니다.

API 가 받는 액세스 토큰에서 사용자의 ID를 추출하므로 API에 사용자 당 작업이 저장됩니다.

참조를 위해 완성된 샘플이 [여기서 .zip으로 제공](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


<!--

### Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

### Important Information


#### Encryption

ADAL encrypts the tokens and store in SharedPreferences by default. You can look at the StorageHelper class to see the details. Android introduced AndroidKeyStore for 4.3(API18) secure storage of private keys. ADAL uses that for API18 and above. If you want to use ADAL for lower SDK versions, you need to provide secret key at AuthenticationSettings.INSTANCE.setSecretKey

#### Session cookies in Webview

Android webview does not clear session cookies after app is closed. You can handle this with sample code below:
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
More about cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=Sept15_HO3-->
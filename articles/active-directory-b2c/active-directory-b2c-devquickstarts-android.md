<properties pageTitle="Azure AD B2C Preview: Calling a Web API from an Android application | Microsoft Azure" description="This article will show you how to create an Android "To-Do List" app that calls a node.js web API using OAuth 2.0 bearer tokens. Android 앱과 Web API 모두는 Azure AD B2C를 사용하여 사용자 ID를 관리하고 사용자를 인증합니다." services="active-directory-b2c" documentationCenter="android" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Azure AD B2C 미리 보기: Android 응용 프로그램에서 Web API 호출하기

Azure AD B2C를 사용하여 몇가지 간단한 단계에서 강력한 셀프 서비스 ID 관리 기능을 Android 앱 및 Web API에 추가할 수 있습니다. 이 문서에서는 OAuth 2.0 전달자 토큰을 사용하여 node.js Web API를 호출하는 Android "할 일 목록" 앱을 만드는 방법을 보여줍니다. Android 앱 및 Web API는 Azure AD B2C를 사용하여 사용자 ID를 관리하고 사용자를 인증합니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

 	
> [AZURE.NOTE]
	이 빠른 시작이 완벽하게 작동하기 위해 B2C를 사용하여 Azure AD에서 Web API를 보호하는 필수 구성 요소를 포함합니다. .Net 및 node.js 모두에 사용할 수 있도록 만들었습니다. 이 연습에서는 node.js Web API 샘플 구성되었다고 가정합니다. [Node.js 자습서에 대한 Azure AD B2C Web API](active-directory-b2c-devquickstarts-api-node.md)를 참조하세요.

 
> [AZURE.NOTE]
	이 문서는 Azure AD B2C를 사용하여 등록, 로그인 및 프로필 관리를 구현하는 방법을 다루지 않습니다. 사용자를 인증한 후에 Web API를 호출하는 데 집중합니다. 아직 준비되지 않은 경우 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)로 시작하여 Azure AD B2C의 기본 사항에 대해 알아봅니다.


보호된 리소스에 액세스해야 하는 Android 클라이언트의 경우 Azure AD는 Active Directory 인증 라이브러리 또는 ADAL을 제공합니다. ADAL의 유일한 용도는 앱이 쉽게 액세스 토큰을 가져오도록 하는 것입니다. 액세스 토큰을 얼마나 쉽게 가져올 수 있는지 보여 주기 위해 다음을 수행하는 Android To-Do List 응용 프로그램을 빌드해 보겠습니다.

-	[OAuth 2.0 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn645545.aspx)을 사용하여 To-Do List API를 호출하기 위한 액세스 토큰을 가져옵니다.
-	사용자의 할 일 목록을 가져옵니다.
-	사용자를 로그아웃합니다.



### 1단계: Azure AD B2C 디렉터리 가져오기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 디렉터리가 없는 경우 넘어가기 전에 [B2C 디렉터리 만들기](active-directory-b2c-get-started.md)로 이동합니다.

### 2단계: 응용 프로그램 만들기

이제 B2C 디렉터리에 앱을 만들어야 하며 Azure AD가 앱과 안전하게 통신해야 한다는 일부 정보를 제공합니다. 이 경우 하나의 논리 앱을 구성하기 때문에 앱과 Web API 모두는 단일 **응용 프로그램 ID**에서 표현됩니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 반드시

- 응용 프로그램에서 **웹앱/웹 API** 포함
- `urn:ietf:wg:oauth:2.0:oob`을 **회신 URL**로 입력 - 이 코드 샘플에 대한 기본 URL입니다.
- 응용 프로그램에 **응용 프로그램 암호**를 만들고 복사합니다. 곧 필요합니다. 참고로 이 값은 사용하기 전에 [XML 이스케이프](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape)되어야 합니다.
- 앱에 할당된 **응용 프로그램 ID**를 적복사합니다. 또한 곧 필요합니다.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

### 3단계: 정책 만들기

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Azure AD B2C에서 모든 사용자 환경은 [**정책**](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 앱은 등록, 로그인 및 Facebook으로 로그인 등 세 가지 ID 환경을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)에서 설명한 대로 각 형식에 하나의 정책을 만들어야 합니다. 세 가지 정책을 만들 때 다음을 확인합니다.

- 등록 정책에서 **표시 이름** 및 다른 몇 가지 등록 특성을 선택합니다.
- 모든 정책에서 **표시 이름** 및 **개체 ID** 응용 프로그램 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
- 각 정책을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다. 이러한 정책 이름이 곧 필요합니다. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

세 가지 정책을 성공적으로 만들었다면 앱을 빌드할 준비가 되었습니다.

이 문서는 방금 만든 정책을 사용하는 방법을 다루지 않습니다. Azure AD B2C에서 정책 작동 방법을 알아보려면 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)로 시작해야 합니다.

### 4단계: 코드 다운로드

이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android)에서 유지 관리됩니다. 진행하면서 샘플을 빌드하려면 [골격 프로젝트를 .zip으로 다운로드](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip)하거나 골격을 복제합니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **이 자습서를 완료하려면 골격을 다운로드해야 합니다.** Android에서 완벽하게 작동하는 응용 프로그램을 구현하는 것이 복잡하기 때문에 다음 자습서를 완료하면 실행될 UX 코드가 **골격**에 포함되어 있습니다. 개발자를 위한 시간 절약 측정입니다. UX 코드는 B2C를 Android 응용 프로그램에 추가하는 항목과 밀접한 관련이 없습니다.

완성된 앱도 [.zip으로 다운로드하거나](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) 동일한 리포지토리의 `complete` 분기에서 사용할 수 있습니다.


Maven으로 빌드하려면 최상위 수준에서 pom.xml을 사용할 수 있습니다.


  * [Android용 maven 설정을 위한 필수 조건 섹션](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)의 단계를 수행합니다.
  * SDK 21을 사용하여 에뮬레이터 설치
  * 리포지토리를 복제한 루트 폴더로 이동합니다.
  * mvn clean install 명령을 실행합니다.
  * cd samples\\hello를 실행하여 빠른 시작 샘플로 디렉터리를 변경합니다.
  * mvn android:deploy android:run 명령을 실행합니다.
  * 앱이 시작되는 것을 볼 수 있습니다.
  * 테스트 사용자 자격 증명을 입력하여 사용해 보세요.

aar 패키지 외에 Jar 패키지도 제출됩니다.

### 5단계: Android ADAL을 다운로드한 후 Android Studio 작업 영역에 추가

사용자가 Android 프로젝트에서 이 라이브러리를 사용하기 위한 여러 옵션을 사용할 수 있도록 구현했습니다.

* 소스 코드를 사용하여 Eclipse에 이 라이브러리를 가져온 후 응용 프로그램에 연결할 수 있습니다.
* Android Studio를 사용하는 경우 *aar* 패키지 형식을 사용하고 이 이진 파일을 참조할 수 있습니다.



####옵션 1: Gradle을 통해 이진 파일 가져오기(권장)

Maven 중앙 리포지토리에서 이진 파일을 가져올 수 있습니다. AAR 패키지는 AndroidStudio의 프로젝트에 다음과 같이 포함할 수 있습니다(예: `build.gradle`).

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

####옵션 2: Maven 통해 aar 가져오기

Eclipse의 m2e 플러그 인을 사용하는 경우 `pom.xml` 파일에서 종속성을 지정할 수 있습니다.

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

####옵션 3: Git를 통해 소스 가져오기(마지막 수단)

git를 통해 SDK 소스 코드를 가져오려면 다음을 입력합니다.

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"


### 6단계: 구성 파일 설정

Android 프로젝트를 구성하기 위해 위 B2C 포털에서 설정한 구성을 사용합니다.

`helpes/Constants.java`를 열고 다음에 대한 값을 입력합니다.

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
**SCOPES** - 서버에서 사용자 로그인에 대해 요청하려는 서버에 전달하는 범위입니다. B2C 미리 보기의 경우 client\_id를 전달합니다. 그러나 나중에 범위를 읽도록 변경됩니다. 이 문서는 업데이트됩니다. **ADDITIONAL\_SCOPES** - 응용 프로그램에 사용하려는 추가 범위입니다. 나중에 사용됩니다. **CLIENT\_ID** - 포털에서 가져온 응용 프로그램 ID입니다. **REDIRECT\_URL** - 토큰이 다시 게시될 리디렉션입니다. **EXTRA\_QP** - URL로 인코딩된 형식인 서버에 전달하려는 추가 데이터입니다. **FB\_POLICY** - 호출하는 정책입니다. 이 연습에 대한 중요한 부분을 게시합니다. **EMAIL\_SIGNIN\_POLICY** - 호출하는 정책입니다. 이 연습에 대한 중요한 부분을 게시합니다. **EMAIL\_SIGNUP\_POLICY** - 호출하는 정책입니다. 이 연습에 대한 중요한 부분을 게시합니다.

### 7단계: 프로젝트에 Android ADAL에 대한 참조 추가


> [AZURE.NOTE]	Android용 ADAL은 Intent 기반 모델을 사용하여 인증을 호출합니다. Intent는 작업을 수행하는 앱 “상”에 위치합니다. 이 샘플 전체 및 Android용 ADAL를 사용하는 모든 항목은 Intent를 관리하고 그 사이에서 정보를 전달합니다.


Android에 사용할 Intents()를 포함하여 응용 프로그램의 레이아웃에 대해 알리는 것이 첫 번째 할 일입니다. intent는 나중에 자세히 설명하겠습니다.

모든 Intent를 포함하도록 프로젝트의 AndroidManifest.xml 파일을 업데이트합니다.

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

표시된 바와 같이 5개의 사용할 활동을 정의합니다.

**AuthenticationActivity** - ADAL에서 비롯되며 로그인 webview를 제공합니다.

**LoginActivity** - 로그인 정책과 각 정책에 대한 단추를 표시합니다.

**SettingsActivity** - 런타임 시 앱 설정을 변경할 수 있도록 합니다.

**AddTaskActivity** - Azure AD로 보호되는 REST API에 작업을 추가할 수 있도록 합니다.

**ToDoActivity** - 작업을 표시하는 주요 활동입니다.



### 8단계: 로그인 활동 만들기

주요 활동을 만들고 이를 `LoginActivity`라고 합니다.

`LoginActivity.java`라는 파일을 만듭니다.

활동을 초기화하고 UI를 제어하는 단추를 추가합니다. 앞에서 Android 코드를 작성했다면 이 역시 매우 간단하고 친숙합니다.

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

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

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
지금까지 자체 활동을 참조로 extras 매개 변수를 사용하여 ToDoActivity Intent(토큰이 필요할 때 ADAL을 호출하는)를 호출하는 단추를 만들었습니다. extra 매개 변수는 `intent.putExtra()` 메서드로 전달됩니다. 여기서 `Constants.java`에서 지정한 것과 같이 "thePolicy"를 정의합니다. 이렇게 하면 인증을 하는 동안 호출할 정책을 Intent에 알릴 수 있습니다.

### 9단계: 설정 활동 만들기

이것은 설정 UI를 채우는 활동입니다.

`SettingsActivity.java`라는 파일을 만듭니다.

CRUD가 아주 간단합니다.

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

	//private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_settings);

        loadSettings();
//		checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//		checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


	}

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

	private void saveSettings(String key, boolean value) {
		SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
				Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
		Editor prefsEditor = prefs.edit();
		prefsEditor.putBoolean(key, value);
		prefsEditor.commit();
	}
}
```

### 10단계: AddTask 활동 만들기

이렇게 하면 REST API 끝점에 작업을 추가할 수 있습니다. 역시 매우 간단합니다.

`AddTaskActivity.java`라는 파일을 만듭니다.

그리고 아래와 같이 작성합니다.

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

### 11단계: ToDoList 활동 만들기

이제 정책에 대해 Azure AD에서 토큰을 가져오고 해당 토큰을 사용하여 Task REST API 서버를 호출할 수 있도록 하는 가장 중요한 활동을 확보했니다.

`ToDoActivity.java`라는 파일을 만듭니다.

다음과 같이 작성합니다.(호출은 나중에 설명하겠습니다.)

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

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
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
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

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}
   
```

        
 이는 `updateLoggedInUser()`, `clearSessionCookie()` 및 `getTasks()` 등 아직 작성하지 않은 메서드에 의존한다는 것을 알 수 있습니다. 이를 아래와 같이 작성할 것입니다. 지금은 Android Studio의 오류를 안전하게 무시할 수 있습니다.

매개 변수에 대한 설명:

  * ***SCOPES***는 필수 항목이며 액세스를 요청하려는 범위입니다. B2C 미리 보기의 경우, 이는 Clientid와는 동일하지만 나중에 변경됩니다.
  * ***POLICY***는 사용자를 인증하려는 기간 동안의 정책입니다. 
  * ***CLIENT\_ID***는 필수 항목이며 AzureAD 포털에서 제공됩니다.
  * redirectUri를 패키지 이름으로 설정할 수 있습니다. acquireToken 호출의 경우에는 제공하지 않아도 됩니다.
  * ***getUserInfo()***는 사용자가 캐시에 이미 있는 경우 조회하고 찾지 못하거나 액세스 토큰이 잘못된 경우 사용자에게 메시지를 표시하는 방법입니다. 이 메서드는 아래에서 작성합니다.
  * ***PromptBehavior.always***는 자격 증명을 요청하여 캐시 및 쿠키를 건너뛰는 데 도움이 됩니다.
  * ***Callback***은 인증 코드가 토큰과 교환되면 호출됩니다.

  이 Callback이 호출되면 accesstoken, 만료 날짜 및 idtoken 정보를 포함하는 AuthenticationResult의 개체가 생성됩니다.

> [AZURE.NOTE]	Microsoft Intune 회사 포털 앱은 broker 구성 요소를 제공하고 사용자의 장치에 설치할 수 있습니다. 개발자는 장치의 모든 응용 프로그램에 SSO 기능을 제공하므로 사용을 허용하도록 준비해야 합니다. Android용 ADAL은 Authenticator에서 만들어진 한 개의 사용자 계정이 있는 경우 broker 계정을 사용합니다. broker를 사용하기 위해 개발자는 broker용 특수 redirectUri를 등록해야 합니다. RedirectUri는 msauth://packagename/Base64UrlencodedSignature 형식입니다. `brokerRedirectPrint.ps1` 스크립트를 사용하여 앱용 redirecturi를 확보하거나 API 호출 `mContext.getBrokerRedirectUri()`를 사용할 수 있습니다. 서명은 Google Play 스토어의 인증서 서명과 관련이 있습니다.

 개발자는 다음을 사용해서 broker 사용자를 건너뛸 수 있습니다.

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] 이 B2C 퀵스타트의 복잡성을 줄이기 위해 샘플에서는 broker를 건너뛰도록 선택했습니다.

다음으로, 작업 API에 대한 인증을 호출하는 동안 토큰만 가지게 되는 일부 도우미 메서드를 만들어 보겠습니다.

`ToDoActivity.java`라는 **동일한 파일에서**

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

글로벌 CONSTANTS에 AuthenticationResult(토큰을 포함하는)를 “설정”하고 “가져올” 메서드를 추가해보겠습니다. `ToDoActivity.java`의 흐름에 **sResult**를 사용하더라도 다른 활동은 수행할 토큰에 대해 액세스할 수 없기 때문에(`AddTaskActivity.java`에서 작업 추가와 같이) 이것이 필요합니다.

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }

    
```
### 12단계: UserIdentifier를 반환할 메서드 만들기

Android용 ADAL은 **UserIdentifier** 개체의 형태로 사용자를 나타냅니다. 이것은 사용자를 관리하며 캐시에 의존하고 서버에 새로운 호출을 생성하도록 호출에 동일한 사용자가 사용되고 있으면 알 수 있도록 합니다. 이것이 용이하도록 `acquireToken()`에 사용할 수 있는 UserIdentifier를 반환할 `getUserInfo()`를 만듭니다. 캐시에서 UserIdentifier의 ID를 신속하게 반환해 줄 getUniqueId() 메서드도 만듭니다.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }
    
```
 
### 13단계: 도우미 메서드 작성

쿠키를 지우고 AuthenticationCallback을 제공하는데 도움이 되는 도우미 메서드를 작성할 필요가 있습니다. 이것은 ToDo 활동을 호출할 때 반드시 빈 상태가 되도록 하기 위해 샘플에 대해서만 사용됩니다.

`ToDoActivity.java`라는 **동일한 파일에서**

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
``` 

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
    
```   

### 14단계: 작업 API 호출

이제 활동이 유선으로 연결되어 있고 토큰을 구하는 어려운 작업을 하기 위해 준비가 되었으므로, 작업 서버에 액세스하는 API를 작성해 보겠습니다.

`getTasks`는 서버의 작업을 나타내는 배열을 제공합니다.

우선 `getTask`를 작성해 보겠습니다.

`ToDoActivity.java`라는 **동일한 파일에서**

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```


 
 처음 실행에서 테이블을 초기화하는 메서드를 작성해보겠습니다.
 
 `ToDoActivity.java`라는 **동일한 파일에서**
 
```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 이 코드가 작업을 수행하기 위해 추가 메서드 일부를 필요로 하는 것을 볼 수 있습니다. 지금 작성해 보겠습니다.
 
### 끝점 URL 생성기 만들기
 
 연결할 끝점 URL을 생성해야 합니다. 동일한 클래스 파일에서 이를 수행해보겠습니다.
 
 `ToDoActivity.java`라는 **동일한 파일에서**
 
 ``` private URL getEndpointUrl() { URL endpoint = null; try { endpoint = new URL(Constants.SERVICE\_URL); } catch (MalformedURLException e) { e.printStackTrace(); } return endpoint; }

 ```


다음 코드의 요청에 대한 액세스 토큰이 추가되었습니다.

### 15단계: UX 메서드 작성

Android는 앱 작동을 위해 일부 콜백 처리가 필요합니다. 이는 `createAndShowDialog` 및 `onResume()`입니다. 앞에서 Android 코드를 작성했다면 이 역시 매우 간단하고 친숙합니다.

지금 작성해 보겠습니다.

`ToDoActivity.java`라는 **동일한 파일에서**

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

그리고 대화 콜백을 관리합니다.

`ToDoActivity.java`라는 **동일한 파일에서**


```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

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

이것으로 끝입니다. 컴파일하는 `ToDoActivity.java` 파일이 있어야 합니다. 이때 전체 프로젝트 역시 컴파일해야 합니다.
    


### 16단계: 샘플 앱 실행

마지막으로 Android Studio 나 Eclipse에서 두 앱을 빌드 및 실행합니다. 앱에 등록 또는 로그인하고 로그인된 사용자에 대한 작업을 만듭니다. 해당 사용자에 대한 작업을 만들면서 로그아웃하고 다른 사용자 권한으로 다시 로그인합니다.

API 가 받는 액세스 토큰에서 사용자의 ID를 추출하므로 API에 사용자 당 작업이 저장됩니다.

참조를 위해 완성된 샘플이 [여기서 .zip으로 제공](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


### 중요 정보


#### 암호화

ADAL은 기본적으로 토큰을 암호화한 후 SharedPreferences에 저장합니다. StorageHelper 클래스를 확인하여 세부 정보를 볼 수 있습니다. Android에서는 개인 키의 보안 저장소인 4.3(API18)용 AndroidKeyStore를 도입했습니다. ADAL은 API18 이상에 이 저장소를 사용합니다. 더 낮은 SDK 버전에 ADAL을 사용하려는 경우 AuthenticationSettings.INSTANCE.setSecretKey에서 비밀 키를 제공해야 합니다.

#### Webview의 세션 쿠키

앱이 닫힌 후에 Android webview가 세션 쿠키를 지우지 않습니다. 아래 샘플 코드로 이 문제를 처리할 수 있습니다. ```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
``` 쿠키에 대한 추가 정보: http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=AcomDC_0224_2016-->
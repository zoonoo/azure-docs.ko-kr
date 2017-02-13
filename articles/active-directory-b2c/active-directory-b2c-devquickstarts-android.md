---
title: "Azure Active Directory B2C: Android 응용 프로그램에서 Web API 호출 | Microsoft Docs"
description: "이 문서에서는 OAuth 2.0 전달자 토큰을 사용하여 Node.js Web API를 호출하는 Android &quot;할 일 목록&quot; 앱을 만드는 방법을 보여 줍니다. Android 앱 및 Web API는 Azure Active Directory B2C를 사용하여 사용자 ID를 관리하고 사용자를 인증합니다."
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 47300333a7c41ad6f23b20ae0698bee839efd3bb


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C: Android 응용 프로그램에서 웹 API 호출
> [!WARNING]
> 이 자습서에서는 특히 B2C에 대한 Android ADAL의 사용을 제거하기 위해 몇 가지 중요 업데이트가 필요합니다.  Android 앱에서 Azure AD B2C 사용을 위한 새로운 지침을 다음 주에 게시하겠으니 그 때까지 보류하는 것이 좋습니다.  그러나 단지 시도해 보려는 것이라면 자유롭게 다음 문서를 계속 진행합니다.
>
>

Azure AD(Azure Active Directory) B2C를 사용하여 몇 가지 간단한 단계로 강력한 셀프 서비스 ID 관리 기능을 Android 앱 및 Web API에 추가할 수 있습니다. 이 문서에서는 OAuth 2.0 전달자 토큰을 사용하여 Node.js Web API를 호출하는 Android "할 일 목록" 앱을 만드는 방법을 보여줍니다. Android 앱 및 Web API는 Azure AD B2C를 사용하여 사용자 ID를 관리하고 사용자를 인증합니다.

이 빠른 시작은 제대로 작동하기 위해 B2C와 Azure AD로 보호되는 Web API가 필요합니다. .NET 및 Node.js 모두에 사용할 수 있도록 만들었습니다. 이 연습에서는 Node.js Web API 샘플이 구성되었다고 가정합니다. 자세한 내용은 [Node.js 자습서용 Azure AD B2C Web API](active-directory-b2c-devquickstarts-api-node.md)를 참조하세요.

보호된 리소스에 액세스해야 하는 Android 클라이언트의 경우 Azure AD는 ADAL(Active Directory 인증 라이브러리)를 제공합니다. ADAL의 유일한 용도는 앱이 쉽게 액세스 토큰을 가져오도록 하는 것입니다. 액세스 토큰을 얼마나 쉽게 가져올 수 있는지 보여 주기 위해 다음을 수행하는 Android 할 일 목록 응용 프로그램을 작성해 보겠습니다.

* [OAuth 2.0 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn645545.aspx)을 사용하여 할 일 목록 API를 호출하는 액세스 토큰을 가져옵니다.
* 사용자의 할 일 목록을 가져옵니다.
* 사용자를 로그아웃합니다.

> [!NOTE]
> 이 문서는 Azure AD B2C를 사용하여 등록, 로그인 및 프로필 관리를 구현하는 방법을 다루지 않습니다. 사용자를 인증한 후에 Web API를 호출하는 방법에 집중합니다. 아직 준비되지 않은 경우 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md) 를 시작하여 Azure AD B2C의 기본 사항에 대해 알아보아야 합니다.
>
>

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C 디렉터리 가져오기
Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 아직 없는 경우 [B2C 디렉터리를 만든](active-directory-b2c-get-started.md) 후에 이 가이드를 계속 진행합니다.

## <a name="create-an-application"></a>응용 프로그램 만들기
다음으로 B2C 디렉터리에서 앱을 만들어야 합니다. 앱과 안전하게 통신하는 데 필요한 Azure AD 정보를 제공합니다. 이 경우 앱과 웹 API는 모두 하나의 논리 앱을 구성하기 때문에 단일 **응용 프로그램 ID** 에서 나타납니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 다음을 수행해야 합니다.

* 응용 프로그램에 **웹앱**/**웹 API**를 포함합니다.
* **회신 URL**로 `urn:ietf:wg:oauth:2.0:oob`을 입력합니다. 이 코드 샘플에 대한 기본 URL입니다.
* 응용 프로그램에 **응용 프로그램 암호** 를 만들고 복사합니다. 이 시간은 나중에 필요합니다. 참고로 이 값은 사용하기 전에 [XML 이스케이프](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) 되어야 합니다.
* 앱에 할당된 **응용 프로그램 ID** 를 복사합니다. 이 ID는 나중에도 필요합니다.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>정책 만들기
[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Azure AD B2C에서 모든 사용자 환경은 [정책](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 앱은 등록, 로그인 및 Facebook을 사용하여 로그인 등 세 가지 ID 환경을 포함합니다.  [정책 참조 문서](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)에서 설명한 대로 각 형식에 하나의 정책을 만들어야 합니다. 세 가지 정책을 만들 때 다음을 확인합니다.

* 등록 정책에서 **표시 이름** 및 다른 등록 특성을 선택합니다.
* 모든 정책에서 **표시 이름** 및 **개체 ID** 응용 프로그램 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
* 각 정책을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다.  이러한 정책 이름이 나중에 필요합니다.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

세 가지 정책을 만들었다면 앱을 빌드할 준비가 되었습니다.

이 문서는 방금 만든 정책을 사용하는 방법을 다루지 않습니다. Azure AD B2C에서 정책 작동 방법을 알아보려면 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)를 시작합니다.

## <a name="download-the-code"></a>코드 다운로드
이 자습서에 대한 코드는 [GitHub에서 유지 관리됩니다](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). 진행하면서 샘플을 작성하기 위해 [골격 프로젝트를 .zip 파일로 다운로드](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip)할 수 있습니다. 기본 구조를 복제할 수도 있습니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [!NOTE]
> **이 자습서를 완료하려면 골격을 다운로드해야 합니다.**  완벽하게 작동하는 Android 응용 프로그램을 구현하는 것이 복잡하기 때문에 이 자습서를 완료한 후에 실행될 UX 코드가 골격에 포함되어 있습니다. 개발자를 위한 시간 절약 측정입니다. UX 코드는 Android 응용 프로그램에 B2C를 추가하는 방법 항목과 밀접한 관련이 없습니다.
>
>

완성된 앱도 [.zip 파일로 다운로드](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)하거나 동일한 리포지토리의 `complete` 분기에서 사용할 수 있습니다.

Maven으로 빌드하려면 최상위 수준에서 `pom.xml`을 사용할 수 있습니다.

1. [Android용 Maven을 설정하기 위한 필수 조건 섹션](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)의 단계를 수행합니다.
2. SDK 21로 에뮬레이터를 설정합니다.
3. 리포지토리를 복제한 루트 폴더로 이동합니다.
4. `mvn clean install`명령을 실행합니다.
5. 빠른 시작 샘플 `cd samples\hello`로 디렉터리를 변경합니다.
6. `mvn android:deploy android:run`명령을 실행합니다.

앱이 시작되는 것을 확인해야 합니다. 테스트 사용자 자격 증명을 입력하여 사용해 보세요.

AAR(Android Archive) 패키지 외에 JAR(Java Archive) 패키지도 제출됩니다.

## <a name="download-the-android-adal-and-add-it-to-your-android-studio-workspace"></a>Android ADAL을 다운로드하고 Android Studio 작업 영역에 추가
Android 프로젝트에 이 라이브러리를 사용하는 방법에 대한 옵션이 있습니다.

* 원본 코드를 사용하여 Eclipse에 라이브러리를 가져온 후 응용 프로그램에 연결할 수 있습니다.
* Android Studio를 사용하는 경우 AAR 패키지 형식을 사용하고 이진 파일을 참조할 수 있습니다.

### <a name="option-1-binaries-via-gradle-recommended"></a>옵션 1: Gradle을 통해 이진 파일 가져오기(권장)
Maven 중앙 리포지토리에서 이진 파일을 가져올 수 있습니다. AAR 패키지는 Android Studio의 프로젝트에 다음과 같이 포함될 수 있습니다(예: `build.gradle`).

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

### <a name="option-2-aar-via-maven"></a>옵션 2: Maven 통한 AAR
Eclipse에서 `m2e` 플러그 인을 사용하는 경우 `pom.xml` 파일에서 종속성을 지정할 수 있습니다.

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### <a name="option-3-source-via-git-last-resort"></a>옵션 3: Git를 통해 소스 가져오기(마지막 수단)
Git를 통해 SDK 원본 코드를 가져오려면 다음을 입력합니다.

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

분기 **수렴**

## <a name="set-up-your-configuration-file"></a>구성 파일 설정
이전에 B2C 포털에서 설정한 구성을 사용하여 Android 프로젝트를 구성합니다.

`helpes/Constants.java` 를 열고 다음에 대한 값을 입력합니다.

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
* `SCOPES`: 사용자가 로그인할 때 서버에서 요청하려는 서버에 다시 전달하는 범위입니다. B2C 미리 보기에 `client_id`를 전달합니다. 그러나 나중에 `read scopes` 로 변경되게 됩니다. 해당 경우 이 문서는 업데이트됩니다.
* `ADDITIONAL_SCOPES`: 응용 프로그램에 사용하려는 추가 범위입니다. 나중에 사용될 것입니다.
* `CLIENT_ID`: 포털에서 가져온 응용 프로그램 ID입니다.
* `REDIRECT_URL`: 토큰이 다시 게시되는 위치의 리디렉션입니다.
* `EXTRA_QP`: URL로 인코드된 형식인 서버에 전달하려는 추가 데이터입니다.
* `FB_POLICY`: 호출하는 정책입니다. 이 연습에 대한 중요한 부분입니다.
* `EMAIL_SIGNIN_POLICY`: 호출하는 정책입니다. 이 연습에 대한 중요한 부분입니다.
* `EMAIL_SIGNUP_POLICY`: 호출하는 정책입니다. 이 연습에 대한 중요한 부분입니다.

## <a name="add-references-to-android-adal-to-your-project"></a>프로젝트에 Android ADAL에 대한 참조 추가
> [!NOTE]
> Android용 ADAL은 의도 기반 모델을 사용하여 인증을 호출합니다. 의도는 작업을 수행하는 앱에 "적용됩니다". 이 전체 샘플 및 Android에 대한 모든 ADAL은 의도를 관리하는 방법 및 의도 간에 정보를 전달하는 방법에 집중합니다.
>
>

첫째, Android에 사용하려는 의도를 포함하여 응용 프로그램의 레이아웃에 대해 알려줍니다. 이러한 의도는 이 자습서의 뒷부분에서 자세히 설명됩니다.

모든 의도를 포함하도록 프로젝트의 `AndroidManifest.xml` 파일을 업데이트합니다.

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

여기에서 볼 수 있듯이 다섯 가지 작업을 정의합니다. 이 모두를 사용합니다.

* `AuthenticationActivity`: ADAL에서 가져오며 로그인 웹 뷰를 제공합니다.
* `LoginActivity`: 로그인 정책과 각 정책에 대한 단추를 표시합니다.
* `SettingsActivity`: 이를 사용하여 런타임 시 앱 설정을 변경할 수 있습니다.
* `AddTaskActivity`: 이를 사용하여 Azure AD로 보호되는 REST API에 태스크를 추가합니다.
* `ToDoActivity`: 태스크를 표시하는 주요 작업입니다.

## <a name="create-the-sign-in-activity"></a>로그인 작업 만들기
주요 활동을 만들고 이를 `LoginActivity`라고 합니다.

`LoginActivity.java`라는 파일을 만듭니다.

활동을 초기화하고 UI를 제어하는 단추를 추가합니다. 앞에서 Android 코드를 작성했다면 익숙할 것입니다.

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
`ToDoActivity` 의도를 호출하는 단추를 만들었습니다(토큰이 필요한 경우 ADAL을 호출함). 작업을 참조 및 추가 매개 변수로 사용하여 수행합니다. extra 매개 변수는 `intent.putExtra()` 메서드로 전달됩니다. `Constants.java`에 지정한 것을 사용하여 `"thePolicy"`을 정의합니다. 인증을 하는 동안 호출할 정책을 의도에 알려줍니다.

## <a name="create-the-settings-activity"></a>설정 작업 만들기
설정 UI를 채우는 작업입니다.

간단한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업에 `SettingsActivity.java` 라는 파일을 만듭니다.

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
//        checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//        checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

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

## <a name="create-the-add-task-activity"></a>add-task 작업 만들기
이 작업을 사용하여 REST API 끝점에 태스크를 추가할 수 있습니다.

`AddTaskActivity.java` 이라는 파일을 만들고 다음을 작성합니다.

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

## <a name="create-the-to-do-list-activity"></a>할 일 목록 작업 만들기
가장 중요한 작업입니다. 이를 사용하여 정책에 대한 Azure AD에서 토큰을 가져올 수 있으며 해당 토큰을 사용하여 태스크 REST API 서버를 호출할 수 있습니다.

`ToDoActivity.java` 이라는 파일을 만들고 다음을 작성합니다. (호출은 나중에 설명합니다.)

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


 아직 작성하지 않은 메서드에 의존한다는 점을 알 수 있습니다. `updateLoggedInUser()`, `clearSessionCookie()` 및 `getTasks()`을 포함합니다. 이 가이드의 뒷부분에 나오는 해당 사항을 작성합니다. 지금은 Android Studio의 오류를 안전하게 무시할 수 있습니다.

매개 변수에 대한 설명:

* `SCOPES`: 필수이며 액세스를 요청하려는 범위입니다. B2C 미리 보기의 경우 이는 `client_id`와 동일하지만 나중에 변경됩니다.
* `POLICY`: 사용자를 인증하기 위한 정책입니다.
* `CLIENT_ID`: 필수이며 Azure AD 포털에서 가져옵니다.
* `redirectUri`: 패키지 이름으로 설정될 수 있습니다. `acquireToken` 호출의 경우에는 제공하지 않아도 됩니다.
* `getUserInfo()`: 사용자가 캐시에 이미 있는지 조회하는 방법입니다. 또한 매개 변수는 해당 사용자를 찾을 수 없거나 사용자의 액세스 토큰이 유효하지 않은 경우 사용자에게 메시지를 표시하는 방법을 설명합니다. 이 메서드는 이 가이드의 뒷부분에 작성됩니다.
* `PromptBehavior.always`: 자격 증명을 요청하여 캐시 및 쿠키를 건너뛰는 데 도움이 됩니다.
* `Callback`: 인증 코드가 토큰과 교환되면 호출됩니다. 액세스 토큰, 만료 날짜 및 ID 토큰 정보를 포함하는 `AuthenticationResult`개체를 가져옵니다.

> [!NOTE]
> Microsoft Intune 회사 포털 앱은 broker 구성 요소를 제공하고 사용자의 장치에 설치될 수 있습니다. 앱은 장치에서 모든 응용 프로그램에 Single Sign-On(SSO) 액세스를 제공합니다. 개발자가 Intune에 대해 허용되도록 준비해야 합니다. Android용 ADAL은 인증자에서 만들어진 한 개의 사용자 계정이 있는 경우 broker 계정을 사용합니다. broker를 사용하기 위해 개발자는 broker에 특별한 `redirectUri` 를 등록해야 합니다. `redirectUri`의 형식은 msauth://packagename/Base64UrlencodedSignature입니다. `brokerRedirectPrint.ps1` 스크립트를 사용하거나 API 호출 `mContext.getBrokerRedirectUri()`를 사용하여 앱에 `redirectUri`를 가져올 수 있습니다. 서명은 Google Play 스토어의 인증서 서명과 관련이 있습니다.
>
>

 다음을 사용하여 broker 사용자를 건너뛸 수 있습니다.

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [!NOTE]
> 이 B2C 퀵스타트의 복잡성을 줄이기 위해 샘플에서 broker를 건너뛰도록 선택했습니다.
>
>

다음으로, 태스크 API에 대한 인증 호출 중에 토큰만을 가져오는 도우미 메서드를 만듭니다.

같은 `ToDoActivity.java` 파일에 다음을 추가합니다.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

`AuthenticationResult`(토큰이 있음)을 전역 `Constants`로 "설정"하고 "가져오는" 메서드를 추가합니다. `ToDoActivity.java`이 해당 흐름에서 `sResult`를 사용하더라도 이러한 메서드를 추가해야 합니다. 이렇게 하지 않으면 다른 작업은 수행하는 토큰에 액세스하지 않습니다(예: `AddTaskActivity.java`에 태스크 추가).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## <a name="create-a-method-to-return-a-user-identifier"></a>메서드를 만들어서 사용자 시별자 반환
Android용 ADAL은 `UserIdentifier` 개체의 형태로 사용자를 나타냅니다. 이 사용자를 관리합니다. 개체를 사용하여 동일한 사용자가 호출에 사용되는지 여부를 식별할 수 있습니다. 이 정보를 사용하여 서버에 대한 새 호출을 확인하지 않고 캐시에 의존할 수 있습니다. 보다 쉽게 수행하기 위해 `UserIdentifier`을 반환하는 `getUserInfo()` 메서드를 만들었습니다. `acquireToken()`로 이를 사용할 수 있습니다. 또한 캐시에 `UserIdentifier`의 ID를 반환하는 `getUniqueId()` 메서드를 만들었습니다.

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

### <a name="write-helper-methods"></a>도우미 메서드 작성
다음으로, 쿠키를 지우고 `AuthenticationCallback`을 제공할 수 있도록 몇 가지 도우미 메서드를 작성합니다. 이러한 메서드는 `ToDo` 작업을 호출할 때 클린 상태인지 확인하는 샘플에만 사용됩니다.

`ToDoActivity.java`라는 동일한 파일에 다음을 작성합니다.

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

## <a name="call-the-task-api"></a>태스크 API 호출
작업이 토큰을 가져올 준비가 되면 API를 작성하여 태스크 서버에 액세스할 수 있습니다.

`getTasks` 은 서버의 태스크를 나타내는 배열을 제공합니다.

`getTasks`을 시작합니다.

`ToDoActivity.java`라는 동일한 파일에 다음을 작성합니다.

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

또한 첫 실행에서 테이블을 초기화하는 메서드를 작성합니다.

`ToDoActivity.java`라는 동일한 파일에 다음을 작성합니다.

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

이 코드가 작업을 수행하기 위해 일부 추가 메서드를 필요로 하는 것을 볼 수 있습니다. 다음 해당 사항을 작성합니다.

### <a name="create-an-endpoint-url-generator"></a>끝점 URL 생성기 만들기
연결할 끝점 URL을 생성해야 합니다. 동일한 클래스 파일에서 이를 수행합니다.

**`ToDoActivity.java`라는 동일한 파일에** 다음을 작성합니다.

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


다음 섹션에서 설명한 코드의 요청에 액세스 토큰을 추가합니다.

## <a name="write-some-ux-methods"></a>일부 UX 메서드 작성
Android는 앱을 작동시키는 일부 콜백을 처리해야 합니다. 이는 `createAndShowDialog` 및 `onResume()`입니다. 앞에서 Android 코드를 작성했다면 익숙할 것입니다.

`ToDoActivity.java`라는 동일한 파일에 다음을 작성합니다.

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

다음으로 대화 상자 콜백을 관리합니다.

`ToDoActivity.java`라는 동일한 파일에 다음을 작성합니다.

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

이제 컴파일하는 `ToDoActivity.java` 파일이 있어야 합니다. 이때 전체 프로젝트 역시 컴파일해야 합니다.

## <a name="run-the-sample-app"></a>샘플 앱 실행
마지막으로 Android Studio 또는 Eclipse에서 앱을 작성하고 실행합니다. 앱 등록 및 로그인 로그인한 사용자에 대한 태스크를 만듭니다. 로그아웃하고 다른 사용자 권한으로 다시 로그인한 다음 해당 사용자에 대한 태스크를 만듭니다.

API가 수신한 액세스 토큰에서 사용자의 ID를 추출하므로 태스크가 API에 대해 사용자 단위로 저장됩니다.

참조를 위해 완료된 샘플은 [.zip 파일로 제공](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)됩니다. 또한 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```

## <a name="important-information"></a>중요 정보
### <a name="encryption"></a>암호화
ADAL은 기본적으로 토큰을 암호화한 후 `SharedPreferences` 에 저장합니다. `StorageHelper` 클래스를 확인하여 세부 정보를 볼 수 있습니다. Android에서는 개인 키의 보안 저장소인 **4.3(API18)용 AndroidKeyStore** 를 도입했습니다. ADAL은 API18 이상에 이 저장소를 사용합니다. 더 낮은 SDK 버전에 ADAL을 사용하려는 경우 `AuthenticationSettings.INSTANCE.setSecretKey`에서 비밀 키를 제공해야 합니다.

### <a name="session-cookies-in-web-view"></a>웹 뷰의 세션 쿠키
앱이 닫힌 후에 Android 웹 뷰가 세션 쿠키를 지우지 않습니다. 다음 샘플 코드로 이를 처리할 수 있습니다.

```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[쿠키에 대해 자세히 알아봅니다](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).



<!--HONumber=Dec16_HO5-->



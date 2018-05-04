---
title: Azure AD Android 시작 | Microsoft Docs
description: 로그인을 위해 Azure AD와 통합되고 OAuth 2.0을 사용하여 Azure AD로 보호되는 API를 호출하는 Android 응용 프로그램 빌드 방법
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/30/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 8ea69fbc95b9d9b2452f041e99c46d223581bdac
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-ad-android-getting-started"></a>Azure AD Android 시작
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

데스크톱 응용 프로그램을 개발하는 경우 Azure AD(Azure Active Directory)를 사용하면 간단하고 편리하게 온-프레미스 Active Directory 계정을 사용하여 사용자를 인증할 수 있습니다. 또한 응용 프로그램에서 Office 365 API 또는 Azure API와 같이 Azure AD를 통해 보호되는 웹 API를 안전하게 사용할 수 있습니다.

보호된 리소스에 액세스해야 하는 Android 클라이언트의 경우 Azure AD는 ADAL(Active Directory 인증 라이브러리)를 제공합니다. ADAL의 유일한 용도는 앱이 쉽게 액세스 토큰을 가져오도록 하는 것입니다. 액세스 토큰을 얼마나 쉽게 가져올 수 있는지 보여 주기 위해 다음을 수행하는 Android 할 일 목록 응용 프로그램을 작성해 보겠습니다.

* [OAuth 2.0 인증 프로토콜](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-oauth-code)을 사용하여 할 일 목록 API를 호출하기 위한 액세스 토큰을 가져옵니다.
* 사용자의 할 일 목록을 가져옵니다.
* 사용자를 로그아웃합니다.

시작하려면 사용자를 만들고 응용 프로그램을 등록할 수 있는 Azure AD 테넌트가 필요합니다. 테넌트가 아직 없는 경우 [얻는 방법을 알아보세요](active-directory-howto-tenant.md).

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>1단계: Node.js REST API TODO 샘플 서버 다운로드 및 실행
Node.js REST API TODO 샘플은 Azure AD용 단일 테넌트 To-Do REST API를 빌드하기 위한 기존 샘플에서도 작동하도록 특수하게 작성되었습니다. 다음은 빠른 시작을 위한 필수 구성 요소입니다.

설정 방법에 대한 자세한 내용은 [Node.js용 Azure Active Directory 샘플 REST API 서비스](active-directory-devquickstarts-webapi-nodejs.md)의 기존 샘플을 참조하세요.


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>2단계: Azure AD 테넌트에 Web API 등록
Active Directory는 두 가지 유형의 응용 프로그램 추가를 지원합니다.

- 사용자에게 서비스를 제공하는 Web API
- 해당 Web API에 액세스하는 응용 프로그램(웹 또는 장치에서 실행)

이 단계에서는 이 샘플을 테스트하기 위해 로컬로 실행하는 Web API를 등록할 것입니다. 일반적으로 이 Web API는 앱에서 액세스할 기능을 제공하는 REST 서비스입니다. Azure AD는 끝점을 보호하는 데 도움이 될 수 있습니다.

앞에서 참조한 TODO REST API를 등록하는 중이라고 가정합니다. 하지만 Azure Active Directory에서 보호하려는 모든 Web API에서 작동합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 위쪽 막대에서 계정을 클릭합니다. **디렉터리** 목록에서 응용 프로그램을 등록할 Azure AD 테넌트를 선택합니다.
3. 왼쪽 창에서 **모든 서비스**를 클릭한 다음, **Azure Active Directory**를 선택합니다.
4. **앱 등록**을 클릭하고 **추가**를 선택합니다.
5. 친숙한 응용 프로그램 이름(예:**TodoListService**)을 입력하고 **웹 응용 프로그램 및/또는 Web API**를 선택한 후 **다음**을 클릭합니다.
6. 로그온 URL에 대해서는 샘플의 기준 URL을 입력합니다. 기본적으로 `https://localhost:8080`입니다.
7. 등록을 완료하려면 **확인**을 클릭합니다.
8. Azure Portal에 있는 상태에서 응용 프로그램 페이지로 이동한 후 응용 프로그램 ID 값을 찾아 복사합니다. 나중에 이 응용 프로그램을 구성하는 경우에 필요합니다.
9. **설정** -> **속성** 페이지에서 앱 ID URI를 업데이트합니다(`https://<your_tenant_name>/TodoListService` 입력). `<your_tenant_name>`을 Azure AD 테넌트의 이름으로 바꿉니다.

## <a name="step-3-register-the-sample-android-native-client-application"></a>3단계: 샘플 Android 네이티브 클라이언트 응용 프로그램 등록
이 샘플에서는 웹 응용 프로그램을 등록해야 합니다. 이렇게 하면 응용 프로그램이 방금 등록한 Web API와 통신할 수 있습니다. 등록되지 않은 응용 프로그램의 경우 로그인을 요청하더라도 Azure AD에서 거부합니다. 이것은 모델 보안의 한 부분입니다.

앞에서 참조한 응용 프로그램 예제를 등록하는 중이라고 가정합니다. 하지만 이 절차는 개발 중인 모든 앱에 대해 작동합니다.

> [!NOTE]
> 한 테넌트에 응용 프로그램과 Web API를 모두 배치하는 이유가 궁금할 수 있습니다. 짐작할 수에 있는 것처럼 다른 테넌트에서 Azure AD에 등록된 외부 API에 액세스하는 앱을 빌드할 수 있습니다. 이렇게 하면 응용 프로그램에서 API 사용에 동의할지 묻는 메시지가 표시됩니다. iOS용 Active Directory 인증 라이브러리가 자동으로 동의 과정을 진행합니다. 고급 기능을 좀 더 살펴보게 되면 다른 서비스 공급자는 물론 Azure 및 Office에서 Microsoft API 모음에 액세스하는 데 필요한 작업의 중요한 부분이라는 것을 알 수 있게 될 것입니다. 이제 Web API와 응용 프로그램을 동일한 테넌트에서 등록했으므로 동의를 묻는 메시지는 표시되지 않습니다. 일반적으로 회사를 위해서만 응용 프로그램을 개발하는 경우가 여기에 해당합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 위쪽 막대에서 계정을 클릭합니다. **디렉터리** 목록에서 응용 프로그램을 등록할 Azure AD 테넌트를 선택합니다.
3. 왼쪽 창에서 **모든 서비스**를 클릭한 다음, **Azure Active Directory**를 선택합니다.
4. **앱 등록**을 클릭하고 **추가**를 선택합니다.
5. 응용 프로그램의 이름(예: **TodoListClient-Android**)을 입력하고 **네이티브 클라이언트 응용 프로그램**을 선택한 후 **다음**을 클릭합니다.
6. 리디렉션 URI로 `http://TodoListClient`를 입력합니다. **Finish**를 클릭합니다.
7. 응용 프로그램 페이지에서 응용 프로그램 ID 값을 찾아 복사합니다. 나중에 이 응용 프로그램을 구성하는 경우에 필요합니다.
8. **설정** 페이지에서 **필요한 사용 권한**, **추가**를 차례로 선택합니다.  TodoListService를 찾은 후 선택하고 **위임된 사용 권한**에서 **TodoListService 액세스** 사용 권한을 추가하고 **완료**를 클릭합니다.

Maven으로 빌드하려면 최상위 수준에서 pom.xml을 사용할 수 있습니다.

1. 선택한 디렉터리에 다음 리포지토리를 복제합니다.

  `$ git clone https://github.com/Azure-Samples/active-directory-android.git`  
2. [Android용 Maven 환경을 설정하기 위한 필수 조건 섹션](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Maven)의 단계를 수행합니다.
3. SDK 19로 에뮬레이터를 설정합니다.
4. 리포지토리를 복제한 루트 폴더로 이동합니다.
5. 다음 명령을 실행합니다. `mvn clean install`
6. 빠른 시작 샘플 `cd samples\hello`로 디렉터리를 변경합니다.
7. 다음 명령을 실행합니다. `mvn android:deploy android:run`

   앱이 시작되는 것을 확인해야 합니다.
8. 테스트 사용자 자격 증명을 입력하여 사용해 보세요.

AAR 패키지 외에 JAR 패키지가 제출됩니다.

## <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>4단계: Android ADAL을 다운로드한 후 Eclipse 작업 영역에 추가
사용자가 Android 프로젝트에서 ADAL을 사용하기 위한 여러 옵션을 사용할 수 있도록 구현했습니다.

* 소스 코드를 사용하여 Eclipse에 이 라이브러리를 가져온 후 응용 프로그램에 연결할 수 있습니다.
* Android Studio를 사용하는 경우 AAR 패키지 형식을 사용하고 이진 파일을 참조할 수 있습니다.

### <a name="option-1-source-zip"></a>옵션 1: 소스 Zip 가져오기
소스 코드의 복사본을 다운로드하려면 페이지 오른쪽에서 **ZIP 다운로드**를 클릭합니다. 또는 [GitHub에서 다운로드](https://github.com/AzureAD/azure-activedirectory-library-for-android/releases)할 수 있습니다.

### <a name="option-2-source-via-git"></a>옵션 2: Git를 통해 소스 가져오기
Git를 통해 SDK 원본 코드를 가져오려면 다음을 입력합니다.

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

### <a name="option-3-binaries-via-gradle"></a>옵션 3: Gradle을 통해 이진 파일 가져오기
Maven 중앙 리포지토리에서 이진 파일을 가져올 수 있습니다. AAR 패키지는 Android Studio의 프로젝트에 다음과 같이 포함할 수 있습니다.

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
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

### <a name="option-4-aar-via-maven"></a>옵션 4: Maven을 통해 AAR 가져오기
M2Eclipse 플러그 인을 사용하는 경우 pom.xml 파일에 대한 종속성을 지정할 수 있습니다.

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


### <a name="option-5-jar-package-inside-the-libs-folder"></a>옵션 5: libs 폴더 안에 JAR 패키지 가져오기
maven 리포지토리에서 JAR 파일을 가져와 프로젝트의 **libs** 폴더에 놓을 수 있습니다. JAR 패키지에는 필수 리소스가 들어 있지 않으므로 이러한 필수 리소스를 프로젝트에도 복사해야 합니다.

## <a name="step-5-add-references-to-android-adal-to-your-project"></a>5단계: 프로젝트에 Android ADAL에 대한 참조 추가
1. 프로젝트에 대한 참조를 추가하고 Android 라이브러리로 지정합니다. 작업을 수행하는 방법을 잘 모르는 경우 [Android Studio 사이트](http://developer.android.com/tools/projects/projects-eclipse.html)에서 자세한 정보를 얻을 수 있습니다.
2. 디버깅하기 위해 프로젝트 종속성을 프로젝트 설정에 추가합니다.
3. 다음을 포함하도록 프로젝트의 AndroidManifest.xml 파일을 업데이트합니다.

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

4. 기본 활동에서 AuthenticationContext의 인스턴스를 만듭니다. 이 호출의 세부 정보는 이 항목에서 다루지 않지만 [Android 네이티브 클라이언트 샘플](https://github.com/AzureAD/azure-activedirectory-library-for-android)에서 유용한 정보를 얻을 수 있습니다. 다음 예제에서 SharedPreferences는 기본 캐시이며 권한은 `https://login.microsoftonline.com/yourtenant.onmicrosoft.com` 형태입니다.

    `mContext = new AuthenticationContext(MainActivity.this, authority, true); // mContext is a field in your activity`

5. 사용자가 자격 증명을 입력하고 권한 부여 코드를 받은 후에 AuthenticationActivity의 끝을 처리하도록 이 코드 블록을 복사합니다.

        @Override
         protected void onActivityResult(int requestCode, int resultCode, Intent data) {
             super.onActivityResult(requestCode, resultCode, data);
             if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
             }
         }

6. 토큰을 요청하려면 콜백을 정의합니다.

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };

7. 마지막으로 해당 콜백을 사용하여 토큰을 요청합니다.

    `mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                   callback);`

매개 변수에 대한 설명은 다음과 같습니다.

* *resource*는 필수 항목이며 액세스하려는 리소스를 나타냅니다.
* *clientid*는 필수 항목이며 Azure AD에서 제공됩니다.
* *RedirectUri*는 acquireToken 호출의 경우에는 제공하지 않아도 됩니다. 패키지 이름으로 설정될 수 있습니다.
* *PromptBehavior*는 자격 증명을 요청하여 캐시 및 쿠키를 건너뛰는 데 도움이 됩니다.
* 권한 부여 코드가 토큰과 교환되면 *callback*이 호출됩니다. 여기에는 액세스 토큰, 만료 날짜 및 ID 토큰 정보를 포함하는 AuthenticationResult의 개체가 생성됩니다.
* *acquireTokenSilent*는 선택 사항입니다. 호출하여 캐싱 및 토큰 새로 고침을 처리할 수 있습니다. 동기화 버전도 제공됩니다. *userId*가 매개 변수로 수락됩니다.

        mContext.acquireTokenSilent(resource, clientid, userId, callback );

이 연습 과정을 사용하여 Azure Active Directory에 성공적으로 통합하는 데 필요한 기술과 지식을 얻을 수 있습니다. 이 작업의 추가 예제를 보려면 GitHub의 AzureADSamples/ 리포지토리를 방문하세요.

## <a name="important-information"></a>중요 정보

### <a name="broker"></a>Broker
Intune 회사 포털 또는 Microsoft Authenticator 앱은 broker 구성 요소를 제공합니다. AccountManager에서 계정이 생성됩니다. 계정 형식은 "com.microsoft.workaccount"입니다. AccountManager에서는 단일 SSO 계정만 허용됩니다. 앱 중 하나에 대한 장치 챌린지를 완료한 후에 이 사용자에 대한 SSO 쿠키를 만듭니다.

broker를 사용하여 구성하는 방법에 대한 자세한 내용은 [broker wiki 문서](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Broker)를 확인하세요. 

### <a name="authority-url-and-ad-fs"></a>기관 URL 및 AD FS
AD FS(Active Directory Federation Services)는 프로덕션 STS로 인식되지 않으므로 인스턴스 검색을 해제하고 AuthenticationContext 생성자에 false를 전달해야 합니다.

기관 URL에는 STS 인스턴스 및 [테넌트 이름](https://login.microsoftonline.com/yourtenant.onmicrosoft.com)이 필요합니다.

### <a name="querying-cache-items"></a>캐시 항목 쿼리
ADAL은 일부 간단한 캐시 쿼리 함수를 사용하여 SharedPreferences에서 기본 캐시를 제공합니다. 다음을 사용하여 AuthenticationContext에서 현재 캐시를 가져올 수 있습니다.

    ITokenCacheStore cache = mContext.getCache();

사용자 지정하려는 경우 캐시 구현을 제공할 수도 있습니다.

    mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);

### <a name="prompt-behavior"></a>프롬프트 동작
ADAL은 프롬프트 동작을 지정하기 위한 옵션을 제공합니다. 새로 고침 토큰이 올바르지 않고 사용자 자격 증명이 필요한 경우 PromptBehavior.Auto가 UI를 표시합니다. PromptBehavior.Always는 캐시 사용을 건너뛰고 항상 UI를 표시합니다.

### <a name="silent-token-request-from-cache-and-refresh"></a>캐시 및 새로 고침의 자동 토큰 요청
자동 토큰 요청은 UI 팝업을 사용하지 않으며 작업이 필요하지 않습니다. 사용 가능한 경우 캐시에서 토큰을 반환합니다. 토큰이 만료되면 이 메서드는 새로 고침을 시도합니다. 새로 고침 토큰이 만료되거나 실패하면 AuthenticationException이 반환됩니다.

    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );

이 메서드를 사용하여 동기화를 호출할 수도 있습니다. callback에 대해 null을 설정하거나 acquireTokenSilentSync를 사용할 수 있습니다.

### <a name="diagnostics"></a>진단
다음은 문제를 진단하기 위한 정보의 기본 출처입니다.

* 예외
* 로그
* 네트워크 추적

라이브러리에 포함된 상관 관계 ID가 진단에 핵심적이라는 점도 알아두세요. ADAL을 코드의 다른 작업과 상호 연관 지으려면 요청별로 상관 관계 ID를 설정할 수 있습니다. 상관 관계 ID를 설정하지 않으면, ADAL에서 임의의 항목을 생성합니다. 모든 로그 메시지 및 네트워크 호출은 상관 관계 ID로 스탬프 지정됩니다. 자체 생성된 ID는 요청마다 변경됩니다.

#### <a name="errors--exceptions"></a>오류 및 예외
예외는 첫 번째 진단입니다. 저희는 유용한 오류 메시지를 제공하기 위해 노력하고 있습니다. 유용하지 않은 오류 메시지가 표시되면 문제를 정리한 후 알려주세요. 모델 및 SDK 번호와 같은 장치 정보도 포함합니다.

앱에서 처리해야 하는 오류에 대한 자세한 내용은 [오류 처리 모범 사례](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-adal-error-handling)를 확인하세요. 

#### <a name="logs"></a>로그
문제를 진단하는 데 도움을 주기 위해 사용할 수 있는 로그 메시지를 생성하도록 라이브러리를 구성할 수 있습니다. 다음을 호출하여 ADAL에서 생성되는 각 로그 메시지를 전달하는 데 사용하는 콜백을 구성하도록 로깅을 구성합니다.

로깅을 설정하려면 [로깅 wiki 문서](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Logging)를 확인하세요.

### <a name="session-cookies-in-webview"></a>WebView의 세션 쿠키
앱이 닫힌 후에 Android WebView가 세션 쿠키를 지우지 않습니다. 다음 샘플 코드를 사용하여 이 문제를 처리할 수 있습니다.

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

쿠키에 대한 자세한 내용은 [Android 사이트에서 CookieSyncManager 정보](http://developer.android.com/reference/android/webkit/CookieSyncManager.html)를 참조하세요.

### <a name="ntlm-dialog-box"></a>NTLM 대화 상자
ADAL 버전 1.1.0은 WebViewClient의 onReceivedHttpAuthRequest 이벤트를 통해 처리되는 NTLM 대화 상자를 지원합니다. 대화 상자에 대한 레이아웃 및 문자열을 사용자 지정할 수 있습니다.

### <a name="cross-app-sso"></a>앱 간 SSO
[ADAL을 사용하여 Android에서 앱 간 SSO를 사용하도록 설정하는 방법](active-directory-sso-android.md)을 알아봅니다.  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

---
title: Azure AD v2 Android 빠른 시작 | Microsoft Docs
description: Android 애플리케이션이 Azure Active Directory v2.0 엔드포인트로 보호되는 액세스 토큰을 필요로 하는 API를 호출할 수 있는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd78e6acd801f3b973cc45609b72f86b257f4d43
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862763"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>빠른 시작: Android 앱에서 사용자 로그인 및 Microsoft Graph API 호출

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

이 빠른 시작에는 Android 애플리케이션이 개인 또는 회사 및 학교 계정에 로그인하고, 액세스 토큰을 가져오고, Microsoft Graph API를 호출할 수 있는 방법을 보여 주는 코드 샘플이 포함되어 있습니다.

![이 빠른 시작에서 생성된 샘플 앱의 작동 방식 표시](media/quickstart-v2-android/android-intro-updated.png)

> [!NOTE]
> **필수 조건**
> * Android Studio 3 이상
> * Android 21 이상 필요 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>빠른 시작 앱 등록 및 다운로드
> 빠른 시작 애플리케이션을 시작하는 옵션은 두 가지가 있습니다.
> * [기본] [옵션 1: 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [수동] [옵션 2: 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>옵션 1: 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
> 앱을 등록하려면
> 1. [Azure Portal - 애플리케이션 등록(미리 보기)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs)으로 이동합니다.
> 1. 응용 프로그램 이름을 입력하고 **등록**을 선택합니다.
> 1. 지침에 따라 클릭 한 번으로 새 애플리케이션을 다운로드하고 자동으로 구성합니다.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>옵션 2: 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성
>
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
> 애플리케이션을 등록하고 앱의 등록 정보를 솔루션에 수동으로 추가하려면 다음 단계를 따르세요.
>
> 1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
> 1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
> 1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록(미리 보기)** > **새 등록**을 차례로 선택합니다.
> 1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
>      - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `Android-Quickstart`)을 입력합니다.
>      - `Register` 단추를 누릅니다.
> 1. `Authentication` > `Redirect URIs` > `Suggested Redirect URIs for public clients`로 이동하고 **msal{AppId}://auth** 형식의 리디렉션 URI를 선택합니다. 변경 내용을 저장합니다.


> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>1단계: 애플리케이션 구성
> 이 빠른 시작의 코드 샘플이 작동하려면 회신 URL을 **msal{AppId}://auth**로 추가해야 합니다. 여기서 {AppId}는 앱의 애플리케이션 ID입니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [자동 변경]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-android/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-project"></a>2단계: 프로젝트를 다운로드합니다.

* [Android Studio 프로젝트 다운로드](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>3단계: 프로젝트 구성

> [!div renderon="docs"]
> 위의 옵션 1을 선택한 경우 이러한 단계를 건너뛸 수 있습니다. Android Studio에서 프로젝트를 열고 앱을 실행합니다. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Android Studio에서 프로젝트를 추출하여 엽니다.
> 1. **app** > **res** > **raw** 내에서 **auth_config.json**을 엽니다.
> 1. **auth_config.json**을 편집하고 `client_id` 및 `tenant_id`를 바꿉니다.
>    ```javascript
>    "client_id" : "Enter_the_Application_Id_Here",
>    "type": "Enter_the_Audience_Info_Here",
>    "tenant_id" : "Enter_the_Tenant_Info_Here"
>    ```
> 1. **app** > **manifests** 내에서 **AndroidManifest.xml**을 엽니다.
> 1. 다음 작업을 **manifest\application** 노드에 추가합니다. 이 코드는 Microsoft가 앱에 콜백할 수 있게 해줍니다.   
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msalEnter_The_Application_Id_Here"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```

> [!div renderon="docs"]
> 1. Android Studio에서 프로젝트를 추출하여 엽니다.
> 1. **app** > **res** > **raw** 내에서 **auth_config.json**을 엽니다.
> 1. **auth_config.json**을 편집하고 `client_id` 및 `redirect_uri`를 바꿉니다.
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. **app** > **manifests** 내에서 **AndroidManifest.xml**을 엽니다.
> 1. 다음 작업을 **manifest\application** 노드에 추가합니다. 이 코드 조각은 OS에서 인증 완료 후 애플리케이션을 다시 시작할 수 있도록 **BrowserTabActivity**를 등록합니다.
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msal<ENTER_YOUR_APPLICATION_ID>"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```
> 1. `<ENTER_THE_APPLICATION_ID_HERE>`를 애플리케이션에 대한 *애플리케이션 ID*로 바꿉니다. *애플리케이션 ID*를 찾아야 하는 경우, *개요* 페이지로 이동하세요.

## <a name="more-information"></a>추가 정보

이 빠른 시작에 대한 자세한 내용은 다음 섹션을 읽어보세요.

### <a name="msal"></a>MSAL

MSAL([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal))은 사용자가 로그인하고 Microsoft Azure AD(Azure Active Directory)에 의해 보호되는 API에 액세스하기 위해 사용하는 토큰을 요청하는 데 사용되는 라이브러리입니다. Gradle을 사용하면 **종속성** 아래의 **Gradle 스크립트** > **build.gradle(모듈: 앱)** 에 다음을 추가하여 설치할 수 있습니다.

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.2.+'
```

### <a name="msal-initialization"></a>MSAL 초기화

다음 코드를 추가하여 MSAL에 대한 참조를 추가할 수 있습니다.

```java
import com.microsoft.identity.client.*;
```

그런 다음, 아래 코드를 사용하여 MSAL을 초기화합니다.

```java
    sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);
```

> |위치: ||
> |---------|---------|
> |`R.raw.auth_config` | 이 파일에는 앱/클라이언트 ID, 로그인 대상 그룹 및 여러 다른 사용자 지정 옵션을 포함한 애플리케이션 구성이 포함됩니다. |

### <a name="requesting-tokens"></a>토큰 요청

MSAL에는 토큰 가져오기에 사용되는 두 가지 메서드인 `acquireToken`이 있으며 `acquireTokenSilentAsync`

#### <a name="getting-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기

일부 상황에서는 사용자가 Azure AD v2.0 엔드포인트를 조작해야 하며, 이로 인해 사용자 자격 증명의 유효성을 검사하거나 동의를 얻기 위해 시스템 브라우저로 컨텍스트가 전환됩니다. 일부 사례:

* 처음으로 사용자가 응용 프로그램에 로그인한 경우
* 암호가 만료되어 사용자가 자격 증명을 다시 입력해야 할 경우
* 애플리케이션이 사용자 동의가 필요한 리소스에 액세스를 요청하고 있는 경우
* 2단계 인증이 필요한 경우

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |위치:||
> |---------|---------|
> | `SCOPES` | 요청된 범위(즉, Microsoft Graph의 경우 `{ "user.read" }`, 사용자 지정 Web API(즉, `api://<Application ID>/access_as_user`)의 경우 `{ "<Application ID URL>/scope" }`)를 포함합니다. |
> | `getAuthInteractiveCallback` | 인증 후 애플리케이션으로 컨트롤을 다시 줄 때 실행되는 콜백 |

#### <a name="getting-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기

사용자가 리소스에 액세스해야 할 때마다 자격 증명의 유효성을 검사할 필요가 없도록 하려고 합니다. 대부분은 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리하려고 합니다. 초기 `acquireToken` 메서드 다음에 `AcquireTokenSilentAsync` 메서드를 사용하여 토큰을 가져오면 보호된 리소스에 액세스할 수 있습니다.

```java
List<IAccount> accounts = sampleApp.getAccounts();
if (sample.size() == 1) {
    sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
} else {
    // No or multiple accounts
}
```

> |위치:||
> |---------|---------|
> | `SCOPES` | 요청된 범위(즉, Microsoft Graph의 경우 `{ "user.read" }`, 사용자 지정 Web API(즉, `api://<Application ID>/access_as_user`)의 경우 `{ "<Application ID URL>/scope" }`)를 포함합니다. |
> | `accounts.get(0)` | 자동으로 토큰을 가져오려는 계정이 포함됩니다. |
> | `getAuthInteractiveCallback` | 인증 후 애플리케이션으로 컨트롤을 다시 줄 때 실행되는 콜백 |

## <a name="next-steps"></a>다음 단계

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>이 빠른 시작에 사용되는 애플리케이션을 만드는 단계 알아보기

이 빠른 시작의 전체 설명을 포함하여 애플리케이션 및 새로운 기능 빌드에 대한 완전한 단계별 가이드를 제공하는 Android 자습서를 사용해 보세요.

> [!div class="nextstepaction"]
> [Graph API 호출 Android 자습서](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Android용 MSAL 라이브러리 wiki

Android용 MSAL 라이브러리에 대한 자세한 정보를 읽어보세요.

> [!div class="nextstepaction"]
> [Android용 MSAL 라이브러리 wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

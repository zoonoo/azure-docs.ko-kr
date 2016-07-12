<properties
	pageTitle="Azure AD v2.0 Android 앱 | Microsoft Azure"
	description="개인 Microsoft 계정과 회사 또는 학교 계정이 있는 사용자로 로그인하고 타사 라이브러리를 사용하여 Graph API를 호출하는 Android 앱을 빌드하는 방법입니다."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="brandwe"/>

#  v2.0 끝점을 사용하는 Graph API와 함께 타사 라이브러리를 사용하여 Android 앱에 로그인 추가

Microsoft ID 플랫폼은 OAuth2 및 OpenID Connect와 같은 개방형 표준을 사용합니다. 이를 통해 개발자는 서비스와 통합하려는 모든 라이브러리를 활용할 수 있습니다. 개발자가 플랫폼을 다른 라이브러리와 함께 사용할 수 있도록 돕기 위해, 타사 라이브러리를 Microsoft ID 플랫폼에 연결하도록 구성하는 방법을 설명하는 이와 같은 연습 몇 가지를 작성했습니다. [RFC6749 OAuth2 사양](https://tools.ietf.org/html/rfc6749)을 구현하는 대부분의 라이브러리는 Microsoft ID 플랫폼에 연결할 수 있습니다.

이 응용 프로그램을 사용하여 사용자는 해당 조직에 로그인한 다음 Graph API를 사용하여 조직 내에서 직접 검색할 수 있습니다.

OAuth2 또는 OpenID Connect를 처음 접하는 경우 이 샘플 구성 대부분이 잘 이해되지 않을 수도 있습니다. [여기서 추천한 프로토콜에 관한 개요](active-directory-v2-protocols-oauth-code.md)를 간략히 살펴볼 것을 추천합니다.


> [AZURE.NOTE]
	조건부 액세스 및 Intune 정책 관리 등과 같은 이런 표준의 식을 사용하는 플랫폼의 일부 기능은 수행하려면 오픈 소스인 Microsoft Azure ID 라이브러리를 사용해야 합니다.


> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.


## 다운로드
이 자습서에 대한 코드는 [GitHub](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git)에서 유지 관리됩니다. 자습서에 따라 [.zip으로 앱 구조를 다운로드](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git/archive/skeleton.zip)하거나 구조를 복제할 수 있습니다.

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

또는 그냥 다운로드하여 바로 시작합니다.

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## 앱 등록
[apps.dev.microsoft.com](https://apps.dev.microsoft.com)에서 새 앱을 만들거나 다음 [자세한 단계](active-directory-v2-app-registration.md)를 따르십시오. 다음을 수행해야 합니다.

- 곧 필요하게 되므로 앱에 할당된 **응용 프로그램 ID**를 적어둡니다.
- 앱용 **Mobile** 플랫폼을 추가합니다.
- 포털에서 **Redirect URI**를 복사합니다. `https://login.microsoftonline.com/common/oauth2/nativeclient`의 기본값을 사용해야 합니다.


## 타사 라이브러리 nxoauth2를 다운로드하고 작업 영역을 실행합니다.

이 연습에서는 Google의 OpenID Connect 코드를 기반으로 하는 OAuth2 라이브러리인 GitHub에서 OIDCAndroidLib을 사용합니다. 네이티브 응용 프로그램 프로필을 구현하고 최종 사용자 권한 부여 끝점을 지원합니다. Microsoft ID 플랫폼과 통합하기 위해 필요한 것은 이것들이 전부입니다.

*  복제

사용자 컴퓨터에 OIDCAndroidLib 리포지토리를 복제하여 시작합니다.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

### Android Studio 환경 설정

*  프로젝트 만들기 새 AndroidStudio 프로젝트를 만들고 기본 마법사를 따라합니다.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

*  프로젝트 모듈 설정 모듈을 설정하는 가장 쉬운 방법은 복제된 리포지토리를 프로젝트 위치로 옮기는 것입니다. 또한 프로젝트를 만든 다음 프로젝트 위치로 직접 복제하여 시작할 수 있습니다.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

다음으로 상황에 맞는 메뉴나 바로 가기 `Ctrl + Alt + Maj + S`을 사용하여 프로젝트 모듈 설정을 엽니다.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

프로젝트 컨테이너 설정 만을 원한다면 기본 앱 모듈을 제거합니다.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

이제 모듈을 복제 리포지토리에서 현재 프로젝트로 가져오기해야 합니다.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

> `oidlib-sample` 모듈에 대해 이들 단계를 반복합니다.

`oidlib-sample` 모듈에서 Oidclib 종속성을 확인합니다.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

"확인"을 클릭하고 Gradle 동기화를 대기합니다.

설정은 gradle처럼 보여야 합니다.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

*  샘플 앱을 빌드하여 샘플이 제대로 실행되는지 확인하십시오.

아직은 이것을 Azure Active Directory와 함께 사용할 수 없습니다. 먼저 일부 끝점을 구성해야 합니다. 이는 샘플 앱을 사용자에 맞게 설정하기 전에 Android Studio 문제가 없도록 하기 위해서입니다.

`oidlib-sample`을 Android Studio의 대상으로 빌드하고 실행합니다.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

*  정리

프로젝트에서 모듈을 제거할 때 AndroidStudio가 안전을 위해 삭제하지 않아 남은 `app ` 디렉터리를 안전하게 삭제할 수 있습니다.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

또한 "구성 편집" 메뉴를 열어 프로젝트에서 모듈을 제거할 때 또한 남겨진 실행 구성을 제거할 수 있습니다.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## 샘플의 끝점 구성

이제 `oidlib-sample`이 성공적으로 실행됨으로 Azure Active Directory와 작동되도록 일부 끝점을 편집합니다.

* 클라이언트 구성

`oidc_clientconf.xml` 파일을 열어 다음과 같이 변경합니다.

1. 토큰을 가져오고 Graph API를 호출하기 위해 OAuth2 흐름 만을 사용하기 때문에 OAuth2만 수행하도록 클라이언트를 설정해 보겠습니다. OIDC 사용은 향후 예에서 이루어질 것입니다.

```xml
    <bool name="oidc_oauth2only">true</bool>
```

2. 등록 포털에서 받은 클라이언트 ID를 구성 합니다.

```xml
    <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
    <string name="oidc_clientSecret"></string>
```

3. 등록 포털에서 받은 URI 리디렉션을 구성합니다.

```xml
    <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
```

4. Graph API에 액세스하기 위해 필요한 범위를 구성합니다.

```xml
    <string-array name="oidc_scopes">
        <item>openid</item>
        <item>User.ReadBasic.All</item>
        <item>offline_access</item>
    </string-array>
```

여기서 `oidc_scopes`을 설정합니다. 이 응용 프로그램에 대해 요청할 범위는 디렉토리에서 모든 사용자의 기본 프로필을 읽을 수 있도록 허용하는 `User.ReadBasic.All`입니다. [여기서 Microsoft Graph와 함께 사용](https://graph.microsoft.io/docs/authorization/permission_scopes) 가능한 모든 범위에 대해 더 자세히 알아 볼 수 있습니다.

OpenID Connect의 범위로써의 `openid` 또는 `offline_access`의 설명을 좋아한다면 [여기서 추천한 프로토콜에 관한 간단한 개요](active-directory-v2-protocols-oauth-code.md)를 살펴보도록 합니다.

* 클라이언트 컴퓨터 구성

`oidc_endpoints.xml` 파일을 열어 다음과 같이 변경합니다.

```xml
<!-- Stores OpenID Connect provider endpoints. -->
<resources>
    <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
    <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
    <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
    <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
</resources>
```

OAuth2를 프로토콜로 사용하는 경우 이러한 끝점은 변경되어서는 안됩니다.

> [AZURE.NOTE] 
`userInfoEndpoint`과 `revocationEndpoint`에 대한 끝점은 Azure Active Directory에서 현재 지원되지 않으므로 샘플에서 사용 가능하지 않다는 유용한 미리 알림을 제공하는 example.com의 기본 값으로 그대로 둡니다.


## Graph API 호출 구성

`HomeActivity.java` 파일을 열어 다음과 같이 변경합니다.

```Java
   //TODO: set your protected resource url
    private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
```

여기서 정보를 반환할 간단한 Graph API 호출을 실행합니다.

## 완료되었습니다!

수행해야 할 변경은 그것이 전부입니다! 응용 프로그램 `oidlib-sample`을 실행하고 로그인을 클릭합니다. O

성공적으로 인증했으므로 Graph API로 호출을 테스트하려면 "보호된 리소스 요청" 단추를 누릅니다.

## 당사 제품에 대한 보안 업데이트 가져오기

[이 페이지](https://technet.microsoft.com/security/dd252948)를 방문해 보안 공지 경고를 구독하여 보안 사건이 발생할 때 알림을 받는 것이 좋습니다.

<!---HONumber=AcomDC_0629_2016-->
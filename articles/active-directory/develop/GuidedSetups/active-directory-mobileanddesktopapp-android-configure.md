---
title: "Azure AD v2 Android 시작 - 구성 | Microsoft Docs"
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
ms.openlocfilehash: 945b09ccdb7537987da33d32d94a3ccacd829ffd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
## <a name="create-an-application-express"></a>응용 프로그램(Express) 만들기
이제 *Microsoft 응용 프로그램 등록 포털*에서 등용 프로그램을 등록해야 합니다.
1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)을 통해 응용 프로그램을 등록합니다.
2.  응용 프로그램 이름과 메일을 입력합니다.
3.  안내식 설정 옵션이 선택되어 있는지 확인합니다.
4.  지침에 따라 응용 프로그램 ID를 가져와 코드에 붙여넣습니다.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>솔루션에 응용 프로그램 등록 정보 추가(고급)
이제 *Microsoft 응용 프로그램 등록 포털*에서 등용 프로그램을 등록해야 합니다.
1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동해 응용 프로그램을 등록합니다.
2. 응용 프로그램 이름과 메일을 입력합니다. 
3. 안내식 설정 옵션이 선택 취소되어 있는지 확인합니다.
4. `Add Platform`를 클릭한 다음 `Native Application`을 선택하고 [저장]을 누릅니다.
5.  (`app` > `java` > *`{host}.{namespace}`*에서) `MainActivity`를 엽니다.
6.  `final static String CLIENT_ID`로 시작하는 줄에서 *[여기에 응용 프로그램 ID 입력]*을 방금 등록한 응용 프로그램 ID로 바꿉니다.

```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
(`app` > `manifests`에서) `AndroidManifest.xml`을 열고 `manifest\application` 노드에 다음 작업을 추가합니다. 이렇게 하면 OS에서 인증 완료 후 응용 프로그램을 다시 시작할 수 있도록 `BrowserTabActivity`가 등록됩니다.
</li>
</ol>

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        
        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```
<!-- Workaround for Docs conversion bug -->
<ol start="8">
<li>
`BrowserTabActivity`에서 `[Enter the application Id here]`를 응용 프로그램 ID로 바꿉니다.
</li>
</ol>

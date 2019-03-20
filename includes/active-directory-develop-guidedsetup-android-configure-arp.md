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
ms.openlocfilehash: b8f961ad3fe4550b915253746d0f4f677c593a8c
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58214489"
---
## <a name="add-the-applications-registration-to-your-code"></a>코드에 애플리케이션 등록 추가

이 단계에서는 프로젝트에 애플리케이션/클라이언트 ID를 추가해야 합니다.

1. (`app` > `java` > *`{host}.{namespace}`* 에서) `MainActivity`를 엽니다.
2. `final static String CLIENT_ID`로 시작하는 줄을 다음으로 바꿉니다.
   ```java
   final static String CLIENT_ID = "[Enter the application Id here]";
   ```
3. 다음을 엽니다. `app` > `manifests` > `AndroidManifest.xml`
4. 다음 작업을 `manifest\application`에 추가합니다. `BrowserTabActivity`는 Microsoft에서 인증을 완료한 후 애플리케이션을 콜백할 수 있도록 합니다.

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

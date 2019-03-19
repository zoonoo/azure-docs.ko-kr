---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: da4944cc95750505e0099b7a987b78e132910460
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125357"
---
## <a name="register-your-application"></a>애플리케이션 등록

다음 두 섹션에 설명된 대로 두 가지 방법 중 하나로 애플리케이션을 등록할 수 있습니다.

### <a name="option-1-express"></a>옵션 1: Express

1. [Microsoft 애플리케이션 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)로 이동합니다.
2. **애플리케이션 이름**에 애플리케이션 이름을 입력합니다.
3. **단계별 설치** 확인란을 선택한 다음 **만들기**를 선택하도록 합니다.
4. 애플리케이션 ID를 가져오는 지침에 따라 코드에 붙여넣습니다.

### <a name="option-2-advanced"></a>옵션 2: 고급

1. [Microsoft 애플리케이션 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동합니다.
2. **애플리케이션 이름** 상자에서 애플리케이션의 이름을 입력합니다.
3. **단계별 설치** 확인란의 선택을 취소한 다음 **만들기**를 선택하도록 합니다.
4. **플랫폼 추가**를 선택하고, **네이티브 애플리케이션**을 선택한 다음, **저장**을 선택합니다.
5. **앱** > **java** > **{host}.{namespace}** 아래에서 `MainActivity`를 엽니다. 
6. *[여기에 애플리케이션 ID 입력]* 을 해당 애플리케이션/클라이언트 ID로 바꿉니다.

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
   <!-- Workaround for Docs conversion bug -->
7. **앱** > **매니페스트** 아래에서 *AndroidManifest.xml* 파일을 엽니다.
8. `manifest\application`에서 다음 작업을 추가합니다. Microsoft에서 인증을 완료한 후 애플리케이션을 콜백할 수 있도록 하는 `BrowserTabActivity` 작업:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
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
9. `BrowserTabActivity`에서 `[Enter the application Id here]`를 애플리케이션/클라이언트 ID로 바꿉니다.

---
title: "Azure AD v2 Android 시작 - 설정 | Microsoft Docs"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8670da0e22dac101817e970b6dc8e2032ee5de62
ms.contentlocale: ko-kr


---

## <a name="set-up-your-project"></a>프로젝트 설정

> 이 샘플의 Android Studio 프로젝트를 다운로드하고 싶으세요? [프로젝트를 다운로드](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip) 하면 실행 전 코드 샘플을 구성하는 [구성](#create-an-application-express) 단계를 건너뛸 수 있습니다.


### <a name="create-a-new-project"></a>새 프로젝트 만들기 
1.    Android Studio를 열고 `File` > `New` > `New Project`로 이동합니다.
2.    응용 프로그램의 이름을 지정하고 `Next`를 클릭합니다.
3.    *API 21 이상(Android 5.0)*이 선택되어 있는지 확인하고 `Next`를 클릭합니다.
4.    `Empty Activity`는 그대로 두고 `Next`을 클릭한 후 `Finish`를 클릭합니다.


### <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>프로젝트에 MSAL(Microsoft 인증 라이브러리) 추가
1.    Android Studio에서 `Gradle Scripts` > `build.gradle (Module: app)`으로 이동합니다.
2.    `Dependencies`아래에 다음 코드를 복사하여 붙여넣습니다.

```ruby  
compile ('com.microsoft.identity.client:msal:0.1.+') {
    exclude group: 'com.android.support', module: 'appcompat-v7'
}
compile 'com.android.volley:volley:1.0.0'
```

<!--start-collapse-->
### <a name="about-this-package"></a>이 패키지 정보

위의 패키지는 MSAL(Microsoft 인증 라이브러리)을 설치합니다. MSAL은 Azure Active Directory v2 끝점으로 보호되는 API에 액세스하는 데 사용되는 사용자 토큰의 획득, 캐싱 및 새로 고침을 처리합니다.
<!--end-collapse-->

## <a name="create-your-applications-ui"></a>응용 프로그램 UI 만들기

1.    `res` > `layout`에서 `activity_main.xml`을 엽니다.
2.    `android.support.constraint.ConstraintLayout` 등에서 `LinearLayout`으로 작업 레이아웃을 변경합니다.
3.    `LinearLayout` 노드에 `android:orientation="vertical"` 속성을 추가합니다.
4.    다음 코드를 복사하여 `LinearLayout` 노드에 붙여넣어 현재 내용을 바꿉니다.

```xml
<TextView
    android:text="Welcome, "
    android:textColor="#3f3f3f"
    android:textSize="50px"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="10dp"
    android:layout_marginTop="15dp"
    android:id="@+id/welcome"
    android:visibility="invisible"/>

<Button
    android:id="@+id/callGraph"
    android:text="Call Microsoft Graph"
    android:textColor="#FFFFFF"
    android:background="#00a1f1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="200dp"
    android:textAllCaps="false" />

<TextView
    android:text="Getting Graph Data..."
    android:textColor="#3f3f3f"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginLeft="5dp"
    android:id="@+id/graphData"
    android:visibility="invisible"/>

<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="0dip"
    android:layout_weight="1"
    android:gravity="center|bottom"
    android:orientation="vertical" >

    <Button
        android:text="Sign Out"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="15dp"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:textAllCaps="false"
        android:id="@+id/clearCache"
        android:visibility="invisible" />
</LinearLayout>    <TextView
    android:text="Welcome, "
    android:textColor="#3f3f3f"
    android:textSize="50px"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="10dp"
    android:layout_marginTop="15dp"
    android:id="@+id/welcome"
    android:visibility="invisible"/>

<Button
    android:id="@+id/callGraph"
    android:text="Call Microsoft Graph"
    android:textColor="#FFFFFF"
    android:background="#00a1f1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="200dp"
    android:textAllCaps="false" />

<TextView
    android:text="Getting Graph Data..."
    android:textColor="#3f3f3f"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginLeft="5dp"
    android:id="@+id/graphData"
    android:visibility="invisible"/>

<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="0dip"
    android:layout_weight="1"
    android:gravity="center|bottom"
    android:orientation="vertical" >

    <Button
        android:text="Sign Out"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="15dp"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:textAllCaps="false"
        android:id="@+id/clearCache"
        android:visibility="invisible" />
</LinearLayout>
```



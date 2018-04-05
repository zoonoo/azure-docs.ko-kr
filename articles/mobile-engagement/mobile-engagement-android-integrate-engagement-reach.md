---
title: Azure Mobile Engagement Android SDK 통합
description: Azure Mobile Engagement용 Android SDK의 최신 업데이트 및 절차
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 9ec3fab3-35ec-458e-bf41-6cdd69e3fa44
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 06/27/2016
ms.author: piyushjo
ms.openlocfilehash: 15e71d8aeb8c8060161ade4262e0be0a093b5650
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-reach-on-android"></a>Android에서 Engagement 도달률을 통합하는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!IMPORTANT]
> 이 가이드를 수행하기 전에 Android 문서의 Engagement를 통합하는 방법에 설명된 통합 절차를 따라야 합니다.
> 
> 

## <a name="standard-integration"></a>표준 통합

프로젝트의 SDK에서 도달률 리소스 파일을 다음과 같이 복사합니다.

* SDK와 함께 제공된 `res/layout` 폴더의 파일을 응용 프로그램의 `res/layout` 폴더로 복사합니다.
* SDK와 함께 제공된 `res/drawable` 폴더의 파일을 응용 프로그램의 `res/drawable` 폴더로 복사합니다.

`AndroidManifest.xml` 파일을 다음과 같이 편집합니다.

* `<application>` 태그와 `</application>` 태그 사이에 다음 섹션을 추가합니다.
  
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/plain" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/html" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
              <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
              <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
          </activity>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
              <action android:name="android.intent.action.BOOT_COMPLETED"/>
              <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
              <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
          </receiver>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
              <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
          </receiver>
* 부팅할 때 클릭하지 않은 시스템 알림을 재생하려면 이 권한이 있어야 합니다. 그렇지 않으면 시스템 알림이 디스크에서 유지되지만 더 이상 표시되지 않습니다. 따라서 실제로 이 권한을 포함해야 합니다.
  
          <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
* 다음 섹션(`<application>` 태그와 `</application>` 태그 사이)을 복사 및 편집하여 알림(앱과 시스템 둘 다의 알림)에 사용된 아이콘을 지정합니다.
  
          <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [!IMPORTANT]
> 도달률 캠페인을 만들 때 시스템 알림을 사용하려는 경우 이 섹션은 **필수** 입니다. Android는 아이콘이 없는 시스템 알림이 표시되지 않도록 방지합니다. 따라서 이 섹션을 누락하는 경우 최종 사용자가 해당 시스템 알림을 받을 수 없게 됩니다.
> 
> 

* 큰 그림을 사용하는 시스템 알림이 있는 캠페인을 만드는 경우 다음 권한을 `</application>` 태그 뒤에 추가해야 합니다(누락된 경우).
  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
          <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
  
  * Android M에서 응용 프로그램이 Android API level 23 이상을 대상으로 하는 경우 ``WRITE_EXTERNAL_STORAGE`` 권한에 사용자 승인이 필요합니다. [이 섹션](mobile-engagement-android-integrate-engagement.md#android-m-permissions)을 읽어보세요.
* 또한 시스템 알림을 위해 장치에서 신호음이 울리거나 진동이 작동해야 하는 경우 도달률 캠페인에서 지정할 수도 있습니다. 신호음이나 진동이 작동하도록 하려면 `</application>` 태그 뒤에 다음 권한을 선언해야 합니다.
  
          <uses-permission android:name="android.permission.VIBRATE" />
  
  도달률 캠페인 관리자에서 신호음 또는 진동 옵션을 선택한 경우 이 권한이 없으면 Android에서 시스템 알림이 표시되지 않도록 방지합니다.

## <a name="native-push"></a>네이티브 푸시
이제 도달률 모듈을 구성했으므로 장치에서 캠페인을 받을 수 있도록 네이티브 푸시를 구성해야 합니다.

Android에서는 다음 두 가지 서비스를 지원합니다.

* Google Play 장치: [GCM과 Engagement 통합 방법](mobile-engagement-android-gcm-integrate.md) 가이드에 따라 [Google Cloud Messaging] 사용
* Amazon 장치: [ADM과 Engagement 통합 방법 가이드](mobile-engagement-android-adm-integrate.md)에 따라 [Amazon Device Messaging] 사용

Amazon 및 Google Play 장치를 모두 대상으로 하려는 경우 개발을 위한 단일 AndroidManifest.xml/APK 내에 모든 것을 포함할 수 있습니다. 하지만 Amazon에 제출할 경우 GCM 코드가 발견되면 응용 프로그램이 거부될 수 있습니다.

이러한 경우에는 여러 APK를 사용해야 합니다.

**이제 응용 프로그램이 도달률 캠페인을 수신하여 표시할 준비가 되었습니다!**

## <a name="how-to-handle-data-push"></a>데이터 푸시를 처리하는 방법
### <a name="integration"></a>통합
응용 프로그램을 통해 도달률 데이터 푸시를 받으려면 다음과 같이 `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver`의 하위 클래스를 생성하여 `AndroidManifest.xml` 파일에서 해당 클래스를 참조해야 합니다(`<application>` 및/또는 `</application>` 태그 사이).

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

그런 다음 `onDataPushStringReceived` 및 `onDataPushBase64Received` 콜백을 재정의할 수 있습니다. 다음은 예제입니다.

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Category
데이터 푸시 캠페인을 만들 때는 필요에 따라 범주 매개 변수를 포함할 수 있으며, 그러면 데이터 푸시를 필터링할 수 있습니다. 범주 매개 변수는 여러 유형의 데이터 푸시를 처리하는 여러 브로드캐스트 수신기가 있는 경우 또는 다른 종류의 `Base64` 데이터를 푸시하면서 구문 분석하기 전에 해당 유형을 식별하려는 경우에 유용합니다.

### <a name="callbacks-return-parameter"></a>콜백의 반환 매개 변수
다음은 `onDataPushStringReceived` 및 `onDataPushBase64Received`의 반환 매개 변수를 올바르게 처리하기 위한 몇 가지 지침입니다.

* 브로드캐스트 수신기가 데이터 푸시의 처리 방법을 모르는 경우 콜백에서 `null`을(를) 반환해야 합니다. 범주를 사용하여 브로드캐스트 수신기가 데이터 푸시를 처리해야 하는지 여부를 확인해야 합니다.
* 브로드캐스트 수신기 중 하나가 데이터 푸시를 허용하는 경우 콜백에서 `true` 을(를) 반환해야 합니다.
* 브로드캐스트 수신기 중 하나가 데이터 푸시를 인식하지만 어떤 이유로든 무시한 경우 콜백에서 `false` 을(를) 반환해야 합니다. 예를 들어 수신한 데이터가 잘못된 경우 `false` 을(를) 반환합니다.
* 하나의 브로드캐스트 수신기가 `true`을(를) 반환하는데 다른 브로드캐스트 수신기가 동일한 데이터 푸시에 대해 `false`을(를) 반환하는 경우 동작이 정의되지 않습니다. 따라서 그렇게 해서는 안됩니다.

반환 형식은 다음과 같이 도달률 통계에서만 사용됩니다.

* `Replied`은(는) 브로드캐스트 수신기 중 하나가 `true` 또는 `false`을(를) 반환한 경우에 증가됩니다.
* `Actioned`은(는) 브로드캐스트 수신기 중 하나가 `true`을(를) 반환한 경우에만 증가됩니다.

## <a name="how-to-customize-campaigns"></a>캠페인을 사용자 지정하는 방법
캠페인을 사용자 지정하기 위해 Reach SDK에서 제공하는 레이아웃을 수정할 수 있습니다.

레이아웃에 사용된 모든 식별자를 유지하고, 식별자를 사용하는 뷰의 유형(특히 텍스트 뷰 및 이미지 뷰의 유형)을 유지해야 합니다. 일부 뷰는 영역을 표시하거나 숨기는 데에만 사용되므로 해당 유형을 변경할 수 있습니다. 제공된 레이아웃의 뷰 유형을 변경하려는 경우 소스 코드를 확인하세요.

### <a name="notifications"></a>알림
두 가지 유형의 알림 즉, 다른 레이아웃 파일을 사용하는 시스템 알림과 앱 내 알림이 있습니다.

#### <a name="system-notifications"></a>시스템 알림
시스템 알림을 사용자 지정하려면 **범주**를 사용해야 합니다. [범주](#categories)로 이동할 수 있습니다.

#### <a name="in-app-notifications"></a>앱 내 알림
기본적으로 앱 내 알림은 Android 메서드 `addContentView()`덕분에 현재 작업의 사용자 인터페이스에 동적으로 추가되는 뷰입니다. 이 보기를 알림 오버레이라고 합니다. 알림 오버레이는 응용 프로그램에서 레이아웃을 수정하지 않아도 되므로 빠른 통합에 적절합니다.

알림 오버레이의 모양을 수정하려면 요구에 맞게 간단히 파일 `engagement_notification_area.xml`을(를) 수정하면 됩니다.

> [!NOTE]
> 파일 `engagement_notification_overlay.xml`은(는) 알림 오버레이를 만드는 데 사용되는 파일이며, 이 파일에는 파일`engagement_notification_area.xml`이(가) 포함되어 있습니다. 또한 요구에 맞게 사용자 지정할 수도 있습니다(예: 오버레이 내에서 알림 영역 위치 지정).
> 
> 

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>작업 레이아웃의 일부로 알림 레이아웃 포함
오버레이는 빠른 통합에 적절하지만 특수한 경우에는 불편해지거나 부작용이 있을 수 있습니다. 오버레이 시스템을 작업 수준에서 사용자 지정할 수 있으며 특수한 작업의 부작용을 쉽게 예방할 수 있습니다.

Android의 **include** 문 덕분에 기존 레이아웃에서 알림 레이아웃을 포함하도록 결정할 수 있습니다. 다음은 `ListView`을(를) 포함하는 수정된 `ListActivity` 레이아웃의 예입니다.

**Engagement 통합 이전:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Engagement 통합 이후:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

이 예제에서는 원래 레이아웃이 최상위 요소로 목록 보기를 사용하므로 부모 컨테이너를 추가했습니다. 또한 `android:layout_weight="1"`을(를) 추가했으므로 `android:layout_height="fill_parent"`(으)로 구성한 목록 보기 아래에 뷰를 추가할 수 있습니다.

Engagement Reach SDK는 알림 레이아웃이 이 작업에 포함되었으며 이 작업의 오버레이를 추가하지 않을 것임을 자동으로 검색합니다.

> [!TIP]
> 응용 프로그램에서 ListActivity를 사용하는 경우 표시되는 도달률 오버레이는 목록 보기의 클릭된 항목에 더 이상 반응하지 못하도록 방지합니다. 이는 알려진 문제입니다. 이 문제를 해결하려면 앞의 샘플에서와 같이 고유한 목록 작업 레이아웃에 알림 레이아웃을 포함하는 것이 좋습니다.
> 
> 

##### <a name="disabling-application-notification-per-activity"></a>작업별 응용 프로그램 알림 비활성화
작업에 오버레이를 추가하지 않으려는 경우와 고유 레이아웃에 알림 레이아웃을 포함하지 않은 경우 다음 예제와 같이 `meta-data` 섹션을 추가하여 `AndroidManifest.xml`에서 이 작업의 오버레이를 비활성화하도록 설정할 수 있습니다.

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a> 범주
제공된 레이아웃을 수정할 경우 모든 알림 모양을 수정하게 됩니다. 범주를 사용하면 알림의 여러 대상 모양을 정의할 수 있으며 경우에 따라서는 동작도 정의할 수 있습니다. 도달률 캠페인을 만들 때 범주를 지정할 수 있습니다. 범주를 사용하면 알림과 설문 조사도 사용자 지정할 수 있습니다. 여기에 대해서는 이 문서의 뒷부분에서 설명합니다.

알림의 범주 처리기를 등록하려면 응용 프로그램이 초기화될 때 호출을 추가해야 합니다.

> [!IMPORTANT]
> 계속하기 전에 Android에서 Engagement를 통합하는 방법 항목에서 android:process 특성 \<android-sdk-engagement-process\>에 관한 경고를 읽어보세요.
> 
> 

다음 예제에서는 이전 경고를 확인하였고 `EngagementApplication`의 하위 클래스를 사용한다고 가정합니다.

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

`MyNotifier` 개체는 알림 범주 처리기의 구현입니다. 이 개체는 `EngagementNotifier` 인터페이스의 구현이거나 기본 구현인 `EngagementDefaultNotifier`의 하위 클래스입니다.

동일한 알림으로 여러 범주를 처리할 수 있습니다. 또한 다음과 같이 해당 범주를 등록할 수 있습니다.

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

기본 범주 구현을 바꾸기 위해 다음 예와 같이 구현을 등록할 수 있습니다.

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

처리기에서 사용된 현재 범주는 `EngagementDefaultNotifier`에서 재정의할 수 있는 대다수 메서드의 매개 변수로 전달됩니다.

그 범주는`String` 매개 변수로 전달되거나 `getCategory()` 메서드가 있는 `EngagementReachContent` 개체에서 간접적으로 전달됩니다.

또한 `EngagementDefaultNotifier`에서 메서드를 다시 정의하여 알림 생성 프로세스의 대부분을 변경할 수 있습니다. 고급 사용자 지정을 더 수행하려면 기술 문서 및 소스 코드를 살펴보세요.

##### <a name="in-app-notifications"></a>앱 내 알림
특정 범주에 대체 레이아웃을 사용하려는 경우 다음 예제와 같이 구현할 수 있습니다.

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**`my_notification_overlay.xml`의 예제 :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

여기에서 알 수 있듯이 오버레이 뷰 식별자는 표준 식별자와 다릅니다. 각 레이아웃에서 오버레이에 고유 식별자를 사용하는 것이 중요합니다.

**`my_notification_area.xml`의 예제 :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

여기에서 알 수 있듯이 알림 영역 뷰 식별자는 표준 식별자와 다릅니다. 각 레이아웃에서 알림 영역에 고유 식별자를 사용하는 것이 중요합니다.

이 간단한 범주 예제는 화면 상단에 표시되는 응용 프로그램(또는 앱 내) 알림을 만듭니다. 알림 영역 자체에 사용되는 표준 식별자는 변경하지 않았습니다.

해당 식별자를 변경하려는 경우 `EngagementDefaultNotifier.prepareInAppArea` 메서드를 다시 정의해야 합니다. 이러한 고급 사용자 지정 수준을 원하는 경우 `EngagementNotifier` 및 `EngagementDefaultNotifier`의 기술 문서와 소스 코드를 살펴보는 것이 좋습니다.

##### <a name="system-notifications"></a>시스템 알림
기본 구현에서 준비한 알림을 변경하려면 `EngagementDefaultNotifier`을(를) 확장하여 `onNotificationPrepared`을(를) 재정의할 수 있습니다.

예: 

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

이 예제는 "진행 중" 범주를 사용할 때 진행 중인 이벤트로 표시되는 내용에 대한 시스템 알림을 만듭니다.

`Notification` 개체를 처음부터 빌드하려는 경우 메서드에 `false`을(를) 반환하고 `NotificationManager`에서 `notify`을(를) 직접 호출할 수 있습니다. 그런 경우에는 `contentIntent`, `deleteIntent` 및 `EngagementReachReceiver`에 사용되는 알림 식별자를 유지하는 것이 중요합니다.

다음은 그러한 구현에 대한 올바른 예제입니다.

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>공지만 알림
공지만 알림에 대한 클릭 관리는 `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared`을(를) 재정의하여 준비된 `Intent`을(를) 수정함으로써 사용자 지정할 수 있습니다. 이 메서드를 사용하면 플래그를 쉽게 조정할 수 있습니다.

다음은 `SINGLE_TOP` 플래그를 추가하는 예제입니다.

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

레거시 Engagement 사용자의 경우 실행 URL이 없는 시스템 알림은 응용 프로그램이 백그라운드에 있으면 해당 응용 프로그램을 시작하므로 이 메서드는 실행 URL이 없는 알림과 함께 호출될 수 있습니다. 의도를 사용자 지정할 때 이런 점을 고려해야 합니다.

또한 `EngagementNotifier.executeNotifAnnouncementAction` 을(를) 처음부터 구현할 수도 있습니다.

##### <a name="notification-life-cycle"></a>알림 수명 주기
기본 범주를 사용할 때는 통계를 보고하고 캠페인 상태를 업데이트하기 위해 `EngagementReachInteractiveContent` 개체에서 일부 수명 주기 메서드를 호출합니다.

* 알림이 응용 프로그램에 표시되거나 상태 표시줄에 나타나는 경우 `handleNotification`이(가) `true`을(를) 반환하면 `displayNotification` 메서드가 `EngagementReachAgent`에서 호출되어 통계를 보고합니다.
* 알림을 해제하면 `exitNotification` 메서드가 호출되고 통계가 보고되며 다음 캠페인을 처리할 수 있게 됩니다.
* 알림을 클릭하면 `actionNotification` 이(가) 호출되고 통계가 보고되며 연결된 의도가 시작됩니다.

만약 `EngagementNotifier` 구현이 기본 동작을 무시하는 경우에는 이러한 수명 주기 메서드를 직접 호출해야 합니다. 다음 예제에서는 기본 동작을 무시하는 몇 가지 경우를 보여 줍니다.

* 범주 처리를 처음부터 구현한 경우처럼 `EngagementDefaultNotifier`을(를) 확장하지 않은 경우
* 시스템 알림의 경우 `onNotificationPrepared`을(를) 재정의하고 `Notification` 개체에서 `contentIntent` 또는 `deleteIntent`을(를) 수정했습니다.
* 앱 내 알림의 경우 `prepareInAppArea`을(를) 재정의합니다. U.I 컨트롤 중 하나에 적어도 `actionNotification`을(를) 매핑합니다.

> [!NOTE]
> `handleNotification`에서 예외가 throw되는 경우 콘텐츠는 삭제되고 `dropContent`이(가) 호출됩니다. 이 사항은 통계로 보고되며 이제 다음 캠페인을 처리할 수 있습니다.
> 
> 

### <a name="announcements-and-polls"></a>알림 및 설문 조사
#### <a name="layouts"></a>레이아웃
`engagement_text_announcement.xml`, `engagement_web_announcement.xml`, `engagement_poll.xml` 파일을 수정하여 텍스트 알림, 웹 알림 및 설문 조사를 사용자 지정할 수 있습니다.

이러한 파일은 제목 영역 및 단추 영역의 두 가지 일반 레이아웃을 공유합니다. 제목의 레이아웃은 `engagement_content_title.xml` 이며, 배경에 대해 제목과 동일한 이름의 그릴 수 있는 파일을 사용합니다. 실행 및 종료 단추의 레이아웃은 `engagement_button_bar.xml` 이며, 배경에 대해 제목과 동일한 이름의 그릴 수 있는 파일을 사용합니다.

설문 조사에서 질문 레이아웃 및 선택 항목은 질문에 `engagement_question.xml` 레이아웃 파일을 사용하고 선택 항목에 `engagement_choice.xml` 파일을 여러 번 사용하여 동적으로 팽창됩니다.

#### <a name="categories"></a>범주
##### <a name="alternate-layouts"></a>대체 레이아웃
알림과 마찬가지로 캠페인의 경우에도 범주를 사용하여 알림 및 설문 조사의 대체 레이아웃을 적용할 수 있습니다.

예를 들어 텍스트 알림의 범주를 생성하려면 다음과 같이 `EngagementTextAnnouncementActivity`을(를) 확장하고 `AndroidManifest.xml` 파일에서 참조할 수 있습니다.

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

의도 필터의 범주는 기본 알림 작업의 차이를 구분하는 데 사용됩니다.

Reach SDK는 의도 시스템을 사용하여 특정 범주에 대해 올바른 작업을 확인하고, 확인에 실패하는 경우 기본 범주에 의존합니다.

그런 다음 `MyCustomTextAnnouncementActivity`을(를) 구현해야 하며, 레이아웃을 변경하지만 동일한 뷰 식별자를 유지하려는 경우 다음 예제와 같이 클래스를 정의해야 합니다.

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

텍스트 알림의 기본 범주를 바꾸려면 단지 `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` 을(를) 고유한 구현으로 바꾸면 됩니다.

웹 알림 및 설문 조사는 유사한 방식으로 사용자 지정할 수 있습니다.

웹 알림의 경우 `EngagementWebAnnouncementActivity`을(를) 확장하고 다음 예제와 같이 `AndroidManifest.xml`에서 작업을 선언할 수 있습니다.

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

설문 조사의 경우 `EngagementPollActivity`을(를) 확장하고 다음 예제와 같이 `AndroidManifest.xml`에서 작업을 선언할 수 있습니다.

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>처음부터 구현
Reach SDK에서 제공하는 `Engagement*Activity` 클래스 중 하나를 확장하지 않고 알림(및 설문 조사) 작업에 대한 범주를 구현할 수 있습니다. 이 작업은, 예를 들어 동일한 뷰를 표준 레이아웃으로 사용하지 않는 레이아웃을 정의하려는 경우에 유용합니다.

고급 알림 사용자 지정과 마찬가지로 표준 구현의 소스 코드를 확인하는 것이 좋습니다.

이때 Reach는 콘텐츠 식별자인 추가 매개 변수와 함께 특정 의도(의도 필터에 해당)를 사용하여 작업을 시작한다는 점 등 몇 가지 사항에 유의해야 합니다.

웹 사이트에서 캠페인을 만들 때 지정한 필드를 포함하는 콘텐츠 개체를 검색하려면 다음을 수행합니다.

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

통계를 위해 다음과 같이 콘텐츠가 `onResume` 이벤트에 표시된다는 것을 보고해야 합니다.

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

그런 다음 작업이 백그라운드로 전환되기 전에 잊지 말고 콘텐츠 개체에서 `actionContent(this)` 또는 `exitContent(this)`을(를) 호출해야 합니다.

`actionContent` 또는 `exitContent`을(를) 호출하지 않은 경우 통계가 전송되지 않으며(즉, 캠페인에 대한 분석이 없음), 무엇보다도 응용 프로그램 프로세스가 다시 시작될 때까지 다음 캠페인을 알리지 못합니다.

방향 또는 기타 구성을 변경하면 코드에서 작업을 백그라운드로 전환할지 여부를 결정하는 것이 어려워질 수 있습니다. 표준 구현에서는 사용자가 `HOME` 또는 `BACK`을(를) 눌러 작업을 떠나지만 방향이 변경되지 않은 경우 콘텐츠가 종료된 것으로 보고되도록 합니다.

다음은 구현의 흥미로운 부분입니다.

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

여기에서 알 수 있듯이 `actionContent(this)`을(를) 호출한 후 작업을 완료하는 경우 `exitContent(this)`이(가) 영향을 받지 않고 안전하게 호출될 수 있습니다.

[here]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]:http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]:https://developer.amazon.com/sdk/adm.html

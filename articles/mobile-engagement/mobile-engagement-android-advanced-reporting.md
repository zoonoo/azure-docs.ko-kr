---
title: Azure Mobile Engagement Android SDK에 대한 고급 보고 옵션
description: 고급 보고를 실행하여 Azure Mobile Engagement Android SDK에 대한 분석을 캡처하는 방법을 설명합니다
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7da7abd5-19d6-4892-94d8-818e5424b2cd
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 419b65250d2b65e184e6e36349b17b5ac9e7a6ba
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-reporting-with-engagement-on-android"></a>Android에서 Engagement를 사용한 고급 보고
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!div class="op_single_selector"]
> * [유니버설 Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

이 항목에서는 Android 응용 프로그램에서 추가 보고 시나리오를 설명합니다. 이러한 옵션은 [시작](mobile-engagement-android-get-started.md) 자습서에서 만든 앱에 적용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

완료한 자습서는 의도적으로 직접적이고 간소화했으나 선택할 수 있는 고급 옵션이 있습니다.

## <a name="modifying-your-activity-classes"></a>`Activity` 클래스 수정
[시작 자습서](mobile-engagement-android-get-started.md)에서는 `*Activity` 하위 클래스가 해당 `Engagement*Activity` 클래스에서 상속하는 설정만 지정했습니다. 예를 들어 레거시 작업이 `ListActivity`를 확장하는 경우 `EngagementListActivity`를 확장하도록 합니다.

> [!IMPORTANT]
> `EngagementListActivity` 또는 `EngagementExpandableListActivity`를 사용할 경우 `super.onCreate(...);`를 호출하기 전에 `requestWindowFeature(...);`를 호출해야 합니다. 그렇지 않으면 충돌이 발생합니다.
> 
> 

이러한 클래스는 `src` 폴더에서 찾을 수 있으며 프로젝트에 복사할 수 있습니다. 또한 클래스는 **JavaDoc**에도 있습니다.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>대체 방법: `startActivity()` 및 `endActivity()` 수동 호출
`Activity` 클래스를 오버로드할 수 없거나 오버로드하지 않으려는 경우 `EngagementAgent`의 메서드를 직접 호출하여 작업을 시작하고 종료할 수 있습니다.

> [!IMPORTANT]
> Android SDK는 응용 프로그램이 닫힐 때에도 `endActivity()` 메서드를 호출하지 않습니다(Android에서 응용 프로그램은 닫히지 않음). 따라서 *모든* 작업의 `onResume` 콜백에서는 `startActivity()` 메서드를, *모든* 작업의 `onPause()` 콜백에서는 `endActivity()` 메서드를 호출하는 것이 *상당히* 좋습니다. 이것이 세션이 손실되지 않도록 하는 유일한 방법입니다. 세션이 손실되는 경우 세션이 보류 중인 한 서비스가 연결된 상태를 유지하므로 Engagement 서비스의 Engagement 백 엔드에 대한 연결이 끊기지 않습니다.
> 
> 

다음은 예제입니다.

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

이 예제는 `EngagementActivity` 클래스 및 해당 변형과 비슷합니다. 해당 소스 코드는 `src` 폴더에 있습니다.

## <a name="using-applicationoncreate"></a>Application.onCreate() 사용
`Application.onCreate()` 및 기타 응용 프로그램 콜백에 배치하는 코드는 Engagement 서비스를 비롯하여 모든 응용 프로그램 프로세스에서 실행됩니다. 이로 인해 Engagement 프로세스, 중복 브로드캐스트 수신기 또는 서비스에서의 불필요한 메모리 할당 및 스레드와 같은 원치 않는 부작용이 발생할 수 있습니다.

`Application.onCreate()`를 재정의하는 경우 `Application.onCreate()` 함수의 시작 부분에 다음 코드 조각을 추가하는 것이 좋습니다.

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

그리고 `Application.onTerminate()`, `Application.onLowMemory()` 및 `Application.onConfigurationChanged(...)`에 대해 동일한 작업을 수행할 수 있습니다.

또한 `Application`을(를) 확장하는 대신 `EngagementApplication`을(를) 확장할 수도 있습니다. 콜백 `Application.onCreate()`은(는) 프로세스 검사를 수행하고 현재 프로세스가 Engagement 서비스를 호스트하는 프로세스가 아닌 경우에만 `Application.onApplicationProcessCreate()`을(를) 호출합니다. 그리고 다른 콜백에 대해서도 동일한 규칙이 적용됩니다.

## <a name="tags-in-the-androidmanifestxml-file"></a>AndroidManifest.xml 파일에 태그 지정
AndroidManifest.xml 파일의 서비스 태그에서 `android:label` 특성을 사용하면 휴대폰의 "서비스 실행 중" 화면에서 최종 사용자에게 표시될 Engagement 서비스의 이름을 선택할 수 있습니다. 이 특성을 `"<Your application name>Service"`로 설정하는 것이 좋습니다(예: `"AcmeFunGameService"`).

또한 `android:process` 특성을 지정하면 Engagement 서비스가 자체 프로세스에서 실행됩니다. 그리고 응용 프로그램과 동일한 프로세스에서 Engagement를 실행하면 주/UI 스레드의 응답성이 떨어질 수 있습니다.

## <a name="building-with-proguard"></a>ProGuard를 사용하여 빌드
ProGuard로 응용 프로그램 패키지를 빌드하는 경우 일부 클래스를 유지해야 합니다. 다음 구성 코드 조각을 사용할 수 있습니다.

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
     }

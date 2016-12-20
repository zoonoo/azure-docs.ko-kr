---
title: "Azure Mobile Engagement iOS SDK 업그레이드 절차 | Microsoft Docs"
description: "Azure Mobile Engagement용 iOS SDK의 최신 업데이트 및 절차"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 72a9e493-3f14-4e52-b6e2-0490fd04b184
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 09/14/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ea5025cf031afb2a6d13356059d090c2d63f1665


---
# <a name="upgrade-procedures"></a>업그레이드 절차
이전 버전의 Engagement를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

SDK의 각 새 버전에 대해 먼저 EngagementSDK 및 EngagementReach 폴더를 대체해야 합니다. 즉, xcode에서 이 폴더를 제거한 후에 다시 가져와야 합니다.

## <a name="from-300-to-400"></a>3.0.0에서 4.0.0으로
### <a name="xcode-8"></a>XCode 8
XCode 8은 SDK 버전 4.0.0부터 필수입니다.

> [!NOTE]
> XCode 7을 사용하는 경우 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)를 사용할 수도 있습니다. iOS 10 장치에서 실행하는 경우 이 이전 버전의 도달률 모듈에는 알려진 버그가 있습니다. 시스템 알림은 작동하지 않습니다. 이 문제를 해결하려면 다음과 같이 앱 대리자에서 더 이상 사용되지 않는 API `application:didReceiveRemoteNotification:`을 구현해야 합니다.
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> 이 iOS API는 더 이상 사용되지 않으므로 예정된(부분적인) iOS 버전 업그레이드에서는 이 동작이 달라질 수 있습니다. 따라서 **이 해결 방법은 권장되지 않습니다**. 가능한 한 빨리 XCode 8로 전환해야 합니다.
> 
> 

### <a name="usernotifications-framework"></a>UserNotifications 프레임워크
빌드 단계에서 `UserNotifications` 프레임워크를 추가해야 합니다.

프로젝트 탐색기에서 프로젝트 창을 열고 올바른 대상을 선택합니다. 그런 다음 **"빌드 단계"** 탭을 열고 **"이진과 라이브러리 연결"** 메뉴에서 프레임워크 `UserNotifications.framework` - 링크를 `Optional`로 설정을 추가합니다.

### <a name="application-push-capability"></a>응용 프로그램 푸시 기능
XCode 8은 앱 푸시 기능을 다시 설정할 수 있습니다. 선택한 대상의 `capability` 탭에서 한 번 더 확인하세요.

### <a name="add-the-new-ios-10-notification-registration-code"></a>새 iOS 10 알림 등록 코드
알림에 앱을 등록하는 이전 코드 조각은 계속 작동하지만 iOS 10에서 실행되는 동안은 사용이 중단된 API를 사용합니다.

`User Notification` 프레임워크 가져오기:

        #import <UserNotifications/UserNotifications.h> 

응용 프로그램에서 대리자 `application:didFinishLaunchingWithOptions` 메서드 바꾸기:

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

다음으로 바꾸기:

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>사용자 고유의 UNUserNotificationCenterDelegate 구현이 이미 있는 경우
SDK에도 자체적으로 UNUserNotificationCenterDelegate 프로토콜이 구현되어 있습니다. 이 프로토콜은 iOS 10 이상에서 실행되는 장치의 Engagement 알림 수명 주기를 모니터링하기 위해 SDK에서 사용됩니다. SDK에서 대리자를 검색하는 경우 응용 프로그램당 UNUserNotificationCenter 대리자가 하나만 있기 때문에 자체 구현을 사용하지 않습니다. 즉, 자체 대리자에 Engagement 논리를 추가해야 합니다.

이 작업은 다음 두 가지 방법으로 수행할 수 있습니다.

SDK에 대리자 호출 전달

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

또는 `AEUserNotificationHandler` 클래스에서 상속

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> 해당 `userInfo` 사전을 에이전트 `isEngagementPushPayload:` 클래스 메서드에 전달하여 알림이 Engagement에서 온 것인지를 확인할 수 있습니다.
> 
> 

## <a name="from-200-to-300"></a>2.0.0에서 3.0.0으로
iOS 4.X에 대한 지원을 삭제했습니다. 이 버전부터 응용 프로그램의 배포 대상은 iOS 6 이상이어야 합니다.

응용 프로그램에서 도달률을 사용하는 경우 원격 알림을 받기 위해 `remote-notification` 값을 Info.plist 파일의 `UIBackgroundModes` 배열에 추가해야 합니다.

메서드 `application:didReceiveRemoteNotification:`은 응용 프로그램 대리자의 `application:didReceiveRemoteNotification:fetchCompletionHandler:`로 대체해야 합니다.

"AEPushDelegate.h"는 더 이상 사용되지 않는 인터페이스이므로 모든 참조를 제거해야 합니다. 여기에는 응용 프로그램 대리자에서 `[[EngagementAgent shared] setPushDelegate:self]` 및 대리자 메서드를 제거하는 일도 포함됩니다.

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

## <a name="from-1160-to-200"></a>1.16.0에서 2.0.0으로 마이그레이션
아래에서는 SDK 통합을 Capptain SAS 제공 Capptain 서비스에서 Azure Mobile Engagement 구동 앱으로 마이그레이션하는 방법을 설명합니다.
이전 버전에서 마이그레이션하는 경우 Capptain 웹 사이트를 참조하여 먼저 1.16으로 마이그레이션한 후 다음 절차를 적용하세요.

> [!IMPORTANT]
> Capptain과 Mobile Engagement는 같은 서비스가 아니며, 아래에서 제공하는 절차에서는 클라이언트 앱을 마이그레이션하는 방법만 중점적으로 설명합니다. 앱에서 SDK를 마이그레이션해도 데이터가 Capptain 서버에서 Mobile Engagement 서버로 마이그레이션되지는 않습니다.
> 
> 

### <a name="agent"></a>에이전트
메서드 `registerApp:`이(가) 새 메서드인 `init:`(으)로 바뀌었습니다. 그에 따라 응용 프로그램 대리자를 업데이트하고 다음과 같이 연결 문자열을 사용해야 합니다.

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

SmartAd 추적이 SDK에서 제거되었으므로 `AETrackModule` 클래스의 모든 인스턴스를 제거해야 합니다.

### <a name="class-name-changes"></a>클래스 이름 변경
브랜드 변경의 일환으로 몇 가지 클래스/파일 이름을 변경해야 합니다.

"CP" 접두사가 붙은 모든 클래스의 이름이 "AE"로 바뀌었습니다.

예제:

* `CPModule.h`의 이름이 `AEModule.h`(으)로 바뀌었습니다.

"Capptain" 접두사가 붙은 모든 클래스의 이름이 "Engagement"로 바뀌었습니다.

예제:

* 클래스 `CapptainAgent`의 이름은 `EngagementAgent`(으)로 바뀌었습니다.
* 클래스 `CapptainTableViewController`의 이름은 `EngagementTableViewController`(으)로 바뀌었습니다.
* 클래스 `CapptainUtils`의 이름은 `EngagementUtils`(으)로 바뀌었습니다.
* 클래스 `CapptainViewController`의 이름은 `EngagementViewController`(으)로 바뀌었습니다.




<!--HONumber=Nov16_HO3-->



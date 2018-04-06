---
title: Azure Mobile Engagement iOS SDK 개요 | Microsoft Docs
description: Azure Mobile Engagement용 iOS SDK의 최신 업데이트 및 절차
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3a03bbd6-bcf8-436c-9775-5a8188629252
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 6c9d2ddeacd1d05142019f22eedf4eb3bb4f06cd
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="ios-sdk-for-azure-mobile-engagement"></a>Azure Mobile Engagement용 iOS SDK
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서에서는 iOS 앱에 Azure Mobile Engagement를 통합하는 방법에 대한 모든 세부 사항을 확인할 수 있습니다. 먼저 통합을 연습해 보려면 [15분 자습서](mobile-engagement-ios-get-started.md)의 단계를 진행하세요

[SDK 콘텐츠](mobile-engagement-ios-sdk-content.md)

## <a name="integration-procedures"></a>통합 절차
1. 시작: [iOS 앱에서 Mobile Engagement를 통합하는 방법](mobile-engagement-ios-integrate-engagement.md)
2. 알림: [iOS 앱에서 도달률(알림)을 통합하는 방법](mobile-engagement-ios-integrate-engagement-reach.md)
3. 태그 계획 구현: [iOS 앱에서 고급 Mobile Engagement API 태깅을 사용하는 방법](mobile-engagement-ios-use-engagement-api.md)

## <a name="release-notes"></a>릴리스 정보
### <a name="410-07172017"></a>4.1.0 (07/17/2017)
* 백그라운드에서 지워진 배지를 수정했습니다.
* 기본 큐에서 호출되지 않는 API에 대한 XCode 9의 경고를 수정했습니다.
* 도달률 설문에서 메모리 누수를 해결했습니다.
* iOS 6.X에 대한 지원을 삭제했습니다. 이 버전부터 응용 프로그램의 배포 대상은 iOS 7 이상이어야 합니다.

이전 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-ios-release-notes.md)

## <a name="upgrade-procedures"></a>업그레이드 절차
이전 버전의 Engagement를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

여러 SDK 버전을 건너뛴 경우에는 여러 절차를 수행해야 할 수 있습니다. 관련 정보는 전체 [업그레이드 절차](mobile-engagement-ios-upgrade-procedure.md)를 참조하세요.

SDK의 각 새 버전에 대해 먼저 EngagementSDK 및 EngagementReach 폴더를 대체해야 합니다. 즉, xcode에서 이 폴더를 제거한 후에 다시 가져와야 합니다.

### <a name="from-300-to-400"></a>3.0.0에서 4.0.0으로
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

#### <a name="usernotifications-framework"></a>UserNotifications 프레임워크
빌드 단계에서 `UserNotifications` 프레임워크를 추가해야 합니다.

프로젝트 탐색기에서 프로젝트 창을 열고 올바른 대상을 선택합니다. 그런 다음 **"빌드 단계"** 탭을 열고 **"이진과 라이브러리 연결"** 메뉴에서 프레임워크 `UserNotifications.framework` - 링크를 `Optional`로 설정을 추가합니다.

#### <a name="application-push-capability"></a>응용 프로그램 푸시 기능
XCode 8은 앱 푸시 기능을 다시 설정할 수 있습니다. 선택한 대상의 `capability` 탭에서 한 번 더 확인하세요.

#### <a name="add-the-new-ios-10-notification-registration-code"></a>새 iOS 10 알림 등록 코드
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

#### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>UNUserNotificationCenter 대리자 충돌 해결

*응용 프로그램이나 타사 라이브러리 중 하나에서 `UNUserNotificationCenterDelegate`를 구현하지 않으면 이 부분을 건너뛸 수 있습니다.*

`UNUserNotificationCenter` 대리자는 iOS 10 이상에서 실행되는 장치의 Engagement 알림 수명 주기를 모니터링하기 위해 SDK에서 사용됩니다. SDK에는 `UNUserNotificationCenterDelegate` 프로토콜의 자체 구현이 있지만 응용 프로그램당 `UNUserNotificationCenter` 위임자가 하나만 있을 수 있습니다. `UNUserNotificationCenter` 개체에 추가된 다른 모든 대리자는 Engagement 대리인과 충돌합니다. SDK가 사용자 또는 다른 타사 대리자를 발견하는 경우 충돌을 해결할 기회를 주기 위해 자체 구현을 사용하지 않습니다. 충돌을 해결하기 위해 사용자 고유의 대리자에게 Engagement 논리를 추가해야 합니다.

이 작업은 다음 두 가지 방법으로 수행할 수 있습니다.

제안 1, SDK에 대리자 호출 전달

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

또는 제안 2, `AEUserNotificationHandler` 클래스에서 상속

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

응용 프로그램 대리자의 `application:willFinishLaunchingWithOptions:` 또는 `application:didFinishLaunchingWithOptions:` 메서드 내에서 `UNUserNotificationCenter` 개체의 대리자가 현재 대리자로 설정되어 있는지 확인합니다.
예를 들어, 위의 제안 1을 구현하는 경우:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code

        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }

---
title: iOS에서 Engagement API를 사용하는 방법
description: 최신 iOS SDK - iOS에서 Engagement API를 사용하는 방법
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1fb4509e-3804-46c1-949f-1cf727f91f9f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 189a3029449a3161da2a20f940b77a5bb63bd1ef
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-the-engagement-api-on-ios"></a>iOS에서 Engagement API를 사용하는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서는 iOS에 Engagement를 통합하는 방법 문서를 보완하는 추가 문서로, Engagement API를 사용하여 응용 프로그램 통계를 보고하는 방법을 자세히 설명합니다.

Engagement에서 응용 프로그램 세션, 활동, 작동 중단 및 기술 정보만 보고하도록 하려는 경우 가장 간단한 방법은 모든 사용자 지정 `UIViewController` 개체가 해당 `EngagementViewController` 클래스에서 상속하도록 지정하는 것입니다.

응용 프로그램 관련 이벤트, 오류, 작업을 보고하는 등 추가 작업을 수행하려는 경우 또는 `EngagementViewController` 클래스에서 구현되는 것과는 다른 방식으로 응용 프로그램 활동을 보고해야 하는 경우에는 Engagement API를 사용해야 합니다.

Engagement API는 `EngagementAgent` 클래스를 통해 제공됩니다. 이 클래스의 인스턴스는 `[EngagementAgent shared]` 정적 메서드를 호출하여 검색할 수 있습니다(반환되는 `EngagementAgent` 개체는 단일 항목임).

API를 호출하기 전에 메서드 `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`을(를) 호출하여 `EngagementAgent` 개체를 초기화해야 합니다.

## <a name="engagement-concepts"></a>Engagement 개념
다음 요소는 iOS 플랫폼과 관련된 일반적인 [Mobile Engagement 개념](mobile-engagement-concepts.md) 을 구체화합니다.

### <a name="session-and-activity"></a>`Session` 및 `Activity`
*작업*은 일반적으로 단일 응용 프로그램 화면과 연결됩니다. 즉, *작업*은 화면을 표시하면 시작되며 화면을 닫으면 중지됩니다. `EngagementViewController` 클래스를 사용하여 Engagement SDK를 통합하는 경우 이러한 방식이 사용됩니다.

하지만 Engagement API를 사용하여 *활동* 을 수동으로 제어할 수도 있습니다. 이렇게 하면 지정된 화면을 여러 하위 부분으로 분할하여 해당 화면의 사용에 대해 더 많은 세부 정보(예: 이 화면 내에서 대화 상자를 사용하는 빈도와 기간)를 확인할 수 있습니다.

## <a name="reporting-activities"></a>활동 보고
### <a name="user-starts-a-new-activity"></a>사용자가 새 활동을 시작함
            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

사용자 활동이 변경될 때마다 `startActivity()` 을(를) 호출해야 합니다. 이 함수를 처음 호출하면 새 사용자 세션이 시작됩니다.

### <a name="user-ends-his-current-activity"></a>사용자가 현재 활동을 종료함
            [[EngagementAgent shared] endActivity];

> [!WARNING]
> 단일 응용 프로그램 사용을 여러 세션으로 분할하려는 경우를 제외하고는 이 함수를 직접 호출하면 **안 됩니다**. 이 함수를 호출하면 현재 세션이 즉시 종료되므로 `startActivity()`를 후속 호출 시 새 세션이 시작됩니다. 응용 프로그램을 닫으면 SDK에서 이 함수를 자동으로 호출합니다.
> 
> 

## <a name="reporting-events"></a>이벤트 보고
### <a name="session-events"></a>세션 이벤트
세션 이벤트는 일반적으로 사용자가 세션 중에 수행하는 동작을 보고하는 데 사용됩니다.

**추가 데이터가 없는 예제:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**추가 데이터가 있는 예제:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>독립 실행형 이벤트
세션 이벤트와 달리 독립 실행형 이벤트는 세션의 컨텍스트 외부에서 사용할 수 있습니다.

**예제:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## <a name="reporting-errors"></a>오류 보고
### <a name="session-errors"></a>세션 오류
세션 오류는 일반적으로 세션 중에 사용자에게 영향을 주는 오류를 보고하는 데 사용됩니다.

**예제:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>독립 실행형 오류
세션 오류와 달리 독립 실행형 오류는 세션의 컨텍스트 외부에서 사용할 수 있습니다.

**예제:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## <a name="reporting-jobs"></a>작업 보고
**예제:**

로그인 프로세스의 기간을 보고하는 경우를 가정해 보겠습니다.

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>작업 중 오류 보고
오류는 현재 사용자 세션이 아닌 실행 중인 작업에 관련될 수 있습니다.

**예제:**

로그인 프로세스 중의 오류를 보고하는 경우를 가정해 보겠습니다.

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>작업 중의 이벤트
이벤트는 현재 사용자 세션이 아닌 실행 중인 작업에 관련될 수 있습니다.

**예제:**

소셜 네트워크가 있으며 작업을 사용하여 사용자가 서버에 연결되어 있는 총 시간을 보고한다고 가정해 보겠습니다. 사용자는 친구로부터 메시지를 받을 수 있습니다. 이것이 작업 이벤트입니다.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## <a name="extra-parameters"></a>extras 매개 변수
이벤트, 오류, 활동 또는 작업에 임의 데이터를 연결할 수 있습니다.

이 데이터는 구조화할 수 있으며 iOS의 NSDictionary 클래스를 사용합니다.

extras는`arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` 또는 기타 `NSDictionary` 인스턴스를 포함할 수 있습니다.

> [!NOTE]
> extras 매개 변수는 JSON에서 serialize됩니다. 위에서 설명한 것과 다른 개체를 전달하려는 경우에는 클래스에서 다음 메서드를 구현해야 합니다.
> 
> -(NSString*) JSONRepresentation;
> 
> 해당 메서드는 개체의 JSON 표현을 반환해야 합니다.
> 
> 

### <a name="example"></a>예
    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>제한
#### <a name="keys"></a>구성
`NSDictionary` 의 각 키는 다음 정규식과 일치해야 합니다.

`^[a-zA-Z][a-zA-Z_0-9]*`

즉, 키는 하나 이상의 문자로 시작해야 하며 그 뒤에 문자, 숫자 또는 밑줄(\_)이 붙어야 합니다.

#### <a name="size"></a>크기
extras는 Engagement 에이전트에 의해 JSON으로 인코딩되고 나면 호출당 **1024** 자로 제한됩니다.

위의 예제에서 서버로 전송된 JSON의 길이는 58자입니다.

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>응용 프로그램 정보 보고
`sendAppInfo:` 함수를 사용하면 추적 정보 또는 기타 응용 프로그램 관련 정보를 수동으로 보고할 수 있습니다.

이러한 정보는 증분 방식으로 보낼 수 있습니다. 그러면 특정 장치에 대해 지정한 키의 최신 값만 보관됩니다.

extras 이벤트와 마찬가지로 `NSDictionary` 클래스도 응용 프로그램 정보를 요약하는 데 사용됩니다. 배열 또는 하위 사전은 JSON serialization을 사용하여 플랫 문자열로 처리됩니다.

**예제:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>제한
#### <a name="keys"></a>구성
`NSDictionary` 의 각 키는 다음 정규식과 일치해야 합니다.

`^[a-zA-Z][a-zA-Z_0-9]*`

즉, 키는 하나 이상의 문자로 시작해야 하며 그 뒤에 문자, 숫자 또는 밑줄(\_)이 붙어야 합니다.

#### <a name="size"></a>크기
응용 프로그램 정보s는 Engagement 에이전트에 의해 JSON으로 인코딩되고 나면 호출당 **1024** 자로 제한됩니다.

위의 예제에서 서버로 전송된 JSON의 길이는 44자입니다.

    {"birthdate":"1983-12-07","gender":"female"}

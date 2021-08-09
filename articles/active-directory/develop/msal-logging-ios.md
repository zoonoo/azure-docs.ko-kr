---
title: iOS/macOS용 MSAL에서 오류 및 예외 로깅
titleSuffix: Microsoft identity platform
description: IOS/macOS용 MSAL에서 오류 및 예외를 로그하는 방법 알아보기
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6e3af65c3eefe7f72b9cf3ff79c280012e39289e
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108164330"
---
# <a name="logging-in-msal-for-iosmacos"></a>iOS/macOS용 MSAL에서 로깅

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>iOS 및 macOS용 MSAL 로깅-ObjC

MSAL 로깅을 캡처하고 사용자 고유의 애플리케이션의 로깅에 통합하도록 콜백을 설정합니다. 콜백에 대한 서명은 다음과 같습니다.

```objc
/*!
    The LogCallback block for the MSAL logger

    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

예를 들면 다음과 같습니다.

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>개인 데이터

기본적으로 MSAL은 개인 데이터를 캡처하거나 기록하지 않습니다. 라이브러리를 사용하면 앱 개발자가 MSALLogger 클래스의 속성을 통해 이 기능을 켤 수 있습니다. `pii.Enabled`를 켜면 앱은 매우 중요한 데이터를 안전하게 처리하고 규정 요구 사항을 준수해야 합니다.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>로깅 수준

IOS 및 macOS용 MSAL을 사용하여 로깅할 때 로깅 수준을 설정하려면 다음 값 중 하나를 사용합니다.

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| 모든 로깅을 사용하지 않도록 설정 |
| `MSALLogLevelError` | 기본 수준, 오류가 발생한 경우에만 정보를 출력 |
| `MSALLogLevelWarning` | 경고 |
| `MSALLogLevelInfo` |  매개 변수 및 다양한 키 집합 작업을 포함하는 라이브러리 진입점 |
|`MSALLogLevelVerbose`     |  API 추적 |

예를 들면 다음과 같습니다.

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
```

 ### <a name="log-message-format"></a>로그 메시지 형식

MSAL 로그 메시지의 메시지 부분은 `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message` 형식입니다.

예를 들면 다음과 같습니다.

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

상관 관계 ID 및 타임스탬프를 제공하면 문제를 추적하는 데 도움이 됩니다. 로그 메시지에서 타임스탬프 및 상관 관계 ID 정보를 사용할 수 있습니다. 이를 검색하는 신뢰할 수 있는 유일한 장소는 MSAL 로깅 메시지입니다.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>iOS 및 macOS용 MSAL 로깅-Swift

MSAL 로깅을 캡처하고 사용자 고유의 애플리케이션의 로깅에 통합하도록 콜백을 설정합니다. 콜백에 대한 서명(Objective-C로 표시)은 다음과 같습니다.

```objc
/*!
    The LogCallback block for the MSAL logger

    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

예를 들면 다음과 같습니다.

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>개인 데이터

기본적으로 MSAL은 개인 데이터를 캡처하거나 기록하지 않습니다. 라이브러리를 사용하면 앱 개발자가 MSALLogger 클래스의 속성을 통해 이 기능을 켤 수 있습니다. `pii.Enabled`를 켜면 앱은 매우 중요한 데이터를 안전하게 처리하고 규정 요구 사항을 준수해야 합니다.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>로깅 수준

IOS 및 macOS용 MSAL을 사용하여 로깅할 때 로깅 수준을 설정하려면 다음 값 중 하나를 사용합니다.

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| 모든 로깅을 사용하지 않도록 설정 |
| `MSALLogLevelError` | 기본 수준, 오류가 발생한 경우에만 정보를 출력 |
| `MSALLogLevelWarning` | 경고 |
| `MSALLogLevelInfo` |  매개 변수 및 다양한 키 집합 작업을 포함하는 라이브러리 진입점 |
|`MSALLogLevelVerbose`     |  API 추적 |

예를 들면 다음과 같습니다.

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
```

### <a name="log-message-format"></a>로그 메시지 형식

MSAL 로그 메시지의 메시지 부분은 `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message` 형식입니다.

예를 들면 다음과 같습니다.

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

상관 관계 ID 및 타임스탬프를 제공하면 문제를 추적하는 데 도움이 됩니다. 로그 메시지에서 타임스탬프 및 상관 관계 ID 정보를 사용할 수 있습니다. 이를 검색하는 신뢰할 수 있는 유일한 장소는 MSAL 로깅 메시지입니다.

---

## <a name="next-steps"></a>다음 단계

더 많은 코드 샘플은 [Microsoft ID 플랫폼 코드 샘플](sample-v2-code.md)을 참조하세요.

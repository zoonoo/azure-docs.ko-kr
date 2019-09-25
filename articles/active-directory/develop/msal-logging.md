---
title: MSAL 응용 프로그램 로그인 | Microsoft id 플랫폼
description: MSAL(Microsoft 인증 라이브러리) 애플리케이션에서 로깅하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dad8a276cd40b1ff04bbced833b5d70cec4fc87
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268594"
---
# <a name="logging-in-msal-applications"></a>MSAL 응용 프로그램 로그인

MSAL (Microsoft 인증 라이브러리) 앱은 문제를 진단 하는 데 도움이 될 수 있는 로그 메시지를 생성 합니다. 앱은 몇 줄의 코드를 사용하여 로깅을 구성하고 세부 정보 수준 및 개인 데이터와 조직 데이터가 기록되는지 여부에 대한 제어를 사용자 지정할 수 있습니다. MSAL 로깅 콜백을 만들고 사용자가 인증 문제가 있는 경우 로그를 제출 하는 방법을 제공 하는 것이 좋습니다.

## <a name="logging-levels"></a>로깅 수준

MSAL은 여러 수준의 로깅 세부 정보를 제공 합니다.

- 오류: 무언가 잘못되어 오류가 발생했음을 나타냅니다. 디버깅 및 문제 식별에 사용합니다.
- 경고: 오류 또는 실패가 아니어도 되지만 진단 및 어설션 매크로나 문제를 해결 하기 위한 것입니다.
- Info(정보): MSAL은 반드시 디버그하기 위한 것이 아니라 정보를 제공하기 위해 사용되는 이벤트를 기록합니다.
- Verbose(자세한 정보): 기본. MSAL은 라이브러리 동작에 대 한 전체 세부 정보를 기록 합니다.

## <a name="personal-and-organizational-data"></a>개인 및 조직 데이터

기본적으로 MSAL 로거가 매우 중요 한 개인 또는 조직 데이터를 캡처하지 않습니다. 이러한 작업을 수행 하기로 결정 한 경우 라이브러리는 개인 데이터와 조직 데이터를 로깅할 수 있는 옵션을 제공 합니다.

## <a name="logging-in-msalnet"></a>MSAL.NET의 로깅

 > [!NOTE]
 > MSAL.NET에 대 한 자세한 내용은 [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)를 참조 하세요. MSAL.NET 로깅 등의 샘플을 가져옵니다.
 
MSAL 3.x에서 로깅은 앱을 만들 때 `.WithLogging` 작성기 한정자를 사용하여 앱별로 설정됩니다. 이 메서드에서 사용하는 매개 변수는 다음과 같습니다.

- *Level*을 사용하면 원하는 로깅 수준을 결정할 수 있습니다. Errors로 설정하면 오류만 발생합니다.
- *PiiLoggingEnabled*를 사용하여 true로 설정하면 개인 및 조직 데이터를 기록할 수 있습니다. 기본적으로이는 false로 설정 되어 있으므로 응용 프로그램이 개인 데이터를 기록 하지 않습니다.
- *LogCallback*은 로깅을 수행하는 대리자로 설정됩니다. *PiiLoggingEnabled*이 true이면 이 메서드에서 메시지를 두 번 받습니다. 첫 번째는 *containsPii* 매개 변수가 false이고 개인 데이터가 없는 메시지이며, 두 번째는 *containsPii* 매개 변수가 true이고 개인 데이터가 포함될 수 있는 메시지입니다. 메시지에 개인 데이터가 포함 되어 있지 않은 경우에는 메시지가 동일 하 게 됩니다.
- *DefaultLoggingEnabled*는 플랫폼에 대한 기본 로깅을 사용하도록 설정합니다. 기본적으로 false입니다. true로 설정하면 데스크톱/UWP 애플리케이션의 이벤트 추적, iOS의 NSLog, Android의 logcat을 사용합니다.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>MSAL.js의 로깅

 `UserAgentApplication` 인스턴스를 만들기 위해 구성하는 중에 로거 개체를 전달하여 MSAL.js에서 로깅을 사용하도록 설정할 수 있습니다. 로거 개체의 속성은 다음과 같습니다.

- `localCallback`: 사용자 지정 방식으로 로그를 사용 하 고 게시 하기 위해 개발자가 제공할 수 있는 콜백 인스턴스입니다. 로그를 리디렉션하려는 방법에 따라 localCallback 메서드를 구현합니다.

- `level`(선택 사항): 구성 가능한 로그 수준입니다. 지원되는 로그 수준은 다음과 같습니다. Error, Warning, Info, Verbose. 기본값은 Info입니다.

- `piiLoggingEnabled`(선택 사항): true로 설정 되어 있는 경우 개인 데이터와 조직 데이터를 기록할 수 있습니다. 기본적으로이는 false로 설정 되어 있으므로 응용 프로그램이 개인 데이터를 기록 하지 않습니다. 개인 데이터 로그는 콘솔, Logcat 또는 NSLog와 같은 기본 출력에 기록되지 않습니다. 기본값은 false로 설정하는 것입니다.

- `correlationId`(선택 사항): 디버깅을 위해 응답과 함께 요청을 매핑하는 데 사용 되는 고유 식별자입니다. 기본값은 RFC4122 버전 4 GUID(128비트)입니다.

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="logging-in-msal-for-ios-and-macos"></a>IOS 및 macOS 용 MSAL 로그인

MSAL 로깅을 캡처하도록 콜백을 설정 하 고 자체 응용 프로그램의 로깅에 통합 합니다. 콜백에 대 한 시그니처는 다음과 같습니다.

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

예를 들어 다음과 같은 가치를 제공해야 합니다.

Objective-C
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

Swift
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

### <a name="personal-identifiable-information-pii"></a>PII (개인 식별이 가능한 정보)

기본적으로 MSAL은 PII를 캡처하거나 기록 하지 않습니다. 라이브러리를 사용 하면 앱 개발자가 MSALLogger 클래스의 속성을 통해이를 설정할 수 있습니다. PII를 켜면 앱은 매우 중요 한 데이터를 안전 하 게 처리 하 고 규정 요구 사항을 준수 합니다.

Objective-C
```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

Swift
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>로깅 수준

IOS 및 macOS 용 MSAL을 사용 하 여 로깅할 때 로깅 수준을 설정 하려면 다음 값 중 하나를 사용 합니다.

|Level  |설명 |
|---------|---------|
| `MSALLogLevelNothing`| 모든 로깅 사용 안 함 |
| `MSALLogLevelError` | 기본 수준은 오류가 발생 한 경우에만 정보를 출력 합니다. |
| `MSALLogLevelWarning` | 경고 |
| `MSALLogLevelInfo` |  매개 변수 및 다양 한 키 집합 작업을 포함 하는 라이브러리 진입점 |
|`MSALLogLevelVerbose`     |  API 추적       |

예를 들어 다음과 같은 가치를 제공해야 합니다.

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 Swift
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>로그 메시지 형식

MSAL 로그 메시지의 메시지 부분은 다음 형식으로 되어 있습니다.`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

예를 들어 다음과 같은 가치를 제공해야 합니다.

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

상관 관계 Id 및 타임 스탬프를 제공 하면 문제를 추적 하는 데 도움이 됩니다. 로그 메시지에서 타임 스탬프 및 상관 관계 ID 정보를 사용할 수 있습니다. 이를 검색 하는 신뢰할 수 있는 유일한 장소는 MSAL 로깅 메시지입니다.

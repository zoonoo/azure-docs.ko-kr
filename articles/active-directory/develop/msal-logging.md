---
title: MSAL 앱로그인 | Azure
titleSuffix: Microsoft identity platform
description: MSAL(Microsoft 인증 라이브러리) 애플리케이션에서 로깅하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 58697cc535357710c6889f05060b5e04e129ae7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084887"
---
# <a name="logging-in-msal-applications"></a>MSAL 응용 프로그램에 로그인

MSAL(Microsoft 인증 라이브러리) 앱은 문제를 진단하는 데 도움이 되는 로그 메시지를 생성합니다. 앱은 몇 줄의 코드를 사용하여 로깅을 구성하고 세부 정보 수준 및 개인 데이터와 조직 데이터가 기록되는지 여부에 대한 제어를 사용자 지정할 수 있습니다. MSAL 로깅 콜백을 만들고 사용자가 인증 문제가 있을 때 로그를 제출할 수 있는 방법을 제공하는 것이 좋습니다.

## <a name="logging-levels"></a>로깅 수준

MSAL은 다음과 같은 여러 수준의 로깅 세부 정보를 제공합니다.

- 오류: 문제가 발생하여 오류가 발생했음을 나타냅니다. 디버깅 및 문제 식별에 사용합니다.
- 경고: 반드시 오류 나 오류가 있는 것은 아니지만 진단 및 문제 파악을 위한 것입니다.
- 정보: MSAL은 디버깅을 목적으로 하는 것은 아닙니다.
- 자세한 내용: 기본값입니다. MSAL은 라이브러리 동작에 대한 전체 세부 정보를 기록합니다.

## <a name="personal-and-organizational-data"></a>개인 및 조직 데이터

기본적으로 MSAL 로거는 매우 중요한 개인 또는 조직 데이터를 캡처하지 않습니다. 라이브러리는 개인 및 조직 데이터를 로깅할 수 있도록 설정하는 옵션을 제공합니다.

특정 언어로 MSAL 로깅에 대한 자세한 내용은 해당 언어와 일치하는 탭을 선택합니다.

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>MSAL.NET의 로깅

 > [!NOTE]
 > MSAL.NET 로깅 등의 샘플은 [MSAL.NET 위키를](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) 참조하십시오.

MSAL 3.x에서 로깅은 앱을 만들 때 `.WithLogging` 작성기 한정자를 사용하여 앱별로 설정됩니다. 이 메서드에서 사용하는 매개 변수는 다음과 같습니다.

- `Level`을 사용하면 원하는 로깅 수준을 결정할 수 있습니다. Errors로 설정하면 오류만 발생합니다.
- `PiiLoggingEnabled`true로 설정된 경우 개인 및 조직 데이터를 기록할 수 있습니다. 애플리케이션에서 개인 데이터를 기록하지 않도록 기본적으로 false로 설정됩니다.
- `LogCallback`은 로깅을 수행하는 대리자로 설정됩니다. true이면 `PiiLoggingEnabled` 이 메서드는 메시지를 두 번 `containsPii` 수신합니다: 매개 변수가 한 번은 거짓이고 `containsPii` 개인 데이터가 없는 메시지는 같고 매개 변수가 있는 두 번째 메시지는 true와 같으며 메시지에는 개인 데이터가 포함될 수 있습니다. 경우에 따라(메시지에 개인 데이터가 포함되어 있지 않은 경우) 메시지가 동일합니다.
- `DefaultLoggingEnabled`을 사용하면 플랫폼에 대한 기본 로깅이 활성화됩니다. 기본적으로 false입니다. true로 설정하면 데스크톱/UWP 애플리케이션의 이벤트 추적, iOS의 NSLog, Android의 logcat을 사용합니다.

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

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>자바를 사용하여 안드로이드에 대한 MSAL에 로그인

로깅 콜백을 만들어 앱 만들기시 로깅을 켭니다. 콜백은 다음과 같은 매개 변수를 사용합니다.

- `tag`은 라이브러리에 의해 콜백에 전달되는 문자열입니다. 로그 항목과 연결되며 로깅 메시지를 정렬하는 데 사용할 수 있습니다.
- `logLevel`을 사용하면 원하는 로깅 수준을 결정할 수 있습니다. 지원되는 로그 `Error`수준은 `Warning` `Info`" `Verbose`, " 및 .
- `message`은 로그 항목의 내용입니다.
- `containsPII`는 개인 데이터가 포함된 메시지 또는 조직 데이터가 기록되는지 여부를 지정합니다. 기본적으로 이 값은 false로 설정되므로 응용 프로그램에서 개인 데이터를 기록하지 않습니다. 이 `containsPII` `true`메서드는 두 번 메시지를 수신합니다: `containsPII` 매개 `false` 변수가 `message` 개인 데이터 없이 설정된 `containsPii` 경우 한 `true` 번, 매개 변수가 설정된 두 번째 메시지는 개인 데이터를 포함할 수 있습니다. 경우에 따라(메시지에 개인 데이터가 포함되어 있지 않은 경우) 메시지가 동일합니다.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

기본적으로 MSAL 로거는 개인 식별 정보 나 조직 식별 정보를 캡처하지 않습니다.
개인 식별 정보 또는 조직 식별 정보의 로깅을 사용하려면 다음을 수행하십시오.

```java
Logger.getInstance().setEnablePII(true);
```

개인 데이터 및 조직 데이터 로깅을 사용하지 않도록 설정하려면 다음을 수행합니다.

```java
Logger.getInstance().setEnablePII(false);
```

기본적으로 logcat에 로깅이 비활성화됩니다. 사용하려면 다음을 수행하십시오.

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[자바 스크립트](#tab/javascript)

 인스턴스를 만들기 위한 구성 중에 로거 개체를 전달하여 MSAL.js(자바스크립트)에서 로깅을 활성화합니다. `UserAgentApplication` 로거 개체의 속성은 다음과 같습니다.

- `localCallback`: 개발자가 사용자 지정 방식으로 로그를 사용하고 게시하기 위해 제공할 수 있는 콜백 인스턴스입니다. 로그를 리디렉션하려는 방법에 따라 localCallback 메서드를 구현합니다.
- `level`(선택 사항): 구성 가능한 로그 수준입니다. 지원되는 로그 `Error`수준은 `Warning` `Info`" `Verbose`, " 및 . 기본값은 `Info`입니다.
- `piiLoggingEnabled`(선택 사항): true로 설정된 경우 개인 및 조직 데이터를 기록합니다. 기본적으로 이는 거짓이므로 응용 프로그램이 개인 데이터를 기록하지 않습니다. 개인 데이터 로그는 콘솔, Logcat 또는 NSLog와 같은 기본 출력에 기록되지 않습니다.
- `correlationId`(선택 사항): 디버깅을 위해 응답을 매핑하는 데 사용되는 고유 식별자입니다. 기본값은 RFC4122 버전 4 GUID(128비트)입니다.

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
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

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>iOS 및 macOS 로깅-ObjC용 MSAL

MSAL 로깅을 캡처하고 응용 프로그램의 로깅에 통합하도록 콜백을 설정합니다. 콜백의 서명은 다음과 같습니다.

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

예를 들어:

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

기본적으로 MSAL은 개인 데이터(PII)를 캡처하거나 기록하지 않습니다. 라이브러리를 사용하면 앱 개발자가 MSALLogger 클래스의 속성을 통해 이 기능을 켤 수 있습니다. 앱을 켜면 `pii.Enabled`매우 중요한 데이터를 안전하게 처리하고 규정 요구 사항을 준수할 책임이 있습니다.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>로깅 수준

iOS 및 macOS용 MSAL을 사용하여 로깅할 때 로깅 수준을 설정하려면 다음 값 중 하나를 사용합니다.

|Level  |설명 |
|---------|---------|
| `MSALLogLevelNothing`| 모든 로깅 사용 안 함 |
| `MSALLogLevelError` | 기본 수준, 오류가 발생할 때만 정보를 인쇄합니다. |
| `MSALLogLevelWarning` | 경고 |
| `MSALLogLevelInfo` |  매개변수 및 다양한 키체인 연산이 있는 라이브러리 진입점 |
|`MSALLogLevelVerbose`     |  API 추적 |

예를 들어:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>로그 메시지 형식

MSAL 로그 메시지의 메시지 부분은`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

예를 들어:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

상관 관계 아이디와 타임스탬프를 제공하는 것은 문제를 추적하는 데 유용합니다. 타임스탬프 및 상관 관계 ID 정보는 로그 메시지에서 사용할 수 있습니다. 이를 검색할 수 있는 유일한 신뢰할 수 있는 장소는 MSAL 로깅 메시지입니다.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>iOS 및 macOS 로깅-스위프트용 MSAL

MSAL 로깅을 캡처하고 응용 프로그램의 로깅에 통합하도록 콜백을 설정합니다. 콜백의 시그니처(Objective-C로 표시)는 다음과 같습니다.

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

예를 들어:

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

기본적으로 MSAL은 개인 데이터(PII)를 캡처하거나 기록하지 않습니다. 라이브러리를 사용하면 앱 개발자가 MSALLogger 클래스의 속성을 통해 이 기능을 켤 수 있습니다. 앱을 켜면 `pii.Enabled`매우 중요한 데이터를 안전하게 처리하고 규정 요구 사항을 준수할 책임이 있습니다.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>로깅 수준

iOS 및 macOS용 MSAL을 사용하여 로깅할 때 로깅 수준을 설정하려면 다음 값 중 하나를 사용합니다.

|Level  |설명 |
|---------|---------|
| `MSALLogLevelNothing`| 모든 로깅 사용 안 함 |
| `MSALLogLevelError` | 기본 수준, 오류가 발생할 때만 정보를 인쇄합니다. |
| `MSALLogLevelWarning` | 경고 |
| `MSALLogLevelInfo` |  매개변수 및 다양한 키체인 연산이 있는 라이브러리 진입점 |
|`MSALLogLevelVerbose`     |  API 추적 |

예를 들어:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>로그 메시지 형식

MSAL 로그 메시지의 메시지 부분은`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

예를 들어:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

상관 관계 아이디와 타임스탬프를 제공하는 것은 문제를 추적하는 데 유용합니다. 타임스탬프 및 상관 관계 ID 정보는 로그 메시지에서 사용할 수 있습니다. 이를 검색할 수 있는 유일한 신뢰할 수 있는 장소는 MSAL 로깅 메시지입니다.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>자바 로깅을위한 MSAL

Java용 MSAL을 사용하면 SLF4J와 호환되는 한 앱과 함께 이미 사용 중인 로깅 라이브러리를 사용할 수 있습니다. JAVA용 MSAL은 [java.util.logging,](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html) [Logback](http://logback.qos.ch/) 및 [Log4j와](https://logging.apache.org/log4j/2.x/)같은 다양한 로깅 프레임워크에 대한 간단한 외관 또는 추상화로 [Java용 단순 로깅 파사드(SLF4J)를](http://www.slf4j.org/) 사용합니다. SLF4J를 사용하면 사용자가 배포 시 원하는 로깅 프레임워크를 연결할 수 있습니다.

예를 들어 로그백을 응용 프로그램의 로깅 프레임워크로 사용하려면 응용 프로그램의 Maven pom 파일에 Logback 종속성을 추가합니다.

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

그런 다음 로그백 구성 파일을 추가합니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J는 배포 시 로그백에 자동으로 바인딩됩니다. MSAL 로그가 콘솔에 기록됩니다.

다른 로깅 프레임워크에 바인딩하는 방법에 대한 지침은 [SLF4J 설명서를](http://www.slf4j.org/manual.html)참조하십시오.

### <a name="personal-and-organization-information"></a>개인 및 조직 정보

기본적으로 MSAL 로깅은 개인 또는 조직 데이터를 캡처하거나 기록하지 않습니다. 다음 예제에서는 개인 또는 조직 데이터 로깅이 기본적으로 꺼져 있습니다.

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

클라이언트 응용 프로그램 빌더를 설정하여 `logPii()` 개인 및 조직 데이터 로깅을 켭니다. 개인 또는 조직 데이터 로깅을 켜면 앱에서 매우 중요한 데이터를 안전하게 처리하고 모든 규정 요구 사항을 준수할 책임이 있습니다.

다음 예제에서는 개인 또는 조직 데이터 로깅이 활성화됩니다.

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>파이썬 로깅을 위한 MSAL

MSAL 파이썬에 로그인하면 표준 파이썬 `logging.info("msg")` 로깅 메커니즘을 사용하며, 예를 들어 다음과 같이 MSAL 로깅을 구성할 수 있습니다 [(username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)작동하도록 볼 수 있습니다).

### <a name="enable-debug-logging-for-all-modules"></a>모든 모듈에 대해 디버그 로깅 사용

기본적으로 모든 파이썬 스크립트의 로그온이 꺼져 있습니다. 전체 Python 스크립트의 모든 모듈에 대해 디버그 로깅을 사용하려면 다음을 사용하십시오.

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>침묵 전용 MSAL 로깅

MSAL 라이브러리 로깅만 무음으로 유지하려면 파이썬 스크립트의 다른 모든 모듈에서 디버그 로깅을 활성화하면서 MSAL Python에서 사용하는 로거를 끕니다.

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>파이썬의 개인 및 조직 데이터

파이썬에 대한 MSAL은 개인 데이터 또는 조직 데이터를 기록하지 않습니다. 개인 또는 조직 데이터를 로그온 또는 오프할 수 있는 속성은 없습니다.

표준 Python 로깅을 사용하여 원하는 대로 로깅할 수 있지만 중요한 데이터를 안전하게 처리하고 규정 요구 사항을 준수해야 합니다.

파이썬로 로그인에 대한 자세한 내용은 파이썬의 [로깅 하우토를](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)참조하십시오.

---

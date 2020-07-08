---
title: MSAL 앱에 로그인 | Microsoft
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
ms.custom: aaddev, tracking-python
ms.openlocfilehash: 300b7e4fe3e3c150a78fee5b63458feab266aafe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84558667"
---
# <a name="logging-in-msal-applications"></a>MSAL 응용 프로그램 로그인

MSAL (Microsoft 인증 라이브러리) 앱은 문제를 진단 하는 데 도움이 될 수 있는 로그 메시지를 생성 합니다. 앱은 몇 줄의 코드를 사용하여 로깅을 구성하고 세부 정보 수준 및 개인 데이터와 조직 데이터가 기록되는지 여부에 대한 제어를 사용자 지정할 수 있습니다. MSAL 로깅 콜백을 만들고 사용자가 인증 문제가 있는 경우 로그를 제출 하는 방법을 제공 하는 것이 좋습니다.

## <a name="logging-levels"></a>로깅 수준

MSAL은 여러 수준의 로깅 세부 정보를 제공 합니다.

- 오류: 무언가 오류가 발생 하 여 오류가 발생 했음을 나타냅니다. 디버깅 및 문제 식별에 사용합니다.
- 경고: 오류 또는 실패가 아니어도 되지만 진단 및 어설션 매크로나 문제를 해결 하기 위한 것입니다.
- 정보: MSAL은 디버깅을 위한 것이 아니라 정보를 제공 하기 위해 의도 된 이벤트를 로깅합니다.
- Verbose: 기본값입니다. MSAL은 라이브러리 동작에 대 한 전체 세부 정보를 기록 합니다.

## <a name="personal-and-organizational-data"></a>개인 및 조직 데이터

기본적으로 MSAL 로거가 매우 중요 한 개인 또는 조직 데이터를 캡처하지 않습니다. 이러한 작업을 수행 하기로 결정 한 경우 라이브러리는 개인 데이터와 조직 데이터를 로깅할 수 있는 옵션을 제공 합니다.

특정 언어로 된 MSAL 로깅에 대 한 자세한 내용을 보려면 해당 언어와 일치 하는 탭을 선택 합니다.

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>MSAL.NET의 로깅

 > [!NOTE]
 > MSAL.NET 로깅 및 기타에 대 한 샘플은 [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) 를 참조 하세요.

MSAL 3.x에서 로깅은 앱을 만들 때 `.WithLogging` 작성기 한정자를 사용하여 앱별로 설정됩니다. 이 메서드에서 사용하는 매개 변수는 다음과 같습니다.

- `Level`원하는 로깅 수준을 결정할 수 있습니다. Errors로 설정하면 오류만 발생합니다.
- `PiiLoggingEnabled`true로 설정 되 면 개인 및 조직 데이터를 기록할 수 있습니다. 애플리케이션에서 개인 데이터를 기록하지 않도록 기본적으로 false로 설정됩니다.
- `LogCallback`는 로깅을 수행 하는 대리자로 설정 됩니다. 이 `PiiLoggingEnabled` true 인 경우이 메서드는 `containsPii` 매개 변수를 false로 설정 하 고 개인 데이터를 포함 하지 않는 메시지를 두 번 받게 되며, 매개 변수를 사용 하는 두 번째 시간은 `containsPii` true와 같으며 메시지에 개인 데이터가 포함 될 수 있습니다. 경우에 따라(메시지에 개인 데이터가 포함되어 있지 않은 경우) 메시지가 동일합니다.
- `DefaultLoggingEnabled`플랫폼에 대 한 기본 로깅을 사용 하도록 설정 합니다. 기본적으로 false입니다. true로 설정하면 데스크톱/UWP 애플리케이션의 이벤트 추적, iOS의 NSLog, Android의 logcat을 사용합니다.

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

## <a name="android"></a>[OWA(Outlook Web Access)](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Java를 사용 하 여 Android 용 MSAL 로그인

로깅 콜백을 만들어 앱을 만들 때 로깅을 설정 합니다. 콜백은 다음과 같은 매개 변수를 사용 합니다.

- `tag`는 라이브러리에 의해 콜백에 전달 되는 문자열입니다. 로그 항목과 연결 되며 로깅 메시지를 정렬 하는 데 사용할 수 있습니다.
- `logLevel`원하는 로깅 수준을 결정할 수 있습니다. 지원 되는 로그 수준은 `Error` ,, `Warning` `Info` 및 `Verbose` 입니다.
- `message`로그 항목의 내용입니다.
- `containsPII`개인 데이터 또는 조직 데이터를 포함 하는 메시지가 기록 되는지 여부를 지정 합니다. 기본적으로이는 false로 설정 되어 있으므로 응용 프로그램이 개인 데이터를 기록 하지 않습니다. `containsPII`가 인 경우 `true` 이 메서드는 `containsPII` 매개 변수를로 설정 하 고 개인 데이터를 사용 하지 않고를 두 번 받게 되며 `false` `message` , `containsPii` 매개 변수를로 설정 하 `true` 고 메시지에 개인 데이터가 포함 될 수 있습니다. 경우에 따라(메시지에 개인 데이터가 포함되어 있지 않은 경우) 메시지가 동일합니다.

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

기본적으로 MSAL 로거가 개인 식별이 가능한 정보나 조직에서 식별할 수 있는 정보를 캡처하지 않습니다.
개인 식별이 가능한 정보 또는 조직에서 식별할 수 있는 정보를 로깅할 수 있습니다.

```java
Logger.getInstance().setEnablePII(true);
```

개인 데이터 및 조직 데이터 로깅을 사용 하지 않도록 설정 하려면:

```java
Logger.getInstance().setEnablePII(false);
```

기본적으로 logcat에 로깅은 사용 하지 않도록 설정 됩니다. 사용 하도록 설정 하려면

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

 인스턴스를 만드는 구성 중에로 거 개체를 전달 하 여 MSAL.js (JavaScript)에서 로깅을 사용 하도록 설정 합니다 `UserAgentApplication` . 로거 개체의 속성은 다음과 같습니다.

- `localCallback`: 사용자 지정 방식으로 로그를 사용 하 고 게시 하기 위해 개발자가 제공할 수 있는 콜백 인스턴스입니다. 로그를 리디렉션하려는 방법에 따라 localCallback 메서드를 구현합니다.
- `level`(선택 사항): 구성 가능한 로그 수준입니다. 지원 되는 로그 수준은 `Error` ,, `Warning` `Info` 및 `Verbose` 입니다. 기본값은 `Info`입니다.
- `piiLoggingEnabled`(선택 사항): true로 설정 하면 개인 데이터와 조직 데이터를 기록 합니다. 기본적으로 응용 프로그램이 개인 데이터를 기록 하지 않도록 false입니다. 개인 데이터 로그는 콘솔, Logcat 또는 NSLog와 같은 기본 출력에 기록되지 않습니다.
- `correlationId`(선택 사항): 디버깅을 위해 응답과 함께 요청을 매핑하는 데 사용 되는 고유 식별자입니다. 기본값은 RFC4122 버전 4 GUID(128비트)입니다.

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

## <a name="msal-for-ios-and-macos-logging-objc"></a>IOS 및 macOS 로깅에 대 한 MSAL-ObjC

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

기본적으로 MSAL은 PII (개인 데이터)를 캡처하거나 기록 하지 않습니다. 라이브러리를 사용 하면 앱 개발자가 MSALLogger 클래스의 속성을 통해이를 설정할 수 있습니다. `pii.Enabled`앱은 매우 중요 한 데이터를 안전 하 게 처리 하 고 규정 요구 사항을 충족 하는 역할을 합니다.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>로깅 수준

IOS 및 macOS 용 MSAL을 사용 하 여 로깅할 때 로깅 수준을 설정 하려면 다음 값 중 하나를 사용 합니다.

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| 모든 로깅 사용 안 함 |
| `MSALLogLevelError` | 기본 수준은 오류가 발생 한 경우에만 정보를 출력 합니다. |
| `MSALLogLevelWarning` | 경고 |
| `MSALLogLevelInfo` |  매개 변수 및 다양 한 키 집합 작업을 포함 하는 라이브러리 진입점 |
|`MSALLogLevelVerbose`     |  API 추적 |

예를 들어:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>로그 메시지 형식

MSAL 로그 메시지의 메시지 부분은 다음 형식으로 되어 있습니다.`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

예를 들어:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

상관 관계 Id 및 타임 스탬프를 제공 하면 문제를 추적 하는 데 도움이 됩니다. 로그 메시지에서 타임 스탬프 및 상관 관계 ID 정보를 사용할 수 있습니다. 이를 검색 하는 신뢰할 수 있는 유일한 장소는 MSAL 로깅 메시지입니다.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>IOS 및 macOS 로깅에 대 한 MSAL-Swift

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

기본적으로 MSAL은 PII (개인 데이터)를 캡처하거나 기록 하지 않습니다. 라이브러리를 사용 하면 앱 개발자가 MSALLogger 클래스의 속성을 통해이를 설정할 수 있습니다. `pii.Enabled`앱은 매우 중요 한 데이터를 안전 하 게 처리 하 고 규정 요구 사항을 충족 하는 역할을 합니다.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>로깅 수준

IOS 및 macOS 용 MSAL을 사용 하 여 로깅할 때 로깅 수준을 설정 하려면 다음 값 중 하나를 사용 합니다.

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| 모든 로깅 사용 안 함 |
| `MSALLogLevelError` | 기본 수준은 오류가 발생 한 경우에만 정보를 출력 합니다. |
| `MSALLogLevelWarning` | 경고 |
| `MSALLogLevelInfo` |  매개 변수 및 다양 한 키 집합 작업을 포함 하는 라이브러리 진입점 |
|`MSALLogLevelVerbose`     |  API 추적 |

예를 들어:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>로그 메시지 형식

MSAL 로그 메시지의 메시지 부분은 다음 형식으로 되어 있습니다.`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

예를 들어:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

상관 관계 Id 및 타임 스탬프를 제공 하면 문제를 추적 하는 데 도움이 됩니다. 로그 메시지에서 타임 스탬프 및 상관 관계 ID 정보를 사용할 수 있습니다. 이를 검색 하는 신뢰할 수 있는 유일한 장소는 MSAL 로깅 메시지입니다.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>Java 로깅을 위한 MSAL

Java 용 MSAL은 SLF4J와 호환 되는 한 앱에서 이미 사용 하 고 있는 로깅 라이브러리를 사용할 수 있도록 합니다. Java 용 MSAL은 다양 한 로깅 프레임 워크 (예: [java.](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html)SLF4J, [Logback](http://logback.qos.ch/) 및 [Log4j](https://logging.apache.org/log4j/2.x/))에 대 한 간단한 외관 또는 추상화로 [java 용 단순 로깅 외관](http://www.slf4j.org/) 을 사용 합니다. SLF4J를 사용 하면 사용자가 배포 시 원하는 로깅 프레임 워크에 연결할 수 있습니다.

예를 들어 응용 프로그램의 로깅 프레임 워크로 Logback을 사용 하려면 응용 프로그램에 대 한 Maven pom 파일에 Logback 종속성을 추가 합니다.

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

그런 다음 Logback 구성 파일을 추가 합니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J은 배포 시에 자동으로 Logback에 바인딩합니다. MSAL 로그가 콘솔에 기록 됩니다.

다른 로깅 프레임 워크에 바인딩하는 방법에 대 한 지침은 [SLF4J 설명서](http://www.slf4j.org/manual.html)를 참조 하세요.

### <a name="personal-and-organization-information"></a>개인 및 조직 정보

기본적으로 MSAL 로깅은 개인 데이터 나 조직 데이터를 캡처하거나 로깅하지 않습니다. 다음 예제에서 개인 또는 조직 데이터 로깅은 기본적으로 해제 되어 있습니다.

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

클라이언트 응용 프로그램 작성기에서을 설정 하 여 개인 및 조직 데이터 로깅을 설정 `logPii()` 합니다. 개인 또는 조직 데이터 로깅을 설정 하는 경우 앱은 매우 중요 한 데이터를 안전 하 게 처리 하 고 규정 요구 사항을 준수 해야 합니다.

다음 예제에서는 개인 또는 조직 데이터 로깅이 사용 됩니다.

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>Python 로깅을 위한 MSAL

MSAL의 로그인은 표준 Python 로깅 메커니즘을 사용 합니다. 예를 들어 `logging.info("msg")` [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)다음과 같이 msal 로깅을 구성할 수 있습니다.

### <a name="enable-debug-logging-for-all-modules"></a>모든 모듈에 대해 디버그 로깅 사용

기본적으로 Python 스크립트의 로깅은 해제 되어 있습니다. 전체 Python 스크립트의 모든 모듈에 대해 디버그 로깅을 사용 하도록 설정 하려면 다음을 사용 합니다.

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>침묵 전용 MSAL 로깅

MSAL 라이브러리 로깅을 대기 전용으로 설정 하 고 Python 스크립트의 모든 다른 모듈에서 디버그 로깅을 사용 하도록 설정 하는 동안 MSAL Python에서 사용 하는로 거를 해제 합니다.

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Python의 개인 및 조직 데이터

Python 용 MSAL은 개인 데이터 나 조직 데이터를 기록 하지 않습니다. 개인 또는 조직 데이터 로깅을 설정 하거나 해제 하는 속성은 없습니다.

표준 Python 로깅을 사용 하 여 원하는 모든 항목을 기록할 수 있지만 중요 한 데이터를 안전 하 게 처리 하 고 규정 요구 사항을 준수 해야 합니다.

Python 로그인에 대 한 자세한 내용은 Python의 [로깅 방법](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)을 참조 하세요.

---

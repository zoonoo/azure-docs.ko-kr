---
title: MSAL 애플리케이션의 로깅 | Azure
description: MSAL(Microsoft 인증 라이브러리) 애플리케이션에서 로깅하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58f18995d46ca61ae68a7b226bbfc9a286e73a0b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544108"
---
# <a name="logging"></a>로깅
MSAL(Microsoft 인증 라이브러리) 앱은 문제를 진단하고 세부 정보를 제공하는 데 도움이 되는 로그 메시지를 생성합니다. 앱은 몇 줄의 코드를 사용하여 로깅을 구성하고 세부 정보 수준 및 개인 데이터와 조직 데이터가 기록되는지 여부에 대한 제어를 사용자 지정할 수 있습니다. MSAL 로깅 콜백을 설정하고 사용자에게 인증 문제가 있을 때 로그를 제출할 수 있는 방법을 제공하는 것이 좋습니다.

## <a name="logging-levels"></a>로깅 수준

MSAL의 로거에서 세부 정보를 캡처하는 데 고려할 수 있는 여러 수준은 다음과 같습니다.

- 오류: 무언가 잘못되어 오류가 발생했음을 나타냅니다. 디버깅 및 문제 식별에 사용합니다.
- 경고: 앱 및 의심스러운 이벤트에 대한 자세한 정보가 필요합니다. 오류 또는 장애가 발생한 것은 아니지만, 문제를 진단하고 정확히 지적하는 데 사용합니다.
- Info(정보): MSAL은 반드시 디버그하기 위한 것이 아니라 정보를 제공하기 위해 사용되는 이벤트를 기록합니다.
- Verbose(자세한 정보): 기본값 MSAL은 많은 양의 정보를 기록하고 특정 라이브러리 동작에 대한 자세한 정보를 제공합니다.

## <a name="personal-and-organizational-data"></a>개인 및 조직 데이터
기본적으로 MSAL 로거는 매우 중요한 개인 또는 조직 데이터를 캡처하지 않습니다. 라이브러리는 개인 및 조직 데이터 로깅을 사용하도록 설정하는 옵션을 제공합니다.

## <a name="logging-in-msalnet"></a>MSAL.NET의 로깅
MSAL 3.x에서 로깅은 앱을 만들 때 `.WithLogging` 작성기 한정자를 사용하여 앱별로 설정됩니다. 이 메서드에서 사용하는 매개 변수는 다음과 같습니다.

- *Level*을 사용하면 원하는 로깅 수준을 결정할 수 있습니다. Errors로 설정하면 오류만 발생합니다.
- *PiiLoggingEnabled*를 사용하여 true로 설정하면 개인 및 조직 데이터를 기록할 수 있습니다. 애플리케이션에서 개인 데이터를 기록하지 않도록 기본적으로 false로 설정됩니다.
- *LogCallback*은 로깅을 수행하는 대리자로 설정됩니다. *PiiLoggingEnabled*이 true이면 이 메서드에서 메시지를 두 번 받습니다. 첫 번째는 *containsPii* 매개 변수가 false이고 개인 데이터가 없는 메시지이며, 두 번째는 *containsPii* 매개 변수가 true이고 개인 데이터가 포함될 수 있는 메시지입니다. 경우에 따라(메시지에 개인 데이터가 포함되어 있지 않은 경우) 메시지가 동일합니다.
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
                                             .ExecuteAsnc();
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>MSAL.js의 로깅

 `UserAgentApplication` 인스턴스를 만들기 위해 구성하는 중에 로거 개체를 전달하여 MSAL.js에서 로깅을 사용하도록 설정할 수 있습니다. 로거 개체의 속성은 다음과 같습니다.

- *localCallback*: 로그를 사용자 지정 방식으로 사용하고 게시할 수 있도록 개발자가 제공할 수 있는 Callback 인스턴스입니다. 로그를 리디렉션하려는 방법에 따라 localCallback 메서드를 구현합니다.

- *level*(선택 사항): 구성 가능한 로그 수준입니다. 지원되는 로그 수준은 다음과 같습니다. Error, Warning, Info, Verbose. 기본값은 Info입니다.

- *piiLoggingEnabled*(선택 사항): true로 설정되면 개인 및 조직 데이터를 기록할 수 있습니다. 애플리케이션에서 개인 데이터를 기록하지 않도록 기본적으로 false로 설정됩니다. 개인 데이터 로그는 콘솔, Logcat 또는 NSLog와 같은 기본 출력에 기록되지 않습니다. 기본값은 false로 설정하는 것입니다.

- *correlationId* (선택 사항): 디버깅 용도로 요청과 응답을 매핑하는 데 사용되는 고유 식별자입니다. 기본값은 RFC4122 버전 4 GUID(128비트)입니다.

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
    system: {
        logger: {
            localCallback: loggerCallback,
            level: Msal.LogLevel.Verbose,
            piiLoggingEnabled: false,
            correlationId: '1234'
        }
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

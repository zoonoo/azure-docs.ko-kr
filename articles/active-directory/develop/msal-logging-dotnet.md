---
title: MSAL.NET에서 오류 및 예외 로깅
titleSuffix: Microsoft identity platform
description: MSAL.NET에서 오류 및 예외를 기록 하는 방법을 알아봅니다.
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
ms.openlocfilehash: 47576265c9b1a20f801231abe2fb37a929d5a27c
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763539"
---
# <a name="logging-in-msalnet"></a>MSAL.NET의 로깅

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>MSAL.NET에서 로깅 구성

MSAL 3.x에서 로깅은 앱을 만들 때 `.WithLogging` 작성기 한정자를 사용하여 앱별로 설정됩니다. 이 메서드에서 사용하는 매개 변수는 다음과 같습니다.

- `Level` 원하는 로깅 수준을 결정할 수 있습니다. Errors로 설정하면 오류만 발생합니다.
- `PiiLoggingEnabled` true로 설정 되 면 개인 및 조직 데이터를 기록할 수 있습니다. 애플리케이션에서 개인 데이터를 기록하지 않도록 기본적으로 false로 설정됩니다.
- `LogCallback` 는 로깅을 수행 하는 대리자로 설정 됩니다. 이 `PiiLoggingEnabled` true 인 경우이 메서드는 `containsPii` 매개 변수를 false로 설정 하 고 개인 데이터를 포함 하지 않는 메시지를 두 번 받게 되며, 매개 변수를 사용 하는 두 번째 시간은 `containsPii` true와 같으며 메시지에 개인 데이터가 포함 될 수 있습니다. 경우에 따라(메시지에 개인 데이터가 포함되어 있지 않은 경우) 메시지가 동일합니다.
- `DefaultLoggingEnabled` 플랫폼에 대 한 기본 로깅을 사용 하도록 설정 합니다. 기본적으로 false입니다. true로 설정하면 데스크톱/UWP 애플리케이션의 이벤트 추적, iOS의 NSLog, Android의 logcat을 사용합니다.

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

> [!TIP]
 > MSAL.NET 로깅 및 기타에 대 한 샘플은 [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

더 많은 코드 샘플은 [Microsoft id 플랫폼 코드 샘플](sample-v2-code.md)을 참조 하세요.
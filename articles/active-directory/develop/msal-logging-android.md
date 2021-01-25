---
title: Android 용 MSAL에서 오류 및 예외를 기록 합니다.
titleSuffix: Microsoft identity platform
description: Android 용 MSAL에서 오류 및 예외를 기록 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: b5d11c1bd091e3d7fbe5be87004b91f242e807f5
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763503"
---
# <a name="logging-in-msal-for-android"></a>Android용 MSAL에서 로깅

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>Java를 사용 하 여 Android 용 MSAL 로그인

로깅 콜백을 만들어 앱을 만들 때 로깅을 설정 합니다. 콜백은 다음과 같은 매개 변수를 사용 합니다.

- `tag` 는 라이브러리에 의해 콜백에 전달 되는 문자열입니다. 로그 항목과 연결 되며 로깅 메시지를 정렬 하는 데 사용할 수 있습니다.
- `logLevel` 원하는 로깅 수준을 결정할 수 있습니다. 지원 되는 로그 수준은 `Error` ,, `Warning` `Info` 및 `Verbose` 입니다.
- `message` 로그 항목의 내용입니다.
- `containsPII` 개인 데이터 또는 조직 데이터를 포함 하는 메시지가 기록 되는지 여부를 지정 합니다. 기본적으로이는 false로 설정 되어 있으므로 응용 프로그램이 개인 데이터를 기록 하지 않습니다. `containsPII`가 인 경우 `true` 이 메서드는 `containsPII` 매개 변수를로 설정 하 고 개인 데이터를 사용 하지 않고를 두 번 받게 되며 `false` `message` , `containsPii` 매개 변수를로 설정 하 `true` 고 메시지에 개인 데이터가 포함 될 수 있습니다. 경우에 따라(메시지에 개인 데이터가 포함되어 있지 않은 경우) 메시지가 동일합니다.

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

## <a name="next-steps"></a>다음 단계

더 많은 코드 샘플은 [Microsoft id 플랫폼 코드 샘플](sample-v2-code.md)을 참조 하세요.
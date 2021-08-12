---
title: Android용 MSAL에서 오류 및 예외 로깅
titleSuffix: Microsoft identity platform
description: Android용 MSAL에서 오류 및 예외를 기록하는 방법을 알아봅니다.
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
ms.openlocfilehash: ce0929adbb2b0213cfd4ee61fe795a2d5f33c648
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954887"
---
# <a name="logging-in-msal-for-android"></a>Android용 MSAL에서 로깅

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>Java를 사용하여 Android용 MSAL에서 로깅

로깅 콜백을 만들어 앱 생성 시 로깅을 켭니다. 콜백은 다음 매개 변수를 사용합니다.

- `tag`는 라이브러리에서 콜백에 전달되는 문자열입니다. 로그 항목과 연결되며 로깅 메시지를 정렬하는 데 사용할 수 있습니다.
- `logLevel`을 사용하면 원하는 로깅 수준을 결정할 수 있습니다. 지원되는 로그 수준은 `Error`, `Warning`, `Info`, 및 `Verbose`입니다.
- `message`는 로그 항목의 콘텐츠입니다.
- `containsPII`는 개인 데이터 또는 조직 데이터가 포함된 메시지를 로깅할지 여부를 지정합니다. 애플리케이션에서 개인 데이터를 기록하지 않도록 기본적으로 false로 설정됩니다. `containsPII`가 `true`이면 이 메서드에서 메시지를 두 번 받습니다. 첫 번째는 `containsPII` 매개 변수가 `false`로 설정되어 있고 `message`에 개인 데이터가 없는 경우이고, 두 번째는 `containsPii` 매개 변수가 `true`로 설정되고 메시지에 개인 데이터가 포함될 수 있는 경우입니다. 경우에 따라(메시지에 개인 데이터가 포함되어 있지 않은 경우) 메시지가 동일합니다.

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

기본적으로 MSAL 로거는 개인 식별이 가능한 정보나 조직 식별이 가능한 정보를 캡처하지 않습니다.
개인 식별이 가능한 정보나 조직 식별이 가능한 정보의 로깅을 사용하도록 설정하려면 다음을 수행합니다.

```java
Logger.getInstance().setEnablePII(true);
```

개인 데이터 및 조직 데이터 로깅을 사용하지 않도록 설정하려면 다음을 수행합니다.

```java
Logger.getInstance().setEnablePII(false);
```

logcat에 대한 로깅은 기본적으로 사용하지 않도록 설정되어 있습니다. 이 기능을 사용하도록 설정하려면 다음을 수행합니다.

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="next-steps"></a>다음 단계

더 많은 코드 샘플은 [Microsoft ID 플랫폼 코드 샘플](sample-v2-code.md)을 참조하세요.
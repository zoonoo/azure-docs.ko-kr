---
title: MSAL.js에서 오류 및 예외 로깅
titleSuffix: Microsoft identity platform
description: MSAL.js에서 오류 및 예외를 로그하는 방법 알아보기
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
ms.openlocfilehash: d7463e3376847a219750254548c25ca79f4cdfb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954820"
---
# <a name="logging-in-msaljs"></a>MSAL.js의 로깅

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>MSAL.js에서 로깅 구성

`UserAgentApplication` 인스턴스를 만들기 위해 구성하는 중에 로거 개체를 전달하여 MSAL.js(JavaScript)에서 로깅을 사용하도록 설정할 수 있습니다. 로거 개체의 속성은 다음과 같습니다.

- `localCallback`: 로그를 사용자 지정 방식으로 사용하고 게시할 수 있도록 개발자가 제공할 수 있는 Callback 인스턴스입니다. 로그를 리디렉션하려는 방법에 따라 localCallback 메서드를 구현합니다.
- `level`(선택 사항): 구성 가능한 로그 수준입니다. 지원되는 로그 수준은 `Error`, `Warning`, `Info`, 및 `Verbose`입니다. 기본값은 `Info`입니다.
- `piiLoggingEnabled`(선택 사항): true로 설정하면 개인 및 조직 데이터를 로그합니다. 애플리케이션에서 개인 데이터를 로그하지 않도록 기본적으로 false로 설정됩니다. 개인 데이터 로그는 콘솔, Logcat 또는 NSLog와 같은 기본 출력에 기록되지 않습니다.
- `correlationId`(선택 사항): 디버깅 용도로 요청과 응답을 매핑하는 데 사용되는 고유 식별자입니다. 기본값은 RFC4122 버전 4 GUID(128비트)입니다.

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
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>다음 단계

더 많은 코드 샘플은 [Microsoft ID 플랫폼 코드 샘플](sample-v2-code.md)을 참조하세요.
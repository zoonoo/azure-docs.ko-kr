---
title: MSAL.js에서 오류 및 예외 로깅
titleSuffix: Microsoft identity platform
description: MSAL.js에서 오류 및 예외를 기록 하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954820"
---
# <a name="logging-in-msaljs"></a>MSAL.js의 로깅

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>MSAL.js에서 로깅 구성

인스턴스를 만드는 구성 중에로 거 개체를 전달 하 여 MSAL.js (JavaScript)에서 로깅을 사용 하도록 설정 합니다 `UserAgentApplication` . 로거 개체의 속성은 다음과 같습니다.

- `localCallback`: 사용자 지정 방식으로 로그를 사용 하 고 게시 하기 위해 개발자가 제공할 수 있는 콜백 인스턴스입니다. 로그를 리디렉션하려는 방법에 따라 localCallback 메서드를 구현합니다.
- `level` (선택 사항): 구성 가능한 로그 수준입니다. 지원 되는 로그 수준은 `Error` ,, `Warning` `Info` 및 `Verbose` 입니다. 기본값은 `Info`입니다.
- `piiLoggingEnabled` (선택 사항): true로 설정 하면 개인 데이터와 조직 데이터를 기록 합니다. 기본적으로 응용 프로그램이 개인 데이터를 기록 하지 않도록 false입니다. 개인 데이터 로그는 콘솔, Logcat 또는 NSLog와 같은 기본 출력에 기록되지 않습니다.
- `correlationId` (선택 사항): 디버깅을 위해 응답과 함께 요청을 매핑하는 데 사용 되는 고유 식별자입니다. 기본값은 RFC4122 버전 4 GUID(128비트)입니다.

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

더 많은 코드 샘플은 [Microsoft id 플랫폼 코드 샘플](sample-v2-code.md)을 참조 하세요.
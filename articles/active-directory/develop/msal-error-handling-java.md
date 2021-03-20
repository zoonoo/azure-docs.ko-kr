---
title: MSAL4J에서 오류 및 예외 처리
titleSuffix: Microsoft identity platform
description: MSAL4J 응용 프로그램에서 오류 및 예외, 조건부 액세스 클레임 문제 및 재시도를 처리 하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760677"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>Java용 MSAL에서 오류 및 예외 처리

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>Java 용 MSAL의 오류 처리

Java용 MSAL에는 모두 `MsalException`에서 상속되는 `MsalClientException`, `MsalServiceException` 및 `MsalInteractionRequiredException`과 같은 세 가지 유형의 예외가 있습니다.

- 라이브러리 또는 디바이스에 로컬인 오류가 발생하면 `MsalClientException`이 throw됩니다.
- STS(보안 토큰 서비스)가 오류 응답을 반환하거나 다른 네트워킹 오류가 발생하면 `MsalServiceException`이 throw됩니다.
- 성공적인 인증을 위해 UI 상호 작용이 필요한 경우 `MsalInteractionRequiredException`이 throw됩니다.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException`은 STS에 대한 요청에서 반환된 HTTP 헤더를 노출합니다. `MsalServiceException.headers()`를 통해 액세스합니다.

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

`AcquireTokenSilently()`를 호출할 때 Java용 MSAL에서 반환되는 일반적인 상태 코드 중 하나는 `InvalidGrantError`입니다. 이것은 인증 토큰을 발급하기 위해서는 추가 사용자 조작이 필요하다는 것을 의미합니다. 애플리케이션은 인증 라이브러리를 다시 호출하지만 퍼블릭 클라이언트 애플리케이션에 대한 `AuthorizationCodeParameters` 또는 `DeviceCodeParameters`를 전송하여 대화형 모드에서 호출해야 합니다.

`AcquireTokenSilently`가 실패하는 대부분의 경우는 토큰 캐시에 요청과 일치하는 토큰이 없기 때문입니다. 액세스 토큰은 1시간 후에 만료되고 `AcquireTokenSilently`는 새로 고침 토큰을 기준으로 새 토큰을 가져오려고 합니다. OAuth2 용어에서는 이것을 새로 고침 토큰 흐름이라고 합니다. 이 흐름은 테넌트 관리자가 보다 엄격한 로그인 정책을 구성하는 등, 여러 가지 이유로 실패할 수도 있습니다.

이 오류가 발생하는 상황은 사용자가 쉽게 해결할 수 있습니다. 예를 들어 컴퓨터에서 특정 회사 네트워크에 연결 해야 하기 때문에 사용 약관을 수락 하거나 현재 구성으로 요청을 수행할 수 없는 경우가 있습니다.

MSAL은 더 나은 사용자 환경을 제공하는 데 사용할 수 있는 `reason` 필드를 노출합니다. 예를 들어 `reason` 필드는 사용자에게 암호가 만료되었거나 일부 리소스를 사용하기 위해 동의를 제공해야 한다는 사실을 사용자에게 알려줄 수 있습니다. 지원되는 값은 `InteractionRequiredExceptionReason` 열거형에 속합니다.

| 이유 | 의미 | 권장 처리 |
|---------|-----------|-----------------------------|
| `BasicAction` | 대화형 인증 흐름 중에 사용자 상호 작용을 통해 상황을 해결할 수 있습니다. | `acquireToken`대화형 매개 변수를 사용 하 여를 호출 합니다. |
| `AdditionalAction` | 대화형 인증 흐름 외부에서 시스템과의 추가적인 수정 상호 작업을 통해 상황을 해결할 수 있습니다. | 대화형 매개 변수를 사용하여 `acquireToken`을 호출하는 방식으로 수정 작업을 설명하는 메시지를 표시합니다. 호출 앱은 사용자가 수정 작업을 완료할 가능성이 없는 경우 additional_action이 필요한 흐름을 숨기도록 선택할 수 있습니다. |
| `MessageOnly` | 지금은 상황을 해결할 수 없습니다. 대화형 인증 흐름을 시작하면 상황을 설명하는 메시지가 표시됩니다. | 대화형 매개 변수를 사용하여 `acquireToken`을 호출하는 방식으로 상황을 설명하는 메시지를 표시합니다. `acquireToken`은 사용자가 메시지를 읽고 창을 닫은 후에 `UserCanceled` 오류를 반환합니다. 앱은 사용자가 메시지를 사용하는 것이 불가능한 경우 메시지를 표시하는 흐름을 숨기도록 선택할 수 있습니다. |
| `ConsentRequired`| 사용자 동의가 누락되었거나 해지되었습니다. |사용자가 동의를 제공할 수 있도록 대화형 매개 변수를 사용하여 `acquireToken`을 호출합니다. |
| `UserPasswordExpired` | 사용자 암호가 만료되었습니다. | `acquireToken`사용자가 암호를 재설정할 수 있도록 대화형 매개 변수를 사용 하 여를 호출 합니다. |
| `None` |  자세한 내용이 제공됩니다. 대화형 인증 흐름 중에 사용자 상호 작용을 통해 상황을 해결할 수 있습니다. | `acquireToken`대화형 매개 변수를 사용 하 여를 호출 합니다. |

### <a name="code-example"></a>코드 예제

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>다음 단계

문제를 진단 하 고 디버그할 수 있도록 [Java 용 MSAL에서 로깅을](msal-logging-java.md) 사용 하도록 설정 하는 것이 좋습니다.

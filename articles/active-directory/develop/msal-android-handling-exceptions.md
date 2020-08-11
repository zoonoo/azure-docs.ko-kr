---
title: 오류 및 예외 (MSAL Android) | Microsoft
titleSuffix: Microsoft identity platform
description: MSAL Android 응용 프로그램에서 오류 및 예외, 조건부 액세스 및 클레임 문제를 처리 하는 방법에 대해 알아봅니다.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: f929d8d45f82ab605cb93ff3e3862ef2ccfe8681
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053034"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Android 용 MSAL에서 예외 및 오류 처리

MSAL (Microsoft 인증 라이브러리)의 예외는 앱 개발자가 응용 프로그램의 문제를 해결 하는 데 도움을 주기 위한 것입니다. 예외 메시지는 지역화되지 않았습니다.

예외 및 오류를 처리할 때 예외 유형 자체와 오류 코드를 사용하여 예외를 구별할 수 있습니다.  오류 코드 목록은 [인증 및 권한 부여 오류 코드](reference-aadsts-error-codes.md)를 참조하세요.

로그인 환경에서 동의, 조건부 액세스(MFA, 디바이스 관리, 위치 기반 제한), 토큰 발급 및 상환 및 사용자 속성에 대한 오류가 발생할 수 있습니다.


|Error 클래스 | 원인/오류 문자열| 처리 방법 |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: 액세스 토큰을 사용 하는 데 사용 되는 새로 고침 토큰이 잘못 되었거나 만료 되었거나 취소 되었습니다. 이 예외는 암호 변경 때문에 발생할 수 있습니다. </li><li>`NO_TOKENS_FOUND`: 액세스 토큰이 존재 하지 않으며 액세스 토큰을 사용 하기 위한 새로 고침 토큰을 찾을 수 없습니다.</li> <li>단계 필요<ul><li>MFA</li><li>누락 된 클레임</li></ul></li><li>조건부 액세스에 의해 차단 됨 (예: [authentication broker](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth) 설치 필요)</li><li>`NO_ACCOUNT_FOUND`: 자동 인증을 위해 캐시에서 사용할 수 있는 계정이 없습니다.</li></ul> |을 호출 하 여 사용자에 게 사용자 `acquireToken()` 이름과 암호를 입력 하 라는 메시지를 표시 하 고, multi-factor authentication에 동의 하 고 수행할 수 있습니다.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: 사용자 또는 서버에서 모든 범위를 수락 하지 않았습니다. 요청 된 범위가 지원 되지 않거나 인식 되지 않거나 특정 계정에 대해 지원 되지 않는 경우 서버에서 범위를 거부할 수 있습니다. </li></ul>| 개발자는 부여 된 범위를 사용 하 여 인증을 계속할지 아니면 인증 프로세스를 시작할지를 결정 해야 합니다. 부여 된 범위에 대해서만 토큰 획득 요청을 다시 전송 하 고를 전달 하 고 호출 하 여 부여 된 권한에 대 한 힌트를 제공 하는 옵션입니다 `silentParametersForGrantedScopes` `acquireTokenSilent` . |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`:이 요청에 필수 매개 변수가 없거나, 잘못 된 매개 변수를 포함 하거나, 매개 변수를 두 번 이상 포함 하거나, 형식이 잘못 된 경우 </li><li>`SERVICE_NOT_AVAILABLE`: 서비스가 종료 되었기 때문에 500/503/506 오류 코드를 나타냅니다. </li><li>`UNAUTHORIZED_REQUEST`: 클라이언트에 인증 코드를 요청할 수 있는 권한이 없습니다.</li><li>`ACCESS_DENIED`: 리소스 소유자 또는 권한 부여 서버에서 요청을 거부 했습니다.</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` 유효성 검사 실패</li><li>`UNKNOWN_ERROR`: 서버에 대 한 요청이 실패 했지만, 오류 및 `error_description` 서비스에서 다시 반환 됩니다.</li><ul>| 이 예외 클래스는 서비스와 통신 하는 경우 인증 또는 토큰 끝점에서 발생 하는 오류를 나타냅니다. MSAL은 오류를 읽고 서버 응답에서 error_description 합니다. 일반적으로 이러한 오류는 코드 또는 앱 등록 포털에서 앱 구성을 수정 하 여 해결 됩니다. 거의 서비스 중단으로 인해이 경고가 트리거될 수 있습니다 .이 경고는 서비스가 복구 될 때까지 대기 하 여 완화할 수 있습니다.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: 캐시 항목을 여러 개 찾았습니다. sdk가 캐시에서 올바른 액세스 또는 새로 고침 토큰을 식별할 수 없습니다. 이 예외는 일반적으로 sdk에서 토큰을 저장 하는 버그를 표시 하거나, 기관이 자동 요청에서 제공 되지 않고 일치 하는 여러 토큰이 발견 되었음을 나타냅니다. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: 장치에서 활성 네트워크를 사용할 수 없습니다. </li><li>`JSON_PARSE_FAILURE`: Sdk가 JSON 형식을 구문 분석 하지 못했습니다.</li><li>`IO_ERROR`: `IOException` 발생, 장치 또는 네트워크 오류 일 수 있습니다. </li><li>`MALFORMED_URL`: Url의 형식이 잘못 되었습니다. 인증 요청, 기관 또는 리디렉션 URI를 생성할 때 발생할 수 있습니다. </li><li>`UNSUPPORTED_ENCODING`: 인코딩이 장치에서 지원 되지 않습니다. </li><li>`NO_SUCH_ALGORITHM`: [Pkce](https://tools.ietf.org/html/rfc7636) 챌린지를 생성 하는 데 사용 되는 알고리즘은 지원 되지 않습니다. </li><li>`INVALID_JWT`: `JWT` 서버에서 반환 되었거나 비어 있거나 형식이 잘못 되었습니다. </li><li>`STATE_MISMATCH`: 권한 부여 응답의 상태가 권한 부여 요청의 상태와 일치 하지 않습니다. 권한 부여 요청의 경우 sdk는 리디렉션에서 반환 된 상태와 요청에서 보낸 상태를 확인 합니다. </li><li>`UNSUPPORTED_URL`: 지원 되지 않는 url 이며 ADFS 인증 유효성 검사를 수행할 수 없습니다. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: 권한이 인증 기관 유효성 검사에 대해 지원 되지 않습니다. Sdk는 B2C 기관을 지원 하지만 B2C authority 유효성 검사는 지원 하지 않습니다. 잘 알려진 호스트만 지원 됩니다. </li><li>`CHROME_NOT_INSTALLED`: Chrome이 장치에 설치 되어 있지 않습니다. Sdk는 사용 가능한 경우 권한 부여 요청에 chrome 사용자 지정 탭을 사용 하 고 chrome 브라우저로 대체 합니다. </li><li>`USER_MISMATCH`: 토큰 획득 요청에 제공 된 사용자가 서버에서 반환 된 사용자와 일치 하지 않습니다.</li></ul>|이 예외 클래스는 라이브러리에 로컬인 일반적인 오류를 나타냅니다. 요청을 수정 하 여 이러한 예외를 처리할 수 있습니다.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: 사용자가 대화형 흐름을 시작 하 고 토큰을 받기 전에 요청을 취소 했습니다. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`:에 대해 Authority를 지정 해야 합니다 `acquireTokenSilent` .</li></ul>|이러한 오류는 개발자가 인수를 수정 하 고 대화형 인증, 완료 콜백, 범위 및 ID가 유효한 계정에 대 한 작업을 확인 하 여 완화할 수 있습니다.|


## <a name="catching-errors"></a>오류 catch

다음 코드 조각에서는 자동 호출 시 오류를 catch 하는 예를 보여 줍니다 `acquireToken` .

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>다음 단계 

[오류 로깅](https://docs.microsoft.com/azure/active-directory/develop/msal-logging?tabs=android) 에 대 한 자세한 정보 

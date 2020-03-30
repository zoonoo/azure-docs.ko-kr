---
title: 안드로이드에 대한 MSAL 마이그레이션 가이드에 ADAL | Azure
description: Azure Active Directory 인증 라이브러리(ADAL) Android 앱을 MSAL(Microsoft 인증 라이브러리)으로 마이그레이션하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084062"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>안드로이드에 대한 MSAL 마이그레이션 가이드에 ADAL

이 문서에서는 ADAL(Azure Active Directory 인증 라이브러리)을 사용하여 MSAL(Microsoft 인증 라이브러리)을 사용하는 앱을 마이그레이션하는 데 필요한 변경 사항을 간중합니다.

## <a name="difference-highlights"></a>차이 하이라이트

ADAL은 Azure Active Directory v1.0 끝점에서 작동합니다. MSAL(Microsoft 인증 라이브러리)은 이전에 Azure Active Directory v2.0 끝점으로 알려진 Microsoft ID 플랫폼과 함께 작동합니다. Microsoft ID 플랫폼은 다음과 같은 점에서 Azure Active Directory v1.0과 다릅니다.

지원:
  - 조직 ID(Azure Active 디렉터리)
  - Outlook.com, Xbox Live 등과 같은 비조직 ID
  - (B2C 만) 구글과 연합 로그인, 페이스 북, 트위터, 아마존

- 표준은 다음과 호환됩니다.
  - 오오스 v2.0
  - 오픈ID 커넥트(OIDC)

MSAL 공용 API는 다음과 같은 중요한 변경 사항을 소개합니다.

- 토큰에 액세스하기 위한 새로운 모델:
  - ADAL은 서버를 나타내는 `AuthenticationContext`을 통해 토큰에 대한 액세스를 제공합니다. MSAL은 클라이언트를 나타내는 `PublicClientApplication`을 통해 토큰에 대한 액세스를 제공합니다. 클라이언트 개발자는 상호 작용하는 데 `PublicClientApplication` 필요한 모든 기관에 대해 새 인스턴스를 만들 필요가 없습니다. 하나의 `PublicClientApplication` 구성만 필요합니다.
  - 리소스 식별자 외에 범위를 사용하여 액세스 토큰을 요청하는 지원입니다.
  - 증분 동의 지원. 개발자는 사용자가 앱 등록 중에 포함되지 않은 기능을 포함하여 앱에서 점점 더 많은 기능에 액세스함에 따라 범위를 요청할 수 있습니다.
  - 권한은 런타임에 더 이상 유효성을 검사하지 않습니다. 대신 개발자는 개발 중에 '알려진 권한' 목록을 선언합니다.
- 토큰 API 변경 사항:
  - ADAL에서 `AcquireToken()` 먼저 자동 요청을 합니다. 실패하면 대화형 요청이 됩니다. 이로 인해 일부 개발자는 만 의존하게 `AcquireToken`되었고, 이로 인해 사용자는 예기치 않게 자격 증명에 대한 메시지가 표시되었습니다. MSAL은 사용자가 UI 프롬프트를 받을 때 개발자가 의도적이어야 합니다.
    - `AcquireTokenSilent`항상 성공하거나 실패하는 자동 요청이 발생합니다.
    - `AcquireToken`항상 UI를 통해 사용자에게 메시지를 표시하는 요청이 발생합니다.
- MSAL은 기본 브라우저 또는 포함된 웹 보기에서 로그인을 지원합니다.
  - 기본적으로 장치의 기본 브라우저가 사용됩니다. 이를 통해 MSAL은 계정에 로그인한 하나 이상의 계정에 이미 있을 수 있는 인증 상태(쿠키)를 사용할 수 있습니다. 인증 상태가 없는 경우 MSAL을 통해 인증하는 동안 인증하면 동일한 브라우저에서 사용되는 다른 웹 응용 프로그램의 이점을 위해 인증 상태(쿠키)가 생성됩니다.
- 새 예외 모델:
  - 예외는 발생한 오류 유형과 이를 해결하기 위해 개발자가 수행해야 하는 작업을 보다 명확하게 정의합니다.
- MSAL은 에 `AcquireToken` 대한 `AcquireTokenSilent` 매개 변수 개체 및 호출을 지원합니다.
- MSAL은 다음에 대한 선언적 구성을 지원합니다.
  - 클라이언트 ID, URI 리디렉션.
  - 임베디드 대 기본 브라우저
  - 당국
  - 읽기 및 연결 시간 설정과 같은 HTTP 설정

## <a name="your-app-registration-and-migration-to-msal"></a>앱 등록 및 MSAL로의 마이그레이션

MSAL을 사용하기 위해 기존 앱 등록을 변경할 필요가 없습니다. 증분/점진적 동의를 활용하려면 등록을 검토하여 증분 요청하려는 특정 범위를 식별해야 할 수 있습니다. 범위 및 증분 동의에 대한 자세한 내용은 다음과 같습니다.

포털의 앱 등록에 API 권한 **탭이** 표시됩니다. 이렇게 하면 앱이 현재 액세스 요청을 요청하도록 구성된 API 및 권한(범위) 목록이 제공됩니다. 또한 각 API 권한과 연결된 범위 이름 목록도 표시됩니다.

### <a name="user-consent"></a>사용자 승인

ADAL 및 AAD v1 끝점을 사용하면 처음 사용할 때 자신이 소유한 리소스에 대한 사용자 동의가 부여되었습니다. MSAL 및 Microsoft ID 플랫폼을 사용하면 점진적으로 동의를 요청할 수 있습니다. 증분 동의는 사용자가 높은 권한을 고려할 수 있는 권한에 유용하거나 권한이 필요한 이유에 대한 명확한 설명과 함께 제공되지 않으면 의문을 제기할 수 있습니다. ADAL에서 이러한 권한으로 인해 사용자가 앱에 로그인을 포기했을 수 있습니다.

> [!TIP]
> 앱에 권한이 필요한 이유에 대한 추가 컨텍스트를 사용자에게 제공해야 하는 시나리오에서는 증분 동의를 사용하는 것이 좋습니다.

### <a name="admin-consent"></a>관리자 동의

조직 관리자는 조직의 모든 구성원을 대신하여 응용 프로그램에서 요구하는 권한에 동의할 수 있습니다. 일부 조직에서는 관리자가 응용 프로그램에 동의할 수 있도록 허용합니다. 관리자 동의를 얻으려면 앱 등록에 응용 프로그램에서 사용하는 모든 API 권한 및 범위를 포함해야 합니다.

> [!TIP]
> 앱 등록에 포함되지 않은 사항에 대해 MSAL을 사용하여 범위를 요청할 수 있지만 사용자가 권한을 부여할 수 있는 모든 리소스와 범위를 포함하도록 앱 등록을 업데이트하는 것이 좋습니다.

## <a name="migrating-from-resource-ids-to-scopes"></a>리소스 아이디에서 범위로 마이그레이션

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>처음 사용할 때 모든 사용 권한에 대한 인증 및 권한 부여 요청

현재 ADAL을 사용하고 있고 증분 동의를 사용할 필요가 없는 경우 MSAL 사용을 시작하는 가장 `acquireToken` 간단한 방법은 `AcquireTokenParameter` 새 개체를 사용하여 요청을 하고 리소스 ID 값을 설정하는 것입니다.

> [!CAUTION]
> 범위와 리소스 ID를 모두 설정할 수 없습니다. 둘 다 설정하려고 하면 `IllegalArgumentException`.

 이렇게 하면 사용된 것과 동일한 v1 동작이 발생합니다. 앱 등록에 요청된 모든 권한은 첫 번째 상호 작용 중에 사용자에게 요청됩니다.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>필요한 경우에만 사용 권한을 인증하고 요청합니다.

증분 동의를 활용하려면 앱이 앱 등록에서 사용하는 사용 권한(범위) 목록을 만들고 다음을 기반으로 두 개의 목록으로 구성합니다.

- 로그인하는 동안 사용자가 앱과 처음 상호 작용하는 동안 요청할 범위입니다.
- 앱의 중요한 기능과 연결된 권한으로 사용자에게도 설명해야 합니다.

범위를 구성한 후에는 토큰을 요청할 리소스(API)별로 각 목록을 구성합니다. 사용자가 동시에 권한을 부여하기를 원하는 다른 범위도 있습니다.

MSAL에 대한 요청을 만드는 데 사용되는 매개 변수 개체는 다음을 지원합니다.

- `Scope`: 액세스 토큰에 대한 권한 부여를 요청하고 받을 범위 목록입니다.
- `ExtraScopesToConsent`: 다른 리소스에 대한 액세스 토큰을 요청하는 동안 권한 부여를 요청하려는 추가 범위 목록입니다. 이 범위 목록을 사용하면 사용자 권한 부여를 요청하는 데 필요한 횟수를 최소화할 수 있습니다. 즉, 사용자 권한 부여 또는 동의 프롬프트가 줄어듭니다.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>인증컨텍스트에서 PublicClientapplication으로 마이그레이션

### <a name="constructing-publicclientapplication"></a>공용 클라이언트 응용 프로그램 생성

MSAL을 사용하면 을 인스턴스화합니다. `PublicClientApplication` 이 개체는 앱 ID를 모델로 하며 하나 이상의 기관에 요청을 하는 데 사용됩니다. 이 개체를 사용하면 클라이언트 ID를 구성하고 URI, 기본 권한, 장치 브라우저와 포함된 웹 보기, 로그 수준 등을 사용할지 여부를 구성합니다.

이 개체를 JSON으로 선언적으로 구성할 수 있습니다.이 개체는 파일로 제공하거나 APK 내의 리소스로 저장합니다.

이 개체는 단일 개체가 아니지만 내부적으로는 `Executors` 대화형 및 자동 요청 모두에 대해 공유를 사용합니다.

### <a name="business-to-business"></a>비즈니스 에서 비즈니스

ADAL에서 액세스 토큰을 요청하는 모든 조직에는 의 별도 `AuthenticationContext`인스턴스가 필요합니다. MSAL에서는 더 이상 요구 사항이 아닙니다. 자동 또는 대화형 요청의 일부로 토큰을 요청할 권한을 지정할 수 있습니다.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>권한 유효성 검사에서 알려진 기관으로 마이그레이션

MSAL에는 권한 유효성 검사를 사용하거나 사용하지 않도록 설정하는 플래그가 없습니다. 기관 유효성 검사는 ADAL의 기능이며 MSAL의 초기 릴리스에서는 코드가 잠재적으로 악의적인 기관에서 토큰을 요청하지 못하도록 합니다. 이제 MSAL은 Microsoft에 알려진 기관 목록을 검색하고 해당 목록을 구성에서 지정한 권한과 병합합니다.

> [!TIP]
> B2C(Azure Business to Consumer) 사용자인 경우 더 이상 권한 유효성 검사를 사용하지 않아도 됩니다. 대신 MSAL 구성에서 지원되는 각 Azure AD B2C 정책을 기관으로 포함합니다.

Microsoft에 알려지지 않고 구성에 포함되지 않은 권한을 사용하려고 하면 `UnknownAuthorityException`을 받게 됩니다.

### <a name="logging"></a>로깅
이제 다음과 같이 로깅을 구성의 일부로 선언적으로 구성할 수 있습니다.

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>UserInfo에서 계정으로 마이그레이션

ADAL에서 인증된 `UserInfo` 계정에 대 한 정보를 검색 하는 데 사용 되는 개체를 `AuthenticationResult` 제공 합니다. 인간 또는 소프트웨어 에이전트를 의미하는 "사용자"라는 용어는 일부 앱이 여러 계정이 있는 단일 사용자(사람이나 소프트웨어 에이전트)를 지원한다는 것을 전달하기 어렵게 만드는 방식으로 적용되었습니다.

은행 계좌를 생각해 보십시오. 두 개 이상의 금융 기관에 두 개 이상의 계정이 있을 수 있습니다. 계정을 개설하면 각 계정에 대해 잔액, 청구서 결제 등에 액세스하는 데 사용되는 ATM 카드 & PIN과 같은 자격 증명이 발급됩니다. 이러한 자격 증명은 발급한 금융 기관에서만 사용할 수 있습니다.

금융 기관의 계정과 마찬가지로 Microsoft ID 플랫폼의 계정은 자격 증명을 사용하여 액세스됩니다. 이러한 자격 증명은 Microsoft에 등록되거나 Microsoft에서 발급합니다. 또는 조직을 대신하여 Microsoft에 의해.

Microsoft ID 플랫폼이 금융 기관과 다른 경우 이 비유에서 Microsoft ID 플랫폼은 사용자가 하나의 계정과 관련 자격 증명을 사용하여 속한 리소스에 액세스할 수 있는 프레임워크를 제공한다는 것입니다. 여러 개인 및 조직. 이것은 한 은행에서 발행 한 카드를 사용할 수있는 것과 같습니다, 또 다른 금융 기관에서. 이는 문제의 모든 조직이 여러 조직에서 하나의 계정을 사용할 수 있는 Microsoft ID 플랫폼을 사용하고 있기 때문입니다. 예를 들면 다음과 같습니다.

Sam은 Contoso.com 위해 작동하지만 Fabrikam.com 속하는 Azure 가상 컴퓨터를 관리합니다. Sam이 Fabrikam의 가상 머신을 관리하려면 액세스 권한이 있어야 합니다. 이 액세스는 Fabrikam.com Sam의 계정을 추가하고 자신의 계정에 가상 컴퓨터로 작업할 수 있는 역할을 부여하여 부여할 수 있습니다. 이 작업은 Azure 포털에서 수행됩니다.

sam의 Contoso.com 계정을 Fabrikam.com 구성원으로 추가하면 Fabrikam.com의 Sam용 Azure 활성 디렉토리에서 새 레코드가 생성됩니다. Azure Active Directory에서 Sam의 레코드를 사용자 개체라고 합니다. 이 경우 해당 사용자 개체는 Contoso.com Sam의 사용자 개체를 가리킵니다. Sam의 Fabrikam 사용자 개체는 Sam의 로컬 표현이며 Fabrikam.com 컨텍스트에서 Sam과 연결된 계정에 대한 정보를 저장하는 데 사용됩니다. Contoso.com 샘의 직함은 수석 데브옵스 컨설턴트입니다. 파브리캄에서 샘의 제목은 계약자 가상 머신입니다. Contoso.com Sam은 가상 컴퓨터를 관리할 책임이 없으며 권한이 없습니다. Fabrikam.com, 그것은 그의 유일한 직업 기능입니다. 그러나 Sam은 여전히 추적할 자격 증명 집합이 하나뿐이며, 이는 Contoso.com 발급한 자격 증명입니다.

호출이 `acquireToken` 성공하면 이후 `acquireTokenSilent` 요청에서 사용할 수 `IAccount` 있는 개체에 대한 참조가 표시됩니다.

### <a name="imultitenantaccount"></a>IMulti테넌트 계정

계정이 표시되는 각 테넌자의 계정에 대한 클레임에 액세스하는 앱이 있는 경우 `IAccount` 개체를 `IMultiTenantAccount`로 캐스팅할 수 있습니다. 이 인터페이스는 현재 `ITenantProfiles`계정과 관련하여 토큰을 요청한 각 테넌트의 계정에 속한 클레임에 액세스할 수 있는 테넌트 ID로 키에 있는 의 맵을 제공합니다.

의 루트에 `IAccount` 클레임은 `IMultiTenantAccount` 항상 홈 테넌트의 클레임을 포함합니다. 홈 테넌트 내에서 토큰을 아직 요청하지 않은 경우 이 컬렉션은 비어 있습니다.

## <a name="other-changes"></a>기타 변경 내용

### <a name="use-the-new-authenticationcallback"></a>새 인증 콜백 사용

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>새 예외로 마이그레이션

ADAL에는 열거형 값을 검색하는 `AuthenticationException`메서드를 포함하는 한 가지 `ADALError` 유형의 예외가 있습니다.
MSAL에는 예외 계층구조가 있으며 각 계층에는 고유한 관련 특정 오류 코드 집합이 있습니다.

MSAL 예외 목록

|예외  | 설명  |
|---------|---------|
| `MsalException`     | MSAL에서 throw된 기본 검사 예외입니다.  |
| `MsalClientException`     | 오류가 클라이언트 측인 경우 throw됩니다. |
| `MsalArgumentException`     | 하나 이상의 입력 인수가 유효하지 않은 경우 throw됩니다. |
| `MsalClientException`     | 오류가 클라이언트 측인 경우 throw됩니다. |
| `MsalServiceException`     | 오류가 서버 측인 경우 throw됩니다. |
| `MsalUserCancelException`     | 사용자가 인증 흐름을 취소한 경우 throw됩니다.  |
| `MsalUiRequiredException`     | 토큰을 자동으로 새로 고칠 수 없는 경우 throw합니다.  |
| `MsalDeclinedScopeException`     | 서버에서 요청한 범위가 하나 이상 거부된 경우 throw됩니다.  |
| `MsalIntuneAppProtectionPolicyRequiredException` | 리소스에 MAMCA 보호 정책이 활성화되어 있는 경우 throw됩니다. |

### <a name="adalerror-to-msalexception-errorcode"></a>MsalException 오류 코드에 대한 ADALError

### <a name="adal-logging-to-msal-logging"></a>MSAL 로깅에 대한 ADAL 로깅

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```

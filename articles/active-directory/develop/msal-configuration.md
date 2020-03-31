---
title: 안드로이드 MSAL 구성 파일 | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory에서 응용 프로그램의 구성을 나타내는 MSAL(Android Microsoft 인증 라이브러리) 구성 파일의 개요입니다.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 9e35ba5a3f3705a52e80262da9bbfbfda489bf83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050368"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>안드로이드 마이크로 소프트 인증 라이브러리 구성 파일

MSAL(Android Microsoft 인증 라이브러리)은 기본 기관, 사용할 권한 등과 같은 공용 클라이언트 앱의 동작을 정의하도록 사용자 지정하는 [기본 구성 JSON 파일과](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) 함께 제공됩니다.

이 문서에서는 구성 파일의 다양한 설정과 MSAL 기반 앱에서 사용할 구성 파일을 지정하는 방법을 이해하는 데 도움이 됩니다.

## <a name="configuration-settings"></a>구성 설정

### <a name="general-settings"></a>일반 설정

| 속성 | 데이터 형식 | 필수 | 메모 |
|-----------|------------|-------------|-------|
| `client_id` | String | yes | 응용 프로그램 [등록 페이지에서](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 앱의 클라이언트 ID |
| `redirect_uri`   | String | yes | [응용 프로그램 등록 페이지에서](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 앱의 리디렉션 URI |
| `authorities` | 목록\<기관> | 예 | 앱에 필요한 권한 목록 |
| `authorization_user_agent` | 권한 부여 에이전트(열거형) | 예 | 가능한 `DEFAULT`값: `BROWSER`,`WEBVIEW` |
| `http` | http구성 | 예 | 구성 `HttpUrlConnection` `connect_timeout` 및`read_timeout` |
| `logging` | 로깅 구성 | 예 | 로깅 세부 정보 수준을 지정합니다. 선택적 구성에는 `pii_enabled`부울 값을 받는 및 `log_level`에 대해 `ERROR` `WARNING` `INFO`수행합니다. `VERBOSE` |

### <a name="client_id"></a>client_id

응용 프로그램을 등록할 때 생성된 클라이언트 ID 또는 앱 ID입니다.

### <a name="redirect_uri"></a>redirect_uri

응용 프로그램을 등록할 때 등록한 리디렉션 URI입니다. 리디렉션 URI가 브로커 앱인 경우 [공용 클라이언트 앱에 대한 리디렉션 URI를](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) 참조하여 브로커 앱에 올바른 리디렉션 URI 형식을 사용하고 있는지 확인합니다.

### <a name="authorities"></a>당국

귀하가 알고 신뢰하는 기관 목록입니다. 여기에 나열된 권한 외에도 MSAL은 Microsoft에 알려진 클라우드 및 권한 목록을 얻기 위해 Microsoft에 쿼리합니다. 이 권한 목록에서 권한의 유형과 앱 등록에 따라 앱의 잠재 고객과 일치해야 하는 '와 같은 `"audience"`추가 선택적 매개 변수를 지정합니다. 다음은 당국의 예목록입니다.

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>AAD 권한 & Microsoft ID 플랫폼 엔드포인트에 매핑

| Type | 사용자 | 테넌트 ID | Authority_Url | 결과 끝점 | 메모 |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADand개인마이크로소프트계정 | | | `https://login.microsoftonline.com/common` | `common`는 계정이 있는 위치에 대한 테넌트 별칭입니다. 특정 Azure Active Directory 테넌트 또는 Microsoft 계정 시스템과 같은 시스템입니다. |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | contoso.com 존재하는 계정만 토큰을 획득할 수 있습니다. 확인된 도메인 또는 테넌트 GUID를 테넌트 ID로 사용할 수 있습니다. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | 이 끝점에서는 Azure Active Directory 계정만 사용할 수 있습니다. Microsoft 계정은 조직의 구성원일 수 있습니다. 조직의 리소스에 대한 Microsoft 계정을 사용하여 토큰을 획득하려면 토큰을 원하는 조직 테넌트를 지정합니다. |
| AAD | 개인마이크로소프트계정 | | | `https://login.microsoftonline.com/consumers` | Microsoft 계정만 이 끝점을 사용할 수 있습니다. |
| B2C | | | 결과 끝점 참조 | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | contoso.onmicrosoft.com 테넌트에 있는 계정만 토큰을 획득할 수 있습니다. 이 예제에서 B2C 정책은 기관 URL 경로의 일부입니다. |

> [!NOTE]
> MSAL에서는 기관 유효성 검사를 사용하도록 설정하고 비활성화할 수 없습니다.
> 권한 은 구성을 통해 지정된 개발자로 알려져 있거나 메타데이터를 통해 Microsoft에 알려져 있습니다.
> MSAL이 알 수 없는 기관에 토큰 에 `MsalClientException` 대한 `UnknownAuthority` 요청을 받으면 형식이 발생합니다.

#### <a name="authority-properties"></a>기관 속성

| 속성 | 데이터 형식  | 필수 | 메모 |
|-----------|-------------|-----------|--------|
| `type` | String | yes | 앱 대상을 입력한 대상 또는 계정을 미러합니다. 가능한 값: `AAD``B2C` |
| `audience` | Object | 예 | type=`AAD`. 앱이 대상으로 하는 ID를 지정합니다. 앱 등록의 값 사용 |
| `authority_url` | String | yes | 형식=`B2C`. 앱에서 사용해야 하는 권한 URL 또는 정책을 지정합니다.  |
| `default` | boolean | yes | 하나 `"default":true` 이상의 권한을 지정하면 단일 이 필요합니다. |

#### <a name="audience-properties"></a>잠재고객 속성

| 속성 | 데이터 형식  | 필수 | 메모 |
|-----------|-------------|------------|-------|
| `type` | String | yes | 앱에서 타겟팅할 대상을 지정합니다. 가능한 `AzureADandPersonalMicrosoftAccount`값: `PersonalMicrosoftAccount` `AzureADMultipleOrgs`,`AzureADMyOrg` |
| `tenant_id` | String | yes | . `"type":"AzureADMyOrg"` 다른 `type` 값에 대한 선택 사항입니다. 이 도메인은 와 같은 `contoso.com`테넌트 도메인또는 `72f988bf-86f1-41af-91ab-2d7cd011db46`테넌트 ID와 같은 것일 수 있습니다. |

### <a name="authorization_user_agent"></a>authorization_user_agent

계정에 로그인하거나 리소스에 대한 액세스 권한을 부여할 때 포함된 웹뷰또는 장치의 기본 브라우저를 사용할지 여부를 나타냅니다.

가능한 값은 다음과 같습니다.
- `DEFAULT`: 시스템 브라우저를 선호합니다. 기기에서 브라우저를 사용할 수 없는 경우 포함된 웹 보기를 사용합니다.
- `WEBVIEW`: 임베디드 웹 뷰를 사용합니다.
- `BROWSER`: 기기의 기본 브라우저를 사용합니다.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

여러 국가 클라우드를 지원하는 `true`클라이언트의 경우 을 지정합니다. 그러면 Microsoft ID 플랫폼은 권한 부여 및 토큰 사용 중에 올바른 국가 클라우드로 자동으로 리디렉션됩니다. `AuthenticationResult`과 관련된 권한을 검사하여 로그인된 계정의 국가 클라우드를 확인할 수 있습니다. 이 `AuthenticationResult` 토큰은 토큰을 요청하는 리소스의 국가 클라우드 별 끝점 주소를 제공하지 않습니다.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Microsoft ID 브로커 호환 브로커에서 URI를 리디렉션할지 여부를 나타내는 부울입니다. 앱 `false` 내에서 브로커를 사용하지 않으려는 경우로 설정합니다.

잠재 고객을 설정한 `"MicrosoftPersonalAccount"`AAD 기관을 사용하는 경우 브로커는 사용되지 않습니다.

### <a name="http"></a>http

다음과 같은 HTTP 시간 시간에 대한 전역 설정을 구성합니다.

| 속성 | 데이터 형식 | 필수 | 메모 |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | 예 | 시간(밀리초) |
| `read_timeout` | int | 예 | 시간(밀리초) |

### <a name="logging"></a>logging

로깅을 위한 다음 전역 설정은 다음과 같습니다.

| 속성 | 데이터 형식  | 필수 | 메모 |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | 예 | 개인 데이터 내사 여부 |
| `log_level`   | boolean | 예 | 출력할 로그 메시지 |
| `logcat_enabled` | boolean | 예 | 로깅 인터페이스 이외에 로그 cat에 출력할지 여부 |

### <a name="account_mode"></a>account_mode

한 번에 앱 내에서 사용할 수 있는 계정 수를 지정합니다. 사용 가능한 값은

- `MULTIPLE`(기본값)
- `SINGLE`

이 설정과 `PublicClientApplication` 일치하지 않는 계정 모드를 사용하여 생성하면 예외가 발생합니다.

단일 계정과 여러 계정 간의 차이점에 대한 자세한 내용은 [단일 및 여러 계정 앱을](single-multi-account.md)참조하십시오.

### <a name="browser_safelist"></a>browser_safelist

MSAL과 호환되는 브라우저의 허용 목록입니다. 이러한 브라우저는 사용자 지정 의도로 리디렉션을 올바르게 처리합니다. 이 목록에 추가할 수 있습니다. 기본값은 아래 표시된 기본 구성으로 제공됩니다.
``
## <a name="the-default-msal-configuration-file"></a>기본 MSAL 구성 파일

MSAL과 함께 제공되는 기본 MSAL 구성은 다음과 같습니다. 최신 버전은 [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)에서 볼 수 있습니다.

이 구성은 제공한 값으로 보완됩니다. 제공한 값이 기본값을 재정의합니다.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>기본 구성 예

다음 예제에서는 클라이언트 ID를 지정하는 기본 구성, URI 리디렉션, 브로커 리디렉션등록 여부 및 권한 목록을 보여 줍니다.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>구성 파일 사용 방법

1. 구성 파일을 만듭니다. 에서 `res/raw/auth_config.json`사용자 지정 구성 파일을 만드는 것이 좋습니다. 그러나 원하는 곳에 넣을 수 있습니다.
2. 을 구성할 때 구성을 찾을 위치를 `PublicClientApplication`MSAL에 알릴 수 있습니다. 예를 들어:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```

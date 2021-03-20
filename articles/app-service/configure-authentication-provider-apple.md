---
title: Apple (미리 보기)을 사용 하 여 로그인 구성
description: Apple에서 로그인을 App Service 또는 Azure Functions 앱에 대 한 id 공급자로 구성 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96603360"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>Apple 공급자 (미리 보기)를 사용 하 여 로그인을 사용 하 여 로그인 하도록 App Service 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Azure App Service 또는 Azure Functions를 구성 하 여 Apple에서 인증 공급자로 로그인을 사용 하는 방법을 보여 줍니다. 

이 문서의 절차를 완료 하려면 Apple 개발자 프로그램에 등록 되어 있어야 합니다. Apple 개발자 프로그램에 등록 하려면 [developer.apple.com/programs/enroll](https://developer.apple.com/programs/enroll/)로 이동 합니다.

> [!CAUTION]
> Apple에서 로그인을 사용 하도록 설정 하면 Azure Portal, Azure CLI, Azure PowerShell 등의 일부 클라이언트를 통해 응용 프로그램에 대 한 App Service 인증/권한 부여 기능의 관리가 사용 되지 않습니다. 이 기능은 미리 보기 중에 모든 관리 환경에서 아직 고려 되지 않는 새 API 화면을 사용 합니다.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Apple 개발자 포털에서 응용 프로그램 만들기
Apple 개발자 포털에서 앱 ID 및 서비스 ID를 만들어야 합니다.

1. Apple 개발자 포털에서 **인증서, 식별자, & 프로필** 로 이동 합니다.
2. **식별자** 탭에서 **(+)** 단추를 선택 합니다.
3. **새 식별자 등록** 페이지에서 **앱 id** 를 선택 하 고 **계속** 을 선택 합니다. (앱 Id에는 서비스 Id가 하나 이상 포함 되어 있습니다.) ![Apple 개발자 포털에서 새 앱 식별자 등록](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. **앱 Id 등록** 페이지에서 설명 및 번들 id를 제공 하 고 기능 목록에서 **Apple으로 로그인** 을 선택 합니다. 그런 다음, **계속** 을 선택합니다. 이 단계에서 **앱 Id 접두사 (팀 id)** 를 기록해 둡니다. 나중에 필요 합니다.
![Apple 개발자 포털에서 새 앱 식별자 구성](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. 앱 등록 정보를 검토 하 고 **등록** 을 선택 합니다.
6. 다시 **식별자** 탭에서 **(+)** 단추를 선택 합니다.
![Apple 개발자 포털에서 새 서비스 식별자 만들기](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. **새 식별자 등록** 페이지에서 **서비스 id** 를 선택 하 고 **계속** 을 선택 합니다.
![Apple 개발자 포털에서 새 서비스 식별자 등록](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. **서비스 ID 등록** 페이지에서 설명 및 식별자를 제공 합니다. 설명은 동의 화면에서 사용자에 게 표시 되는 내용입니다. 식별자는 app service를 사용 하 여 Apple 공급자를 구성 하는 데 사용 되는 클라이언트 ID가 됩니다. 그런 다음, **구성** 을 선택합니다.
![설명 및 식별자 제공](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. 팝업 창에서 기본 앱 Id를 앞에서 만든 앱 Id로 설정 합니다. 도메인 섹션에서 응용 프로그램의 도메인을 지정 합니다. 반환 URL의 경우 URL을 사용 `<app-url>/.auth/login/apple/callback` 합니다. 예: `https://contoso.azurewebsites.net/.auth/login/apple/callback` 그런 다음 **추가** 및 **저장** 을 선택 합니다.
![등록을 위한 도메인 및 반환 URL 지정](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. 서비스 등록 정보를 검토 하 고 **저장** 을 선택 합니다.

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>클라이언트 암호를 생성 합니다.
Apple에서는 응용 프로그램 개발자가 JWT 토큰을 클라이언트 암호 값으로 만들고 서명 해야 합니다. 이 비밀을 생성 하려면 먼저 Apple 개발자 포털에서 타원 curve 개인 키를 생성 하 고 다운로드 합니다. 그런 다음 해당 키를 사용 하 여 [특정 페이로드에](#structure-the-client-secret-jwt) [JWT를 서명](#sign-the-client-secret-jwt) 합니다.

### <a name="create-and-download-the-private-key"></a>개인 키 만들기 및 다운로드
1. Apple 개발자 포털의 **키** 탭에서 **키 만들기** 를 선택 하거나 **(+)** 단추를 선택 합니다.
2. **새 키 등록** 페이지에서 키 이름을 지정 하 고 **Apple에 로그인** 옆의 확인란을 선택 하 고 **구성** 을 선택 합니다.
3. **키 구성** 페이지에서 이전에 만든 기본 앱 ID에 키를 연결 하 고 **저장** 을 선택 합니다.
4. 정보를 확인 하 고 **계속** 을 선택한 다음 정보를 검토 하 고 **등록** 을 선택 하 여 키 만들기를 완료 합니다.
5. **키 다운로드** 페이지에서 키를 다운로드 합니다. `.p8`(PKCS # 8) 파일로 다운로드 됩니다. 파일 콘텐츠를 사용 하 여 클라이언트 암호 JWT에 서명 합니다.

### <a name="structure-the-client-secret-jwt"></a>클라이언트 암호 JWT 구조
Apple에서는 JWT 토큰의 base64 인코딩으로 클라이언트 암호를 요구 합니다. 디코딩된 JWT 토큰의 페이로드는 다음 예제와 같이 구성 되어야 합니다.
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **sub**: APPLE 클라이언트 id (서비스 id 이기도)
- **iss**: Apple 개발자 팀 ID
- **aud**: Apple에서 토큰을 수신 하 고 있으므로 대상 사용자입니다.
- **exp**: **nbf** 후 6 개월 이내

위의 페이로드에 대 한 b a s e 64로 인코딩된 버전은 다음과 같습니다. ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_참고: Apple은 만든 (또는 nbf) 날짜 이후에 만료 날짜가 6 개월 이상인 클라이언트 암호 jwt를 허용 하지 않습니다. 즉, 최소 6 개월 마다 클라이언트 암호를 회전 해야 합니다._

토큰 생성 및 유효성 검사에 대 한 자세한 내용은 [Apple 개발자 설명서](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)에서 확인할 수 있습니다. 

### <a name="sign-the-client-secret-jwt"></a>클라이언트 암호 JWT 서명
`.p8`이전에 다운로드 한 파일을 사용 하 여 클라이언트 암호 JWT에 서명 합니다. 이 파일은 PEM 형식의 개인 서명 키를 포함 하는 [Pcks # 8 파일](https://en.wikipedia.org/wiki/PKCS_8) 입니다. JWT를 만들고 서명할 수 있는 많은 라이브러리가 있습니다. 

JWT 토큰을 만들고 서명 하기 위해 온라인에서 사용할 수 있는 다양 한 종류의 오픈 소스 라이브러리가 있습니다. JWT 토큰을 생성 하는 방법에 대 한 자세한 내용은 jwt.io를 참조 하세요. 예를 들어 클라이언트 암호를 생성 하는 한 가지 방법은 [System.identitymodel NuGet 패키지](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) 를 가져오고 아래와 같이 작은 양의 c # 코드를 실행 하는 것입니다.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **Teamid**: Apple 개발자 팀 id
- **clientId**: APPLE 클라이언트 id (서비스 id 이기도)
- **p8key**: PEM 서식 키- `.p8` 텍스트 편집기에서 파일을 열고 `-----BEGIN PRIVATE KEY-----` `-----END PRIVATE KEY-----` 줄 바꿈 없이 또는 줄 바꿈 없이 모든 항목을 복사 하 여 키를 가져올 수 있습니다.
- **keyId**: 다운로드 한 키의 ID

반환 된이 토큰은 Apple 공급자를 구성 하는 데 사용할 클라이언트 암호 값입니다.

> [!IMPORTANT]
> 클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.
>

선택한 설정 이름을 사용 하 여 앱에 대 한 [응용 프로그램 설정](./configure-common.md#configure-app-settings) 으로 클라이언트 암호를 추가 합니다. 나중에이 이름을 적어 둡니다.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>응용 프로그램에 공급자 정보 추가

> [!NOTE]
> 필요한 구성은 새 API 형식으로, 현재는 [파일 기반 구성 (미리 보기)](.\app-service-authentication-how-to.md#config-file)에서만 지원 됩니다. 이러한 파일을 사용 하 여 아래 단계를 수행 해야 합니다.

이 섹션에서는 새 IDP을 포함 하도록 구성을 업데이트 하는 과정을 안내 합니다. 구성 예제는 다음과 같습니다.

1. 개체가 없는 경우 개체 내에 `identityProviders` 개체를 추가 `apple` 합니다.
2. 개체를 포함 하 `registration` 고 필요에 따라 개체를 사용 하 여 개체를 해당 키에 할당 합니다 `login` .
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. 개체 내에서를 `registration` `clientId` 수집한 클라이언트 ID로 설정 합니다.
    
    b. 개체 내에서 `registration` 를 `clientSecretSettingName` 클라이언트 암호를 저장 한 응용 프로그램 설정의 이름으로 설정 합니다.
    
    다. 개체 내에서 `login` `scopes` Apple에서 인증할 때 사용 되는 범위 목록 (예: "이름" 및 "전자 메일")을 포함 하도록 배열을 설정 하도록 선택할 수 있습니다. 범위가 구성 된 경우 사용자가 처음으로 로그인 할 때 동의 화면에서 명시적으로 요청 됩니다.

이 구성이 설정 되 면 앱에서 Apple 공급자를 인증에 사용할 준비가 된 것입니다.

전체 구성은 다음 예제와 같이 보일 수 있습니다. 여기서 APPLE_GENERATED_CLIENT_SECRET 설정은 생성 된 JWT를 포함 하는 응용 프로그램 설정을 가리킵니다.

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

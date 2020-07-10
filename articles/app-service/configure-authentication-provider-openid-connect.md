---
title: Openid connect Connect 공급자 구성 (미리 보기)
description: App Service 또는 Azure Functions 앱에 대 한 id 공급자로 Openid connect Connect 공급자를 구성 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: 1a4f956c15fae640c2a7978a14bb95328dc9aa71
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209630"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Openid connect Connect 공급자를 사용 하 여 로그인 하도록 App Service 또는 Azure Functions 앱 구성 (미리 보기)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 [Openid connect Connect 사양을](https://openid.net/connect/)준수 하는 사용자 지정 인증 공급자를 사용 하도록 Azure App Service 또는 Azure Functions를 구성 하는 방법을 보여 줍니다. OIDC (Openid connect Connect)는 IDPs (여러 id 공급자)에서 사용 하는 업계 표준입니다. Adherent IDP를 사용 하도록 앱을 구성 하기 위해 사양에 대 한 세부 정보를 이해할 필요가 없습니다.

는 하나 이상의 OIDC 공급자를 사용 하도록 앱을 구성할 수 있습니다. 각각은 구성에서 고유한 이름을 지정 해야 하며, 하나만 기본 리디렉션 대상으로 사용할 수 있습니다.

> [!CAUTION]
> Openid connect Connect 공급자를 사용 하도록 설정 하면 Azure Portal, Azure CLI, Azure PowerShell 등의 일부 클라이언트를 통해 응용 프로그램에 대 한 App Service 인증/권한 부여 기능을 관리할 수 없습니다. 이 기능은 미리 보기 중에 모든 관리 환경에서 아직 고려 되지 않는 새 API 화면을 사용 합니다.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Id 공급자를 사용 하 여 응용 프로그램 등록

공급자는 응용 프로그램의 세부 정보를 등록 해야 합니다. 해당 공급자와 관련 된 지침을 참조 하세요. 응용 프로그램에 대 한 **클라이언트 ID** 및 **클라이언트 암호** 를 수집 해야 합니다.

> [!IMPORTANT]
> 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.
>

> [!NOTE]
> 일부 공급자에는 구성에 대 한 추가 단계와 해당 구성에 제공 된 값을 사용 하는 방법이 필요할 수 있습니다. 예를 들어 Apple은 OIDC 클라이언트 암호로 사용 되지 않는 개인 키를 제공 하 고 대신 앱 구성에서 제공 하는 비밀으로 처리 되는 JWT를 사용 해야 합니다 ( [apple 설명서를 사용 하 여 로그인](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)의 "클라이언트 암호 만들기" 섹션 참조).
>

선택한 설정 이름을 사용 하 여 앱에 대 한 [응용 프로그램 설정](./configure-common.md#configure-app-settings) 으로 클라이언트 암호를 추가 합니다. 나중에이 이름을 적어 둡니다.

또한 공급자에 대 한 Openid connect Connect 메타 데이터가 필요 합니다. 이는 대개 공급자의 발급자 URL 접미사가 인 [구성 메타 데이터 문서](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)를 통해 노출 됩니다 `/.well-known/openid-configuration` . 이 구성 URL을 수집 합니다.

구성 메타 데이터 문서를 사용할 수 없는 경우 다음 값을 별도로 수집 해야 합니다.

- 발급자 URL (로 표시 되는 경우 `issuer` )
- [OAuth 2.0 권한 부여 끝점](https://tools.ietf.org/html/rfc6749#section-3.1) (경우에 따라 `authorization_endpoint` )
- [OAuth 2.0 토큰 끝점](https://tools.ietf.org/html/rfc6749#section-3.2) (간혹로 표시 됨 `token_endpoint` )
- [OAuth 2.0 JSON 웹 키 집합](https://tools.ietf.org/html/rfc8414#section-2) 문서의 URL (로 표시 되는 경우도 있음 `jwks_uri` )

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>응용 프로그램에 공급자 정보 추가

> [!NOTE]
> 필요한 구성은 새 API 형식으로, 현재는 [파일 기반 구성 (미리 보기)](.\app-service-authentication-how-to.md#config-file)에서만 지원 됩니다. 이러한 파일을 사용 하 여 아래 단계를 수행 해야 합니다.

이 섹션에서는 새 IDP을 포함 하도록 구성을 업데이트 하는 과정을 안내 합니다. 구성 예제는 다음과 같습니다.

1. 개체가 없는 경우 개체 내에 `identityProviders` 개체를 추가 `openIdConnectProviders` 합니다.
1. 개체 내에서 `openIdConnectProviders` 새 공급자에 대 한 키를 추가 합니다. 나머지 구성에서 공급자를 참조 하는 데 사용 되는 이름입니다. 예를 들어이 공급자를 사용 하 여 모든 요청을 인증 해야 하는 경우 `globalValidation.unauthenticatedClientAction` 를 "RedirectToLoginPage"로 설정 하 고 `globalValidation.unauthenticatedClientAction` 이 같은 친숙 한 이름으로 설정 합니다.
1. 개체를 포함 하 `registration` 고 필요에 따라 개체를 사용 하 여 개체를 해당 키에 할당 합니다 `login` .
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "loginScopes": [],
             "loginParameterNames": [],
       }
    }
    ```

1. 등록 개체 내에서를 `clientId` 수집한 클라이언트 ID로 설정 하 `clientCredential.secretSettingName` 고, 클라이언트 암호를 저장 한 응용 프로그램 설정의 이름으로 설정 하 고, 개체를 만듭니다 `openIdConnectConfiguration` .

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. 개체 내에서 `openIdConnectConfiguration` 앞서 수집한 Openid connect Connect 메타 데이터를 제공 합니다. 수집 된 정보에 따라 다음과 같은 두 가지 옵션을 사용할 수 있습니다.

    - 속성을 `wellKnownOpenIdConfiguration` 앞에서 수집한 구성 메타 데이터 URL로 설정 합니다.
    - 또는 다음과 같이 수집 된 네 개의 개별 값을 설정 합니다.
        - `issuer`발급자 URL로 설정 합니다.
        - `authorizationEndpoint`권한 부여 끝점으로 설정 합니다.
        - `tokenEndpoint`토큰 끝점으로 설정 합니다.
        - `certificationUri`JSON 웹 키 집합 문서의 URL로 설정 합니다.

    이러한 두 옵션은 함께 사용할 수 없습니다.

이 구성을 설정 하면 앱에서 인증에 Openid connect Connect 공급자를 사용할 준비가 된 것입니다.

예제 구성은 다음과 같이 표시 될 수 있습니다. 예를 들어 Apple과 함께 로그인을 사용 하 여 APPLE_GENERATED_CLIENT_SECRET 설정이 [apple 설명서](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)당 생성 된 JWT를 가리키도록 합니다.

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
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scope": [],
                    "loginParameterNames": []
                }
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

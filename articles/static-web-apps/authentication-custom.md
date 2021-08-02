---
title: Azure Static Web Apps의 사용자 지정 인증
description: Azure Static Web Apps에 대한 사용자 지정 인증을 구성하는 방법
services: static-web-apps
author: aaronpowell
ms.author: aapowell
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: e4583c6474872cc1de909d86d812aa9ac9630536
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854579"
---
# <a name="custom-authentication-in-azure-static-web-apps"></a>Azure Static Web Apps의 사용자 지정 인증

Azure Static Web Apps는 Azure에서 관리하는 공급자 등록을 사용하는 [관리형 인증](authentication-authorization.md)을 제공합니다. 등록에 대한 유연성을 높일 수 있도록 사용자 지정 등록을 사용하여 기본값을 재정의할 수 있습니다.

- 사용자 지정 인증을 사용하면 [OpenID Connect](https://openid.net/connect/)를 지원하는 [사용자 지정 공급자를 구성](#configure-a-custom-openid-connect-provider)할 수도 있습니다. 이 구성을 사용하면 여러 외부 공급자를 등록할 수 있습니다.

- 사용자 지정 등록을 사용하면 미리 구성된 모든 공급자가 사용하지 않도록 설정됩니다.

- 특히 AAD(Azure Active Directory) 등록의 경우 그룹 관리를 위한 [초대 흐름](./authentication-authorization.md#role-management)을 무시할 수 있는 테넌트 제공 옵션이 있습니다.

> [!NOTE]
> 사용자 지정 인증은 Azure Static Web Apps 표준 플랜에서만 사용할 수 있습니다.

## <a name="override-pre-configured-provider"></a>미리 구성된 공급자 재정의

공급자를 재정의하는 데 사용되는 설정은 [구성 파일](configuration.md)의 `auth` 섹션에서 구성됩니다.

소스 제어에 비밀을 포함시키지 않으려면 구성은 [애플리케이션 설정](application-settings.md)에서 구성 파일의 일치하는 이름을 찾습니다. [Azure Key Vault](./key-vault-secrets.md)에 비밀을 저장하도록 선택할 수도 있습니다.

### <a name="configuration"></a>구성

다음 표에는 각 공급자에 대한 다양한 구성 옵션이 포함되어 있습니다.

# <a name="azure-active-directory"></a>[Azure Active Directory](#tab/aad)

| 필드 경로                             | Description                                                                                                               |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `registration.openIdIssuer`            | AAD 테넌트의 OpenID 구성에 대한 엔드포인트입니다.                                                  |
| `registration.clientIdSettingName`     | Azure AD 앱 등록에 대한 애플리케이션(클라이언트) ID를 포함하는 애플리케이션 설정의 이름입니다. |
| `registration.clientSecretSettingName` | Azure AD 앱 등록에 대한 클라이언트 비밀을 포함하는 애플리케이션 설정의 이름입니다.           |

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>",
          "clientIdSettingName": "<AAD_CLIENT_ID>",
          "clientSecretSettingName": "<AAD_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

등록 구성 방식에 영향을 주는 Azure Active Directory 기능 버전이 있는 엔드포인트입니다. AAD v1을 사용하는 경우(발급자 엔드포인트가 "/v2.0"로 끝나지 않음) `"azureActiveDirectory"` 개체의 구성에 다음 `userDetailsClaim` 항목을 추가해야 합니다.

```json
"azureActiveDirectory": {
  "registration": { ... },
  "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" 
}
```

Azure Active Directory 구성 방법에 대한 자세한 내용은 [App Service 인증/권한 부여 설명서](../app-service/configure-authentication-provider-aad.md)를 참조하세요.

# <a name="apple"></a>[Apple](#tab/apple)

| 필드 경로                             | Description                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `registration.clientIdSettingName`     | 클라이언트 ID를 포함하는 애플리케이션 설정의 이름입니다.                                       |
| `registration.clientSecretSettingName` | 클라이언트 암호가 포함된 애플리케이션 설정의 이름입니다.                                   |

```json
{
  "auth": {
    "identityProviders": {
      "apple": {
        "registration": {
          "clientIdSettingName": "<APPLE_CLIENT_ID>",
          "clientSecretSettingName": "<APPLE_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

Apple을 인증 공급자로 구성하는 방법에 대한 자세한 내용은 [App Service 인증/권한 부여 설명서](../app-service/configure-authentication-provider-apple.md)를 참조하세요.

# <a name="facebook"></a>[Facebook](#tab/facebook)

| 필드 경로                          | Description                                                                            |
| ----------------------------------- | -------------------------------------------------------------------------------------- |
| `registration.appIdSettingName`     | 앱 ID를 포함하는 애플리케이션 설정의 이름입니다.                             |
| `registration.appSecretSettingName` | 앱 비밀을 포함하는 애플리케이션 설정의 이름입니다.                         |

```json
{
  "auth": {
    "identityProviders": {
      "facebook": {
        "registration": {
          "appIdSettingName": "<FACEBOOK_APP_ID>",
          "appSecretSettingName": "<FACEBOOK_APP_SECRET>"
        }
      }
    }
  }
}
```

Facebook을 인증 공급자로 구성하는 방법에 대한 자세한 내용은 [App Service 인증/권한 부여 설명서](../app-service/configure-authentication-provider-facebook.md)를 참조하세요.

# <a name="github"></a>[GitHub](#tab/github)

| 필드 경로                             | Description                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `registration.clientIdSettingName`     | 클라이언트 ID를 포함하는 애플리케이션 설정의 이름입니다.                                |
| `registration.clientSecretSettingName` | 클라이언트 암호가 포함된 애플리케이션 설정의 이름입니다.                            |

```json
{
  "auth": {
    "identityProviders": {
      "github": {
        "registration": {
          "clientIdSettingName": "<GITHUB_CLIENT_ID>",
          "clientSecretSettingName": "<GITHUB_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

# <a name="google"></a>[Google](#tab/google)

| 필드 경로                             | Description                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `registration.clientIdSettingName`     | 클라이언트 ID를 포함하는 애플리케이션 설정의 이름입니다.                                |
| `registration.clientSecretSettingName` | 클라이언트 암호가 포함된 애플리케이션 설정의 이름입니다.                            |

```json
{
  "auth": {
    "identityProviders": {
      "google": {
        "registration": {
          "clientIdSettingName": "<GOOGLE_CLIENT_ID>",
          "clientSecretSettingName": "<GOOGLE_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

Google을 인증 공급자로 구성하는 방법에 대한 자세한 내용은 [App Service 인증/권한 부여 설명서](../app-service/configure-authentication-provider-google.md)를 참조하세요.

# <a name="twitter"></a>[Twitter](#tab/twitter)

| 필드 경로                               | Description                                                                                        |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `registration.consumerKeySettingName`    | 소비자 키를 포함하는 애플리케이션 설정의 이름입니다.                                   |
| `registration.consumerSecretSettingName` | 소비자 비밀을 포함하는 애플리케이션 설정의 이름입니다.                                |

```json
{
  "auth": {
    "identityProviders": {
      "twitter": {
        "registration": {
          "consumerKeySettingName": "<TWITTER_CONSUMER_KEY>",
          "consumerSecretSettingName": "<TWITTER_CONSUMER_SECRET>"
        }
      }
    }
  }
}
```

Twitter를 인증 공급자로 구성하는 방법에 대한 자세한 내용은 [App Service 인증/권한 부여 설명서](../app-service/configure-authentication-provider-twitter.md)를 참조하세요.

---

## <a name="configure-a-custom-openid-connect-provider"></a>사용자 지정 OpenID Connect 공급자 구성

이 섹션에서는 [OIDC(OpenID Connect) 사양](https://openid.net/connect/)을 준수하는 사용자 지정 인증 공급자를 사용하도록 Azure Static Web Apps를 구성하는 방법을 보여 줍니다. 사용자 지정 OIDC 공급자를 사용하려면 다음 단계가 필요합니다.

- 하나 이상의 OIDC 공급자가 허용됩니다.
- 각 공급자는 구성에 고유한 이름이 있어야 합니다.
- 하나의 공급자만 기본 리디렉션 대상이 될 수 있습니다.

### <a name="register-your-application-with-the-identity-provider"></a>ID 공급자에 애플리케이션 등록

ID 공급자에 애플리케이션의 세부 정보를 등록해야 합니다. 애플리케이션에 대한 **클라이언트 ID** 및 **클라이언트 암호** 를 생성하는 데 필요한 단계는 공급자에게 문의하세요.

> [!IMPORTANT]
> 애플리케이션 비밀은 중요한 보안 자격 증명입니다. 이 비밀을 다른 사람과 공유하거나, 클라이언트 애플리케이션 내에 배포하거나, 소스 제어에 체크 인하지 마세요.

등록 자격 증명이 있으면 다음 단계를 사용하여 사용자 지정 등록을 만듭니다.

1. 선택한 설정 이름을 사용하여 클라이언트 ID 및 클라이언트 암호를 앱의 [애플리케이션 설정](application-settings.md)으로 추가합니다. 나중에 사용할 수 있도록 이러한 이름을 기록해 둡니다. 또는 클라이언트 ID를 구성 파일에 포함할 수 있습니다.

1. 또한 공급자에 대한 OpenID Connect 메타데이터가 필요합니다. 이 정보는 일반적으로 공급자의 _발급자 URL_ 에 `/.well-known/openid-configuration`이 접미사로 추가된 [구성 메타데이터 문서](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)를 통해 노출됩니다. 이 구성 URL을 수집합니다.

1. OIDC 공급자 및 공급자 정의에 대한 구성 블록이 있는 [구성 파일](configuration.md)의 `auth` 섹션을 추가합니다.

   ```json
   {
     "auth": {
       "identityProviders": {
         "customOpenIdConnectProviders": {
           "myProvider": {
             "registration": {
               "clientIdSettingName": "<MY_PROVIDER_CLIENT_ID_SETTING_NAME>",
               "clientCredential": {
                 "clientSecretSettingName": "<MY_PROVIDER_CLIENT_SECRET_SETTING_NAME>"
               },
               "openIdConnectConfiguration": {
                 "wellKnownOpenIdConfiguration": "https://<PROVIDER_ISSUER_URL>/.well-known/openid-configuration"
               }
             },
             "login": {
               "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
               "scopes": [],
               "loginParameterNames": []
             }
           }
         }
       }
     }
   }
   ```

  코드에서 다음 대체 토큰을 원하는 값으로 변경합니다.

  | 바꿀 원본... | 대상... |
  | --- | --- |
  | `<MY_PROVIDER_CLIENT_ID_SETTING_NAME>` | 사용자 지정 등록에서 생성된 클라이언트 ID와 연결된 애플리케이션 설정 이름입니다. |
  | `<MY_PROVIDER_CLIENT_SECRET_SETTING_NAME>` | 사용자 지정 등록에서 생성된 클라이언트 암호와 연결된 애플리케이션 설정 이름입니다. |
  | `<PROVIDER_ISSUER_URL>` | 공급자의 _발급자 URL_ 에 대한 경로입니다. |

- 이 예제에서 공급자 이름 `myProvider`는 Azure Static Web Apps에서 사용되는 고유 식별자입니다.
- `login` 개체를 사용하면 사용자 지정 범위, 로그인 매개 변수 또는 사용자 지정 클레임에 대한 값을 제공할 수 있습니다.

### <a name="login-logout-and-purging-user-details"></a>로그인, 로그아웃 및 사용자 세부 정보 제거

사용자 지정 OIDC 공급자를 사용하려면 다음 URL 패턴을 사용합니다.

| 작업             | 패턴                                  |
| ------------------ | ---------------------------------------- |
| 로그인              | `/.auth/login/<PROVIDER_NAME_IN_CONFIG>` |
| Logout             | `/.auth/logout`                          |
| 사용자 세부 정보 제거 | `/.auth/purge/<PROVIDER_NAME_IN_CONFIG>` |

### <a name="authentication-callbacks"></a>인증 콜백

인증 공급자는 로그인 또는 로그아웃 요청을 완료하려면 리디렉션 URL이 필요합니다. 다음 엔드포인트는 리디렉션 대상으로 사용할 수 있습니다.

| Type   | URL 패턴                                                 |
| ------ | ----------------------------------------------------------- |
| 로그인  | `https://<YOUR_SITE>/.auth/login/<PROVIDER_NAME_IN_CONFIG>/callback`  |
| Logout | `https://<YOUR_SITE>/.auth/logout/<PROVIDER_NAME_IN_CONFIG>/callback` |

> [!Note]
> 이러한 URL은 인증 공급자로부터 응답을 받기 위해 Azure Static Web Apps에서 제공하므로 이러한 경로에서 페이지를 만들 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Key Vault에서 인증 비밀 보호](./key-vault-secrets.md)

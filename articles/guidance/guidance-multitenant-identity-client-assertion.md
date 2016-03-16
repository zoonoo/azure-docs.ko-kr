<properties
   pageTitle="클라이언트 어설션을 사용하여 Azure AD에서 액세스 토큰 가져오기 | Microsoft Azure"
   description="클라이언트 어설션을 사용하여 Azure AD에서 액세스 토큰을 가져오는 방법을 알아봅니다."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# 클라이언트 어설션을 사용하여 Azure AD에서 액세스 토큰 가져오기

이 문서는 [시리즈의 일부]입니다. 이 시리즈와 함께 제공되는 완전한 [응용 프로그램 예제]도 있습니다.

## 배경

OpenID Connect에서 인증 코드 흐름 또는 하이브리드 흐름을 사용하는 경우 클라이언트는 액세스 토큰에 대한 인증 코드를 교환합니다. 이 단계에서 클라이언트는 서버에 자신을 인증해야 합니다.

![클라이언트 암호](media/guidance-multitenant-identity/client-secret.png)

클라이언트를 인증하는 한 가지 방법은 클라이언트 암호를 사용하는 것입니다. 이는 [Tailspin Surveys][Surveys] 응용 프로그램이 기본적으로 구성된 방식입니다.

다음은 IDP에 액세스 토큰을 요청하는 클라이언트 요청의 예입니다. `client_secret` 매개 변수에 주목하세요.

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

암호는 문자열일 뿐이므로 값을 유출해서는 안 됩니다. 클라이언트 암호를 소스 제어에서 분리하는 것이 가장 좋습니다. Azure에 배포하는 경우 [앱 설정][configure-web-app]에서 암호를 저장합니다.

그러나 Azure 구독에 액세스할 수 있는 모든 사람이 앱 설정을 볼 수 있습니다. 또한 암호를 소스 제어(예: 배포 스크립트)로 체크 인하고 전자 메일 등으로 공유하려는 유혹이 항상 있습니다.

보안을 강화하기 위해 클라이언트 암호 대신 _클라이언트 어설션_을 사용할 수 있습니다. 클라이언트 어설션을 사용하는 경우 클라이언트는 X.509 인증서를 사용하여 토큰 요청을 증명합니다. 클라이언트 인증서는 웹 서버에 설치됩니다. 일반적으로 누구도 클라이언트 암호를 고의로 노출하지 못하도록 하는 것보다 인증서에 대한 액세스를 제한하는 것이 더 쉽습니다.

다음은 클라이언트 어설션을 사용한 토큰 요청입니다.

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

`client_secret` 매개 변수는 더 이상 사용되지 않습니다. 대신 `client_assertion` 매개 변수에 클라이언트 인증서를 사용하여 서명된 JWT 토큰이 포함되어 있습니다. `client_assertion_type` 매개 변수는 어설션 유형(이 예제의 경우 JWT 토큰)을 지정합니다. 서버는 JWT 토큰의 유효성을 검사합니다. JWT 토큰이 유효하지 않은 경우 토큰 요청에서 오류가 반환됩니다.

> [AZURE.NOTE] X.509 인증서는 클라이언트 어설션의 유일한 형식이 아닙니다. 하지만 Azure AD에서 지원되기 때문에 여기에서 중점적으로 다룹니다.

## Surveys 응용프로그램에서 클라이언트 어설션 사용

이 섹션에서는 클라이언트 어설션을 사용하도록 Tailspin Surveys 응용 프로그램을 구성하는 방법을 보여 줍니다. 다음 단계에서는 프로덕션용이 아니라 개발용으로 적합한 자체 서명된 인증서를 생성합니다.

1. 다음과 같이 PowerShell 스크립트 [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault]을 실행합니다.

    ```
    .\Setup-KeyVault.ps -Subject [subject]
    ```

    `Subject` 매개 변수의 경우 "surveysapp"과 같은 이름을 임의로 입력합니다. 스크립트는 자체 서명된 인증서를 생성하고 "현재 사용자/개인" 인증서 저장소에 저장합니다.

2. 스크립트의 출력은 JSON 조각입니다. 다음과 같이 이를 웹앱의 응용 프로그램 매니페스트에 추가합니다.

    1. [Azure 관리 포털][azure-management-portal]에 로그인하고 Azure AD 디렉터리로 이동합니다.

    2. **응용 프로그램**을 클릭합니다.

    3. Surveys 응용 프로그램을 선택합니다.

    4.	**매니페스트 관리**를 클릭하고 **매니페스트 다운로드**를 선택합니다.

    5.	텍스트 편집기에서 매니페스트 JSON 파일을 엽니다. 스크립트의 출력을 `keyCredentials` 속성에 붙여 넣습니다. 다음과 유사하게 나타납니다.

        ```    
        "keyCredentials": [
            {
              "type": "AsymmetricX509Cert",
              "usage": "Verify",
              "keyId": "29d4f7db-0539-455e-b708-....",
              "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
              "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
            }
          ],
         ```

    6.	JSON 파일에 변경 내용을 저장합니다.

    7.	포털로 돌아갑니다. **매니페스트 관리** > **매니페스트 업로드**를 클릭하고 JSON 파일을 업로드합니다.

3. 다음 명령을 실행하여 인증서의 지문을 가져옵니다.

    ```
    certutil -store -user my [subject]
    ```

    여기서 `[subject]`는 PowerShell 스크립트에서 주체에 대해 지정한 값입니다. 지문은 "Cert Hash(sha1)" 아래에 나열됩니다. 16진수 사이의 공백을 제거합니다.

4. 앱 암호 업데이트 솔루션 탐색기에서 Tailspin.Surveys.Web 프로젝트를 마우스 오른쪽 단추로 클릭하고 **사용자 암호 관리**를 선택합니다. 다음과 같이 "AzureAd" 아래에 "Asymmetric" 항목을 추가합니다.

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys application client ID]",
        // "ClientSecret": "[client secret]",  << Delete this entry
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Survey.WebAPI application]",
        // new:
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint]",  // Example: "105b2ff3bc842c53582661716db1b7cdc6b43ec9"
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "Redis": {
        "Configuration": "[Redis connection string]"
      }
    }
    ```

    인증서가 루트 CA 인증 기관에서 서명된 것이 아니므로 `ValidationRequired`를 false로 설정해야 합니다. 프로덕션에서 CA 기관에서 서명된 인증서를 사용하고 `ValidationRequired`를 true로 설정합니다.

    또한 `ClientSecret` 항목은 클라이언트 어설션에 필요하지 않으므로 삭제합니다.

5. Startup.cs에서 `ICredentialService`를 등록하는 코드를 찾습니다. `CertificateCredentialService`를 사용하는 줄의 주석 처리를 제거하고 `ClientCredentialService`를 사용하는 줄을 주석으로 처리합니다.

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

웹 응용 프로그램은 런타임에 인증서 저장소에서 인증서를 읽습니다. 웹앱과 동일한 컴퓨터에 인증서를 설치해야 합니다.

## 추가 리소스

- [Azure 웹 사이트 응용 프로그램에서 인증서 사용][using-certs-in-websites]
- [RFC 7521][RFC7521]. 클라이언트 어설션을 보내기 위한 일반적인 메커니즘을 정의합니다.
- [RFC 7523][RFC7523]. 클라이언트 어설션에 JWT 토큰을 사용하는 방법을 정의합니다.


<!-- Links -->
[configure-web-app]: ../app-service-web/web-sites-configure.md
[azure-management-portal]: https://manage.windowsazure.com
[RFC7521]: https://tools.ietf.org/html/rfc7521
[RFC7523]: https://tools.ietf.org/html/rfc7523
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/
[시리즈의 일부]: guidance-multitenant-identity.md
[응용 프로그램 예제]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->
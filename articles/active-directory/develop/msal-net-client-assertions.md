---
title: 클라이언트 어설션(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리의 기밀 클라이언트 응용 프로그램에 대한 서명된 클라이언트 어설션 지원에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8c97387bfd2a362d3bf5a6b8a3252242f061da31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050285"
---
# <a name="confidential-client-assertions"></a>기밀 클라이언트 어설션

ID를 증명하기 위해 기밀 클라이언트 응용 프로그램은 Azure AD와 비밀을 교환합니다. 비밀은 다음과 같은 것입니다.
- 클라이언트 암호(응용 프로그램 암호)입니다.
- 표준 클레임이 포함된 서명된 어설션을 작성하는 데 사용되는 인증서입니다.

이 비밀은 서명된 어설션이 직접 될 수도 있습니다.

MSAL.NET 기밀 클라이언트 앱에 자격 증명 또는 어설션을 제공하는 네 가지 방법이 있습니다.
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> API를 `WithClientAssertion()` 사용하여 기밀 클라이언트에 대한 토큰을 획득할 수 있지만, 고급이며 일반적이지 않은 매우 구체적인 시나리오를 처리하도록 설계되었기 때문에 기본적으로 사용하지 않는 것이 좋습니다. API를 `.WithCertificate()` 사용하면 MSAL.NET 이를 처리 할 수 있습니다. 이 API는 필요한 경우 인증 요청을 사용자 지정할 수 `.WithCertificate()` 있는 기능을 제공하지만 대부분의 인증 시나리오에 대해 만든 기본 어설션으로 충분합니다. 이 API는 MSAL.NET 내부적으로 서명 작업을 수행하지 못하는 일부 시나리오에서 해결 방법으로 사용할 수도 있습니다.

### <a name="signed-assertions"></a>서명된 어설션

서명된 클라이언트 어설션은 Azure AD, Base64 인코딩에서 위임한 필수 인증 클레임을 포함하는 페이로드를 통해 서명된 JWT의 형태를 취합니다. 이를 사용하려면:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Azure AD에서 예상하는 클레임은 다음과 같습니다.

클레임 유형 | 값 | 설명
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | "aud" (잠재 고객) 클레임은 JWT가 의도한 수신자를 식별합니다(여기 Azure AD) [RFC 7519, 섹션 4.1.3] 참조
exp | 2019년 6월 27일 15:04:17 GMT+0200 (로맨스 데이라이트 시간) | "exp"(만료 시간) 클레임은 JWT가 그 이후에는 처리를 허용하지 않아야 하는 만료 시간을 식별합니다. [RFC 7519, 섹션 4.1.4]
iss | {클라이언트 ID} | "iss"(발급자) 클레임은 JWT를 발급한 주체를 식별합니다. 이 클레임의 처리는 응용 프로그램에 따라 다릅니다. "iss" 값은 StringOrURI 값을 포함하는 대/소문자를 구분하는 문자열입니다. [RFC 7519, 섹션 4.1.1]
jti | (Guid) | "jti"(JWT ID) 클레임은 JWT에 대한 고유 식별자를 제공합니다. 식별자 값은 동일한 값이 실수로 다른 데이터 개체에 할당될 가능성이 무시할 수 있는 방식으로 할당되어야 합니다. 응용 프로그램이 여러 발급자를 사용하는 경우 다른 발급자가 생성한 값 간에 충돌을 방지해야 합니다. "jti" 클레임은 JWT가 재생되는 것을 방지하는 데 사용할 수 있습니다. "jti" 값은 대/소문자를 구분하는 문자열입니다. [RFC 7519, 섹션 4.1.7]
nbf | 2019년 6월 27일 14:54:17 GMT+0200 (로맨스 데이라이트 시간) | "nbf"(not before) 클레임은 JWT가 그 이전에는 처리를 허용하지 않아야 하는 시간을 식별합니다. [RFC 7519, 섹션 4.1.5]
sub | {클라이언트 ID} | "하위"(제목) 클레임은 JWT의 주제를 식별합니다. JWT의 클레임은 일반적으로 주제에 대한 진술입니다. 제목 값은 발급자의 컨텍스트에서 로컬로 고유하거나 전역적으로 고유해야 합니다. 참조 [RFC 7519, 섹션 4.1.2]

다음은 이러한 클레임을 만드는 방법의 예입니다.

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

서명된 클라이언트 어설션을 만드는 방법은 다음과 같습니다.

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>대체 방법

또한 [Microsoft.IdentityModel.JsonWebTokens를](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) 사용하여 어설션을 만들 수도 있습니다. 아래 예제와 같이 코드가 더 우아해집니다.

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

서명된 클라이언트 어설션이 있으면 아래와 같이 MSAL apis와 함께 사용할 수 있습니다.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>With클라이언트클레임

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`기본적으로 Azure AD에서 예상한 클레임과 보내려는 추가 클라이언트 클레임이 포함된 서명된 어설션이 생성됩니다. 이 작업을 수행하는 방법에 대한 코드 조각은 다음과 같습니다.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

전달하는 사전의 클레임 중 하나가 필수 클레임 중 하나와 동일한 경우 추가 클레임의 값이 고려됩니다. MSAL.NET 계산된 클레임을 재정의합니다.

Azure AD에서 예상하는 필수 클레임을 포함하여 고유한 클레임을 `false` 제공하려면 `mergeWithDefaultClaims` 매개 변수를 전달합니다.

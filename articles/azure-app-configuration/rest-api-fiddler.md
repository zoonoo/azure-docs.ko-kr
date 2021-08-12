---
title: Azure Active Directory REST API - Fiddler를 사용한 테스트
description: Fiddler를 사용하여 Azure App Configuration REST API 테스트
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 1142aa25212d87c5484963cda4e172df3d1fbafc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932611"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Fiddler를 사용하여 Azure App Configuration REST API 테스트

[Fiddler](https://www.telerik.com/fiddler)를 사용하여 REST API 테스트하려면 [인증](./rest-api-authentication-hmac.md)에 필요한 HTTP 헤더를 요청에 포함해야 합니다. REST API 테스트하고 인증 헤더를 자동으로 생성하도록 Fiddler를 구성하는 방법은 다음과 같습니다.

1. TLS 1.2가 허용되는 프로토콜인지 확인합니다.
    1. **도구** > **옵션** > **HTTPS**)로 이동합니다.
    1. **HTTPS 트래픽 암호 해독** 이 선택되어 있는지 확인합니다.
    1. 표시되지 않는 경우, 프로토콜 목록에서 **tls1.2** 를 추가합니다.
1. **Fiddler 스크립트 편집기** 를 열거나 Fiddler 내에서 **Ctrl-R** 을 누릅니다
1. `OnBeforeRequest` 함수 앞의 `Handlers` 클래스 내에 다음 코드를 추가합니다

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. `OnBeforeRequest` 메서드의 끝에 다음 코드를 추가합니다. TODO 주석에 표시된 대로 액세스 키를 업데이트합니다.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. [Fiddler Composer](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest)를 사용하여 요청 생성 및 보내기

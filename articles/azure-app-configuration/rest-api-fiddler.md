---
title: Azure Active Directory REST API Fiddler를 사용 하 여 테스트
description: Fiddler를 사용 하 여 Azure 앱 구성을 테스트 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3766567fe58e8d2eb86556d3defa7a85efd9b2fb
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424149"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Fiddler를 사용 하 여 Azure 앱 구성 REST API 테스트

[Fiddler](https://www.telerik.com/fiddler)를 사용 하 여 REST API를 테스트 하려면 요청에 [인증](./rest-api-authentication-hmac.md) 하는 데 필요한 HTTP 헤더를 포함 해야 합니다. REST API 테스트를 위해 Fiddler를 구성 하 고 인증 헤더를 자동으로 생성 하는 방법은 다음과 같습니다.

1. TLS 1.2이 허용 되는 프로토콜 인지 확인 합니다.
    1. **도구**  >  **옵션**  >  **HTTPS** 로 이동 합니다.
    1. **HTTPS 트래픽 암호 해독** 이 선택 되어 있는지 확인 합니다.
    1. 프로토콜 목록에서 **tls 1.2** 를 추가 합니다 (없는 경우).
1. **Fiddler 스크립트 편집기** 를 열거나 Fiddler 내에서 **ctrl + R을** 누릅니다.
1. `Handlers`함수 앞에 클래스 내부에 다음 코드를 추가 합니다. `OnBeforeRequest`

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

1. 함수 끝에 다음 코드를 추가 `OnBeforeRequest` 합니다. TODO 주석으로 표시 된 대로 액세스 키를 업데이트 합니다.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. [Fiddler의 작성기](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) 를 사용 하 여 요청 생성 및 보내기

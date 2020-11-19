---
title: 클라이언트에서 게시 이벤트를 인증 하 여 사용자 지정 토픽 또는 도메인 Event Grid
description: 이 문서에서는 사용자 지정 항목 Event Grid 클라이언트 게시 이벤트를 인증 하는 다양 한 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c415b7e1bb6bd7a2116da82c7d8f1de205009d0
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886263"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a>게시 클라이언트 인증 (Azure Event Grid)
이 문서에서는 **액세스 키** 또는 **SAS (공유 액세스 서명)** 토큰을 사용 하 여 Azure Event Grid 토픽 또는 도메인에 이벤트를 게시 하는 클라이언트 인증에 대 한 정보를 제공 합니다. SAS 토큰을 사용 하는 것이 좋지만 키 인증을 사용 하면 간단한 프로그래밍이 가능 하며 기존의 많은 webhook 게시자와 호환 됩니다.  

## <a name="authenticate-using-an-access-key"></a>액세스 키를 사용 하 여 인증
액세스 키 인증은 인증의 가장 간단한 형태입니다. 액세스 키를 HTTP 헤더 또는 URL 쿼리 매개 변수로 전달할 수 있습니다. 

### <a name="access-key-in-a-http-header"></a>HTTP 헤더의 액세스 키
액세스 키를 HTTP 헤더에 대 한 값으로 전달 `aeg-sas-key` 합니다.

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>쿼리 매개 변수로 서의 액세스 키
`aeg-sas-key`를 쿼리 매개 변수로 지정할 수도 있습니다. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

토픽 또는 도메인에 대 한 액세스 키를 가져오는 방법에 대 한 지침은 [액세스 키 가져오기](get-access-keys.md)를 참조 하세요.

## <a name="authenticate-using-a-sas-token"></a>SAS 토큰을 사용하여 인증
Event Grid 리소스에 대 한 SAS 토큰에는 리소스, 만료 시간 및 서명이 포함 됩니다. SAS 토큰의 형식은 다음과 같습니다. `r={resource}&e={expiration}&s={signature}`

리소스는 이벤트를 전송할 Event Grid 항목의 경로입니다. 예를 들어 올바른 리소스 경로는 `https://<yourtopic>.<region>.eventgrid.azure.net/api/events`입니다. 지원되는 모든 API 버전을 보려면 [Microsoft.EventGrid 리소스 형식](/azure/templates/microsoft.eventgrid/allversions)을 참조하세요. 

첫째, 프로그래밍 방식으로 SAS 토큰을 생성 한 다음 `aeg-sas-token` 헤더 또는 `Authorization SharedAccessSignature` 헤더를 사용 하 여 Event Grid 인증 합니다. 

### <a name="generate-sas-token-programmatically"></a>프로그래밍 방식으로 SAS 토큰 생성
다음 예제에서는 Event Grid를 사용하기 위한 SAS 토큰을 만듭니다.

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>Aeg-sas-토큰 헤더 사용
다음은 헤더에 대 한 값으로 SAS 토큰을 전달 하는 예입니다 `aeg-sas-token` . 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Authorization 헤더 사용
다음은 헤더에 대 한 값으로 SAS 토큰을 전달 하는 예입니다 `Authorization` . 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>다음 단계
이벤트를 전달 하는 이벤트 처리기를 사용한 인증에 대해 알아보려면 [이벤트 배달 인증](security-authentication.md) 을 참조 하세요. 

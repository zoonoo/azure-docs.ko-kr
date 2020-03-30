---
title: IoT 플러그 앤 플레이 미리 보기 리포지토리에 액세스하기 위한 보안 토큰 생성 | 마이크로 소프트 문서
description: IoT 플러그 앤 플레이 미리 보기 모델 리포지토리에 프로그래밍 방식으로 액세스할 때 사용할 공유 액세스 서명 토큰을 생성합니다.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159203"
---
# <a name="generate-sas-token"></a>SAS 토큰 생성

이 방법 가이드에서는 IoT 플러그 앤 플레이 미리 보기 모델 리포지토리 API와 함께 사용할 SAS(공유 액세스 서명) 토큰을 프로그래밍 방식으로 생성하는 방법을 보여 주며, 이 방법들을 보여 주실 수 있습니다.

## <a name="python"></a>Python

다음 코드 조각은 파이썬을 사용하여 SAS 토큰을 생성하는 방법을 보여 주며 다음과 같이 설명합니다.

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

다음 코드 조각은 C를\#사용하여 SAS 토큰을 생성하는 방법을 보여 주며 다음과 같이 설명합니다.

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>SAS 토큰 사용

SAS 토큰을 생성한 후 HTTP POST 요청을 하는 데 사용할 수 있습니다. 예를 들어:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

클라이언트에 SAS 토큰을 제공하면 클라이언트에 리소스의 기본 키가 없으며 해시를 반대로 하여 가져오지 않습니다. SAS 토큰을 사용하면 클라이언트가 액세스할 수 있는 내용과 액세스 하는 기간 동안 제어할 수 있습니다. 정책의 기본 키를 변경하면 정책에서 생성된 모든 SAS 토큰이 무효화됩니다.

## <a name="next-steps"></a>다음 단계

이제 모델 IoT 플러그 앤 플레이 미리 보기 모델 리포지토리에 액세스하는 데 사용할 보안 토큰을 생성하는 방법에 대해 배웠으므로 다음 단계는 [IoT 플러그 앤 플레이 미리 보기 모델링 개발자 가이드에서](concepts-developer-guide.md)자세히 알아보는 것입니다.

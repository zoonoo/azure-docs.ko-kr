---
title: IoT 플러그 앤 플레이 Preview 리포지토리에 액세스 하기 위한 보안 토큰 생성 | Microsoft Docs
description: IoT 플러그 앤 플레이 미리 보기 모델 리포지토리에 프로그래밍 방식으로 액세스할 때 사용할 공유 액세스 서명 토큰을 생성 합니다.
author: YasinMSFT
ms.author: yahajiza
ms.date: 08/06/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e5d6e7087a7e3d5f4a001e16c5cfa19a6df6a68e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880541"
---
# <a name="generate-sas-token"></a>SAS 토큰 생성

이 방법 가이드에서는 IoT 플러그 앤 플레이 미리 보기 모델 리포지토리 Api와 함께 사용 하기 위해 프로그래밍 방식으로 SAS (공유 액세스 서명) 토큰을 생성 하는 방법을 보여 줍니다.

## <a name="python"></a>Python

다음 코드 조각은 Python을 사용 하 여 SAS 토큰을 생성 하는 방법을 보여 줍니다.

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

다음 코드 조각은 C\#를 사용 하 여 SAS 토큰을 생성 하는 방법을 보여 줍니다.

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

SAS 토큰을 생성 한 후에는이 토큰을 사용 하 여 HTTP POST 요청을 수행할 수 있습니다. 예를 들어:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

클라이언트에 SAS 토큰을 제공 하는 경우 클라이언트에는 리소스의 기본 키가 없으며,이를 얻기 위해 해시를 되돌릴 수 없습니다. SAS 토큰은 클라이언트가 액세스할 수 있는 항목 및 기간에 대 한 제어를 제공 합니다. 정책에서 기본 키를 변경 하면 해당 키 로부터 만들어진 모든 SAS 토큰이 무효화 됩니다.

## <a name="next-steps"></a>다음 단계

모델 IoT 플러그 앤 플레이 미리 보기 모델 리포지토리에 액세스 하는 데 사용할 보안 토큰을 생성 하는 방법에 대해 알아보았습니다. 다음 단계는 [IoT 플러그 앤 플레이 미리 보기 모델링 개발자 가이드](concepts-developer-guide.md)에서 자세히 알아보세요.

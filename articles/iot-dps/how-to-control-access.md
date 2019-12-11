---
title: IoT 장치 프로 비전 서비스의 보안 끝점 | Microsoft Docs
description: 개념-백 엔드 앱에 대 한 IoT 장치 프로 비전 서비스에 대 한 액세스를 제어 하는 방법입니다. 보안 토큰에 대 한 정보를 포함 합니다.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.openlocfilehash: f36a48e0cedc309deda8416face5549a54eb8c73
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975128"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub 장치 프로 비전 서비스에 대 한 액세스 제어

이 문서에서는 IoT 장치 프로 비전 서비스의 보안을 설정 하는 옵션을 설명 합니다. 프로 비전 서비스는 *사용 권한을* 사용 하 여 각 끝점에 대 한 액세스 권한을 부여 합니다. 사용 권한은 기능에 따라 서비스 인스턴스에 대 한 액세스를 제한 합니다.

이 문서에서는 다음을 설명 합니다.

* 프로 비전 서비스에 액세스 하기 위해 백 엔드 앱에 부여할 수 있는 다양 한 권한입니다.
* 인증 프로세스 및 사용 권한을 확인 하는 데 사용 하는 토큰입니다.

### <a name="when-to-use"></a>When to use

프로 비전 서비스 끝점에 액세스할 수 있는 적절 한 권한이 있어야 합니다. 예를 들어 백 엔드 앱은 서비스에 보내는 모든 메시지와 함께 보안 자격 증명을 포함 하는 토큰을 포함 해야 합니다.

## <a name="access-control-and-permissions"></a>액세스 제어 및 권한

다음과 같은 방법으로 [권한을](#device-provisioning-service-permissions) 부여할 수 있습니다.

* **공유 액세스 권한 부여 정책**. 공유 액세스 정책은 [사용 권한](#device-provisioning-service-permissions)조합을 부여할 수 있습니다. [Azure Portal][lnk-management-portal]에서 또는 [장치 프로 비전 서비스 REST api][lnk-resource-provider-apis]를 사용 하 여 프로그래밍 방식으로 정책을 정의할 수 있습니다. 새로 만든 프로 비전 서비스에는 다음과 같은 기본 정책이 있습니다.

* **provisioningserviceowner**: 모든 사용 권한이 있는 정책입니다.

> [!NOTE]
> 자세한 내용은 [사용 권한](#device-provisioning-service-permissions) 을 참조 하세요.

## <a name="authentication"></a>Hitelesítés

Azure IoT Hub 장치 프로 비전 서비스는 공유 액세스 정책에 대 한 토큰을 확인 하 여 끝점에 대 한 액세스 권한을 부여 합니다. 대칭 키와 같은 보안 자격 증명은 네트워크를 통해 전송 되지 않습니다.

> [!NOTE]
> 장치 프로 비전 서비스 리소스 공급자는 [Azure Resource Manager][lnk-azure-resource-manager]의 모든 공급자와 마찬가지로 Azure 구독을 통해 보안이 유지 됩니다.

보안 토큰을 생성 하 고 사용 하는 방법에 대 한 자세한 내용은 다음 섹션을 참조 하세요.

HTTP는 유일 하 게 지원 되는 프로토콜 이며 **권한 부여** 요청 헤더에 유효한 토큰을 포함 하 여 인증을 구현 합니다.

#### <a name="example"></a>Példa
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> [Azure IoT 장치 프로 비전 서비스 sdk][lnk-sdks] 는 서비스에 연결할 때 토큰을 자동으로 생성 합니다.

## <a name="security-tokens"></a>보안 토큰

장치 프로 비전 서비스는 네트워크에서 키를 전송 하지 않도록 보안 토큰을 사용 하 여 서비스를 인증 합니다. 또한 보안 토큰은 유효 기간 및 범위가 제한 됩니다. [Azure IoT 장치 프로 비전 서비스 sdk][lnk-sdks] 는 특별 한 구성이 필요 하지 않고 토큰을 자동으로 생성 합니다. 일부 시나리오에서는 보안 토큰을 직접 생성 하 고 사용 해야 합니다. 이러한 시나리오에는 HTTP 표면의 직접 사용이 포함 됩니다.

### <a name="security-token-structure"></a>보안 토큰 구조

보안 토큰을 사용 하 여 서비스에 대 한 시간 제한 액세스를 IoT 장치 프로 비전 서비스의 특정 기능에 부여할 수 있습니다. 프로 비전 서비스에 연결할 수 있는 권한을 얻으려면 서비스는 공유 액세스 또는 대칭 키로 서명 된 보안 토큰을 전송 해야 합니다.

공유 액세스 키로 서명 된 토큰은 공유 액세스 정책 권한과 관련 된 모든 기능에 대 한 액세스 권한을 부여 합니다. 

보안 토큰의 형식은 다음과 같습니다.

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

예상 값은 다음과 같습니다.

| Value (Díj) | Leírás |
| --- | --- |
| 서명과 |`{URL-encoded-resourceURI} + "\n" + expiry`형식의 HMAC-SHA256 서명 문자열입니다. **중요**: 키는 b a s e 64에서 디코딩되 고 키로 사용 되어 HMAC-SHA256 계산을 수행 합니다.|
| 만료 |1 월 1970 일에 epoch 00:00:00 UTC 이후의 시간 (초)에 대 한 UTF8 문자열입니다. |
| {URL 인코딩-resourceURI} | 소문자 URL-소문자 리소스 URI의 인코딩입니다. IoT 장치 프로 비전 서비스 (프로토콜 없음)의 호스트 이름으로 시작 하 여이 토큰을 사용 하 여 액세스할 수 있는 끝점의 URI 접두사입니다. Például: `mydps.azure-devices-provisioning.net`. |
| PolicyName |이 토큰이 참조 하는 공유 액세스 정책의 이름입니다. |

**접두사에 대 한 참고**: URI 접두사는 문자를 기준으로 하지 않고 세그먼트로 계산 됩니다. 예 `/a/b` 들어 `/a/b/c`에 대 한 접두사 이지만 `/a/bc`은 아닙니다.

다음 node.js 코드 조각은 입력 `resourceUri, signingKey, policyName, expiresInMins`에서 토큰을 계산 하는 **generateSasToken** 라는 함수를 보여 줍니다. 다음 섹션에서는 여러 토큰 사용 사례에 대해 서로 다른 입력을 초기화 하는 방법에 대해 자세히 설명 합니다.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

비교 하 여 보안 토큰을 생성 하는 동일한 Python 코드는 다음과 같습니다.

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> IoT 장치 프로 비전 서비스 컴퓨터에서 토큰의 유효 기간에 대 한 유효성을 검사 하므로 토큰을 생성 하는 컴퓨터의 클록에 대 한 드리프트는 최소화 되어야 합니다.

### <a name="use-security-tokens-from-service-components"></a>서비스 구성 요소에서 보안 토큰 사용

서비스 구성 요소는 앞에서 설명한 대로 적절 한 권한을 부여 하는 공유 액세스 정책을 사용 하 여 보안 토큰만 생성할 수 있습니다.

다음은 끝점에 노출 되는 서비스 함수입니다.

| Végpont | Funkció |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |장치 프로 비전 서비스에 장치 등록 작업을 제공 합니다. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |장치 등록 그룹을 관리 하기 위한 작업을 제공 합니다. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |장치 등록 상태를 검색 하 고 관리 하기 위한 작업을 제공 합니다. |


예를 들어 **enrollmentread** 라는 미리 생성 된 공유 액세스 정책을 사용 하 여 생성 된 서비스는 다음 매개 변수를 사용 하 여 토큰을 만듭니다.

* 리소스 URI: `{mydps}.azure-devices-provisioning.net`,
* 서명 키: `enrollmentread` 정책의 키 중 하나입니다.
* 정책 이름: `enrollmentread`,
* 모든 만료 시간. backn

![포털에서 장치 프로 비전 서비스 인스턴스에 대 한 공유 액세스 정책 만들기][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

모든 등록 레코드를 읽을 수 있는 액세스 권한을 부여 하는 결과는 다음과 같습니다.

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>참조 항목:

다음 참조 항목에서는 IoT 장치 프로 비전 서비스에 대 한 액세스를 제어 하는 방법에 대 한 자세한 정보를 제공 합니다.

### <a name="device-provisioning-service-permissions"></a>장치 프로 비전 서비스 권한

다음 표에는 IoT 장치 프로 비전 서비스에 대 한 액세스를 제어 하는 데 사용할 수 있는 권한이 나열 되어 있습니다.

| Engedély | Megjegyzések |
| --- | --- |
| **ServiceConfig** |서비스 구성을 변경할 수 있는 액세스 권한을 부여 합니다. <br/>이 사용 권한은 백 엔드 클라우드 서비스에서 사용 됩니다. |
| **EnrollmentRead** |장치 등록 및 등록 그룹에 대 한 읽기 권한을 부여 합니다. <br/>이 사용 권한은 백 엔드 클라우드 서비스에서 사용 됩니다. |
| **EnrollmentWrite** |장치 등록 및 등록 그룹에 대 한 쓰기 권한을 부여 합니다. <br/>이 사용 권한은 백 엔드 클라우드 서비스에서 사용 됩니다. |
| **RegistrationStatusRead** |장치 등록 상태에 대 한 읽기 권한을 부여 합니다. <br/>이 사용 권한은 백 엔드 클라우드 서비스에서 사용 됩니다. |
| **RegistrationStatusWrite**  |장치 등록 상태에 대 한 삭제 권한을 부여 합니다. <br/>이 사용 권한은 백 엔드 클라우드 서비스에서 사용 됩니다. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/

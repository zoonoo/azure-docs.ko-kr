<properties
 pageTitle="개발자 가이드 - IoT Hub에 대한 액세스 제어 | Microsoft Azure"
 description="Azure IoT Hub 개발자 가이드 - IoT Hub에 대한 액세스를 제어하고 보안을 관리하는 방법"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>


# <a name="control-access-to-iot-hub"></a>IoT Hub에 대한 액세스 제어

## <a name="overview"></a>개요

이 섹션에서는 IoT Hub를 보호하는 옵션을 설명합니다. IoT Hub는 *권한*을 사용하여 각 IoT Hub의 끝점에 대한 액세스를 부여합니다. 사용 권한은 기능에 따라 IoT Hub에 대한 액세스를 제한합니다.

이 문서에서는 다음을 설명합니다.

- IoT Hub에 액세스하기 위해 장치 또는 백 엔드 앱에 부여할 수 있는 다양한 권한
- 권한을 확인하기 위해 사용되는 인증 프로세스 및 토큰.
- 자격 증명을 통해 특정 리소스에 대한 액세스를 제한하는 방법.
- X.509 인증서에 대한 IoT Hub 지원.
- 기존 장치 ID 레지스트리 또는 인증 체계를 사용하는 사용자 지정 장치 인증 메커니즘.

### <a name="when-to-use"></a>사용하는 경우

IoT Hub 끝점에 액세스하려면 적절한 권한이 있어야 합니다. 예를 들어 IoT Hub에 보내는 모든 메시지에는 보안 자격 증명을 포함하는 토큰을 포함해야 합니다.

## <a name="access-control-and-permissions"></a>액세스 제어 및 권한

[권한](#iot-hub-permissions)은 다음과 같은 방식으로 부여할 수 있습니다.

* **허브 수준 공유 액세스 정책**. 공유 액세스 정책은 모든 조합의 [권한](#iot-hub-permissions)을 부여할 수 있습니다. [Azure Portal][lnk-management-portal]에서 또는 프로그래밍 방식으로 [Azure IoT Hub 리소스 공급자 API][lnk-resource-provider-apis]를 사용하여 정책을 정의할 수 있습니다. 새로 만든 IoT Hub에는 다음과 같은 기본 정책이 있습니다.

    - **iothubowner**: 모든 사용 권한이 있는 정책입니다.
    - **서비스**: ServiceConnect 사용 권한이 있는 정책입니다.
    - **장치**: DeviceConnect 사용 권한이 있는 정책입니다.
    - **registryRead**: RegistryRead 사용 권한이 있는 정책입니다.
    - **registryReadWrite**: RegistryRead 및 RegistryWrite 사용 권한이 있는 정책입니다.


* **장치 단위 보안 자격 증명**. 각 IoT Hub는 [장치 ID 레지스트리][lnk-identity-registry]를 포함합니다. 이 레지스트리의 각 장치의 경우 해당 장치 끝점으로 범위가 지정된 **DeviceConnect** 사용 권한을 부여하는 보안 자격 증명을 구성할 수 있습니다.

예를 들어 일반적인 IoT 솔루션에서는 다음이 적용됩니다.
- 장치 관리 구성 요소가 *registryReadWrite* 정책을 사용합니다.
- 이벤트 프로세서 구성 요소는 *서비스* 정책을 사용합니다.
- 런타임 장치 비즈니스 논리 구성 요소는 *서비스* 정책을 사용합니다.
- 개별 장치는 IoT Hub의 ID 레지스트리에 저장된 자격 증명을 사용하여 연결합니다.

## <a name="authentication"></a>인증

Azure IoT Hub는 공유 액세스 정책 및 장치 ID 레지스트리 보안 자격 증명에 대한 토큰을 확인하여 끝점에 대한 액세스를 부여합니다.

대칭 키와 같은 보안 자격 증명은 통신 중에 전송되지 않습니다.

> [AZURE.NOTE] Azure IoT Hub 리소스 공급자는 [Azure Resource Manager][lnk-azure-resource-manager]의 모든 공급자처럼 Azure 구독을 통해 보호됩니다.

보안 토큰을 생성 및 사용하는 방법에 대한 자세한 내용은 [IoT Hub 보안 토큰][lnk-sas-tokens]을 참조하세요.

### <a name="protocol-specifics"></a>프로토콜 세부 사항

지원되는 각 프로토콜(예: MQTT, AMQP 및 HTTP)은 다양한 방식으로 토큰을 전송합니다.

MQTT를 사용하는 경우 CONNECT 패킷에는 사용자 이름 필드의 ClientId, {iothubhostname}/{deviceId}에 deviceId, 암호 필드에 SAS 토큰이 있습니다. {iothubhostname}은 IoT Hub의 전체 CName이어야 합니다(예: contoso.azure devices.net).

[AMQP][lnk-amqp]을 사용하는 경우 IoT Hub는 [SASL PLAIN][lnk-sasl-plain] 및 [AMQP 클레임 기반-보안][lnk-cbs]을 지원합니다.

AMQP 클레임-기반-보안을 사용하는 경우 표준은 해당 토큰을 전송하는 방법을 지정합니다.

SASL PLAIN의 경우 **사용자 이름** 은 다음이 될 수 있습니다.

* `{policyName}@sas.root.{iothubName}` 허브 수준 토큰을 사용하는 경우입니다.
* `{deviceId}@sas.{iothubname}` 장치 범위 토큰을 사용하는 경우입니다.

두 가지 경우 모두 암호 필드는 [IoT Hub 보안 토큰][lnk-sas-tokens] 문서에 설명된 토큰을 포함합니다.

HTTP는 **권한 부여** 요청 헤더에서 유효한 토큰을 포함하여 인증을 구현합니다.

#### <a name="example"></a>예

사용자 이름(DeviceId는 대/소문자 구분): `iothubname.azure-devices.net/DeviceId`

암호(장치 탐색기를 통해 SAS 생성): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] [Azure IoT Hub SDK][lnk-sdks]는 서비스에 연결할 때 토큰을 자동으로 생성합니다. 일부 경우에 SDK는 모든 프로토콜 또는 모든 인증 방법을 지원하지 않습니다.

### <a name="special-considerations-for-sasl-plain"></a>SASL PLAIN에 대한 특별 고려 사항

AMQP와 SASL PLAIN을 사용할 때 IoT Hub에 연결한 클라이언트는 각 TCP 연결에 단일 토큰을 사용할 수 있습니다. 토큰이 만료될 때 TCP 연결은 서비스에서 연결을 끊고 다시 연결을 트리거합니다. 이 동작은 응용 프로그램 백 엔드 구성 요소에 문제를 발생시키지 않는 반면 다음과 같은 이유로 장치 쪽 응용 프로그램에 막대한 피해를 줍니다.

*  게이트웨이가 일반적으로 많은 장치를 대신하여 연결됩니다. SASL PLAIN을 사용할 때 IoT Hub에 연결한 각 장치에 고유한 TCP 연결을 만들어야 합니다. 이 시나리오는 전원 및 네트워킹 리소스의 소비를 상당히 증가시키고 각 장치 연결의 대기 시간을 늘립니다.
* 각 토큰이 만료 후에 다시 연결하기 위해 리소스의 사용이 증가하여 리소스가 제한된 장치에 부정적인 영향을 미칩니다.

## <a name="scope-hub-level-credentials"></a>허브 수준 자격 증명의 범위 지정

제한된 리소스 URI로 토큰을 만들어 허브 수준 보안 정책의 범위를 지정할 수 있습니다. 예를 들어 장치에서 장치-클라우드 메시지를 보낼 끝점은 **/devices/{deviceId}/messages/events**입니다. 또한 **DeviceConnect** 사용 권한으로 허브 수준 공유 액세스 정책을 사용하여 resourceURI가 **/devices/{deviceId}**인 토큰에 서명할 수도 있습니다. 이 방법은 장치 **deviceId**대신 메시지를 전송하는 데에만 사용할 수 있는 토큰을 생성합니다.

이 메커니즘은 [Event Hubs 게시자 정책][lnk-event-hubs-publisher-policy]과 유사하며 사용자 지정 인증 메서드를 구현하도록 합니다.

## <a name="security-tokens"></a>보안 토큰

IoT Hub는 네트워크에서 토큰이 전송되는 것을 피하기 위해 보안 토큰을 사용하여 장치 및 서비스를 인증합니다. 또한 보안 토큰은 유효 기간 및 범위가 제한됩니다. [Azure IoT Hub SDK][lnk-sdks]는 특별한 구성이 필요하지 않고 토큰을 자동으로 생성합니다. 그러나 일부 시나리오에서는 사용자가 보안 토큰을 직접 생성하고 사용해야 합니다. 여기에는 [사용자 지정 장치 인증][lnk-custom-auth]에 설명된 대로 MQTT, AMQP 또는 HTTP 표면의 직접 사용 또는 토큰 서비스 패턴의 구현이 포함됩니다.

또한 IoT Hub를 사용하면 장치가 [X.509 인증서][lnk-x509]를 통해 IoT Hub에서 인증될 수 있습니다. 

### <a name="security-token-structure"></a>보안 토큰 구조
보안 토큰을 사용하여 장치 및 서비스에서 IoT Hub의 특정 기능에 대한 시간 제한 액세스 권한을 부여합니다. 승인된 장치 및 서비스만 연결할 수 있도록 하려면 보안 토큰을 ID 레지스트리에 장치 ID로 저장된 공유 액세스 정책 키 또는 대칭 키로 서명해야 합니다.

공유 액세스 정책 키로 서명된 토큰은 공유 액세스 정책 권한과 관련된 모든 기능에 대한 액세스를 부여합니다. 한편, 장치 ID의 대칭 키로만 서명된 토큰은 관련된 장치 ID에 대해 **DeviceConnect** 권한을 부여합니다.

보안 토큰의 형식은 다음과 같습니다.

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

다음은 필요한 값입니다.

| 값 | 설명 |
| ----- | ----------- |
| {signature} | 형식의 HMAC-SHA256 서명 문자열은 `{URL-encoded-resourceURI} + "\n" + expiry`입니다. **중요**: 키는 base64에서 디코딩되며 HMAC-SHA256 계산을 수행하는 데 키로 사용됩니다. |
| {resourceURI} | 이 토큰으로 액세스할 수 있는 끝점의 URI 접두사(세그먼트별)이며 IoT Hub의 호스트 이름으로 시작합니다(프로토콜 없음). 위치(예: `myHub.azure-devices.net/devices/device1` |
| {expiry} | 1970년 1월 1일 epoch 0시 UTC 이후의 초 수에 대한 UTF8 문자열입니다. |
| {URL-encoded-resourceURI} | 소문자 URL-소문자 리소스 URI의 인코딩 |
| {policyName} | 이 토큰을 참조하는 공유 액세스 정책의 이름입니다. 장치 레지스트리 자격 증명을 참조하는 토큰의 경우가 없습니다. |

**접두사에 대한 참고**: 문자가 아니라 세그먼트에 의해 계산된 URI 접두사입니다. 예를 들어 `/a/b`는 `/a/b/c`에 대한 접두사이지만 `/a/bc`에 대한 접두사는 아닙니다.

다음은 입력 `resourceUri, signingKey, policyName, expiresInMins`의 토큰을 계산하는 Node.js 함수입니다. 다음 섹션에서는 여러 토큰 사용 사례에 대해 서로 다른 입력을 초기화하는 방법을 자세히 설명합니다.

    var crypto = require('crypto');

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // using crypto
        var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
        const hmac = crypto.createHmac('sha256', decodedPassword);
        hmac.update(toSign);
        var base64signature = hmac.digest('base64');
        var base64UriEncoded = encodeURIComponent(base64signature);

        // construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        // console.log("signature:" + token);
        return token;
    };
 
 비교를 위해 동일한 Python 코드는 다음과 같습니다.
 
    from base64 import b64encode, b64decode
    from hashlib import sha256
    from hmac import HMAC
    from urllib import urlencode
    
    def generate_sas_token(uri, key, policy_name='device', expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % (uri, int(ttl))
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())
     
        return 'SharedAccessSignature ' + urlencode({
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl)),
            'skn': policy_name
        })

> [AZURE.NOTE] IoT Hub 컴퓨터에서 토큰의 유효 기간이 확인되므로 토큰을 생성하는 컴퓨터의 시계에서 편차가 최소여야 합니다.

### <a name="use-sas-tokens-in-a-device-client"></a>장치 클라이언트에서 SAS 토큰 사용

보안 토큰을 사용하여 IoT Hub에 **DeviceConnect** 권한을 확보하는 방법은 두 가지입니다. [장치 ID 레지스트리의 대칭 장치 키](#use-a-symmetric-key-in-the-identity-registry)를 사용하거나 [공유 액세스 정책 키](#use-a-shared-access-policy)를 사용합니다.

장치에서 액세스할 수 있는 모든 기능이 `/devices/{deviceId}` 접두사가 있는 끝점에서 의도적으로 노출된다는 것을 기억하십시오.

> [AZURE.IMPORTANT] IoT Hub에서 특정 장치를 인증하는 유일한 방법은 장치 ID 대칭 키를 사용하는 것입니다. 장치 기능에 액세스하는 데 공유 액세스 정책을 사용하는 경우 솔루션은 보안 토큰을 신뢰할 수 있는 하위 구성 요소로 발급하는 구성 요소를 고려해야 합니다.

장치 연결 끝점은 다음과 같습니다(프로토콜과 관련 없음).

| 끝점 | 기능 |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | 장치-클라우드 메시지를 보냅니다. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | 클라우드-장치 메시지를 받습니다. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>ID 레지스트리에서 대칭 키 사용

장치 ID의 대칭 키를 사용하여 토큰을 생성하는 경우 토큰의 policyName(`skn`) 요소가 생략됩니다.

예를 들어, 모든 장치 기능에 액세스하기 위해 만든 토큰은 다음 매개 변수를 포함해야 합니다.

* 리소스 URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* 서명 키: `{device id}` ID에 대한 모든 대칭 키,
* 정책 이름 없음,
* 임의의 만료 시간.

위의 노드 함수를 사용하는 예제는 다음과 같습니다.

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

device1의 모든 기능에 액세스 권한을 부여하는 결과는 다음과 같습니다.

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] .NET 도구 [장치 탐색기][lnk-device-explorer]를 사용하여 보안 토큰을 생성할 수 있습니다.

### <a name="use-a-shared-access-policy"></a>공유 액세스 정책 사용

공유 액세스 정책에서 토큰을 만들 때 정책 이름 필드 `skn` 이 사용된 정책 이름으로 설정되어야 합니다. 또한 정책에서 **DeviceConnect** 권한을 부여해야 합니다.

장치 기능에 액세스하는 데 공유 액세스 정책을 사용하는 두 가지 주요 시나리오는 다음과 같습니다.

* [클라우드 프로토콜 게이트웨이][lnk-endpoints],
* 사용자 지정 인증 체계를 구현하는 데 사용된 [토큰 서비스][lnk-custom-auth].

공유 액세스 정책은 모든 장치로 연결하기 위해 잠재적으로 액세스 권한을 부여할 수 있으므로 보안 토큰을 만들 때 올바른 리소스 URI를 사용하는 것이 중요합니다. 리소스 URI를 사용하여 토큰을 특정 장치로 범위를 제한해야 하는 토큰 서비스에서 특히 중요합니다. 프로토콜 게이트웨이는 모든 장치에 대한 트래픽을 이미 조정하므로 관련성이 떨어집니다.

예를 들어 **장치** 라는 미리 생성된 공유 액세스 정책을 사용하는 토큰 서비스는 다음 매개 변수로 토큰을 만듭니다.

* 리소스 URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* 서명 키: `device` 정책의 키 중 하나,
* 정책 이름: `device`,
* 임의의 만료 시간.

위의 노드 함수를 사용하는 예제는 다음과 같습니다.

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

device1의 모든 기능에 액세스 권한을 부여하는 결과는 다음과 같습니다.

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

프로토콜 게이트웨이는 단순히 리소스 URI를 `myhub.azure-devices.net/devices`로 설정하여 모든 장치에 대해 동일한 토큰을 사용할 수 있습니다.

### <a name="use-security-tokens-from-service-components"></a>서비스 구성 요소에서 보안 토큰 사용

앞에서 설명했듯이 서비스 구성 요소는 적절한 권한을 부여하는 공유 액세스 정책을 사용하여 보안 토큰을 생성할 수 있습니다.

다음은 끝점에 노출된 서비스 기능입니다.

| 끝점 | 기능 |
| ----- | ----------- |
| `{iot hub host name}/devices` | 장치 ID를 만들기, 업데이트, 검색 및 삭제합니다. |
| `{iot hub host name}/messages/events` | 장치-클라우드 메시지를 받습니다. |
| `{iot hub host name}/servicebound/feedback` | 클라우드-장치 메시지에 대한 피드백을 받습니다. |
| `{iot hub host name}/devicebound` | 클라우드-장치 메시지를 보냅니다. |

예를 들어 **registryRead** 라는 미리 생성된 공유 액세스 정책을 사용하여 생성하는 서비스는 다음 매개 변수로 토큰을 만듭니다.

* 리소스 URI: `{IoT hub name}.azure-devices.net/devices`,
* 서명 키: `registryRead` 정책의 키 중 하나,
* 정책 이름: `registryRead`,
* 임의의 만료 시간.

    var endpoint ="myhub.azure-devices.net/devices";   var policyName = 'device';   var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

모든 장치 ID에 대한 읽기 권한을 부여하는 결과는 다음과 같습니다.

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## <a name="supported-x.509-certificates"></a>지원되는 X.509 인증서

X.509 인증서를 사용하여 IoT Hub에서 장치를 인증할 수 있습니다. 다음 내용이 포함됩니다.

-   **기존 X.509 인증서**. 장치에 X.509 인증서가 이미 연결되어 있을 수 있습니다. 장치는 이 인증서를 사용하여 IoT Hub에서 인증을 받을 수 있습니다.

-   **자체 생성 및 자체 서명 X-509 인증서**. 장치 제조업체 또는 사내 배포자는 이러한 인증서를 생성하고 장치에 해당 개인 키(및 인증서)를 저장할 수 있습니다. 이 목적을 위해 [OpenSSL][lnk-openssl] 및 [Windows SelfSignedCertificate][lnk-selfsigned] 유틸리티와 같은 도구를 사용할 수 있습니다.

-   **CA 서명 X.509 인증서**. 또한 CA(인증 기관)에서 생성 및 서명한 X.509 인증서를 사용하여 장치를 식별하고 IoT Hub에서 장치 인증을 받을 수도 있습니다.

장치는 인증을 위해 X.509 인증서 또는 보안 토큰 중 하나만 사용할 수 있습니다.

### <a name="register-an-x.509-client-certificate-for-a-device"></a>장치에 대해 X.509 클라이언트 인증서 등록

[C#용 Azure IoT 서비스 SDK][lnk-service-sdk](버전 1.0.8+)는 인증을 위해 X.509 클라이언트 인증서를 사용하는 장치의 등록을 지원합니다. 장치 가져오기/내보내기 같은 기타 API에서도 X.509 클라이언트 인증서를 지원합니다.

### <a name="c\#-support"></a>C\# 지원

**RegistryManager** 클래스는 장치를 등록하는 프로그래밍 방식을 제공합니다. 특히 **AddDeviceAsync** 및 **UpdateDeviceAsync** 메서드를 사용하면 IoT Hub 장치 ID 레지스트리에서 사용자를 등록하고 장치를 업데이트할 수 있습니다. 이러한 두 메서드는 입력으로 **Device** 인스턴스를 수락합니다. **Device** 클래스에는 사용자가 기본 및 보조 X.509 인증서 지문을 지정할 수 있도록 하는 **Authentication** 속성이 포함되어 있습니다. 지문은 X.509 인증서의 SHA-1 해시(이진 DER 인코딩 사용)를 나타냅니다. 기본 지문이나 보조 지문 또는 둘 다를 지정하는 옵션이 제공됩니다. 인증서 롤오버 시나리오를 처리하기 위해 기본 및 보조 지문이 지원됩니다.

> [AZURE.NOTE] IoT Hub는 전체 X.509 클라이언트 인증서를 요구하거나 저장하지 않으며 지문만 요구하거나 저장합니다.

X.509 클라이언트 인증서를 사용하여 장치를 등록하는 샘플 C\# 코드 조각은 다음과 같습니다.

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x.509-client-certificate-during-runtime-operations"></a>런타임 작업 중에 X.509 클라이언트 인증서 사용

[.NET용 Azure IoT 장치 SDK][lnk-client-sdk](버전 1.0.11+)는 X.509 클라이언트 인증서의 사용을 지원합니다.

### <a name="c\#-support"></a>C\# 지원

클래스 **DeviceAuthenticationWithX509Certificate**는 X.509 클라이언트 인증서를 사용하여  **DeviceClient** 인스턴스를 만들도록 지원합니다.

다음은 샘플 코드 조각입니다.

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>사용자 지정 장치 인증

[토큰][lnk-sas-tokens]을 사용하여 장치별 보안 자격 증명 및 액세스 제어를 구성하도록 IoT Hub [장치 ID 레지스트리][lnk-identity-registry]를 사용할 수 있습니다. 그러나 사용자 지정 장치 ID 레지스트리 및/또는 인증 체계에 이미 상당한 투자를 한 IoT 솔루션의 경우 *토큰 서비스*를 만들어 이 기존 인프라를 IoT Hub와 통합할 수 있습니다. 이러한 방식으로 솔루션에서 다른 IoT 기능을 사용할 수 있습니다.

토큰 서비스는 사용자 지정 클라우드 서비스입니다. **DeviceConnect** 권한으로 IoT Hub *공유 액세스 정책*을 사용하여 *device-scoped* 토큰을 만듭니다. 이러한 토큰은 장치에서 IoT Hub에 연결할 수 있게 해줍니다.

  ![토큰 서비스 패턴의 단계][img-tokenservice]

토큰 서비스 패턴의 주요 단계는 다음과 같습니다.

1. IoT Hub에 대한 **DeviceConnect** 권한으로 IoT Hub 공유 액세스 정책을 만듭니다. [Azure Portal][lnk-management-portal]에서 또는 프로그래밍 방식으로 이 정책을 만들 수 있습니다. 토큰 서비스는 이 정책을 사용하여 만들어지는 토큰을 서명합니다.
2. 장치에서 IoT Hub에 액세스해야 하는 경우 토큰 서비스에 서명된 토큰을 요청합니다. 장치는 사용자 지정 장치 ID 레지스트리/인증 체계로 인증하여 토큰 서비스가 토큰을 만드는 데 사용하는 장치 ID를 확인할 수 있습니다.
3. 토큰 서비스는 토큰을 반환합니다. 토큰은 인증되는 장치의 `deviceId`와 함께 `/devices/{deviceId}`를 `resourceURI`로 사용하여 생성됩니다. 토큰 서비스는 공유 액세스 정책을 사용하여 토큰을 생성합니다.
4. 장치는 IoT Hub에서 직접 토큰을 사용합니다.

> [AZURE.NOTE] .NET 클래스 [SharedAccessSignatureBuilder][lnk-dotnet-sas] 또는 Java 클래스 [IotHubServiceSasToken][lnk-java-sas]을 사용하여 토큰 서비스에서 토큰을 만듭니다.

토큰 서비스는 토큰 만료를 원하는 대로 설정할 수 있습니다. 토큰이 만료되면 IoT Hub가 장치 연결을 끊습니다. 이렇게 되면 장치가 토큰 서비스에 새 토큰을 요청해야 합니다. 만료 시간이 짧으면 장치 및 토큰 서비스에 대한 부하가 증가합니다.

Hub에 연결하는 장치의 경우, 장치가 연결에 장치 키가 아니라 토큰을 사용하더라도 IoT Hub 장치 ID 레지스트리에 장치를 추가해야 합니다. 따라서 장치가 토큰으로 인증하는 경우 [IoT Hub ID 레지스트리][lnk-identity-registry]에서 장치 ID를 활성화 또는 비활성화하여 장치별 액세스 제어를 계속 사용할 수 있습니다. 그러면 긴 만료 시간으로 토큰을 사용하는 위험이 완화됩니다.

### <a name="comparison-with-a-custom-gateway"></a>사용자 지정 게이트웨이와 비교

IoT Hub에 사용자 지정 ID 레지스트리/인증 구성표를 구현하는 데는 토큰 서비스 패턴을 사용하는 것이 좋습니다. IoT Hub에서 대부분의 솔루션 트래픽을 처리할 수 있기 때문입니다. 그러나 여기서는 사용자 지정 인증 체계가 프로토콜과 복잡하게 얽혀 있어 서비스에서 모든 트래픽(*사용자 지정 게이트웨이*)을 처리해야 합니다. [TLS(전송 계층 보안) 및 PSK(미리 공유한 키)][lnk-tls-psk]를 예로 들 수 있습니다. 자세한 내용은 [프로토콜 게이트웨이][lnk-protocols] 항목을 참조하세요.

## <a name="reference"></a>참조

### <a name="iot-hub-permissions"></a>IoT Hub 권한

다음 테이블에는 IoT hub에 대한 액세스를 제어하는 데 사용할 수 있는 권한이 나열되어 있습니다.

| 사용 권한            | 참고 사항 |
| --------------------- | ----- |
| **RegistryRead**      | 장치 ID 레지스트리에 대한 읽기 액세스 권한을 부여합니다. 자세한 내용은 [장치 ID 레지스트리][lnk-identity-registry]를 참조하세요. |
| **RegistryReadWrite** | 장치 ID 레지스트리에 대한 읽기 및 쓰기 액세스 권한을 부여합니다. 자세한 내용은 [장치 ID 레지스트리][lnk-identity-registry]를 참조하세요. |
| **ServiceConnect**    | 클라우드 서비스 지향 통신 및 모니터링 중인 끝점에 대한 액세스를 부여합니다. 예를 들어 장치-클라우드 메시지를 받고 클라우드-장치 메시지를 보내며 해당 전달 승인을 검색할 수 있는 권한을 백 엔드 클라우드 서비스에 부여합니다. |
| **DeviceConnect**     | 장치 지향 통신 끝점에 대한 액세스를 부여합니다. 예를 들어 장치-클라우드 메시지를 보내고 클라우드-장치 메시지를 받을 수 있는 권한을 클라우드에 부여합니다. 이 권한은 장치에서 사용됩니다. |

### <a name="additional-reference-material"></a>추가 참조 자료

개발자 가이드의 다른 참조 자료:

- [IoT Hub 끝점][lnk-endpoints]에서는 각 IoT Hub가 런타임 및 관리 작업에 노출하는 다양한 끝점을 설명합니다.
- [제한 및 할당량][lnk-quotas]에서는 IoT Hub 서비스에 적용되는 할당량과 서비스를 언제 사용할지 예상하는 제한 동작을 설명합니다.
- [IoT Hub 장치 및 서비스 SDK][lnk-sdks]에는 IoT Hub와 상호 작용하는 장치 및 서비스 응용 프로그램을 개발할 때 사용할 수 있는 다양한 언어 SDK가 나열되어 있습니다.
- [쌍, 메서드 및 작업용 쿼리 언어][lnk-query]에는 장치 쌍, 메서드 및 작업을 IoT Hub에서 검색하는 데 사용할 수 있는 쿼리 언어가 설명되어 있습니다.
- [IoT Hub MQTT 지원][lnk-devguide-mqtt]에는 MQTT 프로토콜에 대한 IoT Hub 지원에 대하여 자세한 정보가 제공됩니다.

## <a name="next-steps"></a>다음 단계

IoT Hub 액세스를 제어하는 방법에 대해 알아봤으니 다음과 같은 개발자 가이드 항목을 살펴보세요.

- [장치 쌍을 사용하여 상태 및 구성 동기화][lnk-devguide-device-twins]
- [장치에 직접 메서드 호출][lnk-devguide-directmethods]
- [여러 장치에서 작업 예약][lnk-devguide-jobs]

이 문서에서 설명한 일부 개념을 시도해 보려면 다음과 같은 IoT Hub 자습서를 살펴보세요.

- [Azure IoT Hub 시작][lnk-getstarted-tutorial]
- [IoT Hub를 사용하여 클라우드-장치 메시지를 보내는 방법][lnk-c2d-tutorial]
- [IoT Hub 장치-클라우드 메시지를 처리하는 방법][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md



<!--HONumber=Oct16_HO2-->



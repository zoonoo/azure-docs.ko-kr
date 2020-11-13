---
title: Azure IoT Edge를 사용 하 여 게시 및 구독 | Microsoft Docs
description: IoT Edge MQTT broker를 사용 하 여 메시지 게시 및 구독
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 941435e90f91a4c3a4e41c2869e35157da41d8b0
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592178"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>Azure IoT Edge 게시 및 구독

Azure IoT Edge MQTT broker를 사용 하 여 메시지를 게시 하 고 구독할 수 있습니다. 이 문서에서는이 브로커에 연결 하 고, 사용자 정의 항목을 통해 메시지를 게시 및 구독 하며, IoT Hub 메시징 기본 형식을 사용 하는 방법을 보여 줍니다. IoT Edge MQTT broker는 IoT Edge 허브에 기본 제공 됩니다. 자세한 내용은 [IoT Edge 허브의 중개 기능](iot-edge-runtime.md)을 참조 하세요.

> [!NOTE]
> IoT Edge MQTT broker는 현재 공개 미리 보기로 제공 됩니다.

## <a name="pre-requisites"></a>필수 구성 요소

- 유효한 구독이 있는 Azure 계정
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) `azure-iot` CLI 확장이 설치 된 Azure CLI. 자세한 내용은 [azure Azure CLI에 대 한 Azure IoT 확장 설치 단계](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-iot)를 참조 하세요.
- SKU의 **IoT Hub** 는 F1, S1, S2 또는 S3 중 하나입니다.
- **1.2 이상 버전의 IoT Edge 장치가** 있어야 합니다. IoT Edge MQTT broker는 현재 공개 미리 보기로 제공 되므로 edgeHub 컨테이너에서 다음 환경 변수를 true로 설정 하 여 MQTT broker를 사용 하도록 설정 합니다.

    - experimentalFeatures__enabled
    - mqttbroker__enabled

- IoT Edge 장치에 설치 된 **클라이언트를 Mosquitto** 합니다. 이 문서에서는 [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) 및 [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html)를 포함 하는 널리 사용 되는 Mosquitto 클라이언트를 사용 합니다. 다른 MQTT 클라이언트를 대신 사용할 수 있습니다. Ubuntu 장치에 Mosquitto 클라이언트를 설치 하려면 다음 명령을 실행 합니다.

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Mosquitto 서버는 MQTT 포트 (8883 및 1883)를 차단 하 고 IoT Edge 허브와 충돌할 수 있으므로 설치 하지 마십시오.

## <a name="connect-to-iot-edge-hub"></a>IoT Edge 허브에 연결

IoT Edge 허브에 연결 하는 단계는 [일반 MQTT 클라이언트를 사용 하 여 IoT Hub에 연결 문서](../iot-hub/iot-hub-mqtt-support.md) 또는 [IoT Edge 허브 문서의 개념 설명](iot-edge-runtime.md)에 설명 된 것과 동일한 단계를 따릅니다. 이러한 단계는 다음과 같습니다.

1. 선택적으로 MQTT 클라이언트는 TLS (전송 계층 보안)를 사용 하 여 IoT Edge 허브와의 *보안 연결* 을 설정 합니다.
2. MQTT 클라이언트는 IoT Edge 허브에 대해 자신을 *인증* 합니다.
3. IoT Edge 허브는 권한 부여 정책에 따라 MQTT 클라이언트에 *권한을* 부여 합니다.

### <a name="secure-connection-tls"></a>보안 연결 (TLS)

TLS (전송 계층 보안)는 클라이언트와 IoT Edge 허브 간의 암호화 된 통신을 설정 하는 데 사용 됩니다.

TLS를 사용 하지 않도록 설정 하려면 MQTT (port 1883)를 사용 하 여 edgeHub 컨테이너를 포트 1883에 바인딩합니다.

TLS를 사용 하도록 설정 하기 위해 클라이언트가 MQTTS (포트 8883)를 MQTTS broker에 연결 하는 경우 TLS 채널이 시작 됩니다. Broker는 클라이언트의 유효성을 검사 해야 하는 인증서 체인을 보냅니다. 인증서 체인의 유효성을 검사 하려면 MQTT broker의 루트 인증서를 클라이언트에 신뢰할 수 있는 인증서로 설치 해야 합니다. 루트 인증서를 신뢰할 수 없는 경우 클라이언트 라이브러리는 인증서 확인 오류가 발생 하 여 MQTT broker에서 거부 됩니다. 클라이언트에 broker의이 루트 인증서를 설치 하기 위해 수행 하는 단계는 [투명 게이트웨이](how-to-create-transparent-gateway.md) 사례와 동일 하며 [다운스트림 장치 준비](how-to-connect-downstream-device.md#prepare-a-downstream-device) 설명서에 설명 되어 있습니다.

### <a name="authentication"></a>인증

MQTT 클라이언트는 자신을 인증 하기 위해 먼저 MQTT broker에 연결 패킷을 전송 하 여 해당 이름으로 연결을 시작 해야 합니다. 이 패킷은 세 가지 인증 정보 `client identifier` , 즉, 및를 제공 `username` 합니다 `password` .

-   `client identifier`필드는 IoT Hub의 장치 또는 모듈 이름 이름입니다. 이 도구는 다음 구문을 사용합니다.

    - 장치: `<device_name>`

    - 모듈의 경우: `<device_name>/<module_name>`

   MQTT broker에 연결 하려면 장치 또는 모듈을 IoT Hub에 등록 해야 합니다.

   Broker에서는 동일한 자격 증명을 사용 하는 두 클라이언트의 연결을 허용 하지 않습니다. 두 번째 클라이언트가 동일한 자격 증명을 사용 하 여 연결 되는 경우 broker는 이미 연결 된 클라이언트의 연결을 끊습니다.

- `username`필드는 장치 또는 모듈 이름에서 파생 되며, 다음 구문을 사용 하 여 장치가 속한 IoTHub 이름에서 파생 됩니다.

    - 장치: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

    - 모듈의 경우: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- `password`연결 패킷의 필드는 인증 모드에 따라 달라 집니다.

    - [대칭 키 인증](how-to-authenticate-downstream-device.md#symmetric-key-authentication)의 경우 `password` 필드는 SAS 토큰입니다.
    - [X.509 자체 서명 된 인증](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)의 경우 `password` 필드가 표시 되지 않습니다. 이 인증 모드에서는 TLS 채널이 필요 합니다. 클라이언트는 TLS 연결을 설정 하기 위해 포트 8883에 연결 해야 합니다. TLS 핸드셰이크 중에 MQTT broker는 클라이언트 인증서를 요청 합니다. 이 인증서는 클라이언트의 id를 확인 하는 데 사용 되므로 `password` 나중에 연결 패킷을 보낼 때이 필드는 필요 하지 않습니다. 클라이언트 인증서와 암호 필드를 모두 보내면 오류가 발생 하 고 연결이 닫힙니다. 일반적으로 MQTT 라이브러리 및 TLS 클라이언트 라이브러리를 통해 연결을 시작할 때 클라이언트 인증서를 보낼 수 있습니다. [클라이언트 인증용으로 X509 인증서 사용](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)섹션에서 단계별 예제를 확인할 수 있습니다.

IoT Edge에서 배포한 모듈은 [대칭 키 인증](how-to-authenticate-downstream-device.md#symmetric-key-authentication) 을 사용 하 고 로컬 [IoT Edge 작업 API](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) 를 호출 하 여 오프 라인 상태인 경우에도 프로그래밍 방식으로 SAS 토큰을 가져올 수 있습니다.

### <a name="authorization"></a>권한 부여

MQTT 클라이언트가 IoT Edge 허브에 인증 되 면 연결할 권한이 있어야 합니다. 연결 되 면 특정 항목을 게시 하거나 구독할 수 있는 권한이 있어야 합니다. 이러한 권한 부여는 권한 부여 정책에 따라 IoT Edge 허브에서 부여 합니다. 권한 부여 정책은 해당 쌍을 통해 IoT Edge 허브로 전송 되는 JSON 구조로 표현 되는 문 집합입니다. IoT Edge 허브 쌍을 편집 하 여 해당 권한 부여 정책을 구성 합니다.

> [!NOTE]
> 공개 미리 보기의 경우 MQTT broker의 인증 정책 편집은 Visual Studio, Visual Studio Code 또는 Azure CLI를 통해서만 사용할 수 있습니다. 현재 Azure Portal는 IoT Edge 허브 쌍 및 해당 권한 부여 정책 편집을 지원 하지 않습니다.

각 권한 부여 정책 문은 `identities` , 또는 효과의 조합으로 구성 됩니다 `allow` `deny` `operations` `resources` .

- `identities` 정책의 주체를 설명 합니다. `client identifier`연결 패킷의 클라이언트에서 보낸에 매핑되어야 합니다.
- `allow` or `deny` effect 작업을 허용할지 또는 거부할지를 정의 합니다.
- `operations` 권한을 부여할 작업을 정의 합니다. `mqtt:connect`, `mqtt:publish` 및 `mqtt:subscribe` 는 현재 지원 되는 세 가지 동작입니다.
- `resources` 정책의 개체를 정의 합니다. [Mqtt 와일드 카드](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)를 사용 하 여 정의 된 토픽 또는 토픽 패턴이 될 수 있습니다.

다음은 "rogue_client" 클라이언트의 연결을 명시적으로 허용 하지 않는 권한 부여 정책의 예입니다. Azure IoT 클라이언트를 연결 하 여 "sensor_1"를 토픽에 게시할 수 있도록 허용 합니다 `events/alerts` .

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

권한 부여 정책을 작성할 때 염두에 두어야 할 몇 가지 사항은 다음과 같습니다.
- 쌍 스키마 버전 1.2이 필요 합니다. `$edgeHub`
- 기본적으로 모든 작업은 거부 됩니다.
- 권한 부여 문은 JSON 정의에 표시 되는 순서 대로 평가 됩니다. 먼저를 확인 한 `identities` 다음 요청에 일치 하는 첫 번째 허용 또는 거부 문을 선택 합니다. Allow와 deny 문 사이에 충돌이 발생 하는 경우 deny 문은 wins에 적용 됩니다.
- 권한 부여 정책에서 여러 변수 (예: 대체)를 사용할 수 있습니다.
    - `{{iot:identity}}` 현재 연결 된 클라이언트의 id를 나타냅니다. 예를 들어 `myDevice` 장치의 경우 `myEdgeDevice/SampleModule` 모듈의 경우입니다.
    - `{{iot:device_id}}` 현재 연결 된 장치의 id를 나타냅니다. 예를 들어 `myDevice` 장치의 경우 `myEdgeDevice` 모듈의 경우입니다.
    - `{{iot:module_id}}` 현재 연결 된 모듈의 id를 나타냅니다. 예를 들어, 장치의 경우에는 ' '입니다 `SampleModule` .
    - `{{iot:this_device_id}}` 권한 부여 정책을 실행 하는 IoT Edge 장치의 id를 나타냅니다. 예: `myIoTEdgeDevice`.

IoT hub에 대 한 권한 부여 항목은 사용자 정의 항목과 약간 다르게 처리 됩니다. 기억할 주요 사항은 다음과 같습니다.
- Azure IoT 장치 또는 모듈에는 IoT Edge hub MQTT broker에 연결 하기 위한 명시적인 권한 부여 규칙이 필요 합니다. 기본 연결 권한 부여 정책은 아래에 제공 됩니다.
- Azure IoT 장치 또는 모듈은 명시적 권한 부여 규칙 없이 기본적으로 고유한 IoT hub 토픽에 액세스할 수 있습니다. 그러나이 경우 부모/자식 관계에서 권한 부여가 생기고 이러한 관계를 설정 해야 합니다. IoT Edge 모듈은 자동으로 IoT Edge 장치의 자식으로 설정 되지만 장치는 명시적으로 IoT Edge 게이트웨이의 자식으로 설정 해야 합니다.
- Azure IoT 장치 또는 모듈은 적절 한 명시적인 권한 부여 규칙을 정의 하는 다른 장치 또는 모듈의 IoT hub 토픽을 비롯 한 토픽에 액세스할 수 있습니다.

다음은 모든 Azure IoT 장치 또는 모듈을 broker에 **연결** 하도록 설정 하는 데 사용할 수 있는 기본 권한 부여 정책입니다.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

이제 IoT Edge MQTT broker에 연결 하는 방법을 이해 했으므로 먼저 사용자 정의 항목에 대 한 메시지를 게시 하 고 구독 하는 데 사용할 수 있는 방법, IoT hub 토픽 및 마지막으로 다른 MQTT로 메시지를 구독 하는 방법을 알아보겠습니다.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>사용자 정의 항목 게시 및 구독

이 문서에서는 토픽에 등록 하는 **sub_client** 라는 클라이언트와 토픽에 게시 되는 **pub_client** 라는 다른 클라이언트를 사용 합니다. [대칭 키 인증](how-to-authenticate-downstream-device.md#symmetric-key-authentication) 을 사용 하지만 [x.509 자체 서명 인증](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) 또는 [x.509 자체 서명 된 인증](./how-to-authenticate-downstream-device.md#x509-self-signed-authentication)을 사용 하 여 동일한 작업을 수행할 수 있습니다.

### <a name="create-publisher-and-subscriber-clients"></a>게시자 및 구독자 클라이언트 만들기

IoT Hub에 두 IoT 장치를 만들고 암호를 가져옵니다. 터미널에서 Azure CLI를 사용 하 여 다음을 수행 합니다.

1. IoT Hub에서 두 개의 IoT 장치를 만들어 IoT Edge 장치에 부모로 합니다.

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. SAS 토큰을 생성 하 여 암호를 가져옵니다.

    - 장치:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
       ```
    
       여기서 3600은 SAS 토큰의 기간 (초)입니다 (예: 3600 = 1 시간).
    
    - 모듈의 경우:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
       ```
    
       여기서 3600은 SAS 토큰의 기간 (초)입니다 (예: 3600 = 1 시간).

3. 출력에서 "sas" 키에 해당 하는 값인 SAS 토큰을 복사 합니다. 다음은 위 Azure CLI 명령의 출력 예제입니다.

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>게시자 및 구독자 클라이언트 권한 부여

게시자와 구독자에 게 권한을 부여 하려면 Azure CLI, Visual Studio 또는 Visual Studio code를 통해 IoT Edge 허브 쌍을 편집 하 여 다음 권한 부여 정책을 포함 합니다.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ],
                     }
                  ],
               },
               {
                  "identities": [
                     "pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ],
                     }
                  ],
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>TLS 없이 대칭 키 인증

#### <a name="subscribe"></a>구독

**Sub_client** mqtt 클라이언트를 mqtt broker에 연결 하 고 `test_topic` IoT Edge 장치에서 다음 명령을 실행 하 여를 구독 합니다.

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

`<edge_device_address>`  =  `localhost` 클라이언트가 IoT Edge와 동일한 장치에서 실행 되 고 있기 때문입니다.

이 첫 번째 예제에서는 TLS 없이 포트 1883 (MQTT)를 사용 합니다. 포트 8883 (MQTTS)를 사용 하는 또 다른 예 (예: TLS를 사용 하는 경우)는 다음 섹션에 나와 있습니다.

이제 **sub_client** mqtt 클라이언트가 시작 되었으며에서 들어오는 메시지를 기다리고 `test_topic` 있습니다.

#### <a name="publish"></a>게시

**Pub_client** mqtt 클라이언트를 mqtt broker에 연결 하 고, `test_topic` 다른 터미널에서 IoT Edge 장치에서 다음 명령을 실행 하 여 위와 동일 하 게 메시지를 게시 합니다.

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

`<edge_device_address>`  =  `localhost` 클라이언트가 IoT Edge와 동일한 장치에서 실행 되 고 있기 때문입니다.

명령을 실행 하 **sub_client** mqtt 클라이언트는 "hello" 메시지를 받습니다.

### <a name="symmetric-keys-authentication-with-tls"></a>TLS를 사용 하 여 대칭 키 인증

TLS를 사용 하도록 설정 하려면 포트를 1883 (MQTT)에서 8883 (MQTT)로 변경 하 고 mqtt broker에서 보낸 인증서 체인의 유효성을 검사 하려면 MQTT broker의 루트 인증서가 있어야 합니다. 이 작업은 [보안 연결 (TLS)](#secure-connection-tls)섹션에 제공 된 단계에 따라 수행할 수 있습니다.

위의 예제에서 클라이언트는 MQTT broker와 동일한 장치에서 실행 되므로 포트 번호를 1883 (MQTT)에서 8883 (MQTT)로 변경 하 여 TLS를 사용 하도록 설정 하는 것과 동일한 단계가 적용 됩니다.

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>IoT Hub 토픽 게시 및 구독

[Azure IoT 장치 sdk](https://github.com/Azure/azure-iot-sdks) 는 이미 클라이언트에서 IoT Hub 작업을 수행할 수 있지만 사용자 정의 항목에 대 한 게시/구독을 허용 하지 않습니다. IoT Hub 기본 프로토콜이 적용 되는 한 게시 및 구독 의미 체계를 사용 하 여 MQTT 클라이언트를 사용 하 여 IoT Hub 작업을 수행할 수 있습니다. 이러한 프로토콜이 작동 하는 방식을 이해 하기 위해 specificities을 진행 합니다.

### <a name="send-telemetry-data-to-iot-hub"></a>IoT Hub에 원격 분석 데이터 보내기

IoT Hub에 원격 분석 데이터를 보내는 것은 사용자 정의 항목에 게시 하는 것과 비슷하지만 특정 IoT Hub 토픽을 사용 하는 것과 비슷합니다.

- 장치의 경우 항목에서 원격 분석이 전송 됩니다. `devices/<device_name>/messages/events`
- 모듈의 경우 항목에 대 한 원격 분석이 전송 됩니다. `devices/<device_name>/<module_name>/messages/events`

또한 `FROM /messages/* INTO $upstream` IoT Edge MQTT broker에서 IoT hub로 원격 분석을 보내는 등의 경로를 만듭니다. 라우팅에 대 한 자세한 내용은 [선언 경로](module-composition.md#declare-routes)를 참조 하세요.

### <a name="get-twin"></a>쌍 가져오기

장치/모듈 쌍 가져오기는 일반적인 MQTT 패턴이 아닙니다. 클라이언트는 IoT Hub 서비스를 제공 하는 쌍에 대 한 요청을 발급 해야 합니다.

Twins를 수신 하기 위해 클라이언트는 IoT Hub 특정 항목을 구독 해야 `$iothub/twin/res/#` 합니다. 이 항목 이름은 IoT Hub에서 상속 되며, 모든 클라이언트는 동일한 항목을 구독 해야 합니다. 장치 또는 모듈은 서로 쌍을 받는 것을 의미 하지는 않습니다. IoT Hub 및 IoT Edge 허브는 모든 장치가 동일한 항목 이름을 수신 하는 경우에도 어떤 쌍을 전달할지 알고 있습니다. 

구독이 만들어지면 클라이언트는 임의의 식별자 인 IoT Hub 특정 항목에 메시지를 게시 하 여 쌍을 요청 해야 합니다 `$iothub/twin/GET/?rid=<request_id>/#`  `<request_id>` . 그런 다음 IoT hub는 클라이언트가 구독 하는 토픽의 요청 된 데이터로 응답을 보냅니다 `$iothub/twin/res/200/?rid=<request_id>` . 클라이언트에서 응답과 요청을 연결 하는 방법입니다.

### <a name="receive-twin-patches"></a>수신 쌍 패치

쌍 패치를 수신 하려면 클라이언트가 특수 IoTHub 토픽을 구독 해야 `$iothub/twin/PATCH/properties/desired/#` 합니다. 구독이 만들어지면 클라이언트는이 항목의 IoT Hub에서 보낸 쌍 패치를 수신 합니다. 

### <a name="receive-direct-methods"></a>직접 메서드 받기

직접 메서드를 받는 것은 클라이언트에서 호출을 수신 했음을 확인 해야 한다는 추가로 전체 쌍을 받는 방법과 매우 비슷합니다. 먼저 클라이언트에서 IoT hub를 구독 합니다. 특별 항목을 참조 `$iothub/methods/POST/#` 하세요. 그런 다음이 항목에서 직접 메서드를 받은 후 클라이언트는 `rid` 직접 메서드를 받은 하위 토픽에서 요청 식별자를 추출 하 고 마지막으로 IoT hub에 대 한 확인 메시지를 게시 해야 `$iothub/methods/res/200/<request_id>` 합니다.

### <a name="send-direct-methods"></a>직접 메서드 보내기

직접 메서드를 보내는 것은 HTTP 호출 이므로 MQTT broker를 통해 이동 하지 않습니다. 직접 메서드를 IoT hub에 보내려면 [직접 메서드 이해 및 호출](../iot-hub/iot-hub-devguide-direct-methods.md)을 참조 하세요. 직접 메서드를 다른 모듈에 로컬로 보내려면이 [Azure IOT c # SDK 직접 메서드 호출 예제](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597)를 참조 하세요.

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>MQTT broker를 게시 하 고 구독 합니다.

두 MQTT broker를 연결 하기 위해 IoT Edge 허브에는 MQTT 브리지가 포함 됩니다. MQTT 브리지는 일반적으로 실행 되는 MQTT broker를 다른 MQTT broker에 연결 하는 데 사용 됩니다. 일반적으로 로컬 트래픽의 하위 집합만 다른 broker로 푸시됩니다.

> [!NOTE]
> IoT Edge 허브 브리지는 현재 중첩 된 IoT Edge 장치 간에만 사용할 수 있습니다. IoT hub는 완전 한 기능을 갖춘 MQTT broker가 아니므로 IoT hub에 데이터를 보내는 데 사용할 수 없습니다. IoT hub MQTT broker 기능 지원에 대 한 자세한 내용은 [mqtt 프로토콜을 사용 하 여 iot hub와 통신](../iot-hub/iot-hub-mqtt-support.md)을 참조 하세요. IoT Edge 장치를 중첩 하는 방법에 대해 자세히 알아보려면 [다운스트림 IoT Edge 장치를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) 을 참조 하세요. 

중첩 된 구성에서 IoT Edge 허브 MQTT 브리지는 부모 MQTT broker의 클라이언트 역할을 하기 때문에 자식 EdgeHub가 브리지가 구성 된 특정 사용자 정의 항목을 게시 하 고 구독할 수 있도록 부모 EdgeHub에 대 한 권한 부여 규칙을 설정 해야 합니다.

IoT Edge MQTT 브리지는 해당 쌍을 통해 IoT Edge 허브로 전송 되는 JSON 구조를 통해 구성 됩니다. IoT Edge 허브 쌍을 편집 하 여 MQTT 브리지를 구성 합니다.

> [!NOTE]
> 공개 미리 보기의 경우 MQTT 브리지의 구성은 Visual Studio, Visual Studio Code 또는 Azure CLI를 통해서만 사용할 수 있습니다. 현재 Azure Portal는 IoT Edge 허브 쌍 및 MQTT 브리지 구성을 편집 하는 기능을 지원 하지 않습니다.

IoT Edge hub MQTT broker를 여러 외부 브로커에 연결 하도록 MQTT 브리지를 구성할 수 있습니다. 각 외부 브로커에 대해 다음 설정이 필요 합니다.

- `endpoint` 연결할 원격 MQTT broker의 주소입니다. 현재 부모 IoT Edge 장치만 지원 되며 변수에 의해 정의 됩니다 `$upstream` .
- `settings` 끝점에 대해 브리지로 연결할 항목을 정의 합니다. 끝점 마다 여러 설정이 있을 수 있으며이를 구성 하는 데 사용 되는 값은 다음과 같습니다.
    - `direction`: `in` 원격 브로커 항목을 구독 하거나 `out` 원격 브로커 항목에 게시
    - `topic`: 일치할 핵심 토픽 패턴입니다. [Mqtt 와일드 카드](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) 를 사용 하 여이 패턴을 정의할 수 있습니다. 로컬 브로커 및 원격 브로커의이 토픽 패턴에는 서로 다른 접두사를 적용할 수 있습니다.
    - `outPrefix`: 원격 브로커의 패턴에 적용 되는 접두사입니다 `topic` .
    - `inPrefix`: 로컬 브로커의 패턴에 적용 되는 접두사입니다 `topic` .

다음은 동일한 항목에서 부모 IoT Edge 장치의 항목에 대해 받은 모든 메시지를 `alerts/#` 자식 IoT Edge 장치에 게시 하 고 자식 IoT Edge 장치의 토픽에서 보낸 모든 메시지를 `/local/telemetry/#` 항목의 부모 IoT Edge 장치로 게시 하는 IoT Edge mqtt 브리지 구성의 예입니다 `/remote/messages/#` .

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "",
                    "inPrefix": "/local/telemetry",
                    "outPrefix": "/remote/messages"
                }
            ]
        }]
    }
}
```
IoT Edge hub MQTT 브리지에 대 한 기타 참고 사항:
- Mqtt broker를 사용 하 고 해당 IoT Edge을 지정 된 구성 (예: 지정 된 구성)에서 사용 하는 경우 MQTT 프로토콜이 자동으로 업스트림 프로토콜로 사용 됩니다. `parent_hostname` 업스트림 프로토콜에 대 한 자세한 내용은 [클라우드 통신](iot-edge-runtime.md#cloud-communication)을 참조 하세요. 중첩 된 구성에 대해 자세히 알아보려면 [Azure IoT Edge 게이트웨이에 다운스트림 IoT Edge 장치 연결](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[IoT Edge 허브 이해](iot-edge-runtime.md#iot-edge-hub)

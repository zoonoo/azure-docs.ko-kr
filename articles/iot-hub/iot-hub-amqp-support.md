---
title: Azure IoT Hub AMQP 지원 이해 | Microsoft Docs
description: 개발자 가이드-IoT Hub에 연결 하는 장치에 대 한 지원 AMQP 프로토콜을 사용 하는 장치 지향 및 서비스 지향 끝점입니다. 기본 제공 Azure IoT 장치 Sdk의에서 AMQP 지원에 대 한 정보를 포함 합니다.
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: 703e2c842fb42bad8aa112d84c516a29c2327378
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473508"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>AMQP 프로토콜을 사용 하 여 IoT hub와 통신

IoT Hub에서 지 원하는 [AMQP 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) 다양 한 장치 지향 및 서비스 지향 끝점을 통해 기능을 제공 합니다. 이 문서에서는 IoT Hub 기능을 사용 하기 위해 IoT Hub에 연결 하기 위해 AMQP 클라이언트의 사용을 설명 합니다.

## <a name="service-client"></a>서비스 클라이언트

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>연결 및 IoT Hub (서비스 클라이언트)에 인증
AMQP를 사용 하 여 IoT Hub에 연결 하려면 클라이언트를 사용할 수는 [클레임 기반 보안 (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) 하거나 [단순한 인증 및 보안 레이어 (SASL) 인증](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)합니다.

다음 정보는 서비스 클라이언트에 대 한 필요 합니다.

| 정보 | Value | 
|-------------|--------------|
| IoT Hub 호스트 이름 | `<iot-hub-name>.azure-devices.net` |
| 키 이름 | `service` |
| 액세스 키 | 서비스에 연결 된 기본 또는 보조 키 |
| 공유 액세스 서명 | 다음 형식으로 단기 실행 SAS: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` (이 서명을 생성 하는 코드를 찾을 수 있습니다 [여기](./iot-hub-devguide-security.md#security-token-structure)).


사용 하 여 아래 코드 조각 [python에서 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python) 보낸 사람 링크를 통해 IoT hub에 연결 합니다.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoking-cloud-to-device-messages-service-client"></a>클라우드-장치 메시지 (서비스 클라이언트)를 호출합니다.
서비스 및 IoT Hub 간 및 장치와 IoT Hub 클라우드-장치 메시지 교환 설명 [여기](iot-hub-devguide-messages-c2d.md)합니다. 서비스 클라이언트는 아래에 설명 된 메시지를 보내고 장치에서 이전에 보낸된 메시지에 대 한 피드백을 수신 하는 두 개의 링크를 사용 합니다.

| 만든 사람 | 링크 유형 | 링크 경로 | 설명 |
|------------|-----------|-----------|-------------|
| 서비스 | 보낸 사람 링크 | `/messages/devicebound` | 장치에 보내는 C2D 메시지 서비스에서이 링크에 전송 됩니다. 이 링크를 통해 보낸 메시지가 해당 `To` 속성이 대상 장치의 받는 사람 링크 경로를 설정 합니다: 즉, `/devices/<deviceID>/messages/devicebound`. |
| 서비스 | 받는 사람 링크 | `/messages/serviceBound/feedback` | 완료, 거부 및 중단 피드백 메시지 서비스에서이 링크를 수신 하는 장치에서 제공 될 예정입니다. 참조 [여기](./iot-hub-devguide-messages-c2d.md#message-feedback) 피드백 메시지에 대 한 자세한 내용은 합니다. |

아래 코드 조각에 C2D 메시지를 만들고 사용 하 여 장치에 전송 하는 방법을 보여 줍니다 [python에서 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python)합니다.

```python
import uuid
# Create a message and set message property 'To' to the devicebound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message using the send client created and connected IoT Hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if not needed
send_client.close()
```

피드백을 받으려면 서비스 클라이언트에는 수신자 링크를 만듭니다. 아래 코드 조각에는 사용 하는 방법을 보여 줍니다. [python에서 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python)합니다.

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Recreate the URI using the feedback path above and authenticate
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
  print('received a message')
  # Check content_type in message property to identify feedback messages coming from device
  if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
    msg_body_raw = msg.get_data()
    msg_body_str = ''.join(msg_body_raw)
    msg_body = json.loads(msg_body_str)
    print(json.dumps(msg_body, indent=2))
    print('******************')
    for feedback in msg_body:
      print('feedback received')
      print('\tstatusCode: ' + str(feedback['statusCode']))
      print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
      print('\tdeviceId: ' + str(feedback['deviceId']))
      print
  else:
    print('unknown message:', msg.properties.content_type)
```

C2D 피드백 메시지의 콘텐츠 형식이 위와 같이 `application/vnd.microsoft.iothub.feedback.json` 원본 메시지의 배달 상태를 유추 하는 JSON 본문의 속성을 사용할 수 있습니다.
* 키 `statusCode` 피드백 본문에 다음이 값 중 하나: `['Success', 'Expired', 'DeliveryCountExceeded', 'Rejected', 'Purged']`합니다.
* 키 `deviceId` 피드백 본문에 대상 장치의 ID입니다.
* 키 `originalMessageId` 피드백 본문에는 서비스에서 전송 된 원래 C2D 메시지의 ID입니다. 이 피드백 C2D 메시지를 상호 연결할 수 합니다.

### <a name="receive-telemetry-messages-service-client"></a>(서비스 클라이언트) 원격 분석 메시지를 수신 합니다.


### <a name="additional-notes"></a>추가 메모
* AMQP 연결은 네트워크 결함, 또는 (코드에서 생성) 인증 토큰의 만료로 인해 중단 될 수 있습니다. 서비스 클라이언트는 이러한 상황을 처리 하 고 연결 및 필요한 경우 링크를 다시 설정 해야 합니다. 인증 토큰 만료의 사례에 대 한 클라이언트 연결 삭제를 방지 하려면 해당 만료 전에 토큰을 갱신할 미리 수 있습니다.
* 경우에 따라 클라이언트를 올바르게 처리 링크 리디렉션 수 있어야 합니다. 이 작업을 수행 하는 방법에 AMQP 클라이언트 설명서를 참조 하세요.

### <a name="receive-cloud-to-device-messages-device-and-module-client"></a>클라우드-장치 메시지가 (장치 및 모듈 클라이언트)
장치 쪽에서 사용 되는 AMQP 링크는 다음과 같습니다.

| 만든 사람 | 링크 유형 | 링크 경로 | 설명 |
|------------|-----------|-----------|-------------|
| 디바이스 | 받는 사람 링크 | `/devices/<deviceID>/messages/devicebound` | 장치를 대상으로 하는 C2D 메시지는 각 대상 장치에서이 링크를 수신 됩니다. |
| 디바이스 | 보낸 사람 링크 | `/messages/serviceBound/feedback` | C2D 메시지 피드백 장치에서이 링크를 통해 서비스에 전송 합니다. |
| 모듈 | 받는 사람 링크 | `/devices/<deviceID>/modules/<moduleID>/messages/devicebound` | 이 링크를 각 대상 모듈 모듈으로 C2D 메시지 수신 됩니다. |
| 모듈 | 보낸 사람 링크 | `/messages/serviceBound/feedback` | C2D 메시지 피드백 모듈에서이 링크를 통해 서비스에 전송 합니다. |


## <a name="next-steps"></a>다음 단계

AMQP 프로토콜에 대 한 자세한 내용은 참조는 [AMQP v1.0 사양을](http://www.amqp.org/sites/amqp.org/files/amqp.pdf)합니다.

IoT Hub 메시징에 대 한 자세한 내용은 다음을 참조 하세요.

* [클라우드-장치 메시지](./iot-hub-devguide-messages-c2d.md)
* [추가 프로토콜 지원](iot-hub-protocol-gateway.md)
* [MQTT 프로토콜에 대 한 지원](./iot-hub-mqtt-support.md)

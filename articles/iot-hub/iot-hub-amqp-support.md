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
ms.openlocfilehash: d256faa42161e276e165f95c944b9f58ac4a8927
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297412"
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

| 만든 사람 | 링크 형식 | 링크 경로 | 설명 |
|------------|-----------|-----------|-------------|
| 서비스 | 보낸 사람 링크 | `/messages/devicebound` | 장치에 보내는 C2D 메시지 서비스에서이 링크에 전송 됩니다. 이 링크를 통해 보낸 메시지가 해당 `To` 속성이 대상 장치의 받는 사람 링크 경로를 설정 합니다: 즉, `/devices/<deviceID>/messages/devicebound`. |
| 서비스 | 받는 사람 링크 | `/messages/serviceBound/feedback` | 완료, 거부 및 중단 피드백 메시지 서비스에서이 링크를 수신 하는 장치에서 제공 될 예정입니다. 피드백 메시지에 대 한 자세한 내용은 참조 하세요. [여기](./iot-hub-devguide-messages-c2d.md#message-feedback)합니다. |

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
기본적으로 IoT Hub는 기본 제공 Event hub에서 수집 된 장치 원격 분석 메시지를 저장합니다. 서비스 클라이언트 AMQP 프로토콜을 사용 하 여 저장 된 이벤트를 수신 하 수 있습니다.

이 작업을 위해 서비스 클라이언트는 먼저 IoT Hub 끝점에 연결 하 여 기본 제공 Event Hubs로 리디렉션 주소를 수신 해야 합니다. 서비스 클라이언트는 다음 기본 제공 Event hub에 연결 하려면 제공된 된 주소를 사용 합니다.

각 단계에서 클라이언트는 다음 정보를 제공 해야 합니다.
* 올바른 서비스 자격 증명 (서비스 SAS 토큰)입니다.
* 소비자 그룹 파티션에 잘못 된 경로에서 메시지를 검색 하는 것입니다. 지정 된 소비자 그룹 및 파티션 ID에 대 한 경로 형식은 다음과 같습니다: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (기본 소비자 그룹은 `$Default`).
* 시작점을 파티션의 (시퀀스 번호, 오프셋 또는 큐에 대기 된 타임 스탬프 형식의 수 있음)를 지정 하는 선택적 필터링 조건자입니다.

사용 하 여 아래 코드 조각 [python에서 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python) 위의 단계를 보여줍니다.

```python
import json
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified using endpiont_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI
def set_endpoint_filter(uri, endpoint_filter=''):
  source_uri = uamqp.address.Source(uri)
  source_uri.set_filter(endpoint_filter)
  return source_uri

receive_client = uamqp.ReceiveClient(set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
  batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
  # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
  receive_client.close()

  sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(redirect.address, policy_name, access_key)
  receive_client = uamqp.ReceiveClient(set_endpoint_filter(redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
  print('*** received a message ***')
  print(''.join(msg.get_data()))
  print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
  print('\t: ' + str(msg.annotations['x-opt-offset']))
  print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

지정 된 장치 ID에 대 한 IoT Hub 장치 ID의 해시를 사용 하 여 해당 메시지를 저장할 파티션을 결정. 위의 코드 조각 단일에서 수신 이벤트를 보여 줍니다. 이러한 파티션 합니다. 그러나 일반적인 응용 프로그램을 자주 저장 된 모든 이벤트 허브 파티션의 이벤트를 검색 해야 합니다.


## <a name="device-client"></a>장치 클라이언트

### <a name="connection-and-authenticating-to-iot-hub-device-client"></a>연결 및 IoT Hub (장치 클라이언트)에 인증
AMQP를 사용 하 여 IoT Hub에 연결 하려면 장치를 사용할 수는 [클레임 기반 보안 (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) 하거나 [단순한 인증 및 보안 레이어 (SASL) 인증](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)합니다.

다음 정보는 장치 클라이언트에 대 한 필요 합니다.

| 정보 | Value | 
|-------------|--------------|
| IoT Hub 호스트 이름 | `<iot-hub-name>.azure-devices.net` |
| 액세스 키 | 장치와 연결 된 기본 또는 보조 키 |
| 공유 액세스 서명 | 다음 형식으로 단기 실행 SAS: `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}` (이 서명을 생성 하는 코드를 찾을 수 있습니다 [여기](./iot-hub-devguide-security.md#security-token-structure)).


사용 하 여 아래 코드 조각 [python에서 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python) 보낸 사람 링크를 통해 IoT hub에 연결 합니다.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(hostname=hostname, device_id=device_id), access_key, None)

operation = '<operation-link-name>' # e.g., '/devices/{device_id}/messages/devicebound'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

다음 링크 경로 장치 작업으로 지원 됩니다.

| 만든 사람 | 링크 형식 | 링크 경로 | 설명 |
|------------|-----------|-----------|-------------|
| 디바이스 | 받는 사람 링크 | `/devices/<deviceID>/messages/devicebound` | 장치를 대상으로 하는 C2D 메시지는 각 대상 장치에서이 링크를 수신 됩니다. |
| 디바이스 | 보낸 사람 링크 | `/devices/<deviceID>messages/events` | 장치에서 보낸 D2C 메시지는이 링크를 통해 전송 됩니다. |
| 디바이스 | 보낸 사람 링크 | `/messages/serviceBound/feedback` | C2D 메시지 피드백 장치에서이 링크를 통해 서비스에 전송 합니다. |


### <a name="receive-c2d-commands-device-client"></a>C2D 명령 (장치 클라이언트)를 수신 합니다.
장치에 전송 된 C2D 명령에 도착 `/devices/<deviceID>/messages/devicebound` 링크 합니다. 장치는 일괄 처리에서 이러한 메시지를 수신 하 고 필요에 따라 메시지의 데이터 페이로드는 메시지, 메시지 속성, 주석 또는 응용 프로그램 속성을 사용할 수 있습니다.

사용 하 여 아래 코드 조각 [python에서 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python) 장치 여 C2D 메시지를 수신 합니다.

```python
# ... 
# Create a receive client for the C2D receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
  batch = receive_client.receive_message_batch(max_batch_size=5)
  for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))

    # Property 'to' is set to: '/devices/device1/messages/devicebound',
    print('\tto:                     ' + str(msg.properties.to))

    # Property 'message_id' is set to value provided by the service
    print('\tmessage_id:             ' + str(msg.properties.message_id))

    # Other properties are present if they were provided by the service
    print('\tcreation_time:          ' + str(msg.properties.creation_time))
    print('\tcorrelation_id:         ' + str(msg.properties.correlation_id))
    print('\tcontent_type:           ' + str(msg.properties.content_type))
    print('\treply_to_group_id:      ' + str(msg.properties.reply_to_group_id))
    print('\tsubject:                ' + str(msg.properties.subject))
    print('\tuser_id:                ' + str(msg.properties.user_id))
    print('\tgroup_sequence:         ' + str(msg.properties.group_sequence))
    print('\tcontent_encoding:       ' + str(msg.properties.content_encoding))
    print('\treply_to:               ' + str(msg.properties.reply_to))
    print('\tabsolute_expiry_time:   ' + str(msg.properties.absolute_expiry_time))
    print('\tgroup_id:               ' + str(msg.properties.group_id))

    # Message sequence number in the built-in Event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>(장치 클라이언트) 원격 분석 메시지 보내기
원격 분석 메시지도 수 AMQP를 통해 장치에서 전송 합니다. 장치에서 응용 프로그램 속성의 사전을 선택적으로 제공할 수 또는 다양 한 메시지 속성 등 메시지 id입니다.

사용 하 여 아래 코드 조각 [python에서 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python) 장치의 D2C 메시지를 보내도록 합니다.


```python
# ... 
# Create a send client for the D2C send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>추가 참고 사항
* AMQP 연결은 네트워크 결함, 또는 (코드에서 생성) 인증 토큰의 만료로 인해 중단 될 수 있습니다. 서비스 클라이언트는 이러한 상황을 처리 하 고 연결 및 필요한 경우 링크를 다시 설정 해야 합니다. 인증 토큰 만료의 사례에 대 한 클라이언트 연결 삭제를 방지 하려면 해당 만료 전에 토큰을 갱신할 미리 수 있습니다.
* 경우에 따라 클라이언트를 올바르게 처리 링크 리디렉션 수 있어야 합니다. 이 작업을 처리 하는 방법에 AMQP 클라이언트 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

AMQP 프로토콜에 대 한 자세한 내용은 참조는 [AMQP v1.0 사양을](http://www.amqp.org/sites/amqp.org/files/amqp.pdf)합니다.

IoT Hub 메시징에 대 한 자세한 내용은 다음을 참조 하세요.

* [클라우드-장치 메시지](./iot-hub-devguide-messages-c2d.md)
* [추가 프로토콜 지원](iot-hub-protocol-gateway.md)
* [MQTT 프로토콜에 대 한 지원](./iot-hub-mqtt-support.md)

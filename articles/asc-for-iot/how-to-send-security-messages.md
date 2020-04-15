---
title: 장치 보안 메시지 보내기
description: IoT용 Azure 보안 센터를 사용하여 보안 메시지를 보내는 방법을 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 4877493982671b1b5db686715ef854f25c2966ea
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310989"
---
# <a name="send-security-messages-sdk"></a>보안 메시지 보내기 SDK

이 방법 가이드에서는 IoT 에이전트용 Azure 보안 센터를 사용하지 않고 장치 보안 메시지를 수집하고 전송하도록 선택할 때 IoT 서비스 기능을 위한 Azure 보안 센터에 대해 설명하고 이를 수행하는 방법을 설명합니다.

이 가이드에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> * Azure IoT C SDK를 사용하여 보안 메시지 보내기
> * Azure IoT C# SDK를 사용하여 보안 메시지 보내기
> * Azure IoT 파이썬 SDK를 사용하여 보안 메시지 보내기
> * Azure IoT Node.js SDK를 사용하여 보안 메시지 보내기
> * Azure IoT Java SDK를 사용하여 보안 메시지 보내기

## <a name="azure-security-center-for-iot-capabilities"></a>IoT 기능을 위한 Azure 보안 센터

IoT용 Azure 보안 센터는 전송된 데이터가 [IoT 스키마용 Azure 보안 센터를](https://aka.ms/iot-security-schemas) 준수하고 메시지가 보안 메시지로 설정된 경우 모든 종류의 보안 메시지 데이터를 처리하고 분석할 수 있습니다.

## <a name="security-message"></a>보안 메시지

IoT용 Azure 보안 센터는 다음 조건을 사용하여 보안 메시지를 정의합니다.

- Azure IoT SDK로 메시지를 보낸 경우
- 메시지가 [보안 메시지 스키마를](https://aka.ms/iot-security-schemas) 준수하는 경우
- 메시지를 보내기 전에 보안 메시지로 설정된 경우

각 보안 메시지에는 보낸 사람의 메타데이터(예: `AgentId`" 및 `AgentVersion` `MessageSchemaVersion` 보안 이벤트 목록)가 포함됩니다.
스키마는 이벤트 유형을 포함하여 보안 메시지의 유효하고 필요한 속성을 정의합니다.

> [!NOTE]
> 스키마를 준수하지 않은 상태로 보내는 메시지는 무시됩니다. 무시된 메시지는 현재 저장되지 않으므로 데이터 보내기를 시작하기 전에 스키마를 확인해야 합니다.

> [!NOTE]
> Azure IoT SDK를 사용하여 보안 메시지로 설정되지 않은 메시지는 IoT 파이프라인용 Azure 보안 센터로 라우팅되지 않습니다.

## <a name="valid-message-example"></a>유효한 메시지 예

아래 예제에서는 유효한 보안 메시지 개체를 보여 주실 수 있습니다. 이 예제에는 메시지 메타데이터와 하나의 `ProcessCreate` 보안 이벤트가 포함되어 있습니다.

보안 메시지로 설정되고 전송되면 이 메시지는 IoT용 Azure 보안 센터에서 처리됩니다.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>보안 메시지 보내기

[Azure IoT C 장치 SDK,](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) [Azure IoT C# 장치 SDK,](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) [Azure IoT Node.js SDK,](https://github.com/Azure/azure-iot-sdk-node)Azure [IoT 파이썬 SDK,](https://github.com/Azure/azure-iot-sdk-python)Azure [IoT Java Java SDK](https://github.com/Azure/azure-iot-sdk-java)를 사용하여 IoT 에이전트용 Azure 보안 센터를 사용하지 *않고* 보안 메시지를 보냅니다.

IoT용 Azure 보안 센터에서 처리하기 위해 장치에서 장치 데이터를 보내려면 다음 API 중 하나를 사용하여 IoT 처리 파이프라인을 위한 Azure 보안 센터로 올바른 라우팅을 위한 메시지를 표시합니다.

올바른 헤더로 표시된 경우에도 전송되는 모든 데이터는 [IoT 메시지 스키마에 대한 Azure 보안 센터를](https://aka.ms/iot-security-schemas)준수해야 합니다.

### <a name="send-security-message-api"></a>보안 메시지 보내기 API

**보안 메시지 보내기** API는 현재 C 및 C#, 파이썬, Node.js 및 Java에서 사용할 수 있습니다.

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>Node.js API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python API

파이썬 API를 사용하려면 패키지 [azure-iot 장치를](https://pypi.org/project/azure-iot-device/)설치해야합니다.

파이썬 API를 사용하는 경우 모듈을 통해 또는 고유 장치 또는 모듈 연결 문자열을 사용하여 장치를 통해 보안 메시지를 보낼 수 있습니다. 다음 파이썬 스크립트 예제를 장치에서 사용할 **때IoTHubDeviceClient**및 모듈을 사용하여 **IoTHubModuleClient**를 사용합니다.

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>다음 단계

- IoT 서비스를 위한 Azure 보안 센터 [개요](overview.md) 읽기
- IoT [아키텍처를](architecture.md) 위한 Azure 보안 센터에 대해 자세히 알아보기
- [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
- 자주 [묻는 질문 읽기](resources-frequently-asked-questions.md)
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [권장 사항](concept-recommendations.md) 이해
- [경고](concept-security-alerts.md)를 살펴봅니다.

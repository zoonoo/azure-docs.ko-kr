---
title: IoT에 대 한 Azure Security Center에 보안 메시지 보내기 | Microsoft Docs
description: IoT 용 Azure Security Center를 사용 하 여 보안 메시지를 보내는 방법에 대해 알아봅니다.
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
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: c780eea15b9f064d3279c75ac2f967e8b6099ecb
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596216"
---
# <a name="send-security-messages-sdk"></a>보안 메시지 보내기 SDK

이 방법 가이드에서는 IoT 에이전트 용 Azure Security Center를 사용 하지 않고 장치 보안 메시지를 수집 하 여 전송 하도록 선택할 때 IoT 서비스 기능에 대 한 Azure Security Center 설명 하 고,이 작업을 수행 하는 방법을 설명 합니다.  

이 가이드에서는 다음 작업 방법을 배웁니다. 
> [!div class="checklist"]
> * C#용 보안 메시지 보내기 API 사용
> * C용 보안 메시지 보내기 API 사용

## <a name="azure-security-center-for-iot-capabilities"></a>IoT 기능을 위한 Azure Security Center

IoT 용 Azure Security Center은 전송 된 데이터가 [IoT 스키마에 대 한 Azure Security Center](https://aka.ms/iot-security-schemas) 를 준수 하 고 메시지가 보안 메시지로 설정 되어 있는 한 모든 종류의 보안 메시지 데이터를 처리 하 고 분석할 수 있습니다.

## <a name="security-message"></a>보안 메시지

IoT에 대 한 Azure Security Center는 다음 조건을 사용 하 여 보안 메시지를 정의 합니다.
- Azure IoT C/C# SDK를 사용 하 여 메시지를 보낸 경우
- 메시지가 [보안 메시지 스키마](https://aka.ms/iot-security-schemas) 를 준수 하는 경우
- 메시지를 보내기 전에 보안 메시지로 설정 된 경우

각 보안 메시지에는 `AgentId` `MessageSchemaVersion` , `AgentVersion`및 보안 이벤트 목록과 같은 보낸 사람의 메타 데이터가 포함 됩니다.
스키마는 이벤트 유형을 포함 하 여 보안 메시지의 유효한 속성 및 필수 속성을 정의 합니다.

>[!Note]
> 스키마를 준수하지 않은 상태로 보내는 메시지는 무시됩니다. 무시된 메시지는 현재 저장되지 않으므로 데이터 보내기를 시작하기 전에 스키마를 확인해야 합니다. 

>[!Note]
> Azure IoT C/C# SDK를 사용 하 여 보안 메시지로 설정 되지 않은 보낸 메시지는 iot 파이프라인에 대 한 Azure Security Center 라우팅되지 않습니다.

## <a name="valid-message-example"></a>유효한 메시지 예

아래 예제에서는 유효한 보안 메시지 개체를 보여 줍니다. 예제에는 메시지 메타 데이터와 하나의 `ProcessCreate` 보안 이벤트가 포함 되어 있습니다.

보안 메시지로 설정 하 고 전송 되 면이 메시지는 IoT의 Azure Security Center에 의해 처리 됩니다.

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

[Azure C# iot 장치 Sdk](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) 또는 [azure iot C 장치 Sdk](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview)를 사용 하 여 iot 에이전트에 대 한 Azure Security Center를 사용 하지 않고 보안 메시지를 보냅니다.

IoT에 대 한 Azure Security Center에서 처리 하기 위해 장치에서 장치 데이터를 보내려면 다음 Api 중 하나를 사용 하 여 IoT 처리 파이프라인에 대 한 Azure Security Center에 올바른 라우팅에 대 한 메시지를 표시 합니다. 

올바른 헤더로 표시 된 경우에도 전송 되는 모든 데이터는 [IoT 메시지 스키마의 Azure Security Center](https://aka.ms/iot-security-schemas)준수 해야 합니다. 

### <a name="send-security-message-api"></a>보안 메시지 보내기 API

**보안 메시지 보내기** API는 현재 C 및 C#에서 사용할 수 있습니다.  

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

## <a name="next-steps"></a>다음 단계
- IoT 서비스에 대 한 Azure Security Center [개요](overview.md) 를 참조 하십시오.
- IoT [아키텍처](architecture.md) 에 대 한 Azure Security Center에 대해 자세히 알아보기
- [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
- [FAQ](resources-frequently-asked-questions.md)를 참조합니다.
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [추천 사항](concept-recommendations.md)을 살펴봅니다.
- [경고](concept-security-alerts.md)를 살펴봅니다.

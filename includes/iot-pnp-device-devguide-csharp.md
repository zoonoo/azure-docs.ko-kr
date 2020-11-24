---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 46cb129d18e082f836a688b95111c10c8e191b01
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511467"
---
## <a name="model-id-announcement"></a>모델 ID 알림

모델 ID를 알리기 위해 장치는 연결 정보에 해당 ID를 포함 해야 합니다.

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

연결을 `ClientOptions` `DeviceClient` 초기화 하는 데 사용 되는 모든 메서드에서 새 오버 로드를 사용할 수 있습니다.

> [!TIP]
> 모듈 및 IoT Edge의 경우 대신를 사용 `ModuleClient` `DeviceClient` 합니다.

## <a name="dps-payload"></a>DPS 페이로드

[DPS (장치 프로 비전 서비스)](../articles/iot-dps/about-iot-dps.md) 를 사용 하는 장치에는 `modelId` 다음 JSON 페이로드를 사용 하 여 프로 비전 프로세스 중에 사용할가 포함 될 수 있습니다.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>원격 분석, 속성 및 명령 구현

[IoT 플러그 앤 플레이 모델의 구성 요소 이해](../articles/iot-pnp/concepts-components.md)에서 설명한 대로 장치 빌더는 구성 요소를 사용 하 여 장치를 설명 하는지 결정 해야 합니다. 구성 요소를 사용 하는 경우 장치는이 섹션에 설명 된 규칙을 따라야 합니다.

### <a name="telemetry"></a>원격 분석

기본 구성 요소에는 특별 한 속성이 필요 하지 않습니다.

중첩 된 구성 요소를 사용 하는 경우 장치는 다음과 같은 구성 요소 이름을 가진 메시지 속성을 설정 해야 합니다.

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.ComponentName = componentName;
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await client.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>읽기 전용 속성

기본 구성 요소에서 속성을 보고 하는 경우 특별 한 구문이 필요 하지 않습니다.

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

장치 쌍은 다음에 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

중첩 된 구성 요소를 사용 하는 경우 구성 요소 이름 내에 속성을 만들어야 합니다.

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

장치 쌍은 다음에 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>쓰기 가능 속성

이러한 속성은 장치에서 설정 하거나 솔루션에서 업데이트할 수 있습니다. 솔루션이 속성을 업데이트 하는 경우 클라이언트는 또는의 콜백으로 알림을 받습니다 `DeviceClient` `ModuleClient` . IoT 플러그 앤 플레이 규칙을 따르려면 장치는 속성이 성공적으로 수신 되었음을 서비스에 알려야 합니다.

#### <a name="report-a-writable-property"></a>쓰기 가능한 속성 보고

장치에서 쓰기 가능한 속성을 보고 하는 경우 규칙에 정의 된 값을 포함 해야 합니다 `ack` .

기본 구성 요소에서 쓰기 가능한 속성을 보고 하려면 다음을 수행 합니다.

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

장치 쌍은 다음에 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

중첩 된 구성 요소에서 쓰기 가능한 속성을 보고 하려면 쌍에 표식을 포함 해야 합니다.

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

장치 쌍은 다음에 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Desired 속성 업데이트를 구독 합니다.

서비스는 연결 된 장치에 대 한 알림을 트리거하는 desired 속성을 업데이트할 수 있습니다. 이 알림에는 업데이트를 식별 하는 버전 번호를 포함 하 여 업데이트 된 desired 속성이 포함 됩니다. 장치는 보고 된 속성과 동일한 메시지를 사용 하 여 응답 해야 합니다 `ack` .

기본 구성 요소는 단일 속성을 확인 하 고 수신 된 버전으로 보고 된를 만듭니다 `ack` .

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

장치 쌍은 원하는 및 보고 된 섹션의 속성을 보여 줍니다.

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

중첩 된 구성 요소는 구성 요소 이름을 사용 하 여 래핑된 desired 속성을 받아 보고 된 속성을 다시 보고 해야 합니다 `ack` .

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

중첩 된 구성 요소에 대 한 장치 쌍은 다음과 같이 원하는 및 보고 된 섹션을 보여 줍니다.

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>명령

기본 구성 요소는 서비스에서 호출 된 명령 이름을 받습니다.

중첩 된 구성 요소는 구성 요소 이름 및 구분 기호를 접두사로 하는 명령 이름을 받습니다 `*` .

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>요청 및 응답 페이로드

명령은 형식을 사용 하 여 요청 및 응답 페이로드를 정의 합니다. 장치는 들어오는 입력 매개 변수를 deserialize 하 고 응답을 직렬화 해야 합니다. 다음 예제에서는 페이로드에 정의 된 복합 형식을 사용 하 여 명령을 구현 하는 방법을 보여 줍니다.

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

다음 코드 조각에서는 serialization 및 deserialization을 활성화 하는 데 사용 되는 형식을 포함 하 여 장치에서이 명령 정의를 구현 하는 방법을 보여 줍니다.

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> 요청 및 응답 이름이 네트워크를 통해 전송 되는 직렬화 된 페이로드에 없습니다.

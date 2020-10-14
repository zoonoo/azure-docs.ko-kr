---
title: 서비스 개발자 가이드-IoT 플러그 앤 플레이 | Microsoft Docs
description: 서비스 개발자를 위한 IoT 플러그 앤 플레이 설명
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 431ea692ac734a875e27cb8ed4b015155e0e9b91
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042425"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT 플러그 앤 플레이 서비스 개발자 가이드

IoT 플러그 앤 플레이를 사용 하 여 Azure IoT 응용 프로그램에 기능을 보급 하는 스마트 장치를 빌드할 수 있습니다. IoT 플러그 앤 플레이 장치는 고객이 IoT 플러그 앤 플레이 지원 응용 프로그램에 연결할 때 수동 구성이 필요 하지 않습니다.

IoT 플러그 앤 플레이를 사용 하면 IoT hub로 모델 ID를 발표 한 장치를 사용할 수 있습니다. 예를 들어 장치의 속성 및 명령에 직접 액세스할 수 있습니다.

Iot hub에 연결 된 IoT 플러그 앤 플레이 장치를 사용 하려면 iot 서비스 Sdk 또는 IoT Hub REST API 중 하나를 사용 합니다.

## <a name="service-sdks"></a>서비스 SDK

솔루션에서 Azure IoT 서비스 Sdk를 사용 하 여 장치 및 모듈과 상호 작용 합니다. 예를 들어, 서비스 Sdk를 사용 하 여 쌍 속성을 읽고 업데이트 하 고 명령을 호출할 수 있습니다. 지원 되는 언어에는 c #, Java, Node.js 및 Python이 포함 됩니다.

서비스 Sdk를 사용 하면 데스크톱 또는 웹 응용 프로그램과 같은 솔루션에서 장치 정보에 액세스할 수 있습니다. 서비스 Sdk에는 모델 ID를 검색 하는 데 사용할 수 있는 두 가지 네임 스페이스와 개체 모델이 포함 되어 있습니다.

- Iot Hub 서비스 클라이언트입니다. 이 서비스는 모델 ID를 장치 쌍 속성으로 노출 합니다.

- Digital Twins 서비스 클라이언트. 새 Digital Twins API는 구성 요소, 속성 및 명령과 같은 [DTDL (디지털 Twins 정의 언어)](concepts-digital-twin.md) 모델 구조에서 작동 합니다. 디지털 쌍 Api를 사용 하면 솔루션 빌더가 IoT 플러그 앤 플레이 솔루션을 쉽게 만들 수 있습니다.

| 플랫폼 | IoT Hub 서비스 클라이언트 | 디지털 쌍 서비스 클라이언트 |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [문서](/dotnet/api/microsoft.azure.devices) <br/> [샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [문서](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?preserve-view=true&view=azure-java-stable) <br/> [샘플](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [문서](/javascript/api/azure-iothub/twin?preserve-view=true&view=azure-node-latest) <br/> [샘플](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [문서](/javascript/api/azure-iot-digitaltwins-service/?preserve-view=true&view=azure-node-latest) <br/> [샘플](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [문서](/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?preserve-view=true&view=azure-python) <br/> [샘플](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [문서](/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?preserve-view=true&view=azure-python) <br/> [샘플](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

## <a name="iot-hub-service-client-examples"></a>IoT Hub 서비스 클라이언트 예제

이 섹션에서는 IoT Hub 서비스 클라이언트와 **RegistryManager** 및 **ServiceClient** 클래스를 사용 하는 c # 예제를 보여 줍니다. 장치 쌍을 사용 하 여 장치 상태와 상호 작용 하려면 **RegistryManager** 클래스를 사용 합니다. **RegistryManager** 클래스를 사용 하 여 IoT Hub에서 [장치 등록을 쿼리할](..\iot-hub\iot-hub-devguide-query-language.md) 수도 있습니다. **ServiceClient** 클래스를 사용 하 여 장치에서 명령을 호출 합니다. 장치의 [Dtdl](concepts-digital-twin.md) 모델은 장치가 구현 하는 속성과 명령을 정의 합니다. 코드 조각에서 변수는 iot `deviceTwinId` hub에 등록 된 iot 플러그 앤 플레이 장치의 장치 ID를 포함 합니다.

### <a name="get-the-device-twin-and-model-id"></a>장치 쌍 및 모델 ID 가져오기

Iot hub에 연결 된 IoT 플러그 앤 플레이 장치의 장치 쌍 및 모델 ID를 가져오려면 다음을 수행 합니다.

```csharp
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(parameters.HubConnectionString);

Twin twin = await registryManager.GetTwinAsync(deviceTwinId);
Console.WriteLine($"Device twin: \n{JsonConvert.SerializeObject(twin, Formatting.Indented)}");
Console.WriteLine($"Model ID: {twin.ModelId}.");
```

### <a name="update-device-twin"></a>디바이스 쌍 업데이트

다음 코드 조각에서는 장치에서 속성을 업데이트 하는 방법을 보여 줍니다 `targetTemperature` . 이 샘플에서는 업데이트 하기 전에 쌍을 가져오는 방법을 보여 줍니다 `ETag` . 속성은 장치의 기본 구성 요소에 정의 되어 있습니다.

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired["targetTemperature"] = desiredTargetTemperature;

Console.WriteLine($"Update the targetTemperature property to {desiredTargetTemperature}.");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);
```

다음 코드 조각에서는 구성 요소의 속성을 업데이트 하는 방법을 보여 줍니다 `targetTemperature` . 이 샘플에서는 업데이트 하기 전에 쌍을 가져오는 방법을 보여 줍니다 `ETag` . 속성은 **Thermostat1** 인터페이스에서 정의 됩니다.

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

var twinPatch = CreatePropertyPatch("targetTemperature", desiredTargetTemperature, "thermostat1");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);

// ...

private static Twin CreatePropertyPatch(string propertyName, object propertyValue, string componentName)
{
    var twinPatch = new Twin();
    twinPatch.Properties.Desired[componentName] = new
    {
        __t = "c"
    };
    twinPatch.Properties.Desired[componentName][propertyName] = JsonConvert.SerializeObject(propertyValue);
    return twinPatch;
}
```

구성 요소의 속성에 대 한 속성 패치는 다음 예제와 같습니다.

```json
{
"sampleComponentName":
  {
    "__t": "c",
    "samplePropertyName": 20
  }
}
```

### <a name="call-command"></a>Call 명령

다음 코드 조각에서는 `getMaxMinReport` 기본 구성 요소에 정의 된 명령을 호출 하는 방법을 보여 줍니다.

```csharp
ServiceClient serviceClient = ServiceClient.CreateFromConnectionString(parameters.HubConnectionString);

var commandInvocation = new CloudToDeviceMethod("getMaxMinReport") { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
  CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);

  Console.WriteLine($"Command getMaxMinReport was invoked." +
      $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine($"Unable to execute command getMaxMinReport on {deviceTwinId}.";
}
```

다음 코드 조각에서는 `getMaxMinReport` 구성 요소에서 명령을 호출 하는 방법을 보여 줍니다. 명령은 **Thermostat1** 인터페이스에 정의 되어 있습니다.

```csharp
// Create command name to invoke for component. The command is formatted as <component name>*<command name>
string commandToInvoke = "thermostat1*getMaxMinReport";
var commandInvocation = new CloudToDeviceMethod(commandToInvoke) { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
    CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);
    Console.WriteLine($"Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
}
```

## <a name="digital-twin-examples"></a>디지털 쌍 예

디지털 쌍을 사용 하 여 장치 상태와 상호 작용 하려면 **DigitalTwinClient** 클래스를 사용 합니다. 장치의 [Dtdl](concepts-digital-twin.md) 모델은 장치가 구현 하는 속성과 명령을 정의 합니다.

이 섹션에서는 디지털 Twins API를 사용 하는 c # 예제를 보여 줍니다. 다음 코드 조각은 자동 온도 조절기 및 온도 컨트롤러 장치의 디지털 쌍을 나타내는 다음 클래스를 사용 합니다.

```csharp
using Microsoft.Azure.Devices.Serialization;
using Newtonsoft.Json;
using System;

namespace Microsoft.Azure.Devices.Samples
{
  internal class ThermostatTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new ThermostatMetadata Metadata { get; set; }

    [JsonProperty("maxTempSinceLastReboot")]
    public double? MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public double? TargetTemperature { get; set; }
  }

  internal class ThermostatMetadata : DigitalTwinMetadata
  {
    [JsonProperty("maxTempSinceLastReboot")]
    public ReportedPropertyMetadata MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public WritableProperty TargetTemperature { get; set; }
  }

  internal class ReportedPropertyMetadata
  {
    [JsonProperty("lastUpdateTime")]
    public DateTimeOffset LastUpdateTime { get; set; }
  }

  internal class TemperatureControllerTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new TemperatureControllerMetadata Metadata { get; set; }

    [JsonProperty("serialNumber")]
    public string SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public ThermostatTwin Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public ThermostatTwin Thermostat2 { get; set; }
  }

  internal class TemperatureControllerMetadata : DigitalTwinMetadata
  {
    [JsonProperty("serialNumber")]
    public ReportedPropertyMetadata SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public WritableProperty Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public WritableProperty Thermostat2 { get; set; }
  }
}
```

변수는 iot `digitalTwinId` hub에 등록 된 iot 플러그 앤 플레이 장치의 장치 ID를 포함 합니다.

### <a name="get-the-digital-twin-and-model-id"></a>디지털 쌍 및 모델 ID 가져오기

Iot hub에 연결 된 IoT 플러그 앤 플레이 장치의 디지털 쌍 및 모델 ID를 가져오려면 다음을 수행 합니다.

```csharp
DigitalTwinClient digitalTwinClient = DigitalTwinClient.CreateFromConnectionString(parameters.HubConnectionString);
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
ThermostatTwin thermostatTwin = getDigitalTwinResponse.Body;
Console.WriteLine($"Model ID: {thermostatTwin.Metadata.ModelId}.");
Console.WriteLine($"Digital Twin: \n{JsonConvert.SerializeObject(thermostatTwin, Formatting.Indented)}");
```

### <a name="update-digital-twin"></a>디지털 쌍 업데이트

다음 코드 조각에서는 장치에서 속성을 업데이트 하는 방법을 보여 줍니다 `targetTemperature` . 속성은 장치의 기본 구성 요소에 정의 되어 있습니다.

```csharp
var updateOperation = new UpdateOperationsUtility();

int desiredTargetTemperature = 60;

// Get the current value of the targetTemperature property
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
double? currentTargetTemperature = getDigitalTwinResponse.Body.TargetTemperature;

// Has the targetTemperature property previously been set?
if (currentTargetTemperature != null)
{
  // Update the existing property
  // Prepend the property path with a '/'
  updateOperation.AppendReplacePropertyOp($"/targetTemperature", desiredTargetTemperature);
}
else
{
  // Add a new property
  // Prepend the property path with a '/'
  updateOperation.AppendAddPropertyOp($"/targetTemperature", desiredTargetTemperature);
}

// Update the targetTemperature property on the digital twin
HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

다음 코드 조각에서는 구성 요소의 속성을 업데이트 하는 방법을 보여 줍니다 `targetTemperature` . 속성은 **Thermostat1** 구성 요소에 정의 되어 있습니다.

```csharp
int desiredTargetTemperature = 60;

var updateOperation = new UpdateOperationsUtility();

// Look at when the property was updated and what was it set to.
HttpOperationResponse<TemperatureControllerTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
  .GetDigitalTwinAsync<TemperatureControllerTwin>(digitalTwinId);

ThermostatTwin thermostat1 = getDigitalTwinResponse.Body.Thermostat1;

if (thermostat1 != null)
{
  // Thermostat1 is present in the TemperatureController twin. You can add/replace the component-level property "targetTemperature"
  double? currentComponentTargetTemperature = getDigitalTwinResponse.Body.Thermostat1.TargetTemperature;
  if (currentComponentTargetTemperature != null)
  {
      DateTimeOffset targetTemperatureDesiredLastUpdateTime = getDigitalTwinResponse.Body.Thermostat1.Metadata.TargetTemperature.LastUpdateTime;

      // The property path to be replaced should be prepended with a '/'
      updateOperation.AppendReplacePropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
  else
  {
      // The property path to be added should be prepended with a '/'
      updateOperation.AppendAddPropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
}
else
{
    // Thermostat1 is not present in the TemperatureController twin. Add the component.
    var componentProperty = new Dictionary<string, object> { { "targetTemperature", desiredTargetTemperature }, { "$metadata", new object() } };

    // The property path to be replaced should be prepended with a '/'
    updateOperation.AppendAddComponentOp("/thermostat1", componentProperty);
}

HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

### <a name="call-command"></a>Call 명령

다음 코드 조각에서는 `getMaxMinReport` 기본 구성 요소에 정의 된 명령을 호출 하는 방법을 보여 줍니다.

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
  HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
    .InvokeCommandAsync(digitalTwinId, "getMaxMinReport", JsonConvert.SerializeObject(since));

  Console.WriteLine($"Command getMaxMinReport was invoked. \nDevice returned status: {invokeCommandResponse.Body.Status}." +
    $"\nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
  if (e.Response.StatusCode == HttpStatusCode.NotFound)
  {
    Console.WriteLine($"Unable to execute command getMaxMinReport on {digitalTwinId}.");
  }
}
```

다음 코드 조각에서는 `getMaxMinReport` 구성 요소에서 명령을 호출 하는 방법을 보여 줍니다. 명령은 **Thermostat1** 인터페이스에 정의 되어 있습니다.

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
    HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
        .InvokeComponentCommandAsync(digitalTwinId, "thermostat1", "getMaxMinReport", JsonConvert.SerializeObject(since));

    Console.WriteLine("Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {invokeCommandResponse.Body.Status}. \nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
    if (e.Response.StatusCode == HttpStatusCode.NotFound)
    {
        Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
    }
}
```

## <a name="rest-api"></a>REST API

다음 예에서는 IoT Hub REST API를 사용 하 여 연결 된 IoT 플러그 앤 플레이 장치와 상호 작용 합니다. API의 현재 버전은 `2020-09-30` 입니다. `?api-version=2020-09-30`REST PI 호출에 추가 합니다.

> [!NOTE]
> 모듈 쌍은 현재 API에서 지원 되지 않습니다 `digitalTwins` .

자동 온도 조절기 장치를 호출 하는 경우 `t-123` REST API get 호출을 사용 하 여 장치에서 구현 된 모든 인터페이스의 모든 속성을 가져옵니다.

```REST
GET /digitalTwins/t-123
```

이 호출에는 `$metadata.$model` 장치에서 발표 한 모델 ID를 포함 하는 Json 속성이 포함 됩니다.

모든 인터페이스의 모든 속성은 REST API 템플릿을 사용 하 여 액세스할 수 있습니다 `GET /DigitalTwin/{device-id}` `{device-id}` . 여기서은 장치에 대 한 식별자입니다.

```REST
GET /digitalTwins/{device-id}
```

IoT 플러그 앤 플레이 장치 명령을 직접 호출할 수 있습니다. `Thermostat`장치의 구성 요소에 명령이 있는 경우 `t-123` `restart` REST API POST 호출을 사용 하 여 호출할 수 있습니다.

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

보다 일반적으로이 REST API 템플릿을 통해 명령을 호출할 수 있습니다.

- `device-id`: 장치의 식별자입니다.
- `component-name`: 장치 기능 모델의 implements 섹션에 있는 인터페이스의 이름입니다.
- `command-name`: 명령의 이름입니다.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="read-device-telemetry"></a>장치 원격 분석 읽기

IoT 플러그 앤 플레이 장치는 DTDL 모델에 정의 된 원격 분석을 IoT Hub 보냅니다. 기본적으로 IoT Hub는 원격 분석을 사용할 수 있는 Event Hubs 끝점으로 라우팅합니다. 자세한 내용은 [IoT Hub 메시지 라우팅을 사용 하 여 다른 끝점으로 장치-클라우드 메시지 보내기를](../iot-hub/iot-hub-devguide-messages-d2c.md)참조 하세요.

다음 코드 조각에서는 기본 Event Hubs 끝점에서 원격 분석을 읽는 방법을 보여 줍니다. 이 코드 조각의 코드는 IoT Hub 빠른 시작에서 [장치에서 IoT Hub로 원격 분석을 보내고 백 엔드 응용 프로그램을 사용](../iot-hub/quickstart-send-telemetry-dotnet.md)하 여 읽습니다.

```csharp
await using EventHubConsumerClient consumer = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, connectionString, EventHubName);

Console.WriteLine("Listening for messages on all partitions");

try
{
    await foreach (PartitionEvent partitionEvent in consumer.ReadEventsAsync(cancellationToken))
    {
        Console.WriteLine("Message received on partition {0}:", partitionEvent.Partition.PartitionId);

        string data = Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray());
        Console.WriteLine("\t{0}:", data);

        Console.WriteLine("Application properties (set by device):");
        foreach (var prop in partitionEvent.Data.Properties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }

        Console.WriteLine("System properties (set by IoT Hub):");
        foreach (var prop in partitionEvent.Data.SystemProperties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }
    }
}
catch (TaskCanceledException)
{
    // This is expected when the token is signaled; it should not be considered an
    // error in this scenario.
}
```

이전 코드의 다음 출력은 기본 구성 요소만 있는 구성 요소가 없는 **자동 온도 조절기** IoT 플러그 앤 플레이 장치에서 보낸 온도 원격 분석을 보여 줍니다. `dt-dataschema`System 속성은 모델 ID를 표시 합니다.

```cmd/sh
Message received on partition 1:
        { "temperature": 25.5 }:
Application properties (set by device):
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:30:58
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:Thermostat;1
        content-type: application/json
        content-encoding: utf-8
```

이전 코드의 다음 출력에서는 다중 구성 요소 **TemperatureController** IoT 플러그 앤 플레이 장치에서 보낸 온도 원격 분석을 보여 줍니다. `dt-subject`System 속성은 원격 분석을 보낸 구성 요소의 이름을 표시 합니다. 이 예에서 두 구성 요소는 `thermostat1` `thermostat2` dtdl 모델에 정의 된 및입니다. `dt-dataschema`System 속성은 모델 ID를 표시 합니다.

```cmd/sh
Message received on partition 1:
        {"temperature":11.1}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat1
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
Message received on partition 1:
        {"temperature":41.2}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat2
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-device-twin-change-notifications"></a>장치 쌍 변경 알림 읽기

지원 되는 끝점으로 라우팅하기 위해 장치 쌍 변경 알림을 생성 하도록 IoT Hub를 구성할 수 있습니다. 자세한 내용은 [IoT Hub 메시지 라우팅을 사용 하 여 비 원격 분석 이벤트 > 다른 끝점으로 장치-클라우드 메시지 보내기를](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)참조 하세요.

이전 c # 코드 조각에 표시 된 코드는 IoT Hub 구성 요소 자동 온도 조절기 장치에 대 한 장치 쌍 변경 알림을 생성할 때 다음 출력을 생성 합니다. 응용 프로그램 속성 `iothub-message-schema` 및 `opType` 변경 알림 유형에 대 한 정보를 제공 합니다.

```cmd/sh
Message received on partition 1:
        {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":9.6,"$metadata":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z"}},"$version":2}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:17:41.7408552+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:17:41
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 61394e8ba7d
        content-type: application/json
        content-encoding: utf-8
```

이전 c # 코드 조각에 표시 된 코드는 IoT Hub 구성 요소를 사용 하 여 장치에 대 한 장치 쌍 변경 알림을 생성할 때 다음과 같은 출력을 생성 합니다. 이 예제에서는 자동 온도 조절기 구성 요소가 있는 온도 센서 장치가 알림을 생성할 때 출력을 보여 줍니다. 응용 프로그램 속성 `iothub-message-schema` 및 `opType` 변경 알림 유형에 대 한 정보를 제공 합니다.

```cmd/sh
Message received on partition 1:
        {"version":5,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":9.6},"$metadata":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","thermostat1":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","__t":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"}}},"$version":4}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:27:59.5159720+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:27:59
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 615051f364e
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-digital-twin-change-notifications"></a>디지털 쌍 변경 알림 읽기

지원 되는 끝점으로 라우팅하도록 디지털 쌍 변경 알림을 생성 하도록 IoT Hub를 구성할 수 있습니다. 자세한 내용은 [IoT Hub 메시지 라우팅을 사용 하 여 비 원격 분석 이벤트 > 다른 끝점으로 장치-클라우드 메시지 보내기를](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)참조 하세요.

이전 c # 코드 조각에 표시 된 코드는 IoT Hub 구성 요소 자동 온도 조절기 장치에 대 한 디지털 쌍 변경 알림을 생성할 때 다음 출력을 생성 합니다. 응용 프로그램 속성 `iothub-message-schema` 및 `opType` 변경 알림 유형에 대 한 정보를 제공 합니다.

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-06T10:39:16.0209836Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":34.9}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:39:16.0209836+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:39:16
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 6169857bf8c
        content-type: application/json-patch+json
        content-encoding: utf-8
```

이전 c # 코드 조각에 표시 된 코드는 IoT Hub 구성 요소를 사용 하 여 장치에 대 한 디지털 쌍 변경 알림을 생성할 때 다음과 같은 출력을 생성 합니다. 이 예제에서는 자동 온도 조절기 구성 요소가 있는 온도 센서 장치가 알림을 생성할 때 출력을 보여 줍니다. 응용 프로그램 속성 `iothub-message-schema` 및 `opType` 변경 알림 유형에 대 한 정보를 제공 합니다.

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-06T10:41:44.8312666Z"}},"maxTempSinceLastReboot":29.1}}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:41:44.8312666+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:41:44
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 616f108f0e3
        content-type: application/json-patch+json
        content-encoding: utf-8
```

## <a name="next-steps"></a>다음 단계

이제 장치 모델링에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [DTDL(디지털 쌍 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 디바이스 SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [모델 구성 요소](./concepts-components.md)
- [DTDL authoring tools 설치 및 사용](howto-use-dtdl-authoring-tools.md)
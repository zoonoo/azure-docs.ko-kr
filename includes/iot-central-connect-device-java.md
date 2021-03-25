---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 75533a49c72f13bb9e1e62c160a63ef0606bee23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244506"
---
## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음이 필요합니다.

* **사용자 지정 애플리케이션** 템플릿을 사용하여 만든 Azure IoT Central 애플리케이션. 자세한 내용은 [애플리케이션 만들기 빠른 시작](../articles/iot-central/core/quick-deploy-iot-central.md)을 참조하세요. 애플리케이션은 2020년 7월 14일 이후에 생성되어야 합니다.
* Java SE Development Kit 8이 포함된 개발 머신. [Azure 및 Azure Stack에 대한 Java 장기 지원](/java/azure/jdk/)의 **장기 지원** 에서 **Java 8** 을 선택합니다.
* [Apache Maven 3](https://maven.apache.org/download.cgi).
* 샘플 코드가 포함된 [Java용 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-java) GitHub 리포지토리의 로컬 복사본입니다. 다음 링크를 사용하여 리포지토리의 복사본을 다운로드합니다. [ZIP 다운로드](https://github.com/Azure/azure-iot-sdk-java/archive/master.zip). 그런 다음, 로컬 머신의 적절한 위치에 파일의 압축을 풉니다.

## <a name="review-the-code"></a>코드 검토

이전에 다운로드한 Java용 Microsoft Azure IoT SDK 복사본의 텍스트 편집기에서 *azure-iot-sdk-java/device/iot-device-samples/pnp-device-sample/thermostat-device-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/device/Thermostat.java* 파일을 엽니다.

샘플을 실행하여 IoT Central에 연결하면 DPS(Device Provisioning Service)를 사용하여 디바이스를 등록하고 연결 문자열을 생성합니다. 이 샘플은 명령줄 환경에서 필요한 DPS 연결 정보를 검색합니다.

`main` 메서드는 다음 작업을 수행합니다.

* `initializeAndProvisionDevice`를 호출하여 `dtmi:com:example:Thermostat;1` 모델 ID를 설정하고, DPS를 사용하여 디바이스를 프로비저닝 및 등록하고, **DeviceClient** 인스턴스를 만들고, IoT Central 애플리케이션에 연결합니다. IoT Central은 모델 ID를 사용하여 이 디바이스에 대한 디바이스 템플릿을 식별하거나 생성합니다. 자세한 내용은 [디바이스 템플릿과 디바이스 연결](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)을 참조하세요.
* `getMaxMinReport` 명령에 대한 명령 처리기를 만듭니다.
* 쓰기 가능한 `targetTemperature` 속성에 대한 속성 업데이트 처리기를 만듭니다.
* 스레드를 시작하여 온도 원격 분석을 보내고 5초마다 `maxTempSinceLastReboot` 속성을 업데이트합니다.

```java
async function main() {

public static void main(String[] args) throws URISyntaxException, IOException, ProvisioningDeviceClientException, InterruptedException {

    // ...

    switch (deviceSecurityType.toLowerCase())
    {
        case "dps":
        {
            if (validateArgsForDpsFlow())
            {
                initializeAndProvisionDevice();
                break;
            }
            throw new IllegalArgumentException("Required environment variables are not set for DPS flow, please recheck your environment.");
        }
        case "connectionstring":
        {
            // ...
        }
        default:
        {
            // ...
        }
    }

    deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new TargetTemperatureUpdateCallback(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback =
            Collections.singletonMap(
                    new Property("targetTemperature", null),
                    new Pair<>(new TargetTemperatureUpdateCallback(), null));
    deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);

    String methodName = "getMaxMinReport";
    deviceClient.subscribeToDeviceMethod(new GetMaxMinReportMethodCallback(), methodName, new MethodIotHubEventCallback(), methodName);

    new Thread(new Runnable() {
        @SneakyThrows({InterruptedException.class, IOException.class})
        @Override
        public void run() {
            while (true) {
                if (temperatureReset) {
                    // Generate a random value between 5.0°C and 45.0°C for the current temperature reading.
                    temperature = BigDecimal.valueOf(random.nextDouble() * 40 + 5).setScale(1, RoundingMode.HALF_UP).doubleValue();
                    temperatureReset = false;
                }

                sendTemperatureReading();
                Thread.sleep(5 * 1000);
            }
        }
    }).start();
}
```

`initializeAndProvisionDevice` 메서드는 디바이스에서 DPS를 사용하여 IoT Central에 등록하고 연결하는 방법을 보여줍니다. 페이로드에는 IoT Central이 [디바이스를 디바이스 템플릿과 연결](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)하는 데 사용하는 모델 ID가 포함됩니다.

```java
private static void initializeAndProvisionDevice() throws ProvisioningDeviceClientException, IOException, URISyntaxException, InterruptedException {
    SecurityProviderSymmetricKey securityClientSymmetricKey = new SecurityProviderSymmetricKey(deviceSymmetricKey.getBytes(), registrationId);
    ProvisioningDeviceClient provisioningDeviceClient = null;
    ProvisioningStatus provisioningStatus = new ProvisioningStatus();

    provisioningDeviceClient = ProvisioningDeviceClient.create(globalEndpoint, scopeId, provisioningProtocol, securityClientSymmetricKey);

    AdditionalData additionalData = new AdditionalData();
    additionalData.setProvisioningPayload(String.format("{\"modelId\": \"%s\"}", MODEL_ID));

    provisioningDeviceClient.registerDevice(new ProvisioningDeviceClientRegistrationCallbackImpl(), provisioningStatus, additionalData);

    while (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() != ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED)
    {
        if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ERROR ||
                provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_DISABLED ||
                provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_FAILED)
        {
            provisioningStatus.exception.printStackTrace();
            System.out.println("Registration error, bailing out");
            break;
        }
        System.out.println("Waiting for Provisioning Service to register");
        Thread.sleep(MAX_TIME_TO_WAIT_FOR_REGISTRATION);
    }

    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);

    if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED) {
        // ...

        String iotHubUri = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri();
        String deviceId = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId();

        deviceClient = DeviceClient.createFromSecurityProvider(iotHubUri, deviceId, securityClientSymmetricKey, IotHubClientProtocol.MQTT, options);
        deviceClient.open();
    }
}
```

`sendTemperatureTelemetry` 메서드는 디바이스에서 IoT Central에 온도 원격 분석을 보내는 방법을 보여줍니다.

```java
private static void sendTemperatureTelemetry() {
    String telemetryName = "temperature";
    String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

    Message message = new Message(telemetryPayload);
    message.setContentEncoding(StandardCharsets.UTF_8.name());
    message.setContentTypeFinal("application/json");

    deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);

    temperatureReadings.put(new Date(), temperature);
}
```

`updateMaxTemperatureSinceLastReboot` 메서드는 IoT Central에 `maxTempSinceLastReboot` 속성 업데이트를 보냅니다.

```java
private static void updateMaxTemperatureSinceLastReboot() throws IOException {
    String propertyName = "maxTempSinceLastReboot";
    Property reportedProperty = new Property(propertyName, maxTemperature);

    deviceClient.sendReportedProperties(Collections.singleton(reportedProperty));
}
```

`TargetTemperatureUpdateCallback` 클래스에는 IoT Central에서 쓰기 가능한 속성 업데이트를 처리하는 `TwinPropertyCallBack` 메서드가 포함되어 있습니다.

```java
String propertyName = "targetTemperature";

public void TwinPropertyCallBack(Property property, Object context) {
    if (property.getKey().equalsIgnoreCase(propertyName)) {
        double targetTemperature = ((Number)property.getValue()).doubleValue();

        EmbeddedPropertyUpdate pendingUpdate = new EmbeddedPropertyUpdate(targetTemperature, StatusCode.IN_PROGRESS.value, property.getVersion(), null);
        Property reportedPropertyPending = new Property(propertyName, pendingUpdate);
        try {
            deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyPending));
        } catch (IOException e) {
            throw new RuntimeException("IOException when sending reported property update: ", e);
        }

        // Update temperature in 2 steps
        double step = (targetTemperature - temperature) / 2;
        for (int i = 1; i <=2; i++) {
            temperature = BigDecimal.valueOf(temperature + step).setScale(1, RoundingMode.HALF_UP).doubleValue();
            Thread.sleep(5 * 1000);
        }

        EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(temperature, StatusCode.COMPLETED.value, property.getVersion(), "Successfully updated target temperature");
        Property reportedPropertyCompleted = new Property(propertyName, completedUpdate);
        deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
    } else {
        log.debug("Property: Received an unrecognized property update from service.");
    }
}
```

`GetMaxMinReportMethodCallback` 클래스에는 IoT Central에서 호출된 명령을 처리하는 `call` 메서드가 포함되어 있습니다.

```java
String commandName = "getMaxMinReport";

public DeviceMethodData call(String methodName, Object methodData, Object context) {
    if (methodName.equalsIgnoreCase(commandName)) {

        String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);
        Date since = getCommandRequestValue(jsonRequest, Date.class);

        double runningTotal = 0;
        Map<Date, Double> filteredReadings = new HashMap<>();
        for (Map.Entry<Date, Double> entry : temperatureReadings.entrySet()) {
            if (entry.getKey().after(since)) {
                filteredReadings.put(entry.getKey(), entry.getValue());
                runningTotal += entry.getValue();
            }
        }

        if (filteredReadings.size() > 1) {
            SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'");
            double maxTemp = Collections.max(filteredReadings.values());
            double minTemp = Collections.min(filteredReadings.values());
            double avgTemp = runningTotal / filteredReadings.size();
            String startTime = sdf.format(Collections.min(filteredReadings.keySet()));
            String endTime = sdf.format(Collections.max(filteredReadings.keySet()));

            String responsePayload = String.format(
                    "{\"maxTemp\": %.1f, \"minTemp\": %.1f, \"avgTemp\": %.1f, \"startTime\": \"%s\", \"endTime\": \"%s\"}",
                    maxTemp,
                    minTemp,
                    avgTemp,
                    startTime,
                    endTime);

            return new DeviceMethodData(StatusCode.COMPLETED.value, responsePayload);
        }

        return new DeviceMethodData(StatusCode.NOT_FOUND.value, null);
    }

    log.error("Command: Unknown command {} invoked from service.", methodName);
    return new DeviceMethodData(StatusCode.NOT_FOUND.value, null);
}
```

## <a name="get-connection-information"></a>연결 정보 가져오기

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

Windows에서 다운로드한 Java SDK 리포지토리의 루트 폴더로 이동합니다.

다음 명령을 실행하여 샘플 애플리케이션을 빌드합니다.

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-code"></a>코드 실행

샘플 애플리케이션을 실행하려면 명령줄 환경을 열고 *Thermostat.java* 샘플 파일이 포함된 *azure-iot-sdk-java/device/iot-device-samples/pnp-device-sample/thermostat-device-sample* 폴더로 이동합니다.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

샘플을 실행합니다.

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

다음 출력은 IoT Central에 등록하고 연결하는 디바이스를 보여줍니다. 샘플은 원격 분석 보내기를 시작합니다.

```output
Waiting for Provisioning Service to register
Waiting for Provisioning Service to register
IotHUb Uri : iotc-...azure-devices.net
Device ID : sample-device-01
2020-11-05 14:38:27.218 DEBUG Thermostat:208 - Opening the device client.
2020-11-05 14:38:27.253 DEBUG MqttIotHubConnection:122 - Opening MQTT connection...
2020-11-05 14:38:27.405 DEBUG Mqtt:117 - Sending MQTT CONNECT packet...
2020-11-05 14:38:28.782 DEBUG Mqtt:121 - Sent MQTT CONNECT packet was acknowledged
2020-11-05 14:38:28.786 DEBUG Mqtt:291 - Sending MQTT SUBSCRIBE packet for topic devices/sample-device-01/messages/devicebound/#
2020-11-05 14:38:28.965 DEBUG Mqtt:297 - Sent MQTT SUBSCRIBE packet for topic devices/sample-device-01/messages/devicebound/# was acknowledged
2020-11-05 14:38:28.967 DEBUG MqttIotHubConnection:205 - MQTT connection opened successfully
2020-11-05 14:38:28.968 INFO  IotHubTransport:270 - The connection to the IoT Hub has been established
2020-11-05 14:38:28.970 INFO  IotHubTransport:1133 - Updating transport status to new status CONNECTED with reason CONNECTION_OK
2020-11-05 14:38:28.972 DEBUG IotHubTransport:1143 - Invoking connection status callbacks with new status details
2020-11-05 14:38:28.976 INFO  IotHubTransport:327 - Client connection opened successfully
2020-11-05 14:38:28.978 INFO  DeviceClient:398 - Device client opened successfully
2020-11-05 14:38:28.979 DEBUG Thermostat:142 - Start twin and set handler to receive "targetTemperature" updates.
2020-11-05 14:38:28.993 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [85f8f25b-924e-47a1-b7f0-e7bd227cbb9f] Message Id [df65a02b-6d75-40fe-8636-a571ad3ae970] Device Operation Type [DEVICE_OPERATION_TWIN_SUBSCRIBE_DESIRED_PROPERTIES_REQUEST] )
2020-11-05 14:38:28.994 INFO  IotHubTransport:1040 - Sending message ( Message details: Correlation Id [85f8f25b-924e-47a1-b7f0-e7bd227cbb9f] Message Id [df65a02b-6d75-40fe-8636-a571ad3ae970] Device Operation Type [DEVICE_OPERATION_TWIN_SUBSCRIBE_DESIRED_PROPERTIES_REQUEST] )
2020-11-05 14:38:29.000 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [d3692283-63d6-446a-aeab-5a4e4aa9e948] Message Id [d53139e3-acb4-458b-8c03-119d8fc04d5a] Request Id [0] Device Operation Type [DEVICE_OPERATION_TWIN_GET_REQUEST] )
2020-11-05 14:38:29.018 DEBUG Mqtt:291 - Sending MQTT SUBSCRIBE packet for topic $iothub/twin/res/#
2020-11-05 14:38:29.080 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [f8c2180d-7289-4bf8-afa5-0ad6565a27eb] Message Id [c553e8bd-3c7e-4830-a3e7-12964cf3d936] Device Operation Type [DEVICE_OPERATION_TWIN_SUBSCRIBE_DESIRED_PROPERTIES_REQUEST] )
2020-11-05 14:38:29.103 DEBUG Thermostat:150 - Set handler to receive "getMaxMinReport" command.
2020-11-05 14:38:29.127 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [c6f3c2e6-68da-4d52-b429-9e62418a2fd7] Message Id [7f17f191-7ab0-46e7-897e-a635c0519287] Device Operation Type [DEVICE_OPERATION_METHOD_SUBSCRIBE_REQUEST] )
2020-11-05 14:38:29.167 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [e1dc7b20-1783-462a-87db-a63042a583c4] Message Id [752f3b01-6c28-4f76-a314-97c75f37bc64] )
2020-11-05 14:38:29.169 DEBUG Thermostat:419 - Telemetry: Sent - {"temperature": 39.6 C} with message Id 752f3b01-6c28-4f76-a314-97c75f37bc64.
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

다음과 같이 디바이스가 명령 및 속성 업데이트에 응답하는 방식을 확인할 수 있습니다.

```output
2020-11-05 14:43:03.179 DEBUG Thermostat:318 - Command: Received - Generating min, max, avg temperature report since Thu Nov 05 06:30:00 GMT 2020.
2020-11-05 14:43:03.180 DEBUG Thermostat:345 - Command: MaxMinReport since Thu Nov 05 06:30:00 GMT 2020: "maxTemp": 33.1 C, "minTemp": 33.1 C, "avgTemp": 33.1 C, "startTime": 2020-11-05T14:42:28Z, "endTime": 2020-11-05T14:42:58Z

...

2020-11-05 14:49:08.182 DEBUG Thermostat:261 - Property: Received - {"targetTemperature": 56.0 C}.
2020-11-05 14:49:08.185 DEBUG Thermostat:270 - Property: Update - {"targetTemperature": 56.0 C} is IN_PROGRESS
2020-11-05 14:49:18.206 DEBUG Thermostat:282 - Property: Update - {"targetTemperature": 55.9 C} is COMPLETED
```

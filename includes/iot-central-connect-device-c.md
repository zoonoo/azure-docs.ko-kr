---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: 941c5cd7bdc789bffea364e47917be0b79fdb461
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114408327"
---
[![코드 찾아보기](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

[!INCLUDE [iot-central-prerequisites-basic](iot-central-prerequisites-basic.md)]

Linux 또는 Windows에서 이 자습서를 실행할 수 있습니다. 이 자습서의 셸 명령은 '`/`' 경로 구분 기호에 대한 Linux 규칙을 따릅니다. Windows에서 계속 실행 중인 경우 '`\`'에 대해 이러한 구분 기호를 교환해야 합니다.

필수 구성 요소는 운영 체제에 따라 다릅니다.

### <a name="linux"></a>Linux

이 자습서에서는 Ubuntu Linux를 사용하고 있다고 가정합니다. 이 자습서의 단계는 Ubuntu 18.04를 사용하여 테스트했습니다.

Linux에서 이 자습서를 완료하려면 로컬 Linux 환경에 다음 소프트웨어를 설치해야 합니다.

`apt-get` 명령을 사용하여 **GCC**, **Git**, **cmake** 및 필요한 모든 종속성을 설치합니다.

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` 버전이 **2.8.12** 보다 크고, **GCC** 버전이 **4.4.7** 보다 큰지 확인합니다.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Windows에서 이 자습서를 완료하려면 로컬 Windows 환경에 다음 소프트웨어를 설치합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) - Visual Studio를 [설치](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019)할 때 **C++를 사용한 데스크톱 개발** 워크로드를 포함해야 합니다.
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

## <a name="download-the-code"></a>코드 다운로드

이 자습서에서는 Azure IoT Hub Device C SDK를 복제하고 빌드하는 데 사용할 수 있는 개발 환경을 준비합니다.

선택한 디렉터리에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK 및 라이브러리](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리를 이 위치에 복제합니다.

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

이 작업을 완료하는 데 몇 분 정도가 걸립니다.

## <a name="review-the-code"></a>코드 검토

이전에 다운로드한 C용 Microsoft Azure IoT SDK 복사본의 텍스트 편집기에서 *azure-iot-sdk-c/iothub_client/samples/pnp/pnp_temperature_controller/pnp_temperature_controller.c* 및 *azure-iot-sdk-c/iothub_client/samples/pnp/pnp_temperature_controller/pnp_thermostat_component.c* 파일을 엽니다.

샘플을 실행하여 IoT Central에 연결하면 DPS(Device Provisioning Service)를 사용하여 디바이스를 등록하고 연결 문자열을 생성합니다. 이 샘플은 명령줄 환경에서 필요한 DPS 연결 정보를 검색합니다.

*pnp_temperature_controller.c* 에서 `main` 함수는 먼저 다음에 대한 `CreateDeviceClientAndAllocateComponents`를 호출합니다.

* `dtmi:com:example:Thermostat;1` 모델 ID를 설정합니다. IoT Central은 모델 ID를 사용하여 이 디바이스에 대한 디바이스 템플릿을 식별하거나 생성합니다. 자세한 내용은 [디바이스 템플릿과 디바이스 연결](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)을 참조하세요.
* DPS를 사용하여 디바이스를 프로비저닝하고 등록합니다.
* 디바이스 클라이언트 핸들을 만들고 IoT Central 애플리케이션에 연결합니다.
* 온도 컨트롤러 구성 요소에서 명령에 대한 처리기를 만듭니다.
* 온도 컨트롤러 구성 요소에서 속성 업데이트에 대한 처리기를 만듭니다.
* 두 개의 자동 온도 조절기 구성 요소를 만듭니다.

다음 `main` 함수:

* 모든 구성 요소에 대한 몇 가지 초기 속성 값을 보고합니다.
* 루프를 시작하여 모든 구성 요소에서 원격 분석을 보냅니다.

그러면 `main` 함수는 스레드를 시작하여 주기적으로 원격 분석을 보냅니다.

```c
int main(void)
{
  IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClient = NULL;

  if ((deviceClient = CreateDeviceClientAndAllocateComponents()) == NULL)
  {
    LogError("Failure creating IotHub device client");
  }
  else
  {
    LogInfo("Successfully created device client.  Hit Control-C to exit program\n");

    int numberOfIterations = 0;

    // During startup, send the non-"writable" properties.
    PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
    PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
    PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
    PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);

    while (true)
    {
      // Wake up periodically to poll.  Even if we do not plan on sending telemetry, we still need to poll periodically in order to process
      // incoming requests from the server and to do connection keep alives.
      if ((numberOfIterations % g_sendTelemetryPollInterval) == 0)
      {
          PnP_TempControlComponent_SendWorkingSet(deviceClient);
          PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
          PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
      }

      IoTHubDeviceClient_LL_DoWork(deviceClient);
      ThreadAPI_Sleep(g_sleepBetweenPollsMs);
      numberOfIterations++;
    }

    // Free the memory allocated to track simulated thermostat.
    PnP_ThermostatComponent_Destroy(g_thermostatHandle2);
    PnP_ThermostatComponent_Destroy(g_thermostatHandle1);

    // Clean up the iothub sdk handle
    IoTHubDeviceClient_LL_Destroy(deviceClient);
    // Free all the sdk subsystem
    IoTHub_Deinit();
  }

  return 0;
}
```

`pnp_thermostat_component.c`에서 `PnP_ThermostatComponent_SendTelemetry` 함수는 디바이스가 구성 요소에서 IoT Central로 온도 원격 분석을 보내는 방법을 보여 줍니다.

```c
void PnP_ThermostatComponent_SendTelemetry(PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle, IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
  PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
  IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
  IOTHUB_CLIENT_RESULT iothubResult;

  char temperatureStringBuffer[32];

  if (snprintf(temperatureStringBuffer, sizeof(temperatureStringBuffer), g_temperatureTelemetryBodyFormat, pnpThermostatComponent->currentTemperature) < 0)
  {
    LogError("snprintf of current temperature telemetry failed");
  }
  else if ((messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer)) == NULL)
  {
    LogError("Unable to create telemetry message");
  }
  else if ((iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL)) != IOTHUB_CLIENT_OK)
  {
    LogError("Unable to send telemetry message, error=%d", iothubResult);
  }

  IoTHubMessage_Destroy(messageHandle);
}
```

`pnp_thermostat_component.c`에서 `PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property` 함수는 구성 요소에서 IoT Central로 `maxTempSinceLastReboot` 속성 업데이트를 보냅니다.

```c
void PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle, IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
  PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
  char maximumTemperatureAsString[32];
  IOTHUB_CLIENT_RESULT iothubClientResult;
  STRING_HANDLE jsonToSend = NULL;

  if (snprintf(maximumTemperatureAsString, sizeof(maximumTemperatureAsString), g_maxTempSinceLastRebootPropertyFormat, pnpThermostatComponent->maxTemperature) < 0)
  {
    LogError("Unable to create max temp since last reboot string for reporting result");
  }
  else if ((jsonToSend = PnP_CreateReportedProperty(pnpThermostatComponent->componentName, g_maxTempSinceLastRebootPropertyName, maximumTemperatureAsString)) == NULL)
  {
    LogError("Unable to build max temp since last reboot property");
  }
  else
  {
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
      LogError("Unable to send reported state, error=%d", iothubClientResult);
    }
    else
    {
      LogInfo("Sending maximumTemperatureSinceLastReboot property to IoTHub for component=%s", pnpThermostatComponent->componentName);
    }
  }

  STRING_delete(jsonToSend);
}
```

`pnp_thermostat_component.c`에서 `PnP_ThermostatComponent_ProcessPropertyUpdate` 함수는 IoT Central에서 쓰기 가능한 속성 업데이트를 처리합니다.

```c
void PnP_ThermostatComponent_ProcessPropertyUpdate(PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle, IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL, const char* propertyName, JSON_Value* propertyValue, int version)
{
  PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;

  if (strcmp(propertyName, g_targetTemperaturePropertyName) != 0)
  {
    LogError("Property=%s was requested to be changed but is not part of the thermostat interface definition", propertyName);
  }
  else if (json_value_get_type(propertyValue) != JSONNumber)
  {
    LogError("JSON field %s is not a number", g_targetTemperaturePropertyName);
  }
  else
  {
    double targetTemperature = json_value_get_number(propertyValue);

    LogInfo("Received targetTemperature=%f for component=%s", targetTemperature, pnpThermostatComponent->componentName);
    
    bool maxTempUpdated = false;
    UpdateTemperatureAndStatistics(pnpThermostatComponent, targetTemperature, &maxTempUpdated);

    // The device needs to let the service know that it has received the targetTemperature desired property.
    SendTargetTemperatureResponse(pnpThermostatComponent, deviceClientLL, version);
    
    if (maxTempUpdated)
    {
      // If the Maximum temperature has been updated, we also report this as a property.
        PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(pnpThermostatComponent, deviceClientLL);
    }
  }
}
```

`pnp_thermostat_component.c`에서 `PnP_ThermostatComponent_ProcessCommand` 함수는 IoT Central에서 호출된 명령을 처리합니다.

```c
int PnP_ThermostatComponent_ProcessCommand(PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle, const char *pnpCommandName, JSON_Value* commandJsonValue, unsigned char** response, size_t* responseSize)
{
  PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
  const char* sinceStr;
  int result;

  if (strcmp(pnpCommandName, g_getMaxMinReport) != 0)
  {
    LogError("PnP command=%s is not supported on thermostat component", pnpCommandName);
    result = PNP_STATUS_NOT_FOUND;
  }
  // See caveats section in ../readme.md; we don't actually respect this sinceStr to keep the sample simple,
  // but want to demonstrate how to parse out in any case.
  else if ((sinceStr = json_value_get_string(commandJsonValue)) == NULL)
  {
    LogError("Cannot retrieve JSON string for command");
    result = PNP_STATUS_BAD_FORMAT;
  }
  else if (BuildMaxMinCommandResponse(pnpThermostatComponent, response, responseSize) == false)
  {
    LogError("Unable to build response for component=%s", pnpThermostatComponent->componentName);
    result = PNP_STATUS_INTERNAL_ERROR;
  }
  else
  {
    LogInfo("Returning success from command request for component=%s", pnpThermostatComponent->componentName);
    result = PNP_STATUS_SUCCESS;
  }

  return result;
}
```

## <a name="build-the-code"></a>코드 빌드

디바이스 SDK를 사용하여 포함된 샘플 코드를 빌드합니다.

1. 디바이스 SDK의 루트 폴더에 _cmake_ 하위 디렉터리를 만들고 해당 폴더로 이동합니다.

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 다음 명령을 실행하여 SDK 및 샘플을 빌드합니다.

    ```cmd\bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

## <a name="get-connection-information"></a>연결 정보 가져오기

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>코드 실행

샘플 애플리케이션을 실행하려면 명령줄 환경을 열고 *azure-iot-sdk-c\cmake* 폴더로 이동합니다.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

예제를 실행하려면

```bash
# Bash
cd iothub_client/samples/pnp/pnp_temperature_controller/
./pnp_temperature_controller
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
.\pnp_temperature_controller.exe
```

다음 출력은 IoT Central에 등록하고 연결하는 디바이스를 보여줍니다. 샘플은 원격 분석 보내기를 시작합니다.

```output
Info: Initiating DPS client to retrieve IoT Hub connection information
-> 09:43:27 CONNECT | VER: 4 | KEEPALIVE: 0 | FLAGS: 194 | USERNAME: 0ne0026656D/registrations/sample-device-01/api-version=2019-03-31&ClientVersion=1.6.0 | PWD: XXXX | CLEAN: 1
<- 09:43:28 CONNACK | SESSION_PRESENT: false | RETURN_CODE: 0x0
-> 09:43:29 SUBSCRIBE | PACKET_ID: 1 | TOPIC_NAME: $dps/registrations/res/# | QOS: 1
<- 09:43:30 SUBACK | PACKET_ID: 1 | RETURN_CODE: 1
-> 09:43:30 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $dps/registrations/PUT/iotdps-register/?$rid=1 | PAYLOAD_LEN: 102
<- 09:43:31 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: $dps/registrations/res/202/?$rid=1&retry-after=3 | PACKET_ID: 2 | PAYLOAD_LEN: 94
-> 09:43:31 PUBACK | PACKET_ID: 2
-> 09:43:33 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $dps/registrations/GET/iotdps-get-operationstatus/?$rid=2&operationId=4.2f792ade0a5c3e68.baf0e879-d88a-4153-afef-71aff51fd847 | PAYLOAD_LEN: 102
<- 09:43:34 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: $dps/registrations/res/202/?$rid=2&retry-after=3 | PACKET_ID: 2 | PAYLOAD_LEN: 173
-> 09:43:34 PUBACK | PACKET_ID: 2
-> 09:43:36 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $dps/registrations/GET/iotdps-get-operationstatus/?$rid=3&operationId=4.2f792ade0a5c3e68.baf0e879-d88a-4153-afef-71aff51fd847 | PAYLOAD_LEN: 102
<- 09:43:37 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: $dps/registrations/res/200/?$rid=3 | PACKET_ID: 2 | PAYLOAD_LEN: 478
-> 09:43:37 PUBACK | PACKET_ID: 2
Info: Provisioning callback indicates success.  iothubUri=iotc-60a....azure-devices.net, deviceId=sample-device-01
-> 09:43:37 DISCONNECT
Info: DPS successfully registered.  Continuing on to creation of IoTHub device client handle.
Info: Successfully created device client.  Hit Control-C to exit program

Info: Sending serialNumber property to IoTHub
Info: Sending device information property to IoTHub.  propertyName=swVersion, propertyValue="1.0.0.0"
Info: Sending device information property to IoTHub.  propertyName=manufacturer, propertyValue="Sample-Manufacturer"
Info: Sending device information property to IoTHub.  propertyName=model, propertyValue="sample-Model-123"
Info: Sending device information property to IoTHub.  propertyName=osName, propertyValue="sample-OperatingSystem-name"
Info: Sending device information property to IoTHub.  propertyName=processorArchitecture, propertyValue="Contoso-Arch-64bit"
Info: Sending device information property to IoTHub.  propertyName=processorManufacturer, propertyValue="Processor Manufacturer(TM)"
Info: Sending device information property to IoTHub.  propertyName=totalStorage, propertyValue=10000
Info: Sending device information property to IoTHub.  propertyName=totalMemory, propertyValue=200
Info: Sending maximumTemperatureSinceLastReboot property to IoTHub for component=thermostat1
Info: Sending maximumTemperatureSinceLastReboot property to IoTHub for component=thermostat2
-> 09:43:44 CONNECT | VER: 4 | KEEPALIVE: 240 | FLAGS: 192 | USERNAME: iotc-60a576a2-eec7-48e2-9306-9e7089a79995.azure-devices.net/sample-device-01/?api-version=2020-09-30&DeviceClientType=iothubclient%2f1.6.0%20(native%3b%20Linux%3b%20x86_64)&model-id=dtmi%3acom%3aexample%3aTemperatureController%3b1 | PWD: XXXX | CLEAN: 0
<- 09:43:44 CONNACK | SESSION_PRESENT: false | RETURN_CODE: 0x0
-> 09:43:44 SUBSCRIBE | PACKET_ID: 2 | TOPIC_NAME: $iothub/twin/res/# | QOS: 0 | TOPIC_NAME: $iothub/methods/POST/# | QOS: 0
-> 09:43:44 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: devices/sample-device-01/messages/events/ | PACKET_ID: 3 | PAYLOAD_LEN: 19
-> 09:43:44 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: devices/sample-device-01/messages/events/%24.sub=thermostat1 | PACKET_ID: 4 | PAYLOAD_LEN: 21
-> 09:43:44 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: devices/sample-device-01/messages/events/%24.sub=thermostat2 | PACKET_ID: 5 | PAYLOAD_LEN: 21
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

다음과 같이 디바이스가 명령 및 속성 업데이트에 응답하는 방식을 확인할 수 있습니다.

```output
<- 09:49:03 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $iothub/methods/POST/thermostat1*getMaxMinReport/?$rid=1 | PAYLOAD_LEN: 26
Info: Received PnP command for component=thermostat1, command=getMaxMinReport
Info: Returning success from command request for component=thermostat1
-> 09:49:03 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $iothub/methods/res/200/?$rid=1 | PAYLOAD_LEN: 117

...

<- 09:50:04 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $iothub/twin/PATCH/properties/desired/?$version=2 | PAYLOAD_LEN: 63
Info: Received targetTemperature=67.000000 for component=thermostat2
Info: Sending acknowledgement of property to IoTHub for component=thermostat2
```

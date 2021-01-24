---
title: IoT 플러그 앤 플레이 브리지에 대 한 어댑터를 빌드하는 방법 | Microsoft Docs
description: IoT 플러그 앤 플레이 브리지 어댑터 구성 요소를 식별 합니다. 사용자 고유의 어댑터를 작성 하 여 브리지를 확장 하는 방법을 알아봅니다.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9a7028dfaeb94e87366de7acfa8cebc4c2f4c767
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746831"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지 확장
[Iot 플러그 앤 플레이 브리지](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) 를 사용 하 여 게이트웨이에 연결 된 기존 장치를 iot hub에 연결할 수 있습니다. 브리지를 사용 하 여 IoT 플러그 앤 플레이 인터페이스를 연결 된 장치에 매핑합니다. IoT 플러그 앤 플레이 인터페이스는 장치에서 전송 하는 원격 분석, 장치와 클라우드 간에 동기화 되는 속성 및 장치가 응답 하는 명령을 정의 합니다. Windows 또는 Linux 게이트웨이에서 오픈 소스 브리지 응용 프로그램을 설치 하 고 구성할 수 있습니다. 또한 브리지가 Azure IoT Edge 런타임 모듈로 실행 될 수 있습니다.

이 문서에서는 다음 방법에 대해 자세히 설명 합니다.

- 어댑터를 사용 하 여 IoT 플러그 앤 플레이 브리지를 확장 합니다.
- 브리지 어댑터에 대 한 일반적인 콜백을 구현 합니다.

브리지를 사용 하는 방법을 보여 주는 간단한 예제는 [Linux 또는 Windows에서 실행 되는 IoT 플러그 앤 플레이 bridge 샘플을 IoT Hub에 연결 하는 방법](howto-use-iot-pnp-bridge.md)을 참조 하세요.

이 문서의 지침 및 샘플에서는 [Azure Digital Twins](../digital-twins/overview.md) 및 [IoT 플러그 앤 플레이](overview-iot-plug-and-play.md)에 대 한 기본 지식이 있다고 가정 합니다. 또한이 문서에서는 [IoT 플러그 앤 플레이 브리지를 빌드하고 배포](howto-build-deploy-extend-pnp-bridge.md)하는 방법을 잘 알고 있다고 가정 합니다.

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>어댑터를 사용 하 여 IoT 플러그 앤 플레이 브리지를 확장 하는 디자인 가이드

브리지의 기능을 확장 하기 위해 고유한 브리지 어댑터를 제작할 수 있습니다.

브리지는 어댑터를 사용 하 여 다음을 수행 합니다.

- 장치와 클라우드 간에 연결을 설정 합니다.
- 장치와 클라우드 간 데이터 흐름을 사용 하도록 설정 합니다.
- 클라우드에서 장치 관리를 사용 하도록 설정 합니다.

모든 브리지 어댑터는 다음을 수행 해야 합니다.

- 디지털 쌍 인터페이스를 만듭니다.
- 인터페이스를 사용 하 여 원격 분석, 속성 및 명령과 같은 클라우드 기반 기능에 장치 쪽 기능을 바인딩합니다.
- 장치 하드웨어 또는 펌웨어와 제어 및 데이터 통신을 설정 합니다.

각 브리지 어댑터는 어댑터가 장치와 상호 작용 하는 방식에 따라 특정 유형의 장치와 상호 작용 합니다. 장치와의 통신에서 핸드셰이킹 프로토콜을 사용 하는 경우에도 브리지 어댑터는 여러 가지 방법으로 장치에서 데이터를 해석할 수 있습니다. 이 시나리오에서 브리지 어댑터는 구성 파일의 어댑터에 대 한 정보를 사용 하 여 어댑터가 데이터를 구문 분석 하는 데 사용 해야 하는 *인터페이스 구성을* 결정 합니다.

장치를 조작 하기 위해 브리지 어댑터는 장치에서 지원 되는 통신 프로토콜 및 기본 운영 체제 또는 장치 공급 업체에서 제공 하는 Api를 사용 합니다.

클라우드와 상호 작용 하기 위해 브리지 어댑터는 Azure IoT 장치 C SDK에서 제공 하는 Api를 사용 하 여 원격 분석을 보내고, 디지털 쌍 인터페이스를 만들고, 속성 업데이트를 전송 하 고, 속성 업데이트 및 명령에 대 한 콜백 함수를 만듭니다.

### <a name="create-a-bridge-adapter"></a>브리지 어댑터 만들기

브리지는 [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) 인터페이스에 정의 된 api를 구현 하는 데 브리지 어댑터가 필요 합니다.

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

이 인터페이스에서:

- `PNPBRIDGE_ADAPTER_CREATE` 어댑터를 만들고 인터페이스 관리 리소스를 설정 합니다. 어댑터는 어댑터 생성을 위해 전역 어댑터 매개 변수를 사용할 수도 있습니다. 이 함수는 단일 어댑터에 대해 한 번 호출 됩니다.
- `PNPBRIDGE_COMPONENT_CREATE` 디지털 쌍 클라이언트 인터페이스를 만들고 콜백 함수를 바인딩합니다. 어댑터는 장치에 대 한 통신 채널을 시작 합니다. 어댑터는 원격 분석 흐름을 사용 하도록 리소스를 설정할 수 있지만가 호출 될 때까지 원격 분석 보고를 시작 하지 않습니다 `PNPBRIDGE_COMPONENT_START` . 이 함수는 구성 파일의 각 인터페이스 구성 요소에 대해 한 번씩 호출 됩니다.
- `PNPBRIDGE_COMPONENT_START` 를 호출 하 여 브리지 어댑터가 장치에서 디지털 쌍 클라이언트로 원격 분석을 전달 하기 시작 합니다. 이 함수는 구성 파일의 각 인터페이스 구성 요소에 대해 한 번씩 호출 됩니다.
- `PNPBRIDGE_COMPONENT_STOP` 원격 분석 흐름을 중지 합니다.
- `PNPBRIDGE_COMPONENT_DESTROY` 디지털 쌍 클라이언트 및 연결 된 인터페이스 리소스를 소멸 시킵니다. 이 함수는 브리지가 손상 되거나 오류가 발생 한 경우 구성 파일의 각 인터페이스 구성 요소에 대해 한 번 호출 됩니다.
- `PNPBRIDGE_ADAPTER_DESTROY` 브리지 어댑터 리소스를 정리 합니다.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>브리지 어댑터와의 브리지 코어 상호 작용

다음 목록에서는 브리지가 시작 될 때 발생 하는 상황을 간략하게 설명 합니다.

1. 연결이 시작 되 면 브리지 어댑터 관리자가 구성 파일에 정의 된 각 인터페이스 구성 요소를 살펴보고 `PNPBRIDGE_ADAPTER_CREATE` 해당 어댑터에서를 호출 합니다. 어댑터는 글로벌 어댑터 구성 매개 변수를 사용 하 여 다양 한 *인터페이스 구성을* 지원 하도록 리소스를 설정할 수 있습니다.
1. 구성 파일의 모든 장치에 대해 브리지 관리자는 `PNPBRIDGE_COMPONENT_CREATE` 적절 한 브리지 어댑터에서를 호출 하 여 인터페이스 만들기를 시작 합니다.
1. 어댑터는 인터페이스 구성 요소에 대 한 선택적 어댑터 구성 설정을 받으며이 정보를 사용 하 여 장치에 대 한 연결을 설정 합니다.
1. 어댑터는 디지털 쌍 클라이언트 인터페이스를 만들고 속성 업데이트 및 명령에 대 한 콜백 함수를 바인딩합니다. 디지털 쌍 인터페이스를 만든 후에는 장치 연결을 설정 하면 콜백이 반환 되는 것을 차단 하지 않아야 합니다. 활성 장치 연결은 브리지가 만드는 활성 인터페이스 클라이언트와는 독립적입니다. 연결에 실패 하는 경우 어댑터는 장치가 비활성 상태인 것으로 가정 합니다. 연결 어댑터는이 연결을 다시 시도 하도록 선택할 수 있습니다.
1. 브리지 어댑터 관리자가 구성 파일에 지정 된 모든 인터페이스 구성 요소를 만든 후에는 모든 인터페이스를 Azure IoT Hub에 등록 합니다. 등록은 블로킹, 비동기 호출입니다. 호출이 완료 되 면 브리지 어댑터에서 콜백이 트리거되고이는 클라우드에서 속성 및 명령 콜백을 처리 하기 시작할 수 있습니다.
1. 그러면 브리지 어댑터 관리자가 `PNPBRIDGE_INTERFACE_START` 각 구성 요소에서를 호출 하 고, 브리지 어댑터가 디지털 쌍 클라이언트에 대 한 원격 분석 보고를 시작 합니다.

### <a name="design-guidelines"></a>디자인 지침

새 브리지 어댑터를 개발 하는 경우 다음 지침을 따르세요.

- 지원 되는 장치 기능과이 어댑터를 사용 하는 구성 요소의 인터페이스 정의를 확인 합니다.
- 어댑터에서 구성 파일에 정의 해야 하는 인터페이스 및 전역 매개 변수를 결정 합니다.
- 구성 요소 속성 및 명령을 지 원하는 데 필요한 하위 수준 장치 통신을 확인 합니다.
- 어댑터가 장치에서 원시 데이터를 구문 분석 하 고 IoT 플러그 앤 플레이 인터페이스 정의에서 지정 하는 원격 분석 형식으로 변환 하는 방법을 결정 합니다.
- 이전에 설명한 브리지 어댑터 인터페이스를 구현 합니다.
- 어댑터 매니페스트에 새 어댑터를 추가 하 고 브리지를 빌드합니다.

### <a name="enable-a-new-bridge-adapter"></a>새 브리지 어댑터 사용

Adapter_manifest에서 참조를 추가 하 여 브리지에서 어댑터를 사용 하도록 설정 [합니다.](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c)

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> 브리지 어댑터 콜백은 순차적으로 호출 됩니다. 어댑터는 브리지 코어에서 진행을 수행 하지 못하도록 하므로 콜백을 차단 하지 않아야 합니다.

### <a name="sample-camera-adapter"></a>샘플 카메라 어댑터

[카메라 어댑터 추가 정보](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) 에서는 사용할 수 있는 샘플 카메라 어댑터에 대해 설명 합니다.

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>일반적인 어댑터 시나리오/콜백에 대 한 코드 예제

다음 섹션에서는 브리지로 연결 된 어댑터가 여러 일반적인 시나리오 및 사용에 대 한 콜백을 구현 하는 방법에 대 한 자세한 정보를 제공 합니다 .이 섹션에서는 다음과 같은 콜백에 대해 설명 합니다.
- [수신 속성 업데이트 (클라우드-장치)](#receive-property-update-cloud-to-device)
- [속성 업데이트 보고 (장치-클라우드)](#report-a-property-update-device-to-cloud)
- [원격 분석 보내기 (장치-클라우드)](#send-telemetry-device-to-cloud)
- [클라우드에서 명령 업데이트 콜백을 수신 하 고 장치 쪽에서 처리 (클라우드-장치)](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [장치 쪽에서 명령 업데이트에 응답 (장치-클라우드)](#respond-to-command-update-on-the-device-side-device-to-cloud)

아래 예제는 [환경 센서 샘플 어댑터](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor)를 기반으로 합니다.

### <a name="receive-property-update-cloud-to-device"></a>수신 속성 업데이트 (클라우드-장치)
첫 번째 단계는 콜백 함수를 등록 하는 것입니다.

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
다음 단계는 장치에서 속성 업데이트를 읽도록 콜백 함수를 구현 하는 것입니다.

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>속성 업데이트 보고 (장치-클라우드)
구성 요소가 생성 된 후 언제 든 지 장치에서 다음 상태를 사용 하 여 클라우드에 속성을 보고할 수 있습니다. 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>원격 분석 보내기 (장치-클라우드)
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>클라우드에서 명령 업데이트 콜백을 수신 하 고 장치 쪽에서 처리 (클라우드-장치)
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>장치 쪽에서 명령 업데이트에 응답 (장치-클라우드)

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>다음 단계

IoT 플러그 앤 플레이 브리지에 대 한 자세한 내용을 보려면 [iot 플러그 앤 플레이 브리지](https://github.com/Azure/iot-plug-and-play-bridge) GitHub 리포지토리를 방문 하세요.

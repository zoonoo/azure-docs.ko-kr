<properties
   pageTitle="Windows에서 C를 사용하여 장치 연결 | Microsoft Azure"
   description="Windows에서 실행되는 C로 작성된 응용 프로그램을 사용하여 미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 장치를 연결하는 방법을 설명합니다."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="dobett"/>


# 미리 구성된 IoT Suite 원격 모니터링 솔루션에 장치 연결

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Windows에서 C 샘플 솔루션 만들기

다음 단계에서는 Visual Studio에서 C 프로그램을 사용하여 미리 구성된 원격 모니터링 솔루션과 통신하는 간단한 클라이언트 응용 프로그램을 만드는 방법을 보여 줍니다.

Visual Studio 2015에서 시작 프로젝트를 만들고 IoT Hub 장치 클라이언트 NuGet 패키지를 추가합니다.

1. Visual Studio 2015에서 Visual C++ **Win32 콘솔 응용 프로그램** 템플릿을 사용하여 새 C 콘솔 응용 프로그램을 만듭니다. 프로젝트 이름을 **RMDevice**로 지정합니다.

2. **Win32 응용 프로그램 마법사**의 **응용 프로그램 설정** 페이지에서 **콘솔 응용 프로그램**이 선택되어 있는지 확인하고 **미리 컴파일된 헤더** 및 **SDL(Security Development Lifecycle) 검사**의 선택을 취소합니다.

3. **솔루션 탐색기**에서 stdafx.h, targetver.h 및 stdafx.cpp 파일을 삭제합니다.

4. **솔루션 탐색기**에서 RMDevice.cpp 파일의 이름을 RMDevice.c로 바꿉니다.

5. **솔루션 탐색기**에서 **RMDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다. **찾아보기**를 클릭하고 다음 NuGet 패키지를 검색하여 프로젝트에 설치합니다.

    - Microsoft.Azure.IoTHub.Serializer
    - Microsoft.Azure.IoTHub.IoTHubClient
    - Microsoft.Azure.IoTHub.HttpTransport

## 간단한 IoT Hub 장치의 동작을 지정하는 코드 추가

IoT Hub 클라이언트 라이브러리는 장치에서 IoT Hub로 보내는 메시지의 형식 및 장치에서 응답하는 IoT Hub의 명령을 지정하는 모델을 사용합니다.

1. Visual Studio에서 RMDevice.c 파일을 엽니다. 기존 `#include` 문을 다음 문으로 바꿉니다.

    ```
    #include "iothubtransporthttp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "threadapi.h"
    ```

2. 다음 변수 선언을 `#include` 문 뒤에 추가합니다. 원격 모니터링 솔루션 대시보드에서 자리 표시자 값 [Device Id] 및 [Device Key]를 장치에 대한 값으로 바꿉니다. 대시보드에서 IoT Hub 호스트 이름을 사용하여 [IoTHub Name]을 바꿉니다. 예를 들어 IoT Hub 호스트 이름이 **contoso.azure-devices.net**인 경우 [IoTHub Name]을 contoso로 바꿉니다.

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```

3. 다음 코드를 추가하여 장치에서 IoT Hub와 통신하도록 지원하는 모델을 정의합니다. 이 모델은 장치에서 온도, 외부 온도, 습도 및 장치 ID를 원격 분석으로 보내도록 지정합니다. 또한 장치는 지원하는 명령 목록을 포함하여 장치에 대한 메타데이터를 IoT Hub로 보냅니다. 이 장치는 **SetTemperature** 및 **SetHumidity**에 응답합니다.

    ```
    // Define the Model
    BEGIN_NAMESPACE(Contoso);

    DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
    );

    DECLARE_STRUCT(DeviceProperties,
    ascii_char_ptr, DeviceID,
    _Bool, HubEnabledState
    );

    DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
    );

    END_NAMESPACE(Contoso);
    ```

## 장치의 동작을 구현하는 코드 추가

이제 모델에 정의된 동작을 구현하는 코드를 추가해야 합니다. 장치가 허브에서 명령을 받을 때 실행되는 함수 및 시뮬레이션된 원격 분석을 허브로 보내는 코드를 추가합니다.

1. 장치가 모델에 정의된 **SetTemperature** 및 **SetHumidity** 명령을 받을 때 실행되는 다음 함수를 추가합니다.

    ```
    EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
    {
      (void)printf("Received temperature %d\r\n", temperature);
      thermostat->Temperature = temperature;
      return EXECUTE_COMMAND_SUCCESS;
    }

    EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
    {
      (void)printf("Received humidity %d\r\n", humidity);
      thermostat->Humidity = humidity;
      return EXECUTE_COMMAND_SUCCESS;
    }
    ```

2. IoT Hub로 메시지를 보내는 다음 함수를 추가합니다.

    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```

3. SDK의 serialization 라이브러리를 연결하는 다음 함수를 추가합니다.

    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```

4. IoT Hub에 연결하고, 메시지를 보내고 받으며, 허브에서 연결을 해제하는 다음 함수를 추가합니다. 장치가 연결되는 즉시 IoT Hub에 자체에 대한 메타데이터(장치에서 지원하는 명령 포함)를 보내는 방식을 확인할 수 있습니다. 이를 통해 솔루션은 대시보드에서 장치의 상태를 **실행 중**으로 업데이트할 수 있습니다.

    ```
    void remote_monitoring_run(void)
    {
      if (serializer_init(NULL) != SERIALIZER_OK)
      {
        printf("Failed on serializer_init\r\n");
      }
      else
      {
        IOTHUB_CLIENT_CONFIG config;
        IOTHUB_CLIENT_HANDLE iotHubClientHandle;

        config.deviceId = deviceId;
        config.deviceKey = deviceKey;
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        iotHubClientHandle = IoTHubClient_Create(&config);
        if (iotHubClientHandle == NULL)
        {
          (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
        }
        else
        {
          Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
          if (thermostat == NULL)
          {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
          }
          else
          {
            STRING_HANDLE commandsMetadata;

            if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
            {
              printf("unable to IoTHubClient_SetMessageCallback\r\n");
            }
            else
            {

              /* send the device info upon startup so that the cloud app knows
              what commands are available and the fact that the device is up */
              thermostat->ObjectType = "DeviceInfo";
              thermostat->IsSimulatedDevice = false;
              thermostat->Version = "1.0";
              thermostat->DeviceProperties.HubEnabledState = true;
              thermostat->DeviceProperties.DeviceID = (char*)deviceId;

              commandsMetadata = STRING_new();
              if (commandsMetadata == NULL)
              {
                (void)printf("Failed on creating string for commands metadata\r\n");
              }
              else
              {
                /* Serialize the commands metadata as a JSON string before sending */
                if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                {
                  (void)printf("Failed serializing commands metadata\r\n");
                }
                else
                {
                  unsigned char* buffer;
                  size_t bufferSize;
                  thermostat->Commands = (char*)STRING_c_str(commandsMetadata);

                  /* Here is the actual send of the Device Info */
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed serializing\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                }

                STRING_delete(commandsMetadata);
              }

              thermostat->Temperature = 50;
              thermostat->ExternalTemperature = 55;
              thermostat->Humidity = 50;
              thermostat->DeviceId = (char*)deviceId;

              while (1)
              {
                unsigned char*buffer;
                size_t bufferSize;

                (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);

                if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                {
                  (void)printf("Failed sending sensor value\r\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }

                ThreadAPI_Sleep(1000);
              }
            }

            DESTROY_MODEL_INSTANCE(thermostat);
          }
          IoTHubClient_Destroy(iotHubClientHandle);
        }
        serializer_deinit();
      }
    }
    ```
    
    참고로, 시작 시 IoT Hub로 전송되는 샘플 **장치 정보** 메시지는 다음과 같습니다.
    ```
    {
      "ObjectType":"DeviceInfo",
      "Version":"1.0",
      "IsSimulatedDevice":false,
      "DeviceProperties":
      {
        "DeviceID":"mydevice01", "HubEnabledState":true
      }, 
      "Commands":
      [
        {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
        { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
      ]
    }
    ```
    
    참고로, IoT Hub로 전송되는 샘플 **원격 분석** 메시지는 다음과 같습니다.
    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```
    
    참고로, IoT Hub에서 수신되는 샘플 **명령**은 다음과 같습니다.
    ```
    {
      "Name":"SetHumidity",
      "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
      "CreatedTime":"2016-03-11T15:09:44.2231295Z",
      "Parameters":{"humidity":23}
    }
    ```

5. **remote\_monitoring\_run** 함수를 호출하려면 **main** 함수를 다음 코드로 바꿉니다.

    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

6. **빌드**를 클릭한 다음 **솔루션 빌드**를 클릭하여 장치 응용 프로그램을 빌드합니다.

7. **솔루션 탐색기**에서 **RMDevice** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 클릭한 다음 **새 인스턴스 시작**을 클릭하여 샘플을 빌드하고 실행합니다. 응용 프로그램에서 샘플 원격 분석을 IoT Hub로 보낼 때 나타나는 메시지가 콘솔에 표시됩니다.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-windows]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows

<!---HONumber=AcomDC_0406_2016-->
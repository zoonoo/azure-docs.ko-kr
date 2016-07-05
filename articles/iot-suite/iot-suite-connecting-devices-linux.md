<properties
   pageTitle="Linux에서 C를 사용하여 장치 연결 | Microsoft Azure"
   description="Linux에서 실행되는 C로 작성된 응용 프로그램을 사용하여 미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 장치를 연결하는 방법을 설명합니다."
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
   ms.date="04/26/2016"
   ms.author="dobett"/>


# 미리 구성된 원격 모니터링 솔루션에 장치 연결(Linux)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## 샘플 C 클라이언트 Linux 빌드 및 실행

다음 절차에서는 Ubuntu Linux 장치에서 작성 및 실행한 C 프로그램을 사용하여 미리 구성된 원격 모니터링 솔루션과 통신하는 간단한 클라이언트 응용 프로그램을 만드는 방법을 보여 줍니다. 이러한 단계를 완료하려면 Ubuntu 버전 15.04 또는 15.10을 실행하는 장치가 필요합니다. 계속하기 전에 다음 명령을 사용하여 Ubuntu 장치에서 필수 구성 요소 패키지를 설치합니다.

```
sudo apt-get install cmake gcc g++
```

## 장치에 클라이언트 라이브러리 설치

Azure IoT Hub 클라이언트 라이브러리를 **apt-get** 명령을 사용하여 Ubuntu 장치에 설치할 수 있는 패키지로 사용할 수 있습니다. Ubuntu 컴퓨터에서 라이브러리와 헤더 파일을 포함하는 패키지를 설치하려면 다음 단계를 완료하세요.

1. 컴퓨터에 AzureIoT 리포지토리를 추가합니다.

    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

2. azure-iot-sdk-c-dev 패키지 설치

    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## 장치의 동작을 지정하는 코드 추가

Ubuntu 컴퓨터에서 **remote\_monitoring**이라는 폴더를 만듭니다. **remote\_monitoring** 폴더에 **main.c**, **remote\_monitoring.c**, **remote\_monitoring.h**, **CMakeLists.txt**의 4개 파일을 만듭니다.

IoT Hub 직렬 변환기 클라이언트 라이브러리는 장치에서 IoT Hub로 보내는 메시지의 형식 및 장치에서 응답하는 IoT Hub의 명령을 지정하는 모델을 사용합니다.

1. 텍스트 편집기에서 **remote\_monitoring.c** 파일을 엽니다. 다음 `#include` 문을 추가합니다.

    ```
    #include "iothubtransportamqp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```

2. 다음 변수 선언을 `#include` 문 뒤에 추가합니다. 원격 모니터링 솔루션 대시보드에서 자리 표시자 값 [Device Id] 및 [Device Key]를 장치에 대한 값으로 바꿉니다. 대시보드에서 IoT Hub 호스트 이름을 사용하여 [IoTHub Name]을 바꿉니다. 예를 들어 IoT Hub 호스트 이름이 **contoso.azure-devices.net**인 경우 [IoTHub Name]을 contoso로 바꿉니다.

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "IoTHub Name]";
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

### 장치의 동작을 구현하는 코드 추가

장치가 허브에서 명령을 받을 때 실행되는 함수 및 시뮬레이션된 원격 분석을 허브로 보내는 코드를 추가합니다.

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
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\r\n");
      }
      else
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
          config.deviceSasToken = NULL;
          config.iotHubName = hubName;
          config.iotHubSuffix = hubSuffix;
          config.protocol = AMQP_Protocol;
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
        platform_deinit();
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

### remote\_monitoring\_run 함수를 호출할 코드 추가

텍스트 편집기에서 **remote\_monitoring.h** 파일을 엽니다. 다음 코드를 추가합니다.

```
void remote_monitoring_run(void);
```

텍스트 편집기에서 **main.c** 파일을 엽니다. 다음 코드를 추가합니다.

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## CMake를 사용하여 클라이언트 응용 프로그램 작성

다음 단계에서는 CMake를 사용하여 클라이언트 응용 프로그램을 빌드하는 방법을 설명합니다.

1. 텍스트 편집기에서 **remote\_monitoring** 폴더의 **CMakeLists.txt** 파일을 엽니다.

2. 클라이언트 응용 프로그램을 작성하는 방법을 정의하려면 다음 지침을 추가합니다.

    ```
    cmake_minimum_required(VERSION 2.8.11)

    set(AZUREIOT_INC_FOLDER ".." "/usr/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_amqp_transport
        aziotsharedutil
        uamqp
        pthread
        curl
        ssl
        crypto
    )
    ```

3. **remote\_monitoring** 폴더에서 CMake가 생성하는 *make* 파일을 저장할 폴더를 만든 후 다음과 같이 **cmake** 및 **make** 명령을 실행합니다.

    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

4. 클라이언트 응용 프로그램을 실행하고 IoT Hub에 원격 분석을 전송합니다.

    ```
    ./sample_app
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-linux]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux

<!---HONumber=AcomDC_0622_2016-->
---
title: Azure IoT Hub 모듈 id 및 모듈 쌍 (Python)
description: Python용 IoT SDK를 사용하여 모듈 ID를 만들고 모듈 쌍을 업데이트하는 방법을 알아봅니다.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.custom: devx-track-python
ms.openlocfilehash: 665281adc892e6b3655c0b1d0533cb3148e62940
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92139405"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>IoT Hub 모듈 id 및 모듈 쌍 시작 (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [모듈 id 및 모듈](iot-hub-devguide-module-twins.md) 쌍은 Azure IoT Hub 장치 id 및 장치 쌍과 유사 하지만 보다 세부적인 세분성을 제공 합니다. 장치 id 및 장치 쌍을 Azure IoT Hub 백 엔드 응용 프로그램에서 장치를 구성 하 고 장치 조건에 대 한 가시성을 제공할 수 있지만, 모듈 id 및 모듈 쌍은 장치의 개별 구성 요소에 대해 이러한 기능을 제공 합니다. 운영 체제 기반 장치 또는 펌웨어 장치와 같은 여러 구성 요소가 포함 된 지원 장치에서는 각 구성 요소에 대 한 격리 된 구성 및 조건을 허용 합니다.
>

이 자습서의 끝 부분에는 다음과 같은 3 개의 Python 앱이 있습니다.

* 장치 id, 모듈 id 및 연결 된 보안 키를 만들어 장치 및 모듈 클라이언트를 연결 하는 **CreateModule**.

* **UpdateModuleTwinDesiredProperties**-업데이트 된 모듈 쌍 desired 속성을 IoT Hub으로 보냅니다.

* **ReceiveModuleTwinDesiredPropertiesPatch**는 장치에서 모듈 쌍 desired 속성 패치를 수신 합니다.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

이 문서에서는 id 레지스트리에서 장치를 추가 하 고 해당 장치에 모듈을 추가 하는 백 엔드 서비스를 만듭니다. 이 서비스에는 레지스트리 **읽기**권한도 포함 된 **레지스트리 쓰기** 권한이 필요 합니다. 또한 새로 만든 모듈의 모듈 쌍에 원하는 속성을 추가 하는 서비스를 만듭니다. 이 서비스에는 **서비스 connect** 권한이 필요 합니다. 이러한 권한을 개별적으로 부여 하는 기본 공유 액세스 정책이 있지만이 섹션에서는 이러한 사용 권한을 모두 포함 하는 사용자 지정 공유 액세스 정책을 만듭니다.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>IoT Hub에서 디바이스 ID 및 모듈 ID 만들기

이 섹션에서는 IoT hub의 id 레지스트리에서 장치 id 및 모듈 id를 만드는 Python 서비스 앱을 만듭니다. Id 레지스트리에 항목이 없는 경우 장치 또는 모듈을 IoT hub에 연결할 수 없습니다. 자세한 내용은 [IoT hub의 id 레지스트리 이해](iot-hub-devguide-identity-registry.md)를 참조 하세요. 이 콘솔 앱을 실행하면 디바이스 및 모듈 둘 다의 고유한 ID 및 키가 생성됩니다. 디바이스 및 모듈은 IoT Hub에 디바이스-클라우드 메시지를 보낼 때 이러한 값을 사용하여 자신을 식별합니다. ID는 대/소문자를 구분합니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-hub** 패키지를 설치합니다.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. 명령 프롬프트에서 다음 명령을 실행 하 여 **msrest** 패키지를 설치 합니다. **HTTPOperationError** 예외를 catch 하려면이 패키지가 필요 합니다.

    ```cmd/sh
    pip install msrest
    ```

1. 텍스트 편집기를 사용 하 여 작업 디렉터리에 **CreateModule.py** 라는 파일을 만듭니다.

1. Python 파일에 다음 코드를 추가 합니다. *YourIoTHubConnectionString* 를 [IoT Hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 연결 문자열로 바꿉니다.

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. 명령 프롬프트에서 다음 명령을 실행 합니다.

    ```cmd/sh
    python CreateModule.py
    ```

이 앱은 ID가 **myFirstDevice**인 디바이스 ID와 ID가 **myFirstModule**인 모듈 ID를 **myFirstDevice** 디바이스 아래에 만듭니다. (장치 또는 모듈 ID가 이미 id 레지스트리에 있는 경우 코드는 기존 장치 또는 모듈 정보를 검색 하기만 합니다.) 앱은 각 id에 대 한 ID 및 기본 키를 표시 합니다.

> [!NOTE]
> IoT Hub ID 레지스트리는 디바이스 및 모듈 ID만 저장하여 IoT Hub에 보안 액세스를 사용합니다. ID 레지스트리는 보안 자격 증명으로 사용할 디바이스 ID 및 키를 저장합니다. 또한 ID 레지스트리는 각 디바이스에 대한 액세스를 사용하지 않도록 설정하는 데 사용할 수 있는 해당 디바이스에 대한 enabled/disabled 플래그를 저장합니다. 애플리케이션이 다른 디바이스별 메타데이터를 저장해야 할 경우 애플리케이션별 스토리지를 사용해야 합니다. 모듈 ID에 대한 enabled/disabled 플래그는 없습니다. 자세한 내용은 [IoT hub의 id 레지스트리 이해](iot-hub-devguide-identity-registry.md)를 참조 하세요.
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Python 서비스 SDK를 사용 하 여 모듈 쌍 업데이트

이 섹션에서는 모듈 쌍 desired 속성을 업데이트 하는 Python 서비스 앱을 만듭니다.

1. 명령 프롬프트에서 다음 명령을 실행 하 여 **azure-iot-허브** 패키지를 설치 합니다. 이전 섹션에서 **azure-iot-허브** 패키지를 설치한 경우이 단계를 건너뛸 수 있습니다.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. 텍스트 편집기를 사용 하 여 작업 디렉터리에 **UpdateModuleTwinDesiredProperties.py** 라는 파일을 만듭니다.

1. Python 파일에 다음 코드를 추가 합니다. *YourIoTHubConnectionString* 를 [IoT Hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 연결 문자열로 바꿉니다.

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>디바이스 쪽에서 업데이트

이 섹션에서는 Python 앱을 만들어 장치에서 모듈 쌍 desired 속성 업데이트를 가져옵니다.

1. 모듈 연결 문자열을 가져옵니다. [Azure Portal](https://portal.azure.com/)에서 IoT Hub로 이동 하 고 왼쪽 창에서 **IoT 장치** 를 선택 합니다. 장치 목록에서 **Myfirstdevice** 를 선택 하 고 엽니다. **모듈 id**에서 **myfirstmodule**을 선택 합니다. 모듈 연결 문자열을 복사합니다. 다음 단계에서 필요 합니다.

   ![Azure Portal 모듈 세부 정보](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 패키지를 설치합니다.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 텍스트 편집기를 사용 하 여 작업 디렉터리에 **ReceiveModuleTwinDesiredPropertiesPatch.py** 라는 파일을 만듭니다.

1. Python 파일에 다음 코드를 추가 합니다. *YourModuleConnectionString* 을 1 단계에서 복사한 모듈 연결 문자열로 바꿉니다.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>앱 실행

이 섹션에서는 **ReceiveModuleTwinDesiredPropertiesPatch** device 앱을 실행 한 다음 **UpdateModuleTwinDesiredProperties** service 앱을 실행 하 여 모듈의 desired 속성을 업데이트 합니다.

1. 명령 프롬프트를 열고 장치 앱을 실행 합니다.

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![장치 앱 초기 출력](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. 별도의 명령 프롬프트를 열고 서비스 앱을 실행 합니다.

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    **TelemetryInterval** desired 속성이 서비스 앱 출력의 업데이트 된 모듈 쌍에 표시 되는지 확인 합니다.

   ![서비스 앱 출력](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    장치 앱 출력에서 받은 desired 속성 패치에 동일한 속성이 표시 됩니다.

   ![장치 앱 출력은 desired 속성 패치를 표시 합니다.](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>다음 단계

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [디바이스 관리 시작](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge 시작](../iot-edge/quickstart-linux.md)
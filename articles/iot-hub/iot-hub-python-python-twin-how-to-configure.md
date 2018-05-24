---
title: Azure IoT Hub 장치 쌍 속성(Python) 사용 | Microsoft Docs
description: Azure IoT Hub 장치 쌍을 사용하여 장치를 구성하는 방법입니다. Python용 Azure IoT SDK를 사용하여 시뮬레이션된 장치 앱을 구현하고 장치 쌍으로 장치 구성을 수정하는 서비스 앱을 구현합니다.
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: kgremban
ms.openlocfilehash: f6e002eb570ade7fc4008cc69e6042bd3dd97f7e
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="use-desired-properties-to-configure-devices-python"></a>desired 속성을 사용하여 장치 구성(Python)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

이 자습서의 끝 부분에 다음의 두 Python 콘솔 앱이 설치됩니다.

* **SimulateDeviceConfiguration.py** - 원하는 구성 업데이트를 기다리고 시뮬레이션된 구성 업데이트 프로세스의 상태를 보고하는 시뮬레이션된 장치 앱입니다.
* **SetDesiredConfigurationAndQuery.py** - 장치에 원하는 구성을 설정하고 구성 업데이트 프로세스를 쿼리하는 Python 백 엔드 앱입니다.

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 문서는 장치 및 백 엔드 앱을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 대한 정보를 제공합니다.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* [Python 2.x 또는 3.x][lnk-python-download]. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 나타나면 플랫폼별 환경 변수에 Python을 추가해야 합니다. Python 2.x를 사용하는 경우 [Python 패키지 관리 시스템인 *pip*을 설치 또는 업그레이드][lnk-install-pip]해야 할 수도 있습니다.
* Windows OS를 사용하는 경우 Python에서 네이티브 DLL을 사용하기 위해 필요한 [Visual C++ 재배포 가능 패키지][lnk-visual-c-redist].
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[장치 쌍 시작][lnk-twin-tutorial] 자습서를 수행했으면 이미 IoT Hub 및 **myDeviceId**라는 장치 ID가 있으므로 [시뮬레이션된 장치 앱 만들기][lnk-how-to-configure-createapp] 섹션으로 건너뛸 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 **myDeviceId**로 허브에 연결하는 Python 콘솔 앱을 만들고, 원하는 구성 업데이트를 기다린 다음, 시뮬레이션된 구성 업데이트 프로세스의 업데이트를 보고합니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 **Azure IoT Python 장치 SDK**를 설치합니다.
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. 텍스트 편집기를 사용하여 새 **SimulateDeviceConfiguration.py** 파일을 만듭니다.

1. 다음 코드를 **SimulateDeviceConfiguration.py** 파일에 추가하고 **myDeviceId** 장치 ID를 만들 때 복사한 장치 연결 문자열을 사용해 **{장치 연결 문자열}** 자리 표시자를 대체합니다.

    ```python   
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0

    TWIN_CALLBACKS = 0
    SEND_REPORTED_STATE_CALLBACKS = 0

    CONFIG_ID = "1"
    TWIN_PAYLOAD = "{\"configId\":" + CONFIG_ID + ",\"sendFrequency\":\"24h\"}"
    ```

    **CLIENT** 개체는 서비스의 장치 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 추가 코드에서는 desired 속성에 대한 업데이트에 필요한 핸들러를 연결하고, configId를 비교해 실제 구성 변경 요청이 있는지 확인하는 핸들러를 추가합니다. 그러면 구성 변경을 시작하는 메서드가 호출됩니다. 간단한 설명을 위해 이전 코드에서는 초기 구성에 대한 하드 코드된 기본값을 사용합니다. 실제 앱은 아마도 로컬 저장소로부터 그 구성을 로드할 것입니다.
   
   > [!IMPORTANT]
   > Desired 속성 변경 이벤트는 항상 장치 연결시 한 번에 내보내지기 때문에, 모든 작업을 수행하기 전에 Desired 속성에 실제 변경이 있는지 확인해야 합니다.
   > 

1. **SimulateDeviceConfiguration.py** 파일의 끝에 다음 코드를 추가합니다.

    ```python
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global CONFIG_ID

        desired_configId = payload[payload.find("configId")+11:payload.find("configId")+12]
        if CONFIG_ID != desired_configId:
            print ( "" )
            print ( "Reported pending config change: %s" % payload)
        
            desired_sendFrequency = payload[payload.find("sendFrequency")+17:payload.find("sendFrequency")+19]
            print ( "...desired configId: " + desired_configId)
            print ( "...desired sendFrequency: " + desired_sendFrequency)
            new_payload = "{\"configId\":" + desired_configId + ",\"sendFrequency\":\"" + desired_sendFrequency + "\"}"
            CLIENT.send_reported_state(new_payload, len(new_payload), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
        CONFIG_ID = desired_configId
    
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "" )
        print ( "Device twins updated." )
    ```
   
    **device_twin_callback** 이 메서드는 구성 업데이트 요청으로 로컬 장치 쌍 개체에 대해 보고된 속성을 업데이트하고 _configId_를 설정합니다. 장치 쌍을 성공적으로 업데이트하면 업데이트 메시지가 출력됩니다. 대역폭을 절약하기 위해 보고된 속성은 문서 전체를 교체하는 대신에 수정할 속성(위의 코드에서 **TWIN_PAYLOAD**라고 명명됨)만 지정하여 업데이트합니다.
   
   > [!NOTE]
   > 이 자습서는 동시 구성 업데이트에 대해 어떤 동작도 시뮬레이션하지 않습니다. 일부 구성 업데이트 프로세스는 업데이트가 실행되는 중에 대상 구성의 변경을 수용할 수 있는 반면에 다른 프로세스는 대기해야 하거나 오류 조건을 사용해 거부할 수 있습니다. 특정 구성 프로세스에 대해 원하는 동작을 고려하여 구성 변경을 시작하기 전에 적절한 논리를 추가해야 합니다.
   > 

1. **SimulateDeviceConfiguration.py** 파일의 끝에 다음 코드를 추가합니다. 

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()
        
            CLIENT.send_reported_state(TWIN_PAYLOAD, len(TWIN_PAYLOAD), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. 장치 앱 실행.
   
    ```cmd/sh
    python SimulateDeviceConfiguration.py
    ```
   
    메시지 `Device twins updated.`이 표시되어야 합니다. 앱이 계속 실행되게 합니다.


## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기
이 섹션에서는 새로운 원격 분석 구성 개체를 사용하여 **myDeviceId**와 연결된 장치 쌍에서 *desired 속성*을 업데이트하는 Python 콘솔 앱을 만듭니다. 그런 다음 IoT Hub에 저장된 장치 쌍을 쿼리하고 장치의 desired 구성과 reported 구성 사이의 차이점을 표시합니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 **Azure IoT Python Service SDK**를 설치합니다.
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. 텍스트 편집기를 사용하여 새 **SetDesiredAndQuery.py** 파일을 만듭니다.

1. 다음 코드를 **SetDesiredAndQuery.py** 파일에 추가하고 허브를 만들 때 복사한 IoT Hub 연결 문자열을 사용해 **{IoTHubConnectionString}** 자리 표시자를 대체하고, **{deviceId}** 자리 표시자는 장치 이름으로 대체합니다.

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubError, IoTHubDeviceTwin

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"configId\":3, \"sendFrequency\":\"" + sys.argv[1:][0] + "\"}}}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. **SetDesiredAndQuery.py** 파일의 끝에 다음 코드를 추가합니다.

    ```python
    def iothub_devicetwin_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
        
            twin_info = iothub_twin_method.get_twin(DEVICE_ID)
            print ( "" )
            print ( "Device Twin before update    :" )
            print ( "{0}".format(twin_info) )

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
            print ( "" )
            print ( "Device Twin after update     :" )
            print ( "{0}".format(twin_info) )
        
            while True:
                time.sleep(10)

                twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                print ( "" )
                print ( "Device Twin after client change initiated    :" )
                print ( "{0}".format(twin_info) )
        
                print ( "" )
                print ( "IoT Hub service sample complete, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(30)
                
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceTwins Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicetwin_sample_run()
    ```

1. **SimulateDeviceConfiguration.py**가 실행 중인 상태에서 다음을 사용해 새 명령 프롬프트에서 응용 프로그램을 실행합니다.

    ```cmd/sh
    python SetDesiredAndQuery.py 5m
    ```
   
    **1**에서 **2**로의 reported 구성 ID 변경 내용과 24시간이 아닌 5분이라는 새로운 활성 보내기 빈도가 표시되어야 합니다.


## <a name="next-steps"></a>다음 단계
이 자습서에서는 백 엔드 앱에서 원하는 구성을 *desired 속성*으로 설정하고 시뮬레이션된 장치 앱을 작성하여 그 변경을 감지하고 장치 쌍에 그 상태를 *reported 속성*으로 보고하는 업데이트 프로세스를 시뮬레이트합니다.

아래와 같이 실행할 방법을 알아보려면 다음 리소스를 참조하세요.

* [IoT Hub 시작][lnk-iothub-getstarted] 자습서를 참조하여 장치에서 원격 분석을 보냅니다.
* [jobs 예약 및 브로드캐스트][lnk-schedule-jobs] 자습서를 참조하여 대규모 장치 집합에 대한 작업을 예약하거나 수행합니다.
* [직접 메서드 사용][lnk-methods-tutorial] 자습서를 참조하여 대화형으로(예: 사용자 제어 앱에서 팬 작동) 장치를 제어합니다.

<!-- links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-python-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-methods-tutorial]: iot-hub-python-python-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app

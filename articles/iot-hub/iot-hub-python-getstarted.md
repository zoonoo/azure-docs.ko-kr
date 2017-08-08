---
title: "Azure IoT Hub 시작(Python) | Microsoft Docs"
description: "Python용 IoT SDK를 사용하여 Azure IoT Hub에 장치-클라우드 메시지를 보내는 방법을 알아봅니다. 시뮬레이션된 장치 및 서비스 앱을 만들어서 장치를 등록하고 메시지를 전송하고 IoT Hub의 메시지를 읽습니다."
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/22/2017
ms.author: dkshir
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f0800b70620106f9ae9d19d63b60b726835e338f
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>Python을 사용하여 IoT Hub에 시뮬레이션된 장치 연결
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

이 자습서가 끝나면 다음과 같은 두 개의 Python 앱이 생깁니다.

* **CreateDeviceIdentity.py**는 장치 ID 및 시뮬레이션된 보안 키를 만들어 시뮬레이션된 장치 앱에 연결합니다.
* **SimulatedDevice.py**는 앞에서 만든 장치 ID로 IoT Hub에 연결하고 MQTT 프로토콜을 사용하여 주기적으로 원격 분석 메시지를 보냅니다.

> [!NOTE]
> [Azure IoT SDKs][lnk-hub-sdks] 문서는 장치와 솔루션 백 엔드에서 실행하기 위해 두 응용 프로그램을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보를 제공합니다.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* [Python 2.x 또는 3.x][lnk-python-download]. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 나타나면 플랫폼별 환경 변수에 Python을 추가해야 합니다. Python 2.x를 사용하는 경우 [Python 패키지 관리 시스템인 *pip*을 설치 또는 업그레이드][lnk-install-pip]해야 할 수도 있습니다.
* Windows OS를 사용하는 경우 Python에서 네이티브 DLL을 사용하기 위해 필요한 [Visual C++ 재배포 가능 패키지][lnk-visual-c-redist].
* [Node.js 4.0 이상][lnk-node-download]. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. [IoT Hub 탐색기 도구][lnk-iot-hub-explorer]를 설치하는 데 필요합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 내에 [계정][lnk-free-trial]을 만들 수 있습니다.

> [!NOTE]
> `azure-iothub-service-client` 및 `azure-iothub-device-client`에 대한 *pip* 패키지는 현재 Windows OS에만 사용할 수 있습니다. Linux/Mac OS의 경우 [Python용 개발 환경 준비][lnk-python-devbox] 게시물에서 Linux 및 Mac OS에 해당하는 섹션을 참조하세요.
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

IoT Hub를 만들었습니다. 이 자습서 나머지 부분에서는 IoT Hub 호스트 이름 및 IoT Hub 연결 문자열을 사용합니다.

> [!NOTE]
> Python 또는 Node.js 기반 Azure CLI를 사용하여 손쉽게 명령줄에서 IoT Hub를 만들 수도 있습니다. [Azure CLI 2.0을 사용하여 IoT Hub 만들기][lnk-azure-cli-hub] 문서에 이 작업을 수행하는 빠른 단계가 설명되어 있습니다. 
> 

## <a name="create-a-device-identity"></a>장치 ID 만들기
이 섹션에서는 IoT Hub의 ID 레지스트리에서 장치 ID를 만드는 Python 콘솔 앱을 작성하는 단계를 설명합니다. ID 레지스트리에 항목이 있는 경우에만 장치를 IoT Hub에 연결할 수 있습니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]의 **ID 레지스트리** 섹션을 참조하세요. 이 콘솔 앱을 실행하면 장치-클라우드 메시지를 IoT Hub로 보낼 때 장치가 자체적으로 ID를 식별하는 데 사용할 수 있는 고유한 장치 ID와 키를 생성합니다.

1. 명령 프롬프트를 열고 **Python용 Azure IoT Hub 서비스 SDK**를 설치합니다. SDK를 설치한 후 명령 프롬프트를 닫습니다.

    ```
    pip install azure-iothub-service-client
    ```

2. **CreateDeviceIdentity.py**라는 이름의 Python 파일을 만듭니다. [원하는 Python 편집기/IDE][lnk-python-ide-list](예: 기본 [IDLE][lnk-idle])에서 이 파일을 엽니다.

3. SDK 서비스에서 필요한 모듈을 가져오는 다음 코드를 추가합니다.

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. 다음 코드를 추가하고, `[IoTHub Connection String]` 자리 표시자를 이전 섹션에서 만든 IoT Hub의 연결 문자열로 바꿉니다. `DEVICE_ID`로 아무 이름이나 사용할 수 있습니다.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

3. 일부 장치 정보를 인쇄하려면 다음 함수를 추가합니다.

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. 레지스트리 관리자를 사용하여 장치 ID를 만들려면 다음 함수를 추가합니다. 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. 마지막으로 다음과 같이 주 함수를 추가하고 파일을 저장합니다.

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. 다음과 같이 명령 프롬프트에서 **CreateDeviceIdentity.py**를 실행합니다.

    ```python
    python CreateDeviceIdentity.py
    ```
6. 시뮬레이션된 장치가 만들어지는 것이 보입니다. 이 장치의 **deviceId** 및 **primaryKey**를 적어 둡니다. 나중에 장치로 IoT Hub에 연결하는 응용 프로그램을 만들 때 이러한 값이 필요합니다.

    ![장치 만들기 성공][1]

> [!NOTE]
> IoT Hub ID 레지스트리는 장치 ID만 저장하여 IoT Hub에 보안 액세스를 사용합니다. 보안 자격 증명으로 사용하기 위해 장치 ID 및 키와 개별 장치에 대해 액세스하지 못하도록 설정할 수 있는 사용/사용 안 함 플래그를 저장합니다. 응용 프로그램이 다른 장치별 메타데이터를 저장해야 할 경우 응용 프로그램별 저장소를 사용해야 합니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]를 참조하세요.
> 
> 


## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 장치를 시뮬레이션하고 IoT Hub로 장치-클라우드 메시지를 전송하는 Python 콘솔 앱을 만드는 단계를 설명합니다.

1. 다음과 같이 새 명령 프롬프트를 열고 Python용 Azure IoT Hub 장치 SDK를 설치합니다. 설치한 후 명령 프롬프트를 닫습니다.

    ```
    pip install azure-iothub-device-client
    ```
2. **SimulatedDevice.py**라는 이름의 파일을 만듭니다. 원하는 Python 편집기/IDE(예: IDLE)에서 이 파일을 엽니다.

3. 장치 SDK에서 필요한 모듈을 가져오는 다음 코드를 추가합니다.

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. 다음 코드를 추가하고 `[IoTHub Device Connection String]`의 자리 표시자를 장치에 대한 연결 문자열로 바꿉니다. 장치 연결 문자열은 일반적으로 `HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>` 형식입니다. 이전 섹션에서 만든 장치의 **deviceId** 및 **primaryKey**로 각각 `<deviceId>` 및 `<primaryKey>`를 대체합니다. `<hostName>`을 IoT Hub의 호스트 이름으로 바꿉니다. 일반적으로 `<IoT hub name>.azure-devices.net`입니다.

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. 송신 확인 콜백을 정의하는 다음 코드를 추가합니다. 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. 장치 클라이언트를 초기화하는 다음 코드를 추가합니다.

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        return client
    ```
7. 시뮬레이션된 장치의 메시지를 포맷하여 IoT Hub로 보내는 다음 함수를 추가합니다.

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                # messages can be encoded as string or bytearray
                if (message_counter & 1) == 1:
                    message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
                else:
                    message = IoTHubMessage(msg_txt_formatted)
                # optional: assign ids
                message.message_id = "message_%d" % message_counter
                message.correlation_id = "correlation_%d" % message_counter
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

                client.send_event_async(message, send_confirmation_callback, message_counter)
                print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

                status = client.get_send_status()
                print ( "Send status: %s" % status )
                time.sleep(30)

                status = client.get_send_status()
                print ( "Send status: %s" % status )

                message_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```
8. 마지막으로 주 함수를 추가합니다. 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. **SimulatedDevice.py** 파일을 저장하고 닫습니다. 이제 이 앱을 실행할 준비가 되었습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>시뮬레이션된 장치에서 메시지 받기
장치에서 원격 분석 메시지를 받으려면 IoT Hub를 통해 노출되며 장치-클라우드 메시지를 읽는 [Event Hubs][lnk-event-hubs-overview] 호환 끝점을 사용해야 합니다. Event Hubs에서 오는 IoT Hub의 Event Hub 호환 끝점에 대한 메시지를 처리하는 방법은 [Event Hubs 시작][lnk-eventhubs-tutorial] 자습서를 참조하세요. Event Hubs는 아직 Python에서 원격 분석을 지원하지 않습니다. 따라서 [Node.js](iot-hub-node-node-getstarted.md#D2C_node) 또는 [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp) Event Hubs 기반 콘솔 앱을 만들어서 IoT Hub에서 오는 장치-클라우드 메시지를 읽을 수 있습니다. 이 자습서에서는 [IoT Hub 탐색기 도구][lnk-iot-hub-explorer]를 사용하여 이러한 장치 메시지를 읽는 방법을 보여 줍니다.

1. 명령 프롬프트를 열고 IoT Hub 탐색기를 설치합니다. 

    ```
    npm install -g iothub-explorer
    ```

2. 명령 프롬프트에서 다음 명령을 실행하여 장치에서 오는 장치-클라우드 메시지의 모니터링을 시작합니다. `--login` 뒤의 자리 표시자에 IoT Hub의 연결 문자열을 사용합니다.

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. 새 명령 프롬프트를 열고 **SimulatedDevice.py** 파일이 들어 있는 디렉터리로 이동합니다.

4. 주기적으로 IoT Hub에 원격 분석 데이터를 보내는 **SimulatedDevice.py** 파일을 실행합니다. 
   
    ```
    python SimulatedDevice.py
    ```
5. 이전 섹션의 IoT Hub 탐색기를 실행하는 명령 프롬프트에서 장치 메시지를 관찰합니다. 

    ![Python 장치-클라우드 메시지][2]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 장치 ID를 만들었습니다. 장치-클라우드 메시지를 IoT Hub로 보내기 위해 시뮬레이션된 장치 앱을 사용하는 이 장치 ID를 사용했습니다. IoT Hub 탐색기 도구의 도움을 받아 IoT Hub에서 받은 메시지를 관찰했습니다. 

Azure IoT Hub용 Python SDK의 사용 현황을 자세히 살펴보려면 [이 Git Hub 리포지토리][lnk-python-github]를 방문하세요. Python용 Azure IoT Hub 서비스 SDK의 메시지 기능을 검토하려면 [iothub_messaging_sample.py][lnk-messaging-sample] 파일을 다운로드하여 실행하세요. Python용 Azure IoT Hub 장치 SDK를 사용하는 장치 쪽 시뮬레이션의 경우 [iothub_client_sample.py][lnk-client-sample] 파일을 다운로드하여 실행할 수 있습니다.

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [장치 연결][lnk-connect-device]
* [장치 관리 시작][lnk-device-management]
* [Azure IoT Hub 시작][lnk-iot-edge]

IoT 솔루션을 확장하고 대량의 장치-클라우드 메시지를 처리하는 방법을 알아보려면 [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial] 자습서를 참조하세요.
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/


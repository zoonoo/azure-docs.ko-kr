---
title: Azure IoT Hub(Python)를 사용하여 작업 예약 | Microsoft Docs
description: 여러 디바이스에서 직접 메서드를 호출하여 Azure IoT Hub 작업을 예약하는 방법입니다. Python용 Azure IoT SDK를 사용하여 시뮬레이션된 디바이스 앱 및 작업을 실행하는 서비스 앱을 구현합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.openlocfilehash: 1d721e89534c09a5572e5674796f28355f652165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527404"
---
# <a name="schedule-and-broadcast-jobs-python"></a>작업 예약 및 브로드캐스트(Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub는 백 엔드 앱에서 수백만 개의 디바이스를 예약 및 업데이트하는 작업을 만들고 추적할 수 있게 하는 완전히 관리되는 서비스입니다.  작업(job)은 다음과 같은 작업(action)에 사용될 수 있습니다.

* desired 속성 업데이트
* tags 업데이트
* 직접 메서드 호출

개념적으로 작업(job)은 이러한 작업(action) 중 하나를 래핑하고 디바이스 쌍 쿼리로 정의된 디바이스 집합에 대해 실행 진행 상태를 추적합니다.  예를 들어 백 엔드 앱은 작업을 사용하여 디바이스 쌍 쿼리로 지정되고 향후에 예약된 10,000개 디바이스에서 다시 부팅 메서드를 호출할 수 있습니다.  그런 다음 애플리케이션은 해당하는 각 디바이스에서 재부팅 메서드를 수신 및 실행함에 따라 진행 상태를 추적할 수 있습니다.

이러한 기능에 대한 자세한 내용은 다음 문서를 참조하세요.

* 디바이스 쌍 및 속성: [디바이스 쌍 시작](iot-hub-python-twin-getstarted.md) 및 [자습서: 디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)

* 직접 메서드: [IoT Hub 개발자 가이드 - 직접 메서드](iot-hub-devguide-direct-methods.md) 및 [자습서: 직접 메서드](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 솔루션 백 엔드에서 **LockDoor**를 호출할 수 있는 직접 메서드가 포함된 Python 시뮬레이션된 디바이스 앱을 만듭니다.

* 작업을 사용하여 시뮬레이션된 디바이스 앱에서 **LockDoor** 직접 메서드를 호출하고, 디바이스 작업을 사용하여 desired 속성을 업데이트하는 Python 콘솔 앱을 만듭니다.

이 자습서가 끝나면 다음과 같은 두 개의 Python 앱이 생깁니다.

**simDevice.py** - 디바이스 ID로 IoT Hub에 연결하고 **LockDoor** 직접 메서드를 수신합니다.

**scheduleJobService.py**는 시뮬레이션된 디바이스 앱에서 직접 메서드를 호출하고 작업을 사용하여 디바이스 쌍의 desired 속성을 업데이트합니다.

> [!NOTE]
> **Python용 Azure IoT SDK**는 **작업** 기능을 직접 지원하지 않습니다. 이 자습서에서는 비동기 스레드 및 타이머를 사용하는 대체 솔루션을 제공합니다. 추가 업데이트는 [Python용 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-python) 페이지에서 **Service Client SDK** 기능 목록을 참조하세요.
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 디바이스 앱 만들기

이 섹션에서는 클라우드에서 호출한 메서드에 응답하는 Python 콘솔 앱을 만듭니다. 이 메서드는 시뮬레이션된 **LockDoor** 메서드를 트리거합니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot 장치** 패키지를 설치합니다.

    ```cmd/sh
    pip install azure-iot-device
    ```

2. 텍스트 편집기를 사용하여 작업 디렉터리에 새 **simDevice.py** 파일을 만듭니다.

3. **simDevice.py** 파일의 시작 부분에 다음 `import` 문 및 변수를 추가합니다. `deviceConnectionString`을 위에서 만든 디바이스의 연결 문자열로 바꿉니다.

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. 다음 함수 콜백을 추가하여 **LockDoor** 메서드를 처리합니다.

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. 디바이스 쌍 업데이트를 처리하는 다른 함수 콜백을 추가합니다.

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. 다음 코드를 추가하여 **lockDoor** 메서드에 대한 처리기를 등록합니다. `main` 루틴도 포함하세요.

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. **simDevice.py** 파일을 저장하고 닫습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.
>

## <a name="get-the-iot-hub-connection-string"></a>IoT 허브 연결 문자열 받기

이 문서에서는 장치에서 직접 메서드를 호출 하고 장치 쌍 업데이트 하는 백 엔드 서비스를 만듭니다. 서비스는 장치에서 직접 메서드를 호출하는 **서비스 연결** 권한이 필요합니다. 또한 이 서비스는 **ID 레지스트리를 읽고** 쓸 수 있는 레지스트리 읽기 및 레지스트리 **쓰기** 권한이 필요합니다. 이러한 사용 권한만 포함하는 기본 공유 액세스 정책은 없으므로 이러한 사용 권한만 만들어야 합니다.

**서비스 연결,** **레지스트리 읽기**및 레지스트리 **쓰기** 권한을 부여하는 공유 액세스 정책을 만들고 이 정책에 대한 연결 문자열을 얻으려면 다음 단계를 따르십시오.

1. [Azure 포털에서](https://portal.azure.com)IoT 허브를 엽니다. IoT 허브로 이동하는 가장 쉬운 방법은 **리소스 그룹을**선택하고 IoT 허브가 있는 리소스 그룹을 선택한 다음 리소스 목록에서 IoT 허브를 선택하는 것입니다.

2. IoT 허브의 왼쪽 창에서 **공유 액세스 정책을**선택합니다.

3. 정책 목록 위의 위쪽 메뉴에서 **추가를**선택합니다.

4. 공유 **액세스 정책** 추가 창에서 정책에 대한 설명이름을 입력합니다. 예 : *서비스And레지스트리읽기 쓰기*. **사용 권한에서** **서비스 연결** 및 레지스트리 **쓰기를** 선택합니다(레지스트리 **쓰기를**선택하면**레지스트리 읽기가** 자동으로 선택됨). 그런 다음 **을 선택합니다.**

    ![새 공유 액세스 정책을 추가하는 방법 표시](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. **공유 액세스 정책** 창에서 정책 목록에서 새 정책을 선택합니다.

6. **공유 액세스 키에서**연결 문자열의 복사 아이콘을 선택하고 **기본 키를** 선택하고 값을 저장합니다.

    ![연결 문자열을 검색하는 방법 표시](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

IoT Hub 공유 액세스 정책 및 사용 권한에 대한 자세한 내용은 [액세스 제어 및 사용 권한을](./iot-hub-devguide-security.md#access-control-and-permissions)참조하십시오.

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>직접 메서드를 호출하고 디바이스 쌍의 속성을 업데이트하기 위한 작업 예약

이 섹션에서는 직접 메서드를 사용하여 장치에서 원격 **lockDoor를** 시작하고 장치 쌍의 원하는 속성을 업데이트하는 Python 콘솔 앱을 만듭니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-hub** 패키지를 설치합니다.

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. 텍스트 편집기를 사용하여 작업 디렉터리에 새 **scheduleJobService.py** 파일을 만듭니다.

3. `import` **scheduleJobService.py** 파일의 시작 부분에 다음 문과 변수를 추가합니다. `{IoTHubConnectionString}` 자리 표시자를 IoT 허브 연결 문자열 에서 이전에 복사한 [IoT 허브 연결 문자열로](#get-the-iot-hub-connection-string)바꿉꿉입니다. `{deviceId}` [IoT 허브에서 새 장치 등록에 등록한](#register-a-new-device-in-the-iot-hub)장치 ID로 자리 표시자를 교체합니다.

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. 디바이스를 조회하는 데 사용되는 다음 함수를 추가합니다.

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. 직접 메서드와 디바이스 쌍을 호출하는 작업을 실행하는 다음 메서드를 추가합니다.

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. 작업을 예약하고 작업 상태를 업데이트하도록 다음 코드를 추가합니다. `main` 루틴도 포함하세요.

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. **scheduleJobService.py** 파일을 저장하고 닫습니다.

## <a name="run-the-applications"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. 작업 디렉터리의 명령 프롬프트에서 다음 명령을 실행하여 다시 시작 직접 메서드에 대한 수신을 시작합니다.

    ```cmd/sh
    python simDevice.py
    ```

2. 작업 디렉터리의 명령 프롬프트에서 다음 명령을 실행하여 도어를 잠그고 쌍을 업데이트하는 작업을 트리거합니다.
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. 콘솔에서 직접 메서드와 디바이스 쌍 업데이트에 대한 디바이스 응답을 확인합니다.

    ![IoT 허브 작업 샘플 1 -- 장치 출력](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT 허브 작업 샘플 2-- 장치 출력](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스에 대한 직접 메서드를 예약하고 디바이스 쌍의 속성을 업데이트하는 데 작업을 사용했습니다.

IoT Hub 및 air 펌웨어 업데이트를 통해 원격과 같은 장치 관리 패턴을 계속 시작하려면 [펌웨어 업데이트를 수행하는 방법을 참조하세요.](tutorial-firmware-update.md)
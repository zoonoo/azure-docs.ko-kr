---
title: 자습서 - Azure IoT Central에 제네릭 Python 클라이언트 앱 연결 | Microsoft Docs
description: 이 자습서에서는 디바이스 개발자가 Python 클라이언트 앱을 실행하는 디바이스를 Azure IoT Central 애플리케이션에 연결하는 방법을 보여줍니다. 디바이스 기능 모델을 가져와서 디바이스 템플릿을 만들고, 연결된 디바이스와 상호 작용할 수 있는 보기를 추가합니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: tracking-python
ms.openlocfilehash: 5555c176adfb5be78ea73f17bfa01ba87766acc1
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85100391"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>자습서: 클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결(Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*이 문서는 솔루션 빌더 및 디바이스 개발자에게 적용됩니다.*

이 자습서에서는 디바이스 개발자가 Python 클라이언트 애플리케이션을 Azure IoT Central 애플리케이션에 연결하는 방법을 보여줍니다. Python 애플리케이션은 환경 센서 디바이스의 동작을 시뮬레이션합니다. _디바이스 기능 모델_ 샘플을 사용하여 IoT Central에서 _디바이스 템플릿_을 만듭니다. 운영자가 디바이스와 상호 작용할 수 있도록 디바이스 템플릿에 보기를 추가합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 디바이스 기능 모델을 가져와서 디바이스 템플릿을 만듭니다.
> * 기본 및 사용자 지정 보기를 디바이스 템플릿에 추가합니다.
> * 디바이스 템플릿을 게시하고, 실제 디바이스를 IoT Central 애플리케이션에 추가합니다.
> * Python 디바이스 코드를 만들고 실행하며, IoT Central 애플리케이션에 연결되는지 확인합니다.
> * 디바이스에서 보낸 시뮬레이션된 원격 분석 데이터 보기
> * 보기를 사용하여 디바이스 속성을 관리합니다.
> * 디바이스를 제어하는 동기 및 비동기 명령을 호출합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하려면 다음이 필요합니다.

* **사용자 지정 애플리케이션** 템플릿을 사용하여 만든 Azure IoT Central 애플리케이션. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.
* [Python](https://www.python.org/) 버전 3.7 이상이 설치된 개발 머신. 명령줄에서 `python3 --version` 명령을 실행하여 버전을 확인할 수 있습니다. Python은 다양한 운영 체제에 사용할 수 있습니다. 이 자습서의 지침에서는 Windows 명령 프롬프트에서 **python3** 명령을 실행한다고 가정합니다.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Python 애플리케이션 만들기

다음 단계에서는 애플리케이션에 추가한 실제 디바이스에 연결하는 Python 클라이언트 애플리케이션을 만드는 방법을 보여줍니다. Python 애플리케이션은 실제 디바이스의 동작을 시뮬레이션합니다.

1. 명령줄 환경에서 이전에 만든 `environmental-sensor` 폴더로 이동합니다.

1. 필요한 라이브러리를 설치하려면 다음 명령을 실행합니다.

    ```cmd
    pip install azure-iot-device
    ```

1. **environmental_sensor.py**라는 파일을 `environmental-sensor` 폴더에 만듭니다.

1. 다음 `import` 문을 **environmental_sensor.py** 파일의 시작 부분에 추가합니다.

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. 다음 비동기 `main` 함수 및 변수 선언을 파일에 추가합니다.

    ```python
    async def main():
      # In a production environment, don't store
      # connection information in the code.
      provisioning_host = 'global.azure-devices-provisioning.net'
      id_scope = '{your Scope ID}'
      registration_id = '{your Device ID}'
      symmetric_key = '{your Primary Key}'

      delay = 2

      # All the remaining code is nested within this main function

    if __name__ == '__main__':
    asyncio.run(main())
    ```

    `{your Scope ID}`, `{your Device ID}` 및 `{your Primary Key}` 자리 표시자를 이전에 적어 둔 값으로 업데이트합니다. 실제 애플리케이션에서는 애플리케이션에서 이 정보를 하드 코딩하지 마세요.

    다음 함수 정의 및 코드는 모두 `main` 함수 내에 중첩됩니다.

1. 다음 두 함수를 `main` 내부에 추가하여 디바이스를 등록하고 IoT Central 애플리케이션에 연결합니다. 등록 시 다음 Azure Device Provisioning Service가 사용됩니다.

    ```python
      async def register_device():
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
          provisioning_host=provisioning_host,
          registration_id=registration_id,
          id_scope=id_scope,
          symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print(f'Registration result: {registration_result.status}')

        return registration_result

      async def connect_device():
        device_client = None
        try:
          registration_result = await register_device()
          if registration_result.status == 'assigned':
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
              symmetric_key=symmetric_key,
              hostname=registration_result.registration_state.assigned_hub,
              device_id=registration_result.registration_state.device_id,
            )
            # Connect the client.
            await device_client.connect()
            print('Device connected successfully')
        finally:
          return device_client
    ```

1. 원격 분석 데이터를 Azure IoT Central 애플리케이션에 보내려면 다음 함수를 `main` 함수 내부에 추가합니다.

    ```python
      async def send_telemetry():
        print(f'Sending telemetry from the provisioned device every {delay} seconds')
        while True:
          temp = random.randrange(1, 75)
          humid = random.randrange(30, 99)
          payload = json.dumps({'temp': temp, 'humid': humid})
          msg = Message(payload)
          await device_client.send_message(msg, )
          print(f'Sent message: {msg}')
          await asyncio.sleep(delay)
    ```

    원격 분석 항목의 이름(`temp` 및 `humid`)이 디바이스 템플릿에 사용된 이름과 일치해야 합니다.

1. IoT Central 애플리케이션에서 호출한 명령을 처리하려면 다음 함수를 `main` 함수 내부에 추가합니다.

    ```python
      async def blink_command(request):
        print('Received synchronous call to blink')
        response = MethodResponse.create_from_method_request(
          request, status = 200, payload = {'description': f'Blinking LED every {request.payload} seconds'}
        )
        await device_client.send_method_response(response)  # send response
        print(f'Blinking LED every {request.payload} seconds')

      async def diagnostics_command(request):
        print('Starting asynchronous diagnostics run...')
        response = MethodResponse.create_from_method_request(
          request, status = 202
        )
        await device_client.send_method_response(response)  # send response
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Sending property update to confirm command completion')
        await device_client.patch_twin_reported_properties({'rundiagnostics': {'value': f'Diagnostics run complete at {datetime.datetime.today()}.'}})

      async def turnon_command(request):
        print('Turning on the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      async def turnoff_command(request):
        print('Turning off the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      commands = {
        'blink': blink_command,
        'rundiagnostics': diagnostics_command,
        'turnon': turnon_command,
        'turnoff': turnoff_command,
      }

      # Define behavior for handling commands
      async def command_listener():
        while True:
          method_request = await device_client.receive_method_request()  # Wait for commands
          await commands[method_request.name](method_request)
    ```

    명령 이름(`blink`, `turnon`, `turnoff` 및 `rundiagnostics`)은 디바이스 템플릿에 사용된 이름과 일치해야 합니다.

    현재 IoT Central은 디바이스 기능 모델에 정의된 응답 스키마를 사용하지 않습니다. 동기 명령의 경우 유효한 모든 JSON은 응답 페이로드가 될 수 있습니다. 비동기 명령의 경우 디바이스에서 즉시 202 응답을 반환한 후, 작업이 완료되면 reported 속성 업데이트를 실행해야 합니다. reported 속성 업데이트의 형식은 다음과 같습니다.

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    운영자는 명령 기록에서 응답 페이로드를 볼 수 있습니다.

1. IoT Central 애플리케이션에서 보낸 속성 업데이트를 처리하려면 다음 함수를 `main` 함수 내부에 추가합니다.

    ```python
        async def name_setting(value, version):
          await asyncio.sleep(1)
          print(f'Setting name value {value} - {version}')
          await device_client.patch_twin_reported_properties({'name' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        async def brightness_setting(value, version):
          await asyncio.sleep(5)
          print(f'Setting brightness value {value} - {version}')
          await device_client.patch_twin_reported_properties({'brightness' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        settings = {
          'name': name_setting,
          'brightness': brightness_setting
        }

        # define behavior for receiving a twin patch
        async def twin_patch_listener():
          while True:
            patch = await device_client.receive_twin_desired_properties_patch() # blocking
            to_update = patch.keys() & settings.keys()
            await asyncio.gather(
              *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
            )
    ```

    운영자가 IoT Central 애플리케이션에서 쓰기 가능 속성을 설정하면 애플리케이션에서는 디바이스 쌍 desired 속성을 사용하여 디바이스에 값을 보냅니다. 그러면 디바이스는 디바이스 쌍 reported 속성속성을 사용하여 응답합니다. IoT Central은 reported 속성 값을 받으면 속성 보기를 **동기화됨** 상태로 업데이트합니다.

    속성 이름(`name` 및 `brightness`)은 디바이스 템플릿에 사용된 이름과 일치해야 합니다.

1. 애플리케이션을 제어하려면 다음 함수를 `main` 함수 내부에 추가합니다.

    ```python
      # Define behavior for halting the application
      def stdin_listener():
        while True:
          selection = input('Press Q to quit\n')
          if selection == 'Q' or selection == 'q':
            print('Quitting...')
            break

      device_client = await connect_device()

      if device_client is not None and device_client.connected:
        print('Send reported properties on startup')
        await device_client.patch_twin_reported_properties({'state': 'true'})
        tasks = asyncio.gather(
          send_telemetry(),
          command_listener(),
          twin_patch_listener(),
        )

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)

        # Wait for user to indicate they are done listening for method calls
        await user_finished

        # Cancel tasks
        tasks.add_done_callback(lambda r: r.exception())
        tasks.cancel()
        await device_client.disconnect()

      else:
        print('Device could not connect')
    ```

1. **environmental_sensor.py** 파일을 저장합니다.

## <a name="run-your-python-application"></a>Python 애플리케이션 실행

디바이스 클라이언트 애플리케이션을 시작하려면 명령줄 환경에서 다음 명령을 실행합니다.

```cmd
python3 environmental_sensor.py
```

디바이스가 Azure IoT Central 애플리케이션에 연결되어 원격 분석을 보내기 시작하는 것을 볼 수 있습니다.

![클라이언트 애플리케이션 실행](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

다음과 같이 디바이스가 명령 및 속성 업데이트에 응답하는 방식을 확인할 수 있습니다.

![클라이언트 애플리케이션 관찰](media/tutorial-connect-device-python/run-application-2.png)

## <a name="next-steps"></a>다음 단계

디바이스 개발자로서, Python을 사용하여 디바이스를 만드는 방법의 기본 사항을 배웠으므로 이제 다음 단계를 수행하는 것이 좋습니다.

* [Azure IoT Central 애플리케이션에 MXChip IoT DevKit 디바이스 연결](./howto-connect-devkit.md) 방법 문서에서 실제 디바이스를 IoT Central에 연결하는 방법에 대해 알아봅니다.
* 디바이스 코드를 구현할 때 디바이스 템플릿의 역할을 자세히 알아보려면 [디바이스 템플릿이란?](./concepts-device-templates.md)을 참조하세요.
* IoT Central에 디바이스를 등록하는 방법과 IoT Central에서 디바이스 연결을 보호하는 방법에 대한 자세한 내용은 [Azure IoT Central에 연결](./concepts-get-connected.md)을 참조하세요.

IoT Central 자습서 세트를 계속 진행하고 IoT Central 솔루션 빌드에 대해 자세히 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [게이트웨이 디바이스 템플릿 만들기](./tutorial-define-gateway-device-type.md)

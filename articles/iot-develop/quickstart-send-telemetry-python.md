---
title: Azure IoT Central에 디바이스 원격 분석 보내기 빠른 시작(Python)
description: 이 빠른 시작에서는 Python용 Azure IoT Hub 디바이스 SDK를 사용하여 원격 분석을 디바이스에서 IoT Central로 보냅니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: d0dcca0c4be801f385a48afcd41b6a547bab3fbe
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654712"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>빠른 시작: 디바이스에서 Azure IoT Central로 원격 분석 보내기(Python)

**적용 대상**: [디바이스 애플리케이션 개발](about-iot-develop.md#device-application-development)

이 빠른 시작에서는 기본 IoT 디바이스 애플리케이션 개발 워크플로에 대해 알아봅니다. 먼저 Azure IoT Central을 사용하여 클라우드 애플리케이션을 만듭니다. 그런 다음, Azure IoT Python SDK를 사용하여 시뮬레이션된 디바이스를 빌드하고, IoT Central에 연결하고, 디바이스-클라우드 원격 분석을 보냅니다. 

## <a name="prerequisites"></a>필수 구성 요소
- [Python 3.7 이상](https://www.python.org/downloads/). 지원되는 다른 버전의 Python은 [Azure IoT 디바이스 기능](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)을 참조하세요.
    
    Python 버전이 최신 상태인지 확인하려면 `python --version`을 실행합니다. Python 2 및 Python 3이 모두 설치되어 있고 Python 3 환경을 사용하는 경우 `pip3`을 사용하여 모든 라이브러리를 설치합니다. `pip3`을 실행하면 라이브러리가 Python 3 런타임에 설치됩니다.
    > [!IMPORTANT]
    > Python 설치 관리자에서 **PATH에 Python 추가** 옵션을 선택합니다. Python 3.7 이상이 이미 설치되어 있는 경우 Python 설치 폴더를 `PATH` 환경 변수에 추가했는지 확인합니다.

## <a name="create-an-application"></a>애플리케이션 만들기
이 섹션에서는 IoT Central 애플리케이션을 만듭니다. IoT Central은 IoT 솔루션 개발 및 관리에 따른 복잡성과 비용을 줄이는 데 도움이 되는 포털 기반 IoT 애플리케이션 플랫폼입니다.

Azure IoT Central 애플리케이션을 만들려면 다음을 수행합니다.
1. [Azure IoT Central](https://apps.azureiotcentral.com/)로 이동하여 Microsoft 개인, 회사 또는 학교 계정으로 로그인합니다.
1. **빌드** 로 이동하여 **사용자 지정 앱** 을 선택합니다.
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text="IoT Central 시작 페이지":::
1. **애플리케이션 이름** 에서 고유한 이름을 입력하거나 생성된 이름을 사용합니다.
1. **URL** 에서 기억하기 쉬운 애플리케이션 URL 접두사를 입력하거나 생성된 URL 접두사를 사용합니다.
1. **애플리케이션 템플릿** 을 *사용자 지정 애플리케이션* 으로 설정된 상태로 둡니다. 계정에 템플릿이 이미 있는 경우 드롭다운에 다른 옵션이 표시될 수 있습니다.
1. **가격 책정 계획** 옵션을 선택합니다. 
    - 애플리케이션을 7일 동안 무료로 사용하려면 **무료** 를 선택합니다. 만료되기 전에 무료 애플리케이션을 표준 가격 책정으로 변환할 수 있습니다.
    - 필요에 따라 표준 가격 책정 계획을 선택할 수 있습니다. 표준 가격 책정을 선택하면 더 많은 옵션이 표시되며, **디렉터리**, **Azure 구독** 및 **위치** 를 설정해야 합니다. 가격 책정에 대한 자세한 내용은 [Azure IoT Central 가격](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. 
        - **디렉터리** 는 애플리케이션을 만드는 Azure Active Directory입니다. Azure Active Directory에는 사용자 ID, 자격 증명 및 기타 조직 정보가 포함됩니다. Azure Active Directory가 없는 경우 Azure 구독을 만들 때 자동으로 만들어집니다.
        - **Azure 구독** 을 사용하면 Azure 서비스 인스턴스를 만들 수 있습니다. IoT Central은 구독에서 리소스를 프로비저닝합니다. Azure 구독이 없는 경우 [체험 구독을 만들 수 있습니다](https://azure.microsoft.com/free/). 구독이 만들어지면 IoT Central **새 애플리케이션**  페이지로 돌아갑니다. **Azure 구독** 드롭다운에 새 구독이 표시됩니다.
        - **위치** 는 애플리케이션을 만드는 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)입니다. 최적의 성능을 얻으려면 실제로 디바이스에서 가장 가까운 위치를 선택합니다. 위치가 선택되면 애플리케이션을 다른 위치로 이동할 수 없습니다.

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="IoT Central 새 애플리케이션 대화 상자":::
1. **만들기** 를 선택합니다.
    
    IoT Central에서 애플리케이션이 만들어지면 애플리케이션 대시보드로 리디렉션됩니다.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="IoT Central 새 애플리케이션 대시보드":::

## <a name="add-a-device"></a>디바이스 추가
이 섹션에서는 새 디바이스를 IoT Central 애플리케이션에 추가합니다. 디바이스는 애플리케이션에 연결할 실제 디바이스 또는 시뮬레이션된 디바이스를 나타내는 디바이스 템플릿의 인스턴스입니다. 

새 디바이스를 만들려면 다음을 수행합니다.
1. 왼쪽 창에서 **디바이스** 를 선택한 다음, **+ 새로 만들기** 를 선택합니다. 그러면 새 디바이스 대화 상자가 열립니다.
1. **디바이스 템플릿** 을 *할당되지 않음* 으로 설정된 상태로 둡니다.

    > [!NOTE]
    > 간단히 하기 위해 이 빠른 시작에서는 할당되지 않은 템플릿을 사용하는 시뮬레이션된 디바이스를 연결합니다. IoT Central을 계속 사용하여 디바이스를 관리하는 경우 디바이스 템플릿을 사용하는 방법에 대해 알아봅니다. 디바이스 템플릿을 사용하는 방법에 대한 개요는 [빠른 시작: IoT Central 애플리케이션에 시뮬레이션된 디바이스 추가](../iot-central/core/quick-create-simulated-device.md)를 참조하세요.
1. 친숙한 **디바이스 이름** 및 **디바이스 ID** 를 설정합니다. 필요에 따라 생성된 값을 사용합니다.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="IoT Central 새 디바이스 대화 상자":::
1. **만들기** 를 선택합니다.

    **모든 디바이스** 목록에 만든 디바이스가 표시됩니다.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="IoT Central 모든 디바이스 목록 ":::
    
새 디바이스에 대한 연결 세부 정보를 검색하려면 다음을 수행합니다.
1. **모든 디바이스** 목록에서 연결된 디바이스 이름을 두 번 클릭하여 세부 정보를 표시합니다. 
1. 상단 메뉴에서 **연결** 을 선택합니다.

    **디바이스 연결** 대화 상자에 연결 세부 정보가 표시됩니다. :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="IoT Central 디바이스 연결 세부 정보":::
1. **디바이스 연결** 대화 상자에서 다음 값을 안전한 위치에 복사합니다. 이러한 값은 다음 섹션에서 디바이스를 IoT Central에 연결하는 데 사용합니다.
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>메시지 보내기 및 원격 분석 모니터링
이 섹션에서는 Python SDK를 사용하여 시뮬레이션된 디바이스를 빌드하고, 원격 분석을 IoT Central 애플리케이션에 보냅니다. 

1. Windows CMD, PowerShell 또는 Bash(Windows 또는 Linux용)를 사용하여 터미널을 엽니다. 터미널을 사용하여 Python SDK를 설치하고, 환경 변수를 업데이트하고, Python 코드 샘플을 실행합니다.

1. [Azure IoT Python SDK 디바이스 샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)을 로컬 컴퓨터에 복사합니다.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. *azure-iot-sdk-python/azure-iot-device/samples* 디렉터리로 이동합니다.

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Azure IoT Python SDK를 설치합니다.

    ```console
    pip install azure-iot-device
    ```

1. 시뮬레이션된 디바이스에서 IoT Central에 연결할 수 있도록 다음 환경 변수를 각각 설정합니다. `ID_SCOPE`, `DEVICE_ID` 및 `DEVICE_KEY`의 경우 IoT Central *디바이스 연결* 대화 상자에서 저장한 값을 사용합니다.

    **Windows CMD**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > Windows CMD의 경우 연결 문자열 또는 다른 변수 값을 묶는 따옴표가 없습니다.

    **PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash(Linux 또는 Windows)**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. 터미널에서 *simple_send_temperature.py 샘플 파일의 코드를 실행합니다. 이 코드는 시뮬레이션된 IoT 디바이스에 액세스하고, 메시지를 IoT Central에 보냅니다.

    터미널에서 Python 샘플을 실행하려면 다음을 수행합니다.
    ```console
    python ./simple_send_temperature.py
    ```

    필요에 따라 Python IDE의 샘플에서 Python 코드를 실행할 수 있습니다.
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

Python 코드에서 메시지를 디바이스에서 IoT Central 애플리케이션으로 보내면 메시지가 IoT Central의 디바이스에 대한 **원시 데이터** 탭에 표시됩니다. 최근 메시지를 표시하려면 페이지를 새로 고쳐야 할 수 있습니다.

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="IoT Central 원시 데이터 출력의 스크린샷":::

이제 디바이스가 안전하게 연결되어 원격 분석을 Azure IoT에 보내고 있습니다.

## <a name="clean-up-resources"></a>리소스 정리
이 자습서에서 만든 IoT Central 리소스가 더 이상 필요하지 않은 경우 IoT Central 포털에서 해당 리소스를 삭제할 수 있습니다. 필요에 따라 이 가이드의 설명서를 계속 따르려는 경우 만든 애플리케이션을 유지하고 다른 샘플에 다시 사용할 수 있습니다.

Azure IoT Central 샘플 애플리케이션과 모든 해당 디바이스 및 리소스를 제거하려면 다음을 수행합니다.
1. **관리** > **애플리케이션** 을 차례로 선택합니다.
1. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 디바이스를 클라우드에 안전하게 연결하고 디바이스-클라우드 원격 분석을 보내는 데 사용할 수 있는 기본 Azure IoT 애플리케이션 워크플로를 알아보았습니다. Azure IoT Central을 사용하여 애플리케이션 및 디바이스를 만든 다음, Azure IoT Python SDK를 사용하여 시뮬레이션된 디바이스를 만들고 원격 분석을 보냈습니다. 또한 IoT Central을 사용하여 원격 분석을 모니터링했습니다.

다음 단계로 애플리케이션 샘플을 통해 Azure IoT Python SDK를 살펴봅니다.

- [비동기 샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): 이 디렉터리에는 추가 IoT Hub 시나리오에 대한 비동기 Python 샘플이 포함되어 있습니다.
- [동기 샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): 이 디렉터리에는 Python 3.5 이상에 대한 Python 2.7 또는 동기 호환성 시나리오에 사용할 Python 샘플이 포함되어 있습니다.
- [IoT Edge 샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): 이 디렉터리에는 Edge 모듈 및 다운스트림 디바이스를 사용하는 Python 샘플이 포함되어 있습니다.

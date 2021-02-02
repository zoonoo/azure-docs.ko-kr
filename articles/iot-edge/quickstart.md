---
title: '빠른 시작: Windows에서 Azure IoT Edge 디바이스 만들기 | Microsoft Docs'
description: 이 빠른 시작에서는 IoT Edge 디바이스를 만든 다음, Azure Portal에서 원격으로 미리 빌드된 코드를 배포하는 방법을 알아봅니다.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 71e38059aceb7da63f3545610b9acfe48c5d3150
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663216"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>빠른 시작: Windows 디바이스에 첫 번째 IoT Edge 모듈 배포(미리 보기)

컨테이너화된 코드를 Windows IoT Edge 디바이스의 Linux에 배포하여 이 빠른 시작에서 Azure IoT Edge를 사용해보세요. IoT Edge를 사용하면 디바이스에서 코드를 원격으로 관리하여 더 많은 워크로드를 에지로 전송할 수 있습니다. 이 빠른 시작에서는 자체 디바이스를 사용하여 Windows의 Linux용 Azure IoT Edge를 사용하는 것이 얼마나 쉬운지 확인할 것을 권장합니다.

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

* IoT Hub를 만듭니다.
* IoT Edge 디바이스를 IoT Hub에 등록합니다.
* 디바이스에 Windows의 Linux용 IoT Edge 런타임을 설치하고 시작합니다.
* 원격으로 모듈을 IoT Edge 디바이스에 배포하고 원격 분석을 전송합니다.

![다이어그램 - 빠른 시작: 디바이스 및 클라우드의 아키텍처](./media/quickstart/install-edge-full.png)

이 빠른 시작에서는 Windows 디바이스에서 Linux용 Azure IoT Edge를 설정하는 방법에 대해 설명합니다. 그런 다음, Azure Portal에서 디바이스로 모듈을 배포합니다. 이 빠른 시작에 사용되는 모듈은 온도, 습도 및 압력 데이터를 생성하는 시뮬레이션된 센서입니다. 다른 Azure IoT Edge 자습서에서는 비즈니스 인사이트를 위해 시뮬레이션된 데이터를 분석하는 추가 모듈을 배포하는 과정을 설명하므로 여기서 수행하는 작업을 토대로 진행됩니다.

활성 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만드세요.

>[!NOTE]
>Windows의 Linux용 IoT Edge는 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure CLI에 대한 환경을 준비합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

클라우드 리소스:

* 이 빠른 시작에서 사용하는 모든 리소스를 관리하는 리소스 그룹입니다.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge 디바이스:

* 디바이스는 Windows PC 또는 서버 버전 1809 이상이어야 합니다.
* 최소 메모리 4GB, 권장 메모리 8GB
* 10GB의 사용 가능한 디스크 공간

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

Azure CLI를 사용하여 IoT Hub를 만들어서 빠른 시작을 시작합니다.

![다이어그램 - 클라우드에 IoT 허브 만들기](./media/quickstart/create-iot-hub.png)

이 빠른 시작에는 무료 수준의 IoT Hub가 작동합니다. 이전에 IoT Hub를 사용했고 이미 만든 허브가 있으면 해당 IoT 허브를 사용할 수 있습니다.

다음 코드는 리소스 그룹 `IoTEdgeResources`에 체험 **F1** 허브를 만듭니다. `{hub_name}`을 IoT 허브의 고유한 이름으로 바꿉니다. IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   구독에 이미 한 개의 무료 허브가 있기 때문에 오류가 발생하는 경우 SKU를 **S1** 으로 변경합니다. IoT Hub 이름을 사용할 수 없다는 오류가 발생할 경우 다른 사용자에게 해당 이름의 허브가 이미 있는 것입니다. 새 이름을 사용해 보세요.

## <a name="register-an-iot-edge-device"></a>IoT Edge 디바이스 등록

새로 만든 IoT Hub에 IoT Edge 디바이스를 등록합니다.

![다이어그램 - IoT Hub ID로 디바이스 등록](./media/quickstart/register-device.png)

IoT Hub와 통신할 수 있도록, 시뮬레이트된 디바이스의 디바이스 ID를 만듭니다. 디바이스 ID는 클라우드에 있으며, 사용자는 고유한 디바이스 연결 문자열을 사용하여 물리적 디바이스를 디바이스 ID에 연결합니다.

IoT Edge 디바이스는 일반적인 IoT 디바이스와 다르게 작동하며 다른 방식으로 관리될 수 있으므로, `--edge-enabled` 플래그를 사용하여 이 ID를 IoT Edge 디바이스로 선언합니다.

1. Azure Cloud Shell에서 다음 명령을 입력하여 **myEdgeDevice** 라는 디바이스를 허브에 만듭니다.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   iothubowner 정책 키에 대한 오류가 표시될 경우 Cloud Shell에서 최신 버전의 azure-iot 확장이 실행 중인지 확인합니다.

2. IoT Hub에서 물리적 디바이스를 해당 ID에 연결하는 디바이스의 연결 문자열을 확인합니다. 여기에는 IoT 허브 이름, 디바이스 이름 및 둘 간의 연결을 인증하는 공유 키가 포함됩니다.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. JSON 출력에서 `connectionString` 키를 복사하여 저장합니다. 이 값은 디바이스 연결 문자열입니다. 다음 섹션에서 이 연결 문자열을 사용하여 IoT Edge 런타임을 구성할 것입니다.

   ![CLI 출력에서 연결 문자열 검색](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge 런타임 설치 및 시작

디바이스에서 Windows의 Linux용 IoT Edge를 설치하고 디바이스 연결 문자열로 구성합니다.

![다이어그램 - IoT Edge 디바이스에서 런타임 시작](./media/quickstart/start-runtime.png)

1. [Windows Admin Center를 다운로드합니다](https://aka.ms/WACDownloadEFLOW).

1. 설치 마법사에 따라 디바이스에 Windows Admin Center를 설정합니다.

1. Windows Admin Center에서 화면 오른쪽 위에 있는 **설정 톱니 아이콘** 을 선택합니다.

1. 설정 메뉴의 게이트웨이에서 **확장** 을 선택합니다.

1. **피드** 탭을 선택하고 **추가** 를 선택합니다.

1. 텍스트 상자에 https://aka.ms/wac-insiders-feed 를 입력하고 **추가** 를 선택합니다.

1. 피드가 추가되면 **사용 가능한 확장** 탭으로 이동합니다. 확장 목록을 업데이트하는 데 시간이 걸릴 수 있습니다.

1. **사용 가능한 확장** 목록에서 **Azure IoT Edge** 를 선택합니다.

1. 확장 **설치**

1. 확장이 설치되면 화면 왼쪽 상단에서 **Windows Admin Center** 를 선택하여 기본 대시보드 페이지로 이동합니다.

1. Windows Admin Center를 실행하는 PC를 나타내는 로컬 호스트 연결이 표시됩니다.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="스크린샷 - Windows 관리자 시작 페이지":::

1. **추가** 를 선택합니다.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="스크린샷 - Windows 관리자 시작 페이지 추가 단추":::

1. Azure IoT Edge 타일을 찾고 **새로 만들기** 를 선택합니다. 설치 마법사가 시작됩니다.

    :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="스크린샷 - Windows의 Linux용 Azure IoT Edge 타일":::

1. 설치 마법사를 진행하여 EULA에 동의하고 **다음** 을 선택합니다.

    :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="스크린샷 - 마법사 시작":::

1. Microsoft가 서비스 품질을 모니터링하고 유지하는 데 도움이 되는 확장 진단 데이터를 제공하려면 **선택적 진단 데이터** 를 선택하고 **다음: 배포** 를 선택합니다.

    :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="스크린샷 - 진단 데이터":::

1. **대상 디바이스 선택** 화면에서 원하는 대상 디바이스를 선택하여 최소 요구 사항을 충족하는지 확인합니다. 이 빠른 시작에서는 로컬 디바이스에 IoT Edge를 설치하므로 localhost 연결을 선택합니다. 확인되면 **다음** 을 선택하여 계속합니다.

    :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="스크린샷 - 대상 디바이스 선택":::

1. **다음** 을 선택하여 기본 설정을 적용합니다.

1. 배포 화면에는 패키지 다운로드, 패키지 설치, 호스트 구성 및 Linux VM 최종 설정 프로세스가 표시됩니다.  배포가 성공하면 아래와 같이 표시됩니다.

    :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="스크린샷 - 마법사 배포 성공":::

1. **다음: 연결** 을 클릭하여 IoT 허브 인스턴스의 디바이스 ID로 Azure IoT Edge 디바이스를 프로비저닝하는 마지막 단계를 계속합니다.

1. Azure IoT Hub의 디바이스에서 연결 문자열을 복사하여 디바이스 연결 문자열 필드에 붙여넣습니다. 그런 다음, **선택한 방법으로 프로비저닝** 을 선택합니다.

    > [!NOTE]
    > 연결 문자열을 검색하려면 이전 섹션의 3단계인 [IoT Edge 디바이스 등록](#register-an-iot-edge-device)을 참조하세요.

    :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="스크린샷 - 마법사 프로비저닝":::

1. 프로비저닝이 완료되면 **마침** 을 선택하여 완료하고 Windows Admin Center 시작 화면으로 돌아갑니다. 이제 IoT Edge 디바이스로 나열된 디바이스를 볼 수 있습니다.

    :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="스크린샷 - Windows Admin Center Azure IoT Edge 디바이스":::

1. 대시보드를 보려면 Azure IoT Edge 디바이스를 선택합니다. Azure IoT Hub에서 디바이스 쌍의 워크로드가 배포되었는지 확인해야 합니다. **IoT Edge 모듈 목록** 에는 실행 중인 한 개의 모듈(**edgeAgent**)이 표시되어야 하며, **IoT Edge 상태** 는 **활성(실행 중)** 이 표시되어야 합니다.

IoT Edge 디바이스가 구성되었습니다. 클라우드 배포 모듈을 실행할 준비가 완료된 것입니다.

## <a name="deploy-a-module"></a>모듈 배포

클라우드에서 Azure IoT Edge 디바이스를 관리하여 원격 분석 데이터를 IoT Hub로 보내는 모듈을 배포합니다.

![다이어그램 - 클라우드에서 디바이스로 모듈 배포](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>생성된 데이터 보기

이 빠른 시작에서는 새 IoT Edge 디바이스를 만들고 여기에 IoT Edge 런타임을 설치했습니다. 그런 다음, 디바이스 자체를 변경하지 않고도 디바이스에서 실행할 수 있도록 Azure Portal을 사용하여 IoT Edge 모듈을 배포했습니다.

여기서 푸시한 모듈은 나중에 테스트에 사용할 수 있는 샘플 환경 데이터를 생성합니다. 시뮬레이션된 센서는 머신과 머신 주변의 환경을 모니터링합니다. 예를 들어 이 센서가 서버실, 공장 또는 풍력 터빈에 장착될 수 있습니다. 메시지에는 주변 온도 및 습도, 머신 온도 및 압력, 타임스탬프가 포함됩니다. IoT Edge 자습서는 이 모듈에서 만든 데이터를 분석용 테스트 데이터로 사용합니다.

Windows Admin Center에서 명령 셸로 이동하여 클라우드에서 배포된 모듈이 IoT Edge 디바이스에서 실행되고 있는지 확인합니다.

1. 새로 만든 IoT Edge 디바이스에 연결

   :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="스크린샷 - 디바이스 연결":::

2. **개요** 페이지에는 **IoT Edge 모듈 목록** 과 **IoT Edge 상태** 가 표시됩니다. 여기에서 배포된 다양한 모듈과 디바이스 상태를 볼 수 있습니다.  

3. **도구** 에서 **명령 셸** 을 선택합니다. 명령 셸은 자동으로 ssh(보안 셸)를 사용하여 Windows PC에서 Azure IoT Edge 디바이스의 Linux VM에 연결하는 PowerShell 터미널입니다.

   :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="스크린샷 - 명령 셸":::

4. 디바이스에 있는 세 개의 모듈을 확인하려면 다음 **bash 명령** 을 실행합니다.

   ```bash
   sudo iotedge list
   ```

   :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="스크린샷 - 명령 셸 목록":::

5. 온도 센서 모듈에서 클라우드로 전송되는 메시지를 봅니다.

   ```bash
   iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge 명령은 모듈 이름을 참조하는 경우 대/소문자를 구분합니다.

   :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="스크린샷 - 주변 온도 센서":::

[Visual Studio Code용 Azure IoT Hub 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 IoT 허브에 메시지가 들어오는 것을 확인할 수도 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

IoT Edge 자습서로 계속 진행하려면 이 빠른 시작에서 등록하고 설정한 디바이스를 사용할 수 있습니다. 그렇지 않으면 요금이 발생하지 않도록 Azure 리소스를 삭제할 수 있습니다.

새 리소스 그룹에서 가상 머신 및 IoT 허브를 만든 경우 해당 그룹 및 모든 관련 리소스를 삭제할 수 있습니다. 리소스 그룹의 콘텐츠를 한 번 더 확인하여 유지할 내용이 없는지 검토합니다. 전체 그룹을 삭제하지는 않으려는 경우 대신, 개별 리소스를 삭제할 수 있습니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다.

**IoTEdgeResources** 그룹을 제거합니다. 리소스 그룹을 삭제하는 데 몇 분 정도 소요될 수 있습니다.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

리소스 그룹 목록을 확인하여 리소스 그룹이 제거되었는지 확인할 수 있습니다.

```azurecli-interactive
az group list
```

### <a name="clean-removal-of-azure-iot-edge-for-linux-on-windows"></a>Windows의 Linux용 Azure IoT Edge 제거

Windows Admin Center의 대시보드 확장을 통해 Windows의 Linux용 Azure IoT Edge를 IoT Edge 디바이스에서 제거할 수 있습니다.

1. Windows Admin Center에서 Windows의 Linux용 Azure IoT Edge 디바이스 연결에 연결합니다. Azure 대시보드 도구 확장이 로드됩니다.
2. **제거** 를 선택합니다. Windows의 Linux용 Azure IoT Edge가 제거되면 Windows Admin Center는 시작 페이지로 이동하여 목록에서 Azure IoT Edge 디바이스 연결 항목을 제거합니다.

Windows 시스템에서 Azure IoT Edge를 제거하는 또 다른 방법은 IoT Edge 디바이스에서 **시작** > **설정** > **앱** > **Azure IoT Edge** > **제거** 로 이동하는 것입니다. 이렇게 하면 IoT Edge 디바이스에서 Azure IoT Edge가 제거되지만 Windows Admin Center에서는 연결이 유지됩니다. Windows Admin Center는 설정 메뉴에서도 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 IoT Edge 디바이스를 만들고 Azure IoT Edge 클라우드 인터페이스를 사용하여 디바이스에 코드를 배포했습니다. 이제 해당 환경에 대한 원시 데이터를 생성하는 테스트 디바이스가 준비되었습니다.

다음 단계는 비즈니스 논리를 실행하는 IoT Edge를 만들기 시작할 수 있도록 로컬 개발 환경을 설정하는 것입니다.

> [!div class="nextstepaction"]
> [IoT Edge 모듈 개발 시작](tutorial-develop-for-linux.md)

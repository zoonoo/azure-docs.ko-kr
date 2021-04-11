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
ms.openlocfilehash: 5444f6adb9d441cb6253c180cf2d079c1c36316c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562684"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>빠른 시작: Windows 디바이스에 첫 번째 IoT Edge 모듈 배포(미리 보기)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

컨테이너화된 코드를 Windows IoT Edge 디바이스의 Linux에 배포하여 이 빠른 시작에서 Azure IoT Edge를 사용해보세요. IoT Edge를 사용하면 디바이스에서 코드를 원격으로 관리하여 더 많은 워크로드를 에지로 전송할 수 있습니다. 이 빠른 시작에서는 자체 디바이스를 사용하여 Windows의 Linux용 Azure IoT Edge를 사용하는 것이 얼마나 쉬운지 확인할 것을 권장합니다.

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

* IoT Hub를 만듭니다.
* IoT Edge 디바이스를 IoT Hub에 등록합니다.
* 디바이스에 Windows의 Linux용 IoT Edge 런타임을 설치하고 시작합니다.
* 원격으로 모듈을 IoT Edge 디바이스에 배포하고 원격 분석을 전송합니다.

![디바이스와 클라우드에 대한 이 빠른 시작의 아키텍처를 보여주는 다이어그램](./media/quickstart/install-edge-full.png)

이 빠른 시작에서는 Windows 디바이스에서 Linux용 Azure IoT Edge를 설정하는 방법에 대해 설명합니다. 그런 다음, Azure Portal에서 디바이스로 모듈을 배포합니다. 여기서 사용하는 모듈은 온도, 습도 및 압력 데이터를 생성하는 시뮬레이션된 센서입니다. 다른 Azure IoT Edge 자습서는 비즈니스 인사이트를 획득하기 위해 시뮬레이션된 데이터를 분석하는 모듈을 배포하는 과정을 설명하므로 여기서 수행하는 작업을 토대로 진행됩니다.

활성 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만드세요.

>[!NOTE]
>Windows의 Linux용 IoT Edge는 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure CLI에 대한 환경을 준비합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

이 빠른 시작에서 사용할 모든 리소스를 관리하는 클라우드 리소스 그룹을 만듭니다.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge 디바이스가 다음 요구 사항을 충족하는지 확인합니다.

* 버전
  * Windows 10 버전 1809 이상; 17763 이상 빌드
    * Professional, Enterprise, IoT Enterprise
  * Windows Server 2019 빌드 17763 이상

  
* 하드웨어 요구 사항
  * 사용 가능한 최소 메모리: 2GB
  * 사용 가능한 최소 디스크 공간: 10GB


>[!NOTE]
>이 빠른 시작에서는 Windows Admin Center를 사용하여 Windows의 Linux용 IoT Edge 배포판을 만듭니다. PowerShell을 사용할 수도 있습니다. PowerShell을 사용하여 배포판을 만들려면 [Windows 디바이스에 Linux용 Azure IoT Edge 설치 및 프로비저닝](how-to-install-iot-edge-on-windows.md) 방법 가이드의 단계를 따르세요.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

먼저 Azure CLI를 사용하여 IoT 허브를 만듭니다.

![IoT 허브를 만드는 단계를 보여주는 다이어그램](./media/quickstart/create-iot-hub.png)

이 빠른 시작에서는 무료 Azure IoT Hub가 작동합니다. 이전에 IoT Hub를 사용했고 이미 만든 허브가 있으면 해당 IoT 허브를 사용할 수 있습니다.

다음 코드는 리소스 그룹 `IoTEdgeResources`에 체험 **F1** 허브를 만듭니다. `{hub_name}`을 IoT 허브의 고유한 이름으로 바꿉니다. IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

구독에 이미 한 개의 무료 허브가 있기 때문에 오류가 발생하는 경우 SKU를 `S1`으로 변경합니다. IoT 허브 이름을 사용할 수 없다는 오류가 발생할 경우 다른 사용자에게 해당 이름의 허브가 이미 있는 것입니다. 새 이름을 사용해 보세요.

## <a name="register-an-iot-edge-device"></a>IoT Edge 디바이스 등록

새로 만든 IoT Hub에 IoT Edge 디바이스를 등록합니다.

![IoT 허브 ID로 디바이스를 등록하는 단계를 보여주는 다이어그램](./media/quickstart/register-device.png)

IoT Hub와 통신할 수 있도록, 시뮬레이트된 디바이스의 디바이스 ID를 만듭니다. 디바이스 ID는 클라우드에 있으며, 사용자는 고유한 디바이스 연결 문자열을 사용하여 물리적 디바이스를 디바이스 ID에 연결합니다.

IoT Edge 디바이스는 일반적인 IoT 디바이스와 다르게 작동하며 다른 방법으로 관리할 수 있습니다. `--edge-enabled` 플래그를 사용하여 이 ID를 IoT Edge 디바이스용으로 선언합니다.

1. Azure Cloud Shell에서 다음 명령을 입력하여 **myEdgeDevice** 라는 디바이스를 허브에 만듭니다.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     `iothubowner` 정책 키에 대한 오류가 발생할 경우 Cloud Shell에서 최신 버전의 Azure IoT 확장이 실행 중인지 확인합니다.

1. IoT Hub에서 물리적 디바이스를 해당 ID에 연결하는 디바이스의 연결 문자열을 확인합니다. 연결 문자열에는 IoT 허브 이름, 디바이스 이름 및 둘 간의 연결을 인증하는 공유 키가 포함됩니다.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. JSON 출력에서 `connectionString` 키를 복사하여 저장합니다. 이 값은 디바이스 연결 문자열입니다. 다음 섹션에서 이 값을 사용하여 IoT Edge 런타임을 구성할 것입니다.

     ![Cloud Shell의 연결 문자열 출력을 보여주는 스크린샷](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge 런타임 설치 및 시작

디바이스에 Windows의 Linux용 IoT Edge를 설치하고 디바이스 연결 문자열로 디바이스를 구성합니다.

![IoT Edge 런타임을 시작하는 단계를 보여주는 다이어그램](./media/quickstart/start-runtime.png)

1. [Windows Admin Center를 다운로드합니다](https://aka.ms/wacdownload).

1. 설치 마법사의 지시에 따라 디바이스에 Windows Admin Center를 설치합니다.

1. Windows Admin Center를 엽니다.

1. 오른쪽 상단 모서리에서 **설정 기어** 아이콘을 선택한 다음, **확장** 을 선택합니다.

1. **피드** 탭에서 **추가** 를 선택합니다.

1. 텍스트 상자에 `https://aka.ms/wac-insiders-feed`를 입력하고 **추가** 를 선택합니다.

1. 피드가 추가된 후에는 **사용 가능한 확장** 탭으로 이동하여 확장 목록이 업데이트될 때까지 기다립니다.

1. **사용 가능한 확장** 목록에서 **Azure IoT Edge** 를 선택합니다.

1. 확장을 설치합니다.

1. 확장이 설치되면 왼쪽 위 모서리에서 **Windows Admin Center** 를 선택하여 기본 대시보드 페이지로 이동합니다.

     **localhost** 연결은 Windows Admin Center를 실행하는 PC를 나타냅니다.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Windows Admin Start 페이지의 스크린샷":::

1. **추가** 를 선택합니다.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Windows Admin Center에서 [추가] 단추를 선택하는 모습을 보여주는 스크린샷":::

1. Azure IoT Edge 타일에서 **새로 만들기** 를 선택하여 설치 마법사를 시작합니다.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Azure IoT Edge 타일에서 새 배포 만들기를 보여주는 스크린샷":::

1. 설치 마법사를 계속 진행하여 Microsoft 소프트웨어 사용 조건에 동의하고, **다음** 을 선택합니다.

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="[다음]을 선택하여 설치 마법사를 계속 진행하는 스크린샷":::

1. **선택적 진단 데이터** 를 선택하고 **다음: 배포** 로 이동합니다. 이렇게 선택하면 Microsoft에서 서비스 품질을 모니터링하고 유지하는 데 도움이 되는 확장된 진단 데이터가 제공됩니다.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="진단 데이터 옵션을 보여주는 스크린샷":::

1. **대상 디바이스 선택** 화면에서 원하는 대상 디바이스를 선택하여 최소 요구 사항을 충족하는지 확인합니다. 이 빠른 시작에서는 로컬 디바이스에 IoT Edge를 설치하므로 **localhost** 연결을 선택합니다. 대상 디바이스가 요구 사항을 충족하면 **다음** 을 선택하여 계속합니다.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="대상 디바이스 목록을 보여주는 스크린샷":::

1. **다음** 을 선택하여 기본 설정을 수락합니다. 배포 화면에는 패키지 다운로드, 패키지 설치, 호스트 구성 및 Linux VM(가상 머신) 최종 설정 프로세스가 표시됩니다. 배포가 성공하면 다음과 같은 화면이 표시됩니다.

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="성공한 배포의 스크린샷":::

1. 완료되면 **다음: 연결** 을 클릭하여 IoT 허브 인스턴스의 디바이스 ID로 Azure IoT Edge 디바이스를 프로비저닝하는 마지막 단계를 계속합니다.

1. [이 빠른 시작의 앞부분](#register-an-iot-edge-device)에서 복사한 연결 문자열을 **디바이스 연결 문자열** 필드에 붙여넣습니다. 그런 다음, **선택한 방법으로 프로비저닝** 을 선택합니다.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="[디바이스 연결 문자열] 필드의 연결 문자열을 보여주는 스크린샷":::

1. 프로비저닝이 완료되면 **마침** 을 선택하여 완료하고 Windows Admin Center 시작 화면으로 돌아갑니다. 이제 디바이스가 IoT Edge 디바이스로 나열될 것입니다.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Windows Admin Center의 모든 연결을 보여주는 스크린샷":::

1. 대시보드를 보려면 Azure IoT Edge 디바이스를 선택합니다. Azure IoT Hub에서 디바이스 쌍의 워크로드가 배포되었는지 확인해야 합니다. **IoT Edge 모듈 목록** 에는 실행 중인 한 개의 모듈(**edgeAgent**)이 표시되고, **IoT Edge 상태** 는 **활성(실행 중)** 이어야 합니다.

IoT Edge 디바이스가 구성되었습니다. 클라우드 배포 모듈을 실행할 준비가 완료된 것입니다.

## <a name="deploy-a-module"></a>모듈 배포

클라우드에서 Azure IoT Edge 디바이스를 관리하여 원격 분석 데이터를 IoT Hub로 보내는 모듈을 배포합니다.

![모듈을 배포하는 단계를 보여주는 다이어그램](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-the-generated-data"></a>생성된 데이터 보기

이 빠른 시작에서는 새 IoT Edge 디바이스를 만들고 여기에 IoT Edge 런타임을 설치했습니다. 그런 다음, 디바이스 자체를 변경하지 않고도 디바이스에서 실행할 수 있도록 Azure Portal을 사용하여 IoT Edge 모듈을 배포했습니다.

여기서 푸시한 모듈은 나중에 테스트에 사용할 수 있는 샘플 환경 데이터를 생성합니다. 시뮬레이션된 센서는 머신과 머신 주변의 환경을 모니터링합니다. 예를 들어 이 센서가 서버실, 공장 또는 풍력 터빈에 장착될 수 있습니다. 센서에서 보내는 메시지에는 주변 온도 및 습도, 머신 온도 및 압력, 타임스탬프가 포함됩니다. IoT Edge 자습서는 이 모듈에서 만든 데이터를 분석용 테스트 데이터로 사용합니다.

Windows Admin Center의 명령 셸에서, 클라우드에서 배포한 모듈이 IoT Edge 디바이스에서 실행되고 있는지 확인합니다.

1. 새로 만든 IoT Edge 디바이스에 연결합니다.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Windows Admin Center에서 [연결]을 선택하는 모습을 보여주는 스크린샷":::

     **개요** 페이지에 **IoT Edge 모듈 목록** 및 **IoT Edge 상태** 가 표시됩니다. 배포된 모듈과 디바이스 상태를 볼 수 있습니다.  

1. **도구** 에서 **명령 셸** 을 선택합니다. 명령 셸은 자동으로 SSH(보안 셸)를 사용하여 Windows PC에서 Azure IoT Edge 디바이스의 Linux VM에 연결하는 PowerShell 터미널입니다.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="명령 셸을 여는 것을 보여주는 스크린샷":::

1. 디바이스에 있는 세 개의 모듈을 확인하려면 다음 Bash 명령을 실행합니다.

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="명령 셸 IoT Edge 목록 출력을 보여주는 스크린샷":::

1. 온도 센서 모듈에서 클라우드로 전송되는 메시지를 봅니다.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >IoT Edge 명령은 모듈 이름을 참조할 때 대/소문자를 구분합니다.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="모듈에서 클라우드로 전송된 메시지 목록을 보여주는 스크린샷":::

[Visual Studio Code용 Azure IoT Hub 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 메시지가 IoT 허브에 도착하는 것을 확인할 수도 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

IoT Edge 자습서를 계속 진행하려는 경우에는 이 단계를 건너뜁니다. 이 빠른 시작에서 등록하고 설정한 디바이스를 계속 사용할 수 있습니다. 그렇지 않으면 요금이 발생하지 않도록 Azure 리소스를 삭제할 수 있습니다.

새 리소스 그룹에서 가상 머신 및 IoT 허브를 만든 경우 해당 그룹 및 모든 관련 리소스를 삭제할 수 있습니다. 전체 그룹을 삭제하지는 않으려는 경우 대신, 개별 리소스를 삭제할 수 있습니다.

> [!IMPORTANT]
> 리소스 그룹의 콘텐츠를 확인하여 유지할 내용이 없는지 검토합니다. 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다.

다음 명령을 사용하여 **IoTEdgeResources** 그룹을 제거합니다. 삭제하는 데 몇 분 정도 걸릴 수 있습니다.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

이 명령으로 리소스 그룹 목록을 보면 리소스 그룹이 제거되었는지 확인할 수 있습니다.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Windows의 Linux용 Azure IoT Edge 제거

Windows Admin Center의 대시보드 확장을 사용하여 Windows의 Linux용 Azure IoT Edge를 제거할 수 있습니다.

1. Windows Admin Center에서 IoT Edge 디바이스에 연결합니다. Azure 대시보드 도구 확장이 로드됩니다.

1. **제거** 를 선택합니다. Azure IoT Edge가 제거되면 Windows Admin Center는 **시작** 페이지에서 Azure IoT Edge 디바이스 연결 항목을 제거합니다.

>[!Note]
>Windows 시스템에서 Azure IoT Edge를 제거하는 또 다른 방법은 IoT Edge 디바이스에서 **시작** > **설정** > **앱** > **Azure IoT Edge** > **제거** 를 선택하는 것입니다. 이 방법은 IoT Edge 디바이스에서 Azure IoT Edge를 제거하지만, Windows Admin Center에서는 연결이 유지됩니다. 제거를 완료하려면 **설정** 메뉴에서도 Windows Admin Center를 제거합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 IoT Edge 디바이스를 만들고 Azure IoT Edge 클라우드 인터페이스를 사용하여 디바이스에 코드를 배포했습니다. 이제 해당 환경에 대한 원시 데이터를 생성하는 테스트 디바이스가 준비되었습니다.

다음으로 비즈니스 논리를 실행하는 IoT Edge를 만들기 시작할 수 있도록 로컬 개발 환경을 설정하세요.

> [!div class="nextstepaction"]
> [IoT Edge 모듈 개발 시작](tutorial-develop-for-linux.md)

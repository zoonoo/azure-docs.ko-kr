---
title: Azure IoT Edge + Windows 빠른 시작 | Microsoft Docs
description: 시뮬레이트된 에지 장치에서 분석을 실행하여 Azure IoT Edge를 시도합니다.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 08/02/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3b54a326fc648a443897a6e39c823d9c097cf1d3
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626385"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>빠른 시작: Azure Portal에서 Windows 장치(미리 보기)로 첫 번째 IoT Edge 모듈을 배포합니다.

이 빠른 시작에서 Azure IoT Edge 클라우드 인터페이스를 사용하여 사전 빌드된 코드를 IoT Edge 장치에 원격으로 배포합니다. 이 작업을 수행하려면 먼저 Windows 장치를 사용하여 IoT Edge 장치를 시뮬레이션한 다음 모듈을 배포할 수 있습니다.

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

1. IoT Hub를 만듭니다.
2. IoT Edge 장치를 IoT Hub에 등록합니다.
3. 장치에 IoT Edge 런타임을 설치하고 시작합니다.
4. 원격으로 모듈을 IoT Edge 장치에 배포하고 IoT Hub에 원격 분석을 전송합니다.

![자습서 아키텍처][2]

이 빠른 시작에서 배포하는 모듈은 온도, 습도 및 압력 데이터를 생성하는 시뮬레이션된 센서입니다. 다른 Azure IoT Edge 자습서에서는 비즈니스 정보를 위해 시뮬레이션된 데이터를 분석하는 모듈을 배포하는 과정을 설명하므로 여기에서 수행하는 작업을 토대로 진행됩니다. 

>[!NOTE]
>Windows의 IoT Edge 런타임은 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 있습니다.

활성 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][lnk-account]을 만드세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

이 빠른 시작에서는 Azure CLI를 사용하여 많은 단계를 수행하고, Azure IoT에는 추가 기능을 사용할 수 있게 하는 확장이 있습니다. 

Azure IoT 확장을 Cloud Shell 인스턴스에 추가합니다.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>필수 조건

클라우드 리소스: 

* 이 빠른 시작에서 사용하는 모든 리소스를 관리하는 리소스 그룹입니다. 

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus
   ```

IoT Edge 장치: 

* IoT Edge 장치 역할을 하는 Windows 컴퓨터 또는 가상 머신입니다. 지원되는 Windows 버전을 사용하세요.
   * Windows 10 이상
   * Windows Server 2016 이상
* 가상 머신인 경우 [중첩된 가상화][lnk-nested]를 사용하도록 설정하고 최소 2GB 메모리를 할당하세요. 
* [Windows용 Docker][lnk-docker]를 설치하고, 지금 실행 중인지 확인합니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

Azure CLI를 사용하여 IoT Hub를 만들어서 이 빠른 시작을 시작합니다. 

![IoT Hub 만들기][3]

이 빠른 시작에는 무료 수준의 IoT Hub가 작동합니다. 이전에 IoT Hub를 사용했고 이미 만든 체험 허브가 있으면 해당 IoT 허브를 사용할 수 있습니다. 구독마다 하나의 무료 IoT Hub만 가질 수 있습니다. 

다음 코드는 **IoTEdgeResources** 리소스 그룹에서 무료 **F1** 허브를 만듭니다. *{hub_name}* 을 IoT 허브의 고유한 이름으로 바꿉니다.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 
   ```

   구독에 이미 한 개의 무료 허브가 있기 때문에 오류가 발생하는 경우 SKU를 **S1**으로 변경합니다. 

## <a name="register-an-iot-edge-device"></a>IoT Edge 장치 등록

새로 만든 IoT Hub에 IoT Edge 장치를 등록합니다.
![장치 등록][4]

IoT Hub와 통신할 수 있도록, 시뮬레이트된 장치의 장치 ID를 만듭니다. 장치 ID는 클라우드에 있으며, 사용자는 고유한 장치 연결 문자열을 사용하여 물리적 장치를 장치 ID에 연결합니다. 

IoT Edge 장치는 일반적인 IoT 장치와 다르게 작동하며 다른 방식으로 관리될 수 있으므로, 처음부터 IoT Edge 장치로 선언합니다. 

1. Azure Cloud Shell에서 다음 명령을 입력하여 **myEdgeDevice**라는 장치를 허브에 만듭니다.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

1. IoT Hub에서 물리적 장치를 해당 ID에 연결하는 장치에 대한 연결 문자열을 검색합니다. 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

1. 연결 문자열을 복사하고 저장합니다. 다음 섹션에서 이 값을 사용하여 IoT Edge 런타임을 구성할 것입니다. 

## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge 런타임 설치 및 시작

IoT Edge 장치에 Azure IoT Edge 런타임을 설치하고 장치 연결 문자열을 사용하여 구성합니다. 
![장치 등록][5]

IoT Edge 런타임은 모든 IoT Edge 장치에 배포되며, 세 가지 구성 요소가 있습니다. **IoT Edge 보안 디먼**은 Edge 장치가 부팅되고 IoT Edge 에이전트를 시작하여 장치를 부트스트랩할 때마다 시작됩니다. **IoT Edge 에이전트**는 IoT Edge 허브를 포함하여 IoT Edge 장치에서 모듈을 쉽게 배포하고 모니터링할 수 있습니다. **IoT Edge 허브**는 IoT Edge 장치의 모듈 간 통신과 장치와 IoT Hub 간의 통신을 관리합니다. 

런타임을 설치하는 동안 장치 연결 문자열을 요청하라는 메시지가 나타납니다. Azure CLI에서 검색한 문자열을 사용합니다. 이 문자열은 물리적 장치를 Azure의 IoT Edge 장치 ID에 연결합니다. 

이 섹션의 지침은 Linux 컨테이너를 사용하여 IoT Edge 런타임을 구성합니다. Windows 컨테이너를 사용하려는 경우 [Windows 컨테이너를 사용하려면 Windows에 Azure IoT Edge 런타임 설치](how-to-install-iot-edge-windows-with-windows.md)를 참조하세요.

IoT Edge 장치로 작동하도록 준비한 Windows 머신 또는 VM에서 다음 단계를 완료합니다. 

### <a name="download-and-install-the-iot-edge-service"></a>IoT Edge 서비스 다운로드 및 설치

PowerShell을 사용하여 IoT Edge 런타임을 다운로드하여 설치합니다. IoT Hub에서 검색한 장치 연결 문자열을 사용하여 장치를 구성합니다. 

1. IoT Edge 장치에서 관리자 권한으로 PowerShell을 실행합니다.

2. IoT Edge 서비스를 장치에 다운로드하여 설치합니다. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

3. **DeviceConnectionString**을 요청하는 메시지가 표시되면 이전 섹션에서 복사한 문자열을 입력합니다. 연결 문자열 옆에 따옴표를 포함하지 마세요. 

### <a name="view-the-iot-edge-runtime-status"></a>IoT Edge 런타임 상태 보기

런타임이 성공적으로 설치 및 구성되었는지 확인합니다.

1. IoT Edge 서비스의 상태를 확인합니다.

   ```powershell
   Get-Service iotedge
   ```

2. 서비스 문제를 해결해야 할 경우 서비스 로그를 검색합니다. 

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
    -FilterHashtable @{ProviderName= "iotedged";
      LogName = "application"; StartTime = [datetime]::Today} |
    select TimeCreated, Message |
    sort-object @{Expression="TimeCreated";Descending=$false} |
    format-table -autosize -wrap
   ```

3. IoT Edge 장치에서 실행되는 모든 모듈을 봅니다. 서비스를 처음 시작했으므로 **edgeAgent** 모듈이 실행되는 것을 확인해야 합니다. edgeAgent 모듈은 기본적으로 실행되며, 장치에 배포하는 추가 모듈을 설치하고 시작하는 데 도움이 됩니다. 

   ```powershell
   iotedge list
   ```

   ![장치에서 하나의 모듈 보기](./media/quickstart/iotedge-list-1.png)

IoT Edge 장치가 구성되었습니다. 클라우드 배포 모듈을 실행할 준비가 완료된 것입니다. 

## <a name="deploy-a-module"></a>모듈 배포

클라우드에서 Azure IoT Edge 장치를 관리하여 원격 분석 데이터를 IoT Hub로 보낼 모듈을 배포합니다.
![장치 등록][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>생성된 데이터 보기

이 빠른 시작에서는 새 IoT Edge 장치를 만들고 여기에 IoT Edge 런타임을 설치했습니다. 그런 다음 장치 자체를 변경하지 않고도 장치에서 실행할 IoT Edge 모듈을 푸시할 수 있도록 Azure Portal을 사용했습니다. 이 경우 푸시한 모듈에서는 자습서에 대해 사용할 수 있는 환경 데이터를 만듭니다. 

클라우드에서 배포된 모듈을 IoT Edge 장치에서 실행 중인지 확인합니다. 

```powershell
iotedge list
```

   ![장치에서 세 가지 모듈 보기](./media/quickstart/iotedge-list-2.png)

tempSensor 모듈에서 클라우드로 전송되는 메시지를 봅니다. 

```powershell
iotedge logs tempSensor -f
```

  ![모듈의 데이터 보기](./media/quickstart/iotedge-logs.png)

[Visual Studio Code용 Azure IoT Toolkit 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 IoT Hub에서 받는 메시지를 볼 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

IoT Edge 자습서로 계속 진행하려면 이 빠른 시작에서 등록하고 설정한 장치를 사용할 수 있습니다. 그렇지 않으면 만든 Azure 리소스를 삭제하고 장치에서 IoT Edge 런타임을 제거할 수 있습니다. 

### <a name="delete-azure-resources"></a>Azure 리소스 삭제

새 리소스 그룹에서 가상 머신 및 IoT 허브를 만든 경우 해당 그룹 및 모든 관련 리소스를 삭제할 수 있습니다. 유지하려는 모든 해당 리소스 그룹에 있는 경우 정리하려는 개별 리소스를 삭제합니다. 

**IoTEdgeResources** 그룹을 제거합니다. 

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```

### <a name="remove-the-iot-edge-runtime"></a>IoT Edge 런타임 제거

향후 테스트에서 IoT Edge 장치를 사용하려는 경우 tempSensor 모듈이 IoT Hub에 데이터를 보내지 못하게 하려면 다음 명령을 사용하여 IoT Edge 서비스를 중지하고 장치에서 생성된 컨테이너를 삭제합니다. 

   ```powershell
   Stop-Service iotedge -NoWait
   ```

테스트를 다시 시작할 준비가 되면 서비스를 다시 시작할 수 있습니다.

   ```powershell
   Start-Service iotedge
   ```

장치에서 설치를 제거하려면 다음 명령을 사용합니다.  

IoT Edge 런타임을 제거합니다.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

IoT Edge 런타임을 제거하면 만든 컨테이너는 중지되지만 장치에는 계속 남아 있습니다. 모든 컨테이너를 봅니다.

   ```powershell
   docker ps -a
   ```

IoT Edge 런타임에 의해 장치에서 만들어진 컨테이너를 삭제합니다. tempSensor 컨테이너를 다르게 부른 경우 컨테이너의 이름을 변경합니다. 

   ```powershell
   docker rm -f tempSensor
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```
   
## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 IoT Edge 장치를 만들고 Azure IoT Edge 클라우드 인터페이스를 사용하여 장치에 코드를 배포했습니다. 이제 해당 환경에 대한 원시 데이터를 생성하는 테스트 장치가 준비되었습니다. 

Azure IoT Edge가 이러한 데이터를 통해 비즈니스 통찰력을 얻는 데 어떻게 도움을 주는지 알아보려면 다른 자습서를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Function을 사용하여 센서 데이터 필터링](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png


<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-delete


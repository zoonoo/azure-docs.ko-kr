---
title: Azure IoT Edge + Linux 빠른 시작 | Microsoft Docs
description: 이 빠른 시작에서는 미리 작성된 코드를 IoT Edge 장치에 원격으로 배포하는 방법을 알아봅니다.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: dfcb764d75b7328d1234d47d82afdae8d6a0deef
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413017"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>빠른 시작: Linux x64 장치에 첫 번째 IoT Edge 모듈 배포

Azure IoT Edge는 클라우드의 강력한 기능을 사물 인터넷 장치로 옮겨놓습니다. 이 빠른 시작에서는 클라우드 인터페이스를 사용하여 미리 작성된 코드를 IoT Edge 장치에 원격으로 배포하는 방법에 대해 알아봅니다.

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

1. IoT Hub를 만듭니다.
2. IoT Edge 장치를 IoT Hub에 등록합니다.
3. IoT Edge 런타임을 장치에 설치하고 시작합니다.
4. 모듈을 IoT Edge 장치에 원격으로 배포합니다.

![빠른 시작 아키텍처][2]

이 빠른 시작에서는 Linux 컴퓨터 또는 가상 머신을 IoT Edge 장치로 전환합니다. 그런 다음, Azure Portal에서 모듈을 장치에 배포할 수 있습니다. 이 빠른 시작에서 배포하는 모듈은 온도, 습도 및 압력 데이터를 생성하는 시뮬레이션된 센서입니다. 다른 Azure IoT Edge 자습서에서는 비즈니스 정보를 위해 시뮬레이션된 데이터를 분석하는 모듈을 배포하는 과정을 설명하므로 여기에서 수행하는 작업을 토대로 진행됩니다. 

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

* IoT Edge 장치 역할을 하는 Linux 가상 머신입니다. 

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image Canonical:UbuntuServer:16.04-LTS:latest --admin-username azureuser --generate-ssh-keys --size Standard_B1ms
   ```

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

IoT Hub와 통신할 수 있도록, 시뮬레이트된 장치의 장치 ID를 만듭니다. IoT Edge 장치는 일반적인 IoT 장치와 다르게 작동하며 다른 방식으로 관리될 수 있으므로, 처음부터 IoT Edge 장치로 선언합니다. 

1. Azure Cloud Shell에서 다음 명령을 입력하여 **myEdgeDevice**라는 장치를 허브에 만듭니다.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

1. IoT Hub에서 물리적 장치를 해당 ID에 연결하는 장치에 대한 연결 문자열을 검색합니다. 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

1. 연결 문자열을 복사하고 저장합니다. 다음 섹션에서 이 값을 사용하여 IoT Edge 런타임을 구성할 것입니다. 


## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge 런타임 설치 및 시작

장치에 Azure IoT Edge 런타임을 설치하고 시작합니다. 
![장치 등록][5]

IoT Edge 런타임은 모든 IoT Edge 장치에 배포되며, 세 가지 구성 요소가 있습니다. **IoT Edge 보안 디먼**은 Edge 장치가 부팅되고 IoT Edge 에이전트를 시작하여 장치를 부트스트랩할 때마다 시작됩니다. **IoT Edge 에이전트**는 IoT Edge 허브를 포함하여 IoT Edge 장치에서 모듈을 쉽게 배포하고 모니터링할 수 있습니다. **IoT Edge 허브**는 IoT Edge 장치의 모듈 간 통신과 장치와 IoT Hub 간의 통신을 관리합니다. 

이 빠른 시작에 대해 준비된 Linux 컴퓨터 또는 VM에서 다음 단계를 완료합니다. 

### <a name="register-your-device-to-use-the-software-repository"></a>소프트웨어 리포지토리를 사용하도록 장치 등록

IoT Edge 런타임을 실행하는 데 필요한 패키지는 소프트웨어 리포지토리에서 관리됩니다. 이 리포지토리에 액세스할 수 있도록 IoT Edge 장치를 구성합니다. 

이 섹션의 단계는 **Ubuntu 16.04**를 실행하는 장치를 대상으로 합니다. 다른 Linux 버전의 소프트웨어 리포지토리에 액세스하려면 [Linux(x64)에 Azure IoT Edge 런타임 설치](how-to-install-iot-edge-linux.md) 또는 [Linux(ARM32v7/armhf)에 Azure IoT Edge 런타임 설치](how-to-install-iot-edge-linux-arm.md)를 참조하세요.

1. IoT Edge 장치로 사용되는 컴퓨터에서 리포지토리 구성을 설치합니다.

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. 리포지토리에 액세스하려면 공개 키를 설치합니다.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>컨테이너 런타임 설치

IoT Edge 런타임은 일단의 컨테이너이며, IoT Edge 장치에 배포하는 논리는 컨테이너로 패키지됩니다. 컨테이너 런타임을 설치하여 이러한 구성 요소에 맞게 장치를 준비합니다.

**apt-get**을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

컨테이너 런타임인 **Moby**를 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

Moby용 CLI 명령을 설치합니다. 

   ```bash
   sudo apt-get install moby-cli
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>IoT Edge 보안 디먼 설치 및 구성

보안 디먼은 시스템 서비스로 설치되므로 장치를 부팅할 때마다 IoT Edge 런타임이 시작됩니다. 또한 설치에는 **hsmlib** 버전이 포함되어 있어 보안 디먼에서 장치의 하드웨어 보안과 상호 작용할 수 있습니다. 

1. IoT Edge 보안 디먼을 다운로드 및 설치합니다. 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. IoT Edge 구성 파일을 엽니다. 이 파일은 보호된 파일이므로 승격된 권한을 사용하여 액세스해야 할 수 있습니다.
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. IoT Edge 장치 연결 문자열을 추가합니다. 변수 **device_connection_string**을 찾고, 장치를 등록한 후 복사한 문자열을 사용하여 해당 값을 업데이트합니다.

4. 파일을 저장하고 닫습니다. 

   `CTRL + X`, `Y`, `Enter`

4. IoT Edge 보안 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

5. Edge 보안 디먼이 시스템 서비스로 실행되고 있는지 확인합니다.

   ```bash
   sudo systemctl status iotedge
   ```

   ![시스템 서비스로 실행되는 Edge 디먼 확인](./media/quickstart-linux/iotedged-running.png)

   또한 다음 명령을 실행하여 Edge 보안 디먼의 로그를 확인할 수도 있습니다.

   ```bash
   journalctl -u iotedge
   ```

6. 장치에서 실행 중인 모듈을 확인합니다. 

   >[!TIP]
   >먼저 `iotedge` 명령을 실행하려면 *sudo*를 사용해야 합니다. 컴퓨터에서 로그아웃하고 다시 로그인하여 권한을 업데이트한 다음, 상승된 권한 없이 `iotedge` 명령을 실행할 수 있습니다. 

   ```bash
   sudo iotedge list
   ```

   ![장치에서 하나의 모듈 보기](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>모듈 배포

클라우드에서 Azure IoT Edge 장치를 관리하여 원격 분석 데이터를 IoT Hub로 보낼 모듈을 배포합니다.
![장치 등록][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>생성된 데이터 보기

이 빠른 시작에서는 새 IoT Edge 장치를 만들고 여기에 IoT Edge 런타임을 설치했습니다. 그런 다음 장치 자체를 변경하지 않고도 장치에서 실행할 IoT Edge 모듈을 푸시할 수 있도록 Azure Portal을 사용했습니다. 이 경우 푸시한 모듈에서는 자습서에 대해 사용할 수 있는 환경 데이터를 만듭니다. 

시뮬레이션된 장치를 실행 중인 컴퓨터에서 명령 프롬프트를 다시 엽니다. 클라우드에서 배포된 모듈을 IoT Edge 장치에서 실행 중인지 확인합니다.

   ```bash
   sudo iotedge list
   ```
   로그오프 및 로그인 후에는 위의 명령에 *sudo*가 필요하지 않습니다.

   ![장치에서 세 가지 모듈 보기](./media/quickstart-linux/iotedge-list-2.png)

tempSensor 모듈에서 전송되는 메시지를 봅니다.

   ```bash
   sudo iotedge logs tempSensor -f 
   ```
로그오프 및 로그인 후에는 위의 명령에 *sudo*가 필요하지 않습니다.

![모듈의 데이터 보기](./media/quickstart-linux/iotedge-logs.png)

로그에 표시된 마지막 줄이 `Using transport Mqtt_Tcp_Only`인 경우 온도 센서 모듈이 Edge Hub에 연결하기 위해 대기 중일 수 있습니다. 모듈을 종료하고 Edge 에이전트가 다시 시작되도록 합니다. 모듈은 `sudo docker stop tempSensor` 명령으로 종료할 수 있습니다.

또한 [IoT Hub 탐색기 도구][lnk-iothub-explorer] 또는 [Visual Studio Code용 Azure IoT Toolkit 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 장치에서 보내는 원격 분석을 볼 수도 있습니다. 


## <a name="clean-up-resources"></a>리소스 정리

IoT Edge 자습서로 계속 진행하려면 이 빠른 시작에서 등록하고 설정한 장치를 사용할 수 있습니다. 그렇지 않으면 만든 Azure 리소스를 삭제하고 장치에서 IoT Edge 런타임을 제거할 수 있습니다. 

### <a name="delete-azure-resources"></a>Azure 리소스 삭제

새 리소스 그룹에서 가상 머신 및 IoT 허브를 만든 경우 해당 그룹 및 모든 관련 리소스를 삭제할 수 있습니다. 유지하려는 모든 해당 리소스 그룹에 있는 경우 정리하려는 개별 리소스를 삭제합니다. 

**IoTEdgeResources** 그룹을 제거합니다. 

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```

### <a name="remove-the-iot-edge-runtime"></a>IoT Edge 런타임 제거

장치에서 설치를 제거하려면 다음 명령을 사용합니다.  

IoT Edge 런타임을 제거합니다.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

IoT Edge 런타임을 제거하면 만든 컨테이너는 중지되지만 장치에는 계속 남아 있습니다. 모든 컨테이너를 봅니다.

   ```bash
   sudo docker ps -a
   ```

IoT Edge 런타임에 의해 장치에서 만들어진 컨테이너를 삭제합니다. tempSensor 컨테이너를 다르게 부른 경우 컨테이너의 이름을 변경합니다. 

   ```bash
   sudo docker rm -f tempSensor
   sudo docker rm -f edgeHub
   sudo docker rm -f edgeAgent
   ```

컨테이너 런타임을 제거합니다.

   ```bash
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>다음 단계

이 빠른 시작은 모든 IoT Edge 자습서에 대한 필수 구성 요소입니다. 다른 자습서를 계속 진행하면서 Azure IoT Edge가 이러한 데이터를 통해 비즈니스 통찰력을 얻는 데 어떻게 도움을 줄 수 있는지 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Function을 사용하여 센서 데이터 필터링](tutorial-deploy-function.md)



<!-- Images -->
[0]: ./media/quickstart-linux/cloud-shell.png
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png


<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device

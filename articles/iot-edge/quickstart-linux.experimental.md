---
title: Linux에서 Azure IoT Edge 시뮬레이트 | Microsoft Docs
description: 이 빠른 시작에서는 미리 작성된 코드를 IoT Edge 장치에 원격으로 배포하는 방법을 알아봅니다.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6b63c10a8c092d6568f8caf9842f007a5dc9c027
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049165"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>빠른 시작: Linux x64 장치에 첫 번째 IoT Edge 모듈 배포

Azure IoT Edge에서는 클라우드로 모든 데이터를 푸시하는 대신, 사용자 장치에서 분석 및 데이터 처리를 수행할 수 있습니다. IoT Edge 자습서에는 다양한 형식의 모듈을 배포하는 방법이 설명되어 있지만 먼저 테스트할 장치가 필요합니다. 

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

1. IoT Hub를 만듭니다.
2. IoT Edge 장치를 IoT Hub에 등록합니다.
3. IoT Edge 런타임 시작
4. 모듈을 IoT Edge 장치에 원격으로 배포합니다.

![자습서 아키텍처][2]

이 빠른 시작에서는 Linux 컴퓨터 또는 가상 머신을 IoT Edge 장치로 전환합니다. 그런 다음, Azure Portal에서 모듈을 장치에 배포할 수 있습니다. 이 빠른 시작에서 배포하는 모듈은 온도, 습도 및 압력 데이터를 생성하는 시뮬레이션된 센서입니다. 다른 Azure IoT Edge 자습서에서는 비즈니스 정보를 위해 시뮬레이션된 데이터를 분석하는 모듈을 배포하는 과정을 설명하므로 여기에서 수행하는 작업을 토대로 진행됩니다. 

활성 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][lnk-account]을 만드세요.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

Azure Portal에서 IoT Hub를 만들어 빠른 시작을 시작합니다.
![IoT Hub 만들기][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>IoT Edge 장치 등록

새로 만든 IoT Hub에 IoT Edge 장치를 등록합니다.
![장치 등록][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]


## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge 런타임 설치 및 시작

장치에 Azure IoT Edge 런타임을 설치하고 시작합니다. 
![장치 등록][5]

IoT Edge 런타임은 모든 IoT Edge 장치에 배포되며, 세 가지 구성 요소가 있습니다. **IoT Edge 보안 디먼**은 Edge 장치가 부팅되고 IoT Edge 에이전트를 시작하여 장치를 부트스트랩할 때마다 시작됩니다. **IoT Edge 에이전트**는 IoT Edge 허브를 포함하여 IoT Edge 장치에서 모듈을 쉽게 배포하고 모니터링할 수 있습니다. **IoT Edge 허브**는 IoT Edge 장치의 모듈 간 통신과 장치와 IoT Hub 간의 통신을 관리합니다. 

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

Moby 컨테이너 런타임 및 해당 CLI 명령을 설치합니다. 

   ```bash
   sudo apt-get install moby-engine
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

3. 장치를 등록할 때 복사한 IoT Edge 장치 연결 문자열을 추가합니다. 이 빠른 시작의 앞부분에서 복사한 **device_connection_string** 변수의 값을 바꿉니다.

4. Edge 보안 디먼을 다시 시작합니다.

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

   ```bash
   iotedge list
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
   iotedge list
   ```

   ![장치에서 세 가지 모듈 보기](./media/quickstart-linux/iotedge-list-2.png)

tempSensor 모듈에서 전송되는 메시지를 봅니다.

   ```bash
   iotedge logs tempSensor -f 
   ```

![모듈의 데이터 보기](./media/quickstart-linux/iotedge-logs.png)

로그에 표시된 마지막 줄이 `Using transport Mqtt_Tcp_Only`인 경우 온도 센서 모듈이 Edge Hub에 연결하기 위해 대기 중일 수 있습니다. 모듈을 종료하고 Edge 에이전트가 다시 시작되도록 합니다. 모듈은 `sudo docker stop tempSensor` 명령으로 종료할 수 있습니다.

또한 [IoT Hub 탐색기 도구][lnk-iothub-explorer] 또는 [Visual Studio Code용 Azure IoT Toolkit 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 장치에서 보내는 원격 분석을 볼 수도 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

IoT Edge 자습서로 계속 진행하려면 이 빠른 시작에서 등록하고 설정한 장치를 사용할 수 있습니다. 장치에서 설치를 제거하려면 다음 명령을 사용합니다.  

IoT Edge 런타임을 제거합니다.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

장치에서 만들어진 컨테이너를 삭제합니다. 

   ```bash
   sudo docker rm -f $(sudo docker ps -aq)
   ```

컨테이너 런타임을 제거합니다.

   ```bash
   sudo apt-get remove --purge moby
   ```

이 빠른 시작에서 만든 Azure IoT Hub 또는 IoT Edge 장치가 더 이상 필요하지 않은 경우 Azure Portal에서 삭제할 수 있습니다. IoT Hub의 개요 페이지로 이동하여 **삭제**를 선택합니다. 

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 IoT Edge 장치를 만들고 Azure IoT Edge 클라우드 인터페이스를 사용하여 장치에 코드를 배포했습니다. 이제 해당 환경에 대한 원시 데이터를 생성하는 시뮬레이트된 장치가 준비되었습니다. 

이 빠른 시작은 모든 IoT Edge 자습서에 대한 필수 구성 요소입니다. 자습서를 계속 진행하면서 Azure IoT Edge가 이러한 데이터를 통해 비즈니스 통찰력을 얻는 데 어떻게 도움을 줄 수 있는지 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Function을 사용하여 센서 데이터 필터링](tutorial-deploy-function.md)


<!-- Images -->
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

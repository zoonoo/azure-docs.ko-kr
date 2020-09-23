---
title: 공간 분석 컨테이너를 설치 하 고 실행 하는 방법-Computer Vision
titleSuffix: Azure Cognitive Services
description: 공간 분석 컨테이너를 사용 하 여 사람 및 거리를 검색할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 3d419268302ac8fd55559c6af9cd328f22bd2404
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938413"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>공간 분석 컨테이너 (미리 보기)를 설치 하 고 실행 합니다.

공간 분석 컨테이너를 사용 하 여 실시간 스트리밍 비디오를 분석 하 여 사용자, 이동 및 물리적 환경 개체와의 상호 작용 간의 공간 관계를 이해할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 공간 분석 컨테이너를 실행 하려면 만든 리소스의 키와 끝점이 필요 합니다. 키와 끝점은 나중에 사용 합니다.


### <a name="spatial-analysis-container-requirements"></a>공간 분석 컨테이너 요구 사항

공간 분석 컨테이너를 실행 하려면 [NVIDIA Tesla T4 GPU](https://www.nvidia.com/data-center/tesla-t4/)를 사용 하는 계산 장치가 필요 합니다. GPU 가속에 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) 를 사용 하는 것이 좋지만 컨테이너는 최소 요구 사항을 충족 하는 다른 데스크톱 컴퓨터에서 실행 됩니다. 이 장치를 호스트 컴퓨터로 참조 합니다.

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 장치](#tab/azure-stack-edge)

Azure Stack Edge는 네트워크 데이터 전송 기능을 사용 하는 서비스 제공 서비스 솔루션 및 AI 지원에 지 컴퓨팅 장치입니다. 자세한 준비 및 설정 지침은 [Azure Stack Edge 설명서](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)를 참조 하세요.

#### <a name="desktop-machine"></a>[데스크톱 컴퓨터](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>최소 하드웨어 요구 사항

* 4gb 시스템 RAM
* 4gb의 GPU RAM
* 8 코어 CPU
* 1 NVIDIA Tesla T4 GPU
* 20gb의 HDD 공간

#### <a name="recommended-hardware"></a>권장 하드웨어

* 32 GB 시스템 RAM
* 16gb의 GPU RAM
* 8 코어 CPU
* 2 NVIDIA Tesla T4 Gpu
* 50 GB의 SSD 공간

이 문서에서는 다음 소프트웨어 패키지를 다운로드 하 여 설치 합니다. 호스트 컴퓨터에서 다음을 실행할 수 있어야 합니다 (지침은 아래 참조).

* [Nvidia 그래픽 드라이버](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) 및 [Nvidia, da 도구 키트](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* [NVIDIA mp](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) 구성 (다중 프로세스 서비스)
* [DOCKER CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) 및 [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) 런타임.

---

| 요구 사항 | Description |
|--|--|
| 카메라 | 공간 분석 컨테이너는 특정 카메라 브랜드에 연결 되지 않습니다. 카메라 장치는 RTSP (실시간 스트리밍 프로토콜) 및 h.264 인코딩을 지원 하 고, 호스트 컴퓨터에서 액세스할 수 있어야 하며, 15FPS 및 1080p 해상도로 스트리밍할 수 있어야 합니다. |
| Linux OS | [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) 가 호스트 컴퓨터에 설치 되어 있어야 합니다.  |


## <a name="request-access-to-the-spatial-analysis-functionality"></a>공간 분석 기능에 대 한 액세스 요청

[요청 양식을](https://aka.ms/cognitivegate) 작성 하 고 전송 하 여 컨테이너에 대 한 액세스를 요청 합니다. 

이 양식에서는 컨테이너를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. 양식이 제출 되 면 Azure Cognitive Services 팀에서 검토 하 고 의사 결정을 내립니다.

> [!IMPORTANT]
> * 양식에서 Azure 구독 ID와 연결 된 전자 메일 주소를 사용 해야 합니다.
> * 컨테이너를 실행 하는 데 사용 하는 Computer Vision 리소스는 승인 된 Azure 구독 ID로 생성 되어야 합니다.

승인 되 면 문서 뒷부분에 설명 된 대로 MCR (Microsoft Container Registry)에서 다운로드 한 후 컨테이너를 실행할 수 있습니다.

Azure 구독이 승인 되지 않은 경우에는 컨테이너를 실행할 수 없습니다.

## <a name="set-up-the-host-computer"></a>호스트 컴퓨터 설정

호스트 컴퓨터에 대 한 Azure Stack Edge 장치를 사용 하는 것이 좋습니다. 다른 장치를 구성 하는 경우 **데스크톱 컴퓨터** 를 클릭 합니다.

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 장치](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Azure Stack Edge 포털에서 계산 구성 
 
공간 분석은 Azure Stack Edge의 계산 기능을 사용 하 여 AI 솔루션을 실행 합니다. 계산 기능을 사용 하도록 설정 하려면 다음을 확인 합니다. 

* Azure Stack Edge 장치를 [연결 하 여 활성화](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate) 했습니다. 
* 장치에 액세스 하기 위해 PowerShell 5.0 이상을 실행 하는 Windows 클라이언트 시스템이 있습니다.  
* Kubernetes 클러스터를 배포 하려면 [Azure Portal](https://portal.azure.com/)에서 **로컬 UI** 를 통해 Azure Stack Edge 장치를 구성 해야 합니다. 
  1. Azure Stack Edge 장치에서 계산 기능을 사용 하도록 설정 합니다. 계산을 사용 하도록 설정 하려면 장치의 웹 인터페이스에서 **계산** 페이지로 이동 합니다. 
  2. 계산에 사용할 네트워크 인터페이스를 선택한 다음 **사용**을 클릭 합니다. 그러면 해당 네트워크 인터페이스에서 장치에 가상 스위치가 생성 됩니다.
  3. Kubernetes 테스트 노드 IP 주소와 Kubernetes external services IP 주소는 비워 둡니다.
  4. **적용**을 클릭합니다. 이 작업은 약 2 분 정도 걸릴 수 있습니다. 

![컴퓨팅 구성](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>에 지 계산 역할 설정 및 IoT Hub 리소스 만들기

[Azure Portal](https://portal.azure.com/)에서 Azure Stack Edge 리소스로 이동 합니다. **개요** 페이지 또는 탐색 목록에서에 지 계산 **시작** 단추를 클릭 합니다.  **Edge 계산 구성**   타일에서 **구성**을 클릭 합니다. 

![링크](media/spatial-analysis/configure-edge-compute-tile.png)

 **Edge 계산 구성**   페이지에서 기존 IoT Hub를 선택 하거나 새 항목을 만들도록 선택 합니다. 기본적으로 표준 (S1) 가격 책정 계층은 IoT Hub 리소스를 만드는 데 사용 됩니다. 무료 계층 IoT Hub 리소스를 사용 하려면 하나를 만든 다음 선택 합니다. IoT Hub 리소스는 Azure Stack에 지 리소스에서 사용 하는 것과 동일한 구독 및 리소스 그룹을 사용 합니다. 

**만들기**를 클릭합니다. IoT Hub 리소스 생성은 몇 분 정도 걸릴 수 있습니다. IoT Hub 리소스를 만든 후에에 **지 계산 구성** 타일이 새 구성을 표시 하도록 업데이트 됩니다. Edge 계산 역할이 구성 되어 있는지 확인 하려면 **계산 구성**타일에서 구성 **보기** 를 선택   합니다.

Edge 컴퓨팅 역할이 Edge 디바이스에 설정되면 두 가지 디바이스, 즉 IoT 디바이스와 IoT Edge 디바이스가 만들어집니다. 이 두 디바이스는 모두 IoT Hub 리소스에서 볼 수 있습니다. Azure IoT Edge 런타임은 이미 IoT Edge 장치에서 실행 되 고 있습니다.            

> [!NOTE]
> 현재 IoT Edge 장치에는 Linux 플랫폼만 사용할 수 있습니다. Azure Stack Edge 장치에 대 한 문제 해결에 대 한 도움말은 [로깅 및 문제 해결](spatial-analysis-logging.md) 문서를 참조 하세요.

###  <a name="enable-mps-on-azure-stack-edge"></a>Azure Stack Edge에서 MP 사용 

1. 관리자 권한으로 Windows PowerShell 세션을 실행 합니다. 

2. Windows 원격 관리 서비스가 클라이언트에서 실행 되 고 있는지 확인 합니다. PowerShell 터미널에서 다음 명령을 사용 합니다. 
    
    ```powershell
    winrm quickconfig
    ```
    
    방화벽 예외에 대 한 경고가 표시 되는 경우 네트워크 연결 유형을 확인 하 고 [Windows 원격 관리](https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management) 설명서를 참조 하세요.

3. 장치 IP 주소에 변수를 할당 합니다. 
    
    ```powershell
    $ip = "" Replace with the IP address of your device. 
    ```
    
4. 장치의 IP 주소를 클라이언트의 신뢰할 수 있는 호스트 목록에 추가 하려면 다음 명령을 사용 합니다. 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. 장치에서 Windows PowerShell 세션을 시작 합니다. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. 메시지가 표시 되 면 암호를 입력 합니다. 로컬 웹 UI에 로그인 하는 데 사용 되는 것과 동일한 암호를 사용 합니다. 기본 로컬 웹 UI 암호는 `Password1` 입니다.

`Start-HcsGpuMPS`을 입력 하 여 장치에서 mp 서비스를 시작 합니다. 

Azure Stack Edge 장치에 대 한 문제 해결에 대 한 도움말은 [Azure Stack edge 장치 문제 해결](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) 을 참조 하세요. 

#### <a name="desktop-machine"></a>[데스크톱 컴퓨터](#tab/desktop-machine)

호스트 컴퓨터가 Azure Stack Edge 장치가 아닌 경우 다음 지침을 따르세요.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>호스트 컴퓨터에서 NVIDIA verda Toolkit 및 Nvidia 그래픽 드라이버를 설치 합니다.

다음 bash 스크립트를 사용 하 여 필요한 Nvidia 그래픽 드라이버 및 verda Toolkit을 설치 합니다.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

컴퓨터를 다시 부팅 하 고 다음 명령을 실행 합니다.

```bash
nvidia-smi
```

다음 출력이 표시되어야 합니다.

![NVIDIA 드라이버 출력](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>호스트 컴퓨터에 Docker CE 및 nvidia docker2 설치

호스트 컴퓨터에 Docker CE를 설치 합니다.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

*Nvidia-docker-2* 소프트웨어 패키지를 설치 합니다.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>호스트 컴퓨터에서 NVIDIA MP를 사용 하도록 설정

> [!TIP]
> 호스트 컴퓨터의 터미널 창에서 MPS 명령을 실행 합니다. Docker 컨테이너 인스턴스 내에 있지 않습니다.

최상의 성능 및 사용률을 위해 [mp (NVIDIA Multiprocess Service](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf))에 대 한 호스트 컴퓨터의 GPU를 구성 합니다. 호스트 컴퓨터의 터미널 창에서 MPS 명령을 실행 합니다.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>호스트 컴퓨터에서 Azure IoT Edge 구성

공간 분석 컨테이너를 호스트 컴퓨터에 배포 하려면 Standard (S1) 또는 Free (F0) 가격 책정 계층을 사용 하 여 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) 서비스의 인스턴스를 만듭니다. 호스트 컴퓨터가 Azure Stack Edge 인 경우 Azure Stack Edge 리소스에서 사용 하는 것과 동일한 구독 및 리소스 그룹을 사용 합니다.

Azure CLI를 사용 하 여 Azure IoT Hub의 인스턴스를 만듭니다. 해당 하는 경우 매개 변수를 바꿉니다. 또는 [Azure Portal](https://portal.azure.com/)에서 Azure IoT Hub를 만들 수 있습니다.

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "test-resource-group" --location "WestUS"

az iot hub create --name "test-iot-hub-123" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "test-iot-hub-123" --device-id "my-edge-device" --edge-enabled
```

호스트 컴퓨터가 Azure Stack Edge 장치가 아닌 경우 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) 버전 1.0.8를 설치 해야 합니다. 올바른 버전인 Ubuntu Server 18.04를 다운로드 하려면 다음 단계를 수행 합니다.
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
Copy the generated list.

```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Microsoft GPG 공개 키를 설치 합니다.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

장치에서 패키지 목록을 업데이트 합니다.

```bash
sudo apt-get update
```

1.0.8 릴리스를 설치 합니다.

```bash
sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
```

그런 다음 [연결 문자열](https://docs.microsoft.com/azure/iot-edge/how-to-register-device#register-in-the-azure-portal)을 사용 하 여 호스트 컴퓨터를 IoT Hub 인스턴스의 IoT Edge 장치로 등록 합니다.

IoT Edge 장치를 Azure IoT Hub에 연결 해야 합니다. 이전에 만든 IoT Edge 장치에서 연결 문자열을 복사 해야 합니다. 또는 Azure CLI에서 아래 명령을 실행할 수 있습니다.

```bash
az iot hub device-identity show-connection-string --device-id my-edge-device --hub-name test-iot-hub-123
```

호스트 컴퓨터를 편집용으로 엽니다  `/etc/iotedge/config.yaml` . `ADD DEVICE CONNECTION STRING HERE`연결 문자열로 대체 합니다. 파일을 저장하고 닫습니다. 호스트 컴퓨터에서 IoT Edge 서비스를 다시 시작 하려면이 명령을 실행 합니다.

```bash
sudo systemctl restart iotedge
```

[Azure Portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal) 또는 [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli)에서 호스트 컴퓨터의 IoT 모듈로 공간 분석 컨테이너를 배포 합니다. 포털을 사용 하는 경우 이미지 URI를 Azure Container Registry 위치로 설정 합니다. 

다음 단계를 사용 하 여 Azure CLI를 사용 하 여 컨테이너를 배포 합니다.

---

### <a name="iot-deployment-manifest"></a>IoT 배포 매니페스트

여러 호스트 컴퓨터에서 컨테이너 배포를 간소화 하기 위해 배포 매니페스트 파일을 만들어 컨테이너 생성 옵션 및 환경 변수를 지정할 수 있습니다. [GitHub에서 배포 매니페스트의](https://go.microsoft.com/fwlink/?linkid=2142179)예제를 찾을 수 있습니다.

다음 표에서는 IoT Edge 모듈에서 사용 하는 다양 한 환경 변수를 보여 줍니다. 에서 특성을 사용 하 여 위에 연결 된 배포 매니페스트에 설정할 수도 있습니다 `env` `spatialanalysis` .

| 설정 이름 | 값 | 설명|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | 나타납니다 구문 | 로깅 수준에서 두 값 중 하나를 선택 합니다.|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | 수정 안 함|
| ARCHON_PERF_MARKER| false| 성능 로깅에 대해 true로 설정 합니다. 그렇지 않으면 false입니다.| 
| ARCHON_NODES_LOG_LEVEL | 나타납니다 구문 | 로깅 수준에서 두 값 중 하나를 선택 합니다.|
| OMP_WAIT_POLICY | 수동적인 | 수정 안 함|
| QT_X11_NO_MITSHM | 1 | 수정 안 함|
| API_KEY | API 키| Computer Vision 리소스의 Azure Portal에서이 값을 수집 합니다. Azure Portal에서 리소스에 대 한 **키 및 끝점** 섹션에서 찾을 수 있습니다. |
| BILLING_ENDPOINT | 끝점 URI| Computer Vision 리소스의 Azure Portal에서이 값을 수집 합니다. Azure Portal에서 리소스에 대 한 **키 및 끝점** 섹션에서 찾을 수 있습니다.|
| EULA | 수락할 | 컨테이너를 실행 하려면이 값을 *허용* 으로 설정 해야 합니다. |
| 표시가 | 주파수 | 이 값은 호스트 컴퓨터의 출력과 동일 해야 `echo $DISPLAY` 합니다. Azure Stack Edge 장치는 표시 되지 않습니다. 이 설정은 적용 되지 않습니다.|


> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

사용자 고유의 설정 및 선택 작업을 사용 하 여 파일 [ 에서 샘플DeploymentManifest.js](https://go.microsoft.com/fwlink/?linkid=2142179) 를 업데이트 한 후 아래 [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) 명령을 사용 하 여 호스트 컴퓨터에서 컨테이너를 IoT Edge 모듈로 배포할 수 있습니다.

```azurecli
az login
az extension add --name azure-iot
az iot edge deployment create --deployment-id "<deployment name>" --hub-name "<IoT Hub name>" --content DeploymentManifest.json --target-condition "deviceId='<IoT Edge device name>'" -–subscription "<subscriptionId>"
```

|매개 변수  |설명  |
|---------|---------|
| `--deployment-id` | 배포의 새 이름입니다. |
| `--hub-name` | Azure IoT Hub 이름입니다. |
| `--content` | 배포 파일의 이름입니다. |
| `--target-condition` | 호스트 컴퓨터에 대 한 IoT Edge 장치 이름입니다. |
| `-–subscription` | 구독 ID 또는 이름입니다. |

이 명령을 실행 하면 배포가 시작 됩니다. Azure Portal에서 Azure IoT Hub 인스턴스의 페이지로 이동 하 여 배포 상태를 확인 합니다. 상태는 417로 표시 될 수 있습니다. 장치 배포 구성은 장치가 컨테이너 이미지 다운로드를 완료 하 고 실행을 시작할 때까지 *설정 되지 않습니다* .

## <a name="validate-that-the-deployment-is-successful"></a>배포가 성공 했는지 확인

컨테이너가 실행되고 있는지 확인하는 방법은 여러 가지가 있습니다. Azure Portal의 Azure IoT Hub 인스턴스에서 공간 분석 모듈의 **IoT Edge 모듈 설정** 에서 *런타임 상태* 를 찾습니다. *런타임 상태* 에 대 한 **원하는 값** 및 **보고 된 값** 이 *실행*중인지 확인 합니다.

![배포 확인 예](./media/spatial-analysis/deployment-verification.png)

배포가 완료 되 고 컨테이너가 실행 되 면 **호스트 컴퓨터가** Azure IoT Hub으로 이벤트를 보내기 시작 합니다. 버전의 작업을 사용 하는 경우 `.debug` 배포 매니페스트에 구성한 각 카메라에 대 한 시각화 도우미 창이 표시 됩니다. 이제 배포 매니페스트에서 모니터링할 회선과 영역을 정의 하 고 지침에 따라 다시 배포할 수 있습니다. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>공간 분석으로 수행 되는 작업 구성

연결 된 카메라를 사용 하 고 작업을 구성 하는 등의 작업을 구성 하려면 [공간 분석 작업](spatial-analysis-operations.md) 을 사용 하 여 컨테이너를 구성 해야 합니다. 구성 하는 각 카메라 장치에 대해 공간 분석에 대 한 작업은 Azure IoT Hub 인스턴스로 전송 되는 JSON 메시지의 출력 스트림을 생성 합니다.

## <a name="redeploy-or-delete-the-deployment"></a>배포 다시 배포 또는 삭제

배포를 업데이트 해야 하는 경우 이전 배포가 성공적으로 배포 되었는지 확인 하거나 완료 되지 않은 IoT Edge 장치 배포를 삭제 해야 합니다. 그렇지 않으면 해당 배포는 계속 진행 되며 시스템이 잘못 된 상태로 유지 됩니다. Azure Portal 또는 [Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment)를 사용할 수 있습니다.

## <a name="use-the-output-generated-by-the-container"></a>컨테이너에 의해 생성 된 출력 사용

컨테이너에서 생성 된 출력을 사용 하기 시작 하려면 다음 문서를 참조 하세요.

*   선택한 프로그래밍 언어에 대 한 Azure Event Hub SDK를 사용 하 여 Azure IoT Hub 끝점에 연결 하 고 이벤트를 수신 합니다. 자세한 내용은 [기본 제공 끝점에서 장치-클라우드 메시지 읽기](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) 를 참조 하세요. 
*   Azure IoT Hub에 대 한 메시지 라우팅을 설정 하 여 이벤트를 다른 끝점으로 보내거나 Azure Blob Storage에 이벤트를 저장 합니다. 자세한 내용은 [IoT Hub 메시지 라우팅](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) 을 참조 하세요. 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>기록 된 비디오 파일을 사용 하 여 공간 분석 실행

기록 된 비디오 또는 라이브 비디오 모두에서 공간 분석을 사용할 수 있습니다. 기록 된 비디오에 공간 분석을 사용 하려면 비디오 파일을 기록 하 고 mp4 파일로 저장 해 보세요. Azure에서 blob 저장소 계정을 만들거나 기존 저장소 계정을 사용 합니다. 그런 후 Azure Portal에서 다음 blob storage 설정을 업데이트 합니다.
    1. **사용 하지 않도록 설정** 해야 하는 **보안 전환** 변경
    2. **Blob 공용 액세스 허용** 을 **사용** 으로 변경

**컨테이너** 섹션으로 이동 하 고 새 컨테이너를 만들거나 기존 컨테이너를 사용 합니다. 그런 다음 비디오 파일을 컨테이너에 업로드 합니다. 업로드 된 파일에 대 한 파일 설정을 확장 하 고 **SAS 생성**을 선택 합니다. 테스트 기간을 포함 하기에 충분 한 **만료 날짜** 를 설정 해야 합니다. 허용 되는 **프로토콜** 을 *HTTP* (*HTTPS* 는 지원 되지 않음)로 설정 합니다.

**SAS 토큰 및 URL 생성** 을 클릭 하 고 BLOB SAS url을 복사 합니다. 를로 바꾸고 `https` `http` 비디오 재생을 지 원하는 브라우저에서 URL을 테스트 합니다.

`VIDEO_URL` [배포 매니페스트에서](https://go.microsoft.com/fwlink/?linkid=2142179) 모든 그래프에 대해 만든 URL로 대체 합니다. 로 설정 하 `VIDEO_IS_LIVE` `false` 고 공간 분석 컨테이너를 업데이트 된 매니페스트로 다시 배포 합니다. 아래 예제를 참조하세요.

공간 분석 모듈은 비디오 파일 사용을 시작 하 고 계속 해 서 자동으로 재생 됩니다.


```json
"zonecrossing": {
  "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
  "version": 1,
  "enabled": true,
  "parameters": {
      "VIDEO_URL": "Replace http url here",
      "VIDEO_SOURCE_ID": "personcountgraph",
      "VIDEO_IS_LIVE": false,
        "VIDEO_DECODE_GPU_INDEX": 0,
      "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
      "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"threshold\":35.0}]}"
    }
  },

```

## <a name="troubleshooting"></a>문제 해결

컨테이너를 시작 하거나 실행할 때 문제가 발생 하는 경우 일반적인 문제에 대 한 단계는 [원격 분석 및 문제 해결](spatial-analysis-logging.md) 을 참조 하세요. 또한이 문서에는 로그 생성 및 수집 및 시스템 상태 수집에 대 한 정보도 포함 되어 있습니다.

## <a name="billing"></a>결제

공간 분석 컨테이너는 Azure 계정에서 Computer Vision 리소스를 사용 하 여 Azure로 청구 정보를 보냅니다. 공개 미리 보기에서 공간 분석을 사용 하는 것은 현재 무료로 제공 됩니다. 

Azure Cognitive Services 컨테이너는 계량/청구 끝점에 연결 하지 않고 실행할 수 있는 권한이 없습니다. 사용자는 컨테이너가 항상 청구 엔드포인트와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 분석 중인 비디오 또는 이미지와 같은 고객 데이터를 Microsoft에 보내지 않습니다.


## <a name="summary"></a>요약

이 문서에서는 공간 분석 컨테이너를 다운로드, 설치 및 실행 하기 위한 개념과 워크플로를 배웠습니다. 요약하면 다음과 같습니다.

* 공간 분석은 Docker 용 Linux 컨테이너입니다.
* 컨테이너 이미지는 Microsoft Container Registry에서 다운로드 됩니다.
* 컨테이너 이미지는 Azure IoT Edge에서 IoT 모듈로 실행 됩니다.
* 컨테이너를 구성 하 고 호스트 컴퓨터에 배포 하는 방법을 설명 합니다.

## <a name="next-steps"></a>다음 단계

* [사용자를 계산 하는 웹 응용 프로그램 배포](spatial-analysis-web-app.md)
* [공간 분석 작업 구성](spatial-analysis-operations.md)
* [로깅 및 문제 해결](spatial-analysis-logging.md)
* [카메라 배치 가이드](spatial-analysis-camera-placement.md)
* [영역 및 줄 배치 가이드](spatial-analysis-zone-line-placement.md)

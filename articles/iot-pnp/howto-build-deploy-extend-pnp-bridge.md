---
title: IoT 플러그 앤 플레이 브리지를 빌드하고 배포 하는 방법 | Microsoft Docs
description: IoT 플러그 앤 플레이 브리지 구성 요소를 식별 합니다. IoT 장치, 게이트웨이 및 IoT Edge 모듈로 실행 하는 방법에 대해 알아봅니다.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4612e1236af5fbe47db9a3569e2f4da2378017e2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784900"
---
# <a name="build-and-deploy-the-iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지를 빌드하고 배포 합니다.

[Iot 플러그 앤 플레이 브리지](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) 를 사용 하 여 게이트웨이에 연결 된 기존 장치를 iot hub에 연결할 수 있습니다. 브리지를 사용 하 여 IoT 플러그 앤 플레이 인터페이스를 연결 된 장치에 매핑합니다. IoT 플러그 앤 플레이 인터페이스는 장치에서 전송 하는 원격 분석, 장치와 클라우드 간에 동기화 되는 속성 및 장치가 응답 하는 명령을 정의 합니다. Windows 또는 Linux 게이트웨이에서 오픈 소스 브리지 응용 프로그램을 설치 하 고 구성할 수 있습니다. 또한 브리지가 Azure IoT Edge 런타임 모듈로 실행 될 수 있습니다.

이 문서에서는 다음 방법에 대해 자세히 설명 합니다.

- 브리지를 구성 합니다.
- 다양 한 환경에서 브리지를 빌드하고 실행 하는 방법을 설명 합니다.

브리지를 사용 하는 방법을 보여 주는 간단한 예제는 [Linux 또는 Windows에서 실행 되는 IoT 플러그 앤 플레이 bridge 샘플을 IoT Hub에 연결 하는 방법](howto-use-iot-pnp-bridge.md)을 참조 하세요.

이 문서의 지침 및 샘플에서는 [Azure Digital Twins](../digital-twins/overview.md) 및 [IoT 플러그 앤 플레이](overview-iot-plug-and-play.md)에 대 한 기본 지식이 있다고 가정 합니다.

## <a name="general-prerequisites"></a>일반 전제 조건

### <a name="supported-os-platforms"></a>지원 되는 OS 플랫폼

지원 되는 OS 플랫폼 및 버전은 다음과 같습니다.

|플랫폼  |지원되는 버전  |
|---------|---------|
|Windows 10 |     모든 Windows Sku가 지원 됩니다. 예: IoT Enterprise, Server, Desktop, IoT Core. *카메라 상태 모니터링 기능을 위해 20H1 이상 빌드를 권장 합니다. 모든 다른 기능은 모든 Windows 10 빌드에서 사용할 수 있습니다.*  |
|Linux     |Ubuntu 18.04에서 테스트 되 고 지원 되는 다른 배포판의 기능은 테스트 되지 않았습니다.         |
||

### <a name="hardware"></a>하드웨어

- 위의 OS Sku 및 버전을 지 원하는 모든 하드웨어 플랫폼입니다.
- 직렬, USB, Bluetooth 및 카메라 주변 기기와 센서는 기본적으로 지원 됩니다. IoT 플러그 앤 플레이 브리지를 확장 하 여 사용자 지정 주변 장치나 센서를 지원할 수 있습니다.

### <a name="azure-iot-products-and-tools"></a>Azure IoT 제품 및 도구

- **Azure IoT Hub** -장치를에 연결 하려면 azure 구독에서 [azure IoT Hub](../iot-hub/index.yml) 가 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다. IoT hub가 없는 경우 [다음 지침에 따라 새로 만듭니다](../iot-hub/iot-hub-create-using-cli.md). IoT 플러그 앤 플레이 지원은 기본 계층 IoT hub에 포함 되지 않습니다.
- **Azure iot 탐색기** -iot 플러그 앤 플레이 장치와 상호 작용 하려면 azure iot 탐색기 도구를 사용할 수 있습니다. 운영 체제용 [최신 릴리스의 Azure IoT 탐색기를 다운로드하여 설치](./howto-use-iot-explorer.md)합니다. IoT hub에 연결 하 고, 복제 한 **iot-플러그 앤 플레이-브리지** 의 *pnpbridge\docs\schemas* 폴더에서 모델 정의를 검색 하도록 Azure iot 탐색기 도구를 구성 합니다.

## <a name="configure-a-bridge"></a>브리지 구성

브리지를 구성 하는 방법에는 다음 두 가지가 있습니다.

- 브리지가 IoT 장치 또는 게이트웨이에서 고유 하 게 실행 되는 경우 구성 파일을 사용 합니다. 이 시나리오는 Linux 또는 Windows 컴퓨터를 사용할 수 있습니다.
- 브리지가 IoT Edge 런타임에서 IoT Edge 모듈로 실행 되는 경우 모듈 쌍 desired 속성을 사용 합니다. 이 시나리오에서는 IoT Edge 런타임이 Linux 환경에서 호스팅되는 것으로 가정 합니다.

### <a name="configuration-file"></a>구성 파일

플러그 앤 플레이 브리지가 IoT 장치 또는 게이트웨이에서 기본적으로 실행 되는 경우 구성 파일을 사용 하 여 다음을 수행 합니다.

- IoT Central 또는 IoT Hub 연결 정보를 가져옵니다.
- IoT 플러그 앤 플레이 인터페이스가 게시 되는 장치를 구성 합니다.

다음 옵션 중 하나를 사용 하 여 구성 파일을 브리지에 제공 합니다.

- 구성 파일에 대 한 경로를 명령줄 매개 변수로 브리지 실행 파일에 전달 합니다.
- *Pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* 폴더에 있는 기존 *config.js* 파일을 사용 합니다.

[구성 파일 스키마](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json) 는 GitHub 리포지토리에서 사용할 수 있습니다.

> [!TIP]
> VS Code에서 브리지 구성 파일을 편집 하는 경우이 스키마 파일을 사용 하 여 파일의 유효성을 검사할 수 있습니다.

### <a name="iot-edge-module-configuration"></a>IoT Edge 모듈 구성

브리지가 IoT Edge 런타임에서 IoT Edge 모듈로 실행 되 면 구성 파일이 클라우드에서 desired 속성에 대 한 업데이트로 전송 됩니다 `PnpBridgeConfig` . 브리지는 어댑터 및 구성 요소를 구성 하기 전에이 속성 업데이트를 대기 합니다.

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>IoT 장치 또는 게이트웨이에서 브리지를 빌드하고 실행 합니다.

| 플랫폼 | 지원됨 |
| :-----------: | :-----------: |
| Windows |  예 |
| Linux | 예 |

### <a name="prerequisites"></a>필수 구성 요소

이 섹션을 완료 하려면 로컬 컴퓨터에 다음 소프트웨어를 설치 해야 합니다.

- [Visual studio (Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) 와 같은 c + + 컴파일을 지 원하는 개발 환경-visual studio를 설치할 때 NuGet 패키지 관리자 구성 요소와 **c + +를 사용한 데스크톱 개발** 워크 로드를 포함 해야 합니다.
- [Cmake-](https://cmake.org/download/) csa를 설치할 때 **시스템 경로에 C\ca 추가** 옵션을 선택 합니다.
- Windows에서 빌드하는 경우에도 [windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk)를 다운로드 해야 합니다.

### <a name="source-code"></a>소스 코드

[IoT 플러그 앤 플레이 브리지](https://github.com/Azure/iot-plug-and-play-bridge) 리포지토리를 로컬 컴퓨터에 복제 합니다.

```console
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

이전 명령을 실행 하는 데 몇 분 정도 걸릴 것입니다.

> [!NOTE]
> Windows에서 오류가 발생 하 여 문제가 발생 하는 경우 `git submodule update` 다음 명령을 실행 하 여 문제를 해결 하십시오 `git config --system core.longpaths true` ..

### <a name="build-the-bridge-on-windows"></a>Windows에서 브리지 빌드

**VS 2019에 대 한 개발자 명령 프롬프트** 를 열고 복제 한 리포지토리가 포함 된 폴더로 이동 하 여 다음 명령을 실행 합니다.

```console
cd pnpbridge\scripts\windows

build.cmd
```

이전 명령을 실행 하는 데 몇 분 정도 걸릴 것입니다.

필요에 따라 Visual Studio에서 생성 된 *pnpbridge\cmake\ pnpbridge_x86 \ azure_iot_pnp_bridge .sln* 솔루션 파일을 열어 코드를 편집, 다시 빌드 및 디버그할 수 있습니다.

> [!TIP]
> 이 프로젝트는 CMAKE를 사용 하 여 프로젝트 파일을 생성 합니다. 적절 한 CMAKE 파일이 업데이트 되지 않으면 Visual Studio에서 프로젝트에 대 한 모든 수정 내용이 손실 될 수 있습니다.

### <a name="build-the-bridge-on-linux"></a>Linux에서 브리지 빌드

Bash 셸을 사용 하 여 복제 한 리포지토리가 포함 된 폴더로 이동 하 고 다음 명령을 실행 합니다.

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>일반 센서 구성

Windows의 `pnp_bridge_connection_parameters` *iot-plug-and-play-bridge\pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* 폴더 또는 windows의 *iot-플러그 앤 플레이-브리지/pnpbridge/cmake/pnpbridge_linux \ src/pnpbridge/samples/console* 폴더에 있는 파일 *에 대 한config.js* 의 노드에서 다음 매개 변수를 수정 합니다.

연결 문자열을 사용 하 여 IoT hub에 연결 하는 경우:

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> `symmetric_key`값은 연결 문자열의 SAS 키와 일치 해야 합니다.

DPS를 사용 하 여 IoT hub 또는 IoT Central 응용 프로그램에 연결 하는 경우:

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

구성 파일의 나머지 부분을 검토 하 여이 샘플에서 구성 된 인터페이스 구성 요소 및 전역 매개 변수를 확인 합니다.

### <a name="start-the-bridge-in-windows"></a>Windows에서 브리지 시작

명령 프롬프트에서 다음을 실행 하 여 브리지를 시작 합니다.

```console
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> 다른 위치의 구성 파일을 사용 하려면 브리지를 실행할 때 해당 경로를 명령줄 매개 변수로 전달 합니다.
  
> [!TIP]
> 기본 제공 카메라 또는 PC에 연결 된 USB 카메라가 있으면 카메라를 사용 하는 응용 프로그램 (예: 기본 제공 **카메라** 앱)을 시작할 수 있습니다. **카메라** 앱이 실행 되는 경우 브리지 콘솔 출력에는 모니터링 통계가 표시 되 고의 프레임 비율은 IoT hub에 대 한 디지털 쌍 인터페이스를 통해 보고 됩니다.

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>IoT Edge 모듈로 브리지를 빌드하고 실행 합니다.

| 플랫폼 | 지원됨 |
| :-----------: | :-----------: |
| Windows |  아니요 |
| Linux | 예 |

### <a name="prerequisites"></a>필수 구성 요소

이 섹션을 완료 하려면 무료 또는 표준 계층 Azure IoT hub가 필요 합니다. IoT hub를 만드는 방법에 대 한 자세한 내용은 [iot Hub 만들기](../iot-hub/iot-hub-create-through-portal.md)를 참조 하세요.

이 섹션의 단계에서는 Windows 10 컴퓨터에서 다음과 같은 개발 환경을 가정 합니다. 이러한 도구를 사용 하 여 IoT Edge 장치에 IoT Edge 모듈을 빌드하고 배포할 수 있습니다.

- Ubuntu 18.04 LTS를 실행 하는 Linux 용 Windows 하위 시스템 (WSL) 2. 자세한 내용은 [windows 10 용 Windows 하위 시스템 설치 가이드](/windows/wsl/install-win10)를 참조 하세요.
- WSL 2를 사용 하도록 구성 된 Windows 용 Docker 데스크톱. 자세히 알아보려면 [Docker Desktop WSL 2 백 엔드](https://docs.docker.com/docker-for-windows/wsl/)를 참조 하세요.
- 다음 세 가지 확장을 설치 하 여 [Windows 환경에 설치 Visual Studio Code](https://code.visualstudio.com/docs/setup/windows) 합니다.

  - [원격 개발 확장 팩](https://aka.ms/vscode-remote/download/extension) -이 확장을 사용 하면 wsl 2에서 코드를 빌드하고 실행할 수 있습니다.
  - [Visual Studio Code에 대 한 Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) -이 확장은 VS Code의 **Wsl: Ubuntu-18.04** 환경에서 사용 하도록 설정 해야 합니다.
  - [Visual Studio Code 용 Azure IoT 도구](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) -이 확장은 VS Code의 **Wsl: Ubuntu-18.04** 환경에서 사용 하도록 설정 해야 합니다.

- WSL 2 환경에서 다음 명령을 실행 하 여 필요한 빌드 도구를 설치 합니다.

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- Azure 리소스를 관리 하기 위해 WSL 2 환경에 설치 된 [Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest&preserve-view=true) 입니다.

  > [!TIP]
  > 원하는 경우 `az` CLI가 사전 설치 된 [Azure Cloud Shell](https://shell.azure.com/) 에서 명령을 실행할 수 있습니다.

### <a name="create-an-iot-edge-device"></a>IoT Edge 디바이스 만들기

여기에 있는 명령은 Azure 가상 머신에서 실행 되는 IoT Edge 장치를 만듭니다. 가상 머신에서 IoT Edge 장치를 실행 하는 것은 실제 장치에 액세스할 수 없는 경우 배포를 테스트 하는 데 유용 합니다.

IoT hub에서 IoT Edge 장치 등록을 만들려면 WSL 2 환경에서 다음 명령을 실행 합니다. 명령을 사용 하 여 `az login` Azure 구독에 로그인 합니다.

```azurecli
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

IoT Edge runtime이 설치 된 Azure 가상 머신을 만들려면 다음 명령을 실행 합니다. 적절 한 값을 사용 하 여 자리 표시자를 업데이트 합니다.

```azurecli
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

이제 가상 머신에서 실행 중인 IoT Edge 런타임이 있습니다. 다음 명령을 사용 하 여 **$edgeAgent** 및 **$edgeHub** 장치에서 실행 되 고 있는지 확인할 수 있습니다.

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> 이 가상 머신이 실행 되는 동안 요금이 청구 됩니다. 이를 사용 하지 않을 때는 종료 하 고 종료 한 후 제거 해야 합니다.

### <a name="download-the-source-code"></a>소스 코드 다운로드

다음 단계에서는 WSL 2 환경에서 Docker 이미지를 빌드합니다. **Iot 플러그 앤 플레이-브리지** 리포지토리를 복제 하려면 적절 한 폴더의 wsl 2 bash shell에서 다음 명령을 실행 합니다.

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

이전 명령을 실행 하는 데 몇 분 정도 걸릴 것입니다.

### <a name="update-the-dockerfile"></a>*Dockerfile* 업데이트

VS Code 시작 하 고, 명령 팔레트를 열고, *원격 wsl: wsl에서 폴더 열기* 를 입력 한 다음, 복제 된 리포지토리를 포함 하는 *iot-플러그 앤 플레이-브리지* 폴더를 엽니다.

*Pnpbridge\Dockerfile.amd64* 파일을 엽니다. 환경 변수 정의를 다음과 같이 편집 합니다.

```dockerfile
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> 다음 명령을 사용 하 여 장치 연결 문자열을 검색할 수 있습니다. `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

다른 아키텍처에 대 한 샘플 *Dockerfiles* 가 있습니다.

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>IoT 플러그 앤 플레이 bridge 모듈 이미지를 빌드합니다.

VS Code에서 명령 팔레트를 열고 *Docker 레지스트리: DOCKER CLI에 로그인* 을 입력 한 다음 Azure 구독 및 컨테이너 레지스트리를 선택 합니다. 이 명령을 사용 하 여 Docker를 컨테이너 레지스트리에 연결 하 고 모듈 이미지를 업로드할 수 있습니다.

파일 *에서 pnpbridge/module.js* 를 엽니다. 를 `{your container registry name}.azurecr.io/iotpnpbridge` 컨테이너 이미지 리포지토리로, `v1` 버전으로 추가 합니다.

VS Code에서 **탐색기** 보기에서 파일 *의 pnpbridge/module.js* 을 마우스 오른쪽 단추로 클릭 하 고 **빌드 및 푸시 IoT Edge 모듈 이미지** 를 선택한 다음, **amd64** 를 플랫폼으로 선택 합니다.

VS Code의 **Docker** 보기에서 이제 **iotpnpbridge: V1-amd64** module 이미지를 포함 하는 컨테이너 레지스트리의 내용을 찾아볼 수 있습니다.

### <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

IoT Edge 장치는 컨테이너 레지스트리에서 해당 모듈 이미지를 다운로드 합니다. 이 예제에서는 Azure container registry를 사용 합니다.

**브리지-에 지 리소스** 그룹에 Azure container registry를 만듭니다. 그런 다음 컨테이너 레지스트리에 대 한 관리자 액세스를 사용 하도록 설정 하 고 IoT Edge 장치에서 모듈 이미지를 다운로드 하는 데 필요한 자격 증명을 가져옵니다.

```azurecli
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>배포 매니페스트 만들기

IoT Edge 배포 매니페스트는 IoT Edge 장치에 배포할 모듈 및 구성 값을 지정 합니다.

VS Code에서 파일의 *pnpbridge/deployment.template.js* 를 엽니다.

- `registryCredentials`이전 명령의 값으로를 업데이트 합니다. `address`값은와 같습니다 `{your container registry name}.azurecr.io` .
- 의 값을 업데이트 `image` `ModulePnpBridge` 합니다. 모듈 이미지는와 같습니다 `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64` .
- `PnPBridgeConfig`CO2 검색 어댑터를 구성 하려면를 다음 JSON으로 바꿉니다.

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  변경 내용을 저장합니다.

VS Code의 **탐색기** 보기에서 파일 *의 pnpbridge/deployment.template.js* 을 마우스 오른쪽 단추로 클릭 하 **IoT Edge 배포 매니페스트 생성** 을 선택 합니다. 이 명령은 배포 매니페스트에 *pnpbridge/config/deployment.amd64.js* 를 생성 합니다.

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>IoT Edge 장치에 브리지 배포

VS Code에서 명령 팔레트를 열고 *Azure IoT Hub: IoT Hub를 선택한* 다음 구독 및 IoT Hub를 선택 합니다.

VS Code에서 **탐색기** 보기에서 파일 *의 pnpbridge/config/deployment.amd64.js* 를 마우스 오른쪽 단추로 클릭 하 고 **단일 장치에 대 한 배포 만들기** 를 선택 하 고 연결-에 **지-장치** 를 선택 합니다.

장치에서 모듈의 상태를 보려면 다음 명령을 실행 합니다.

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

이제 실행 중인 모듈 목록에 CO2 감지 어댑터를 사용 하도록 구성 된 **ModulePnpBridge** 모듈이 포함 되어 있습니다.

### <a name="tidy-up"></a>정리

Azure 구독에서 가상 컴퓨터 및 컨테이너 레지스트리를 제거 하려면 다음 명령을 실행 합니다.

```azurecli
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>폴더 구조

*pnpbridge\deps\azure-iot-sdk-c-pnp*: c Sdk 용 Azure IOT 장치 sdk를 포함 하는 Git 하위 모듈.

*pnpbridge\scripts*: 스크립트를 빌드합니다.

*pnpbridge\src*: IoT 플러그 앤 플레이 브리지 코어에 대 한 소스 코드입니다.

*pnpbridge\src\adapters*: 다양 한 IoT 플러그 앤 플레이 브리지 어댑터에 대 한 소스 코드입니다.

## <a name="next-steps"></a>다음 단계

IoT 플러그 앤 플레이 브리지에 대 한 자세한 내용을 보려면 [iot 플러그 앤 플레이 브리지](https://github.com/Azure/iot-plug-and-play-bridge) GitHub 리포지토리를 방문 하세요.
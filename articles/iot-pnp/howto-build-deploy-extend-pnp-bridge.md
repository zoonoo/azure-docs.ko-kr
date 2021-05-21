---
title: IoT 플러그 앤 플레이 브리지 빌드 및 배포 방법 | Microsoft Docs
description: IoT 플러그 앤 플레이 브리지 구성 요소를 식별합니다. IoT 디바이스 및 게이트웨이에서 실행하는 방법과 IoT Edge 모듈로 실행하는 방법에 대해 알아봅니다.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c43a49c6b4675e98b557ff73d6f8b6468d74da8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057442"
---
# <a name="build-and-deploy-the-iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지 빌드 및 배포

[IoT 플러그 앤 플레이 브리지](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture)를 사용하여 게이트웨이에 연결된 기존 디바이스를 IoT 허브에 연결할 수 있습니다. 브리지를 사용하여 IoT 플러그 앤 플레이 인터페이스를 연결된 디바이스에 매핑할 수 있습니다. IoT 플러그 앤 플레이 인터페이스는 디바이스가 전송하는 원격 분석 데이터, 디바이스와 클라우드 간에 동기화된 속성, 디바이스가 응답하는 명령을 정의합니다. Windows 또는 Linux 게이트웨이에 오픈 소스 브리지 애플리케이션을 설치하고 구성할 수 있습니다. 또한 브리지가 Azure IoT Edge 런타임 모듈로 실행될 수 있습니다.

이 문서에서는 다음 방법을 자세히 설명합니다.

- 브리지 구성
- 다양한 환경에서 브리지를 빌드하고 실행하는 방법

브리지를 사용하는 방법을 보여 주는 간단한 예제는 [Linux 또는 Windows에서 실행되는 IoT 플러그 앤 플레이 브리지 샘플을 IoT Hub에 연결하는 방법](howto-use-iot-pnp-bridge.md)을 참조하세요.

이 문서의 지침 및 샘플에서는 [Azure Digital Twins](../digital-twins/overview.md) 및 [IoT 플러그 앤 플레이](overview-iot-plug-and-play.md)에 대한 기본적인 이해가 있다고 가정합니다.

## <a name="general-prerequisites"></a>일반 전제 조건

### <a name="supported-os-platforms"></a>지원되는 OS 플랫폼

지원되는 OS 플랫폼 및 버전은 다음과 같습니다.

|플랫폼  |지원되는 버전  |
|---------|---------|
|Windows 10 |     모든 Windows SKU가 지원됩니다. 예: IoT Enterprise, Server, Desktop, IoT Core. *카메라 상태 모니터링 기능을 위해 20H1 이상의 빌드를 권장합니다. 다른 모든 기능은 모든 Windows 10 빌드에서 사용할 수 있습니다.*  |
|Linux     |Ubuntu 18.04에서 테스트되고 지원되었으며, 다른 배포판의 기능은 테스트되지 않았습니다.         |
||

### <a name="hardware"></a>하드웨어

- 위의 OS SKU 및 버전을 지원하는 모든 하드웨어 플랫폼입니다.
- 직렬, USB, Bluetooth 및 카메라 주변 장치와 센서는 기본적으로 지원됩니다. IoT 플러그 앤 플레이 브리지를 확장하여 사용자 지정 주변 장치나 센서를 지원할 수 있습니다.

### <a name="azure-iot-products-and-tools"></a>Azure IoT 제품 및 도구

- **Azure IoT Hub** - 디바이스를 연결하려면 Azure 구독에서 [Azure IoT 허브](../iot-hub/index.yml)가 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다. IoT 허브가 없는 경우 [다음 지침에 따라 하나를 만듭니다](../iot-hub/iot-hub-create-using-cli.md). IoT 플러그 앤 플레이 지원은 기본 계층 IoT 허브에 포함되지 않습니다.
- **Azure IoT Explorer** - IoT 플러그 앤 플레이 디바이스와 상호 작용하려면 Azure IoT Explorer 도구를 사용할 수 있습니다. 운영 체제용 [최신 릴리스의 Azure IoT 탐색기를 다운로드하여 설치](./howto-use-iot-explorer.md)합니다. IoT 허브에 연결하고, 복제한 **iot-plug-and-play-bridge** 의 *pnpbridge\docs\schemas* 폴더에서 모델 정의를 검색하도록 Azure IoT Explorer 도구를 구성합니다.

## <a name="configure-a-bridge"></a>브리지 구성

브리지를 구성하는 방법은 다음과 같이 두 가지입니다.

- 브리지가 기본적으로 IoT 디바이스 또는 게이트웨이에서 실행되는 경우에는 구성 파일을 사용합니다. 이 시나리오는 Linux 또는 Windows 컴퓨터를 사용할 수 있습니다.
- 브리지가 IoT Edge 런타임에서 IoT Edge 모듈로 실행되는 경우에는 모듈 쌍 desired 속성을 사용합니다. 이 시나리오에서는 IoT Edge 런타임이 Linux 환경에서 호스팅되는 것으로 가정합니다.

### <a name="configuration-file"></a>구성 파일

플러그 앤 플레이 브리지가 기본적으로 IoT 디바이스 또는 게이트웨이에서 실행되는 경우 구성 파일을 사용하여 다음을 수행합니다.

- IoT Central 또는 IoT Hub 연결 정보를 가져옵니다.
- IoT 플러그 앤 플레이 인터페이스를 게시할 디바이스를 구성합니다.

다음 옵션 중 하나를 사용하여 구성 파일을 브리지에 제공합니다.

- 구성 파일에 대한 경로를 명령줄 매개 변수로 브리지 실행 파일에 전달합니다.
- *pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console* 폴더에 있는 기존 *config.json* 파일을 사용합니다.

[구성 파일 스키마](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json)는 GitHub 리포지토리에서 사용할 수 있습니다.

> [!TIP]
> VS Code에서 브리지 구성 파일을 편집하는 경우 이 스키마 파일을 사용하여 파일의 유효성을 검사할 수 있습니다.

### <a name="iot-edge-module-configuration"></a>IoT Edge 모듈 구성

브리지가 IoT Edge 런타임에서 IoT Edge 모듈로 실행되면 구성 파일이 클라우드에서 `PnpBridgeConfig` desired 속성에 업데이트로 전송됩니다. 브리지는 어댑터 및 구성 요소를 구성하기 전에 이 속성 업데이트를 기다립니다.

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>IoT 디바이스 또는 게이트웨이에서 브리지를 빌드하고 실행합니다.

| 플랫폼 | 지원됨 |
| :-----------: | :-----------: |
| Windows |  예 |
| Linux | 예 |

### <a name="prerequisites"></a>필수 구성 요소

이 섹션을 완료하려면 로컬 머신에 다음 소프트웨어를 설치해야 합니다.

- [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) 등의 C++ 컴파일을 지원하는 개발 환경 - Visual Studio를 설치할 때 NuGet 패키지 관리자 구성 요소와 **C++를 사용한 데스크톱 개발** 워크로드를 포함해야 합니다.
- [CMake](https://cmake.org/download/) - CMake를 설치할 때 **시스템 경로에 CMake 추가** 옵션을 선택합니다.
- Windows에서 빌드하는 경우에도 [Windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk)를 다운로드해야 합니다.

### <a name="source-code"></a>소스 코드

[IoT 플러그 앤 플레이 브리지](https://github.com/Azure/iot-plug-and-play-bridge) 리포지토리를 로컬 컴퓨터에 복제합니다.

```console
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

이전 명령은 실행하는 데 몇 분 정도 걸릴 수 있습니다.

> [!NOTE]
> Windows에서 `git submodule update` 실패로 문제가 발생하는 경우 `git config --system core.longpaths true` 명령을 실행하여 문제를 해결하세요.

### <a name="build-the-bridge-on-windows"></a>Windows에서 브리지 빌드

**VS 2019용 개발자 명령 프롬프트** 를 열고 복제한 리포지토리가 포함된 폴더로 이동하여 다음 명령을 실행합니다.

```console
cd pnpbridge\scripts\windows

build.cmd
```

이전 명령은 실행하는 데 몇 분 정도 걸릴 수 있습니다.

필요에 따라 Visual Studio에서 생성된 *pnpbridge\cmake\pnpbridge_x86\azure_iot_pnp_bridge.sln* 솔루션 파일을 열어 코드를 편집, 다시 빌드 및 디버그할 수 있습니다.

> [!TIP]
> 이 프로젝트는 CMAKE를 사용하여 프로젝트 파일을 생성합니다. 적절한 CMAKE 파일이 업데이트되지 않으면 Visual Studio에서 프로젝트에 대해 수행한 모든 수정 내용이 손실될 수 있습니다.

### <a name="build-the-bridge-on-linux"></a>Linux에서 브리지 빌드

bash 셸을 사용하여 복제한 리포지토리가 포함된 폴더로 이동하고 다음 명령을 실행합니다.

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>일반 센서 구성

Windows의 *iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console* 폴더 또는 Windows의 *iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_linux\src/pnpbridge/samples/console* 폴더에 있는 *config.json* 파일의 `pnp_bridge_connection_parameters` 노드에서 다음 매개 변수를 수정합니다.

연결 문자열을 사용하여 IoT 허브에 연결하는 경우:

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
> `symmetric_key` 값은 연결 문자열의 SAS 키와 일치해야 합니다.

DPS를 사용하여 IoT 허브 또는 IoT Central 애플리케이션에 연결하는 경우:

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

구성 파일의 나머지 부분을 검토하여 이 샘플에서 구성된 인터페이스 구성 요소 및 전역 매개 변수를 확인합니다.

### <a name="start-the-bridge-in-windows"></a>Windows에서 브리지 시작

명령 프롬프트에서 다음을 실행하여 브리지를 시작합니다.

```console
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> 다른 위치의 구성 파일을 사용하려면 브리지를 실행할 때 해당 경로를 명령줄 매개 변수로 전달합니다.
  
> [!TIP]
> 기본 제공 카메라 또는 PC에 연결된 USB 카메라가 있으면 카메라를 사용하는 애플리케이션(예: 기본 제공 **카메라** 앱)을 시작할 수 있습니다. **카메라** 앱이 실행되는 경우 브리지 콘솔 출력에는 모니터링 통계가 표시되고 프레임 비율은 IoT 허브에 대한 디지털 트윈 인터페이스를 통해 보고됩니다.

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>IoT Edge 모듈로 브리지 빌드 및 실행

| 플랫폼 | 지원됨 |
| :-----------: | :-----------: |
| Windows |  아니요 |
| Linux | 예 |

### <a name="prerequisites"></a>필수 구성 요소

이 섹션을 완료하려면 무료 또는 표준 계층 Azure IoT 허브가 필요합니다. IoT 허브를 만드는 방법에 대한 자세한 내용은 [IoT 허브 만들기](../iot-hub/iot-hub-create-through-portal.md)를 참조하세요.

이 섹션의 단계에서는 Windows 10 컴퓨터에 다음과 같은 개발 환경이 있다고 가정합니다. 이러한 도구를 사용하여 IoT Edge 모듈을 빌드하고 IoT Edge 디바이스에 배포할 수 있습니다.

- Ubuntu 18.04 LTS를 실행하는 Linux용 Windows 하위 시스템(WSL) 2. 자세한 내용은 [Windows 10을 위한 Linux용 Windows 하위 시스템 설치 가이드](/windows/wsl/install-win10)를 참조하세요.
- WSL 2를 사용하도록 구성된 Windows용 Docker Desktop. 자세한 내용은 [Docker Desktop WSL 2 백엔드](https://docs.docker.com/docker-for-windows/wsl/)를 참조하세요.
- 다음 세 가지 확장과 함께 [Windows 환경에 설치된 Visual Studio Code](https://code.visualstudio.com/docs/setup/windows):

  - [원격 개발 확장 팩](https://aka.ms/vscode-remote/download/extension) - 이 확장을 사용하면 WSL 2에서 코드를 빌드하고 실행할 수 있습니다.
  - [Visual Studio Code용 Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) - 이 확장은 VS Code의 **WSL: Ubuntu-18.04** 환경에서 사용하도록 설정되어야 합니다.
  - [Visual Studio Code용 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) - 이 확장은 VS Code의 **WSL: Ubuntu-18.04** 환경에서 사용하도록 설정되어야 합니다.

- WSL 2 환경에서 다음 명령을 실행하여 필요한 빌드 도구를 설치합니다.

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- Azure 리소스를 관리하기 위해 WSL 2 환경에 설치된 [Azure CLI](/cli/azure/install-azure-cli-apt)

  > [!TIP]
  > 원하는 경우 CLI가 사전 설치된 [Azure Cloud Shell](https://shell.azure.com/)에서 `az` 명령을 실행할 수 있습니다.

### <a name="create-an-iot-edge-device"></a>IoT Edge 디바이스 만들기

여기에 있는 명령은 Azure 가상 머신에서 실행되는 IoT Edge 디바이스를 만듭니다. 가상 머신에서 IoT Edge 디바이스를 실행하는 것은 실제 디바이스에 액세스할 수 없는 경우 배포를 테스트하는 데 유용합니다.

IoT 허브에서 IoT Edge 디바이스 등록을 만들려면 WSL 2 환경에서 다음 명령을 실행합니다. Azure 구독에 로그인하려면 `az login` 명령을 사용합니다.

```azurecli
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

IoT Edge 런타임이 설치된 Azure 가상 머신을 만들려면 다음 명령을 실행합니다. 적절한 값으로 자리 표시자를 업데이트합니다.

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

이제 IoT Edge 런타임이 가상 머신에서 실행되고 있습니다. 다음 명령을 사용하여 **$edgeAgent** 및 **$edgeHub** 가 디바이스에서 실행되고 있는지 확인할 수 있습니다.

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> 이 가상 머신이 실행되는 동안 요금이 청구됩니다. 가상 머신을 사용하지 않을 때는 종료해야 하며 가상 머신을 사용하는 작업이 끝나면 제거해야 합니다.

### <a name="download-the-source-code"></a>소스 코드 다운로드

다음 단계에서는 WSL 2 환경에서 Docker 이미지를 빌드합니다. **iot-plug-and-play-bridge** 리포지토리를 복제하려면 적절한 폴더의 WSL 2 bash 셸에서 다음 명령을 실행합니다.

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

이전 명령은 실행하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="update-the-dockerfile"></a>*Dockerfile* 업데이트

VS Code를 시작하고, 명령 팔레트를 열고, *Remote WSL: Open folder in WSL* 을 입력한 다음, 복제된 리포지토리가 포함된 *iot-plug-and-play-bridge* 폴더를 엽니다.

*pnpbridge\Dockerfile.amd64* 파일을 엽니다. 환경 변수 정의를 다음과 같이 편집합니다.

```dockerfile
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> 다음 명령을 사용하여 디바이스 연결 문자열을 검색할 수 있습니다. `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

다른 아키텍처에 대한 샘플 *Dockerfiles* 가 있습니다.

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>IoT 플러그 앤 플레이 브리지 모듈 이미지 빌드

VS Code에서 명령 팔레트를 열고 *Docker Registries: Log In to Docker CLI* 를 입력한 다음, Azure 구독 및 컨테이너 레지스트리를 선택합니다. 이 명령을 사용하여 Docker를 컨테이너 레지스트리에 연결하고 모듈 이미지를 업로드할 수 있습니다.

*pnpbridge/module.json* 파일을 엽니다. `{your container registry name}.azurecr.io/iotpnpbridge`를 컨테이너 이미지 리포지토리로 추가하고, `v1`을 버전으로 추가합니다.

VS Code의 **탐색기** 보기에서 *pnpbridge/module.json* 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 모듈 이미지 빌드 및 푸시** 를 선택한 다음, **amd64** 를 플랫폼으로 선택합니다.

이제 VS Code의 **Docker** 보기에서 **iotpnpbridge:V1-amd64** 모듈 이미지를 포함하는 컨테이너 레지스트리의 내용을 찾아볼 수 있습니다.

### <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

IoT Edge 디바이스는 컨테이너 레지스트리에서 해당 모듈 이미지를 다운로드합니다. 이 예에서는 Azure 컨테이너 레지스트리를 사용합니다.

**bridge-edge-resources** 리소스 그룹에 Azure 컨테이너 레지스트리를 만듭니다. 그런 다음, 컨테이너 레지스트리에 대한 관리자 액세스 권한을 사용하도록 설정하고 IoT Edge 디바이스가 모듈 이미지를 다운로드하는 데 필요한 자격 증명을 가져옵니다.

```azurecli
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>배포 매니페스트 만들기

IoT Edge 배포 매니페스트는 IoT Edge 디바이스에 배포할 모듈 및 구성 값을 지정합니다.

VS Code에서 *pnpbridge/deployment.template.json* 파일을 엽니다.

- 이전 명령의 값으로 `registryCredentials`를 업데이트합니다. `address` 값이 `{your container registry name}.azurecr.io`처럼 표시됩니다.
- `ModulePnpBridge`에 대해 `image` 값을 업데이트합니다. 이 모듈 이미지는 `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64`와 같습니다.
- CO2 검색 어댑터를 구성하려면 `PnPBridgeConfig`를 다음 JSON으로 바꿉니다.

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

VS Code의 **탐색기** 보기에서 *pnpbridge/deployment.template.json* 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 배포 매니페스트 생성** 을 선택합니다. 이 명령은 *pnpbridge/config/deployment.amd64.json* 배포 매니페스트를 생성합니다.

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>IoT Edge 디바이스에 브리지 배포

VS Code에서 명령 팔레트를 열고 *Azure IoT Hub: Select IoT Hub* 를 입력한 다음, 구독 및 IoT 허브를 선택합니다.

VS Code의 **탐색기** 보기에서 *pnpbridge/config/deployment.amd64.json* 파일을 마우스 오른쪽 단추로 클릭하고 **단일 디바이스용 배포 만들기** 를 선택한 다음, **bridge-edge-device** 를 선택합니다.

디바이스에서 모듈의 상태를 보려면 다음 명령을 실행합니다.

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

이제 실행 중인 모듈 목록에 CO2 감지 어댑터를 사용하도록 구성된 **ModulePnpBridge** 모듈이 포함됩니다.

### <a name="tidy-up"></a>정리

Azure 구독에서 가상 머신 및 컨테이너 레지스트리를 제거하려면 다음 명령을 실행합니다.

```azurecli
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>폴더 구조

*pnpbridge\deps\azure-iot-sdk-c-pnp*: C SDK용 Azure IoT 디바이스 SDK가 포함되어 있는 Git 하위 모듈입니다.

*pnpbridge\scripts*: 스크립트를 빌드합니다.

*pnpbridge\src*: IoT 플러그 앤 플레이 브리지 코어의 소스 코드입니다.

*pnpbridge\src\adapters*: 다양한 IoT 플러그 앤 플레이 브리지 어댑터의 소스 코드입니다.

## <a name="next-steps"></a>다음 단계

IoT 플러그 앤 플레이 브리지에 대해 자세히 알아보려면 [IoT 플러그 앤 플레이 브리지](https://github.com/Azure/iot-plug-and-play-bridge) GitHub 리포지토리를 방문하세요.
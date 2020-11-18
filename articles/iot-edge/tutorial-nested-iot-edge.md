---
title: 자습서 - IoT Edge 디바이스의 계층 구조 만들기 - Azure IoT Edge
description: 이 자습서에서는 게이트웨이를 사용하여 IoT Edge 디바이스의 계층 구조를 만드는 방법을 보여줍니다.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 78a8ae7724c9ede06b24649d3b19ea90b791ae08
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541320"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>자습서: IoT Edge 디바이스의 계층 구조 만들기(미리 보기)

계층 구조의 계층으로 구성된 네트워크에 Azure IoT Edge 노드를 배포합니다. 계층 구조의 각 계층은 그 아래 계층에 있는 디바이스의 메시지와 요청을 처리하는 게이트웨이 디바이스입니다.

>[!NOTE]
>이 기능에는 Linux 컨테이너를 실행하는 IoT Edge 버전 1.2(공개 미리 보기로 제공됨)가 필요합니다.

최상위 계층만 클라우드에 연결되고 하위 계층은 인접한 북쪽 및 남쪽 계층과만 통신할 수 있도록 디바이스의 계층 구조를 구성할 수 있습니다. 이러한 네트워크 계층화는 [ISA-95 standard](https://en.wikipedia.org/wiki/ANSI/ISA-95)를 따르는 대부분의 산업 네트워크의 기초입니다.

이 자습서의 목표는 프로덕션 환경을 시뮬레이션하는 IoT Edge 디바이스의 계층 구조를 만드는 것입니다. 끝에는 [시뮬레이션된 온도 센서 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor)을 계층 구조를 통해 컨테이너 이미지를 다운로드하여 인터넷 액세스 없이 하위 계층 디바이스에 배포합니다.

이러한 목표를 달성하기 위해 이 자습서에서는 IoT Edge 디바이스의 계층 구조를 만들고, 디바이스에 IoT Edge 런타임 컨테이너를 배포하고, 로컬에서 디바이스를 구성하는 과정을 안내합니다. 이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
>
> * IoT Edge 디바이스의 계층 구조에서 관계를 만들고 정의합니다.
> * 계층 구조의 디바이스에서 IoT Edge 런타임을 구성합니다.
> * 디바이스 계층 구조 전체에 일관된 인증서를 설치합니다.
> * 계층 구조의 디바이스에 워크로드를 추가합니다.
> * API 프록시 모듈을 사용하여 하위 계층 디바이스에서 단일 포트를 통해 HTTP 트래픽을 안전하게 라우팅합니다.

이 자습서에서는 다음 네트워크 계층이 정의됩니다.

* **최상위 계층**: 이 계층의 IoT Edge 디바이스는 클라우드에 직접 연결할 수 있습니다.

* **하위 계층**: 이 계층의 IoT Edge 디바이스는 클라우드에 직접 연결할 수 없습니다. 데이터를 보내고 받으려면 하나 이상의 중간 IoT Edge 디바이스를 거쳐야 합니다.

이 자습서에서는 간단하게 두 개의 디바이스 계층 구조를 사용합니다. **topLayerDevice** 라는 디바이스는 계층 구조의 최상위 계층에 있는 디바이스를 나타내며 클라우드에 직접 연결할 수 있습니다. 이 디바이스를 **부모 디바이스** 라고도 합니다. **lowerLayerDevice** 라는 다른 디바이스는 계층 구조의 하위 계층에 있는 디바이스를 나타내며, 클라우드에 직접 연결할 수 없습니다. 이 디바이스를 **자식 디바이스** 라고도 합니다. 프로덕션 환경을 나타내는 하위 계층 디바이스를 더 추가할 수 있습니다. 하위 계층 디바이스에 대한 구성은 **lowerLayerDevice** 의 구성을 따릅니다.

## <a name="prerequisites"></a>필수 구성 요소

IoT Edge 디바이스의 계층 구조를 만들려면 다음이 필요합니다.

* 인터넷에 연결된 컴퓨터(Windows 또는 Linux)
* IoT Edge 디바이스로 구성할 Linux 디바이스 2개. 사용 가능한 디바이스가 없으면 [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/)를 사용할 수 있습니다.
* 유효한 구독이 있는 Azure 계정. [Azure 구독](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Azure IoT 확장 v0.10.6 이상이 설치된 Azure CLI v2.3.1. 이 자습서에서는 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)을 사용합니다. Azure Cloud Shell에 익숙하지 않은 경우 [빠른 시작에서 자세한 내용을 확인하세요.](https://docs.microsoft.com/azure/iot-edge/quickstart-linux#use-azure-cloud-shell)

이 시나리오는 스크립팅된 [산업 IoT용 Azure IoT Edge 샘플](https://aka.ms/iotedge-nested-sample)을 수행하여 시도할 수도 있습니다. 이 샘플에서는 Azure 가상 머신을 미리 구성된 디바이스로 배포하여 공장 환경을 시뮬레이션합니다.

## <a name="configure-your-iot-edge-device-hierarchy"></a>IoT Edge 디바이스 계층 구조 구성

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>IoT Edge 디바이스의 계층 구조 만들기

IoT Edge 디바이스를 만드는 첫 번째 단계는 Azure Portal 또는 Azure CLI를 통해 수행할 수 있습니다. 이 자습서에서는 **topLayerDevice** 및 이것의 자식인 **lowerLayerDevice** 라는 두 개 IoT Edge 디바이스의 계층 구조를 만듭니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://ms.portal.azure.com/)에서 IoT Hub로 이동합니다.

1. 왼쪽 창 메뉴의 **자동 디바이스 관리** 에서 **IoT Edge** 를 선택합니다.

1. **+ IoT Edge 디바이스 추가** 를 선택합니다. 이 디바이스는 최상위 계층 디바이스이므로 적절한 고유 디바이스 ID를 입력합니다. **저장** 을 선택합니다.

1. **+ IoT Edge 디바이스 추가** 를 다시 선택합니다. 이 디바이스는 에지 하위 계층 디바이스이므로 적절한 고유 디바이스 ID를 입력합니다.

1. **상위 디바이스 설정** 을 선택하여 디바이스 목록에서 최상위 디바이스를 선택하고 **확인** 을 선택합니다. **저장** 을 선택합니다.

   ![하위 계층 디바이스의 부모 설정](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/)에서 다음 명령을 입력하여 허브에 IoT Edge 디바이스를 만듭니다. 이 디바이스는 최상위 계층 디바이스이므로 적절한 고유 디바이스 ID를 입력합니다.

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. 다음 명령을 입력하여 자식 IoT Edge 디바이스를 만들고 디바이스 간에 부모-자식 관계를 만듭니다.

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

각 IoT Edge 디바이스의 연결 문자열을 기록해 둡니다. 나중에 사용됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://ms.portal.azure.com/)에서 IoT Hub의 **IoT Edge** 섹션으로 이동합니다.

1. 디바이스 목록에 있는 디바이스 중 하나의 디바이스 ID를 클릭합니다.

1. **기본 연결 문자열** 필드에서 **복사** 를 선택하고 원하는 위치에 기록합니다.

1. 다른 모든 디바이스에 대해 반복합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/)에서 각 디바이스에 대해 다음 명령을 입력하여 디바이스의 연결 문자열을 검색하고 원하는 위치에 기록합니다.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

### <a name="create-certificates"></a>인증서 만들기

[게이트웨이 시나리오](iot-edge-as-gateway.md)의 모든 디바이스에는 서로 보안 연결을 설정하기 위한 공유 인증서가 필요합니다. 다음 단계를 사용하여 이 시나리오의 두 디바이스에 대한 데모 인증서를 만듭니다.

Linux 디바이스에서 데모 인증서를 만들려면 생성 스크립트를 복제하여 bash에서 로컬로 실행되도록 설정해야 합니다.

1. 데모 인증서를 생성하는 스크립트가 포함된 IoT Edge Git 리포지토리를 복제합니다.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. 작업하려는 디렉터리로 이동합니다. 모든 인증서 및 키 파일은 이 디렉터리에 생성됩니다.

1. 복제된 IoT Edge 리포지토리에서 작업 디렉터리로 구성 및 스크립트 파일을 복사합니다.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. 루트 CA 인증서 및 중간 인증서를 만듭니다.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   이 스크립트 명령은 여러 인증서와 키 파일을 만들지만 여기에서는 다음 파일을 게이트웨이 계층 구조에 대한 **루트 CA 인증서** 로 사용합니다.

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. 다음 명령을 사용하여 IoT Edge 디바이스 CA 인증서 및 프라이빗 키 세트를 두 개 만듭니다. 하나는 최상위 계층 디바이스용이고 다른 하나는 하위 계층 디바이스용입니다. CA 인증서를 서로 구분할 수 있도록 기억하기 쉬운 이름을 제공합니다.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   이 스크립트 명령은 여러 인증서와 키 파일을 만들지만, 여기에서는 각 IoT Edge 디바이스에서 다음 인증서와 키 쌍을 사용하고 config.yaml 파일에서 참조합니다.

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

각 디바이스에는 루트 CA 인증서의 복사본 및 자체 디바이스 CA 인증서와 프라이빗 키의 복사본이 필요합니다. USB 드라이브 또는 [보안 파일 복사](https://www.ssh.com/ssh/scp/)를 사용하여 인증서를 각 디바이스로 이동할 수 있습니다.

1. 인증서가 전송되면 각 디바이스에 대한 루트 CA를 설치합니다.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   이 명령은 /etc/ssl/certs에 인증서가 하나 추가되었음을 출력합니다.

### <a name="install-iot-edge-on-the-devices"></a>디바이스에 IoT Edge 설치

두 디바이스 모두에서 다음 단계를 수행하여 IoT Edge를 설치합니다.

1. 디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

1. Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

1. hsmlib 및 IoT Edge 디먼을 설치합니다. <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/libiothsm-std_1.2.0.rc1-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/iotedge_1.2.0_rc1-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>IoT Edge 런타임 구성

두 디바이스 모두에서 다음 단계를 수행하여 IoT Edge 런타임을 구성합니다. 디바이스에 대한 IoT Edge 런타임을 구성하는 작업은 4단계로 구성되며, 모두 IoT Edge 구성 파일을 편집하여 수행됩니다.

1. 디바이스의 연결 문자열을 구성 파일에 추가하여 각 디바이스를 수동으로 프로비저닝합니다.

1. 구성 파일을 디바이스 CA 인증서, 디바이스CA 프라이빗 키, 루트 CA 인증서로 가리켜서 디바이스 인증서 설정을 마칩니다.

1. IoT Edge 에이전트를 사용하여 시스템을 부트스트랩합니다.

1. **최상위 계층** 디바이스의 **hostname** 매개 변수를 업데이트하고 **하위 계층** 디바이스의 **hostname** 매개 변수와 **parent_hostname** 매개 변수를 모두 업데이트합니다.

다음 단계를 완료하고 IoT Edge 서비스를 다시 시작하여 디바이스를 구성합니다.

1. 각 디바이스에서 IoT Edge 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 파일의 프로비저닝 구성을 찾고 **Manual provisioning configuration using a connection string** 섹션의 주석 처리를 제거합니다.

1. **device_connection_string** 의 값을 IoT Edge 디바이스의 연결 문자열로 업데이트합니다. 다른 프로비저닝 섹션이 주석 처리되었는지 확인합니다. **provisioning:** 줄 앞에 공백이 없고 중첩된 항목은 공백 두 칸으로 들여쓰기 되어 있는지 확인합니다.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >클립보드 내용을 Nano에 붙여 넣으려면 `Shift+Right Click` 또는 `Shift+Insert` 키를 누릅니다.

1. **certificates** 섹션을 찾습니다. 세 개 인증서 필드의 주석 처리를 제거하고 이전 섹션에서 만들고 IoT Edge 디바이스로 이동한 인증서를 가리키도록 업데이트합니다. 파일 URI 경로(`file:///<path>/<filename>` 형식임)를 제공합니다.

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. **최상위 계층** 디바이스에 대한 **hostname** 매개 변수를 찾습니다. 값을 IoT Edge 디바이스의 FQDN(정규화된 도메인 이름) 또는 IP 주소로 업데이트합니다. 선택한 값을 계층 구조의 디바이스 전체에 일관되게 사용합니다.

   ```yml
   hostname: <device fqdn or IP>
   ```

1. **하위 계층** 의 IoT Edge 디바이스의 경우, 부모 디바이스의 **호스트 이름** 필드에 있는 것과 일치하는, 상위 디바이스의 FQDN 또는 IP를 가리키도록 구성 파일을 업데이트합니다. **최상위 계층** 의 IoT Edge 디바이스에 대해서는 이 매개 변수를 주석 처리된 상태로 둡니다.

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > 하위 계층이 둘 이상 있는 계층 구조의 경우, *parent_hostname* 필드를 바로 위 계층에 있는 디바이스의 FQDN으로 업데이트합니다.

1. **최상위 계층** 디바이스의 경우 **agent** yaml 섹션을 찾고 이미지 값을 올바른 버전의 IoT Edge 에이전트로 업데이트합니다. 이 경우 사용 가능한 IoT Edge 에이전트 이미지의 공개 미리 보기 버전을 사용하여 Azure Container Registry에서 최상위 계층의 IoT Edge 에이전트를 가리킵니다.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc1"
       auth: {}
   ```

1. **하위 계층** 의 IoT Edge 디바이스의 경우, 부모 디바이스의 FQDN 또는 IP(뒤에 API 프록시 포트 8000이 있음)를 가리키도록 이미지 값의 도메인 이름을 업데이트합니다. API 프록시 모듈은 다음 섹션에서 추가합니다.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc1"
       auth: {}
   ```

1. 파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

1. 구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>최상위 계층 디바이스에 모듈 배포

IoT Edge 런타임 구성을 완료하고 워크로드를 배포하기 위한 나머지 단계는 Azure Portal 또는 Azure CLI를 통해 클라우드에서 수행됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure portal](https://ms.portal.azure.com/)에서:

1. IoT Hub로 이동합니다.

1. 왼쪽 창 메뉴의 **자동 디바이스 관리** 에서 **IoT Edge** 를 선택합니다.

1. 디바이스 목록에서 **최상위 계층** Edge 디바이스의 디바이스 ID를 클릭합니다.

1. 위쪽 막대에서 **모듈 설정** 을 선택합니다.

1. 기어 아이콘 옆에 있는 **런타임 설정** 을 선택합니다.

1. **Edge 허브** 아래 이미지 필드에 `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc1`을 입력합니다.

   ![Edge 허브의 이미지 편집](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Edge 허브 모듈에 다음 환경 변수를 추가합니다.

    | Name | 값 |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Edge 허브의 환경 변수 편집](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. **Edge 에이전트** 아래 이미지 필드에 `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc1`을 입력합니다. **저장** 을 선택합니다.

1. Docker 레지스트리 모듈을 최상위 계층 디바이스에 추가합니다. **+ 추가** 를 선택하고 드롭다운에서 **IoT Edge 모듈** 을 선택합니다. Docker 레지스트리 모듈에 이름 `registry`를 제공하고 이미지 URI로 `registry:latest`를 입력합니다. 다음으로, 환경 변수를 추가하고 Microsoft 컨테이너 레지스트리에서 로컬 레지스트리 모듈을 가리키도록 옵션을 만들고 registry:5000에서 컨테이너 이미지를 다운로드하고 이러한 이미지를 제공합니다.

1. 환경 변수 탭에서 다음과 같은 환경 변수 이름-값 쌍을 입력합니다.

    | Name | 값 |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. 컨테이너 만들기 옵션 탭에서 다음 JSON을 입력합니다.

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. 다음으로, 최상위 계층 디바이스에 API 프록시 모듈을 추가합니다. **+ 추가** 를 선택하고 드롭다운에서 **마켓플레이스 모듈** 을 선택합니다. `IoT Edge API Proxy`를 검색하고 모듈을 선택합니다. IoT Edge API 프록시는 포트 8000을 사용하며, 기본적으로 포트 5000에서 `registry`라는 레지스트리 모듈을 사용하도록 구성됩니다.

1. **검토 + 만들기** 와 **만들기** 를 차례로 선택하여 배포를 완료합니다. 인터넷에 액세스할 수 있는 최상위 계층 디바이스의 IoT Edge 런타임은 IoT Edge 허브 및 IoT Edge 에이전트에 대한 **공개 미리 보기** 구성을 끌어와서 실행합니다.

   ![Edge 허브, Edge 에이전트, 레지스트리 모듈, API 프록시 모듈을 포함하는 전체 배포](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/)에서 다음 명령을 입력하여 deployment.json 파일을 만듭니다.

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. 아래 JSON의 내용을 deployment.json에 복사하고 저장한 후 닫습니다.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc1",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc1",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. 다음 명령을 입력하여 최상위 계층 Edge 디바이스에 대한 배포를 만듭니다.

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

## <a name="deploy-modules-to-the-lower-layer-device"></a>하위 계층 디바이스에 모듈 배포

Azure Portal 및 Azure CLI를 모두 사용하여 클라우드에서 **하위 계층** 디바이스로 워크로드를 배포할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure portal](https://ms.portal.azure.com/)에서:

1. IoT Hub로 이동합니다.

1. 왼쪽 창 메뉴의 **자동 디바이스 관리** 에서 **IoT Edge** 를 선택합니다.

1. IoT Edge 디바이스 목록에서 하위 계층 디바이스의 디바이스 ID를 클릭합니다.

1. 위쪽 막대에서 **모듈 설정** 을 선택합니다.

1. 기어 아이콘 옆에 있는 **런타임 설정** 을 선택합니다.

1. **Edge 허브** 아래 이미지 필드에 `$upstream:8000/azureiotedge-hub:1.2.0-rc1`을 입력합니다.

1. Edge 허브 모듈에 다음 환경 변수를 추가합니다.

    | Name | 값 |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. **Edge 에이전트** 아래 이미지 필드에 `$upstream:8000/azureiotedge-agent:1.2.0-rc1`을 입력합니다. **저장** 을 선택합니다.

1. 온도 센서 모듈을 추가합니다. **+ 추가** 를 선택하고 드롭다운에서 **마켓플레이스 모듈** 을 선택합니다. `Simulated Temperature Sensor`를 검색하고 모듈을 선택합니다.

1. **IoT Edge 모듈** 에서 방금 추가한 `Simulated Temperature Sensor` 모듈을 선택하고 `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0`을 가리키도록 이미지 URI를 업데이트합니다.

1. **저장**, **검토 + 만들기**, **만들기** 를 차례로 선택하여 배포를 완료합니다.

   ![Edge 허브, Edge 에이전트 및 시뮬레이션된 온도 센서를 포함하는 전체 배포](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/)에서 다음 명령을 입력하여 deployment.json 파일을 만듭니다.

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. 아래 JSON의 내용을 deployment.json에 복사하고 저장한 후 닫습니다.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc1",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc1",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. 다음 명령을 입력하여 하위 계층 Edge 디바이스에 대한 세트 모듈 배포를 생성합니다.

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

You can also watch the messages arrive at your IoT hub by using the [Azure IoT Hub extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## Clean up resources

You can delete the local configurations and the Azure resources that you created in this article to avoid charges.

To delete the resources:

1. Sign in to the [Azure portal](https://portal.azure.com) and select **Resource groups**.

2. Select the name of the resource group that contains your IoT Edge test resources. 

3. Review the list of resources contained in your resource group. If you want to delete all of them, you can select **Delete resource group**. If you want to delete only some of them, you can click into each resource to delete them individually. 

## Next steps

In this tutorial, you configured two IoT Edge devices as gateways and set one as the parent device of the other. Then, you demonstrated pulling a container image onto the child device through a gateway. You can also try out this scenario by following the scripted [Azure IoT Edge for Industrial IoT sample](https://aka.ms/iotedge-nested-sample), which deploys Azure virtual machines as preconfigured devices to simulate a factory environment.

To see how Azure IoT Edge can create more solutions for your business, continue on to the other tutorials.

> [!div class="nextstepaction"]
> [Deploy an Azure Machine Learning model as a module](tutorial-deploy-machine-learning.md)

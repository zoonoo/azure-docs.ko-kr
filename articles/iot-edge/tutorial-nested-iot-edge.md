---
title: 자습서 - IoT Edge 디바이스의 계층 구조 만들기 - Azure IoT Edge
description: 이 자습서에서는 게이트웨이를 사용하여 IoT Edge 디바이스의 계층 구조를 만드는 방법을 보여줍니다.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: f1b1a94dc1d96e625947eef5730c24f080fc155a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721415"
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
> * [IoT Edge API 프록시 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview)을 사용하여 하위 계층 디바이스에서 단일 포트를 통해 HTTP 트래픽을 안전하게 라우팅합니다.

이 자습서에서는 다음 네트워크 계층이 정의됩니다.

* **최상위 계층**: 이 계층의 IoT Edge 디바이스는 클라우드에 직접 연결할 수 있습니다.

* **하위 계층**: 최상위 계층 아래에 있는 계층의 IoT Edge 디바이스는 클라우드에 직접 연결할 수 없습니다. 데이터를 보내고 받으려면 하나 이상의 중간 IoT Edge 디바이스를 거쳐야 합니다.

이 자습서에서는 아래 그림과 같이 간단하게 두 개의 디바이스 계층 구조를 사용합니다. **최상위 계층 디바이스** 는 계층 구조의 최상위 계층에 있는 디바이스를 나타내며 클라우드에 직접 연결할 수 있습니다. 이 디바이스를 **부모 디바이스** 라고도 합니다. **하위 계층 디바이스** 는 계층 구조의 하위 계층에 있는 디바이스를 나타내며 클라우드에 직접 연결할 수 없습니다. 프로덕션 환경을 나타내는 하위 계층 디바이스를 필요에 따라 더 추가할 수 있습니다. 하위 계층의 디바이스를 **자식 디바이스** 라고도 합니다. 추가 하위 계층 디바이스에 대한 구성은 **하위 계층 디바이스** 의 구성을 따릅니다.

![두 개의 디바이스(최상위 계층 디바이스 및 하위 계층 디바이스)를 포함하는 자습서 계층 구조](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>필수 구성 요소

IoT Edge 디바이스의 계층 구조를 만들려면 다음이 필요합니다.

* 인터넷에 연결된 컴퓨터(Windows 또는 Linux)
* 유효한 구독이 있는 Azure 계정. [Azure 구독](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Azure IoT 확장 v0.10.6 이상이 설치된 Azure CLI v2.3.1. 이 자습서에서는 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다. Azure Cloud Shell에 익숙하지 않은 경우 [빠른 시작에서 자세한 내용을 확인하세요.](./quickstart-linux.md#prerequisites)
  * Azure CLI 모듈 및 확장의 현재 버전을 보려면 [az version](/cli/azure/reference-index?#az_version)을 실행합니다.
* 계층의 각 디바이스에 대한 IoT Edge 디바이스로 구성할 Linux 디바이스입니다. 이 자습서에는 디바이스가 두 개 사용됩니다. 사용 가능한 디바이스가 없는 경우 다음 명령에서 자리 표시자 텍스트를 대체하고 실행하여 계층 구조의 각 디바이스를 위해 두 개의 Azure 가상 머신을 만들 수 있습니다.

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

* 다음 포트가 오픈 인바운드인지 확인합니다. 8000, 443, 5671, 8883:
  * 8000: API 프록시를 통해 Docker 컨테이너 이미지를 끌어오는 데 사용됩니다.
  * 443: REST API 호출을 위해 부모 및 자식 에지 허브 간에 사용됩니다.
  * 5671, 8883: AMQP 및 MQTT에 사용됩니다.

  자세한 내용은 [Azure Portal을 사용하여 가상 머신에 대한 포털을 여는 방법](../virtual-machines/windows/nsg-quickstart-portal.md)을 참조하세요.

>[!TIP]
>IoT Edge 디바이스의 계층 구조 설정을 자동화하려면 스크립팅된 [산업용 IoT용 Azure IoT Edge 샘플](https://aka.ms/iotedge-nested-sample)을 따라할 수 있습니다. 이 스크립팅된 시나리오는 Azure 가상 머신을 미리 구성된 디바이스로 배포하여 공장 환경을 시뮬레이션합니다.
>
>샘플 계층 구조 만들기를 단계별로 수행하려면 아래 자습서 단계를 계속 진행하세요.

## <a name="configure-your-iot-edge-device-hierarchy"></a>IoT Edge 디바이스 계층 구조 구성

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>IoT Edge 디바이스의 계층 구조 만들기

IoT Edge 디바이스는 계층 구조의 계층을 구성합니다. 이 자습서에서는 두 개의 IoT Edge 디바이스(**최상위 계층 디바이스** 및 그 자식 디바이스인 **하위 계층 디바이스**)로 구성된 계층 구조를 만듭니다. 필요에 따라 추가 자식 디바이스를 만들 수 있습니다.

IoT Edge 디바이스를 만들려면 Azure Portal 또는 Azure CLI를 사용할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://ms.portal.azure.com/)에서 IoT Hub로 이동합니다.

1. 왼쪽 창 메뉴의 **자동 디바이스 관리** 에서 **IoT Edge** 를 선택합니다.

1. **+ IoT Edge 디바이스 추가** 를 선택합니다. 이 디바이스는 최상위 계층 디바이스이므로 적절한 고유 디바이스 ID를 입력합니다. **저장** 을 선택합니다.

1. **+ IoT Edge 디바이스 추가** 를 다시 선택합니다. 이 디바이스는 하위 계층 디바이스이므로 적절한 고유 디바이스 ID를 입력합니다.

1. **상위 디바이스 설정** 을 선택하여 디바이스 목록에서 최상위 디바이스를 선택하고 **확인** 을 선택합니다. **저장** 을 선택합니다.

   ![하위 계층 디바이스의 부모 설정](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/)에서 다음 명령을 입력하여 허브에 IoT Edge 디바이스를 만듭니다. 이 디바이스는 최상위 계층 디바이스이므로 적절한 고유 디바이스 ID를 입력합니다.

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

   디바이스 만들기가 성공하면 디바이스의 JSON 구성이 출력됩니다.

   ![성공적으로 만든 디바이스의 JSON 출력](./media/tutorial-nested-iot-edge/json-success-output.png)

1. 다음 명령을 입력하여 하위 계층 IoT Edge 디바이스를 만듭니다. 계층 구조에 더 많은 계층이 필요한 경우 두 개 이상의 하위 계층 디바이스를 만들 수 있습니다. 각각에 고유한 디바이스 ID를 제공해야 합니다.

   ```azurecli-interactive
   az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. 다음 명령을 입력하여 **최상위 계층 디바이스** 와 각 **하위 계층 디바이스** 간의 부모-자식 관계를 정의합니다. 계층 구조의 하위 계층 디바이스 각각에 대해 이 명령을 실행해야 합니다.

   ```azurecli-interactive
   az iot hub device-identity parent set --device-id {lower_layer_device_id} --parent-device-id {top_layer_device_id} --hub-name {hub_name}
   ```

   이 명령에는 명시적 출력이 없습니다.

---

다음으로 각 IoT Edge 디바이스의 연결 문자열을 기록해 둡니다. 나중에 사용됩니다.

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

위의 단계를 올바르게 완료한 경우 Azure Portal 또는 Azure CLI에서 다음 단계를 사용하여 부모-자식 관계가 올바른지 확인할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://ms.portal.azure.com/)에서 IoT Hub의 **IoT Edge** 섹션으로 이동합니다.

1. 디바이스 목록에서 **하위 계층 디바이스** 의 디바이스 ID를 클릭합니다.

1. 디바이스의 세부 정보 페이지에서 **최상위 계층 디바이스** 의 ID가 **부모 디바이스** 필드 옆에 표시됩니다.

   [자식 디바이스에서 승인된 부모 디바이스](./media/tutorial-nested-iot-edge/lower-layer-device-parent.png)

**최상위 계층 디바이스** 를 통해 자식 계층 관계를 설정할 수도 있습니다.

1. 디바이스 목록에서 **최상위 계층 디바이스** 의 디바이스 ID를 클릭합니다.

1. 맨 위에서 **자식 디바이스 관리** 탭을 선택합니다.

1. 디바이스 목록 아래에 **하위 계층 디바이스** 가 표시됩니다.

   [부모 디바이스에서 승인된 자식 디바이스](./media/tutorial-nested-iot-edge/top-layer-device-child.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/)에서 자식 디바이스에서 승인된 부모 디바이스 ID를 가져와 자식 디바이스가 부모 디바이스와의 관계를 성공적으로 설정했는지 확인할 수 있습니다. 위의 그림과 같이 부모 디바이스의 JSON 구성이 출력됩니다.

   ```azurecli-interactive
   az iot hub device-identity parent show --device-id {lower_layer_device_id} --hub-name {hub_name}
   ```

**최상위 계층 디바이스** 를 쿼리하여 자식 계층 관계를 설정할 수도 있습니다.

1. 부모 디바이스가 인식하는 자식 디바이스를 나열합니다.

    ```azurecli-interactive
    az iot hub device-identity children list --device-id {top_layer_device_id} --hub-name {hub_name}
    ```

---

계층 구조가 올바르게 구성되었으면 다음 단계를 진행할 수 있습니다.

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
   ./certGen.sh create_edge_device_ca_certificate "{top_layer_certificate_name}"
   ./certGen.sh create_edge_device_ca_certificate "{lower_layer_certificate_name}"
   ```

   이 스크립트 명령은 여러 인증서와 키 파일을 만들지만, 여기에서는 각 IoT Edge 디바이스에서 다음 인증서와 키 쌍을 사용하고 config 파일에서 참조합니다.

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

각 디바이스에는 루트 CA 인증서의 복사본 및 자체 디바이스 CA 인증서와 프라이빗 키의 복사본이 필요합니다. USB 드라이브 또는 [보안 파일 복사](https://www.ssh.com/ssh/scp/)를 사용하여 인증서를 각 디바이스로 이동할 수 있습니다.

1. 인증서가 전송되면 각 디바이스에 대한 루트 CA를 설치합니다.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   이 명령은 `/etc/ssl/certs`에 인증서가 하나 추가되었음을 출력합니다.

   [인증서 설치 성공 메시지](./media/tutorial-nested-iot-edge/certificates-success-output.png)

위의 단계를 올바르게 완료한 경우 해당 디렉터리로 이동하여 설치된 인증서를 검색하면 `/etc/ssl/certs`에 인증서가 설치되었는지 확인할 수 있습니다.

1. `/etc/ssl/certs`로 이동합니다.

   ```bash
   cd /etc/ssl/certs
   ```

1. `azure`에 대해 설치된 인증서 및 `grep`을 나열합니다.

   ```bash
   ll | grep azure
   ```

   루트 CA 인증서에 연결된 인증서 해시와 `usr/local/share` 디렉터리의 복사본에 연결된 루트 CA 인증서가 표시됩니다.

   [Azure 인증서 검색 결과](./media/tutorial-nested-iot-edge/certificate-list-results.png)

각 디바이스에 인증서가 올바로 설치되었으면 다음 단계를 진행할 수 있습니다.

### <a name="install-iot-edge-on-the-devices"></a>디바이스에 IoT Edge 설치

IoT Edge 버전 1.2 런타임 이미지를 설치하면 디바이스가 디바이스의 계층 구조로 작동하는 데 필요한 기능에 액세스할 수 있습니다.

IoT Edge를 설치하려면 적절한 리포지토리 구성을 설치하고, 필수 구성 요소를 설치하고, 필요한 릴리스 자산을 설치해야 합니다.

1. 디바이스 운영 체제와 일치하는 리포지토리 구성을 설치합니다.

   * **Ubuntu Server 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS Stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

1. 생성된 목록을 sources.list.d 디렉터리에 복사합니다.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Microsoft GPG 공개 키를 설치합니다.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

1. 디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

1. Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

1. IoT Edge 및 IoT ID 서비스를 설치합니다.

   ```bash
   sudo apt-get install aziot-edge
   ```

위의 단계를 올바르게 완료한 경우 계층 구조의 각 디바이스에서 Azure IoT Edge 구성 파일 `/etc/aziot/config.toml`을 업데이트하도록 요청하는 Azure IoT Edge 배너 메시지가 표시됩니다. 그러면 다음 단계를 진행할 수 있습니다.

### <a name="configure-the-iot-edge-runtime"></a>IoT Edge 런타임 구성

구성 단계는 디바이스를 프로비전하는 이외에 앞서 만든 인증서를 사용하여 계층 구조의 디바이스 간에 신뢰할 수 있는 통신을 설정합니다. 또한 계층 구조의 네트워크 구조를 설정하기 시작합니다. 최상위 계층 디바이스는 인터넷 연결을 유지하여 클라우드에서 런타임의 이미지를 끌어오고, 하위 계층 디바이스는 최상위 계층 디바이스를 통해 라우팅하여 이러한 이미지에 액세스합니다.

IoT Edge 런타임을 구성하려면 구성 파일의 여러 구성 요소를 수정해야 합니다. **최상위 계층 디바이스** 와 **하위 계층 디바이스** 는 구성이 약간 다르므로 각 단계에서 어느 디바이스의 구성 파일을 편집하는지 잘 확인해야 합니다. 디바이스에 대한 IoT Edge 런타임을 구성하는 작업은 4단계로 구성되며, 모두 IoT Edge 구성 파일을 편집하여 수행됩니다.

* 디바이스의 연결 문자열을 구성 파일에 추가하여 각 디바이스를 수동으로 프로비저닝합니다.

* 구성 파일을 디바이스 CA 인증서, 디바이스CA 프라이빗 키, 루트 CA 인증서로 가리켜서 디바이스 인증서 설정을 마칩니다.

* IoT Edge 에이전트를 사용하여 시스템을 부트스트랩합니다.

* **최상위 계층** 디바이스의 **hostname** 매개 변수를 업데이트하고 **하위 계층** 디바이스의 **hostname** 매개 변수와 **parent_hostname** 매개 변수를 모두 업데이트합니다.

다음 단계를 완료하고 IoT Edge 서비스를 다시 시작하여 디바이스를 구성합니다.

>[!TIP]
>Nano에서 구성 파일을 탐색할 때 **Ctrl + W** 를 사용하여 파일에서 키워드를 검색할 수 있습니다.

1. 각 디바이스에서 포함된 템플릿을 기반으로 구성 파일을 만듭니다.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml

1. On each device, open the IoT Edge configuration file.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. **최상위 계층 디바이스** 에 대한 **호스트 이름** 섹션을 찾습니다. `hostname` 매개 변수가 포함된 줄에서 주석 처리를 제거하고 값을 IoT Edge 디바이스의 FQDN(정규화된 도메인 이름) 또는 IP 주소로 업데이트합니다. 선택한 값을 계층 구조의 디바이스 전체에 일관되게 사용합니다.

   ```toml
   hostname = "<device fqdn or IP>"
   ```

   >[!TIP]
   >클립보드 내용을 Nano에 붙여 넣으려면 `Shift+Right Click` 또는 `Shift+Insert` 키를 누릅니다.

1. **하위 계층** 에 있는 모든 IoT Edge 디바이스에 대해 **부모 호스트 이름** 섹션을 찾습니다. `parent_hostname` 매개 변수가 포함된 줄에서 주석 처리를 제거하고 부모 디바이스의 FQDN 또는 IP를 가리키도록 값을 업데이트합니다. 부모 디바이스의 **호스트 이름** 필드에 입력한 정확한 값을 사용합니다. **최상위 계층** 의 IoT Edge 디바이스에 대해서는 이 매개 변수를 주석 처리된 상태로 둡니다.

   ```toml
   parent_hostname = "<parent device fqdn or IP>"
   ```

   > [!NOTE]
   > 하위 계층이 둘 이상 있는 계층 구조의 경우, *parent_hostname* 필드를 바로 위 계층에 있는 디바이스의 FQDN으로 업데이트합니다.

1. 파일의 **트러스트 번들 인증서** 섹션을 찾습니다. `trust_bundle_cert` 매개 변수가 포함된 줄에서 주석 처리를 제거하고 게이트웨이 계층 구조의 모든 디바이스에서 공유하는 루트 CA 인증서에 대한 파일 URI 경로로 값을 업데이트합니다.

   ```toml
   trust_bundle_cert = "<root CA certificate>"
   ```

1. 파일의 **프로비전** 섹션을 찾습니다. **연결 문자열을 사용하여 수동 프로비저닝** 에 대한 줄에서 주석 처리를 제거합니다. 계층 구조의 각 디바이스에 대해 **connection_string** 의 값을 IoT Edge 디바이스의 연결 문자열로 업데이트합니다.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string: "<Device connection string>"
   ```

1. **기본 Edge 에이전트** 섹션을 찾습니다.

   * **최상위 계층** 디바이스에 대해 edgeAgent 이미지 값을 Azure Container Registry 모듈의 공개 미리 보기 버전으로 업데이트합니다.
   
     ```toml
     [agent.config]
     image = "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
     ```

   * **하위 계층** 의 각 IoT Edge 디바이스에 대해 edgeAgent 이미지를 부모 디바이스를 가리킨 다음 API 프록시가 수신 대기하는 포트를 가리키도록 업데이트합니다. 다음 섹션에서 부모 디바이스에 API 프록시 모듈을 배포합니다.
   
     ```toml
     [agent.config]
     image = "<parent hostname value>:8000/azureiotedge-agent:1.2.0-rc4"
     ```

1. **Edge CA 인증서** 섹션을 찾습니다. 이 섹션의 처음 세 줄에서 주석 처리를 제거합니다. 그런 다음, 이전 섹션에서 만들어 IoT Edge 디바이스로 이동한 디바이스 CA 인증서 및 디바이스 CA 프라이빗 키 파일을 가리키도록 두 매개 변수를 업데이트합니다. 파일 URI 경로를 `file:///<path>/<filename>` 형식(예: `file:///certs/iot-edge-device-ca-top-layer-device.key.pem`)으로 제공합니다.

   ```yml
   [edge_ca]
   cert = "<File URI path to the device full chain CA certificate unique to this device.>"
   pk = "<File URI path to the device CA private key unique to this device.>"
   ```

   >[!NOTE]
   >**전체 체인** CA 인증서 경로 및 파일 이름을 사용하여 `device_ca_cert` 필드를 채워야 합니다.

1. 파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

1. 구성 파일에서 프로비전 정보를 입력한 후 변경 내용을 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

계속하기 전에 계층 구조의 각 디바이스에 대한 구성 파일을 업데이트했는지 확인합니다. 계층 구조에 따라 하나의 **최상위 계층 디바이스** 와 하나 이상의 **하위 계층 디바이스** 를 구성했습니다.

위의 단계를 올바르게 완료한 경우 디바이스가 올바르게 구성되었는지 확인할 수 있습니다.

1. 디바이스에서 구성 및 연결 확인을 실행합니다.

   ```bash
   sudo iotedge check
   ```

**최상위 계층 디바이스** 에서 몇 가지 통과 평가 및 하나 이상의 경고가 포함된 출력이 표시될 것입니다. IoT Edge 버전 1.2가 공개 미리 보기 상태이므로 `latest security daemon` 확인을 통해 다른 IoT Edge 버전이 최신 안정화 버전인 것으로 경고합니다. 로그 정책에 대한 그리고 네트워크에 따라 DNS 정책에 대한 추가 경고가 표시될 수 있습니다.

**하위 계층 디바이스** 의 경우 최상위 계층 디바이스와 유사한 출력이 표시되지만 EdgeAgent 모듈을 업스트림에서 끌어올 수 없음을 나타내는 추가 경고가 표시됩니다. IoT Edge API 프록시 모듈 및 Docker Container Registry 모듈(하위 계층 디바이스가 이 모듈에서 이미지를 끌어옴)이 아직 **최상위 계층 디바이스** 에 배포되지 않았으므로 이 경고는 허용됩니다.

`iotedge check`의 샘플 출력은 다음과 같습니다.

[샘플 구성 및 연결 결과](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png)

각 디바이스의 구성이 올바르면 다음 단계를 진행할 수 있습니다.

## <a name="deploy-modules-to-the-top-layer-device"></a>최상위 계층 디바이스에 모듈 배포

모듈은 디바이스에 대한 배포 및 IoT Edge 런타임을 완료하고 계층 구조를 추가로 정의하는 데 사용됩니다. IoT Edge API 프록시 모듈은 하위 계층 디바이스에서 단일 포트를 통해 HTTP 트래픽을 안전하게 라우팅합니다. Docker 레지스트리 모듈은 이미지 끌어오기를 최상위 계층 디바이스로 라우팅하여 하위 계층 디바이스가 액세스할 수 있는 Docker 이미지의 리포지토리를 허용합니다.

최상위 계층 디바이스에 모듈을 배포하려면 Azure Portal 또는 Azure CLI를 사용할 수 있습니다.

>[!NOTE]
>IoT Edge 런타임 구성을 완료하고 워크로드를 배포하는 나머지 단계는 IoT Edge 디바이스에서 수행되지 않습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure portal](https://ms.portal.azure.com/)에서:

1. IoT Hub로 이동합니다.

1. 왼쪽 창 메뉴의 **자동 디바이스 관리** 에서 **IoT Edge** 를 선택합니다.

1. 디바이스 목록에서 **최상위 계층** Edge 디바이스의 디바이스 ID를 클릭합니다.

1. 위쪽 막대에서 **모듈 설정** 을 선택합니다.

1. 기어 아이콘 옆에 있는 **런타임 설정** 을 선택합니다.

1. **Edge 허브** 아래 이미지 필드에 `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4`을 입력합니다.

   ![Edge 허브의 이미지 편집](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Edge 허브 모듈에 다음 환경 변수를 추가합니다.

    | Name | 값 |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Edge 허브의 환경 변수 편집](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. **Edge 에이전트** 아래 이미지 필드에 `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4`을 입력합니다. **저장** 을 선택합니다.

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
                               } 
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
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4",
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

위의 단계를 올바르게 완료한 경우 **최상위 계층 디바이스** 가 **배포에 지정** 된 대로 IoT Edge API 프록시 모듈, Docker Container Registry 모듈, 시스템 모듈 등 4개의 모듈을 보고해야 합니다. 디바이스가 새 배포를 수신하고 모듈을 시작하는 데 몇 분 정도 걸릴 수 있습니다. **디바이스에서 보고** 한 온도 센서 모듈이 표시될 때까지 페이지를 새로 고칩니다. 디바이스가 모듈을 보고하면 다음 단계를 진행할 수 있습니다.

## <a name="deploy-modules-to-the-lower-layer-device"></a>하위 계층 디바이스에 모듈 배포

또한 모듈은 하위 계층 디바이스의 워크로드 역할을 합니다. 시뮬레이션된 온도 센서 모듈은 디바이스 계층 구조를 통해 데이터의 기능 흐름을 제공하는 샘플 원격 분석 데이터를 만듭니다.

하위 계층 디바이스에 모듈을 배포하려면 Azure Portal 또는 Azure CLI를 사용할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure portal](https://ms.portal.azure.com/)에서:

1. IoT Hub로 이동합니다.

1. 왼쪽 창 메뉴의 **자동 디바이스 관리** 에서 **IoT Edge** 를 선택합니다.

1. IoT Edge 디바이스 목록에서 하위 계층 디바이스의 디바이스 ID를 클릭합니다.

1. 위쪽 막대에서 **모듈 설정** 을 선택합니다.

1. 기어 아이콘 옆에 있는 **런타임 설정** 을 선택합니다.

1. **Edge 허브** 아래 이미지 필드에 `$upstream:8000/azureiotedge-hub:1.2.0-rc4`을 입력합니다.

1. Edge 허브 모듈에 다음 환경 변수를 추가합니다.

    | Name | 값 |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. **Edge 에이전트** 아래 이미지 필드에 `$upstream:8000/azureiotedge-agent:1.2.0-rc4`을 입력합니다. **저장** 을 선택합니다.

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
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc4",
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

If you completed the above steps correctly, your **lower layer device** should report three modules: the temperature sensor module and the system modules, as **Specified in Deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by Device**. Once the modules are reported by the device, you are ready to continue.

## Troubleshooting

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc4
```

`azureiotedge-diagnostics` 값은 레지스트리 모듈과 연결된 컨테이너 레지스트리에서 가져옵니다. 이 자습서는 기본적으로 https://mcr.microsoft.com: 으로 설정되어 있습니다.

| 이름 | 값 |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

프라이빗 컨테이너 레지스트리를 사용하는 경우 모든 이미지(예: IoTEdgeAPIProxy, edgeAgent, edgeHub 및 진단)가 컨테이너 레지스트리에 있는지 확인합니다.

## <a name="view-generated-data"></a>생성된 데이터 보기

사용자가 푸시한 **시뮬레이션된 온도 센서** 모듈은 샘플 환경 데이터를 생성합니다. 주변 온도 및 습도, 머신 온도 및 압력, 타임스탬프가 포함된 메시지를 보냅니다.

[Visual Studio Code용 Azure IoT Hub 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 IoT 허브에 도달하는 메시지를 볼 수 있습니다.

[Azure Cloud Shell](https://shell.azure.com/)을 통해 이러한 메시지를 볼 수도 있습니다.

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>리소스 정리

요금이 부과되지 않도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다.

리소스를 삭제하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **리소스 그룹** 을 선택합니다.

2. IoT Hub 테스트 리소스가 포함된 리소스 그룹 이름을 선택합니다. 

3. 리소스 그룹에 포함된 리소스의 목록을 검토합니다. 모든 항목을 삭제하려는 경우 **리소스 그룹 삭제** 를 선택할 수 있습니다. 일부만 삭제하려는 경우 각 리소스를 클릭하여 개별적으로 삭제할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 두 개의 IoT Edge 디바이스를 게이트웨이로 구성하고 다른 디바이스를 부모 디바이스로 설정했습니다. 그런 다음, 게이트웨이를 통해 자식 디바이스로 컨테이너 이미지를 끌어오는 방법을 시연했습니다.

Azure IoT Edge에서 추가 업무용 솔루션을 만드는 방법을 확인하려는 경우 다른 자습서를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Machine Learning 모델을 모듈로 배포](tutorial-deploy-machine-learning.md)

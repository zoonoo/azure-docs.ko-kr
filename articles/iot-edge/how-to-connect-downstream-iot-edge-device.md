---
title: 다운스트림 IoT Edge 장치 연결-Azure IoT Edge | Microsoft Docs
description: IoT Edge 장치를 구성 하 여 Azure IoT Edge 게이트웨이 장치에 연결 하는 방법입니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: d5da6576258d3e33296781bbc262494220140ddc
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489287"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway-preview"></a>다운스트림 IoT Edge 장치를 Azure IoT Edge 게이트웨이 (미리 보기)에 연결

이 문서에서는 IoT Edge 게이트웨이와 다운스트림 IoT Edge 장치 간에 트러스트 된 연결을 설정 하는 방법에 대 한 지침을 제공 합니다.

>[!NOTE]
>이 기능에는 Linux 컨테이너를 실행 하는 공개 미리 보기로 제공 되는 IoT Edge 버전 1.2이 필요 합니다.

게이트웨이 시나리오에서 IoT Edge 장치는 게이트웨이 및 다운스트림 장치 일 수 있습니다. 여러 IoT Edge 게이트웨이를 계층화 하 여 장치 계층 구조를 만들 수 있습니다. 다운스트림 (또는 자식) 장치는 게이트웨이 또는 부모 장치를 통해 메시지를 인증 하 고 보내거나 받을 수 있습니다.

게이트웨이 계층의 IoT Edge 장치에는 두 가지 구성이 있으며,이 문서에서는 두 가지를 모두 처리 합니다. 첫 번째는 **최상위 계층** IoT Edge 장치입니다. 여러 IoT Edge 장치가 서로 연결 되는 경우 부모 장치가 없지만 IoT Hub에 직접 연결 되는 모든 장치는 최상위 계층에 있는 것으로 간주 됩니다. 이 장치는 아래의 모든 장치에서 요청을 처리 하는 일을 담당 합니다. 다른 구성은 계층의 **하위 계층** 에 있는 모든 IoT Edge 장치에 적용 됩니다. 이러한 장치는 다른 다운스트림 IoT 및 IoT Edge 장치에 대 한 게이트웨이 일 수 있지만 자체 부모 장치를 통해 통신을 라우팅해야 합니다.

일부 네트워크 아키텍처에서는 계층에서 상위 IoT Edge 장치만 클라우드에 연결할 수 있어야 합니다. 이 구성에서 계층 구조의 하위 계층에 있는 모든 IoT Edge 장치는 게이트웨이 (또는 부모) 장치와 다운스트림 (또는 자식) 장치와만 통신할 수 있습니다.

이 문서의 모든 단계는 [IoT Edge 장치를 구성 하 여 투명 한 게이트웨이로 작동 하도록 구성](how-to-create-transparent-gateway.md)합니다 .이는 IoT Edge 장치를 다운스트림 IoT 장치의 게이트웨이로 설정 합니다. 모든 게이트웨이 시나리오에 적용 되는 동일한 기본 단계는 다음과 같습니다.

* **인증** : 게이트웨이 계층의 모든 장치에 대 한 IoT Hub id를 만듭니다.
* **권한 부여** : IoT Hub에서 부모/자식 관계를 설정 하 여 IoT Hub에 연결 하는 것과 같이 부모 장치에 연결할 수 있는 자식 장치에 권한을 부여 합니다.
* **게이트웨이 검색** : 자식 장치가 로컬 네트워크에서 해당 부모 장치를 찾을 수 있는지 확인 합니다.
* **보안 연결** : 동일한 체인에 포함 된 신뢰할 수 있는 인증서를 사용 하 여 보안 연결을 설정 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 무료 또는 표준 IoT hub
* 두 개 이상의 **IoT Edge 장치** , 하나는 최상위 계층 장치 및 하나 이상의 하위 계층 장치입니다. IoT Edge 장치를 사용할 수 없는 경우 [Ubuntu 가상 머신에서 Azure IoT Edge을 실행할](how-to-install-iot-edge-ubuntuvm.md)수 있습니다.
* Azure CLI를 사용 하 여 장치를 만들고 관리 하는 경우 Azure IoT extension v 0.10.6 이상이 설치 된 Azure CLI v 2.3.1을 보유 해야 합니다.

이 문서에서는 시나리오에 적합 한 게이트웨이 계층 구조를 만드는 데 도움이 되는 자세한 단계와 옵션을 제공 합니다. 단계별 자습서는 [게이트웨이를 사용 하 여 IoT Edge 장치 계층 구조 만들기](tutorial-nested-iot-edge.md)를 참조 하세요.

## <a name="create-a-gateway-hierarchy"></a>게이트웨이 계층 구조 만들기

시나리오에서 IoT Edge 장치에 대 한 부모/자식 관계를 정의 하 여 IoT Edge 게이트웨이 계층을 만듭니다. 새 장치 id를 만들 때 부모 장치를 설정 하거나 기존 장치 id의 부모 및 자식을 관리할 수 있습니다.

부모/자식 관계를 설정 하는 단계는 자식 장치가 IoT Hub에 연결 하는 것과 같이 부모 장치에 연결할 수 있도록 권한을 부여 합니다.

IoT Edge 장치만 부모 장치가 될 수 있지만 IoT Edge 장치 및 IoT 장치는 모두 자식일 수 있습니다. 부모는 여러 자식을 포함할 수 있지만 자식은 하나의 부모만 가질 수 있습니다. 부모/자식 집합을 함께 연결 하 여 한 장치의 자식이 다른 장치의 부모를 갖도록 게이트웨이 계층을 만듭니다.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 새 장치 id를 만들 때 또는 기존 장치를 편집 하 여 부모/자식 관계를 관리할 수 있습니다.

새 IoT Edge 장치를 만들 때 해당 허브의 기존 IoT Edge 장치 목록에서 부모 및 자식 장치를 선택 하는 옵션이 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT hub로 이동 합니다.
1. 탐색 메뉴에서 **IoT Edge** 를 선택 합니다.
1. **IoT Edge 디바이스 추가** 를 선택합니다.
1. 장치 ID 및 인증 설정 설정과 함께 **부모 장치를 설정** 하거나 **자식 장치를 선택할** 수 있습니다.
1. 부모 또는 자식으로 사용할 장치를 하나 이상 선택 합니다.

기존 장치에 대 한 부모/자식 관계를 만들거나 관리할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT hub로 이동 합니다.
1. 탐색 메뉴에서 **IoT Edge** 를 선택 합니다.
1. **IoT Edge 장치** 목록에서 관리 하려는 장치를 선택 합니다.
1. **부모 장치 설정** 또는 **자식 장치 관리** 를 선택 합니다.
1. 부모 또는 자식 장치를 추가 하거나 제거 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 대 한 [azure iot](/cli/azure/ext/azure-iot) 확장은 iot 리소스를 관리 하는 명령을 제공 합니다. 새 장치 id를 만들거나 기존 장치를 편집 하 여 IoT 및 IoT Edge 장치의 부모/자식 관계를 관리할 수 있습니다.

[Az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) of 명령 집합을 사용 하 여 지정 된 장치에 대 한 부모/자식 관계를 관리할 수 있습니다.

이 `create` 명령에는 장치를 만들 때 자식 장치를 추가 하 고 부모 장치를 설정 하기 위한 매개 변수가 포함 되어 있습니다.

,, 및 등의 추가 장치 id 명령을 사용 하 여 `add-children` `list-children` `remove-children` `get-parent` `set-parent` 기존 장치에 대 한 부모/자식 관계를 관리할 수 있습니다.

---

## <a name="prepare-certificates"></a>인증서 준비

동일한 게이트웨이 계층의 여러 장치에 일관 된 인증서 체인을 설치 하 여 보안 통신을 설정 해야 합니다. IoT Edge 장치 또는 IoT 리프 장치에 관계 없이 계층의 모든 장치에는 동일한 루트 CA 인증서의 복사본이 필요 합니다. 계층의 각 IoT Edge 장치는 해당 루트 CA 인증서를 해당 장치 CA 인증서의 루트로 사용 합니다.

이 설정을 사용 하면 각 다운스트림 IoT Edge 장치 또는 IoT 리프 장치가 연결 된 edgeHub에 공유 루트 CA 인증서로 서명 된 서버 인증서가 있는지 확인 하 여 부모 id를 확인할 수 있습니다.

<!-- TODO: certificate graphic -->

다음 인증서를 만듭니다.

* 지정 된 게이트웨이 계층의 모든 장치에 대 한 최상위 공유 인증서 인 **루트 CA 인증서** 입니다. 이 인증서는 모든 장치에 설치 됩니다.
* 루트 인증서 체인에 포함 하려는 **중간 인증서**
* 루트 및 중간 인증서에 의해 생성 된 **장치 CA 인증서** 및 **개인 키** 입니다. 게이트웨이 계층의 각 IoT Edge 장치에 대해 하나의 고유한 장치 CA 인증서가 필요 합니다.

>[!NOTE]
>현재 libiothsm의 제한으로 인해 2038 년 1 월 1 일 이후에 만료 되는 인증서를 사용할 수 없습니다.

자체 서명 된 인증 기관을 사용 하거나 신뢰할 수 있는 상업적 인증 기관 (예: Baltimore, Verisign, Digicert 또는 GlobalSign)에서 구매할 수 있습니다.

사용할 자체 인증서가 없는 경우 [데모 인증서를 만들어 IoT Edge 장치 기능을 테스트할](how-to-create-test-certificates.md)수 있습니다. 이 문서의 단계에 따라 하나의 루트 및 중간 인증서 집합을 만든 다음 각 장치에 대 한 IoT Edge 장치 CA 인증서를 만듭니다.

## <a name="configure-iot-edge-on-devices"></a>장치에서 IoT Edge 구성

게이트웨이로 IoT Edge을 설정 하는 단계는 다운스트림 장치로 IoT Edge를 설정 하는 단계와 매우 비슷합니다.

게이트웨이 검색을 사용 하도록 설정 하려면 모든 IoT Edge 게이트웨이 장치를 자식 장치가 로컬 네트워크에서 찾을 때 사용 하는 **호스트 이름** 으로 구성 해야 합니다. 모든 다운스트림 IoT Edge 장치는에 연결할 **parent_hostname** 구성 해야 합니다. 단일 IoT Edge 장치가 부모 및 자식 장치인 경우 두 매개 변수가 모두 필요 합니다.

보안 연결을 사용 하려면 게이트웨이 시나리오의 모든 IoT Edge 장치를 고유한 장치 CA 인증서와 게이트웨이 계층의 모든 장치에서 공유 하는 루트 CA 인증서의 복사본으로 구성 해야 합니다.

장치에 IoT Edge 이미 설치 되어 있어야 합니다. 그렇지 않은 경우 [Azure IoT Edge 런타임을 설치한](how-to-install-iot-edge.md) 다음 [대칭 키 인증](how-to-manual-provision-symmetric-key.md) 또는 [x.509 인증서 인증](how-to-manual-provision-x509.md)을 사용 하 여 장치를 프로 비전 하는 단계를 수행 합니다.

이 섹션의 단계에서는이 문서의 앞부분에서 설명한 **루트 ca 인증서** 및 **장치 ca 인증서와 개인 키** 를 참조 합니다. 다른 장치에서 인증서를 만든 경우이 장치에서 해당 인증서를 사용할 수 있도록 합니다. USB 드라이브와 같은 [Azure Key Vault](../key-vault/general/overview.md)서비스를 사용 하거나 [안전한 파일 복사](https://www.ssh.com/ssh/scp/)와 같은 기능을 사용 하 여 파일을 물리적으로 전송할 수 있습니다.

다음 단계를 사용 하 여 장치에 IoT Edge를 구성 합니다.

Linux에서 사용자 **iotedge** 에 인증서와 키를 보유 하는 디렉터리에 대 한 읽기 권한이 있는지 확인 합니다.

1. 이 IoT Edge 장치에 **루트 CA 인증서** 를 설치 합니다.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. 인증서 저장소를 업데이트 합니다.

   ```bash
   sudo update-ca-certificates
   ```

   이 명령은 하나의 인증서가/etc/ssl/certs.에 추가 되었음을 출력 해야 합니다.

1. IoT Edge 보안 디먼 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Config.xml 파일에서 **certificate** 섹션을 찾습니다. 인증서를 가리키도록 3 개의 인증서 필드를 업데이트 합니다. 형식을 사용 하는 파일 URI 경로를 제공 합니다 `file:///<path>/<filename>` .

   * **device_ca_cert** :이 장치에 고유한 장치 ca 인증서에 대 한 파일 URI 경로입니다.
   * **device_ca_pk** :이 장치에 고유한 장치 ca 개인 키에 대 한 파일 URI 경로입니다.
   * **trusted_ca_certs** : 게이트웨이 계층의 모든 장치에서 공유 하는 루트 ca 인증서에 대 한 파일 URI 경로입니다.

1. Config.xml 파일에서 **hostname** 매개 변수를 찾습니다. 호스트 이름을 FQDN (정규화 된 도메인 이름) 또는 IoT Edge 장치의 IP 주소로 업데이트 합니다.

   이 매개 변수 값은 다운스트림 장치에서이 게이트웨이에 연결 하는 데 사용 하는 것입니다. 호스트 이름은 기본적으로 컴퓨터 이름을 사용 하지만 다운스트림 장치를 연결 하려면 FQDN 또는 IP 주소가 필요 합니다.

   서버 인증서 일반 이름의 문자 제한인 64 자 보다 짧은 호스트 이름을 사용 합니다.

   게이트웨이 계층 구조 전체에서 호스트 이름 패턴과 일치 해야 합니다. Fqdn 또는 IP 주소 중 하나만 사용 합니다.

1. **이 장치가 자식 장치인 경우** **parent_hostname** 매개 변수를 찾습니다. 부모 장치의 FQDN 또는 IP 주소가 되도록 **parent_hostname** 필드를 업데이트 하 고 부모 구성 파일에서 호스트 이름으로 제공 된 것과 일치 합니다.

1. 이 기능은 공개 미리 보기 상태 이지만 시작 시 IoT Edge 에이전트의 공개 미리 보기 버전을 사용 하도록 IoT Edge 장치를 구성 해야 합니다.

   **에이전트** yaml 섹션을 찾고 이미지 값을 공개 미리 보기 이미지로 업데이트 합니다.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc1"
       auth: {}
   ```

1. `Ctrl+O`Config.xml 파일을 저장 () 하 고 닫습니다. `Ctrl+X`

1. 이전에 IoT Edge에 다른 인증서를 사용한 경우 다음 두 디렉터리의 파일을 삭제 하 여 새 인증서가 적용 되었는지 확인 합니다.

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. IoT Edge 서비스를 다시 시작 하 여 변경 내용을 적용 합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

1. 구성에서 오류를 확인 합니다.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >IoT Edge 검사 도구는 컨테이너를 사용 하 여 일부 진단 검사를 수행 합니다. 다운스트림 IoT Edge 장치에서이 도구를 사용 하려면 사용자가 `mcr.microsoft.com/azureiotedge-diagnostics:latest` 개인 컨테이너 레지스트리에 액세스 하거나 컨테이너 이미지를 포함할 수 있는지 확인 합니다.

## <a name="configure-runtime-modules-for-public-preview"></a>공개 미리 보기용 런타임 모듈 구성

이 기능은 공개 미리 보기 상태 이지만 IoT Edge 런타임 모듈의 공개 미리 보기 버전을 사용 하도록 IoT Edge 장치를 구성 해야 합니다. 이전 섹션에서는 시작할 때 edgeAgent를 구성 하는 단계에 대해 설명 합니다. 또한 장치에 대 한 배포에서 런타임 모듈을 구성 해야 합니다.

1. 공개 미리 보기 이미지를 사용 하도록 edgeHub 모듈을 구성 `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc1` 합니다.

1. EdgeHub 모듈에 대해 다음과 같은 환경 변수를 구성 합니다.

   | Name | 값 |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. 공개 미리 보기 이미지를 사용 하도록 edgeAgent 모듈을 구성 `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc1` 합니다.

## <a name="network-isolate-downstream-devices"></a>네트워크 격리 다운스트림 장치

이 문서에서 지금 까지의 단계는 게이트웨이 또는 다운스트림 장치로 IoT Edge 장치를 설정 하 고 둘 사이에 트러스트 된 연결을 만듭니다. 게이트웨이 장치는 인증 및 메시지 라우팅을 비롯 하 여 다운스트림 장치와 IoT Hub 간의 상호 작용을 처리 합니다. 다운스트림 IoT Edge 장치에 배포 된 모듈은 클라우드 서비스에 대 한 자체 연결을 만들 수 있습니다.

ISA-95 표준을 따르는 것과 같은 일부 네트워크 아키텍처는 인터넷 연결 수를 최소화 하기 위해 검색 합니다. 이러한 시나리오에서는 직접 인터넷에 연결 하지 않고도 다운스트림 IoT Edge 장치를 구성할 수 있습니다. 게이트웨이 장치를 통한 라우팅 IoT Hub 통신 외에도 다운스트림 IoT Edge 장치는 모든 클라우드 연결에 대해 게이트웨이 장치를 사용할 수 있습니다.

이 네트워크 구성에서는 게이트웨이 계층의 최상위 계층에 있는 IoT Edge 장치만 클라우드에 직접 연결 되어야 합니다. 하위 계층의 IoT Edge 장치는 부모 장치 또는 자식 장치와만 통신할 수 있습니다. 게이트웨이 장치의 특수 모듈은 다음을 비롯 한이 시나리오를 가능 하 게 합니다.

* **API 프록시 모듈** 은 다른 IoT Edge 장치가 있는 모든 IoT Edge 게이트웨이에서 필요 합니다. 즉, 하위 계층을 제외 하 고 게이트웨이 계층의 *모든 계층* 에 있어야 합니다. 이 모듈에서는 [nginx](https://nginx.org) 역방향 프록시를 사용 하 여 단일 포트에서 네트워크 계층을 통해 HTTP 데이터를 라우팅합니다. 모듈 쌍 및 환경 변수를 통해 매우 구성 가능 하므로 게이트웨이 시나리오 요구 사항에 맞게 조정할 수 있습니다.

* **Docker 레지스트리 모듈** 은 게이트웨이 계층의 *최상위 계층* 에 IoT Edge 게이트웨이에서 배포할 수 있습니다. 이 모듈은 하위 계층에서 모든 IoT Edge 장치를 대신 하 여 컨테이너 이미지를 검색 하 고 캐시 하는 역할을 담당 합니다. 최상위 계층에이 모듈을 배포 하는 대신 로컬 레지스트리를 사용 하거나 컨테이너 이미지를 장치에 수동으로 로드 하 고 모듈 끌어오기 정책을 **never** 로 설정 합니다.

* **IoT Edge의 Azure Blob Storage** 는 게이트웨이 계층의 *최상위 계층* 에 있는 IoT Edge 게이트웨이에서 배포할 수 있습니다. 이 모듈은 하위 계층에서 모든 IoT Edge 장치를 대신 하 여 blob을 업로드 하는 역할을 담당 합니다. Blob을 업로드 하는 기능을 사용 하면 모듈 로그 업로드 및 지원 번들 업로드와 같이 하위 계층에서 IoT Edge 장치에 대해 유용한 문제 해결 기능을 사용할 수도 있습니다.

### <a name="network-configuration"></a>네트워크 구성

최상위 계층의 각 게이트웨이 장치에 대해 네트워크 운영자는 다음을 수행 해야 합니다.

* 고정 IP 주소 또는 FQDN (정규화 된 도메인 이름)을 제공 합니다.
* 포트 443 (HTTPS) 및 5671 (AMQP)을 통해이 IP 주소에서 Azure IoT Hub 호스트 이름으로의 아웃 바운드 통신에 권한을 부여 합니다.
* 포트 443 (HTTPS)를 통해이 IP 주소에서 Azure Container Registry 호스트 이름으로의 아웃 바운드 통신에 권한을 부여 합니다.

  API 프록시 모듈은 한 번에 하나의 컨테이너 레지스트리에 대 한 연결만 처리할 수 있습니다. Microsoft Container Registry (mcr.microsoft.com)에서 제공 하는 공용 이미지를 포함 하 여 모든 컨테이너 이미지를 개인 컨테이너 레지스트리에 저장 하는 것이 좋습니다.

하위 계층의 각 게이트웨이 장치에 대해 네트워크 운영자는 다음을 수행 해야 합니다.

* 고정 IP 주소를 제공 합니다.
* 포트 443 (HTTPS) 및 5671 (AMQP)을 통해이 IP 주소에서 부모 게이트웨이의 IP 주소로 아웃 바운드 통신에 권한을 부여 합니다.

### <a name="deploy-modules-to-top-layer-devices"></a>최상위 계층 장치에 모듈 배포

게이트웨이 계층의 최상위 계층에 있는 IoT Edge 장치에는 장치에서 실행할 수 있는 작업 모듈 외에도 배포 해야 하는 필수 모듈 집합이 있습니다.

API 프록시 모듈은 가장 일반적인 게이트웨이 시나리오를 처리 하도록 사용자 지정 되도록 설계 되었습니다. 이 문서에서는 기본 구성에서 모듈을 설정 하는 방법과 예제를 제공 합니다. 자세한 내용 및 예제는 [게이트웨이 계층 구조 시나리오에 대 한 API 프록시 모듈 구성](how-to-configure-api-proxy-module.md) 을 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에서 IoT hub로 이동 합니다.
1. 탐색 메뉴에서 **IoT Edge** 를 선택 합니다.
1. **IoT Edge 장치** 목록에서 구성 하는 최상위 계층 장치를 선택 합니다.
1. **모듈 설정** 을 선택합니다.
1. **IoT Edge 모듈** 섹션에서 **추가** 를 선택한 다음 **Marketplace 모듈** 을 선택 합니다.
1. **IOT EDGE API 프록시** 모듈을 검색 하 고 선택 합니다.
1. 배포 된 모듈 목록에서 API 프록시 모듈의 이름을 선택 하 고 다음 모듈 설정을 업데이트 합니다.
   1. **환경 변수** 탭에서 **NGINX_DEFAULT_PORT** 값을로 업데이트 `443` 합니다.
   1. **컨테이너 만들기 옵션** 탭에서 포트 443를 참조 하도록 포트 바인딩을 업데이트 합니다.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   이러한 변경으로 포트 443에서 수신 대기 하도록 API 프록시 모듈을 구성 합니다. 포트 바인딩 충돌을 방지 하려면 포트 443에서 수신 하지 않도록 edgeHub 모듈을 구성 해야 합니다. 대신, API 프록시 모듈은 포트 443에서 edgeHub 트래픽을 라우팅합니다.

1. **런타임 설정** 을 선택 하 고 edgeHub 모듈 만들기 옵션을 찾습니다. 포트 5671 및 8883에 대 한 바인딩을 그대로 두고 포트 443에 대 한 포트 바인딩을 삭제 합니다.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. **저장** 을 선택 하 여 변경 내용을 런타임 설정에 저장 합니다.
1. **추가** 를 다시 선택 하 고 **모듈 IoT Edge** 선택 합니다.
1. 배포에 Docker 레지스트리 모듈을 추가 하려면 다음 값을 제공 합니다.
   1. **IoT Edge 모듈 이름** : `registry`
   1. **모듈 설정** 탭에서 **이미지 URI** :`registry:latest`
   1. **환경 변수** 탭에서 다음 환경 변수를 추가 합니다.

      * **이름** : `REGISTRY_PROXY_REMOTEURL` **값** :이 레지스트리 모듈이 매핑될 컨테이너 레지스트리의 URL입니다. 예들 들어 `https://myregistry.azurecr`입니다.

        레지스트리 모듈은 하나의 컨테이너 레지스트리에만 매핑할 수 있으므로 모든 컨테이너 이미지를 단일 개인 컨테이너 레지스트리에 포함 하는 것이 좋습니다.

      * **이름** : `REGISTRY_PROXY_USERNAME` **값** : 컨테이너 레지스트리를 인증 하는 사용자 이름입니다.

      * **이름** : `REGISTRY_PROXY_PASSWORD` **값** : 컨테이너 레지스트리를 인증 하기 위한 암호입니다.

   1. **컨테이너 만들기 옵션** 탭에서 다음을 붙여넣습니다.

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

1. **추가** 를 선택 하 여 모듈을 배포에 추가 합니다.
1. 다음 **: 경로** 를 선택 하 여 다음 단계로 이동 합니다.
1. 다운스트림 장치에서 IoT Hub에 연결 하기 위해 장치-클라우드 메시지를 사용 하도록 설정 하려면 모든 메시지를 IoT Hub에 전달 하는 경로를 포함 합니다. 예를 들면 다음과 같습니다.
    1. **이름** : `Route`
    1. **값** : `FROM /messages/* INTO $upstream`
1. **검토 + 만들기** 를 선택 하 여 최종 단계로 이동 합니다.
1. **만들기** 를 선택 하 여 장치에 배포 합니다.

### <a name="deploy-modules-to-lower-layer-devices"></a>하위 계층 장치에 모듈 배포

게이트웨이 계층 구조의 하위 계층에 있는 장치에는 장치에서 실행할 수 있는 워크 로드 모듈 외에도 배포 해야 하는 필수 모듈이 하나 있습니다. IoT Edge

#### <a name="route-container-image-pulls"></a>경로 컨테이너 이미지 가져오기

게이트웨이 계층에서 장치를 IoT Edge 하는 데 필요한 프록시 모듈에 대해 설명 하기 전에 하위 계층의 IoT Edge 장치가 해당 모듈 이미지를 가져오는 방법을 이해 해야 합니다.

하위 계층 장치를 클라우드에 연결할 수 없지만 일반적인 방법으로 모듈 이미지를 풀 하려면 게이트웨이 계층의 최상위 계층 장치를 이러한 요청을 처리 하도록 구성 해야 합니다. 최상위 계층 장치는 컨테이너 레지스트리에 매핑되는 Docker **레지스트리** 모듈을 실행 해야 합니다. 그런 다음 컨테이너 요청을 라우팅하도록 API 프록시 모듈을 구성 합니다. 이러한 세부 정보는이 문서의 이전 섹션에서 설명 합니다. 이 구성에서 하위 계층 장치는 클라우드 컨테이너 레지스트리를 가리키지 않고 최상위 계층에서 실행 되는 레지스트리를 가리켜야 합니다.

예를 들어를 호출 하는 대신 `mcr.microsoft.com/azureiotedge-api-proxy:latest` 하위 계층 장치는를 호출 해야 `$upstream:443/azureiotedge-api-proxy:latest` 합니다.

**$Upstream** 매개 변수는 하위 계층 장치의 부모를 가리키며 요청은 레지스트리 모듈에 대 한 컨테이너 요청을 라우팅하는 프록시 환경을 포함 하는 최상위 계층에 도달할 때까지 모든 계층을 통해 라우팅합니다. `:443`이 예제의 포트는 부모 장치에서 수신 대기 하는 API 프록시 모듈이 있는 포트로 바꾸어야 합니다.

API 프록시 모듈은 하나의 레지스트리 모듈로만 라우팅할 수 있으며 각 레지스트리 모듈은 하나의 컨테이너 레지스트리에만 매핑할 수 있습니다. 따라서 하위 계층 장치를 끌어올 이미지는 단일 컨테이너 레지스트리에 저장 해야 합니다.

하위 계층 장치가 게이트웨이 계층 구조를 통해 모듈 끌어오기 요청을 수행 하지 않도록 하려는 경우 또 다른 옵션은 로컬 레지스트리 솔루션을 관리 하는 것입니다. 또는 배포를 만들기 전에 모듈 이미지를 장치에 푸시한 다음 **Imagepullpolicy** 를 **never** 로 설정 합니다.

#### <a name="bootstrap-the-iot-edge-agent"></a>IoT Edge 에이전트 부트스트랩

IoT Edge 에이전트는 IoT Edge 장치에서 시작 하는 첫 번째 런타임 구성 요소입니다. 다운스트림 IoT Edge 장치가 시작 될 때 edgeAgent module 이미지에 액세스할 수 있는지 확인 한 다음 배포에 액세스 하 고 나머지 모듈 이미지를 시작할 수 있습니다.

IoT Edge 장치에서 config.xml 파일로 이동 하 여 인증 정보, 인증서 및 부모 호스트 이름을 제공 하는 경우 edgeAgent 컨테이너 이미지도 업데이트 합니다.

최상위 게이트웨이 장치가 컨테이너 이미지 요청을 처리 하도록 구성 된 경우를 `mcr.microsoft.com` 부모 호스트 이름 및 API 프록시 수신 대기 포트로 바꿉니다. 배포 매니페스트에서는를 바로 가기로 사용할 수 `$upstream` 있지만이 경우 라우팅을 처리 하려면 edgeHub 모듈이 필요 하며,이 시점에서 모듈이 시작 되지 않았습니다. 예를 들면 다음과 같습니다.

```yml
agent:
  name: "edgeAgent"
  type: "docker"
  env: {}
  config:
    image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2.0-rc1"
    auth: {}
```

로컬 컨테이너 레지스트리를 사용 하거나 장치에서 수동으로 컨테이너 이미지를 제공 하는 경우에는 그에 따라 config.xml 파일을 업데이트 합니다.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>런타임 구성 및 프록시 배포 모듈

**API 프록시 모듈** 은 클라우드와 모든 다운스트림 IoT Edge 장치 간의 모든 통신을 라우팅하는 데 필요 합니다. 다운스트림 IoT Edge 장치 없이 계층의 하단 계층에 있는 IoT Edge 장치에는이 모듈이 필요 하지 않습니다.

API 프록시 모듈은 가장 일반적인 게이트웨이 시나리오를 처리 하도록 사용자 지정 되도록 설계 되었습니다. 이 문서에서는 기본 구성에서 모듈을 설정 하는 단계에 대해 간략하게 설명 합니다. 자세한 내용 및 예제는 [게이트웨이 계층 구조 시나리오에 대 한 API 프록시 모듈 구성](how-to-configure-api-proxy-module.md) 을 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에서 IoT hub로 이동 합니다.
1. 탐색 메뉴에서 **IoT Edge** 를 선택 합니다.
1. **IoT Edge 장치** 목록에서 구성 하는 하위 계층 장치를 선택 합니다.
1. **모듈 설정** 을 선택합니다.
1. **IoT Edge 모듈** 섹션에서 **추가** 를 선택한 다음 **Marketplace 모듈** 을 선택 합니다.
1. **IOT EDGE API 프록시** 모듈을 검색 하 고 선택 합니다.
1. 배포 된 모듈 목록에서 API 프록시 모듈의 이름을 선택 하 고 다음 모듈 설정을 업데이트 합니다.
   1. **모듈 설정** 탭에서 **이미지 URI** 값을 업데이트 합니다. `mcr.microsoft.com`을 `$upstream:443`로 바꿉니다.
   1. **환경 변수** 탭에서 **NGINX_DEFAULT_PORT** 값을로 업데이트 `443` 합니다.
   1. **컨테이너 만들기 옵션** 탭에서 포트 443를 참조 하도록 포트 바인딩을 업데이트 합니다.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   이러한 변경으로 포트 443에서 수신 대기 하도록 API 프록시 모듈을 구성 합니다. 포트 바인딩 충돌을 방지 하려면 포트 443에서 수신 하지 않도록 edgeHub 모듈을 구성 해야 합니다. 대신, API 프록시 모듈은 포트 443에서 edgeHub 트래픽을 라우팅합니다.

1. **런타임 설정** 을 선택 합니다.
1. EdgeHub 모듈 설정을 업데이트 합니다.

   1. **이미지** 필드에서을로 바꿉니다 `mcr.microsoft.com` `$upstream:443` .
   1. **만들기 옵션** 필드에서 포트 443에 대 한 포트 바인딩을 삭제 하 고 포트 5671 및 8883에 대 한 바인딩을 그대로 둡니다.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. EdgeAgent 모듈 설정을 업데이트 합니다.
   1. **이미지** 필드에서을로 바꿉니다 `mcr.microsoft.com` `$upstream:443` .

1. **저장** 을 선택 하 여 변경 내용을 런타임 설정에 저장 합니다.
1. 다음 **: 경로** 를 선택 하 여 다음 단계로 이동 합니다.
1. 다운스트림 장치에서 IoT Hub에 연결 하기 위해 장치-클라우드 메시지를 사용 하도록 설정 하려면에 모든 메시지를 전달 하는 경로를 포함 `$upstream` 합니다. 낮은 계층 장치의 경우 업스트림 매개 변수는 부모 장치를 가리킵니다. 예를 들면 다음과 같습니다.
    1. **이름** : `Route`
    1. **값** : `FROM /messages/* INTO $upstream`
1. **검토 + 만들기** 를 선택 하 여 최종 단계로 이동 합니다.
1. **만들기** 를 선택 하 여 장치에 배포 합니다.

## <a name="next-steps"></a>다음 단계

[IoT Edge 디바이스를 게이트웨이로 사용하는 방법](iot-edge-as-gateway.md)

[게이트웨이 계층 시나리오에 대 한 API 프록시 모듈 구성](how-to-configure-api-proxy-module.md)
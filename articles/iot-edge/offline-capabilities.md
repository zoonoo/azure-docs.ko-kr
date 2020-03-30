---
title: 디바이스 오프라인으로 작동 - Azure IoT Edge | Microsoft Docs
description: IoT Edge 디바이스 및 모듈을 인터넷 연결 없이 오프라인으로 더 오래 작동하는 방법과 IoT Edge를 사용하여 일반 IoT 디바이스를 오프라인으로 작동하는 방법을 이해합니다.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 55512491121aee28404ab5f85b4223c67a2f0e1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80236071"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>IoT Edge 장치, 모듈 및 하위 장치에 대한 확장된 오프라인 기능 이해

Azure IoT Edge는 IoT Edge 장치에서 확장된 오프라인 작업을 지원하며, IoT Edge가 아닌 자식 장치에서도 오프라인 작업을 지원합니다. IoT Edge 장치가 IoT Hub에 연결할 수 있는 기회가 한 번 있는 한 해당 장치와 모든 하위 장치는 간헐적으로 또는 인터넷 연결이 없는 계속 작동할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

IoT Edge 디바이스가 오프라인으로 전환되면 IoT Edge 허브는 세 가지 역할을 맡습니다. 첫째, 업스트림으로 이동하는 모든 메시지를 저장하고 디바이스가 다시 연결될 때까지 보관합니다. 둘째, 모듈 및 자식 디바이스가 계속 작동할 수 있도록 IoT Hub를 대신하여 모듈 및 자식 디바이스를 인증합니다. 셋째, 일반적으로 IoT Hub를 통과하는 자식 디바이스 간 통신을 지원합니다.

다음 예제에서는 오프라인 모드에서 작동하는 IoT Edge 시나리오를 보여줍니다.

1. **디바이스 구성**

   IoT Edge 디바이스는 자동으로 오프라인 기능이 설정됩니다. 이 기능을 다른 IoT 디바이스로 확장하려면 IoT Hub의 디바이스 간에 부모-자식 관계를 선언해야 합니다. 그런 다음 할당된 상위 장치를 신뢰하도록 자식 장치를 구성하고 부모를 통해 게이트웨이로 장치-클라우드 통신을 라우팅합니다.

2. **IoT 허브와 동기화**

   적어도 IoT Edge 런타임을 설치한 후 IoT Edge 디바이스를 온라인에 연결하여 IoT Hub와 동기화해야 합니다. 이 동기화에서 IoT Edge 디바이스는 할당된 모든 자식 디바이스에 대한 세부 정보를 얻습니다. 또한 IoT Edge 디바이스는 오프라인 작업을 사용하도록 설정하고 원격 분석 메시지의 로컬 스토리지에 대한 설정을 검색하도록 로컬 캐시를 안전하게 업데이트합니다.

3. **오프라인으로 이동**

   IoT Hub와의 연결이 끊어진 동안 IoT Edge 디바이스, 배포된 모듈 및 자식 IoT 디바이스는 무기한 작동할 수 있습니다. 모듈과 자식 디바이스는 오프라인 상태에서 IoT Edge 허브로 인증하여 시작 및 다시 시작할 수 있습니다. IoT Hub에 업스트림 바인딩된 원격 분석 데이터는 로컬로 저장됩니다. 모듈 간 통신 또는 자식 IoT 디바이스 간 통신은 직접 메서드 또는 메시지를 통해 유지됩니다.

4. **IoT Hub로 다시 연결하고 다시 동기화**

   IoT Hub와의 연결이 복원되면 IoT Edge 디바이스가 다시 동기화됩니다. 로컬에 저장된 메시지는 즉시 IoT Hub로 전달되지만 연결 속도, IoT Hub 대기 시간 및 관련 요인에 따라 달라집니다. 저장된 순서대로 배달됩니다.

   모듈 및 디바이스의 desired 속성과 reported 속성 간 차이가 조정됩니다. IoT Edge 디바이스는 할당된 자식 IoT 디바이스에 변경 내용을 업데이트합니다.

## <a name="restrictions-and-limits"></a>제한 사항 및 제한

이 문서에서 설명하는 확장된 오프라인 기능은 [IoT Edge 버전 1.0.7 이상에서](https://github.com/Azure/azure-iotedge/releases)사용할 수 있습니다. 이보다 낮은 버전은 오프라인 기능 집합을 갖고 있습니다. 확장된 오프라인 기능이 없는 기존 IoT Edge 디바이스는 런타임 버전을 변경하여 업그레이드할 수 없지만, 새 IoT Edge 디바이스 ID를 사용하여 이러한 기능을 획득하도록 다시 구성해야 합니다.

IoT Edge가 아닌 장치만 하위 장치로 추가할 수 있습니다.

IoT Edge 장치와 할당된 자식 장치는 초기 일회성 동기화 후 무기한 오프라인으로 작동할 수 있습니다. 그러나 메시지 저장은 TTL(라이브 시간) 설정 및 메시지를 저장하는 데 사용할 수 있는 디스크 공간에 따라 달라집니다.

## <a name="set-up-parent-and-child-devices"></a>상위 및 자식 장치 설정

IoT Edge 장치가 확장된 오프라인 기능을 자식 IoT 장치로 확장하려면 두 단계를 완료해야 합니다. 먼저 Azure Portal에서 부모-자식 관계를 선언합니다. 둘째, 상위 장치와 하위 장치 간의 신뢰 관계를 만든 다음 부모대 클라우드 통신을 구성하여 부모를 게이트웨이로 구성합니다.

### <a name="assign-child-devices"></a>자식 디바이스 할당

하위 장치는 동일한 IoT Hub에 등록된 모든 비 IoT Edge 장치일 수 있습니다. 상위 장치에는 여러 자식 장치가 있을 수 있지만 하위 장치에는 상위 장치가 하나만 있습니다. Azure 포털을 통해, Azure CLI를 사용하거나, IoT Hub 서비스 SDK를 사용하는 세 가지 옵션이 자식 장치를 에지 장치로 설정합니다.

다음 섹션에서는 기존 IoT 장치에 대한 IoT Hub에서 부모/하위 관계를 선언하는 방법에 대한 예제를 제공합니다. 자식 장치에 대한 새 장치 ID를 만드는 경우 자세한 내용은 [Azure IoT Hub에 다운스트림 장치 인증을](how-to-authenticate-downstream-device.md) 참조하세요.

#### <a name="option-1-iot-hub-portal"></a>옵션 1: IoT 허브 포털

새 장치를 만들 때 부모-자식 관계를 선언할 수 있습니다. 또는 기존 장치의 경우 상위 IoT Edge 장치 또는 하위 IoT 장치의 장치 세부 정보 페이지에서 관계를 선언할 수 있습니다.

   ![IoT Edge 디바이스 세부 정보 페이지에서 자식 디바이스 관리](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>옵션 2: `az` 명령줄 도구 사용

[IoT](https://github.com/azure/azure-iot-cli-extension) 확장(v0.7.0 이상)이 있는 [Azure 명령줄 인터페이스를](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 사용하여 장치 [ID](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 하위 명령으로 상위 자식 관계를 관리할 수 있습니다. 아래 예제에서는 쿼리를 사용하여 허브의 모든 비 IoT Edge 장치를 IoT Edge 장치의 하위 장치로 할당합니다.

```azurecli
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name
```

[쿼리를](../iot-hub/iot-hub-devguide-query-language.md) 수정하여 다른 장치 하위 집합을 선택할 수 있습니다. 큰 장치 집합을 지정하는 경우 명령은 몇 초 정도 걸릴 수 있습니다.

#### <a name="option-3-use-iot-hub-service-sdk"></a>옵션 3: IoT 허브 서비스 SDK 사용

마지막으로 C#, Java 또는 Node.js IoT Hub 서비스 SDK를 사용하여 프로그래밍 방식으로 상위 자식 관계를 관리할 수 있습니다. 다음은 C# SDK를 사용하여 [자식 장치를 할당하는 예입니다.](https://aka.ms/set-child-iot-device-c-sharp)

### <a name="set-up-the-parent-device-as-a-gateway"></a>상위 장치를 게이트웨이로 설정

부모/자식 관계를 IoT Hub에서 자체 ID를 가지고 있지만 부모를 통해 클라우드를 통해 통신하는 투명한 게이트웨이로 생각할 수 있습니다. 보안 통신을 위해 하위 장치는 상위 장치가 신뢰할 수 있는 원본에서 온지 확인할 수 있어야 합니다. 그렇지 않으면 타사에서 부모를 가장하고 통신을 가로채는 악성 장치를 설정할 수 있습니다.

이 신뢰 관계를 만드는 한 가지 방법은 다음 문서에서 자세히 설명합니다.

* [IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md)
* [다운스트림(하위) 장치를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>DNS 서버 지정

견고성을 향상하려면 사용자 환경에서 사용되는 DNS 서버 주소를 지정하는 것이 좋습니다. IoT Edge에 대한 DNS 서버를 설정하려면 Edge Agent 모듈의 해상도가 ['빈 구성 파일'을 지속적으로 보고하고](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) 문제 해결 문서에서 장치에서 시작되는 모듈이 없음을 참조하십시오.

## <a name="optional-offline-settings"></a>선택적 오프라인 설정

장치가 오프라인 상태가 되면 IoT Edge 상위 장치는 연결이 다시 설정될 때까지 모든 장치-클라우드 메시지를 저장합니다. IoT Edge 허브 모듈은 오프라인 메시지의 저장 및 전달을 관리합니다. 장기간 오프라인 상태가 될 수 있는 장치의 경우 두 개의 IoT Edge 허브 설정을 구성하여 성능을 최적화합니다.

첫째, IoT Edge 허브가 기기가 다시 연결될 수 있도록 메시지를 충분히 오래 유지할 수 있도록 라이브 설정 시간을 늘립니다. 그런 다음, 메시지 스토리지에 대한 추가 디스크 공간을 추가합니다.

### <a name="time-to-live"></a>TTL(Time to live)

TTL(Time to Live) 설정은 메시지가 만료되기 전까지 대기할 수 있는 시간의 양(초)입니다. 기본값은 7200초(2시간)입니다. 최대값은 약 20억 인 정수 변수의 최대값에 의해서만 제한됩니다.

이 설정은 모듈 쌍에 저장되는 IoT Edge 허브의 desired 속성입니다. Azure 포털에서 또는 배포 매니페스트에서 직접 구성할 수 있습니다.

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>시스템 모듈을 위한 호스트 스토리지

메시지 및 모듈 상태 정보는 기본적으로 IoT Edge 허브의 로컬 컨테이너 파일 시스템에 저장됩니다. 특히 오프라인으로 작동할 때 안정성을 높이기 위해 호스트 IoT Edge 장치의 저장소를 전용으로 사용할 수도 있습니다. 자세한 내용은 [모듈이 장치의 로컬 저장소에 액세스할 수 있도록 제공을](how-to-access-host-storage-from-module.md) 참조하십시오.

## <a name="next-steps"></a>다음 단계

상위/하위 장치 연결에 대해 투명 게이트웨이를 설정하는 방법에 대해 자세히 알아봅니다.

* [IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md)
* [Azure IoT Hub에 다운스트림 디바이스 인증](how-to-authenticate-downstream-device.md)
* [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)

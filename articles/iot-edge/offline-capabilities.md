---
title: Azure IoT Edge 오프라인 기능 | Microsoft Docs
description: IoT Edge 디바이스 및 모듈을 오프라인으로 더 오래 작동하는 방법과 IoT Edge를 사용하여 일반 IoT 디바이스를 오프라인으로 작동하는 방법을 이해합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3ab775d57ba188930cc66b0fa1655307e9a78179
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284645"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>IoT Edge 디바이스, 모듈 및 자식 디바이스용 확장 오프라인 기능(미리 보기)을 이해합니다.

Azure IoT Edge는 IoT Edge 디바이스에서 확장된 오프라인 작업을 지원하고, 비 Edge 자식 디바이스에서도 오프라인 작업을 지원합니다. IoT Edge 디바이스를 IoT Hub에 연결할 수 있는 한, 해당 디바이스 및 자식 디바이스는 일시적으로 인터넷에 연결하여 또는 인터넷 연결 없이 계속 작동할 수 있습니다. 

>[!NOTE]
>IoT Edge에 대한 오프라인 지원은 현재 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)입니다.

## <a name="how-it-works"></a>작동 방법

IoT Edge 디바이스가 오프라인으로 전환되면 Edge 허브는 세 가지 역할을 맡습니다. 첫째, 업스트림으로 이동하는 모든 메시지를 저장하고 디바이스가 다시 연결될 때까지 보관합니다. 둘째, 모듈 및 자식 디바이스가 계속 작동할 수 있도록 IoT Hub를 대신하여 모듈 및 자식 디바이스를 인증합니다. 셋째, 일반적으로 IoT Hub를 통과하는 자식 디바이스 간 통신을 지원합니다. 

다음 예제에서는 오프라인 모드에서 작동하는 IoT Edge 시나리오를 보여줍니다.

1. **IoT Edge 장치를 구성합니다.**

   IoT Edge 디바이스는 자동으로 오프라인 기능이 설정됩니다. 이 기능을 다른 IoT 디바이스로 확장하려면 IoT Hub의 디바이스 간에 부모-자식 관계를 선언해야 합니다. 

2. **IoT Hub와 동기화합니다.**

   적어도 IoT Edge 런타임을 설치한 후 IoT Edge 디바이스를 온라인에 연결하여 IoT Hub와 동기화해야 합니다. 이 동기화에서 IoT Edge 디바이스는 할당된 모든 자식 디바이스에 대한 세부 정보를 얻습니다. 또한 IoT Edge 디바이스는 오프라인 작업을 사용하도록 설정하고 원격 분석 메시지의 로컬 저장소에 대한 설정을 검색하도록 로컬 캐시를 안전하게 업데이트합니다. 

3. **오프라인으로 전환합니다.**

   IoT Hub와의 연결이 끊어진 동안 IoT Edge 디바이스, 배포된 모듈 및 자식 IoT 디바이스는 무기한 작동할 수 있습니다. 모듈과 자식 디바이스는 오프라인인 동안 Edge 허브로 인증하여 시작 및 다시 시작할 수 있습니다. IoT Hub에 업스트림 바인딩된 원격 분석 데이터는 로컬로 저장됩니다. 모듈 간 통신 또는 자식 IoT 디바이스 간 통신은 직접 메서드 또는 메시지를 통해 유지됩니다. 

4. **IoT Hub와 다시 연결하고 동기화합니다.**

   IoT Hub와의 연결이 복원되면 IoT Edge 디바이스가 다시 동기화됩니다. 로컬에 저장된 메시지는 저장된 순서대로 전달됩니다. 모듈 및 디바이스의 desired 속성과 reported 속성 간 차이가 조정됩니다. IoT Edge 디바이스는 할당된 자식 IoT 디바이스에 변경 내용을 업데이트합니다.

## <a name="restrictions-and-limits"></a>제한 사항 및 제한

이 문서에서 설명한 확장된 오프라인 기능은 [IoT Edge 버전 1.0.4 이상](https://github.com/Azure/azure-iotedge/releases)에서 사용할 수 있습니다. 이보다 낮은 버전은 오프라인 기능 집합을 갖고 있습니다. 확장된 오프라인 기능이 없는 기존 IoT Edge 디바이스는 런타임 버전을 변경하여 업그레이드할 수 없지만, 새 IoT Edge 디바이스 ID를 사용하여 이러한 기능을 획득하도록 다시 구성해야 합니다. 

확장된 오프라인 지원은 IoT Hub를 사용할 수 있는 모든 지역(미국 동부 지역 **제외**)에서 사용할 수 있습니다.

비 Edge IoT 디바이스는 오직 한 대만 자식 디바이스로 추가할 수 있습니다. 

IoT Edge 디바이스 및 할당된 자식 디바이스는 초기 일회성 동기화 후 오프라인으로 무기한 작동할 수 있습니다. 그러나 메시지 저장소는 TTL(Time to Live) 및 메시지 저장에 사용 가능한 디스크 공간에 따라 달라집니다. 

## <a name="set-up-an-edge-device"></a>Edge 디바이스 설정

IoT Edge 디바이스의 확장된 오프라인 기능을 자식 IoT 디바이스로 확장하려면 Azure Portal에서 부모-자식 관계를 선언해야 합니다.

### <a name="assign-child-devices"></a>자식 디바이스 할당

동일한 IoT Hub에 등록된 모든 비 Edge 디바이스는 자식 디바이스가 될 수 있습니다. 새 디바이스를 만들 때나 부모 IoT Edge 디바이스 또는 자식 IoT 디바이스의 디바이스 세부 정보 페이지에서 부모-자식 관계를 관리할 수 있습니다. 

   ![IoT Edge 디바이스 세부 정보 페이지에서 자식 디바이스 관리](./media/offline-capabilities/manage-child-devices.png)

부모 디바이스는 여러 자식 디바이스를 가질 수 있지만, 자식 디바이스는 한 부모만 가질 수 있습니다.

### <a name="specifying-dns-servers"></a>DNS 서버 지정 

견고성을 향상시키려면 환경에서 사용되는 DNS 서버 주소를 지정하는 것이 좋습니다. 예를 들어 Linux에서 다음이 포함되도록 **/etc/docker/daemon.json**을 업데이트합니다(파일을 만들어야 할 수도 있음).

```
{
    "dns": [“1.1.1.1”]
}
```

로컬 DNS 서버를 사용하는 경우 1.1.1.1을 로컬 DNS 서버의 IP 주소로 바꿉니다. 변경 내용이 적용되도록 Docker 서비스를 다시 시작합니다.


## <a name="optional-offline-settings"></a>선택적 오프라인 설정

디바이스를 오프라인으로 오랫동안 실행한 후 생성된 모든 메시지를 수집하려면 모든 메시지를 저장할 수 있도록 Edge 허브를 구성해야 합니다. 장기 메시지 저장소를 사용하도록 Edge 허브를 변경하는 두 가지 방법이 있습니다. 먼저 TTL(Time to Live)을 늘린 다음, 메시지 저장을 위한 추가 디스크 공간을 추가합니다. 

### <a name="time-to-live"></a>TTL(Time to live)

TTL(Time to Live) 설정은 메시지가 만료되기 전까지 대기할 수 있는 시간의 양(초)입니다. 기본값은 7200초(2시간)입니다. 

이 설정은 모듈 쌍에 저장되는 Edge 허브의 desired 속성입니다. Azure Portal의 **고급 Edge 런타임 설정 구성** 섹션에서 또는 배포 매니페스트에서 직접 구성할 수 있습니다. 

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

### <a name="additional-offline-storage"></a>추가 오프라인 저장소

기본적으로 메시지는 Edge 허브 컨테이너의 파일 시스템에 저장됩니다. 저장소가 오프라인 요구 사항을 처리하기에 충분하지 않은 경우 IoT Edge 디바이스의 로컬 저장소를 전용으로 사용할 수 있습니다. 컨테이너의 저장소 폴더를 가리키는 Edge 허브에 대한 환경 변수를 만들어야 합니다. 그런 다음, 만들기 옵션을 사용하여 해당 저장소 폴더를 호스트 머신의 폴더에 바인딩합니다. 

Azure Portal의 **고급 Edge 런타임 설정 구성** 섹션에서 Edge 허브 모듈의 환경 변수 및 만들기 옵션을 구성할 수 있습니다. 또는 배포 매니페스트에서 직접 구성할 수도 있습니다. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"Binds\":[\"<HostStoragePath>:<ModuleStoragePath>\"],\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

`<HostStoragePath>` 및 `<ModuleStoragePath>`를 호스트 및 모듈 저장소 경로로 바꿉니다. 호스트 및 모듈 저장소 경로는 모두 절대 경로여야 합니다.  예를 들어 `\"Binds\":[\"/etc/iotedge/storage/:/iotedge/storage/"`는 `/etc/iotedge/storage` 경로 호스트가 `/iotedge/storage/` 컨테이너 경로에 매핑되어 있다는 의미입니다.  [docker 문서](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)에서 createOptions에 대한 자세한 세부 정보를 찾을 수도 있습니다.

## <a name="next-steps"></a>다음 단계

[투명한 게이트웨이](how-to-create-transparent-gateway.md) 시나리오에서 확장된 오프라인 작업을 사용하도록 설정합니다.

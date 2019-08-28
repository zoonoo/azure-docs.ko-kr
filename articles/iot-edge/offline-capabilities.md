---
title: 디바이스 오프라인으로 작동 - Azure IoT Edge | Microsoft Docs
description: IoT Edge 디바이스 및 모듈을 인터넷 연결 없이 오프라인으로 더 오래 작동하는 방법과 IoT Edge를 사용하여 일반 IoT 디바이스를 오프라인으로 작동하는 방법을 이해합니다.
author: kgremban
ms.author: kgremban
ms.date: 08/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6d82b353f8b485b4441853b7ff8e70e7d69f4d6a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986976"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>IoT Edge 장치, 모듈 및 자식 장치에 대 한 확장 된 오프 라인 기능 이해

Azure IoT Edge은 IoT Edge 장치에서 확장 된 오프 라인 작업을 지원 하 고 IoT Edge 없는 자식 장치에서 오프 라인 작업을 지원 합니다. IoT Edge 디바이스를 IoT Hub에 연결할 수 있는 한, 해당 디바이스 및 자식 디바이스는 일시적으로 인터넷에 연결하여 또는 인터넷 연결 없이 계속 작동할 수 있습니다. 


## <a name="how-it-works"></a>작동 방법

IoT Edge 디바이스가 오프라인으로 전환되면 IoT Edge 허브는 세 가지 역할을 맡습니다. 첫째, 업스트림으로 이동하는 모든 메시지를 저장하고 디바이스가 다시 연결될 때까지 보관합니다. 둘째, 모듈 및 자식 디바이스가 계속 작동할 수 있도록 IoT Hub를 대신하여 모듈 및 자식 디바이스를 인증합니다. 셋째, 일반적으로 IoT Hub를 통과하는 자식 디바이스 간 통신을 지원합니다. 

다음 예제에서는 오프라인 모드에서 작동하는 IoT Edge 시나리오를 보여줍니다.

1. **장치 구성**

   IoT Edge 디바이스는 자동으로 오프라인 기능이 설정됩니다. 이 기능을 다른 IoT 디바이스로 확장하려면 IoT Hub의 디바이스 간에 부모-자식 관계를 선언해야 합니다. 그런 다음 할당 된 부모 장치를 신뢰 하도록 자식 장치를 구성 하 고 부모를 통해 장치-클라우드 통신을 게이트웨이로 라우팅합니다. 

2. **IoT Hub와 동기화**

   적어도 IoT Edge 런타임을 설치한 후 IoT Edge 디바이스를 온라인에 연결하여 IoT Hub와 동기화해야 합니다. 이 동기화에서 IoT Edge 디바이스는 할당된 모든 자식 디바이스에 대한 세부 정보를 얻습니다. 또한 IoT Edge 디바이스는 오프라인 작업을 사용하도록 설정하고 원격 분석 메시지의 로컬 스토리지에 대한 설정을 검색하도록 로컬 캐시를 안전하게 업데이트합니다. 

3. **오프 라인으로 전환**

   IoT Hub와의 연결이 끊어진 동안 IoT Edge 디바이스, 배포된 모듈 및 자식 IoT 디바이스는 무기한 작동할 수 있습니다. 모듈과 자식 디바이스는 오프라인 상태에서 IoT Edge 허브로 인증하여 시작 및 다시 시작할 수 있습니다. IoT Hub에 업스트림 바인딩된 원격 분석 데이터는 로컬로 저장됩니다. 모듈 간 통신 또는 자식 IoT 디바이스 간 통신은 직접 메서드 또는 메시지를 통해 유지됩니다. 

4. **IoT Hub 다시 연결 및 다시 동기화**

   IoT Hub와의 연결이 복원되면 IoT Edge 디바이스가 다시 동기화됩니다. 로컬에 저장된 메시지는 저장된 순서대로 전달됩니다. 모듈 및 디바이스의 desired 속성과 reported 속성 간 차이가 조정됩니다. IoT Edge 디바이스는 할당된 자식 IoT 디바이스에 변경 내용을 업데이트합니다.

## <a name="restrictions-and-limits"></a>제한 사항 및 제한

이 문서에서 설명 하는 확장 된 오프 라인 기능은 [IoT Edge 버전 1.0.7 이상](https://github.com/Azure/azure-iotedge/releases)에서 사용할 수 있습니다. 이보다 낮은 버전은 오프라인 기능 집합을 갖고 있습니다. 확장된 오프라인 기능이 없는 기존 IoT Edge 디바이스는 런타임 버전을 변경하여 업그레이드할 수 없지만, 새 IoT Edge 디바이스 ID를 사용하여 이러한 기능을 획득하도록 다시 구성해야 합니다. 

확장된 오프라인 지원은 IoT Hub를 사용할 수 있는 모든 지역(미국 동부 지역 **제외**)에서 사용할 수 있습니다.

IoT Edge 아닌 장치만 자식 장치로 추가할 수 있습니다. 

IoT Edge 디바이스 및 할당된 자식 디바이스는 초기 일회성 동기화 후 오프라인으로 무기한 작동할 수 있습니다. 그러나 메시지 스토리지는 TTL(Time to Live) 및 메시지 저장에 사용 가능한 디스크 공간에 따라 달라집니다. 

## <a name="set-up-parent-and-child-devices"></a>부모 및 자식 장치 설정

확장 된 오프 라인 기능을 자식 IoT 장치로 확장 하는 IoT Edge 장치의 경우 두 단계를 완료 해야 합니다. 먼저 Azure Portal에서 부모-자식 관계를 선언 합니다. 둘째, 부모 장치와 모든 자식 장치 간에 트러스트 관계를 만든 다음 장치-클라우드 통신을 구성 하 여 부모를 게이트웨이로 이동 합니다. 

### <a name="assign-child-devices"></a>자식 디바이스 할당

하위 장치는 동일한 IoT Hub에 등록 된 IoT Edge 이외의 장치 일 수 있습니다. 부모 장치에는 자식 장치가 여러 개 있을 수 있지만 자식 장치에는 하나의 부모만 있습니다. 에 지 장치에 대 한 자식 장치를 설정 하는 세 가지 옵션은 Azure Portal, Azure CLI 사용 하거나 IoT Hub 서비스 SDK를 사용 하는 것입니다. 

다음 섹션에서는 기존 IoT 장치에 대 한 IoT Hub에서 부모/자식 관계를 선언 하는 방법에 대 한 예를 제공 합니다. 자식 장치에 대 한 새 장치 id를 만드는 경우 자세한 내용은 [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md) 을 참조 하세요.

#### <a name="option-1-iot-hub-portal"></a>옵션 1: IoT Hub 포털

새 장치를 만들 때 부모-자식 관계를 선언할 수 있습니다. 또는 기존 장치의 경우 부모 IoT Edge 장치 또는 자식 IoT 장치의 장치 세부 정보 페이지에서 관계를 선언할 수 있습니다. 

   ![IoT Edge 디바이스 세부 정보 페이지에서 자식 디바이스 관리](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>옵션 2: `az` 명령줄 도구 사용

[IoT 확장](https://github.com/azure/azure-iot-cli-extension) (v 0.7.0 이상)과 함께 [Azure 명령줄 인터페이스](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 를 사용 하 여 [장치 id](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 하위 명령으로 부모 자식 관계를 관리할 수 있습니다. 아래 예제에서는 쿼리를 사용 하 여 허브에 있는 모든 비 IoT Edge 장치를 IoT Edge 장치의 자식 장치로 할당 합니다. 

```shell
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

[쿼리](../iot-hub/iot-hub-devguide-query-language.md) 를 수정 하 여 다른 장치 하위 집합을 선택할 수 있습니다. 많은 장치 집합을 지정 하는 경우이 명령은 몇 초 정도 걸릴 수 있습니다.

#### <a name="option-3-use-iot-hub-service-sdk"></a>옵션 3: IoT Hub 서비스 SDK 사용 

마지막으로 C#, Java 또는 Node.js IOT HUB 서비스 SDK를 사용 하 여 프로그래밍 방식으로 부모 자식 관계를 관리할 수 있습니다. SDK를 C# 사용 하 여 [자식 장치를 할당](https://aka.ms/set-child-iot-device-c-sharp) 하는 예제는 다음과 같습니다.

### <a name="set-up-the-parent-device-as-a-gateway"></a>부모 장치를 게이트웨이로 설정

부모/자식 관계는 투명 게이트웨이로 간주할 수 있습니다. 여기서 자식 장치는 IoT Hub에서 자체 id를 갖지만 부모를 통해 클라우드를 통해 통신 합니다. 안전한 통신을 위해 자식 장치는 부모 장치가 신뢰할 수 있는 원본에서 온 것임을 확인할 수 있어야 합니다. 그렇지 않으면 제 3 자가 부모를 가장 하 고 통신을 가로챌 악성 장치를 설정할 수 있습니다. 

이 트러스트 관계를 만드는 한 가지 방법은 다음 문서에 자세히 설명 되어 있습니다. 
* [IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md)
* [다운스트림 (자식) 장치를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>DNS 서버 지정 

견고성을 향상 시키려면 사용자 환경에서 사용 되는 DNS 서버 주소를 지정 하는 것이 좋습니다. [문제 해결 문서에서 DNS 서버를 설정](troubleshoot.md#resolution-7)하는 두 가지 옵션을 참조 하세요.

## <a name="optional-offline-settings"></a>선택적 오프라인 설정

장치가 오프 라인으로 전환 되는 경우 연결을 다시 설정할 때까지 IoT Edge 부모 장치에서 모든 장치-클라우드 메시지를 저장 합니다. IoT Edge 허브 모듈은 오프 라인 메시지의 저장 및 전달을 관리 합니다. 오랜 시간 동안 오프 라인으로 전환할 수 있는 장치의 경우 두 개의 IoT Edge 허브 설정을 구성 하 여 성능을 최적화 합니다. 

먼저 IoT Edge 허브가 장치를 다시 연결 하는 데 충분 한 시간 동안 메시지를 유지할 수 있도록 시간을 라이브 설정으로 늘립니다. 그런 다음, 메시지 스토리지에 대한 추가 디스크 공간을 추가합니다. 

### <a name="time-to-live"></a>TTL(Time to Live)

TTL(Time to Live) 설정은 메시지가 만료되기 전까지 대기할 수 있는 시간의 양(초)입니다. 기본값은 7200초(2시간)입니다. 최 댓 값은 정수 변수의 최 댓 값 (20억)에 의해서만 제한 됩니다. 

이 설정은 모듈 쌍에 저장되는 IoT Edge 허브의 desired 속성입니다. Azure Portal 또는 배포 매니페스트에서 직접 구성할 수 있습니다. 

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

### <a name="host-storage-for-system-modules"></a>시스템 모듈의 호스트 저장소

메시지 및 모듈 상태 정보는 기본적으로 IoT Edge 허브의 로컬 컨테이너 파일 시스템에 저장 됩니다. 안정성을 향상 시키기 위해 특히 오프 라인으로 작업 하는 경우 호스트 IoT Edge 장치에 전용 저장소를 지정할 수 있습니다.

호스트 시스템에서 저장소를 설정 하려면 컨테이너의 저장소 폴더를 가리키는 IoT Edge 허브 및 IoT Edge 에이전트에 대 한 환경 변수를 만듭니다. 그런 다음, 만들기 옵션을 사용하여 해당 스토리지 폴더를 호스트 머신의 폴더에 바인딩합니다. 

**고급 Edge 런타임 설정 구성** 섹션에서 Azure Portal의 IoT Edge 허브 모듈에 대한 환경 변수 및 만들기 옵션을 구성할 수 있습니다. 

1. IoT Edge 허브와 IoT Edge 에이전트 모두에서 모듈의 디렉터리를 가리키는 **Storagefolder** 라는 환경 변수를 추가 합니다.
1. IoT Edge 허브와 IoT Edge 에이전트 모두에 대해 바인딩을 추가 하 여 호스트 컴퓨터의 로컬 디렉터리를 모듈의 디렉터리에 연결 합니다. 이는 아래와 같이 함수의 반환값을 데이터 프레임으로 바로 변환하는 데 사용할 수 있음을 나타냅니다. 

   ![로컬 저장소에 대 한 만들기 옵션 및 환경 변수 추가](./media/offline-capabilities/offline-storage.png)

또는 배포 매니페스트에서 직접 로컬 저장소를 구성할 수 있습니다. 이는 아래와 같이 함수의 반환값을 데이터 프레임으로 바로 변환하는 데 사용할 수 있음을 나타냅니다. 

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

`<HostStoragePath>` 및`<ModuleStoragePath>` 를 호스트 및 모듈 저장소 경로로 바꾸고, 두 값이 모두 절대 경로 여야 합니다. 

예를 들어 `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]`는 컨테이너에서 **/iotedge/storage/** 디렉터리에 매핑된 사용자의 호스트 시스템의 **/etc/iotedge/storage** 디렉터리를 의미합니다. 또는 Windows 시스템에 대한 또 다른 예로 `"Binds":["C:\\temp:C:\\contemp"]`는 컨테이너에서 **C:\\contemp** 디렉터리에 매핑된 사용자의 호스트 시스템의 **C:\\temp** 디렉터리를 의미합니다. 

Linux 장치에서 IoT Edge 허브의 사용자 프로필 UID 1000에 호스트 시스템 디렉터리에 대 한 읽기, 쓰기 및 실행 권한이 있는지 확인 합니다. 이러한 권한은 IoT Edge 허브가 디렉터리에 메시지를 저장 하 고 나중에 검색할 수 있도록 하기 위해 필요 합니다. IoT Edge 에이전트는 루트로 작동 하므로 추가 권한이 필요 하지 않습니다. 를 사용 `chown` 하 여 디렉터리 소유자를 변경한 다음 `chmod` 사용 권한을 변경 하는 등 Linux 시스템에서 디렉터리 권한을 관리 하는 방법에는 여러 가지가 있습니다. 이는 아래와 같이 함수의 반환값을 데이터 프레임으로 바로 변환하는 데 사용할 수 있음을 나타냅니다.

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

[Docker 문서](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)에서 만들기 옵션에 대 한 자세한 내용을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

부모/자식 장치 연결에 대해 투명 게이트웨이를 설정 하는 방법에 대해 자세히 알아보세요. 

* [IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md)
* [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md)
* [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)

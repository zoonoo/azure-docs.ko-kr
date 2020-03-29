---
title: 모듈에서 IoT Edge 장치 로컬 저장소 사용 - Azure IoT Edge | 마이크로 소프트 문서
description: 환경 변수를 사용하고 옵션을 만들어 IoT Edge 장치 로컬 스토리지에 대한 모듈 액세스를 활성화합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434534"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>모듈에 디바이스의 로컬 스토리지에 대한 액세스 권한 부여

Azure 저장소 서비스를 사용하거나 장치의 컨테이너 저장소에 데이터를 저장하는 것 외에도 호스트 IoT Edge 장치 자체의 저장소를 사용하여 특히 오프라인으로 작동할 때 안정성을 향상시킬 수 있습니다.

## <a name="link-module-storage-to-device-storage"></a>장치 저장 장치에 대한 링크 모듈 스토리지

모듈 저장소에서 호스트 시스템의 저장소에 대한 링크를 활성화하려면 컨테이너의 저장소 폴더를 가리키는 모듈에 대한 환경 변수를 만듭니다. 그런 다음, 만들기 옵션을 사용하여 해당 스토리지 폴더를 호스트 머신의 폴더에 바인딩합니다.

예를 들어 IoT Edge 허브에서 메시지를 장치의 로컬 저장소에 저장하고 나중에 검색하도록 설정하려는 경우 **런타임 설정** 섹션에서 Azure Portal에서 환경 변수 및 만들기 옵션을 구성할 수 있습니다.

1. IoT Edge 허브와 IoT Edge 에이전트 모두모듈의 디렉터리를 가리키는 **storageFolder라는** 환경 변수를 추가합니다.
1. IoT Edge 허브와 IoT Edge 에이전트 모두 바인딩을 추가하여 호스트 컴퓨터의 로컬 디렉터리를 모듈의 디렉터리에 연결합니다. 예를 들어:

   ![로컬 저장소에 대한 만들기 옵션 및 환경 변수 추가](./media/how-to-access-host-storage-from-module/offline-storage.png)

또는 배포 매니페스트에서 로컬 저장소를 직접 구성할 수 있습니다. 예를 들어:

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
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
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

호스트 `<HostStoragePath>` `<ModuleStoragePath>` 및 모듈 저장 경로를 교체하고; 두 값 모두 절대 경로여야 합니다.

예를 들어 Linux 시스템에서는 `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` 호스트 시스템의 디렉터리 **/etc/iotedge/저장소가** 컨테이너의 디렉토리/iotedge/storage/에 매핑된다는 의미입니다. **/iotedge/storage/** Windows 시스템에서 다른 예는 `"Binds":["C:\\temp:C:\\contemp"]` 디렉터리 **C:\\** 호스트 시스템의 temp가 디렉터리 **C에\\** 매핑된다는 것을 의미합니다.

또한 Linux 장치에서 모듈의 사용자 프로필에 호스트 시스템 디렉터리에 대한 읽기, 쓰기 및 실행 권한이 있는지 확인합니다. IoT Edge 허브가 장치의 로컬 저장소에 메시지를 저장할 수 있도록 설정하는 이전 예제로 돌아가면 사용자 프로필UID 1000에 권한을 부여해야 합니다. (IoT Edge 에이전트는 루트로 작동하므로 추가 권한이 필요하지 않습니다.) 디렉터리 소유자를 `chown` 변경한 다음 `chmod` 다음과 같이 사용 권한을 변경하는 등 Linux 시스템에서 디렉터리 권한을 관리하는 방법에는 여러 가지가 있습니다.

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

docker 문서에서 옵션 만들기에 대한 자세한 내용을 찾을 수 [있습니다.](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)

## <a name="next-steps"></a>다음 단계

모듈에서 호스트 저장소에 액세스하는 추가 예제는 [IoT Edge의 Azure Blob 저장소가 있는 에지의 데이터 저장을](how-to-store-data-blob.md)참조하십시오.

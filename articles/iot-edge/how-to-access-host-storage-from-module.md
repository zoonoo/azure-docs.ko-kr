---
title: 모듈에서 IoT Edge 장치 로컬 저장소 사용-Azure IoT Edge | Microsoft Docs
description: 환경 변수를 사용 하 고 IoT Edge 장치 로컬 저장소에 대 한 모듈 액세스를 설정 하는 옵션을 만듭니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 72fb7cfad5683edeb3b3335c28c53a7e693d00d5
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330798"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>모듈에 디바이스의 로컬 스토리지에 대한 액세스 권한 부여

Azure storage 서비스 또는 장치의 컨테이너 저장소에서 데이터를 저장 하는 것 외에도, 특히 오프 라인에서 작동 하는 경우 안정성 향상을 위해 호스트 IoT Edge 장치 자체에 저장소를 지정할 수 있습니다.

## <a name="link-module-storage-to-device-storage"></a>장치 저장소에 모듈 저장소 연결

모듈 저장소에서 호스트 시스템의 저장소로의 링크를 사용 하도록 설정 하려면 컨테이너의 저장소 폴더를 가리키는 모듈에 대해 환경 변수를 만듭니다. 그런 다음, 만들기 옵션을 사용하여 해당 스토리지 폴더를 호스트 머신의 폴더에 바인딩합니다.

예를 들어 IoT Edge 허브가 장치의 로컬 저장소에 메시지를 저장 하 고 나중에 검색할 수 있도록 하려면 **고급 Edge 런타임 구성 설정의 Azure Portal에서 환경 변수와 만들기 옵션을 구성할 수 있습니다.** 섹션.

1. IoT Edge 허브와 IoT Edge 에이전트 모두에서 모듈의 디렉터리를 가리키는 **Storagefolder** 라는 환경 변수를 추가 합니다.
1. IoT Edge 허브와 IoT Edge 에이전트 모두에 대해 바인딩을 추가 하 여 호스트 컴퓨터의 로컬 디렉터리를 모듈의 디렉터리에 연결 합니다. 다음은 그 예입니다.

   ![로컬 저장소에 대 한 만들기 옵션 및 환경 변수 추가](./media/how-to-access-host-storage-from-module/offline-storage.png)

또는 배포 매니페스트에서 직접 로컬 저장소를 구성할 수 있습니다. 다음은 그 예입니다.

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

@No__t-0 및 `<ModuleStoragePath>`을 호스트 및 모듈 저장소 경로로 바꿉니다. 두 값 모두 절대 경로 여야 합니다.

예를 들어 Linux 시스템에서 `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]`은 호스트 시스템 **의 디렉터리가 컨테이너** 의 **/iotedge/storage/** 디렉터리에 매핑되는 것을 의미 합니다. 또 다른 예로 Windows 시스템에서 `"Binds":["C:\\temp:C:\\contemp"]`은 호스트 시스템의 c **: \\temp** 디렉터리를 컨테이너의 **c: \\contemp** 에 매핑합니다.

또한 Linux 장치에서 모듈의 사용자 프로필에 호스트 시스템 디렉터리에 대 한 읽기, 쓰기 및 실행 권한이 있어야 합니다. IoT Edge 허브가 장치의 로컬 저장소에 메시지를 저장 하도록 설정 하는 이전 예제로 돌아가면 UID 1000 사용자 프로필에 대 한 사용 권한을 부여 해야 합니다. IoT Edge 에이전트는 루트로 작동 하므로 추가 권한이 필요 하지 않습니다. @No__t-0을 사용 하 여 디렉터리 소유자를 변경한 다음-1을 @no__t 하 여 다음과 같은 사용 권한을 변경 하는 등 Linux 시스템에서 디렉터리 권한을 관리 하는 몇 가지 방법이 있습니다.

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

[Docker 문서](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)에서 만들기 옵션에 대 한 자세한 내용을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

모듈에서 호스트 저장소에 액세스 하는 추가 예제는 [IoT Edge에서 Azure Blob Storage를 사용 하 여에 지에 데이터 저장](how-to-store-data-blob.md)을 참조 하세요.

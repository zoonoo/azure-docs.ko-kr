---
title: 모듈의 createOptions 작성 - Azure IoT Edge | Microsoft Docs
description: 배포 매니페스트에서 createOptions를 사용하여 런타임 시 모듈을 구성하는 방법
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ee5536562eb3f2008908a36ff296ef2cfa337ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200614"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>IoT Edge 모듈의 컨테이너 만들기 옵션을 구성하는 방법

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

배포 매니페스트의 **createOptions** 매개 변수를 사용하여 런타임 시 모듈 컨테이너를 구성할 수 있습니다. 이 매개 변수는 모듈에 대한 제어를 확장하며, 호스트 디바이스 리소스에 대한 모듈의 액세스를 허용 또는 제한하거나 네트워킹을 구성하는 등의 작업을 허용합니다.

IoT Edge 모듈은 IoT Edge 디바이스에서 Docker 호환 컨테이너로 구현됩니다. Docker는 컨테이너를 만들기 위한 다양한 옵션을 제공하며 해당 옵션은 IoT Edge 모듈에도 적용됩니다. 자세한 내용은 [Docker 컨테이너 만들기 옵션](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)을 참조하세요.

## <a name="format-create-options"></a>만들기 옵션 형식 지정

IoT Edge 배포 매니페스트는 JSON으로 형식이 지정된 만들기 옵션을 허용합니다. 예를 들어, 모든 edgeHub 모듈에 대해 자동으로 포함되는 만들기 옵션을 사용합니다.

```json
"createOptions": {
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
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

이 edgeHub 예제에서는 **HostConfig.PortBindings** 매개 변수를 사용하여 컨테이너의 공개된 포트를 호스트 디바이스의 포트에 매핑합니다.

Visual Studio 또는 Visual Studio Code용 Azure IoT Tools 확장을 사용하는 경우 **deployment.template.json** 파일에서 JSON 형식으로 만들기 옵션을 작성할 수 있습니다. 그런 다음, 확장을 사용하여 IoT Edge 솔루션을 빌드하거나 배포 매니페스트를 생성하는 경우 확장은 IoT Edge 런타임에 필요한 형식으로 JSON을 문자열 변환합니다. 예:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

만들기 옵션을 작성하기 위한 하나의 팁은 `docker inspect` 명령을 사용하는 것입니다. 개발 프로세스의 일부로 `docker run <container name>`을 사용하여 모듈을 로컬로 실행합니다. 모듈이 원하는 방식으로 작동하면 `docker inspect <container name>`을 실행합니다. 이 명령은 JSON 형식으로 모듈 세부 정보를 출력합니다. 구성한 매개 변수를 찾고 JSON을 복사합니다. 예:

[![Docker 검사 edgeHub의 결과](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>일반적인 시나리오

컨테이너 만들기 옵션은 많은 시나리오를 가능하게 하지만 IoT Edge 솔루션을 빌드할 때 가장 자주 나오는 몇 가지 시나리오는 다음과 같습니다.

* [호스트 스토리지에 대한 액세스 권한을 모듈에 부여](how-to-access-host-storage-from-module.md)
* [모듈 포트에 호스트 포트 매핑](#map-host-port-to-module-port)
* [모듈 메모리 및 CPU 사용량 제한](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>모듈 포트에 호스트 포트 매핑

모듈이 IoT Edge 솔루션 외부의 서비스와 통신해야 하고 이 작업에 메시지 라우팅을 사용하지 않는 경우 호스트 포트를 모듈 포트에 매핑해야 합니다.

>[!TIP]
>이 포트 매핑은 동일한 디바이스에서 모듈 간 통신에 필요하지 않습니다. 모듈 A가 모듈 B에서 호스트되는 API를 쿼리해야 하는 경우 포트 매핑 없이 쿼리할 수 있습니다. 모듈 B는 dockerfile에서 포트를 공개해야 합니다(예: `EXPOSE 8080`). 그런 다음, 모듈 A는 모듈 B의 이름을 사용하여 API를 쿼리할 수 있습니다(예: `http://ModuleB:8080/api`).

먼저 모듈 내 포트가 연결을 수신 대기하도록 공개되는지 확인합니다. Dockerfile에서 [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 명령을 사용하여 이 작업을 수행할 수 있습니다. 예들 들어 `EXPOSE 8080`입니다. 지정되지 않은 경우 Expose 명령이 기본적으로 TCP 프로토콜로 설정되거나 사용자가 UDP를 지정할 수 있습니다.

그런 다음, [Docker 컨테이너 만들기 옵션](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)의 **HostConfig** 그룹에서 **PortBindings** 설정을 사용하여 모듈의 공개된 포트를 호스트 디바이스의 포트에 매핑합니다. 예를 들어, 모듈 내에서 포트 8080을 공개하고 호스트 디바이스의 포트 80에 매핑하려는 경우 template.json 파일의 만들기 옵션은 다음 예제와 같습니다.

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

배포 매니페스트에 대해 문자열화된 후 동일한 구성이 다음 예제와 같이 표시됩니다.

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>모듈 메모리 및 CPU 사용량 제한

모듈이 사용할 수 있는 호스트 리소스의 양을 선언할 수 있습니다. 이 컨트롤은 한 모듈이 너무 많은 메모리 또는 CPU 사용량을 소비할 수 없도록 하고 다른 프로세스가 디바이스에서 실행되는 것을 방지하는 데 유용합니다. 다음을 포함하여 **HostConfig** 그룹에서 [Docker 컨테이너 만들기 옵션](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)을 사용하여 이 설정을 관리할 수 있습니다.

* **메모리**: 메모리 한도(바이트)입니다. 예를 들어, 268435456바이트 = 256MB입니다.
* **MemorySwap**: 총 메모리 한도(메모리 + 스왑)입니다. 예를 들어, 536870912바이트 = 512MB입니다.
* **CpuPeriod**: CPU 기간의 길이(마이크로초)입니다. 기본값은 100000입니다. 예를 들어, 값 25000은 컨테이너를 CPU 리소스의 25%로 제한합니다.

template.json 형식에서 이 값은 다음 예제와 같이 표시됩니다.

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

최종 배포 매니페스트에 대해 문자열화된 후 이 값이 다음 예제와 같이 표시됩니다.

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>다음 단계

작업의 만들기 옵션에 관한 추가 예제는 다음 IoT Edge 샘플을 참조하세요.

* [Raspberry Pi 3의 Custom Vision 및 Azure IoT Edge](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT Edge Blob Storage 샘플](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)

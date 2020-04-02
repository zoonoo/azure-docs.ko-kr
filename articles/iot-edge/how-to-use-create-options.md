---
title: 작성 모듈에 대한 옵션 쓰기 - Azure IoT 에지 | 마이크로 소프트 문서
description: 배포 매니페스트에서 createOptions를 사용하여 런타임에 모듈을 구성하는 방법
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550345"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>IoT Edge 모듈에 대한 컨테이너 생성 옵션 구성 방법

배포 매니페스트의 **createOptions** 매개 변수를 사용하면 런타임에 모듈 컨테이너를 구성할 수 있습니다. 이 매개 변수는 모듈에 대한 제어를 확장하고 호스트 장치의 리소스에 대한 모듈의 액세스를 허용하거나 제한하거나 네트워킹을 구성하는 등의 작업을 허용합니다.

IoT Edge 모듈은 IoT Edge 장치에서 Docker 호환 컨테이너로 구현됩니다. Docker는 컨테이너를 만들기 위한 많은 옵션을 제공하며 이러한 옵션은 IoT Edge 모듈에도 적용됩니다. 자세한 내용은 [Docker 컨테이너 만들기 옵션을](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)참조하십시오.

## <a name="format-create-options"></a>만들기 옵션 서식 지정

IoT Edge 배포 매니페스트는 JSON형식으로 포맷된 옵션을 만듭니다. 예를 들어 모든 edgeHub 모듈에 대해 자동으로 포함된 만들기 옵션을 선택합니다.

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

이 edgeHub 예제에서는 **HostConfig.PortBindings** 매개 변수를 사용하여 컨테이너의 노출된 포트를 호스트 장치의 포트에 매핑합니다.

Visual Studio 또는 Visual Studio 코드에 Azure IoT 도구 확장을 사용하는 경우 **deploy.template.json** 파일에서 JSON 형식으로 만들기 옵션을 작성할 수 있습니다. 그런 다음 확장을 사용하여 IoT Edge 솔루션을 빌드하거나 배포 매니페스트를 생성하면 IoT Edge 런타임이 기대하는 형식으로 JSON을 문자열화합니다. 다음은 그 예입니다.

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

만들기 옵션을 작성하기 위한 한 `docker inspect` 가지 팁은 명령을 사용하는 것입니다. 개발 프로세스의 일부로 을 사용하여 `docker run <container name>`모듈을 로컬로 실행합니다. 모듈이 원하는 방식으로 작동하면 을 실행합니다. `docker inspect <container name>` 이 명령은 모듈 세부 정보를 JSON 형식으로 출력합니다. 구성한 매개 변수를 찾아 JSON을 복사합니다. 다음은 그 예입니다.

[![도커 검사 결과](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png) 에지허브](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>일반적인 시나리오

컨테이너 만들기 옵션은 많은 시나리오를 사용할 수 있지만 IoT Edge 솔루션을 빌드할 때 가장 자주 발생하는 몇 가지 시나리오는 다음과 같습니다.

* [모듈에 호스트 스토리지에 대한 액세스 권한 부여](how-to-access-host-storage-from-module.md)
* [호스트 포트를 모듈 포트로 매핑](#map-host-port-to-module-port)
* [모듈 메모리 및 CPU 사용량 제한](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>호스트 포트를 모듈 포트로 매핑

모듈이 IoT Edge 솔루션 외부의 서비스와 통신해야 하고 메시지 라우팅을 사용하지 않는 경우 호스트 포트를 모듈 포트에 매핑해야 합니다.

>[!TIP]
>이 포트 매핑은 동일한 장치에서 모듈 간 통신에 필요하지 않습니다. 모듈 A가 모듈 B에서 호스팅되는 API를 쿼리해야 하는 경우 포트 매핑 없이 쿼리할 수 있습니다. 모듈 B는 dockerfile에 포트를 노출해야 `EXPOSE 8080`합니다. 그런 다음 모듈 A는 모듈 B의 이름을 사용하여 `http://ModuleB:8080/api`API를 쿼리할 수 있습니다.

먼저 모듈 내부의 포트가 노출되어 연결을 수신 수신못하도록 해야 합니다. dockerfile에서 [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 명령을 사용하여 이 작업을 수행할 수 있습니다. `EXPOSE 8080`)을 입력합니다. 노출 명령은 지정하지 않은 경우 TCP 프로토콜에 기본값을 지정하거나 UDP를 지정할 수 있습니다.

그런 다음 Docker 컨테이너의 **HostConfig** 그룹의 **PortBindings** 설정을 사용하여 모듈의 노출된 포트를 호스트 장치의 포트에 매핑하는 [옵션을 만듭니다.](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) 예를 들어 모듈 내부에 포트 8080을 노출하고 호스트 장치의 포트 80에 매핑하려는 경우 template.json 파일의 만들기 옵션은 다음과 같습니다.

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

배포 매니페스트에 대해 문자열화되면 동일한 구성은 다음 예제와 같습니다.

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>모듈 메모리 및 CPU 사용량 제한

모듈에서 사용할 수 있는 호스트 리소스의 양을 선언할 수 있습니다. 이 컨트롤은 한 모듈이 너무 많은 메모리 또는 CPU 사용량을 소비하지 못하게 하고 다른 프로세스가 장치에서 실행되지 않도록 하는 데 유용합니다. 다음을 포함하여 **HostConfig** 그룹에서 [옵션을 만드는 Docker 컨테이너를](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) 사용하여 이러한 설정을 관리할 수 있습니다.

* **메모리**: 바이트의 메모리 제한. 예를 들어 268435456 바이트 = 256MB입니다.
* **메모리 스왑**: 총 메모리 제한 (메모리 + 스왑). 예를 들어 536870912 바이트 = 512MB
* **CPU기간**: CPU 기간의 길이를 마이크로초단위로 합니다. 기본값은 100000이므로 예를 들어 25000값은 컨테이너를 CPU 리소스의 25%로 제한합니다.

template.json 형식에서 이러한 값은 다음과 같습니다.

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

최종 배포 매니페스트에 대해 문자열화되면 이러한 값은 다음 예제와 같습니다.

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>다음 단계

작업 옵션 만들기의 자세한 예는 다음 IoT Edge 샘플을 참조하십시오.

* [사용자 지정 비전 및 Azure IoT 가장자리 에 대 한 라즈베리 파이 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT 에지 Blob 저장소 샘플](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)

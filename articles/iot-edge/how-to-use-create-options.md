---
title: 모듈에 대 한 createOptions 쓰기-Azure IoT Edge | Microsoft Docs
description: 배포 매니페스트에서 createOptions를 사용 하 여 런타임 시 모듈을 구성 하는 방법
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550345"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>IoT Edge 모듈의 컨테이너 만들기 옵션을 구성 하는 방법

배포 매니페스트의 **Createoptions** 매개 변수를 사용 하면 런타임 시 모듈 컨테이너를 구성할 수 있습니다. 이 매개 변수는 모듈에 대 한 컨트롤을 확장 하 고, 모듈에서 호스트 장치 리소스에 대 한 액세스를 허용 하거나 제한 하거나 네트워킹을 구성 하는 등의 작업을 허용 합니다.

IoT Edge 모듈은 IoT Edge 장치에서 Docker 호환 컨테이너로 구현 됩니다. Docker는 컨테이너를 만들기 위한 다양 한 옵션을 제공 하며 이러한 옵션은 IoT Edge 모듈에도 적용 됩니다. 자세한 내용은 [Docker 컨테이너 만들기 옵션](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)을 참조 하세요.

## <a name="format-create-options"></a>서식 만들기 옵션

IoT Edge 배포 매니페스트는 JSON으로 형식이 지정 된 만들기 옵션을 허용 합니다. 예를 들어 모든 edgeHub 모듈에 대해 자동으로 포함 되는 만들기 옵션을 사용 합니다.

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

이 edgeHub 예제에서는 **Hostconfig. PortBindings** 매개 변수를 사용 하 여 컨테이너의 노출 된 포트를 호스트 장치의 포트에 매핑합니다.

Visual Studio 또는 Visual Studio Code에 대 한 Azure IoT Tools 확장을 사용 하는 경우에는 **deployment. template. json** 파일에 json 형식으로 만들기 옵션을 작성할 수 있습니다. 그런 다음 확장을 사용 하 여 IoT Edge 솔루션을 빌드하거나 배포 매니페스트를 생성 하는 경우 IoT Edge 런타임에서 기대 하는 형식으로 JSON을 json.stringify 합니다. 다음은 그 예입니다.

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

만들기 옵션을 작성 하는 한 가지 팁은 `docker inspect` 명령을 사용 하는 것입니다. 개발 프로세스의 일부로를 사용 하 여 `docker run <container name>`모듈을 로컬로 실행 합니다. 원하는 방식으로 작동 하는 모듈이 있으면를 실행 `docker inspect <container name>`합니다. 이 명령은 JSON 형식으로 모듈 세부 정보를 출력 합니다. 구성 된 매개 변수를 찾고 JSON을 복사 합니다. 다음은 그 예입니다.

[![Docker 검사 edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png) 의 결과](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>일반적인 시나리오

컨테이너 만들기 옵션은 많은 시나리오를 사용할 수 있지만, 다음은 IoT Edge 솔루션을 빌드할 때 가장 자주 발생 하는 몇 가지 시나리오입니다.

* [모듈에 호스트 저장소에 대 한 액세스 권한 부여](how-to-access-host-storage-from-module.md)
* [호스트 포트를 모듈 포트에 매핑](#map-host-port-to-module-port)
* [모듈 메모리와 CPU 사용량 제한](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>호스트 포트를 모듈 포트에 매핑

모듈이 IoT Edge 솔루션 외부의 서비스와 통신 해야 하 고이를 위해 메시지 라우팅을 사용 하 고 있지 않은 경우에는 호스트 포트를 모듈 포트에 매핑해야 합니다.

>[!TIP]
>이 포트 매핑은 동일한 장치에서 모듈 간 통신에 필요 하지 않습니다. 모듈 A가 모듈 B에서 호스트 되는 API를 쿼리해야 하는 경우에는 포트 매핑 없이이 작업을 수행할 수 있습니다. 모듈 B는 dockerfile에서 포트를 노출 해야 합니다 (예: `EXPOSE 8080`). 그런 다음 모듈 A는 모듈 B의 이름 (예: `http://ModuleB:8080/api`)을 사용 하 여 API를 쿼리할 수 있습니다.

먼저 모듈 내의 포트가 연결을 수신 대기 하도록 노출 되는지 확인 합니다. Dockerfile에서 [노출](https://docs.docker.com/engine/reference/builder/#expose) 하는 명령을 사용 하 여이 작업을 수행할 수 있습니다. `EXPOSE 8080`)을 입력합니다. 지정 하지 않으면 기본적으로 표시 되는 명령은 TCP 프로토콜을 지정 하 고, 또는 UDP를 지정할 수 있습니다.

그런 다음 [Docker 컨테이너 만들기 옵션](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) 의 **Hostconfig** 그룹에서 **portbindings** 설정을 사용 하 여 모듈의 노출 된 포트를 호스트 장치의 포트에 매핑합니다. 예를 들어 모듈 내에서 포트 8080을 노출 하 고 호스트 장치의 포트 80에 매핑하려고 하는 경우 템플릿. json 파일의 만들기 옵션은 다음 예제와 같습니다.

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

배포 매니페스트에 대해 문자열 형식 한 후에는 동일한 구성이 다음 예제와 같이 표시 됩니다.

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>모듈 메모리와 CPU 사용량 제한

모듈에서 사용할 수 있는 호스트 리소스의 양을 선언할 수 있습니다. 이 컨트롤은 한 모듈에서 메모리 나 CPU 사용량을 너무 많이 사용 하 고 다른 프로세스가 장치에서 실행 되지 않도록 하는 데 유용 합니다. 다음을 포함 하 여 **Hostconfig** 그룹에서 [Docker 컨테이너 만들기 옵션](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) 을 사용 하 여 이러한 설정을 관리할 수 있습니다.

* **메모리**: 메모리 제한 (바이트)입니다. 예를 들어 268435456 바이트 = 256 MB입니다.
* **Memoryswap**: 전체 메모리 한도 (메모리 + 스왑)입니다. 예: 536870912 바이트 = 512 MB
* **CpuPeriod**: CPU 기간의 길이 (마이크로초)입니다. 기본값은 10만 이므로 예를 들어 25000 값은 컨테이너를 CPU 리소스의 25%로 제한 합니다.

이러한 값은 다음 예제와 같이 템플릿 json 형식으로 표시 됩니다.

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

최종 배포 매니페스트에 문자열 형식 한 후에는 다음 예제와 같은 값이 표시 됩니다.

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>다음 단계

작업의 만들기 옵션에 대 한 자세한 예제는 다음 IoT Edge 샘플을 참조 하세요.

* [Raspberry Pi 3의 Custom Vision 및 Azure IoT Edge](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT Edge blob storage 샘플](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)

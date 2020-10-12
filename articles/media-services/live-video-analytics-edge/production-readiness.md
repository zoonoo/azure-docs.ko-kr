---
title: 프로덕션 준비 및 모범 사례-Azure
description: 이 문서에서는 프로덕션 환경의 IoT Edge 모듈에서 라이브 비디오 분석을 구성 하 고 배포 하는 방법에 대 한 지침을 제공 합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c34e05e184cfa6f0933701a76177fae3eed70c0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87071941"
---
# <a name="production-readiness-and-best-practices"></a>프로덕션 준비 및 모범 사례

이 문서에서는 프로덕션 환경의 IoT Edge 모듈에서 라이브 비디오 분석을 구성 하 고 배포 하는 방법에 대 한 지침을 제공 합니다. IoT Edge 솔루션 준비에 대 한 [프로덕션 문서에서 IoT Edge 솔루션 배포 준비](../../iot-edge/production-checklist.md) 도 검토 해야 합니다. 

> [!NOTE]
> 조직의 IT 부서에 보안과 관련 된 측면을 참조 해야 합니다.

## <a name="running-the-module-as-a-local-user"></a>로컬 사용자로 모듈 실행

IoT Edge 모듈에서 라이브 비디오 분석을에 지 장치에 배포 하는 경우 기본적으로 상승 된 권한으로 실행 됩니다. 이렇게 하면 모듈 ()의 로그를 확인 하는 경우 다음이 `sudo iotedge logs {name-of-module}` 표시 됩니다.

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

아래 섹션에는 위의 경고를 해결 하는 방법이 설명 되어 있습니다.

### <a name="creating-and-using-a-local-user-account"></a>로컬 사용자 계정 만들기 및 사용

가능 하면 소수의 권한이 있는 계정을 사용 하 여 프로덕션 환경에서 IoT Edge 모듈에 대해 라이브 비디오 분석을 실행할 수 있습니다. 예를 들어 다음 명령을 사용 하 여 Linux VM에서 로컬 사용자 계정을 만드는 방법을 보여 줍니다.

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

그런 다음 배포 매니페스트에서 LOCAL_USER_ID 및 LOCAL_GROUP_ID 환경 변수를 루트가 아닌 사용자 및 그룹으로 설정할 수 있습니다.

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>장치 저장소에 대 한 사용 권한 부여

IoT Edge 모듈의 Live Video Analytics에는 다음과 같은 경우 로컬 파일 시스템에 파일을 쓸 수 있는 기능이 필요 합니다.

* 모듈 쌍 속성 [[Applicationdatadirectory](module-twin-configuration-schema.md#module-twin-properties)]를 사용 하 여 구성 데이터를 저장할 로컬 파일 시스템의 디렉터리를 지정 해야 합니다.
* 미디어 그래프를 사용 하 여 클라우드에 비디오를 기록 하려면 모듈에서에 지 장치의 디렉터리를 캐시로 사용 해야 합니다 (자세한 내용은 [연속 비디오 녹화](continuous-video-recording-concept.md) 문서 참조).
* 기록 된 비디오에 대 한 파일 경로를 지정 해야 하는 [로컬 파일에 기록](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)합니다.

위의 방법 중 하나를 사용 하려는 경우 위의 사용자 계정에 관련 디렉터리에 대 한 액세스 권한이 있는지 확인 해야 합니다. 예를 들어 applicationDataDirectory를 고려 합니다. Edge 장치에서 디렉터리를 만들고 장치 저장소를 모듈 저장소에 연결할 수 있습니다. 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

그런 다음 배포 매니페스트의 edge 모듈에 대 한 만들기 옵션에서 위의 디렉터리 ("var/local/windowsazure.mediaservices/")를 모듈의 디렉터리 (예: "/var/lib/azuremediaservices/")에 매핑하는 바인딩 설정을 추가할 수 있습니다. 후자 디렉터리를 applicationDataDirectory의 값으로 사용 합니다.

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

[연속 비디오 녹화](continuous-video-recording-tutorial.md)와 같이 빠른 시작 및 자습서에 대 한 샘플 미디어 그래프를 살펴보면, 미디어 캐시 디렉터리 (localMediaCachePath)에서 applicationdatadirectory 아래에 하위 디렉터리를 사용 하는 것에 주목 합니다. 캐시에 임시 데이터가 포함 되어 있기 때문에 권장 되는 방법입니다.

### <a name="naming-video-assets-or-files"></a>비디오 자산 또는 파일 이름 지정

미디어 그래프를 사용 하면에 지에 대 한 클라우드 또는 mp4 파일의 자산을 만들 수 있습니다. 미디어 자산은 [연속 비디오 녹화](continuous-video-recording-tutorial.md) 또는 [이벤트 기반 비디오 기록](event-based-video-recording-tutorial.md)에 의해 생성 될 수 있습니다. 이러한 자산과 파일의 이름은 원하는 대로 지정할 수 있지만 연속 비디오 기록 기반 미디어 자산의 권장 이름 구조는 " &lt; anytext &gt; -$ {GraphTopologyName}-$ {}"입니다. 예를 들어 다음과 같이 자산 싱크에 대해 assetNamePattern를 설정할 수 있습니다.

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

이벤트 기반 비디오 레코딩 생성 자산의 경우 권장 되는 명명 패턴은 " &lt; anytext &gt; -$ {system.string}"입니다. 시스템 변수는 이벤트가 동시에 발생 하는 경우 자산을 덮어쓰지 않도록 합니다. 예를 들어 다음과 같이 자산 싱크에 대해 assetNamePattern를 설정할 수 있습니다.

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

동일한 그래프의 여러 인스턴스를 실행 하는 경우 그래프 토폴로지 이름 및 인스턴스 이름을 사용 하 여 구분할 수 있습니다. 예를 들어 다음과 같이 자산 싱크에 대해 assetNamePattern를 설정할 수 있습니다.

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName} -${System.DateTime}"
```

에 지에 대 한 이벤트 기반 비디오 레코딩 생성 mp4 비디오 클립의 경우 권장 되는 명명 패턴에는 DateTime을 포함 해야 하며, 동일한 그래프의 여러 인스턴스에 대해 GraphTopologyName 및 GraphInstanceName을 사용 하는 것이 좋습니다. 예를 들어 다음과 같이 파일 싱크에 filePathPattern을 설정할 수 있습니다. 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

또는 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```

### <a name="keeping-your-vm-clean"></a>VM 정리 유지

에 지 장치로 사용 하는 Linux VM은 주기적으로 관리 되지 않는 경우 응답 하지 않을 수 있습니다. 캐시를 정리 하 고, 불필요 한 패키지를 제거 하 고, VM에서 사용 하지 않는 컨테이너를 제거 하는 것이 중요 합니다. 이 작업을 수행 하려면에 지 VM에서을 사용할 수 있는 권장 명령 집합입니다.

1. `sudo apt-get clean`

    Apt-clean 가져오기 명령은/cv/ds에 남아 있는 검색 된 패키지 파일의 로컬 리포지토리를 지웁니다. 정리 하는 디렉터리는/var/cache/apt/archives/및/var/cache/apt/archives/partial/.입니다. /Dev/sv/sv/srt/ss에 있는 파일은 잠금 파일과 부분 하위 디렉터리입니다. Apt-일반적으로 정기적으로 예약 된 유지 관리의 일환으로 필요에 따라 디스크 공간을 정리 하는 데 사용 됩니다. 자세한 내용은 apt를 사용 하 [여 정리](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html)를 참조 하세요.
1. `sudo apt-get autoclean`

    Apt-get autoclean 옵션 (예: apt)은 검색 된 패키지 파일의 로컬 리포지토리를 지우므로 더 이상 다운로드할 수 없고 유용 하지 않은 파일만 제거 합니다. 캐시가 너무 커지지 않도록 하는 데 도움이 됩니다.
1. `sudo apt-get autoremove1`

    자동 제거 옵션을 사용 하면 다른 패키지에 필요한 패키지는 제거 되지만, 다른 패키지는 제거 되었으므로 더 이상 필요 하지 않으므로 자동으로 설치 된 패키지가 제거 됩니다.
1. `sudo docker image ls` –에 지 시스템의 Docker 이미지 목록을 제공 합니다.
1. `sudo docker system prune `

    Docker는 이미지, 컨테이너, 볼륨 및 네트워크와 같은 사용 되지 않는 개체 ("가비지 수집"이 라고도 함)를 정리 하는 데 특별 한 접근 방법을 사용 합니다. 이러한 개체는 Docker가 명시적으로 수행 하도록 요청 하지 않는 한 일반적으로 제거 되지 않습니다. 이로 인해 Docker에서 추가 디스크 공간을 사용할 수 있습니다. 각 개체 형식에 대해 Docker는 정리 명령을 제공 합니다. 또한 docker 시스템 정리를 사용 하 여 한 번에 여러 유형의 개체를 정리할 수 있습니다. 자세한 내용은 [사용 하지 않는 Docker 개체 정리](https://docs.docker.com/config/pruning/)를 참조 하세요.
1. `sudo docker rmi REPOSITORY:TAG`

    Edge 모듈에서 업데이트가 수행 됨에 따라 docker에는 이전 버전의 edge 모듈이 계속 제공 될 수 있습니다. 이 경우 docker rmi 명령을 사용 하 여 이미지 버전 태그로 식별 된 특정 이미지를 제거 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[빠른 시작: 시작 - IoT Edge의 Live Video Analytics](get-started-detect-motion-emit-events-quickstart.md)

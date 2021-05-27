---
title: Live Video Analytics의 프로덕션 준비 상태 및 모범 사례 - Azure
description: 이 문서에서는 프로덕션 환경의 IoT Edge 모듈에서 Azure Live Video Analytics를 구성하고 배포하는 방법에 대한 지침을 제공합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 7893750c1f835a95b5eba706f94679a41a424856
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372066"
---
# <a name="production-readiness-and-best-practices-in-live-video-analytics"></a>Live Video Analytics의 프로덕션 준비 상태 및 모범 사례

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

이 문서에서는 프로덕션 환경의 IoT Edge 모듈에서 Live Video Analytics를 구성하고 배포하는 방법에 대한 지침을 제공합니다. IoT Edge 솔루션 준비에 대해서는 [IoT Edge 솔루션을 프로덕션 단계에서 배포하도록 준비](../../iot-edge/production-checklist.md) 문서도 검토해야 합니다. 

> [!NOTE]
> 조직의 IT 부서에 보안과 관련된 측면을 문의해야 합니다.

## <a name="running-the-module-as-a-local-user"></a>로컬 사용자로 모듈 실행

IoT Edge의 Live Video Analytics 모듈을 에지 디바이스에 배포하는 경우 기본적으로 상승된 권한으로 실행됩니다. 이렇게 하면 모듈(`sudo iotedge logs {name-of-module}`)에서 로그를 확인하는 경우 다음이 표시됩니다.

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

아래 섹션에는 위의 경고를 해결하는 방법이 설명되어 있습니다.

### <a name="creating-and-using-a-local-user-account"></a>로컬 사용자 계정 만들기 및 사용

가능하면 약간의 권한이 있는 계정을 사용하여 프로덕션 환경에서 IoT Edge의 Live Video Analytics 모듈을 실행할 수 있으며 실행해야 합니다. 예를 들어 다음 명령을 사용하여 Linux VM에서 로컬 사용자 계정을 만드는 방법을 보여 줍니다.

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

그런 다음, 배포 매니페스트에서 LOCAL_USER_ID 및 LOCAL_GROUP_ID 환경 변수를 루트가 아닌 사용자 및 그룹으로 설정할 수 있습니다.

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

### <a name="granting-permissions-to-device-storage"></a>디바이스 스토리지에 대한 사용 권한 부여

IoT Edge의 Live Video Analytics 모듈에는 다음과 같은 경우 로컬 파일 시스템에 파일을 쓸 수 있는 기능이 필요합니다.

* 모듈 쌍 속성 [`applicationDataDirectory`](module-twin-configuration-schema.md#module-twin-properties)를 사용하는 경우. 이 경우에는 구성 데이터를 저장하기 위해 로컬 파일 시스템의 디렉터리를 지정해야 합니다.
* 미디어 그래프를 사용하여 클라우드에 비디오를 기록하려면 모듈에서 에지 디바이스의 디렉터리를 캐시로 사용해야 합니다(자세한 내용은 [연속 비디오 녹화](continuous-video-recording-concept.md) 문서 참조).
* [로컬 파일에 기록](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)하는 경우. 녹화된 비디오의 파일 경로를 지정해야 합니다.

위의 방법 중 하나를 사용하려는 경우 위의 사용자 계정에 관련 디렉터리에 대한 액세스 권한이 있는지 확인해야 합니다. applicationDataDirectory를 예로 들 수 있습니다. 에지 디바이스에서 디렉터리를 만들고 디바이스 스토리지를 모듈 스토리지에 연결할 수 있습니다. 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

그런 다음, 배포 매니페스트의 에지 모듈에 대한 만들기 옵션에서 위의 디렉터리("var/local/windowsazure.mediaservices/")를 모듈의 디렉터리(예: "/var/lib/azuremediaservices/")에 매핑하는 바인딩 설정을 추가할 수 있습니다. 후자 디렉터리를 applicationDataDirectory의 값으로 사용합니다.

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

[연속 비디오 녹화](continuous-video-recording-tutorial.md)와 같은 빠른 시작 및 자습서에 대한 샘플 미디어 그래프를 살펴보면, 미디어 캐시 디렉터리(localMediaCachePath)에서 applicationdatadirectory 아래의 하위 디렉터리를 사용하는 것을 알 수 있습니다. 캐시에 임시 데이터가 포함되어 있기 때문에 권장되는 방법입니다.

### <a name="naming-video-assets-or-files"></a>비디오 자산 또는 파일 이름 지정

미디어 그래프를 사용하면 에지에서 클라우드 또는 mp4 파일의 자산을 만들 수 있습니다. 미디어 자산은 [연속 비디오 녹화](continuous-video-recording-tutorial.md) 또는 [이벤트 기반 비디오 녹화](event-based-video-recording-tutorial.md)를 통해 생성될 수 있습니다. 이러한 자산과 파일의 이름은 원하는 대로 지정할 수 있지만 연속 비디오 녹화 기반 미디어 자산의 권장되는 이름 구조는 "&lt;anytext&gt;-${System.GraphTopologyName}-${System.GraphInstanceName}"입니다.   

대체 패턴은 $ 기호 뒤에 중괄호를 사용해서 정의합니다( **${variableName}** ).  

예를 들어 다음과 같이 자산 싱크에 대해 assetNamePattern을 설정할 수 있습니다.

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

이벤트 기반 비디오 녹화 생성 자산의 경우 권장되는 명명 패턴은 "&lt;anytext&gt;-${System.DateTime}"입니다. 시스템 변수는 이벤트가 동시에 발생하는 경우 자산을 덮어쓰지 않도록 합니다. 예를 들어 다음과 같이 자산 싱크에 대해 assetNamePattern을 설정할 수 있습니다.

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

동일한 그래프의 여러 인스턴스를 실행하는 경우 그래프 토폴로지 이름 및 인스턴스 이름을 사용하여 구분할 수 있습니다. 예를 들어 다음과 같이 자산 싱크에 대해 assetNamePattern을 설정할 수 있습니다.

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName}-${System.DateTime}"
```

에지의 이벤트 기반 비디오 녹화 생성 mp4 비디오 클립의 경우 권장되는 명명 패턴에는 DateTime을 포함해야 하며, 동일한 그래프의 여러 인스턴스에 대해 시스템 변수 GraphTopologyName 및 GraphInstanceName을 사용하는 것이 좋습니다. 예를 들어, 다음과 같이 파일 싱크에 filePathPattern을 설정할 수 있습니다. 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

또는 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```
>[!NOTE]
> 위의 예에서 **fileSinkOutputName** 변수는 그래프 토폴로지에서 정의한 샘플 변수 이름입니다. 시스템 변수가 **아닙니다**. 

#### <a name="system-variables"></a>시스템 변수
사용할 수 있는 시스템 정의 변수는 다음과 같습니다.

|시스템 변수|Description|예제|
|-----------|-----------|-----------|
|System.DateTime|ISO8601 파일 호환 형식의 UTC 날짜 시간(기본 표현 YYYYMMDDThhmmss)입니다.|20200222T173200Z|
|System.PreciseDateTime|ISO8601 파일 호환 형식의 밀리초를 포함하는 UTC 날짜 시간(기본 표현 YYYYMMDDThhmmss.sss)입니다.|20200222T173200.123Z|
|System.GraphTopologyName|실행 중인 그래프 토폴로지의 사용자 제공 이름입니다.|IngestAndRecord|
|System.GraphInstanceName|실행 중인 그래프 인스턴스의 사용자 제공 이름입니다.|camera001|

>[!TIP]
> 이름에 있는 "."로 인해 자산 이름을 지정할 때 PreciseDateTime을 사용할 수 없습니다.
### <a name="keeping-your-vm-clean"></a>VM을 정리 상태로 유지

에지 디바이스로 사용하는 Linux VM은 주기적으로 관리되지 않는 경우 응답하지 않을 수 있습니다. 캐시를 정리하고, 불필요한 패키지를 제거하고, VM에서 사용하지 않은 컨테이너를 제거하는 것이 중요합니다. 이 작업을 수행하기 위해 여기서는 에지 VM에서 사용할 수 있는 권장 명령 세트를 제공합니다.

1. `sudo apt-get clean`

    apt-get clean 명령은 /var/cache에 남아 있는 검색된 패키지 파일의 로컬 리포지토리를 지웁니다. 정리하는 디렉터리는 /var/cache/apt/archives/ 및 /var/cache/apt/archives/partial/입니다. /var/cache/apt/archives에 남아 있는 유일한 파일은 잠금 파일과 부분 하위 디렉터리입니다. apt-get clean 명령은 일반적으로 정기적으로 예약된 유지 관리의 일환으로 필요에 따라 디스크 공간을 정리하는 데 사용됩니다. 자세한 내용은 [apt-get을 사용하여 정리](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html)를 참조하세요.
1. `sudo apt-get autoclean`

    apt-get autoclean 옵션(예: apt-get clean)은 검색된 패키지 파일의 로컬 리포지토리를 지우므로 더 이상 다운로드할 수 없고 유용하지 않은 파일만 제거합니다. 캐시가 너무 커지지 않도록 하는 데 도움이 됩니다.
1. `sudo apt-get autoremove1`

    자동 제거 옵션을 사용하면 다른 패키지에 필요하지만 해당 다른 패키지가 제거되면 더 이상 필요하지 않게 되는 자동으로 설치된 패키지가 제거됩니다.
1. `sudo docker image ls` – 에지 시스템의 Docker 이미지 목록을 제공합니다.
1. `sudo docker system prune`

    Docker는 이미지, 컨테이너, 볼륨 및 네트워크와 같은 사용되지 않는 개체("가비지 수집"이라고도 함)를 정리하는 일반적인 접근 방법을 사용합니다. Docker가 명시적으로 요청하지 않는 한, 이러한 개체는 일반적으로 제거되지 않습니다. 이로 인해 Docker에서 추가 디스크 공간을 사용할 수 있습니다. 각 개체 형식에 대해 Docker는 정리 명령을 제공합니다. 또한 docker 시스템 정리를 사용하여 한 번에 여러 형식의 개체를 정리할 수 있습니다. 자세한 내용은 [사용하지 않은 Docker 개체 정리](https://docs.docker.com/config/pruning/)를 참조하세요.
1. `sudo docker rmi REPOSITORY:TAG`

    에지 모듈에서 업데이트가 수행되면 docker에는 이전 버전의 에지 모듈이 계속 제공될 수 있습니다. 이 경우 docker rmi 명령을 사용하여 이미지 버전 태그로 식별된 특정 이미지를 제거하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[빠른 시작: 시작 - IoT Edge의 Live Video Analytics](get-started-detect-motion-emit-events-quickstart.md)

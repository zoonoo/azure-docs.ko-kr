---
title: 모니터링 및 로깅-Azure
description: 이 문서에서는 IoT Edge에 대 한 라이브 비디오 분석의 모니터링 및 로깅에 대 한 개요를 제공 합니다.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 6a7251b62421642ad9f5dba4f4c2a15ce74cd5cf
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900878"
---
# <a name="monitoring-and-logging"></a>모니터링 및 로깅

이 문서에서는 IoT Edge 모듈의 Live Video Analytics에서 원격 모니터링에 대 한 이벤트를 수신 하는 방법을 알아봅니다. 

또한 모듈이 생성 하는 로그를 제어 하는 방법에 대해 알아봅니다.

## <a name="taxonomy-of-events"></a>이벤트 분류

IoT Edge의 Live Video Analytics는 다음 분류에 따라 이벤트 또는 원격 분석 데이터를 내보냅니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="이벤트의 분류를 표시 하는 다이어그램입니다.":::

* 작업: 사용자의 작업 또는 [미디어 그래프](media-graph-concept.md) 를 실행 하는 동안 생성 된 이벤트
   
   * 볼륨: 낮을 것으로 예상 됨 (몇 분 또는 그 미만)
   * 예제:

      - 기록 시작 (다음 예제에 표시)
      - 기록 중지 됨
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 진단: 성능 문제를 진단 하는 데 도움이 되는 이벤트

   * 볼륨: 높을 수 있습니다 (1 분에 여러 번).
   * 예제:
   
      - RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 정보 (다음 예제에 표시 됨) 
      - 들어오는 비디오 피드의 간격

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 분석: 비디오 분석의 일부로 생성 된 이벤트

   * 볼륨: 높을 수 있습니다 (1 분 이상 여러 번).
   * 예제:
      
      - 동작이 검색 됨 (다음 예제에서 표시 됨) 
      - 유추 결과

   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

모듈에서 내보낸 이벤트는 [IoT Edge 허브](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)로 전송 됩니다. 다른 대상에서 다른 대상으로 라우팅할 수 있습니다. 

### <a name="timestamps-in-analytic-events"></a>분석 이벤트의 타임 스탬프

앞에서 설명한 것 처럼 비디오 분석의 일부로 생성 된 이벤트에는 타임 스탬프가 연결 되어 있습니다. [라이브 비디오](video-recording-concept.md) 를 그래프 토폴로지의 일부로 기록한 경우 이러한 타임 스탬프를 통해 기록 된 비디오에서 특정 이벤트가 발생 한 위치를 찾을 수 있습니다. 다음은 [Azure Media Services 자산](terminology.md#asset)에 기록 되는 비디오의 타임 라인에 분석 이벤트의 타임 스탬프를 매핑하는 방법에 대 한 지침입니다.

먼저 값을 추출 `eventTime` 합니다. [시간 범위 필터](playback-recordings-how-to.md#time-range-filters) 에이 값을 사용 하 여 기록의 적절 한 부분을 검색 합니다. 예를 들어 30 초 전에 시작 하 `eventTime` 고 30 초 후에 시작 하는 비디오를 검색할 수 있습니다. 앞의 예제에서이 ( `eventTime` 가) 2020-05-12T23:33:09.381 z 인 경우 30 초 이전 및 이후 HLS 매니페스트에 대 한 요청은 `eventTime` 이 요청 처럼 보입니다.

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

위의 URL은 미디어 재생 목록에 대 한 Url이 포함 된 [마스터 재생 목록을](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) 반환 합니다. 미디어 재생 목록은 다음과 같은 항목을 포함 합니다.

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
위의 항목은의 값에서 시작 하는 비디오 조각을 사용할 수 있음을 보고 합니다 `timestamp` `143039375031270` . `timestamp`분석 이벤트의 값은 미디어 재생 목록과 동일한 날짜/시간을 사용 합니다. 관련 비디오 조각을 식별 하 고 올바른 프레임을 검색 하는 데 사용할 수 있습니다.

자세한 내용은 HLS의 프레임에 [정확한 검색에 대](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) 한 다음 문서를 참조 하세요.

## <a name="controlling-events"></a>이벤트 제어

다음 모듈 쌍 속성을 사용 하 여 IoT Edge 모듈의 Live Video Analytics에서 게시 한 운영 및 진단 이벤트를 제어할 수 있습니다. 이러한 속성은 [모듈 쌍 JSON 스키마](module-twin-configuration-schema.md)에 설명 되어 있습니다.

- `diagnosticsEventsOutputName`: 모듈에서 진단 이벤트를 가져오려면이 속성을 포함 하 고 값을 제공 합니다. 모듈이 진단 이벤트를 게시 하지 않도록 하려면이를 생략 하거나 비워 둡니다.
   
- `operationalEventsOutputName`: 모듈에서 작업 이벤트를 가져오려면이 속성을 포함 하 고 값을 제공 합니다. 모듈이 작업 이벤트를 게시 하지 않도록 하려면이를 생략 하거나 비워 둡니다.
   
분석 이벤트는 동작 감지 프로세서 또는 HTTP 확장 프로세서와 같은 노드에 의해 생성 됩니다. IoT hub 싱크는 IoT Edge 허브로 전송 하는 데 사용 됩니다. 

배포 매니페스트에서 모듈 쌍의 속성을 사용 하 여 [모든 이전 이벤트의 라우팅을](../../iot-edge/module-composition.md#declare-routes) 제어할 수 있습니다 `desired` `$edgeHub` .

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

위의 JSON에서 `lvaEdge` 는 IoT Edge 모듈에서 라이브 비디오 분석의 이름입니다. 라우팅 규칙은 [Declare 경로](../../iot-edge/module-composition.md#declare-routes)에 정의 된 스키마를 따릅니다.

> [!NOTE]
> 분석 이벤트가 IoT Edge 허브에 도달 하 게 하려면 모든 동작 검색 프로세서 노드 및/또는 모든 HTTP 확장 프로세서 노드에 대 한 IoT hub 싱크 노드 다운스트림이 있어야 합니다.

## <a name="event-schema"></a>이벤트 스키마

이벤트는에 지 장치에서 시작 되며 edge 또는 클라우드에서 사용할 수 있습니다. IoT Edge에서 라이브 비디오 분석에 의해 생성 된 이벤트는 Azure IoT Hub에 의해 설정 된 [스트리밍 메시징 패턴](../../iot-hub/iot-hub-devguide-messages-construct.md) 을 따릅니다. 패턴은 시스템 속성, 응용 프로그램 속성 및 본문으로 구성 됩니다.

### <a name="summary"></a>요약

IoT Hub를 통해 관찰 되는 모든 이벤트에는 공용 속성 집합이 있습니다.

|속성   |속성 형식| 데이터 형식   |Description|
|---|---|---|---|
|`message-id`   |시스템 |guid|  고유한 이벤트 ID입니다.|
|`topic`|   applicationProperty |문자열|    Azure Media Services 계정의 Azure Resource Manager 경로입니다.|
|`subject`| applicationProperty |문자열|    이벤트를 내보내는 엔터티의 하위 경로입니다.|
|`eventTime`|   applicationProperty|    문자열| 이벤트가 생성 된 시간입니다.|
|`eventType`|   applicationProperty |문자열|    이벤트 유형 식별자입니다. 다음 섹션을 참조 하십시오.|
|`body`|본문    |object|    특정 이벤트 데이터입니다.|
|`dataVersion`  |applicationProperty|   문자열  |{Major}. 주|

### <a name="properties"></a>속성

#### <a name="message-id"></a>message-id

이벤트의 GUID (globally unique identifier)입니다.

#### <a name="topic"></a>토픽

그래프와 연결 된 Azure Media Services 계정을 나타냅니다.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

이벤트를 내보내는 엔터티입니다.

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

속성을 사용 하 여 `subject` 일반 이벤트를 생성 하는 모듈에 매핑할 수 있습니다. 예를 들어 잘못 된 RTSP 사용자 이름 또는 암호의 경우 생성 된 이벤트는 `Microsoft.Media.Graph.Diagnostics.ProtocolError` `/graphInstances/myGraph/sources/myRtspSource` 노드에 있습니다.

#### <a name="event-types"></a>일정 유형

이벤트 유형은이 스키마에 따라 네임 스페이스에 할당 됩니다.

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>이벤트 클래스

|클래스 이름|설명|
|---|---|
|분석  |콘텐츠 분석의 일부로 생성 되는 이벤트입니다.|
|진단    |문제 및 성능 진단에 도움이 되는 이벤트입니다.|
|작동    |리소스 작업의 일부로 생성 되는 이벤트입니다.|

이벤트 유형은 각 이벤트 클래스에 대해 고유 합니다.

예제:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>이벤트 시간

이벤트 시간은 ISO 8601 문자열에서 지정 됩니다. 이벤트가 발생 한 시간을 나타냅니다.

### <a name="azure-monitor-collection-via-telegraf"></a>Telegraf를 통해 컬렉션 Azure Monitor

이러한 메트릭은 IoT Edge 모듈의 라이브 비디오 분석에서 보고 됩니다.  

|메트릭 이름|형식|레이블|Description|
|-----------|----|-----|-----------|
|lva_active_graph_instances|계기|iothub, edge_device, module_name, graph_topology|토폴로지 당 활성 그래프의 총 수입니다.|
|lva_received_bytes_total|카운터|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|노드에서 받은 총 바이트 수입니다. RTSP 원본에 대해서만 지원 됩니다.|
|lva_data_dropped_total|카운터|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|삭제 된 데이터 (이벤트, 미디어 등)의 카운터입니다.|

> [!NOTE]
> [프로메테우스 끝점](https://prometheus.io/docs/practices/naming/) 은 컨테이너의 포트 9600에서 노출 됩니다. IoT Edge 모듈에서 라이브 비디오 분석의 이름을 "lvaEdge"로 지정할 경우에 GET 요청을 보내 메트릭에 액세스할 수 있습니다 http://lvaEdge:9600/metrics .   

IoT Edge 모듈의 라이브 비디오 분석에서 메트릭 수집을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. 개발 컴퓨터에 폴더를 만들고 해당 폴더로 이동 합니다.

1. 폴더에서 `telegraf.toml` 다음 구성을 포함 하는 파일을 만듭니다.
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = ""
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > . A m ml 파일의 변수를 바꾸어야 합니다. 변수는 중괄호 ()로 표시 됩니다 `{}` .

1. 동일한 폴더에서 `.dockerfile` 다음 명령을 포함 하는을 만듭니다.
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Docker CLI 명령을 사용 하 여 Docker 파일을 빌드하고 Azure container registry에 이미지를 게시 합니다.
    
   Docker CLI를 사용 하 여 컨테이너 레지스트리에 푸시하는 방법에 대 한 자세한 내용은 [docker 이미지 밀어넣기 및 끌어오기](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)를 참조 하세요. Azure Container Registry에 대 한 자세한 내용은 [설명서](https://docs.microsoft.com/azure/container-registry/)를 참조 하세요.


1. Azure Container Registry 푸시가 완료 되 면 배포 매니페스트 파일에 다음 노드를 추가 합니다.
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > 매니페스트 파일의 변수를 바꾸어야 합니다. 변수는 중괄호 ()로 표시 됩니다 `{}` .


   Azure Monitor은 [서비스 주체를 통해 인증할](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)수 있습니다.
        
   Azure Monitor Telegraf 플러그 인은 [몇 가지 인증 방법을](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)노출 합니다. 

  1. 서비스 주체 인증을 사용 하려면 다음 환경 변수를 설정 합니다.  
     `AZURE_TENANT_ID`: 인증할 테 넌 트를 지정 합니다.  
     `AZURE_CLIENT_ID`: 사용할 응용 프로그램 클라이언트 ID를 지정 합니다.  
     `AZURE_CLIENT_SECRET`: 사용할 앱 암호를 지정 합니다.  
     
     >[!TIP]
     > 서비스 주체에 게 **모니터링 메트릭 게시자** 역할을 제공할 수 있습니다.

1. 모듈을 배포한 후 메트릭은 단일 네임 스페이스의 Azure Monitor에 표시 됩니다. 메트릭 이름은 프로메테우스에서 내보낸 것과 일치 합니다. 

   이 경우 Azure Portal에서 IoT hub로 이동 하 고 왼쪽 창에서 **메트릭** 을 선택 합니다. 여기에 메트릭이 표시 됩니다.

## <a name="logging"></a>로깅

다른 IoT Edge 모듈과 마찬가지로 Edge 장치에서 [컨테이너 로그를 검사할](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) 수도 있습니다. [다음 모듈](module-twin-configuration-schema.md) 쌍 속성을 사용 하 여 로그에 기록 되는 정보를 구성할 수 있습니다.

* `logLevel`

   * 허용되는 값은 `Verbose`, `Information`, `Warning`, `Error`, `None`입니다.
   * 기본값은 `Information`입니다. 로그에는 오류, 경고 및 정보 메시지가 포함 됩니다.
   * 값을로 설정 하면 `Warning` 로그에 오류 및 경고 메시지가 포함 됩니다.
   * 값을로 설정 하면 `Error` 로그에 오류 메시지만 포함 됩니다.
   * 값을로 설정 하면 `None` 로그가 생성 되지 않습니다. (이 구성은 권장 하지 않습니다.)
   * `Verbose`문제를 진단 하기 위해 Azure 지원에 로그를 공유 해야 하는 경우에만를 사용 합니다.

* `logCategories`

   * `Application`,, 값 중 하나 이상의 쉼표로 구분 된 목록입니다. `Events` `MediaPipeline`
   * 기본값은 `Application, Events`입니다.
   * `Application`: 모듈 시작 메시지, 환경 오류 및 직접 메서드 호출과 같은 모듈의 상위 수준 정보입니다.
   * `Events`:이 문서 앞부분에서 설명한 모든 이벤트입니다.
   * `MediaPipeline`: RTSP 가능 카메라와의 연결을 설정 하는 데 문제가 있는 것과 같은 문제를 해결할 때 통찰력을 제공할 수 있는 하위 수준 로그입니다.
   
### <a name="generating-debug-logs"></a>디버그 로그 생성

특정 한 경우에는 Azure 지원에서 문제를 해결 하도록 돕기 위해 앞에서 설명한 것 보다 더 자세한 로그를 생성 해야 할 수도 있습니다. 이러한 로그를 생성 하려면:

1. 를 통해 [장치 저장소에 모듈 저장소를 연결](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) `createOptions` 합니다. 빠른 시작에서 [배포 매니페스트 템플릿을](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) 살펴보면 다음 코드가 표시 됩니다.

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   이 코드를 사용 하면 Edge 모듈이 장치 저장소 경로에 로그를 쓸 수 있습니다 `/var/local/mediaservices/` . 

 1. 모듈에 다음 속성을 추가 합니다 `desired` .

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

이제 모듈은 장치 저장소 경로에 디버그 로그를 이진 형식으로 씁니다 `/var/local/mediaservices/debuglogs/` . Azure 지원으로 이러한 로그를 공유할 수 있습니다.

## <a name="faq"></a>FAQ

질문이 있는 경우 [모니터링 및 메트릭 FAQ](faq.md#monitoring-and-metrics)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[연속 비디오 녹화](continuous-video-recording-tutorial.md)

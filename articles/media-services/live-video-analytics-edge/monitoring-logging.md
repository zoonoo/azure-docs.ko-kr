---
title: Live Video Analytics에서 모니터링 및 로깅 - Azure
description: 이 문서에서는 IoT Edge의 Azure Live Video Analytics에서 모니터링 및 로깅에 대한 개요를 제공합니다.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 4bc69192d0d36d01755d625ee46cd77cd9eec1b9
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372144"
---
# <a name="monitoring-and-logging-in-live-video-analytics-on-iot-edge"></a>IoT Edge의 Live Video Analytics에서 모니터링 및 로깅

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

이 문서에서는 IoT Edge 모듈의 Live Video Analytics에서 원격 모니터링에 대한 이벤트를 수신하는 방법을 알아봅니다. 

또한 모듈에서 생성하는 로그를 제어하는 방법에 대해 알아봅니다.

## <a name="taxonomy-of-events"></a>이벤트 분류

IoT Edge의 Live Video Analytics는 다음 분류에 따라 이벤트 또는 원격 분석 데이터를 내보냅니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="이벤트의 분류를 보여 주는 다이어그램.":::

* 작업: 사용자의 작업 또는 [미디어 그래프](media-graph-concept.md) 실행 중에 생성된 이벤트
   
   * 볼륨: 낮을 것으로 예상됨(1분에 몇 번 또는 그 미만)
   * 예:

      - 기록이 시작됨(다음 예제에 표시됨)
      - 기록이 중지됨
      
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
* 진단: 성능 문제를 진단하는 데 도움이 되는 이벤트

   * 볼륨: 높을 수 있음(1분에 여러 번)
   * 예:
   
      - RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 정보(다음 예제에 표시됨) 
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
* 분석: 비디오 분석의 일부로 생성된 이벤트

   * 볼륨: 높을 수 있음(1분에 여러 번 또는 그 이상)
   * 예:
      
      - 동작이 감지됨(다음 예제에 표시됨) 
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

모듈에서 내보낸 이벤트는 [IoT Edge 허브](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)로 전송됩니다. 이 허브에서 다른 대상으로 라우팅할 수 있습니다. 

### <a name="timestamps-in-analytic-events"></a>분석 이벤트의 타임스탬프

앞에서 설명한 것처럼 비디오 분석의 일부로 생성된 이벤트는 타임스탬프가 연결되어 있습니다. 그래프 토폴로지의 일부로 [라이브 비디오를 녹화](video-recording-concept.md)한 경우 타임스탬프를 통해 녹화된 비디오에서 특정 이벤트가 발생한 위치를 찾을 수 있습니다. 다음은 [Azure Media Services 자산](terminology.md#asset)에 기록되는 비디오의 타임라인에 분석 이벤트의 타임스탬프를 매핑하는 방법에 대한 지침입니다.

먼저 `eventTime` 값을 추출합니다. 이 값을 [시간 범위 필터](playback-recordings-how-to.md#time-range-filters)에 사용하여 적절한 기록 부분을 검색합니다. 예를 들어 `eventTime` 30초 전에 시작하고 30초 후에 종료되는 비디오를 검색할 수 있습니다. 이전 예제에서 `eventTime`이 2020-05-12T23:33:09.381Z인 경우 `eventTime` 30초 전후 HLS 매니페스트에 대한 요청은 다음 요청처럼 보입니다.

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

위의 URL은 미디어 재생 목록에 대한 URL이 포함된 [마스터 재생 목록](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)을 반환합니다. 미디어 재생 목록에는 다음과 같은 항목이 포함됩니다.

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
위의 항목은 `timestamp` 값 `143039375031270`에서 시작하는 비디오 조각을 사용할 수 있다고 보고합니다. 분석 이벤트의 `timestamp` 값은 미디어 재생 목록과 동일한 날짜 표시줄을 사용합니다. 관련 비디오 조각을 식별하고 올바른 프레임을 검색하는 데 사용할 수 있습니다.

자세한 내용은 HLS의 [프레임 단위의 정확한 검색에 대한 다음 문서](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS)를 참조하세요.

## <a name="controlling-events"></a>이벤트 제어

IoT Edge 모듈의 Live Video Analytics에서 게시한 운영 및 진단 이벤트를 제어하는 데 다음 모듈 쌍 속성을 사용할 수 있습니다. 다음 모듈 쌍속성은 [모듈 쌍 JSON 스키마](module-twin-configuration-schema.md)에 설명되어 있습니다.

- `diagnosticsEventsOutputName`: 모듈에서 진단 이벤트를 가져오려면 이 속성을 포함하고 속성에 대한 값을 제공합니다. 모듈이 진단 이벤트를 게시하지 않도록 하려면 이 속성을 생략하거나 비워 둡니다.
   
- `operationalEventsOutputName`: 모듈에서 작업 이벤트를 가져오려면 이 속성을 포함하고 속성에 대한 값을 제공합니다. 모듈이 작업 이벤트를 게시하지 않도록 하려면 이 속성을 생략하거나 비워 둡니다.
   
분석 이벤트는 동작 감지 프로세서 또는 HTTP 확장 프로세서와 같은 노드에서 생성됩니다. IoT 허브 싱크는 분석 이벤트를 IoT Edge 허브로 전송하는 데 사용됩니다. 

배포 매니페스트에서 `$edgeHub` 모듈 쌍의 `desired` 속성을 사용하여 [모든 이전 이벤트의 라우팅](../../iot-edge/module-composition.md#declare-routes)을 제어할 수 있습니다.

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

위의 JSON에서 `lvaEdge`는 IoT Edge 모듈의 Live Video Analytics 이름입니다. 회람 규칙은 [Declare 경로](../../iot-edge/module-composition.md#declare-routes)에 정의된 스키마를 따릅니다.

> [!NOTE]
> 분석 이벤트가 IoT Edge 허브에 도달하게 하려면 모든 동작 감지 프로세서 노드 및/또는 모든 HTTP 확장 프로세서 노드의 IoT 허브 싱크 노드 다운스트림이 있어야 합니다.

## <a name="event-schema"></a>이벤트 스키마

이벤트는 에지 디바이스에서 시작되며 에지 또는 클라우드에서 사용할 수 있습니다. IoT Edge의 Live Video Analytics에서 생성한 이벤트는 Azure IoT Hub에 의해 설정된 [스트리밍 메시징 패턴](../../iot-hub/iot-hub-devguide-messages-construct.md)을 따릅니다. 패턴은 시스템 속성, 애플리케이션 속성 및 본문으로 구성됩니다.

### <a name="summary"></a>요약

IoT Hub를 통해 관찰되는 모든 이벤트에는 공용 속성 세트가 있습니다.

|속성   |속성 형식| 데이터 형식   |Description|
|---|---|---|---|
|`message-id`   |시스템 |guid|  고유한 이벤트 ID입니다.|
|`topic`|   applicationProperty |문자열|    Azure Media Services 계정의 Azure Resource Manager 경로입니다.|
|`subject`| applicationProperty |문자열|    이벤트를 내보내는 엔터티의 하위 경로입니다.|
|`eventTime`|   applicationProperty|    문자열| 이벤트가 생성된 시간입니다.|
|`eventType`|   applicationProperty |문자열|    이벤트 유형 식별자입니다. (다음 섹션을 참조하세요.)|
|`body`|본문    |object|    특정 이벤트 데이터입니다.|
|`dataVersion`  |applicationProperty|   문자열  |{Major}.{Minor}|

### <a name="properties"></a>속성

#### <a name="message-id"></a>message-id

이벤트의 GUID(Globally Unique Identifier)입니다.

#### <a name="topic"></a>토픽

그래프와 연결된 Azure Media Services 계정을 나타냅니다.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

이벤트를 내보내는 엔터티입니다.

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

`subject` 속성을 사용하여 일반 이벤트를 생성하는 모듈에 매핑할 수 있습니다. 예를 들어 RTSP 사용자 이름 또는 암호가 잘못된 경우 생성된 이벤트는 `/graphInstances/myGraph/sources/myRtspSource` 노드의 `Microsoft.Media.Graph.Diagnostics.ProtocolError`입니다.

#### <a name="event-types"></a>일정 유형

이벤트 유형은 다음 스키마에 따라 네임스페이스에 할당됩니다.

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>이벤트 클래스

|클래스 이름|설명|
|---|---|
|분석  |콘텐츠 분석의 일부로 생성되는 이벤트입니다.|
|진단    |문제 및 성능 진단에 도움이 되는 이벤트입니다.|
|작동    |리소스 작업의 일부로 생성되는 이벤트입니다.|

이벤트 유형은 각 이벤트 클래스에 고유합니다.

예:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>이벤트 시간

이벤트 시간은 ISO 8601 문자열로 형식이 지정됩니다. 이벤트가 발생한 시간을 나타냅니다.

### <a name="azure-monitor-collection-via-telegraf"></a>Telegraf를 통한 Azure Monitor 컬렉션

다음 메트릭은 IoT Edge 모듈의 Live Video Analytics에서 보고됩니다.  

|메트릭 이름|유형|레이블|Description|
|-----------|----|-----|-----------|
|lva_active_graph_instances|계기|iothub, edge_device, module_name, graph_topology|토폴로지당 총 활성 그래프 수입니다.|
|lva_received_bytes_total|카운터|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|노드에서 받은 총 바이트 수입니다. RTSP 소스에 대해서만 지원됩니다.|
|lva_data_dropped_total|카운터|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|삭제된 데이터(이벤트, 미디어 등)의 카운터입니다.|

> [!NOTE]
> [Prometheus 엔드포인트](https://prometheus.io/docs/practices/naming/)는 컨테이너의 포트 9600에서 노출됩니다. IoT Edge 모듈의 Live Video Analytics 이름을 "lvaEdge"로 지정하는 경우 GET 요청을 http://lvaEdge:9600/metrics 로 전송하여 메트릭에 액세스할 수 있습니다.   

IoT Edge 모듈의 Live Video Analytics에서 메트릭 수집을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 개발 컴퓨터에 폴더를 만들고 해당 폴더로 이동합니다.

1. 해당 폴더에서 다음 구성이 포함된 `telegraf.toml` 파일을 만듭니다.
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > .toml 파일에서 변수를 바꿔야 합니다. 변수는 중괄호(`{}`)로 표시됩니다.

1. 동일한 폴더에서 다음 명령이 포함된 Dockerfile을 만듭니다.
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Docker CLI 명령을 사용하여 Docker 파일을 빌드하고 Azure Container Registry에 이미지를 게시합니다.
    
   Docker CLI를 사용하여 컨테이너 레지스트리에 푸시하는 방법에 대한 자세한 내용은 [Docker 이미지 푸시 및 풀](../../container-registry/container-registry-get-started-docker-cli.md)을 참조하세요. Azure Container Registry에 대한 자세한 내용은 [설명서](../../container-registry/index.yml)를 참조하세요.


1. Azure Container Registry로 푸시가 완료되면 배포 매니페스트 파일에 다음 노드를 추가합니다.
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
    > 매니페스트 파일에서 변수를 바꿔야 합니다. 변수는 중괄호(`{}`)로 표시됩니다.


   Azure Monitor는 [서비스 주체를 통해 인증](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)할 수 있습니다.
        
   Azure Monitor Telegraf 플러그 인은 [몇 가지 인증 방법](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)을 제공합니다. 

  1. 서비스 주체 인증을 사용하려면 다음 환경 변수를 설정합니다.  
     `AZURE_TENANT_ID`: 인증할 테넌트를 지정합니다.  
     `AZURE_CLIENT_ID`: 사용할 앱 클라이언트 ID를 지정합니다.  
     `AZURE_CLIENT_SECRET`: 사용할 앱 비밀을 지정합니다.  
     
     >[!TIP]
     > 서비스 주체에게 **모니터링 메트릭 게시자** 역할을 제공할 수 있습니다. **[서비스 주체 만들기](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** 의 단계에 따라 서비스 주체를 만들고 역할을 할당합니다.

1. 모듈을 배포하면 메트릭이 단일 네임스페이스의 Azure Monitor에 표시됩니다. 메트릭 이름은 Prometheus에서 내보낸 것과 일치합니다. 

   이 경우 Azure Portal에서 IoT 허브로 이동하고 왼쪽 창에서 **메트릭** 을 선택합니다. 여기에 메트릭이 표시됩니다.

### <a name="log-analytics-metrics-collection"></a>Log Analytics 메트릭 수집
[Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)와 함께 [Prometheus 엔드포인트](https://prometheus.io/docs/practices/naming/)를 사용하여 사용된 CPUPercent, MemoryUsedPercent 등의 메트릭을 생성하고 [모니터링](../../azure-monitor/essentials/metrics-supported.md)할 수 있습니다.   

> [!NOTE]
> 아래 구성은 로그를 수집하지 않고 **메트릭만** 수집합니다. 수집기 모듈을 확장하여 로그를 수집하고 업로드할 수도 있습니다.

[ ![Log Analytics를 사용한 메트릭 수집을 보여 주는 다이어그램.](./media/telemetry-schema/log-analytics.png)](./media/telemetry-schema/log-analytics.png#lightbox)

1. [메트릭 수집](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector) 방법을 알아봅니다.
1. Docker CLI 명령을 사용하여 [Docker 파일](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux)을 빌드하고 Azure Container Registry에 이미지를 게시합니다.
    
   Docker CLI를 사용하여 컨테이너 레지스트리에 푸시하는 방법에 대한 자세한 내용은 [Docker 이미지 푸시 및 풀](../../container-registry/container-registry-get-started-docker-cli.md)을 참조하세요. Azure Container Registry에 대한 자세한 내용은 [설명서](../../container-registry/index.yml)를 참조하세요.

1. Azure Container Registry로 푸시가 완료되면 배포 매니페스트에 다음을 삽입합니다.
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://lvaEdge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > `edgeHub`, `edgeAgent`, `lvaEdge` 모듈은 배포 매니페스트 파일에 정의된 모듈의 이름입니다. 모듈 이름이 일치하는지 확인하세요.   

    다음 단계에 따라 `LogAnalyticsWorkspaceId` 및 `LogAnalyticsSharedKey` 값을 가져올 수 있습니다.
    1. Azure Portal로 이동
    1. Log Analytics 작업 영역 찾기
    1. Log Analytics 작업 영역을 찾았으면 왼쪽 탐색 창에서 `Agents management` 옵션으로 이동합니다.
    1. 사용할 수 있는 작업 영역 ID 및 비밀 키를 확인합니다.

1. 그런 다음 왼쪽 탐색 창에서 `Workbooks` 탭을 클릭하여 통합 문서를 만듭니다.
1. Kusto 쿼리 언어를 사용하여 아래와 같이 쿼리를 작성하고 IoT Edge 모듈에 사용된 CPU 백분율을 가져올 수 있습니다.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("lvaEdge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [ ![Kusto 쿼리를 사용한 메트릭을 보여 주는 다이어그램.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>로깅

다른 IoT Edge 모듈과 마찬가지로 Edge 디바이스에서 [컨테이너 로그를 검사](../../iot-edge/troubleshoot.md#check-container-logs-for-issues)할 수도 있습니다. [다음 모듈 쌍](module-twin-configuration-schema.md) 속성을 사용하여 로그에 기록되는 정보를 구성할 수 있습니다.

* `logLevel`

   * 허용되는 값은 `Verbose`, `Information`, `Warning`, `Error`, `None`입니다.
   * 기본값은 `Information`입니다. 로그에는 오류, 경고, 정보 메시지가 포함됩니다.
   * 값을 `Warning`으로 설정하면 로그에 오류 및 경고 메시지가 포함됩니다.
   * 값을 `Error`로 설정하면 로그에 오류 메시지만 포함됩니다.
   * 값을 `None`으로 설정하면 로그가 생성되지 않습니다. (이 구성은 권장되지 않습니다.)
   * 문제를 진단하기 위해 Azure 지원과 로그를 공유해야 하는 경우에만 `Verbose`를 사용합니다.

* `logCategories`

   * 쉼표로 구분된 하나 이상의 `Application`, `Events`, `MediaPipeline` 값 목록입니다.
   * 기본값은 `Application, Events`입니다.
   * `Application`: 모듈 시작 메시지, 환경 오류, 직접 메서드 호출과 같은 모듈의 개략적인 정보입니다.
   * `Events`:이 문서 앞부분에서 설명한 모든 이벤트입니다.
   * `MediaPipeline`: RTSP 지원 카메라와의 연결 설정 문제와 같은 문제를 해결할 때 인사이트를 제공할 수 있는 하위 수준 로그입니다.
   
### <a name="generating-debug-logs"></a>디버그 로그 생성

경우에 따라 Azure 지원에서 문제를 해결하도록 지원하기 위해 앞에서 설명한 것보다 더 자세한 로그를 생성해야 할 수도 있습니다. 로그를 생성하려면 다음을 수행하세요.

1. `createOptions`를 통해 [디바이스 스토리지에 모듈 스토리지를 연결](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage)합니다. 빠른 시작에서 [배포 매니페스트 템플릿](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json)을 살펴보면 다음 코드가 표시됩니다.

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   이 코드를 사용하면 Edge 모듈에서 디바이스 스토리지 경로 `/var/local/mediaservices/`에 로그를 쓸 수 있습니다. 

 1. 모듈에 다음 `desired` 속성을 추가합니다.

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

이제 모듈에서 디바이스 스토리지 경로 `/var/local/mediaservices/debuglogs/`에 디버그 로그를 이진 형식으로 씁니다. 로그를 Azure 지원과 공유할 수 있습니다.

## <a name="faq"></a>FAQ

질문이 있는 경우 [모니터링 및 메트릭 FAQ](faq.md#monitoring-and-metrics)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[연속 비디오 녹화](continuous-video-recording-tutorial.md)

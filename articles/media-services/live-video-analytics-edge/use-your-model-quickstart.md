---
title: 사용자 고유의 모델을 사용하여 라이브 비디오 분석 - Azure
description: 이 빠른 시작에서는 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하기 위해 컴퓨터 비전을 적용합니다.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261491"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>빠른 시작: 사용자 고유의 모델로 라이브 비디오 분석

이 빠른 시작에서는 Live Video Analytics on IoT Edge를 사용하여 개체를 검색하는 컴퓨터 비전 모델을 적용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하는 방법을 보여 줍니다. 라이브 비디오 피드의 프레임 하위 집합이 유추 서비스로 전송되고 해당 결과가 IoT Edge Hub로 전송됩니다. Azure VM을 IoT Edge 디바이스와 시뮬레이션된 라이브 비디오 스트림으로 사용합니다. 이 문서의 모든 샘플 코드는 C#으로 작성되었습니다.

이 문서는 [이 빠른 시작](detect-motion-emit-events-quickstart.md)을 기반으로 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 머신에 설치된 [Visual Studio Code](https://code.visualstudio.com/)(다음 확장 포함):
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* 시스템에 설치된 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* 이전에 [이 빠른 시작](detect-motion-emit-events-quickstart.md)을 완료하지 않은 경우 다음 단계를 완료합니다.
     * [Azure 리소스 설정](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> Azure IoT Tools를 설치할 때 docker를 설치할지 묻는 메시지가 표시될 수 있습니다. 이 메시지는 무시해도 됩니다.

## <a name="review-the-sample-video"></a>샘플 비디오 검토
위의 단계에 따라 Azure 리소스를 설정하면 고속도로 교통에 대한 (짧은) 비디오가 IoT Edge 디바이스로 사용되는 Azure의 Linux VM에 복사됩니다. 이 비디오 파일은 이 자습서의 라이브 스트림을 시뮬레이션하는 데 사용됩니다.

[VLC Player](https://www.videolan.org/vlc/)와 같은 애플리케이션을 사용하여 시작하고, Control+N을 누르고, [이 링크](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)를 비디오에 붙여 넣어 재생을 시작할 수 있습니다. 많은 자동차가 달리는 고속도로 교통에 대한 장면임을 알 수 있습니다.

아래 단계를 완료한 후에는 Live Video Analytics on IoT Edge를 사용하여 차량, 사람 등과 같은 개체를 감지하고 연결된 유추 이벤트를 IoT Edge Hub에 게시합니다.

## <a name="overview"></a>개요

![개요](./media/quickstarts/overview-qs5.png)

위의 다이어그램은 이 빠른 시작의 신호 흐름을 보여 줍니다. 에지 모듈(자세한 내용은 [여기](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) 참조)은 RTSP 서버를 호스트하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](media-graph-concept.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [프레임 속도 필터 프로세서](media-graph-concept.md#frame-rate-filter-processor) 노드로 보냅니다. 이 프로세서는 [HTTP 확장 프로세서](media-graph-concept.md#http-extension-processor) 노드에 도달하는 비디오 스트림의 프레임 속도를 제한합니다. 

HTTP 확장 노드는 비디오 프레임을 지정된 이미지 형식으로 변환하고 REST를 통해 이미지를 HTTP 엔드포인트 뒤에서 AI 모델을 실행하는 다른 Edge 모듈에 릴레이하여 프록시 역할을 수행합니다. 이 예에서 Edge 모듈은 다양한 형식의 개체를 검색할 수 있는 [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) 모델을 사용하여 빌드됩니다. HTTP 확장 프로세서 노드는 검색 결과를 수집하고 이벤트를 [IoT Hub 싱크](media-graph-concept.md#iot-hub-message-sink ) 노드에 게시한 후 이러한 이벤트를 [IoT Edge Hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)에 보냅니다.

이 빠른 시작에서는 다음을 수행합니다.

1. 미디어 그래프를 만들고 배포합니다.
1. 결과를 해석합니다.
1. 리소스를 정리합니다.



## <a name="create-and-deploy-the-media-graph"></a>미디어 그래프를 만들고 배포
    
### <a name="examine-and-edit-the-sample-files"></a>샘플 파일 검사 및 편집

필수 구성 요소의 일부인 샘플 코드를 폴더에 다운로드했습니다. Visual Studio Code를 시작하고 폴더를 엽니다.

1. Visual Studio Code에서 "src/edge"로 이동합니다. 사용자가 만든 .env 파일이 몇 가지 배포 템플릿 파일과 함께 표시됩니다.

    * 배포 템플릿은 일부 자리 표시자 값이 있는 에지 디바이스에 대한 배포 매니페스트를 나타냅니다. .env 파일에는 이러한 변수에 대한 값이 있습니다.
1. 그런 다음, "src/cloud-to-device-console-app" 폴더로 이동합니다. 여기에는 사용자가 만든 appsettings.json 파일과 몇 가지 다른 파일이 함께 표시됩니다.

    * c2d-console-app.csproj - Visual Studio Code에 대한 프로젝트 파일입니다.
    * operations.json - 이 파일에는 프로그램이 실행할 다양한 작업이 나열됩니다.
    * Program.cs- 다음을 수행하는 샘플 프로그램 코드입니다.

        * 앱 설정을 로드합니다.
        *  Live Video Analytics on IoT Edge 모듈에 의해 노출되는 직접 메서드를 호출합니다. 이 모듈을 통해 [직접 메서드](direct-methods.md)를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다. 
        * 터미널 창에서 프로그램의 출력을 검토하고, 출력 창에서 모듈에 의해 생성된 이벤트를 살펴볼 수 있도록 일시 중지합니다.
        * 리소스를 정리하는 직접 메서드를 호출합니다.   


1. operations.json 파일을 다음과 같이 편집합니다.
    * 그래프 토폴로지에 대한 링크(`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`)를 변경합니다.
    * GraphInstanceSet에서 위 링크의 값과 일치하도록 그래프 토폴로지의 이름(`"topologyName" : "InferencingWithHttpExtension"`)을 편집합니다.
    * GraphTopologyDelete에서 이름(`"name": "InferencingWithHttpExtension"`)을 편집합니다.

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge 배포 매니페스트 생성 및 배포

1. "src/edge/ deployment.yolov3.template.json" 파일을 마우스 오른쪽 단추로 클릭하고 IoT Edge 배포 매니페스트 생성을 클릭합니다.

    ![IoT Edge 배포 매니페스트 생성](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. 이렇게 하면 src/edge/config 폴더에 "deployment.yolov3.amd64.json"이라는 매니페스트 파일이 생성됩니다.
1. 이전에 [빠른 시작](detect-motion-emit-events-quickstart.md)을 완료한 경우 이 단계를 건너뜁니다. 그렇지 않으면 왼쪽 하단에서 AZURE IOT HUB 창 옆에 있는 "추가 작업" 아이콘을 클릭하여 IoTHub 연결 문자열을 설정합니다. appsettings.json 파일에서 문자열을 복사할 수 있습니다. Visual Studio Code 내에 적절한 IoT Hub가 구성되도록 하는 데 권장되는 또 다른 방법은 [Iot Hub 선택 명령](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)을 사용하는 것입니다.
    
    ![IoTHub 연결 문자열](./media/quickstarts/set-iotconnection-string.png)
1. 그런 다음, "src/edge/config/ deployment.yolov3.amd64.json"을 마우스 오른쪽 단추로 클릭하고 "단일 디바이스용 배포 만들기"를 클릭합니다. 

    ![단일 디바이스용 배포 만들기](./media/quickstarts/create-deployment-single-device.png)
1. 그러면 IoT Hub 디바이스를 선택하라는 메시지가 표시됩니다. 드롭다운 목록에서 lva-sample-device를 선택합니다.
1. 약 30초 후에 왼쪽 하단 섹션에서 Azure IOT Hub를 새로 고치면 다음 모듈이 배포된 에지 디바이스가 있을 것입니다.

    1. Live Video Analytics 모듈(이름: "lvaEdge")
    1. RTSP 서버를 시뮬레이션하는 "rtspsim"이라는 모듈은 라이브 비디오 피드의 원본 역할을 합니다.
    1. 이름으로 알 수 있듯이 "yolov3"이라는 모듈은 컴퓨터 비전을 이미지에 적용하고 여러 개체 형식 클래스를 반환하는 YOLOv3 개체 감지 모델입니다.
 
        ![YOLOv3 개체 감지 모델](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>이벤트 모니터링 준비

Live Video Analytics 디바이스를 마우스 오른쪽 단추로 클릭하고 "기본 제공 이벤트 엔드포인트 모니터링 시작"을 클릭합니다. 이 단계는 IoT Hub 이벤트를 모니터링하고 Visual Studio Code의 출력 창에서 확인하는 데 필요합니다. 

![모니터링 시작](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>샘플 프로그램 실행

1. 디버깅 세션을 시작합니다(F5 키 누름). 터미널 창에 출력된 일부 메시지가 보이기 시작합니다.
1. GraphTopologyList 및 GraphInstanceList 직접 메서드를 호출하면 operations.json이 시작됩니다. 이전 빠른 시작 후에 리소스를 정리한 경우 빈 목록이 반환되고 Enter 키를 누를 때까지 일시 중지됩니다.
   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "1.0"
   }
   ---------------  Response: GraphTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput
   Press Enter to continue
   ```

1. 터미널 창에서 "Enter" 키를 누르면 다음 직접 메서드 호출 세트가 생성됩니다.
     * 위의 topologyUrl을 사용한 GraphTopologySet 호출
     * 다음 본문을 사용한 GraphInstanceSet 호출
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "InferencingWithHttpExtension",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
           },
           {
             "name": "rtspUserName",
             "value": "testuser"
           },
           {
             "name": "rtspPassword",
             "value": "testpassword"
           }
         ]
       }
     }
     ```
     * 그래프 인스턴스를 시작하고 비디오 흐름을 시작하는 GraphInstanceActivate 호출
     * 그래프 인스턴스가 실제로 실행 중인 상태임을 보여 주기 위한 두 번째 GraphInstanceList 호출
1. 이제 터미널 창의 출력이 '계속하려면 Enter 키를 누르세요.'라는 메시지에서 일시 중지됩니다. 아직은 "Enter" 키를 누르지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 볼 수 있습니다.
1. 이제 Visual Studio Code의 출력 창으로 전환하면 Live Video Analytics on IoT Edge 모듈이 IoT Hub로 전송한 메시지가 표시됩니다.
     * 이러한 메시지는 아래 섹션에서 설명합니다.
1. 미디어 그래프가 계속 실행되고 결과가 출력됩니다. RTSP 시뮬레이터는 원본 비디오를 계속 반복합니다. 미디어 그래프를 중지하려면 터미널 창으로 돌아가서 "Enter" 키를 누릅니다. 리소스 정리를 위해 다음과 같은 일련의 호출을 수행합니다.
     * 그래프 인스턴스를 비활성화하는 GraphInstanceDeactivate 호출
     * 인스턴스를 삭제하는 GraphInstanceDelete 호출
     * 토폴로지를 삭제하는 GraphTopologyDelete 호출
     * 이제 목록이 비어 있음을 보여 주는 GraphTopologyList에 대한 최종 호출

## <a name="interpret-results"></a>결과 해석

미디어 그래프를 실행하면 Http 확장 프로세서 노드의 결과가 IoT Hub 싱크 노드를 통해 IoT Hub에 전송됩니다. Visual Studio Code의 출력 창에 표시되는 메시지에는 "body" 섹션과 "applicationProperties" 섹션이 포함되어 있습니다. 이러한 섹션이 나타내는 내용을 이해하려면 [이 문서](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)를 참조하세요.

아래 메시지에서 애플리케이션 속성 및 본문의 내용은 Live Video Analytics 모듈에 의해 정의됩니다. 



### <a name="mediasession-established-event"></a>MediaSession Established 이벤트

미디어 그래프가 인스턴스화되면 RTSP 원본 노드가 rtspsim-live55 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 성공하면 이 이벤트가 출력됩니다. 이벤트 유형은 Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished입니다.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

위의 메시지에서 다음 사항을 확인하세요.
* 메시지는 진단 이벤트입니다. MediaSessionEstablished는 RTSP 원본 노드(주체)가 RTSP 시뮬레이터와 연결을 설정할 수 있었으며, (시뮬레이션된) 라이브 피드를 받기 시작했음을 나타냅니다.
* applicationProperties의 "subject"는 메시지가 미디어 그래프의 RTSP 원본 노드에서 생성되었음을 나타냅니다.
* applicationProperties의 "eventType"은 진단 이벤트임을 나타냅니다.
* "eventTime"은 이벤트가 발생한 시간을 나타냅니다.
* "body"에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 경우에는 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보입니다.

### <a name="inference-event"></a>유추 이벤트

HTTP 확장 프로세서 노드는 yolov3 모듈에서 유추 결과를 받고 IoT Hub 싱크 노드를 통해 유추 이벤트로 내보냅니다. 이러한 이벤트에서 형식은 자동차 또는 트럭과 같은 엔터티임을 나타내는 "entity"로 설정되고 eventTime는 개체가 감지된 시간(UTC)을 알려줍니다. 다음은 동일한 비디오 프레임에서 다양한 신뢰도의 자동차 두 대가 감지된 예입니다.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

위의 메시지에서 다음 사항을 확인하세요.

* applicationProperties의 "subject"는 메시지가 생성된 그래프 토폴로지의 노드를 나타냅니다. 
* applicationProperties의 "eventType"은 분석 이벤트임을 나타냅니다.
* "eventTime"은 이벤트가 발생한 시간을 나타냅니다.
* "body"는 분석 이벤트에 대한 데이터를 포함합니다. 이 경우 이벤트는 유추 이벤트이므로 본문에 "inferences" 데이터가 포함됩니다.
* "inferences" 섹션은 "type"이 "entity"이며 "entity"에 대한 추가 데이터가 있음을 나타냅니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작을 시도하려면 생성된 리소스를 유지해야 합니다. 그렇지 않으면 Azure Portal로 이동하여 리소스 그룹을 찾고 이 빠른 시작을 실행한 리소스 그룹을 선택한 후 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

고급 사용자에 대한 추가 문제를 검토합니다.

* RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격](https://www.onvif.org/conformant-products/) 제품 페이지에서 프로필 G, S 또는 T를 준수하는 디바이스를 찾아 RTSP를 지원하는 IP 카메라를 검색할 수 있습니다.
* Azure Linux VM 대신 AMD64 또는 X64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)의 지침을 따른 후 [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) 빠른 시작의 지침을 따라 Azure IoT Hub에 디바이스를 등록할 수 있습니다.


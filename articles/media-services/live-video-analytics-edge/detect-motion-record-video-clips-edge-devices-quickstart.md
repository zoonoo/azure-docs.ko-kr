---
title: 에지 디바이스에서 동작을 감지하고 비디오 녹화 - Azure
description: 이 빠른 시작에서는 Live Video Analytics on IoT Edge를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하고, 동작이 있는지 감지하고, 해당하는 경우 에지 디바이스의 로컬 파일 시스템에 MP4 비디오 클립을 녹화하는 방법을 보여 줍니다.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 14dcc7b298244a1d53a9b820c641ea87c4f9a016
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091864"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>빠른 시작: 에지 디바이스에서 동작을 감지하고 비디오 녹화
 
이 빠른 시작에서는 Live Video Analytics on IoT Edge를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하는 방법을 보여줍니다. 동작이 있는지 감지하는 방법, 동작이 있다면 에지 디바이스의 로컬 파일 시스템에 MP4 비디오 클립을 녹화하는 방법을 보여줍니다. 이 빠른 시작에서는 Azure VM을 IoT Edge 디바이스로 사용하고, 시뮬레이션된 라이브 비디오 스트림도 사용합니다. 

이 문서의 모든 샘플 코드는 C#으로 작성되었습니다. [동작 감지 및 이벤트 내보내기](detect-motion-emit-events-quickstart.md) 빠른 시작을 기반으로 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 다음 확장이 포함된 [Visual Studio Code](https://code.visualstudio.com/)
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [동작 감지 및 이벤트 내보내기](detect-motion-emit-events-quickstart.md) 빠른 시작을 아직 완료하지 않았으면 다음 단계를 수행합니다.
     1. [Azure 리소스 설정](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [개발 환경 설정](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [IoT Edge 배포 매니페스트 생성 및 배포](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [이벤트 모니터링 준비](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> Azure IoT Tools를 설치할 때 Docker를 설치하라는 메시지가 표시될 수 있습니다. 이 메시지는 무시해도 됩니다.

## <a name="review-the-sample-video"></a>샘플 비디오 검토
이 빠른 시작에 사용할 Azure 리소스를 설정할 때, 짧은 주차장 비디오가 IoT Edge 디바이스로 사용되는 Azure의 Linux VM에 복사됩니다. 이 비디오 파일은 이 자습서의 라이브 스트림을 시뮬레이션하는 데 사용됩니다.

[VLC 미디어 플레이어](https://www.videolan.org/vlc/) 같은 애플리케이션을 열고, Ctrl+N 키를 누르고, [이 링크](https://lvamedia.blob.core.windows.net/public/lots_015.mkv)를 주차장 비디오에 붙여넣어 재생을 시작합니다. 5초 마크 정도에 흰색 차가 주차장을 통과합니다.

아래 단계를 완료하여 Live Video Analytics on IoT Edge를 사용하여 자동차의 동작을 감지하고 5초 마크 정도에 시작되는 비디오 클립을 녹화합니다.

## <a name="overview"></a>개요

![개요](./media/quickstarts/overview-qs4.png)

위의 다이어그램에서는 이 빠른 시작의 신호 흐름을 보여줍니다. [에지 모듈](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스트하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](media-graph-concept.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [동작 감지 프로세서](media-graph-concept.md#motion-detection-processor) 노드로 보냅니다. RTSP 원본은 이벤트에 의해 트리거될 때까지 닫힌 상태로 유지되는 [신호 게이트 프로세서](media-graph-concept.md#signal-gate-processor) 노드로 동일한 비디오 프레임을 보냅니다.

동작 감지 프로세서는 비디오의 동작을 감지하면 신호 게이트 프로세서 노드로 이벤트를 보내서 트리거합니다. 게이트가 구성된 시간 동안 열리고 [파일 싱크](media-graph-concept.md#file-sink) 노드로 비디오 프레임을 보냅니다. 이 싱크 노드는 에지 디바이스의 로컬 파일 시스템에 MP4 파일로 비디오를 녹화합니다. 이 파일은 구성된 위치에 저장됩니다.

이 빠른 시작에서는 다음을 수행합니다.

1. 미디어 그래프를 만들고 배포합니다.
1. 결과를 해석합니다.
1. 리소스를 정리합니다.

## <a name="examine-and-edit-the-sample-files"></a>샘플 파일 검사 및 편집
이 빠른 시작의 필수 구성 요소로 샘플 코드를 폴더에 다운로드했습니다. 다음 단계에 따라 샘플 코드를 검사하고 편집합니다.

1. Visual Studio Code에서 *src/edge*로 이동합니다. *.env* 파일 및 몇 가지 배포 템플릿 파일이 표시됩니다.

    배포 템플릿은 변수가 일부 속성에 사용된 에지 디바이스에 대한 배포 매니페스트를 나타냅니다. *.env* 파일에는 이러한 변수에 대한 값이 포함되어 있습니다.
1. *src/cloud-to-device-console-app* 폴더로 이동합니다. 여기에는 *appsettings.json* 파일 및 몇 가지 다른 파일이 표시됩니다.
    * ***c2d-console-app.csproj*** - Visual Studio Code에 대한 프로젝트 파일입니다.
    * ***operations.json*** - 프로그램에서 실행하려는 작업 목록입니다.
    * ***Program.cs*** - 샘플 프로그램 코드입니다. 이 코드에서는 다음을 수행합니다.

        * 앱 설정을 로드합니다.
        * Live Video Analytics on IoT Edge 모듈에서 공개하는 직접 메서드를 호출합니다. 이 모듈을 통해 [직접 메서드](direct-methods.md)를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다. 
        * **터미널** 창에서 프로그램의 출력을 검사하고 **출력** 창에서 모듈이 생성한 이벤트를 검사할 수 있도록 일시 중지합니다.
        * 리소스를 정리하는 직접 메서드를 호출합니다.

1. *operations.json* 파일을 편집합니다.
    * 그래프 토폴로지의 링크를 변경합니다.

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * `GraphInstanceSet` 아래에서 이전 링크의 값과 일치하도록 그래프 토폴로지의 이름을 편집합니다.
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`

    * 다음과 같이 비디오 파일을 가리키도록 RTSP URL을 편집합니다.

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`

    * 다음과 같이 `GraphTopologyDelete` 아래에서 이름을 편집합니다.

        `"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-the-modules-status"></a>검토 - 모듈의 상태 확인

[IoT Edge 배포 매니페스트 생성 및 배포](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) 단계에서 Visual Studio Code의 **AZURE IOT HUB**(왼쪽 하단 섹션) 아래에서 **lva-sample-device** 노드를 확장합니다. 다음 모듈이 배포된 것을 볼 수 있습니다.

* **lvaEdge**라는 이름의 Live Video Analytics 모듈
* RTSP 서버를 시뮬레이션하고 라이브 비디오 피드의 원본으로 작동하는 **rtspsim** 모듈

  ![모듈](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>검토 - 이벤트 모니터링 준비
[이벤트 모니터링 준비](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events) 단계를 완료했는지 확인합니다.

![기본 제공 이벤트 엔드포인트 모니터링 시작](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>샘플 프로그램 실행

1. F5 키를 선택하여 디버깅 세션을 시작합니다. **터미널** 창에 몇 가지 메시지가 출력됩니다.
1. *operations.json* 코드가 `GraphTopologyList` 및 `GraphInstanceList` 직접 메서드를 호출합니다. 이전 빠른 시작을 완료한 후에 리소스를 정리했다면 이 프로세스가 빈 목록을 반환한 다음, 일시 중지됩니다. Enter 키를 선택합니다.

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

    **터미널** 창에 직접 메서드 호출의 다음 세트가 표시됩니다.

     * `topologyUrl`을 사용하는 `GraphTopologySet` 호출 
     * 다음 본문을 사용하는 `GraphInstanceSet` 호출

         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph",
           "properties": {
             "topologyName": "EVRToFilesOnMotionDetection",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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
     * 그래프 인스턴스 및 비디오 흐름을 시작하는 `GraphInstanceActivate` 호출
     * 그래프 인스턴스가 실행 중 상태임을 보여주는 두 번째 `GraphInstanceList` 호출
1. **터미널** 창의 출력이 `Press Enter to continue`에서 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하면 호출한 직접 메서드에 대한 JSON 응답 페이로드가 보입니다.
1. Visual Studio Code에서 **출력** 창으로 전환합니다. Live Video Analytics on IoT Edge 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 빠른 시작의 다음 섹션에서는 이러한 메시지를 설명합니다.

1. 미디어 그래프가 계속 실행되어 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 미디어 그래프를 중지하려면 **터미널** 창으로 돌아가서 Enter 키를 선택합니다. 

    일련의 다음 호출은 리소스를 정리합니다.
     * `GraphInstanceDeactivate` 호출은 그래프 인스턴스를 비활성화합니다.
     * `GraphInstanceDelete` 호출은 인스턴스를 삭제합니다.
     * `GraphTopologyDelete` 호출은 토폴로지를 삭제합니다.
     * 마지막 `GraphTopologyList` 호출은 목록이 비어 있음을 보여줍니다.

## <a name="interpret-results"></a>결과 해석 
미디어 그래프를 실행하면 동작 감지기 프로세서 노드의 결과가 IoT Hub 싱크 노드를 통해 IoT 허브에 전송됩니다. Visual Studio Code의 **출력** 창에 표시되는 메시지에는 `body` 섹션과 `applicationProperties` 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 Live Video Analytics 모듈은 애플리케이션 속성 및 본문의 콘텐츠를 정의합니다.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

미디어 그래프가 인스턴스화되면 RTSP 원본 노드가 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결에 성공하면 다음 이벤트가 출력됩니다.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

앞의 출력에서 

* 메시지는 진단 이벤트 `MediaSessionEstablished`입니다. RTSP 원본 노드(주체)에서 RTSP 시뮬레이터와의 연결을 설정하고 라이브 피드(시뮬레이션된)를 받기 시작했다는 것을 나타냅니다.
* `applicationProperties`에서 `subject`는 메시지가 생성된 그래프 토폴로지의 노드를 참조합니다. 여기서는 메시지가 RTSP 원본 노드에서 시작됩니다.
* `applicationProperties`에서 `eventType`은 이 이벤트가 진단 이벤트임을 나타냅니다.
* `eventTime` 값은 이벤트가 발생한 시간을 나타냅니다.
* `body` 섹션에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 예제의 데이터는 [SDP(세션 설명 프로토콜)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보로 구성됩니다.

### <a name="recordingstarted-event"></a>RecordingStarted 이벤트

동작이 감지되면 신호 게이트 프로세서 노드가 활성화되고 미디어 그래프의 파일 싱크 노드가 MP4 파일 쓰기를 시작합니다. 파일 싱크 노드가 작업 이벤트를 보냅니다. `type`은 동작 감지 프로세서의 결과임을 나타내기 위해 `motion`으로 설정됩니다. `eventTime` 값은 동작이 발생한 UTC 시간입니다. 이 프로세스에 대한 자세한 내용은 이 빠른 시작의 [개요](#overview) 섹션을 참조하세요.

다음은 이 메시지의 예제입니다.

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

위의 메시지에서 

* `applicationProperties`에서 `subject`는 메시지가 생성된 미디어 그래프의 노드를 참조합니다. 여기서는 메시지가 파일 싱크 노드에서 시작됩니다.
* `applicationProperties`에서 `eventType`은 이 이벤트가 작동 중임을 나타냅니다.
* `eventTime` 값은 이벤트가 발생한 시간을 나타냅니다. 이 시간은 `MediaSessionEstablished`가 지나고 비디오 흐름이 시작된 지 5-6초 후입니다. 이 시간은 [자동차가 주차장에 진입하기 시작한](#review-the-sample-video) 5-6초 마크에 해당합니다.
* `body` 섹션에는 운영 이벤트에 대한 데이터가 포함됩니다. 이 예제의 데이터는 `outputType` 및 `outputLocation`으로 구성됩니다.
* `outputType` 변수는 이 정보가 파일 경로에 대한 것임을 나타냅니다.
* `outputLocation` 값은 에지 모듈에서 MP4 파일의 위치입니다.

### <a name="recordingstopped-and-recordingavailable-events"></a>RecordingStopped 및 RecordingAvailable 이벤트

[그래프 토폴로지](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json)에서 신호 게이트 프로세서 노드의 속성을 살펴보면 활성화 시간이 5초로 설정된 것을 볼 수 있습니다. 따라서 `RecordingStarted` 이벤트가 수신되고 약 5초 후 다음 이벤트가 수신됩니다.

* 녹화가 중지되었음을 나타내는 `RecordingStopped` 이벤트
* 이제 MP4 파일을 볼 수 있음을 나타내는 `RecordingAvailable` 이벤트

일반적으로 두 이벤트는 서로 몇 초 간격을 두고 내보내집니다.

## <a name="play-the-mp4-clip"></a>MP4 클립 재생

MP4 파일은 OUTPUT_VIDEO_FOLDER_ON_DEVICE를 사용하여 *.env* 파일에서 구성한 에지 디바이스의 디렉터리에 기록됩니다. 기본값을 사용했다면 결과는 */home/lvaadmin/samples/output/* 폴더에 있습니다.

MP4 클립을 재생하는 방법은 다음과 같습니다.

1. 리소스 그룹으로 이동하고, VM을 찾고, Azure Bastion을 사용하여 연결합니다.

    ![Resource group](./media/quickstarts/resource-group.png)
    
    ![VM](./media/quickstarts/virtual-machine.png)

1. [Azure 리소스 설정](detect-motion-emit-events-quickstart.md#set-up-azure-resources) 과정에서 생성된 자격 증명을 사용하여 로그인합니다. 
1. 명령 프롬프트에서 관련 디렉터리로 이동합니다. 기본 위치는 */home/lvaadmin/samples/output*입니다. 이 디렉터리에 MP4 파일이 있습니다.

    ![출력](./media/quickstarts/samples-output.png) 

1. [SCP(Secure Copy)](../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md)를 사용하여 로컬 머신에 파일을 복사합니다. 
1. [VLC 미디어 플레이어](https://www.videolan.org/vlc/) 또는 기타 MP4 플레이어를 사용하여 파일을 재생합니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작을 시도하려면 여기서 만든 리소스를 그대로 유지합니다. 그렇지 않으면 Azure Portal에서 리소스 그룹으로 이동하고, 이 빠른 시작을 실행한 리소스 그룹을 선택한 다음, 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

* 라이브 비디오 피드에 AI를 적용하는 [사용자 고유의 모델로 Live Video Analytics 실행](use-your-model-quickstart.md) 빠른 시작을 실행합니다.
* 고급 사용자에 대한 추가 문제를 검토합니다.

    * RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격 제품](https://www.onvif.org/conformant-products) 페이지에서 RTSP를 지원하는 IP 카메라를 찾을 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.
    * Azure에서 Linux VM을 사용하는 대신 AMD64 또는 x64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../iot-edge/how-to-install-iot-edge-linux.md)의 지침을 따릅니다. 그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart-linux.md)의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.

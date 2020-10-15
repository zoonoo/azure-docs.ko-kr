---
title: Live Video Analytics on IoT Edge 및 Azure Custom Vision을 사용하여 라이브 비디오 분석
description: Custom Vision을 사용하여 장난감 트럭을 검색할 수 있는 컨테이너화된 모델을 빌드하는 방법과 Live Video Analytics on IoT Edge(LVA)의 AI 확장 기능을 사용하여 라이브 비디오 스트림에서 장난감 트럭을 검색하는 데 에지에 모델을 배포하는 방법에 대해 알아봅니다.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: e77521765156a13f0675602ffd0b39f78d8957bb
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016794"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>자습서: Live Video Analytics on IoT Edge 및 Azure Custom Vision을 사용하여 라이브 비디오 분석

이 자습서에서 [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)을 사용하여 장난감 트럭을 검색하는 방법과 Live Video Analytics on IoT Edge의 [AI 확장 기능](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model)을 사용하여 라이브 비디오 스트림에서 장난감 트럭을 검색하는 데 에지에 모델을 배포하는 방법에 대해 알아봅니다.

데이터 과학, ML 또는 AI에 대한 지식 없이 누구나 간단히 몇 개의 이미지를 업로드하고 레이블을 지정하여 컴퓨터 비전 모델을 빌드하고 훈련할 수 있는 Custom Vision의 강력한 기능과 사용자 지정 모델을 에지에 컨테이너로 쉽게 배포하고 시뮬레이션된 라이브 비디오 피드를 분석하는 Live Video Analytics 기능을 결합하는 방법을 보여 드리겠습니다. 이 자습서에서는 Azure VM을 IoT Edge 디바이스로 사용하고 C#으로 작성된 샘플 코드를 기반으로 하며, [동작 감지 및 이벤트 내보내기](detect-motion-emit-events-quickstart.md) 빠른 시작을 통해 빌드합니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 관련 리소스 설치
> * 클라우드에서 Custom Vision 모델을 빌드하여 장난감 트럭을 검색하고 에지에 배포
> * 사용자 지정 비전 모델에 대한 http 확장을 사용하여 미디어 그래프를 만들고 배포
> * 샘플 코드 실행
> * 결과를 검사하고 해석

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>추천 참고 자료  

시작하기 전에 다음 문서를 참조하는 것이 좋습니다. 

* [Live Video Analytics on IoT Edge 개요](overview.md)
* [Azure Custom Vision 개요](../../cognitive-services/custom-vision-service/overview.md)
* [Live Video Analytics on IoT Edge 용어](terminology.md)
* [미디어 그래프 개념](media-graph-concept.md)
* [비디오 녹화가 없는 Live Video Analytics](analyze-live-video-concept.md)
* [사용자 고유의 모델로 Live Video Analytics 실행](use-your-model-quickstart.md)
* [자습서: IoT Edge 모듈 개발](../../iot-edge/tutorial-develop-for-linux.md)
* [deployment.*.template.json을 편집하는 방법](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 필수 구성 요소는 다음과 같습니다.

* 개발 컴퓨터의 [Visual Studio Code](https://code.visualstudio.com/)([Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 및 [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 확장 포함)

    > [!TIP]
    > Docker를 설치하라는 메시지가 표시될 수 있습니다. 이 메시지는 무시하세요.
* 개발 머신에 설치된 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer)
* 다음이 있는지 확인합니다.
    
    * [Azure 리소스 설정](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [개발 환경 설정](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)

## <a name="review-the-sample-video"></a>샘플 비디오 검토

이 자습서에서는 [장난감 자동차 유추 비디오](https://lvamedia.blob.core.windows.net/public/t2.mkv) 파일을 사용하여 라이브 스트림을 시뮬레이션합니다. [VLC media player](https://www.videolan.org/vlc/)와 같은 애플리케이션을 통해 비디오를 검사할 수 있습니다. Ctrl+N을 선택하고 [장난감 자동차 유추 비디오](https://lvamedia.blob.core.windows.net/public/t2.mkv)에 대한 링크를 붙여넣어 재생을 시작합니다. 비디오를 시청할 때 36초 마커에서 장난감 트럭이 비디오에 나타납니다. 사용자 지정 모델은 이 특정 장난감 트럭을 검색하도록 학습되었습니다. 이 자습서에서는 Live Video Analytics on IoT Edge를 사용하여 이러한 장난감 트럭을 검색하고 IoT Edge 허브에 관련 유추 이벤트를 게시합니다.

## <a name="overview"></a>개요

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Custom Vision 개요":::

이 다이어그램에서는 이 자습서의 신호 흐름을 보여 줍니다. [에지 모듈](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스팅하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](media-graph-concept.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [프레임 속도 필터 프로세서](media-graph-concept.md#frame-rate-filter-processor) 노드로 보냅니다. 이 프로세서는 [HTTP 확장 프로세서](media-graph-concept.md#http-extension-processor) 노드에 도달하는 비디오 스트림의 프레임 속도를 제한합니다.
HTTP 확장 노드는 프록시 역할을 수행합니다. 비디오 프레임을 지정된 이미지 형식으로 변환합니다. 그런 다음, REST를 통해 이미지를 HTTP 엔드포인트 내부에서 AI 모델을 실행하는 다른 에지 모듈에 릴레이합니다. 이 예에서 에지 모듈은 Custom Vision을 사용하여 빌드된 장난감 트럭 탐지기 모델입니다. HTTP 확장 프로세서 노드는 감지 결과를 수집하고, 이벤트를 [IoT Hub 싱크](media-graph-concept.md#iot-hub-message-sink) 노드에 게시합니다. 그런 다음, 노드에서 이러한 이벤트를 [IoT Edge Hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)에 보냅니다.

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Custom Vision 장난감 검색 모델 빌드 및 배포 

Custom Vision이라는 이름에서 알 수 있듯이 이를 활용하여 클라우드에서 사용자 지정 개체 탐지기 또는 분류자를 빌드할 수 있습니다. 컨테이너를 통해 클라우드 또는 에지에 배포할 수 있는 Custom Vision 모델을 빌드하기 위한 간단하고 사용하기 쉬운 직관적인 인터페이스를 제공합니다. 

장난감 트럭 탐지기를 빌드하려면 웹 포털의 [빠른 시작 문서](../../cognitive-services/custom-vision-service/get-started-build-detector.md)를 통해 이 Custom Vision의 개체 탐지기 빌드를 따르는 것이 좋습니다.

추가 참고 사항:
 
* 이 자습서에서는 빠른 시작 문서의 [필수 조건 섹션](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites)에 제공된 샘플 이미지를 사용하지 마세요. 대신, 특정 이미지 집합을 활용하여 장난감 탐지기 Custom Vision 모델을 빌드했으므로 빠른 시작에서 [학습 이미지를 선택](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images)하라는 메시지가 표시되면 [이러한 이미지](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip)를 사용하는 것이 좋습니다.
* 빠른 시작의 이미지 태그 지정 섹션에서 그림에 표시된 장난감 트럭에 "배달 트럭"이라는 태그를 지정했는지 확인하세요.

완료되면 만족스럽게 모델이 준비된 경우 성능 탭에서 내보내기 단추를 사용하여 Docker 컨테이너로 내보낼 수 있습니다. 컨테이너 플랫폼 유형으로 Linux를 선택했는지 확인하세요. 이는 컨테이너가 실행되는 플랫폼입니다. 컨테이너를 다운로드하는 컴퓨터는 Windows 또는 Linux일 수 있습니다. 다음 지침은 Windows 컴퓨터에 다운로드된 컨테이너 파일을 기반으로 합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Custom Vision 개요"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            이 명령은 로컬 컴퓨터에서 컨테이너를 테스트합니다. 이미지에 모델을 학습했을 때와 동일한 배달 트럭이 있는 경우 출력은 다음과 유사해야 합니다. 배달 트럭이 90.12%의 확률로 검색되었습니다.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>샘플 파일 검사

1. VSCode에서 "src/edge"로 이동합니다. 사용자가 만든 .env 파일이 몇 가지 배포 템플릿 파일과 함께 표시됩니다.

    배포 템플릿은 일부 자리 표시자 값이 있는 에지 디바이스에 대한 배포 매니페스트를 나타냅니다. .env 파일에는 이러한 변수에 대한 값이 있습니다.
1. 그런 다음, "src/cloud-to-device-console-app" 폴더로 이동합니다. 여기에는 앞에서 만든 appsettings.json 파일과 몇 가지 다른 파일이 함께 표시됩니다.

    * c2d-console-app.csproj - VSCode에 대한 프로젝트 파일입니다.
    * operations.json - 이 파일에는 프로그램이 실행할 다양한 작업이 나열됩니다.
    * Program.cs - 다음을 수행하는 샘플 프로그램 코드입니다.

        * 앱 설정을 로드합니다.
        * Live Video Analytics on IoT Edge 모듈의 직접 메서드를 호출하여 토폴로지를 만들고, 그래프를 인스턴스화하고, 그래프를 활성화합니다.
        * TERMINAL 창에서 그래프 출력을 검토하고 OUTPUT 창에서 IoT Hub로 전송된 이벤트를 검사할 수 있도록 일시 중지합니다.
        * 그래프 인스턴스를 비활성화하고, 그래프 인스턴스를 삭제하고, 그래프 토폴로지를 삭제합니다.
        
## <a name="generate-and-deploy-the-deployment-manifest"></a>배포 매니페스트 생성 및 배포

1. VSCode에서 "src/cloud-to-device-console-app/operations.json"으로 이동합니다.

1. GraphTopologySet 아래에서 다음 사항을 충족하는지 확인합니다.<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. GraphInstanceSet 아래에서 다음을 확인합니다. 
    1. "topologyName" : "InferencingWithHttpExtension"
    1. 매개 변수 배열의 맨 위에 다음을 추가합니다. - `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. rtspUrl 매개 변수 값을 "rtsp://rtspsim:554/media/t2.mkv"로 변경합니다.    
1. GraphTopologyDelete 아래에서 "name"을 확인합니다. "InferencingWithHttpExtension"
1. "src/edge/deployment.customvision.template.json" 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 배포 매니페스트 생성**을 클릭합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Custom Vision 개요" 아이콘을 클릭하여 IoTHub 연결 문자열을 설정합니다. appsettings.json 파일에서 문자열을 복사할 수 있습니다. VSCode 내에 적절한 IoT Hub가 구성되도록 하는 데 권장되는 또 다른 방법은 [Iot Hub 선택 명령](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)을 사용하는 것입니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Custom Vision 개요":::
1. 그런 다음, "src/edge/config/ deployment.customvision.amd64.json"을 마우스 오른쪽 단추로 클릭하고 **단일 디바이스용 배포 만들기**를 클릭합니다. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Custom Vision 개요":::
1. 그러면 IoT Hub 디바이스를 선택하라는 메시지가 표시됩니다. 드롭다운 목록에서 lva-sample-device를 선택합니다.
1. 약 30초 후에 왼쪽 하단 섹션에서 Azure IOT Hub를 새로 고치면 다음 모듈이 배포된 에지 디바이스가 있을 것입니다.

    * Live Video Analytics on IoT Edge 모듈(이름 "lvaEdge")
    * RTSP 서버를 시뮬레이션하는 `rtspsim`이라는 모듈은 라이브 비디오 피드의 원본 역할을 합니다.
    * 이름으로 알 수 있듯이 `cv` 모듈은 이미지에 Custom Vision을 적용하고 여러 태그 유형을 반환하는 Custom Vision 장난감 트럭 검색 모델입니다. (이 모델은 "배송 트럭"이라는 하나의 태그에 대해서만 학습되었습니다.)

## <a name="prepare-for-monitoring-events"></a>이벤트 모니터링 준비

마우스 오른쪽 단추로 Live Video Analytics 디바이스를 클릭하고, **기본 제공 이벤트 엔드포인트 모니터링 시작**을 선택합니다. 이 단계는 Visual Studio Code의 출력 창에서 IoT Hub 이벤트를 모니터링하는 데 필요합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Custom Vision 개요":::

## <a name="run-the-sample-program"></a>샘플 프로그램 실행

이 자습서의 그래프 토폴로지를 브라우저에서 열면 inferencingUrl 값이 http://cv:80/image 으로 설정된 것이 보입니다. 따라서, 유추 서버가 라이브 비디오에서 장난감 트럭을 감지하면 결과를 반환합니다.

1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누름) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Custom Vision 개요":::
1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Custom Vision 개요"
              }
            ]
          }
        }
   ```
    
   * 그래프 인스턴스 및 비디오 흐름을 시작하는 GraphInstanceActivate에 대한 호출입니다.
   * 그래프 인스턴스가 실행 중 상태임을 보여주는 두 번째 GraphInstanceList 호출입니다.
    
1. 터미널 창의 출력이 [계속하려면 Enter를 누르세요]라는 메시지에서 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. Visual Studio Code에서 출력 창으로 전환합니다. Live Video Analytics on IoT Edge 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 자습서의 다음 섹션에서는 이러한 메시지를 설명합니다.
1. 미디어 그래프가 계속 실행되어 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 미디어 그래프를 중지하려면 터미널 창으로 돌아가서 Enter 키를 선택합니다.
일련의 다음 호출은 리소스를 정리합니다.
    
   * GraphInstanceDeactivate에 대한 호출이 그래프 인스턴스를 비활성화합니다.
   * GraphInstanceDelete에 대한 호출이 인스턴스를 삭제합니다.
   * GraphTopologyDelete에 대한 호출이 토폴로지를 삭제합니다.
   * GraphTopologyList에 대한 최종 호출에서는 목록이 비어 있음을 보여 줍니다.
    
## <a name="interpret-the-results"></a>결과 해석

미디어 그래프를 실행하면 HTTP 확장 프로세서 노드의 결과가 IoT Hub 싱크 노드를 통해 IoT 허브로 전달됩니다. OUTPUT 창에 표시되는 메시지에는 본문 섹션과 applicationProperties 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 Live Video Analytics 모듈은 애플리케이션 속성 및 본문의 콘텐츠를 정의합니다.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

미디어 그래프가 인스턴스화되면 RTSP 원본 노드가 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결에 성공하면 다음 이벤트가 출력됩니다. 이벤트 유형은 Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished입니다.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

이 메시지에서 다음 세부 정보를 확인합니다.

* 메시지는 진단 이벤트입니다. MediaSessionEstablished는 RTSP 원본 노드(주체)에서 RTSP 시뮬레이터에 연결되었고 시뮬레이션된 라이브 피드를 받기 시작했음을 나타냅니다.
* applicationProperties에서 subject는 메시지가 미디어 그래프의 RTSP 원본 노드에서 생성되었음을 나타냅니다.
* applicationProperties에서 eventType은 이 이벤트가 진단 이벤트임을 나타냅니다.
* eventTime은 이벤트가 발생한 시간을 나타냅니다.
* 본문에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 경우 데이터는 [SDP(세션 설명 프로토콜)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보로 구성됩니다.

### <a name="inference-event"></a>유추 이벤트

HTTP 확장 프로세서 노드는 Custom Vision 컨테이너에서 유추 결과를 받고 IoT Hub 싱크 노드를 통해 결과를 유추 이벤트로 내보냅니다.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

위의 메시지에서 다음 사항을 확인하세요.

* applicationProperties의 subject는 메시지가 생성된 MediaGraph의 노드를 나타냅니다. 이 경우 메시지는 Http 확장 프로세서에서 온 것입니다.
* applicationProperties의 eventType은 분석 유추 이벤트임을 나타냅니다.
* eventTime은 이벤트가 발생한 시간을 나타냅니다.
* "body"는 분석 이벤트에 대한 데이터를 포함합니다. 이 경우 이벤트는 유추 이벤트이므로 본문에는 "예측"이라는 유추의 배열이 포함됩니다.
* "예측" 섹션에는 프레임에서 장난감 배달 트럭(태그=배달 트럭)이 발견된 예측 목록이 포함되어 있습니다. 기억하시겠지만 배달 트럭은 장난감 트럭에 대해 사용자 지정 학습된 모델에 제공한 사용자 지정 태그이며, 모델은 다른 확률 신뢰도 점수를 사용하여 입력 비디오에서 장난감 트럭을 유추하고 식별합니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 자습서 또는 빠른 시작을 진행하려면 생성된 리소스를 유지해야 합니다. 다른 자습서를 진행할 생각이 없으면 Azure Portal로 이동하여 해당하는 리소스 그룹을 찾고, 이 자습서를 실행한 리소스 그룹을 선택하고, 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

고급 사용자에 대한 추가 문제를 검토합니다.

* RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격](https://www.onvif.org/conformant-products/) 제품 페이지에서 RTSP를 지원하는 IP 카메라를 검색할 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.
* Azure Linux VM 대신 AMD64 또는 x64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../iot-edge/how-to-install-iot-edge-linux.md)의 지침을 따릅니다. 

그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart-linux.md)의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
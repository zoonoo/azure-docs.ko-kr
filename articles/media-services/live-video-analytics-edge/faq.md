---
title: IoT Edge Faq의 Live Video Analytics-Azure
description: 이 항목에서는 IoT Edge Faq에서 라이브 비디오 분석에 대 한 답변을 제공 합니다.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401579"
---
# <a name="frequently-asked-questions-faqs"></a>FAQ(질문과 대답)

이 항목에서는 IoT Edge Faq에서 라이브 비디오 분석에 대 한 답변을 제공 합니다.

## <a name="general"></a>일반

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>그래프 토폴로지 정의에서 사용할 수 있는 시스템 변수는 무엇 인가요?

|변수   |설명|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|일반적으로 날짜 및 시간 (기본 표현 yyyyMMddTHHmmssZ)으로 표현 되는 UTC 시간의 인스턴트를 나타냅니다.|
|PreciseDateTime|밀리초 (기본 표현 yyyyMMddTHHmmss)를 사용 하는 ISO8601 파일 호환 형식으로 된 UTC 날짜 시간 인스턴스를 나타냅니다.|
|GraphTopologyName   |그래프의 청사진을 보유 하는 미디어 그래프 토폴로지를 나타냅니다.|
|System.object 인스턴스 이름|  미디어 그래프 인스턴스를 나타내고, 매개 변수 값을 보유 하 고, 토폴로지를 참조 합니다.|

## <a name="configuration-and-deployment"></a>구성 및 배포

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>미디어에 지 모듈을 Windows 10 장치에 배포할 수 있나요?

예. [Windows 10의 Linux 컨테이너](/virtualization/windowscontainers/deploy-containers/linux-containers)에 대 한 문서를 참조 하세요.

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>IP 카메라 및 RTSP 설정에서 캡처

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>비디오 스트림으로 보내려면 디바이스에서 특별한 SDK를 사용해야 하나요?

아니요. IoT Edge의 Live Video Analytics는 RTSP 비디오 스트리밍 프로토콜 (대부분의 IP 카메라에서 지원 됨)을 사용 하 여 미디어 캡처를 지원 합니다.

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>RTMP 또는 부드러운 (예: Media Services 라이브 이벤트)를 사용 하 여 IoT Edge에서 라이브 비디오 분석으로 미디어를 푸시할 수 있나요?

* 아니요. Live Video Analytics는 IP 카메라에서 비디오를 캡처하기 위한 RTSP만 지원 합니다.
* TCP/HTTP를 통한 RTSP 스트리밍을 지 원하는 모든 카메라는 작동 합니다. 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>그래프 인스턴스에서 RTSP 원본 URL을 다시 설정하거나 업데이트할 수 있나요?

예 (graph 인스턴스가 비활성 상태인 경우)  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>테스트 및 개발 하는 동안 사용할 수 있는 RTSP 시뮬레이터가 있나요?

예. 학습 프로세스를 지원 하기 위한 빠른 시작 및 자습서에서 사용할 수 있는 [RTSP 시뮬레이터](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) edge 모듈이 있습니다. 이 모듈은 최상의 노력으로 제공되며 항상 사용할 수 있는 것은 아닙니다. 이를 몇 시간 넘게 사용 하지 않는 것이 좋습니다. 프로덕션 배포에 대 한 계획을 만들기 전에 실제 RTSP 원본으로 테스트 하는 데 투자 해야 합니다.

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>Edge에서 IP 카메라의 ONVIF 검색을 지원하나요?

아니요. Edge에서 디바이스에 대한 ONVIF 검색을 지원하지 않습니다.

## <a name="streaming-and-playback"></a>스트리밍 및 재생

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>가장자리에서 AMS로 기록 된 자산은 HLS 또는 대시와 같은 Media Services 스트리밍 기술을 사용 하 여 재생할 수 있나요?

예. 기록 된 자산은 Azure Media Services의 다른 자산과 같이 스트리밍할 수 있습니다. 콘텐츠를 스트리밍하려면 스트리밍 끝점이 생성 되 고 실행 중 상태 여야 합니다. 표준 스트리밍 로케이터 생성 프로세스를 사용 하면 모든 지원 플레이어 프레임 워크에 스트리밍하기 위해 HLS 또는 대시 매니페스트에 액세스할 수 있습니다. HLS 또는 대시 매니페스트 게시를 만드는 방법에 대 한 자세한 내용은 [동적 패키징](../latest/dynamic-packaging-overview.md)을 참조 하세요.

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>보관 된 자산에서 Media Services의 표준 콘텐츠 보호 및 DRM 기능을 사용할 수 있나요?

예. 미디어 그래프에서 기록 된 자산에는 표준 동적 암호화 콘텐츠 보호 및 DRM 기능을 모두 사용할 수 있습니다.

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>기록 된 자산의 콘텐츠를 보는 데 사용할 수 있는 플레이어는 무엇입니까?

규격 Apple HTTP 라이브 스트리밍 (HLS) 버전 3 또는 버전 4를 지 원하는 모든 표준 플레이어가 지원 됩니다. 또한 규격 MPEG 대시 재생을 지 원하는 플레이어도 지원 됩니다.

테스트에 권장 되는 플레이어는 다음과 같습니다.

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka 플레이어](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple 네이티브 HTTP 라이브 스트리밍](https://developer.apple.com/streaming/)
* HTML5 비디오 플레이어에서 기본 제공 되는 Edge, Chrome 또는 Safari
* HLS 또는 대시 재생을 지 원하는 상업적 플레이어

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>미디어 그래프 자산의 스트리밍에 대 한 제한은 무엇 인가요?

미디어 그래프에서 라이브 또는 기록 된 자산을 스트리밍하는 것은 미디어 & 엔터테인먼트, Media Services 및 브로드캐스트 고객에 대 한 주문형 및 라이브 스트리밍에 대해 지원 되는 것과 동일한 높은 규모의 인프라 및 스트리밍 끝점을 사용 합니다. 즉, Verizon 또는 Akamai Azure CDN, Verizon 또는 Akamai의 사용자에 게 콘텐츠를 제공 하는 데 사용 하거나 시나리오에 따라 최대 수백만 명의 사용자에 게 콘텐츠를 제공할 수 있습니다.

Apple HTTP 라이브 스트리밍 (HLS) 또는 MPEG-4를 모두 사용 하 여 콘텐츠를 배달할 수 있습니다.

## <a name="design-your-ai-model"></a>AI 모델 디자인 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>Docker 컨테이너에 여러 AI 모델을 래핑 했습니다. 라이브 비디오 분석과 함께 사용 하려면 어떻게 해야 하나요? 

솔루션은 Live Video Analytics와 통신 하기 위해 추론 서버에서 사용 하는 통신 프로토콜에 따라 달라 집니다. 이 작업을 수행 하는 몇 가지 방법은 다음과 같습니다.

#### <a name="http-protocol"></a>HTTP 프로토콜:

* 단일 컨테이너 (단일 lvaExtension):  

   추론 서버에서 단일 포트를 사용할 수 있지만 여러 AI 모델에 대해 서로 다른 끝점을 사용할 수 있습니다. 예를 들어 Python 샘플의 경우 `route` 다음과 같이 모델 별로 서로 다른 s를 사용할 수 있습니다. 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   그런 다음 라이브 비디오 분석 배포에서 그래프를 인스턴스화할 때 각 인스턴스의 유추 서버 URL을 다음과 같이 설정 합니다. 

   첫 번째 인스턴스: 유추 서버 URL =`http://lvaExtension:44000/score/face_detection`<br/>
   두 번째 인스턴스: 유추 서버 URL =`http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > 또는 그래프를 인스턴스화할 때 AI 모델을 다른 포트에 노출 하 고 호출할 수도 있습니다.  

* 여러 컨테이너: 

   각 컨테이너는 다른 이름으로 배포 됩니다. 현재 라이브 비디오 분석 설명서 집합에서 이름이 **lvaExtension** 인 확장을 배포 하는 방법을 살펴보았습니다. 이제 두 가지 컨테이너를 개발할 수 있습니다. 각 컨테이너는 동일한 HTTP 인터페이스 (즉, 동일한 `/score` 끝점)를 가집니다. 이러한 두 컨테이너를 다른 이름으로 배포 하 고 둘 다 **다른 포트** 에서 수신 하 고 있는지를 확인할 수 있습니다. 

   예를 들어 이름이 인 한 컨테이너 `lvaExtension1` 는 포트를 수신 대기 하 고, `44000` 해당 이름을 가진 다른 컨테이너는 `lvaExtension2` 포트를 수신 대기 `44001` 합니다. 

   라이브 비디오 분석 토폴로지에서는 다음과 같은 다양 한 유추 Url을 사용 하 여 두 개의 그래프를 인스턴스화합니다. 

   첫 번째 인스턴스: 유추 서버 URL = `http://lvaExtension1:44001/score`    
   두 번째 인스턴스: 유추 서버 URL = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>GRPC 프로토콜: 

라이브 비디오 분석 모듈 1.0를 사용 하는 경우 gRPC 프로토콜을 사용 하는 경우 gRPC 서버에서 다른 포트를 통해 서로 다른 AI 모델을 노출 하는 경우에만이 방법이 가능 합니다. [이 예제](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)에는 모든 yolo 모델을 노출 하는 단일 포트 44000이 있습니다. 이론적으로는 yolo gRPC 서버를 다시 작성 하 여 44000, 다른 일부 모델은 45000, ... 

라이브 비디오 분석 모듈 2.0을 사용 하면 gRPC 확장 노드에 새 속성이 추가 됩니다. 이 속성은 gRPC 계약의 일부로 사용할 수 있는 선택적 문자열인 **Extensionconfiguration** 이라고 합니다. 단일 유추 서버에 여러 AI 모델이 패키지 된 경우에는 모든 AI 모델에 대 한 노드를 노출할 필요가 없습니다. 대신, 그래프 인스턴스의 경우 확장 공급자 (사용자)가 **Extensionconfiguration** 속성을 사용 하 여 여러 ai 모델을 선택 하는 방법을 정의 하 고 실행 하는 동안 Live Video Analytics는이 문자열을 추론 서버에 전달 합니다 .이 문자열을 사용 하 여 원하는 AI 모델을 호출할 수 있습니다. 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>AI 모델을 중심으로 gRPC 서버를 빌드하고 여러 카메라/그래프 인스턴스에서 사용 되는 것을 지원 하려고 합니다. 내 서버를 빌드하려면 어떻게 해야 하나요? 

 먼저 서버에서 한 번에 두 개 이상의 요청을 처리할 수 있어야 합니다. 또는 서버가 병렬 스레드에서 작동 하는지 확인할 수 있습니다. 

예를 들어 [Live Video Analytics GRPC 샘플](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)중 하나에는 기본 병렬 채널 수가 설정 되어 있습니다. 다음을 참조하세요. 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

위의 gRPC 서버 인스턴스화에 있으면 서버는 카메라 당 3 개의 채널 (그래프 토폴로지 인스턴스당)을 한 번에 하나만 열 수 있습니다. 세 개 이상의 인스턴스를 서버에 연결 해서는 안 됩니다. 3 개 이상의 채널을 열려고 하면 기존 요청이 삭제 될 때까지 요청이 보류 됩니다.  

위의 gRPC 서버 구현은 Python 샘플에서 사용 됩니다. 개발자는 자신의 서버를 구현 하거나 위의 기본 구현에서 작업자 수를 비디오 피드를 가져오는 데 사용 되는 카메라 수로 늘릴 수 있습니다. 

여러 카메라를 설정 하 고 사용 하기 위해 개발자는 각 인스턴스가 동일 하거나 다른 유추 서버 (예: 위 단락에서 언급 한 서버)를 가리키는 여러 그래프 토폴로지 인스턴스를 인스턴스화할 수 있습니다. 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>추론 의사 결정을 내리기 전에 업스트림에서 여러 프레임을 받을 수 있기를 원합니다. 이 기능을 사용 하도록 설정 하려면 어떻게 해야 하나요? 

현재 [기본 샘플](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) 은 "상태 비저장" 모드로 작동 합니다. 이러한 샘플은 이전 호출의 상태를 유지 하지 않고를 호출 하는 경우에도 발생 합니다. 즉, 여러 토폴로지 인스턴스가 동일한 유추 서버를 호출할 수 있으며 서버는 호출자 당 및 상태를 호출 하는 사용자를 구분할 수 없습니다. 

#### <a name="http-protocol"></a>HTTP 프로토콜

HTTP 프로토콜을 사용 하는 경우: 

상태를 유지 하기 위해 각 호출자 (그래프 토폴로지 인스턴스)는 호출자에 고유한 HTTP 쿼리 매개 변수를 사용 하 여 추론 서버를 호출 합니다. 예:에 대 한 유추 서버 URL 주소  

첫 번째 토폴로지 인스턴스 = `http://lvaExtension:44000/score?id=1`<br/>
두 번째 토폴로지 인스턴스 = `http://lvaExtension:44000/score?id=2`

… 

서버 쪽에서 점수를 매길 때 점수를 확인할 수 있습니다. ID = 1 인 경우 해당 호출자에 대해 상태를 별도로 유지할 수 있습니다 (그래프 토폴로지 인스턴스). 그런 다음 받은 비디오 프레임을 버퍼 (예: 배열 또는 DateTime 키가 있는 사전)에 보관 한 다음 x 프레임을 받은 후 처리할 서버 (유추)를 정의할 수 있습니다. 

#### <a name="grpc-protocol"></a>GRPC 프로토콜 

GRPC 프로토콜을 사용 하는 경우: 

GRPC 확장을 사용 하는 경우 각 세션은 단일 카메라 피드에 대 한 것 이므로 ID를 제공할 필요가 없습니다. 이제 extensionConfiguration 속성을 사용 하 여 비디오 프레임을 버퍼에 저장 하 고 x 프레임을 받은 후 처리할 서버를 정의할 수 있습니다 (유추). 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>특정 컨테이너의 모든 ProcessMediaStreams이 동일한 AI 모델을 실행 하나요? 

아니요.  

Graph 인스턴스의 최종 사용자에 대 한 Start/stop 호출은 세션을 구성 하거나 카메라 연결 끊기/다시 연결 하는 것일 수 있습니다. 카메라에서 비디오를 스트리밍하는 경우 한 세션을 유지 하는 것이 목표입니다. 

* 처리를 위해 비디오를 보내는 두 카메라는 두 개의 세션을 만듭니다. 
* 두 gRPCExtension 노드가 있는 그래프로 이동 하는 한 카메라는 두 개의 세션을 만듭니다. 

각 세션은 라이브 비디오 분석과 gRPC 서버 간의 전이중 연결 이며 각 세션은 다른 모델/파이프라인을 가질 수 있습니다. 

> [!NOTE]
> 카메라 연결 끊기/다시 연결 (허용 오차 제한을 초과 하는 기간 동안 카메라가 오프 라인으로 전환)의 경우 Live Video Analytics는 gRPC 서버를 사용 하 여 새 세션을 엽니다. 서버에서 이러한 세션의 상태를 추적 하는 요구 사항은 없습니다. 

또한 라이브 비디오 분석에서는 graph 인스턴스의 단일 카메라에 대 한 여러 gRPC 확장 지원이 추가 되었습니다. 이러한 gRPC 확장을 사용 하 여 AI 처리를 순차적으로 또는 병렬로 수행 하거나 둘의 조합을 사용할 수 있습니다. 

> [!NOTE]
> 여러 확장을 병렬로 실행 하는 경우 하드웨어 리소스에 영향을 주므로 컴퓨팅 요구에 맞는 하드웨어를 선택 하는 동안이 점을 염두에 두어야 합니다. 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>동시 ProcessMediaStreams의 최대 개수는 몇 개입니까? 

Live Video Analytics는 적용 되는 제한이 없습니다.  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>추론 서버에서 CPU 또는 GPU 또는 다른 하드웨어 가속기를 사용 해야 하는지 어떻게 결정 하나요? 

이는 AI 모델 개발의 복잡성과 개발자가 CPU 및 하드웨어 가속기를 사용 하는 방법에 전적으로 의존 합니다. AI 모델을 개발 하는 동안 개발자는 모델에서 어떤 작업을 수행 하는 데 사용할 리소스를 지정할 수 있습니다. 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>처리 후 경계 상자를 사용 하 여 이미지를 저장 어떻게 할까요?? 

현재는 경계 상자 좌표를 유추 메시지로만 제공 합니다. 개발자는 이러한 메시지를 사용 하 고 비디오 프레임에 경계 상자를 오버레이 하는 사용자 지정 MJPEG 스 트리머을 빌드할 수 있습니다.  

## <a name="grpc-compatibility"></a>gRPC 호환성 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>미디어 스트림 설명자에 대 한 필수 필드는 어떻게 알 수 있나요? 

지정 되지 않은 모든 필드 값에는 [gRPC에 지정](https://developers.google.com/protocol-buffers/docs/proto3#default)된 기본값이 지정 됩니다.  

Live Video Analytics는 **proto3** 버전의 프로토콜 버퍼 언어를 사용 합니다. 라이브 비디오 분석 계약에 사용 되는 모든 프로토콜 버퍼 데이터는 [여기에 정의 된](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)프로토콜 버퍼 파일에서 사용할 수 있습니다. 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>최신 프로토콜 버퍼 파일을 사용 하 고 있는지 확인 하려면 어떻게 해야 하나요? 

최신 프로토콜 버퍼 파일은 여기에서 [가져올](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)수 있습니다. 계약 파일을 업데이트할 때마다이 위치에 표시 됩니다. 프로토콜 파일을 업데이트 하는 즉시 계획은 없지만 파일 맨 위에 있는 패키지 이름을 확인 하 여 버전을 확인 합니다. 이 단계는 다음과 같아야 합니다. 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

이러한 파일에 대 한 모든 업데이트는 이름 끝의 "v-값"을 증가 시킵니다. 

> [!NOTE]
> Live Video Analytics는 언어의 proto3 버전을 사용 하므로 필드는 선택 사항이 며,이 필드는 이전 버전과 이후 버전과 호환 됩니다. 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>Live Video Analytics에서 사용할 수 있는 gRPC 기능 필수 기능과 선택적인 기능은 무엇 인가요? 

Protobuf 계약이 충족 되 면 서버 쪽 gRPC 기능을 사용할 수 있습니다. 

## <a name="monitoring-and-metrics"></a>모니터링 및 메트릭

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>Event Grid를 사용 하 여에 지에서 미디어 그래프를 모니터링할 수 있나요?

예. 프로메테우스 메트릭을 사용 하 여 event grid에 게시할 수 있습니다. 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>Azure Monitor를 사용 하 여 클라우드 또는에 지에서 내 미디어 그래프의 상태, 메트릭 및 성능을 볼 수 있나요?

예. 지원됩니다. [Azure Monitor 메트릭을 사용 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)에 대해 자세히 알아보세요.

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>Media Services IoT Edge 모듈을 보다 쉽게 모니터링할 수 있는 도구가 있나요?

Visual Studio Code는 LVAEdge 모듈 끝점을 쉽게 모니터링할 수 있는 "Azure IoT Tools" 확장을 지원 합니다. 이 도구를 사용 하 여 "이벤트"에 대 한 IoT Hub 기본 제공 끝점 모니터링을 신속 하 게 시작 하 고에 지 장치에서 클라우드로 라우팅되는 유추 메시지를 확인할 수 있습니다. 

또한이 확장을 사용 하 여 LVAEdge 모듈에 대 한 모듈 쌍을 편집 하 여 미디어 그래프 설정을 수정할 수 있습니다.

자세한 내용은 [모니터링 및 로깅](monitoring-logging.md) 문서를 참조 하세요.

## <a name="billing-and-availability"></a>요금 청구 및 가용성

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>IoT Edge에 대 한 Live Video Analytics는 어떻게 청구 되나요?

자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[빠른 시작: 시작 - IoT Edge의 Live Video Analytics](get-started-detect-motion-emit-events-quickstart.md)

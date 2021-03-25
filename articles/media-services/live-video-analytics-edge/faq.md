---
title: IoT Edge FAQ에서 라이브 비디오 분석-Azure
description: 이 문서에서는 IoT Edge에 대 한 라이브 비디오 분석에 대 한 자주 묻는 질문에 답변 합니다.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 661b6155ce2d95e2111a1fa338fd5df438e61e7d
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105032802"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>IoT Edge FAQ의 Live Video Analytics

이 문서에서는 Azure IoT Edge에 대 한 라이브 비디오 분석에 대 한 자주 묻는 질문에 답변 합니다.

## <a name="general"></a>일반

**그래프 토폴로지 정의에서 사용할 수 있는 시스템 변수는 무엇 인가요?**

| 변수   |  Description  | 
| --- | --- | 
| [System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | UTC 시간의 인스턴트를 나타냅니다. 일반적으로 다음과 같은 형식으로 날짜와 시간으로 표현 됩니다.<br>*yyyyMMddTHHmmssZ* | 
| PreciseDateTime | 밀리초를 사용 하는 ISO8601 파일 호환 형식의 UTC (협정 세계시) 날짜/시간 인스턴스를 다음 형식으로 나타냅니다.<br>*yyyyMMddTHHmmss* | 
| GraphTopologyName   | 미디어 그래프 토폴로지를 나타내며 그래프의 청사진을 보유 합니다. | 
| System.object 인스턴스 이름 |    미디어 그래프 인스턴스를 나타내고, 매개 변수 값을 보유 하 고, 토폴로지를 참조 합니다. | 

## <a name="configuration-and-deployment"></a>구성 및 배포

**미디어에 지 모듈을 Windows 10 장치에 배포할 수 있나요?**

예. 자세한 내용은 [Windows 10의 Linux 컨테이너](/virtualization/windowscontainers/deploy-containers/linux-containers)를 참조 하세요.

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>IP 카메라 및 RTSP 설정에서 캡처

**비디오 스트림으로 보내려면 디바이스에서 특별한 SDK를 사용해야 하나요?**

아니요, IoT Edge의 Live Video Analytics는 대부분의 IP 카메라에서 지원 되는 비디오 스트리밍에 RTSP (실시간 스트리밍 프로토콜)를 사용 하 여 미디어 캡처를 지원 합니다.

**RTMP (Real-Time Messaging Protocol) 또는 부드러운 스트리밍 프로토콜 (예: Media Services 라이브 이벤트)을 사용 하 여 IoT Edge에서 라이브 비디오 분석으로 미디어를 푸시할 수 있나요?**

아니요, Live Video Analytics는 IP 카메라에서 비디오를 캡처하기 위한 RTSP만 지원 합니다. TCP/HTTP를 통한 RTSP 스트리밍을 지 원하는 모든 카메라는 작동 합니다. 

**그래프 인스턴스에서 RTSP 원본 URL을 다시 설정 하거나 업데이트할 수 있나요?**

예 (graph 인스턴스가 *비활성* 상태인 경우)  

**테스트 및 개발 하는 동안 사용할 수 있는 RTSP 시뮬레이터가 있나요?**

예, [RTSP 시뮬레이터](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) edge 모듈은 빠른 시작 및 자습서에서 학습 프로세스를 지 원하는 데 사용할 수 있습니다. 이 모듈은 최상의 노력으로 제공 되며 항상 사용할 수 있는 것은 아닙니다. 시뮬레이터를 몇 시간 넘게 사용 *하지* 않는 것이 좋습니다. 프로덕션 배포를 계획 하기 전에 실제 RTSP 원본으로 테스트에 투자 해야 합니다.

**Edge에서 IP 카메라의 ONVIF 검색을 지원하나요?**

아니요, edge에서 장치에 대 한 오픈 네트워크 비디오 인터페이스 포럼 (ONVIF) 검색을 지원 하지 않습니다.

## <a name="streaming-and-playback"></a>스트리밍 및 재생

**HLS 또는 대시와 같은 스트리밍 기술을 사용 하 여에 지에서 Azure Media Services에 기록 된 자산을 재생할 수 있나요?**

예. Azure Media Services에서 다른 자산과 같이 기록 된 자산을 스트리밍할 수 있습니다. 콘텐츠를 스트리밍하려면 스트리밍 끝점이 생성 되 고 실행 중 상태 여야 합니다. 표준 스트리밍 로케이터 생성 프로세스를 사용 하면 모든 지원 플레이어 프레임 워크에 스트리밍하기 위해 HTTP (대시로 라고도 함)를 통한 HLS (Apple HTTP 라이브 스트리밍) 또는 동적 적응 스트리밍에 액세스할 수 있습니다. HLS 또는 대시 매니페스트를 만들고 게시 하는 방법에 대 한 자세한 내용은 [동적 패키징](../latest/dynamic-packaging-overview.md)을 참조 하세요.

**보관 된 자산에서 Media Services의 표준 콘텐츠 보호 및 DRM 기능을 사용할 수 있나요?**

예. 미디어 그래프에서 기록 된 자산에는 모든 표준 동적 암호화 콘텐츠 보호 및 DRM (디지털 권한 관리) 기능을 사용할 수 있습니다.

**기록 된 자산의 콘텐츠를 보는 데 사용할 수 있는 플레이어는 무엇입니까?**

규격 HLS 버전 3 또는 버전 4를 지 원하는 모든 표준 플레이어가 지원 됩니다. 또한 규격 MPEG 대시 재생을 지 원하는 플레이어도 지원 됩니다.

테스트에 권장 되는 플레이어는 다음과 같습니다.

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka 플레이어](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple 네이티브 HTTP 라이브 스트리밍](https://developer.apple.com/streaming/)
* Edge, Chrome 또는 Safari 기본 제공 HTML5 비디오 플레이어
* HLS 또는 대시 재생을 지 원하는 상업적 플레이어

**미디어 그래프 자산의 스트리밍에 대 한 제한은 무엇 인가요?**

미디어 그래프에서 라이브 또는 기록 된 자산을 스트리밍하는 것은 동일한 대규모 인프라 및 스트리밍 끝점을 사용 합니다 .이 끝점은 미디어 & 엔터테인먼트를 위한 주문형 및 라이브 스트리밍 (이상) 및 브로드캐스트 고객에 대해 지원 Media Services 합니다. 즉, 시나리오에 따라 Azure Content Delivery Network, Verizon 또는 Akamai를 빠르고 쉽게 사용 하도록 설정 하 여 적은 수의 사용자에 게 콘텐츠를 제공할 수 있습니다.

Apple HLS 또는 MPEG-4를 사용 하 여 콘텐츠를 배달할 수 있습니다.

## <a name="design-your-ai-model"></a>AI 모델 디자인 

**Docker 컨테이너에 여러 AI 모델을 래핑 했습니다. 라이브 비디오 분석과 함께 사용 하려면 어떻게 해야 하나요?** 

솔루션은 추론 서버에서 라이브 비디오 분석과 통신 하는 데 사용 하는 통신 프로토콜에 따라 달라 집니다. 다음 섹션에서는 각 프로토콜의 작동 방식에 대해 설명 합니다.

*HTTP 프로토콜을 사용 합니다*.

* 단일 컨테이너 (단일 lvaExtension):  

   추론 서버에서 단일 포트를 사용할 수 있지만 여러 AI 모델에 대해 서로 다른 끝점을 사용할 수 있습니다. 예를 들어 Python 샘플의 경우 다음과 같이 모델 별로 다른를 사용할 수 있습니다 `route` . 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   그런 다음 라이브 비디오 분석 배포에서 그래프를 인스턴스화할 때 다음과 같이 각 인스턴스에 대 한 유추 서버 URL을 설정 합니다. 

   첫 번째 인스턴스: 유추 서버 URL =`http://lvaExtension:44000/score/face_detection`<br/>
   두 번째 인스턴스: 유추 서버 URL =`http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > 또는 그래프를 인스턴스화할 때 AI 모델을 다른 포트에 노출 하 고 호출할 수 있습니다.  

* 여러 컨테이너: 

   각 컨테이너는 다른 이름으로 배포 됩니다. 이전에는 라이브 비디오 분석 설명서 집합에서 이름이 *lvaExtension* 인 확장을 배포 하는 방법을 살펴보았습니다. 이제 동일한 HTTP 인터페이스를 사용 하는 두 개의 서로 다른 컨테이너를 개발할 수 있습니다. 즉, 동일한 `/score` 끝점을 사용 합니다. 이러한 두 컨테이너를 다른 이름으로 배포 하 고 둘 다 *다른 포트* 에서 수신 하 고 있는지 확인 합니다. 

   예를 들어 라는 하나의 컨테이너는 `lvaExtension1` 포트를 수신 대기 `44000` 하 고 라는 두 번째 컨테이너는 `lvaExtension2` 포트를 수신 대기 `44001` 합니다. 

   라이브 비디오 분석 토폴로지에서는 다음과 같이 여러 가지 유추 Url을 사용 하 여 두 개의 그래프를 인스턴스화합니다. 

   첫 번째 인스턴스: 유추 서버 URL = `http://lvaExtension1:44001/score`    
   두 번째 인스턴스: 유추 서버 URL = `http://lvaExtension2:44001/score`
   
*GRPC 프로토콜을 사용 합니다*. 

* 라이브 비디오 분석 모듈 1.0를 사용 하 여 범용 원격 프로시저 호출 (gRPC) 프로토콜을 사용 하는 경우 gRPC 서버에서 다른 포트를 통해 서로 다른 AI 모델을 노출 하는 경우에만이 작업을 수행할 수 있습니다. [이 코드 예제](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json)에서 단일 포트인 44000는 모든 yolo 모델을 노출 합니다. 이론적으로 yolo gRPC 서버를 다시 작성 하 여 포트 44000 및 포트 45000의 다른 모델을 노출할 수 있습니다. 

* 라이브 비디오 분석 모듈 2.0을 사용 하면 gRPC 확장 노드에 새 속성이 추가 됩니다. **Extensionconfiguration** 속성은 grpc 계약의 일부로 사용할 수 있는 선택적 문자열입니다. 단일 유추 서버에 여러 AI 모델이 패키지 된 경우에는 모든 AI 모델에 대 한 노드를 노출할 필요가 없습니다. 대신, 그래프 인스턴스의 경우 확장 공급자는 **Extensionconfiguration** 속성을 사용 하 여 여러 AI 모델을 선택 하는 방법을 정의할 수 있습니다. 실행 하는 동안 Live Video Analytics는이 문자열을 추론 서버에 전달 하 여 원하는 AI 모델을 호출 하는 데 사용할 수 있습니다. 

**AI 모델을 중심으로 gRPC 서버를 빌드하고 여러 카메라 또는 그래프 인스턴스에서 사용을 지원할 수 있도록 하려고 합니다. 내 서버를 빌드하려면 어떻게 해야 하나요?** 

 먼저 서버에서 한 번에 둘 이상의 요청을 처리 하거나 병렬 스레드에서 작업을 수행할 수 있어야 합니다. 

예를 들어 다음과 같은 [라이브 비디오 분석 gRPC 샘플](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)에서 기본 병렬 채널 수가 설정 되었습니다. 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

이전 gRPC 서버 인스턴스화에 서 서버는 카메라 당 한 번 또는 그래프 토폴로지 인스턴스당 3 개의 채널만 열 수 있습니다. 3 개 이상의 인스턴스를 서버에 연결 하지 마세요. 3 개 이상의 채널을 열려고 하면 기존 채널이 삭제 될 때까지 요청이 보류 됩니다.  

이전 gRPC 서버 구현은 Python 샘플에서 사용 됩니다. 개발자는 사용자 고유의 서버를 구현 하거나 앞의 기본 구현을 사용 하 여 작업자 수를 늘릴 수 있습니다 .이 수치는 비디오 피드에 사용할 카메라 수로 설정 됩니다. 

여러 카메라를 설정 하 고 사용 하려면 각각 동일 하거나 다른 유추 서버 (예: 이전 단락에서 언급 한 서버)를 가리키는 여러 그래프 토폴로지 인스턴스를 인스턴스화할 수 있습니다. 

**추론 의사 결정을 내리기 전에 업스트림에서 여러 프레임을 받을 수 있기를 원합니다. 이 기능을 사용 하도록 설정 하려면 어떻게 해야 하나요?** 

현재 [기본 샘플](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) 은 *상태 비저장* 모드에서 작동 합니다. 이전 호출 또는를 호출한 사용자의 상태를 유지 하지 않습니다. 즉, 여러 토폴로지 인스턴스가 동일한 유추 서버를 호출할 수 있지만 서버는 호출자 당 또는 상태를 호출 하는 사용자를 구분할 수 없습니다. 

*HTTP 프로토콜을 사용 합니다*.

상태, 각 호출자 또는 그래프 토폴로지 인스턴스를 유지 하기 위해는 호출자에 게 고유한 HTTP 쿼리 매개 변수를 사용 하 여 추론 서버를 호출 합니다. 예를 들어 각 인스턴스의 유추 서버 URL 주소가 다음과 같이 표시 됩니다.  

첫 번째 토폴로지 인스턴스 = `http://lvaExtension:44000/score?id=1`<br/>
두 번째 토폴로지 인스턴스 = `http://lvaExtension:44000/score?id=2`

… 

서버 쪽에서 점수 경로는를 호출 하는 사람을 인식 합니다. ID = 1 인 경우 해당 호출자 또는 그래프 토폴로지 인스턴스에 대해 별도로 상태를 유지할 수 있습니다. 그런 다음 받은 비디오 프레임을 버퍼에 유지할 수 있습니다. 예를 들어 배열 또는 DateTime 키가 있는 사전을 사용 하 고 값은 프레임입니다. 그런 다음 *x* 개의 프레임을 받은 후에 처리할 서버를 정의할 수 있습니다 (유추). 

*GRPC 프로토콜을 사용 합니다*. 

GRPC 확장을 사용 하는 경우 각 세션은 단일 카메라 피드에 대 한 것 이므로 ID를 제공할 필요가 없습니다. 이제 extensionConfiguration 속성을 사용 하 여 버퍼에 비디오 프레임을 저장 하 고 *x* 개의 프레임을 받은 후 처리할 서버를 정의할 수 있습니다 (유추). 

**특정 컨테이너의 모든 ProcessMediaStreams이 동일한 AI 모델을 실행 하나요?** 

아니요. 그래프 인스턴스의 최종 사용자에 대 한 시작 또는 중지 호출은 세션을 구성 하거나 카메라 연결 끊기 또는 다시 연결 하는 것일 수도 있습니다. 카메라에서 비디오를 스트리밍하는 경우 한 세션을 유지 하는 것이 목표입니다. 

* 처리를 위해 비디오를 보내는 두 카메라는 두 개의 세션을 만듭니다. 
* 두 gRPC 확장 노드가 있는 그래프로 이동 하는 한 카메라는 두 개의 세션을 만듭니다. 

각 세션은 라이브 비디오 분석과 gRPC 서버 간의 전이중 연결 이며 각 세션은 다른 모델 또는 파이프라인을 가질 수 있습니다. 

> [!NOTE]
> 카메라 연결을 끊거나 다시 연결 하는 경우 허용 오차 제한을 초과 하는 기간 동안 카메라가 오프 라인으로 전환 되 면 Live Video Analytics에서 gRPC 서버를 사용 하 여 새 세션을 엽니다. 서버에서 이러한 세션의 상태를 추적 하는 요구 사항은 없습니다. 

또한 Live Video Analytics는 그래프 인스턴스의 단일 카메라에 대 한 여러 gRPC 확장에 대 한 지원을 추가 합니다. 이러한 gRPC 확장을 사용 하 여 AI 처리를 순차적으로 또는 양쪽의 조합으로 수행할 수 있습니다. 

> [!NOTE]
> 여러 확장을 병렬로 실행 하면 하드웨어 리소스에 영향을 줍니다. 컴퓨팅 요구에 맞는 하드웨어를 선택 하는 경우이 점을 염두에 두어야 합니다. 

**동시 ProcessMediaStreams의 최대 수는 얼마 인가요?** 

라이브 비디오 분석은이 숫자에 제한을 적용 하지 않습니다.  

**추론 서버에서 CPU 또는 GPU를 사용 해야 하는지 아니면 다른 하드웨어 가속기를 사용 해야 하는지 결정 하려면 어떻게 해야 하나요?** 

결정은 개발 된 AI 모델의 복잡성 및 CPU와 하드웨어 가속기를 사용 하는 방법에 따라 달라 집니다. AI 모델을 개발 하는 동안 모델에서 사용할 리소스 및 수행할 작업을 지정할 수 있습니다. 

**경계 상자 사후 처리를 사용 하 여 이미지를 저장 어떻게 할까요??** 

현재는 경계 상자 좌표를 유추 메시지로만 제공 합니다. 이러한 메시지를 사용 하 고 비디오 프레임에서 경계 상자를 오버레이 하는 사용자 지정 MJPEG 스 트리머을 빌드할 수 있습니다.  

## <a name="grpc-compatibility"></a>gRPC 호환성 

**미디어 스트림 설명자에 대 한 필수 필드는 어떻게 알 수 있나요?** 

값을 제공 하지 않는 모든 필드에는 [gRPC에 지정 된 대로 기본값이](https://developers.google.com/protocol-buffers/docs/proto3#default)지정 됩니다.  

Live Video Analytics는 *proto3* 버전의 프로토콜 버퍼 언어를 사용 합니다. 라이브 비디오 분석 계약에 사용 되는 모든 프로토콜 버퍼 데이터는 [프로토콜 버퍼 파일](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)에서 사용할 수 있습니다. 

**최신 프로토콜 버퍼 파일을 사용 하 고 있는지 확인 하려면 어떻게 해야 하나요?** 

[계약 파일 사이트](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)에서 최신 프로토콜 버퍼 파일을 가져올 수 있습니다. 계약 파일을 업데이트할 때마다이 위치에 배치 됩니다. 프로토콜 파일을 업데이트 하기 위한 즉각적인 계획은 없으므로 파일 맨 위에 있는 패키지 이름을 확인 하 여 버전을 확인 합니다. 이 단계는 다음과 같아야 합니다. 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

이러한 파일을 업데이트 하면 이름 끝에 "v-값"이 증가 합니다. 

> [!NOTE]
> 라이브 비디오 분석은 언어의 proto3 버전을 사용 하기 때문에 필드는 선택 사항이 며 버전은 뒤로 및 앞으로 호환 됩니다. 

**Live Video Analytics에서 사용할 수 있는 gRPC 기능 필수 및 선택적 기능은 무엇 인가요?** 

Protobuf (프로토콜 버퍼) 계약이 충족 된 경우 서버 쪽 gRPC 기능을 사용할 수 있습니다. 

## <a name="monitoring-and-metrics"></a>모니터링 및 메트릭

**Azure Event Grid를 사용 하 여 가장자리의 미디어 그래프를 모니터링할 수 있나요?**

예. 프로메테우스 메트릭을 사용 하 여 event grid에 게시할 수 있습니다. 

**Azure Monitor를 사용 하 여 클라우드 또는에 지에서 내 미디어 그래프의 상태, 메트릭 및 성능을 볼 수 있나요?**

예,이 방법을 지원 합니다. 자세히 알아보려면 [Azure Monitor 메트릭 개요](../../azure-monitor/essentials/data-platform-metrics.md)를 참조 하세요.

**Media Services IoT Edge 모듈을 보다 쉽게 모니터링할 수 있는 도구가 있나요?**

Visual Studio Code는 LVAEdge 모듈 끝점을 쉽게 모니터링할 수 있는 Azure IoT Tools 확장을 지원 합니다. 이 도구를 사용 하 여 "이벤트"에 대 한 IoT hub 기본 제공 끝점 모니터링을 신속 하 게 시작 하 고에 지 장치에서 클라우드로 라우팅되는 유추 메시지를 볼 수 있습니다. 

또한이 확장을 사용 하 여 LVAEdge 모듈에 대 한 모듈 쌍을 편집 하 여 미디어 그래프 설정을 수정할 수 있습니다.

자세한 내용은 [모니터링 및 로깅](monitoring-logging.md) 문서를 참조 하세요.

## <a name="billing-and-availability"></a>요금 청구 및 가용성

**IoT Edge에 대 한 Live Video Analytics는 어떻게 청구 되나요?**

청구 세부 정보는 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[빠른 시작: IoT Edge에서 라이브 비디오 분석 시작](get-started-detect-motion-emit-events-quickstart.md)

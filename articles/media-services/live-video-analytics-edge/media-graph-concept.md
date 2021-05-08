---
title: 미디어 그래프 개념 - Azure
description: 미디어 그래프에서 미디어를 캡처할 위치, 처리하는 방법 및 결과를 전달해야 하는 위치를 정의할 수 있습니다. 이 문서에서는 미디어 그래프 개념에 대한 자세한 설명을 제공합니다.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: ad23acbbbdd0c15e92e471ee22a229470a8a3a75
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557669"
---
# <a name="media-graph"></a>미디어 그래프

## <a name="suggested-pre-reading"></a>추천 참고 자료

* [Live Video Analytics on IoT Edge 개요](overview.md)
* [Live Video Analytics on IoT Edge 용어](terminology.md)

## <a name="overview"></a>개요

미디어 그래프에서 미디어를 캡처할 위치, 처리하는 방법 및 결과를 전달해야 하는 위치를 정의할 수 있습니다. 원하는 방식으로 구성 요소 또는 노드를 연결하여 이를 수행합니다. 아래 다이어그램은 해당 미디어 그래프의 그래픽 표현을 보여 줍니다.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/media-graph.svg" alt-text="미디어 그래프":::

IoT Edge의 Live Video Analytics는 다양한 유형의 원본, 프로세서 및 싱크를 지원합니다.

* **원본 노드** 에서 미디어 그래프로 미디어를 캡처할 수 있습니다. 이 컨텍스트의 미디어는 개념적으로 오디오 스트림, 비디오 스트림, 데이터 스트림 또는 단일 스트림과 함께 결합된 오디오, 비디오 및/또는 데이터가 있는 스트림이 될 수 있습니다.
* **프로세서 노드** 는 미디어 그래프 내의 미디어를 처리할 수 있도록 합니다.
* **싱크 노드** 는 미디어 그래프 외부의 서비스 및 앱에 처리 결과를 제공할 수 있도록 합니다.

## <a name="media-graph-topologies-and-instances"></a>미디어 그래프 토폴로지 및 인스턴스 

IoT Edge의 Live Video Analytics를 사용하면 "그래프 토폴로지" 및 "그래프 인스턴스"라는 두 가지 개념을 통해 미디어 그래프를 관리할 수 있습니다. 그래프 토폴로지를 사용하면 매개 변수를 값의 자리 표시자로 사용하여 그래프의 청사진을 정의할 수 있습니다. 토폴로지는 미디어 그래프에서 사용되는 노드 및 미디어 그래프 내에서 연결되는 방법을 정의합니다. 예를 들어, 카메라에서 피드를 기록하려는 경우 비디오를 수신하는 원본 노드와 비디오를 쓰는 싱크 노드가 있는 그래프가 필요합니다.

토폴로지의 매개 변수 값은 토폴로지를 참조하는 그래프 인스턴스를 만들 때 지정됩니다. 이를 통해 토폴로지에 지정된 매개 변수에 대해 서로 다른 값을 사용하여 동일한 토폴로지를 참조하는 여러 인스턴스를 만들 수 있습니다. 위의 예제에서 매개 변수를 사용하여 카메라의 IP 주소와 녹화된 비디오의 이름을 표시할 수 있습니다. 해당 토폴로지를 사용하여 여러 그래프 인스턴스를 만들 수 있습니다. 즉, 건물의 각 카메라에 대해 특정 IP 주소 및 특정 이름을 사용하여 하나의 인스턴스를 만들 수 있습니다.

## <a name="media-graph-states"></a>미디어 그래프 상태  

그래프 토폴로지 및 그래프 인스턴스의 수명 주기는 다음 상태 다이어그램에 표시됩니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/graph-topology-lifecycle.svg" alt-text="그래프 토폴로지 및 그래프 인스턴스 수명 주기":::

[그래프 토폴로지를 만드는](direct-methods.md#graphtopologyset)것으로 시작합니다. 그런 다음, 이 토폴로지에서 처리하려는 각 라이브 비디오 피드에 대해 [그래프 인스턴스를 만듭니다](direct-methods.md#graphinstanceset). 

그래프 인스턴스는 `Inactive`(유휴) 상태가 됩니다.

라이브 비디오 피드를 그래프 인스턴스로 보낼 준비가 되면 [활성화](direct-methods.md#graphinstanceactivate)합니다. 그래프 인스턴스는 `Activating` 상태를 잠깐 전환되었다가 성공하면 `Active` 상태로 전환됩니다. `Active` 상태에서 미디어가 처리됩니다(그래프 인스턴스가 입력 데이터를 수신하는 경우).

> [!NOTE]
>  그래프 인스턴스는 데이터를 이동하지 않는 상태에서도 활성 상태일 수 있습니다. 예: 카메라 오프라인 상태.
> 그래프 인스턴스가 활성 상태일 때 Azure 구독에 대한 요금이 청구됩니다.

다른 라이브 비디오 피드를 처리하는 경우, 동일한 토폴로지에 대해 다른 그래프 인스턴스를 만들고 활성화하는 프로세스를 반복할 수 있습니다.

라이브 비디오 피드의 처리가 완료되면 그래프 인스턴스를 [비활성화](direct-methods.md#graphinstancedeactivate)할 수 있습니다. 그래프 인스턴스는 `Deactivating` 상태로 잠깐 전환되었다가 포함된 모든 데이터를 플러시한 다음 `Inactive` 상태로 돌아갑니다.

`Inactive` 상태일 때만 그래프 인스턴스를 [삭제](direct-methods.md#graphinstancedelete)할 수 있습니다.

특정 그래프 토폴로지를 참조하는 모든 그래프 인스턴스가 삭제된 후에는 [그래프 토폴로지를 삭제](direct-methods.md#graphtopologydelete)할 수 있습니다.


## <a name="sources-processors-and-sinks"></a>원본, 프로세서 및 싱크  

IoT Edge의 Live Video Analytics는 미디어 그래프 내에서 다음과 같은 유형의 노드를 지원합니다.

### <a name="sources"></a>원본 

#### <a name="rtsp-source"></a>RTSP 원본 

RTSP 원본 노드를 사용하면 [RTSP](https://tools.ietf.org/html/rfc2326) 서버에서 미디어를 수집할 수 있습니다. 감시 및 IP 기반 카메라는 휴대폰 및 비디오 카메라와 같은 다른 종류의 디바이스와 다른 RTSP(실시간 스트리밍 프로토콜)라는 프로토콜에서 데이터를 전송합니다. 이 프로토콜은 서버(카메라)와 클라이언트 간의 미디어 세션을 설정하고 제어하는 데 사용됩니다. 미디어 그래프의 RTSP 원본 노드는 클라이언트 역할을 하며 RTSP 서버를 사용하여 세션을 설정할 수 있습니다. 대부분의 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)와 같은 디바이스에는 기본 제공 RTSP 서버가 있습니다. [ONVIF](https://www.onvif.org/)는 [프로필 G, S, T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) 규격 디바이스의 정의에서 RTSP를 지원하도록 요구합니다. RTSP 원본 노드에서는 인증된 연결을 사용하도록 설정하는 자격 증명과 함께 RTSP URL을 지정해야 합니다.

#### <a name="iot-hub-message-source"></a>IoT Hub 메시지 원본 

다른 [IoT Edge 모듈과](../../iot-fundamentals/iot-glossary.md#iot-edge) 마찬가지로 IoT Edge 모듈의 Live Video Analytics는 [IoT Edge 허브](../../iot-fundamentals/iot-glossary.md#iot-edge-hub)를 통해 메시지를 받을 수 있습니다. 이러한 메시지는 Edge 디바이스나 클라우드에서 실행되는 다른 모듈이나 앱에서 보낼 수 있습니다. 이러한 메시지는 모듈의 [명명된 입력](../../iot-edge/module-composition.md#sink)으로 전달(라우팅)됩니다. IoT Hub 메시지 원본 노드에서는 이러한 메시지가 미디어 그래프에 도달할 수 있습니다. 이러한 메시지 또는 신호는 미디어 그래프에서 내부적으로 사용되며 일반적으로 신호 게이트를 활성화는 데 사용할 수 있습니다(아래 [신호 게이트](#signal-gate-processor) 참조). 

예를 들어, 문이 열릴 때 메시지를 생성하는 IoT Edge 모듈을 사용할 수 있습니다. 해당 모듈의 메시지를 IoT Edge 허브로 라우팅할 수 있습니다. 그런 다음 미디어 그래프의 IoT 허브 메시지 원본으로 라우팅할 수 있습니다. 미디어 그래프 내에서 IoT 허브 메시지 원본은 신호 게이트 프로세서에 이벤트를 전달할 수 있습니다. 그러면 RTSP 원본에서 파일로 비디오 녹화를 켤 수 있습니다. 

### <a name="processors"></a>프로세서  

#### <a name="motion-detection-processor"></a>동작 감지 프로세서 

동작 감지 프로세서 노드를 사용하면 라이브 비디오에서 동작을 감지할 수 있습니다. 수신되는 비디오 프레임을 검사하고 비디오에서 이동이 있는지 확인합니다. 동작이 감지되면 비디오 프레임을 다운스트림 구성 요소로 전달하고 이벤트를 내보냅니다. 동작 감지 프로세서 노드(다른 노드와 함께)를 사용하여 동작이 감지되면 수신되는 비디오의 녹화를 트리거할 수 있습니다.

#### <a name="frame-rate-filter-processor"></a>프레임 속도 필터 프로세서  

프레임 속도 필터 프로세서 노드를 사용하면 지정된 속도에서 들어오는 비디오 스트림의 프레임을 샘플링할 수 있습니다. 그러면 추가 처리를 위해 다운스트림 구성 요소(예: HTTP 확장 프로세서 노드)로 전송되는 프레임 수를 줄일 수 있습니다.
>[!WARNING]
> 이 프로세서는 IoT Edge 모듈의 Live Video Analytics 최신 릴리스에서 **사용되지 않습니다**. 이제 그래프 확장 프로세서 자체 내에서 프레임 속도 관리가 지원됩니다.

#### <a name="http-extension-processor"></a>HTTP 확장 프로세서

HTTP 확장 프로세서 노드를 사용하면 고유한 IoT Edge 모듈을 미디어 그래프에 연결할 수 있습니다. 이 노드는 디코딩된 비디오 프레임을 입력으로 가져오고, 이러한 프레임을 모듈에서 노출하는 HTTP REST 엔드포인트로 릴레이합니다. 이 노드에는 필요한 경우 REST 엔드포인트를 사용하여 인증하는 기능이 있습니다. 또한 이 노드에는 비디오 프레임을 REST 엔드포인트로 릴레이하기 전에 스케일링하고 인코딩하기 위한 기본 제공 이미지 포맷터가 포함되어 있습니다. 스케일러는 이미지 가로 세로 비율을 유지하거나 패딩하거나 늘일 수 있는 옵션을 제공합니다. 이미지 인코더는 JPEG, PNG 또는 BMP 형식을 지원합니다. [여기](media-graph-extension-concept.md#http-extension-processor)서 프로세서에 대해 자세히 알아보세요.

#### <a name="grpc-extension-processor"></a>gRPC 확장 프로세서

gRPC 확장 프로세서 노드는 디코딩된 비디오 프레임을 입력으로 가져오고, 이러한 프레임을 모듈에서 노출하는 [gRPC](terminology.md#grpc) 엔드포인트로 릴레이합니다. 이 노드는 [공유 메모리](https://en.wikipedia.org/wiki/Shared_memory)를 사용하여 데이터를 전송하거나 콘텐츠를 gRPC 메시지의 본문에 직접 포함할 수 있도록 지원합니다. 또한 이 노드에는 비디오 프레임을 gRPC 엔드포인트로 릴레이하기 전에 스케일링하고 인코딩하기 위한 기본 제공 이미지 포맷터가 포함되어 있습니다. 스케일러는 이미지 가로 세로 비율을 유지하거나 패딩하거나 늘일 수 있는 옵션을 제공합니다. 이미지 인코더는 jpeg, png 또는 bmp 형식을 지원합니다. [여기](media-graph-extension-concept.md#grpc-extension-processor)서 프로세서에 대해 자세히 알아보세요.

#### <a name="signal-gate-processor"></a>신호 게이트 프로세서  

신호 게이트 프로세서 노드를 사용하면 한 노드에서 다른 노드로 미디어를 조건부로 전달할 수 있습니다. 또한 미디어와 이벤트를 동기화 할 수 있도록 하는 버퍼 역할을 합니다. 일반적인 사용 사례는 RTSP 원본 노드와 자산 싱크 노드 사이에 신호 게이트 프로세서 노드를 삽입하고 동작 감지기 프로세서 노드의 출력을 사용하여 게이트를 트리거하는 것입니다. 이러한 미디어 그래프를 사용하면 동작이 감지된 경우에만 비디오를 녹화할 수 있습니다.

### <a name="sinks"></a>Sinks  

#### <a name="asset-sink"></a>자산 싱크  

자산 싱크 노드를 사용하면 미디어(비디오 및/또는 오디오) 데이터를 Azure Media Services 자산에 쓸 수 있습니다. 미디어 그래프에는 자산 싱크 노드가 하나만 있을 수 있습니다. [자산](terminology.md#asset) 섹션에서 자산에 대한 자세한 정보와 녹화의 역할 및 미디어 재생을 확인하세요. 또한 이 노드의 속성을 사용하는 방법에 대한 자세한 내용은 [연속 비디오 녹화](continuous-video-recording-concept.md) 문서를 참조하세요.

#### <a name="file-sink"></a>파일 싱크  

파일 싱크 노드를 사용하면 미디어(비디오 및/또는 오디오) 데이터를 IoT Edge 디바이스의 로컬 파일 시스템 위치에 쓸 수 있습니다. 미디어 그래프에는 파일 싱크 노드가 하나만 있을 수 있으며 신호 게이트 프로세서 노드의 다운스트림이어야 합니다. 그러면 출력 파일의 지속 시간이 신호 게이트 프로세서 노드 속성에 지정된 값으로 제한됩니다. 에지 디바이스의 디스크 공간이 부족하지 않도록 하려면 IoT Edge 모듈의 Live Video Analytics에서 데이터를 저장하는 데 사용할 수 있는 최대 크기를 설정할 수도 있습니다.  
> [!NOTE]
파일 싱크가 가득 차면 IoT Edge 모듈의 Live Video Analytics는 가장 오래된 데이터를 삭제하고 새 데이터로 대체합니다.
#### <a name="iot-hub-message-sink"></a>IoT Hub 메시지 싱크  

IoT Hub 메시지 싱크 노드를 사용하여 IoT Edge 허브에 이벤트를 게시할 수 있습니다. 그런 다음 IoT Edge 허브는 데이터를 에지 디바이스의 다른 모듈이나 앱에 라우팅하거나 배포 매니페스트에 지정된 경로별로 클라우드의 IoT Hub에 라우팅할 수 있습니다. IoT Hub 메시지 싱크 노드는 동작 감지 프로세서 노드와 같은 업스트림 프로세서 또는 HTTP 확장 프로세서 노드를 통해 외부 유추 서비스에서 발생한 이벤트를 허용할 수 있습니다.

## <a name="rules-on-the-use-of-nodes"></a>노드 사용 규칙

미디어 그래프 내에서 다양한 노드를 사용할 수 있는 방법에 대한 추가 규칙은 [그래프 토폴로지의 제한 사항](quotas-limitations.md#limitations-on-graph-topologies-at-preview)을 참조하세요.

## <a name="scenarios"></a>시나리오

위에서 정의한 원본, 프로세서 및 싱크 조합을 사용하여 라이브 비디오 분석과 관련된 다양한 시나리오에 대한 미디어 그래프를 작성할 수 있습니다. 예제 시나리오는 다음과 같습니다.

* [연속 비디오 녹화](continuous-video-recording-concept.md)
* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)
* [비디오 녹화가 없는 Live Video Analytics](analyze-live-video-concept.md)

## <a name="next-steps"></a>다음 단계

[빠른 시작: 사용자 고유의 모델로 Live Video Analytics 실행](use-your-model-quickstart.md) 문서를 참조하여 라이브 비디오 피드에서 동작 감지를 실행하는 방법을 확인할 수 있습니다.

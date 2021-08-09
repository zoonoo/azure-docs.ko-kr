---
title: Azure Video Analyzer 파이프라인
description: Azure Video Analyzer 파이프라인을 사용하여 입력 데이터를 캡처할 위치, 처리 방법 및 결과를 전달해야 하는 위치를 정의할 수 있습니다. 파이프라인은 원하는 데이터 흐름을 얻기 위해 연결된 노드로 구성됩니다.
ms.topic: conceptual
ms.date: 05/13/2021
ms.openlocfilehash: deed7eec980272dd3dd001219016eee139f22ad6
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111591245"
---
# <a name="pipeline"></a>파이프라인

Azure Video Analyzer 파이프라인을 사용하여 입력 데이터를 캡처할 위치, 처리 방법 및 결과를 전달해야 하는 위치를 정의할 수 있습니다. 파이프라인은 원하는 데이터 흐름을 얻기 위해 연결된 노드로 구성됩니다. 아래 다이어그램은 파이프라인을 그림으로 표현한 것입니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline/pipeline-representation.svg" alt-text="파이프라인의 표현":::

파이프라인은 여러 유형의 노드를 지원합니다.

* **원본 노드** 는 데이터를 파이프라인으로 캡처할 수 있습니다. 데이터는 오디오, 비디오 및/또는 메타데이터를 참조합니다.
* **프로세서 노드** 는 파이프라인 내의 미디어를 처리할 수 있습니다.
* **싱크 노드** 는 파이프라인 외부의 서비스 및 앱에 결과를 제공할 수 있습니다.

## <a name="suggested-pre-reading"></a>추천 참고 자료

* [개요](overview.md)
* [용어](terminology.md)

## <a name="pipeline-topologies"></a>파이프라인 토폴로지

파이프라인 토폴로지를 사용하면 상호 연결된 노드 세트를 통해 사용자 지정 요구 사항에 맞게 라이브 비디오를 처리하고 분석하는 방법을 설명할 수 있습니다. 토폴로지의 노드 종류, 노드를 연결하는 방법을 선택하고 매개 변수를 값의 자리 표시자로 사용하여 다양한 시나리오에 대한 여러 토폴로지를 만들 수 있습니다. 파이프라인은 특정 파이프라인 토폴로지의 개별 인스턴스입니다. 파이프라인은 미디어가 실제로 처리되는 위치입니다. 파이프라인은 파이프라인 토폴로지에 선언된 사용자 정의 매개 변수를 통해 개별 카메라(및 기타 측면)와 연결할 수 있습니다.

예를 들어 여러 IP 카메라에서 비디오를 녹화하려면 RTSP 원본 노드와 비디오 싱크 노드로 구성된 파이프라인 토폴로지를 정의하면 됩니다. RTSP 원본 노드는 RTSP URL, 사용자 이름 및 암호를 매개 변수로 사용할 수 있습니다. 비디오 싱크 노드는 비디오 이름을 매개 변수로 사용할 수 있습니다. 동일한 토폴로지에서 여러 파이프라인을 만들 때(카메라마다 파이프라인 하나씩) 이러한 매개 변수의 값을 제공할 수 있습니다.

## <a name="pipeline-states"></a>파이프라인 상태

아래 다이어그램은 파이프라인의 수명 주기입니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline/pipeline-activation.svg" alt-text="파이프라인의 수명 주기":::

파이프라인 토폴로지 만들기부터 시작합니다. 토폴로지가 정의되면 매개 변수 값을 제공하여 파이프라인을 만들 수 있습니다. 만들기가 완료되면 파이프라인은 "비활성" 상태입니다. 파이프라인을 활성화하면 파이프라인이 "활성화 중" 상태를 거쳐 "활성" 상태가 됩니다. 

파이프라인이 "활성" 상태가 되면 데이터(라이브 비디오)가 파이프라인을 통해 흐르기 시작합니다. 파이프라인을 비활성화하면 활성 파이프라인이 "비활성화 중" 상태를 거쳐 "비활성" 상태가 됩니다. 비활성 파이프라인만 삭제할 수 있습니다.

토폴로지의 매개 변수에 여러 값을 제공하여 단일 토폴로지에서 여러 파이프라인을 만들 수 있습니다. 모든 파이프라인을 삭제한 후 토폴로지를 삭제할 수 있습니다.

> [!NOTE]
> 파이프라인은 데이터가 흐르지 않아도(예: 입력 비디오 원본이 오프라인 상태) 활성 상태일 수 있습니다. 파이프라인이 활성 상태이면 Azure 구독 요금이 청구됩니다.

## <a name="sources-processors-and-sinks"></a>원본, 프로세서 및 싱크

Video Analyzer 에지 모듈은 파이프라인 내에서 다음과 같은 유형의 노드를 지원합니다.

### <a name="sources"></a>원본

#### <a name="rtsp-source"></a>RTSP 원본

RTSP 원본 노드는 RTSP 서버에서 미디어를 캡처할 수 있습니다. RTSP 프로토콜은 서버(예: IP 카메라)와 클라이언트 간의 미디어 세션 설정 및 제어를 정의합니다. 파이프라인의 [RTSP](https://tools.ietf.org/html/rfc2326) 원본 노드는 클라이언트 역할을 하며 RTSP 서버와의 세션을 설정할 수 있습니다. 대부분의 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)와 같은 디바이스에는 기본 제공 RTSP 서버가 있습니다. [ONVIF](https://www.onvif.org/)는 [프로필 G, S, T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) 규격 디바이스의 정의에서 RTSP를 지원하도록 요구합니다. RTSP 원본 노드에서는 인증된 연결을 사용하도록 설정하는 자격 증명과 함께 RTSP URL을 지정해야 합니다.

#### <a name="iot-hub-message-source"></a>IoT Hub 메시지 원본

다른 [IoT Edge 모듈](../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-device)과 마찬가지로 Azure Video Analyzer 모듈은 [IoT Edge 허브](../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub)를 통해 메시지를 받을 수 있습니다. 메시지는 Edge 디바이스나 클라우드에서 실행되는 다른 모듈이나 앱에서 보낼 수 있습니다. 이러한 메시지는 비디오 분석기 모듈의 [명명된 입력](../../iot-edge/module-composition.md?view=iotedge-2020-11&preserve-view=true#sink)으로 전달(라우팅)할 수 있습니다. IoT Hub 메시지 원본 노드는 이러한 메시지를 파이프라인으로 수집할 수 있습니다. 그런 다음, 파이프라인에서 메시지를 사용하여 신호 게이트를 활성화할 수 있습니다(아래의 [신호 게이트](#signal-gate-processor) 참조).

예를 들어 문이 열릴 때 메시지를 생성하는 IoT Edge 모듈을 사용할 수 있습니다. 해당 모듈의 메시지를 IoT Edge 허브로 라우팅한 다음, 파이프라인의 IoT 허브 메시지 원본으로 라우팅할 수 있습니다. 파이프라인 내에서 IoT 허브 메시지 원본의 메시지를 신호 게이트 프로세서에 전달할 수 있습니다. 그러면 RTSP 원본에서 파일로 비디오 녹화를 켤 수 있습니다.

### <a name="processors"></a>프로세서

#### <a name="motion-detection-processor"></a>동작 감지 프로세서

동작 감지 프로세서 노드를 사용하면 라이브 비디오에서 동작을 감지할 수 있습니다. 수신되는 비디오 프레임을 검사하고 비디오에서 이동이 있는지 확인합니다. 동작이 감지되면 비디오 프레임을 파이프라인의 다음 노드에 전달하고 이벤트를 내보냅니다. 동작 감지 프로세서 노드(다른 노드와 함께)를 사용하여 동작이 감지되면 수신되는 비디오의 녹화를 트리거할 수 있습니다.

#### <a name="http-extension-processor"></a>HTTP 확장 프로세서

HTTP 확장 프로세서 노드는 파이프라인을 자체 IoT Edge 모듈로 확장할 수 있습니다. 이 노드는 디코딩된 비디오 프레임을 입력으로 사용하며, 이러한 프레임을 모듈에서 노출하는 HTTP REST 엔드포인트에 릴레이합니다. 여기서 AI 모델을 사용하여 프레임을 분석하고 유추 결과를 반환할 수 있습니다. 또한 이 노드에는 비디오 프레임을 HTTP 엔드포인트로 릴레이하기 전에 스케일링하고 인코딩하기 위한 기본 제공 이미지 포맷터가 포함되어 있습니다. 스케일러는 이미지 가로 세로 비율을 유지하거나 패딩하거나 늘일 수 있는 옵션을 제공합니다. 이미지 인코더는 JPEG, PNG, BMP 및 RAW 형식을 지원합니다. [여기서 프로세서](pipeline-extension.md#http-extension-processor)에 대해 자세히 알아보세요.

#### <a name="grpc-extension-processor"></a>gRPC 확장 프로세서

gRPC 확장 프로세서 노드는 디코딩된 비디오 프레임을 입력으로 가져오고, 이러한 프레임을 모듈에서 노출하는 [gRPC](terminology.md#grpc) 엔드포인트로 릴레이합니다. 이 노드는 [공유 메모리](https://en.wikipedia.org/wiki/Shared_memory)를 사용하여 데이터를 전송하거나 프레임을 gRPC 메시지의 본문에 직접 포함할 수 있도록 지원합니다. HTTP 확장 프로세스와 마찬가지로 이 노드에도 비디오 프레임을 gRPC 엔드포인트로 릴레이하기 전에 스케일링하고 인코딩하기 위한 기본 제공 이미지 포맷터가 포함되어 있습니다. [여기서 프로세서](pipeline-extension.md#grpc-extension-processor)에 대해 자세히 알아보세요.

#### <a name="cognitive-services-extension-processor"></a>Cognitive Services 확장 프로세서

Cognitive Services 확장 프로세서 노드는 파이프라인을 [공간 분석](https://azure.microsoft.com/services/cognitive-services/computer-vision/) IoT Edge 모듈로 확장할 수 있습니다. 이 노드는 디코딩된 비디오 프레임을 입력으로 사용하며, 이러한 프레임을 모듈에서 노출하는 [gRPC](pipeline-extension.md#grpc-extension-processor) 엔드포인트에 릴레이합니다. 여기서 공간 분석 기술을 사용하여 프레임을 분석하고 유추 결과를 반환할 수 있습니다. [여기서 프로세서](pipeline-extension.md#cognitive-services-extension-processor)에 대해 자세히 알아보세요.

#### <a name="signal-gate-processor"></a>신호 게이트 프로세서

신호 게이트 프로세서 노드를 사용하면 한 노드에서 다른 노드로 미디어를 조건부로 전달할 수 있습니다. 신호 게이트 프로세서 노드 바로 뒤에 비디오 싱크 또는 파일 싱크가 있어야 합니다. 예제 사용 사례는 RTSP 원본 노드와 비디오 싱크 노드 사이에 신호 게이트 프로세서 노드를 삽입하고 동작 감지기 프로세서 노드의 출력을 사용하여 게이트를 트리거하는 것입니다. 이러한 파이프라인을 사용하면 동작이 감지된 경우에만 비디오를 녹화할 수 있습니다. 또한 동작 감지 프로세서 노드 대신 HTTP 또는 gRPC 확장 노드의 출력을 사용하여 게이트를 트리거할 수 있으므로 흥미로운 항목이 감지될 때 비디오를 녹화할 수 있습니다.

#### <a name="object-tracker-processor"></a>개체 추적기 프로세서

개체 추적기 프로세서 노드는 업스트림 HTTP 또는 gRPC 확장 프로세서 노드에서 감지된 개체를 추적할 수 있습니다. 이 노드는 모든 프레임의 개체를 감지해야 하는 경우에 유용하지만, 에지 디바이스는 모든 프레임에 AI 모델을 적용하는 데 필요한 컴퓨팅 성능을 갖고 있지 않습니다. 컴퓨터 비전 모델을 10번째 프레임마다 실행할 수 있는 경우 개체 추적기는 이러한 프레임의 결과를 가져온 다음, [광학 흐름](https://en.wikipedia.org/wiki/Optical_flow) 기술을 사용하여 모델이 다음 프레임에서 다시 적용될 때까지 2번째, 3번째,..., 9번째 프레임에 대한 결과를 생성할 수 있습니다. 이 노드를 사용하는 경우 컴퓨팅 성능과 정확도 사이에 득실이 있습니다. AI 모델이 적용되는 프레임이 가까울수록 정확도가 향상됩니다. 그러나 AI 모델을 더 자주 적용하므로 더 높은 컴퓨팅 성능이 필요하다는 의미입니다. 개체 추적기 프로세서 노드의 일반적인 용도는 개체가 선을 통과할 때 감지하는 것입니다.

#### <a name="line-crossing-processor"></a>선 통과 프로세서

선 통과 프로세서 노드는 개체가 사용자 정의 선을 통과하는 것을 감지할 수 있습니다. 뿐만 아니라 선을 통과하는 개체의 수를 유지합니다(파이프라인이 활성화되는 시간부터). 이 노드는 개체 추적 장치 프로세서 노드의 다운스트림에 사용해야 합니다.

### <a name="sinks"></a>Sinks

#### <a name="video-sink"></a>비디오 싱크

비디오 싱크 노드를 사용하면 비디오 및 관련 메타데이터를 Video Analyzer 클라우드 리소스에 저장할 수 있습니다. 이벤트에 따라 비디오를 연속적으로 또는 불연속적으로 녹화할 수 있습니다. 비디오 싱크 노드는 클라우드 연결이 끊어지면 에지 디바이스의 비디오를 캐시하고, 연결이 복원되면 업로드를 다시 시작할 수 있습니다. 또한 이 노드의 속성을 구성하는 방법에 대한 자세한 내용은 [연속 비디오 녹화](continuous-video-recording.md) 문서를 참조하세요.

#### <a name="file-sink"></a>파일 싱크

파일 싱크 노드를 사용하면 비디오를 에지 디바이스의 로컬 파일 시스템 위치에 쓸 수 있습니다. 파이프라인에는 파일 싱크 노드가 하나만 있어야 하며 신호 게이트 프로세서 노드의 다운스트림이어야 합니다. 그러면 출력 파일의 지속 시간이 신호 게이트 프로세서 노드 속성에 지정된 값으로 제한됩니다. 에지 디바이스의 디스크 공간이 부족하지 않도록 Video Analyzer 에지 모듈이 데이터를 캐시하는 데 사용할 수 있는 최대 크기를 설정할 수도 있습니다.

> [!NOTE]
> 캐시가 가득 차면 Video Analyzer 에지 모듈은 가장 오래된 데이터를 삭제하고 새 데이터로 바꿉니다.

#### <a name="iot-hub-message-sink"></a>IoT Hub 메시지 싱크

IoT Hub 메시지 싱크 노드를 사용하여 IoT Edge 허브에 이벤트를 게시할 수 있습니다. 데이터를 에지 디바이스의 다른 모듈 또는 앱에 라우팅하거나 배포 매니페스트에 지정된 경로별로 클라우드의 IoT Hub에 라우팅하도록 IoT Edge 허브를 구성할 수 있습니다. IoT Hub 메시지 싱크 노드는 동작 감지 프로세서 노드와 같은 업스트림 프로세서 노드 또는 HTTP 확장 프로세서 노드를 통해 외부 유추 서비스에서 발생한 이벤트를 허용할 수 있습니다.

## <a name="rules-on-the-use-of-nodes"></a>노드 사용 규칙

파이프라인 내에서 다양한 노드를 사용하는 방법에 대한 추가 규칙은 [파이프라인의 제한 사항](quotas-limitations.md#limitations-on-pipeline-topologies)을 참조하세요.

## <a name="scenarios"></a>시나리오

위에서 정의한 원본, 프로세서 및 싱크 조합을 사용하여 라이브 비디오 분석과 관련된 다양한 시나리오에 대한 파이프라인을 작성할 수 있습니다. 예제 시나리오는 다음과 같습니다.

* [연속 비디오 녹화](continuous-video-recording.md) 
* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md) 
* [비디오 녹화 없이 비디오 분석](analyze-live-video-without-recording.md) 

## <a name="next-steps"></a>다음 단계

라이브 비디오 피드에서 동작 감지를 실행하는 방법은 [빠른 시작: Azure Video Analyzer 시작](get-started-detect-motion-emit-events.md)을 참조하세요.


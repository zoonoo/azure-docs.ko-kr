---
title: IoT Edge Faq의 Live Video Analytics-Azure
description: 이 항목에서는 IoT Edge Faq에서 라이브 비디오 분석에 대 한 답변을 제공 합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 705c74b1ba6d3a28911185b82a558099e87dc18d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011518"
---
# <a name="frequently-asked-questions-faqs"></a>FAQ(질문과 대답)

이 항목에서는 IoT Edge Faq에서 라이브 비디오 분석에 대 한 답변을 제공 합니다.

## <a name="general"></a>일반

그래프 토폴로지 정의에서 사용할 수 있는 시스템 변수는 무엇 인가요?

|변수   |설명|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|일반적으로 날짜와 시간으로 표시된 시간을 나타냅니다.|
|GraphTopologyName   |그래프의 청사진을 보유 하는 미디어 그래프 토폴로지를 나타냅니다.|
|System.object 인스턴스 이름|  미디어 그래프 인스턴스를 나타내고, 매개 변수 값을 보유 하 고, 토폴로지를 참조 합니다.|

## <a name="configuration-and-deployment"></a>구성 및 배포

미디어에 지 모듈을 Windows 10 장치에 배포할 수 있나요?
    * 예. [Windows 10의 Linux 컨테이너](/virtualization/windowscontainers/deploy-containers/linux-containers)에 대 한 문서를 참조 하세요.

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>IP 카메라 및 RTSP 설정에서 캡처

* 비디오 스트림으로 보내려면 디바이스에서 특별한 SDK를 사용해야 하나요?
    * 아니요. IoT Edge의 Live Video Analytics는 RTSP 비디오 스트리밍 프로토콜 (대부분의 IP 카메라에서 지원 됨)을 사용 하 여 미디어 캡처를 지원 합니다.
* RTMP 또는 부드러운 (예: Media Services 라이브 이벤트)를 사용 하 여 IoT Edge에서 라이브 비디오 분석으로 미디어를 푸시할 수 있나요?
    * 아니요. LVA는 IP 카메라에서 비디오를 캡처하기 위한 RTSP만 지원 합니다.
    * TCP/HTTP를 통한 RTSP 스트리밍을 지 원하는 모든 카메라는 작동 합니다. 
* 그래프 인스턴스에서 RTSP 원본 URL을 다시 설정하거나 업데이트할 수 있나요?
    * 예 (graph 인스턴스가 비활성 상태인 경우)  
* 테스트 및 개발 하는 동안 사용할 수 있는 RTSP 시뮬레이터가 있나요?
    * 예. 학습 프로세스를 지원 하기 위한 빠른 시작 및 자습서에서 사용할 수 있는 [RTSP 시뮬레이터](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) edge 모듈이 있습니다. 이 모듈은 최상의 노력으로 제공되며 항상 사용할 수 있는 것은 아닙니다. 이를 몇 시간 넘게 사용 하지 않는 것이 좋습니다. 프로덕션 배포에 대 한 계획을 만들기 전에 실제 RTSP 원본으로 테스트 하는 데 투자 해야 합니다.
* Edge에서 IP 카메라의 ONVIF 검색을 지원하나요?
    * 아니요. Edge에서 디바이스에 대한 ONVIF 검색을 지원하지 않습니다.

## <a name="streaming-and-playback"></a>스트리밍 및 재생

* 가장자리에서 AMS로 기록 된 자산은 HLS 또는 대시와 같은 Media Services 스트리밍 기술을 사용 하 여 재생할 수 있나요?
    * 예. 기록 된 자산은 Azure Media Services의 다른 자산과 같이 스트리밍할 수 있습니다. 콘텐츠를 스트리밍하려면 스트리밍 끝점이 생성 되 고 실행 중 상태 여야 합니다. 표준 스트리밍 로케이터 생성 프로세스를 사용 하면 모든 지원 플레이어 프레임 워크에 스트리밍하기 위해 HLS 또는 대시 매니페스트에 액세스할 수 있습니다. HLS 또는 대시 매니페스트 게시를 만드는 방법에 대 한 자세한 내용은 [동적 패키징](../latest/dynamic-packaging-overview.md)을 참조 하세요.
* 보관 된 자산에서 Media Services의 표준 콘텐츠 보호 및 DRM 기능을 사용할 수 있나요?
    * 예. 미디어 그래프에서 기록 된 자산에는 표준 동적 암호화 콘텐츠 보호 및 DRM 기능을 모두 사용할 수 있습니다.
* 기록 된 자산의 콘텐츠를 보는 데 사용할 수 있는 플레이어는 무엇입니까?
   * 규격 Apple HTTP 라이브 스트리밍 (HLS) 버전 3 또는 버전 4를 지 원하는 모든 표준 플레이어가 지원 됩니다. 또한 규격 MPEG 대시 재생을 지 원하는 플레이어도 지원 됩니다.
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
* 미디어 그래프 자산의 스트리밍에 대 한 제한은 무엇 인가요?
    * 미디어 그래프에서 라이브 또는 기록 된 자산을 스트리밍하는 것은 미디어 & 엔터테인먼트, Media Services 및 브로드캐스트 고객에 대 한 주문형 및 라이브 스트리밍에 대해 지원 되는 것과 동일한 높은 규모의 인프라 및 스트리밍 끝점을 사용 합니다. 즉, Verizon 또는 Akamai Azure CDN, Verizon 또는 Akamai의 사용자에 게 콘텐츠를 제공 하는 데 사용 하거나 시나리오에 따라 최대 수백만 명의 사용자에 게 콘텐츠를 제공할 수 있습니다.

    Apple HTTP 라이브 스트리밍 (HLS) 또는 MPEG-4를 모두 사용 하 여 콘텐츠를 배달할 수 있습니다.

## <a name="monitoring-and-metrics"></a>모니터링 및 메트릭

* Event Grid를 사용 하 여에 지에서 미디어 그래프를 모니터링할 수 있나요?
    * 아니요. 현재 Event Grid 지원 되지 않습니다.
* Azure Monitor를 사용 하 여 클라우드 또는에 지에서 내 미디어 그래프의 상태, 메트릭 및 성능을 볼 수 있나요?
    * 아니요.
* Media Services IoT Edge 모듈을 보다 쉽게 모니터링할 수 있는 도구가 있나요?
    * Visual Studio Code는 LVAEdge 모듈 끝점을 쉽게 모니터링할 수 있는 "Azure IoT Tools" 확장을 지원 합니다. 이 도구를 사용 하 여 "이벤트"에 대 한 IoT Hub 기본 제공 끝점 모니터링을 신속 하 게 시작 하 고에 지 장치에서 클라우드로 라우팅되는 유추 메시지를 확인할 수 있습니다. 

    또한이 확장을 사용 하 여 LVAEdge 모듈에 대 한 모듈 쌍을 편집 하 여 미디어 그래프 설정을 수정할 수 있습니다.

자세한 내용은 [모니터링 및 로깅](monitoring-logging.md) 문서를 참조 하세요.

## <a name="billing-and-availability"></a>요금 청구 및 가용성

* LiveVideo Analytics IoT Edge는 어떻게 청구 되나요?
    * 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[빠른 시작: 시작 - IoT Edge의 Live Video Analytics](get-started-detect-motion-emit-events-quickstart.md)

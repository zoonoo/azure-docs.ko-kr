---
title: 연속 비디오 녹화-Azure
description: CVR (연속 비디오 녹화)는 비디오 원본에서 비디오를 지속적으로 기록 하는 프로세스를 의미 합니다. 이 항목에서는 CVR의 용도에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 76af97fe1398421f5f37cfca32127d926ce56bac
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043305"
---
# <a name="continuous-video-recording"></a>연속 비디오 녹화  

## <a name="suggested-pre-reading"></a>추천 참고 자료  

* [미디어 그래프 개념](media-graph-concept.md)
* [비디오 기록 개념](video-recording-concept.md)

## <a name="overview"></a>개요

CVR (연속 비디오 녹화)는 비디오 원본에서 비디오를 지속적으로 기록 하는 프로세스를 의미 합니다. IoT Edge의 Live Video Analytics는 RTSP 원본 노드와 자산 싱크 노드로 구성 된 [미디어 그래프](media-graph-concept.md) 를 통해 cctv 카메라에서 지속적으로 비디오 기록을 지원 합니다. 아래 다이어그램에서는 이러한 미디어 그래프를 그래픽으로 표시 합니다. 이러한 미디어 그래프의 [그래프 토폴로지의](media-graph-concept.md?branch=release-preview-media-services-lva#media-graph-topologies-and-instances) JSON 표현은 [여기](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset)에서 찾을 수 있습니다.

![연속 비디오 녹화](./media/continuous-video-recording/continuous-video-recording-overview.png)

위에 표시 된 미디어 그래프는에 지 장치에서 실행 될 수 있습니다. asset sink는 Azure Media Services [자산](terminology.md#asset)에 비디오를 기록 합니다. 미디어 그래프가 활성화 된 상태로 유지 되는 동안에는 비디오가 기록 됩니다. 비디오는 자산으로 기록 되므로 Media Services의 기존 스트리밍 기능을 사용 하 여 재생할 수 있습니다. 자세한 내용은 [기록 된 콘텐츠 재생](video-playback-concept.md) 을 참조 하세요.

## <a name="resilient-recording"></a>복원 력 있는 기록

IoT Edge에 대 한 라이브 비디오 분석은에 지 장치가 종종 클라우드와의 연결을 잃거나 사용 가능한 대역폭을 사용 하지 못할 수 있는, 보다 낮은 수준의 네트워크 상태에서 작동할 수 있습니다. 이를 고려 하기 위해 소스의 비디오는 캐시에 로컬로 기록 되 고 주기적으로 자산과 자동으로 동기화 됩니다. [Graph 토폴로지 JSON](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json)을 검토 하면 다음 속성이 정의 되어 있는 것을 볼 수 있습니다.

```
    "segmentLength": "PT30S",
    "localMediaCacheMaximumSizeMiB": "2048",
    "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```
두 번째 속성은 복원 력 기록과 관련이 있습니다 (둘 다 자산 싱크 노드에 대 한 필수 속성 이기도 함). LocalMediaCachePath 속성은 자산 싱크에 업로드 하기 전에 해당 폴더 경로를 사용 하 여 미디어 데이터를 캐시 하도록 자산 싱크에 지시 합니다. [이](../../iot-edge/how-to-access-host-storage-from-module.md) 문서를 참조 하 여 edge 모듈이 장치의 로컬 저장소를 어떻게 활용할 수 있는지 파악할 수 있습니다. LocalMediaCacheMaximumSizeMiB 속성은 자산 싱크에서 캐시로 사용할 수 있는 디스크 공간의 크기 (1 MiB = 1024 * 1024 바이트)를 정의 합니다. 

Edge 모듈이 매우 긴 시간 동안 연결을 잃고 캐시 폴더에 저장 된 콘텐츠가 localMediaCacheMaximumSizeMiB 값에 도달 하는 경우 자산 싱크는 가장 오래 된 데이터부터 시작 하 여 캐시에서 데이터를 삭제 하기 시작 합니다. 예를 들어 장치에서 오전 10 시에 연결이 끊어진 경우 캐시가 오후 6 시의 최대 한도에 도달 하면 자산 싱크는 오전 10 시에 기록 된 데이터를 삭제 하기 시작 합니다. 

네트워크 연결이 복원 되 면 자산 싱크는 가장 오래 된 데이터부터 시작 하 여 캐시에서 업로드를 시작 합니다. 위의 예제에서는 연결이 복원 된 시간 (6:02PM)에 따라 캐시에서 5 분 분량의 비디오를 삭제 해야 한다고 가정 합니다. 그러면 자산 싱크가 10:05AM 표시에서 업로드가 시작 됩니다.

나중에 [이러한](playback-recordings-how-to.md) api를 사용 하 여 자산을 검사 하는 경우 자산에 약 오전 10 시에서 오전 10 시: 오전 10 시 까지의 간격이 있음을 알 수 있습니다.

## <a name="segmented-recording"></a>분할 기록  

위에서 설명한 것 처럼 자산 싱크 노드는 로컬 캐시에 비디오를 기록 하 고, 주기적으로 비디오를 클라우드에 업로드 합니다. SegmentLength 속성 (위 섹션에 표시)은 자산을 기록 하는 저장소 계정에 데이터를 쓰는 작업과 관련 된 쓰기 트랜잭션 비용을 제어 하는 데 도움이 됩니다. 예를 들어 업로드 기간을 30 초에서 5 분으로 늘리면 저장소 트랜잭션 수는 10 (5 * 60/30)의 비율로 삭제 됩니다.

SegmentLength 속성을 사용 하면 edge 모듈이 segmentLength 초 당 한 번씩 비디오를 업로드할 수 있습니다. 이 속성의 최소값은 30 초 (기본값) 이며, 최대 5 분까지 30 초 단위로 늘릴 수 있습니다.

>[!NOTE]
>SegmentLength가 재생에 미치는 영향에 대해서는 [이](playback-recordings-how-to.md) 문서를 참조 하세요.


## <a name="see-also"></a>참고 항목

* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)
* [기록 된 콘텐츠 재생](video-playback-concept.md)


## <a name="next-steps"></a>다음 단계

[자습서: 연속 비디오 녹화](continuous-video-recording-tutorial.md)

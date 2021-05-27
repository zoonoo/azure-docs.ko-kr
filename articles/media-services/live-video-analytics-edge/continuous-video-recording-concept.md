---
title: Live Video Analytics를 사용한 연속 비디오 녹화 - Azure
description: CVR(연속 비디오 녹화)은 Live Video Analytics를 사용하여 비디오 원본에서 비디오를 지속적으로 녹화하는 프로세스를 다룹니다. 이 항목에서는 CVR의 용도에 대해 설명합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 28fbe234984e3d75ef854bb8a001e5979d2bb97c
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110367679"
---
# <a name="continuous-video-recording-with-live-video-analytics"></a>Live Video Analytics를 사용한 연속 비디오 녹화

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

## <a name="suggested-pre-reading"></a>추천 참고 자료  

* [미디어 그래프 개념](media-graph-concept.md)
* [비디오 녹화 개념](video-recording-concept.md)

## <a name="overview"></a>개요

연속 비디오 녹화(CVR)는 비디오 원본에서 비디오를 지속적으로 녹화하는 프로세스를 다룹니다. IoT Edge의 Live Video Analytics는 RTSP 원본 노드와 자산 싱크 노드로 구성된 [미디어 그래프](media-graph-concept.md)를 통해 CCTV 카메라에서 연중무휴 연속 비디오 녹화를 지원합니다. 아래 다이어그램은 해당 미디어 그래프의 그래픽 표현을 보여 줍니다. 미디어 그래프의 [그래프 토폴로지](media-graph-concept.md#media-graph-topologies-and-instances)의 JSON 표현은 [여기](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset)에서 확인할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="연속 비디오 녹화":::

위에 설명된 미디어 그래프는 Azure Media Services [자산](terminology.md#asset)에 비디오를 녹화하는 자산 싱크를 통해 에지 디바이스에서 실행될 수 있습니다. 미디어 그래프가 활성화된 상태로 유지되는 동안에는 비디오가 녹화됩니다. 비디오는 자산으로 녹화되므로 Media Services의 기존 스트리밍 기능을 사용하여 재생할 수 있습니다. 자세한 내용은 [녹화된 콘텐츠 재생](video-playback-concept.md)을 참조하세요.

## <a name="resilient-recording"></a>복원력 있는 녹화

IoT Edge의 Live Video Analytics는 에지 디바이스가 가끔 클라우드와의 연결이 끊어지거나 가용 대역폭이 저하될 수 있는 불완전한 네트워크 조건에서의 작동을 지원합니다. 이를 설명하기 위해 원본의 비디오는 캐시에 로컬로 녹화되고 주기적으로 자산과 자동으로 동기화됩니다. [그래프 토폴로지 JSON](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json)을 살펴보면 다음과 같은 속성이 정의되어 있는 것을 알 수 있습니다.

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```

후자의 두 속성은 복원력 있는 녹화와 관련이 있습니다(둘 다 자산 싱크 노드에 대한 필수 속성이기도 함). localMediaCachePath 속성은 자산에 업로드하기 전에 해당 폴더 경로를 사용하여 미디어 데이터를 캐시하도록 자산 싱크에 지시합니다. [이](../../iot-edge/how-to-access-host-storage-from-module.md) 문서를 참조하여 에지 모듈이 디바이스의 로컬 스토리지를 어떻게 활용할 수 있는지 파악할 수 있습니다. localMediaCacheMaximumSizeMiB 속성은 자산 싱크가 캐시로 사용할 수 있는 디스크 공간의 크기(1MiB = 1024 * 1024바이트)를 정의합니다. 

에지 모듈이 매우 긴 시간 동안 연결이 끊어지고 캐시 폴더에 저장된 콘텐츠가 localMediaCacheMaximumSizeMiB 값에 도달하는 경우 자산 싱크는 가장 오래된 데이터부터 캐시에서 데이터를 삭제하기 시작합니다. 예를 들어 디바이스에서 오전 10시에 연결이 끊어진 경우 캐시가 오후 6시의 최대 한도에 도달하면 자산 싱크는 오전 10시에 기록된 데이터를 삭제하기 시작합니다. 

네트워크 연결이 복원되면 자산 싱크는 가장 오래된 데이터부터 다시 캐시에서 업로드를 시작합니다. 위의 예제에서는 연결이 복원된 시간(6:02PM)까지 캐시에서 5분 분량의 비디오를 삭제해야 한다고 가정합니다. 그러면 자산 싱크가 10:05AM부터 업로드를 시작합니다.

나중에 [해당](playback-recordings-how-to.md) API를 사용하여 자산을 살펴보면 약 10AM에서 10:05AM까지의 자산에 간격이 있음을 알 수 있습니다.

## <a name="segmented-recording"></a>분할된 녹화  

위에서 설명한 것처럼 자산 싱크 노드는 로컬 캐시에 비디오를 녹화하고, 주기적으로 비디오를 클라우드에 업로드합니다. segmentLength 속성(위 섹션에 표시됨)은 자산을 녹화하는 스토리지 계정에 데이터를 쓰는 작업과 관련된 쓰기 트랜잭션 비용을 제어하는 데 도움이 됩니다. 예를 들어 업로드 기간을 30초에서 5분으로 늘리면 스토리지 트랜잭션 수는 10(5*60/30)의 비율로 삭제됩니다.

segmentLength 속성을 사용하면 에지 모듈이 segmentLength 초당 최대 한 번 비디오를 업로드할 수 있습니다. 이 속성의 최솟값은 30초(기본값)이며, 최대 5분까지 30초 단위로 늘릴 수 있습니다.

> [!NOTE]
> segmentLength가 재생에 미치는 영향에 대해서는 [녹화 재생](playback-recordings-how-to.md) 문서를 참조하세요.

## <a name="see-also"></a>참조

* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)
* [녹화된 콘텐츠 재생](video-playback-concept.md)

## <a name="next-steps"></a>다음 단계

[자습서: 연속 비디오 녹화](continuous-video-recording-tutorial.md)

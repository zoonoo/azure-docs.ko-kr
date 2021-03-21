---
title: 규모에 Video Indexer을 사용할 때 고려해 야 할 사항-Azure
titleSuffix: Azure Media Services
description: 이 항목에서는 규모에 Video Indexer을 사용할 때 고려해 야 할 사항에 대해 설명 합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: b955c0f494b757fd29c400194ef8b11314a89a03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96483613"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>규모에 Video Indexer을 사용할 때 고려해 야 할 사항

비디오 인덱싱에 Azure Media Services 비디오 인덱서를 사용 하 여 비디오를 확장 하는 경우 크기를 조정 하는 것이 좋습니다. 

이 문서에서는 다음과 같은 질문에 대답 합니다.

* 고려해 야 하는 기술 제약 조건이 있나요?
* 효율적이 고 효율적인 방법 입니까?
* 프로세스에서 과도 한 비용 낭비를 방지할 수 있나요?

문서는 규모에 Video Indexer를 사용 하는 방법에 대 한 6 가지 모범 사례를 제공 합니다.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>비디오를 업로드 하는 경우 바이트 배열 보다 URL을 사용 하는 것이 좋습니다.

Video Indexer는 URL에서 비디오를 업로드 하거나 파일을 바이트 배열로 전송 하 여 직접 업로드 하는 옵션을 제공 하 고, 후자에는 몇 가지 제약 조건이 제공 됩니다. 자세한 내용은 [업로드 고려 사항 및 제한 사항](upload-index-videos.md#uploading-considerations-and-limitations) 을 참조 하세요.

먼저 파일 크기 제한이 있습니다. 바이트 배열 파일의 크기는 URL을 사용 하는 동안 30gb 업로드 크기 제한과 비교 하 여 2gb로 제한 됩니다.

둘째, 성능에 영향을 줄 수 있는 몇 가지 문제를 고려 하 여 크기를 조정 하는 것이 좋습니다.

* 다중 파트를 사용 하 여 파일을 보내는 것은 네트워크에 대 한 높은 종속성을 의미 합니다. 
* 서비스 안정성, 
* connectivity 
* 업로드 속도, 
* wwpn (wide web)에서 패킷이 손실 됩니다.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="규모에 Video Indexer를 사용 하기 위한 첫 번째 고려 사항":::

URL을 사용 하 여 비디오를 업로드 하는 경우 미디어 파일의 위치에 대 한 경로를 제공 하 고 나머지를 처리 하는 Video Indexer 해야 `videoUrl` 합니다. ( [Video API 업로드](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload) 의 필드 참조)

> [!TIP]
> `videoUrl`VIDEO API 업로드의 선택적 매개 변수를 사용 합니다.

URL을 사용 하 여 비디오를 업로드 하는 방법의 예제를 보려면 [다음 예제](upload-index-videos.md#code-sample)를 확인 하세요. 또는 [SAS URL](../../storage/common/storage-sas-overview.md)을 사용 하 여 Video Indexer에 전송할 수 있는 저장소 계정에 콘텐츠를 가져오는 빠르고 안정적인 방법으로 [AzCopy](../../storage/common/storage-use-azcopy-v10.md) 를 사용할 수 있습니다.

## <a name="increase-media-reserved-units-if-needed"></a>필요한 경우 미디어 예약 단위 늘리기

일반적으로 Video Indexer 사용을 시작 하는 경우 개념 증명 단계에서는 많은 컴퓨팅 전력이 필요 하지 않습니다. 비디오를 더 대규모로 배치 해야 하는 경우에는 인덱스를 사용 하 여 사용 사례에 맞는 속도를 유지 하려면 Video Indexer 사용량을 확장 해야 합니다. 따라서 현재 컴퓨팅 성능이 충분 하지 않은 경우에 사용 하는 계산 리소스의 수를 늘려야 합니다.

Azure Media Services에서 컴퓨팅 능력과 병렬 처리를 증가 시키려는 경우 RUs (미디어 [예약 단위](../latest/concept-media-reserved-units.md))에 주의 해야 합니다. RUs는 미디어 처리 작업에 대 한 매개 변수를 결정 하는 계산 단위입니다. RUs의 수는 각 계정에서 동시에 처리할 수 있는 미디어 작업의 수에 영향을 주며 해당 유형에 따라 처리 속도가 결정 되며 한 비디오에는 인덱싱 속도가 복잡 한 경우 두 개 이상의 추가 작업이 필요할 수 있습니다. RUs가 사용 중인 경우에는 다른 리소스를 사용할 수 있을 때까지 새 작업이 큐에 저장 됩니다.

작업을 효율적으로 수행 하 고 유휴 시간을 유지 하는 리소스를 사용 하지 않도록 하기 위해 Video Indexer는 처리가 필요 없을 때 RUs를 아래로 회전 하는 자동 크기 조정 시스템을 제공 하 고, 러시 시간 (모든 RUs를 완전히 사용)에 있는 경우 RUs를 스핀 합니다. 계정 설정에서 [자동 크기 조정을](manage-account-connected-to-azure.md#autoscale-reserved-units) 설정 하거나 [업데이트-유료-Azure-Media Services API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&pattern=update)를 사용 하 여이 기능을 사용 하도록 설정할 수 있습니다.

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="규모에 Video Indexer를 사용 하기 위한 두 번째 고려 사항":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="AMS 예약 단위":::

인덱싱 기간 및 낮은 처리량을 최소화 하려면 S3 형식의 10 RUs로 시작 하는 것이 좋습니다. 나중에 더 많은 콘텐츠 또는 더 높은 동시성을 지원 하도록 확장 하 고 더 많은 리소스를 필요로 하는 경우 지원 시스템 (유료 계정에만 해당)을 [사용 하 여](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 더 많은 RUs 할당을 요청할 수 있습니다.

## <a name="respect-throttling"></a>제한 준수

Video Indexer는 대규모로 인덱싱을 처리 하도록 작성 되었으며,이를 최대한 활용 하려는 경우에는 시스템의 기능을 파악 하 고 통합을 적절 하 게 디자인 해야 합니다. 비디오 일괄 처리에 대 한 업로드 요청을 전송 하 여 일부 영화는 업로드 되지 않았고 HTTP 429 응답 코드 (너무 많은 요청)를 수신 하 고 있음을 확인 하는 것이 좋습니다. 지원 되는 [분당 영화 제한](upload-index-videos.md#uploading-considerations-and-limitations)보다 많은 요청을 전송 했기 때문에 발생할 수 있습니다. `retry-after`HTTP 응답에 헤더를 추가 Video Indexer 헤더는 다음 재시도를 시도해 야 하는 경우를 지정 합니다. 다음 요청을 시도 하기 전에 준수 하는지 확인 합니다.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="제한을 준수 하 여 통합 웰 설계":::

## <a name="use-callback-url"></a>콜백 URL 사용

업로드 요청을 보낸 후에 요청 상태를 지속적으로 폴링하는 대신, [콜백 URL](upload-index-videos.md#callbackurl)을 추가 하 고 Video Indexer를 업데이트할 때까지 기다리는 것이 좋습니다. 업로드 요청에서 상태가 변경 되는 즉시 지정 된 URL에 대 한 POST 알림을 받게 됩니다.

콜백 URL은 [비디오 업로드 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload)의 매개 변수 중 하나로 추가할 수 있습니다. [GitHub 리포지토리의](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/)코드 샘플을 확인 하세요. 

콜백 URL의 경우 HTTP로 트리거할 수 있는 서버 리스 이벤트 구동 플랫폼인 Azure Functions를 사용 하 고 다음 흐름을 구현할 수도 있습니다.

### <a name="callback-url-definition"></a>콜백 URL 정의

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>올바른 인덱싱 매개 변수 사용

규모에 Video Indexer를 사용 하는 것과 관련 된 의사 결정을 내릴 때 요구 사항에 적합 한 매개 변수를 사용 하 여 최대한 활용 하는 방법을 살펴보세요. 다른 매개 변수를 정의 하 여 사용 사례에 대해 생각해 보고, 비용을 절감 하 고 비디오에 대 한 인덱싱 프로세스를 더 빠르게 수행할 수 있습니다.

비디오를 업로드 하 고 인덱싱 전에이 짧은 [설명서](upload-index-videos.md)를 읽고, [indexingpreset](upload-index-videos.md#indexingpreset) 및 [streamingpreset](upload-index-videos.md#streamingpreset) 을 확인 하 여 옵션에 대해 더 잘 이해 하세요.

예를 들어 비디오를 시청 하지 않으려는 경우에는 미리 설정을 스트리밍으로 설정 하지 마세요. audio insights만 필요한 경우에는 video insights를 인덱싱하지 마세요.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>가장 높은 해상도가 아닌 최적 해상도의 인덱스

비디오 인덱싱에 필요한 비디오 품질은 무엇 인가요? 

대부분의 경우 인덱싱 성능은 HD (720P) 비디오와 4K 비디오 간의 차이가 거의 없습니다. 결국 동일한 확신을 가진 거의 동일한 정보를 얻을 수 있습니다. 업로드 하는 동영상의 품질이 높으면 파일 크기가 높을수록 비디오를 업로드 하는 데 필요한 컴퓨팅 능력과 시간이 더 커집니다.

예를 들어 얼굴 감지 기능을 사용 하는 경우 더 높은 해상도를 사용 하면 작고 컨텍스트 중요 한 얼굴이 있는 시나리오에 도움이 될 수 있습니다. 그러나이로 인해 런타임에는 크게 증가 하 고 가양성의 위험이 증가 합니다.

따라서 사용 사례에 적합 한 결과를 얻고 먼저 로컬로 테스트 하는 것을 확인 하는 것이 좋습니다. 동일한 비디오를 720P 및 4K에 업로드 하 고 얻은 통찰력을 비교 합니다.

## <a name="next-steps"></a>다음 단계

[API에 의해 생성 된 Azure Video Indexer 출력 검사](video-indexer-output-json-v2.md)
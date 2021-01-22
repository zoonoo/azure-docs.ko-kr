---
title: 패키지 및 배달 시나리오 기반 마이그레이션 지침 | Microsoft Docs
description: 이 문서에서는 Azure Media Services v 2에서 v3로 마이그레이션하는 데 도움이 되는 패키지 및 배달에 대 한 시나리오 기반 지침을 제공 합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 74437a4adee9e2853f7b932316d57e406b7d00be
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690510"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>패키징 및 배달 시나리오 기반 마이그레이션 지침

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2 단계](./media/migration-guide/steps-4.svg)

이 문서에서는 Azure Media Services v 2에서 v3로 마이그레이션하는 데 도움이 되는 패키지 및 배달에 대 한 시나리오 기반 지침을 제공 합니다.

V3 API에서 콘텐츠가 게시 되는 방식에 대 한 주요 변경 내용입니다. 새 게시 모델은 간소화 되며 더 작은 엔터티를 사용 하 여 스트리밍 로케이터를 만듭니다. API는 두 개의 엔터티와 이전에 필요한 네 개의 엔터티로 줄어듭니다. 이제 콘텐츠 키 정책 및 스트리밍 로케이터는,, 및의 필요성 `ContentKeyAuthoriationPolicy` 을 대체 `AssetDeliveyPolicy` `ContentKey` `AccessPolicy` 합니다.

## <a name="packaging-and-delivery-in-v3"></a>V3에서 패키징 및 배달

1. [콘텐츠 키 정책을](content-key-policy-concept.md)만듭니다.
1. [스트리밍 로케이터](streaming-locators-concept.md)를 만듭니다.
1. [스트리밍 경로](create-streaming-locator-build-url.md) 가져오기 
    1. [대시](dynamic-packaging-overview.md#mpeg-dash-protocol) 또는 [HLS](dynamic-packaging-overview.md#hls-protocol) 플레이어에 대해 구성 합니다.

특정 단계는 게시 개념, 자습서 및 방법 가이드를 참조 하세요.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>게시 개념, 자습서 및 방법 가이드

### <a name="concepts"></a>개념

- [Media Services v3의 동적 패키징](dynamic-packaging-overview.md)
- [필터](filters-concept.md)
- [동적 포장기를 사용 하 여 매니페스트 필터링](filters-dynamic-manifest-overview.md)
- [Azure Media Services의 스트리밍 끝점 (원본)](streaming-endpoint-concept.md)
- [CDN 통합을 사용 하 여 콘텐츠 스트리밍](scale-streaming-cdn.md)
- [스트리밍 로케이터](streaming-locators-concept.md)

### <a name="how-to-guides"></a>방법 가이드

- [Media Services v3을 사용 하 여 스트리밍 끝점 관리](manage-streaming-endpoints-howto.md)
- [CLI 예: 자산 게시](cli-publish-asset.md)
- [스트리밍 로케이터 생성 및 URL 빌드](create-streaming-locator-build-url.md)
- [작업 결과 다운로드](download-results-howto.md)
- [설명 오디오 트랙 신호](signal-descriptive-audio-howto.md)
- [Azure Media Player 전체 설정](https://docs.microsoft.com/azure/media-services/azure-media-player/azure-media-player-full-setup)
- [Azure Media Services에서 Video.js 플레이어를 사용 하는 방법](how-to-video-js-player.md)
- [Azure Media Services에서 Shaka 플레이어를 사용 하는 방법](how-to-shaka-player.md)

## <a name="samples"></a>샘플

[코드 샘플에서 V2 및 V3 코드도 비교할](migrate-v-2-v-3-migration-samples.md)수 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]

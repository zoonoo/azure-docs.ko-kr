---
title: 패키징 및 전달 마이그레이션 지침
description: 이 문서에서는 Azure Media Services v2에서 v3로 마이그레이션하는 데 도움이 되는 패키징 및 전달에 대한 시나리오 기반 지침을 제공합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: d8cba5c69802828a0bd486d902d85199f3ae1d00
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567911"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>패키징 및 전달 시나리오 기반 마이그레이션 지침

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 단계 2](./media/migration-guide/steps-4.svg)

이 문서에서는 Azure Media Services v2에서 v3로 마이그레이션하는 데 도움이 되는 패키징 및 전달에 대한 시나리오 기반 지침을 제공합니다.

콘텐츠가 v3 API에 게시되는 방식에 대한 주요 변경 내용입니다. 새 게시 모델은 간소화되며 더 작은 엔터티를 사용하여 스트리밍 로케이터를 만듭니다. API는 이전에 4개의 엔터티가 필요했던 것에 비해 2개의 엔터티로 줄어들었습니다. 이제 콘텐츠 키 정책 및 스트리밍 로케이터가 `ContentKeyAuthorizationPolicy`, `AssetDeliveryPolicy`, `ContentKey` 및 `AccessPolicy`의 필요성을 대체합니다.

## <a name="packaging-and-delivery-in-v3"></a>v3의 패키징 및 전달

1. [콘텐츠 키 정책](drm-content-key-policy-concept.md)을 만듭니다.
1. [스트리밍 로케이터](stream-streaming-locators-concept.md)를 만듭니다.
1. [스트리밍 경로](create-streaming-locator-build-url.md) 가져오기 
    1. [DASH](encode-dynamic-packaging-concept.md#deliver-dash) 또는 [HLS](encode-dynamic-packaging-concept.md#deliver-hls) 플레이어용으로 구성합니다.

특정 단계는 아래 게시 개념, 자습서 및 방법 가이드를 참조하세요.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>게시 개념, 자습서 및 방법 가이드

### <a name="concepts"></a>개념

- [Media Services v3의 동적 패키징](encode-dynamic-packaging-concept.md)
- [필터](filters-concept.md)
- [Dynamic Packager를 사용하여 매니페스트 필터링](filters-dynamic-manifest-concept.md)
- [Azure Media Services의 스트리밍 엔드포인트(원본)](stream-streaming-endpoint-concept.md)
- [CDN 통합을 통한 콘텐츠 스트리밍](stream-scale-streaming-cdn-concept.md)
- [스트리밍 로케이터](stream-streaming-locators-concept.md)

### <a name="how-to-guides"></a>방법 가이드

- [Media Services v3를 통해 스트리밍 엔드포인트 관리](stream-manage-streaming-endpoints-how-to.md)
- [CLI 예: 자산 게시](cli-publish-asset.md)
- [스트리밍 로케이터 생성 및 URL 빌드](create-streaming-locator-build-url.md)
- [작업 결과 다운로드](job-download-results-how-to.md)
- [신호 설명 오디오 트랙](signal-descriptive-audio-howto.md)
- [Azure Media Player 전체 설정](../azure-media-player/azure-media-player-full-setup.md)
- [Azure Media Services에서 Video.js 플레이어를 사용하는 방법](player-how-to-video-js-player.md)
- [Azure Media Services에서 Shaka 플레이어를 사용하는 방법](player-shaka-player-how-to.md)

## <a name="samples"></a>샘플

[코드 샘플에서 V2와 V3 코드를 비교](migrate-v-2-v-3-migration-samples.md)할 수도 있습니다.

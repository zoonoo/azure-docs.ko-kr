---
title: Media Services 라이브 스트리밍 마이그레이션 지침
description: 이 문서에서는 Azure Media Services v2에서 v3로 마이그레이션하는 데 도움이 되는 라이브 스트리밍 시나리오 기반 지침을 제공합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: d021267c38f0043d2361b1a6392fbacd0634a164
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279700"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>라이브 스트리밍 시나리오 기반 마이그레이션 지침

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2단계](./media/migration-guide/steps-4.svg)

이제 Azure Portal에서 라이브 이벤트 설정 및 관리를 지원합니다.  V2에서 V3로의 마이그레이션을 테스트하는 동안 사용해 보는 것이 좋습니다.

## <a name="test-the-v3-live-event-workflow"></a>V3 라이브 이벤트 워크플로 테스트

> [!NOTE]
> v2로 만든 채널 및 프로그램(v3에서는 라이브 이벤트 및 라이브 출력으로 매핑)은 V3 api로 관리됩니다. 이 지침은 기존 V2 채널을 중지할 수 있는 편리한 시간에 V3 라이브 이벤트 및 라이브 출력으로 전환하는 것에 대한 것입니다. 현재는 지속적으로 실행되는 연중 무휴 라이브 채널을 새 V3 라이브 이벤트로 원활하게 마이그레이션할 수 있는 기능이 없습니다. 따라서 유지 관리 가동 중지 시간은 대상 및 비즈니스에 가장 적합한 방식으로 조정해야 합니다.

V2에서 V3로 콘텐츠를 이동하기 전에 Media Services를 통해 라이브 이벤트를 전달하는 새로운 방법을 테스트합니다. 다음은 마이그레이션에서 사용할 수 있는 V3 기능입니다.

- 인코딩에 대한 새 v3 [라이브 이벤트](live-event-outputs-concept.md#live-events)를 만듭니다. [1080P 및 720P 인코딩 사전 설정](live-event-types-comparison-reference.md#system-presets)을 활성화할 수 있습니다.
- 프로그램 대신 [라이브 출력](live-event-outputs-concept.md#live-outputs) 엔터티 사용
- [스트리밍 로케이터](stream-streaming-locators-concept.md)를 만듭니다.
- [HLS 및 DASH](encode-dynamic-packaging-concept.md) 라이브 스트리밍에 대한 요구 사항을 고려하세요.
- 라이브 이벤트의 빠른 시작이 필요한 경우 새 [대기 모드](live-event-outputs-concept.md#standby-mode) 기능을 살펴봅니다.
- 라이브 이벤트가 발생하는 동안 라이브 이벤트를 기록하려면 새 [라이브](live-event-live-transcription-how-to.md) 기록 기능을 살펴봅니다.
- 더 긴 스트리밍 기간이 필요한 경우 v3에서 연중 무휴 라이브 이벤트를 만듭니다.
- [Event Grid](monitoring/monitor-events-portal-how-to.md)를 사용하여 라이브 이벤트를 모니터링합니다.

특정 단계는 아래 라이브 이벤트 개념, 자습서 및 방법 가이드를 참조하세요.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>라이브 이벤트 개념, 자습서 및 방법 가이드

### <a name="concepts"></a>개념

- [Azure Media Services v3를 통한 라이브 스트리밍](stream-live-streaming-concept.md)
- [Media Services의 라이브 이벤트 및 라이브 출력](live-event-outputs-concept.md)
- [검증된 온-프레미스 라이브 스트리밍 인코더](encode-recommended-on-premises-live-encoders.md)
- [타임 시프팅 및 라이브 출력을 사용하여 주문형 비디오 재생 만들기](live-event-cloud-dvr-time-how-to.md)
- [live-event-live-transcription-how-to(미리 보기)](live-event-live-transcription-how-to.md)
- [라이브 이벤트 유형 비교](live-event-types-comparison-reference.md)
- [라이브 이벤트 상태 및 청구](live-event-states-billing-concept.md)
- [라이브 이벤트의 짧은 대기 시간 설정](live-event-latency-reference.md)
- [Media Services 라이브 이벤트 오류 코드](live-event-error-codes-reference.md)

### <a name="tutorials-and-quickstarts"></a>자습서 및 빠른 시작

- [자습서: Media Services로 라이브 스트리밍](stream-live-tutorial-with-api.md)
- [OBS를 사용하여 Azure Media Services 라이브 스트림 만들기](live-event-obs-quickstart.md)
- [빠른 시작: 포털을 사용하여 콘텐츠 업로드, 인코딩 및 스트리밍](asset-create-asset-upload-portal-quickstart.md)
- [빠른 시작: Wirecast를 사용하여 Azure Media Services 라이브 스트림 만들기](live-event-wirecast-quickstart.md)

## <a name="samples"></a>샘플

[코드 샘플에서 V2와 V3 코드를 비교](migrate-v-2-v-3-migration-samples.md)할 수도 있습니다.

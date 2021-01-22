---
title: 라이브 스트리밍에 대 한 Media Services v2에서 v3 마이그레이션 시나리오 기반 지침 | Microsoft Docs
description: 이 문서에서는 Azure Media Services v 2에서 v 2로의 최소 마이그레이션에 도움이 되는 라이브 스트리밍 시나리오 기반 지침을 제공 합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 89fcf85b20d11664d5d1caa3fbe142fa5bbdbebc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690487"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>라이브 스트리밍 시나리오 기반 마이그레이션 지침

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2 단계](./media/migration-guide/steps-4.svg)

이제 Azure Portal에서 라이브 이벤트 설정 및 관리를 지원 합니다.  V2를 V3 마이그레이션을 테스트 하는 동안 사용해 보는 것이 좋습니다.

## <a name="test-the-v3-live-event-workflow"></a>V3 라이브 이벤트 워크플로 테스트

> [!NOTE]
> V 2를 사용 하 여 만든 채널 및 프로그램은 v 3에서 라이브 이벤트 및 라이브 출력에 매핑되고 v3 api를 사용 하 여 관리 됩니다. 기존 V2 채널을 중지할 수 있는 경우 편리한 시간에 V3 라이브 이벤트 및 라이브 출력으로 전환 하는 방법에 대 한 지침을 제공 합니다. 현재는 지속적으로 실행 되는 연중 무휴 라이브 채널을 새 V3 라이브 이벤트로 원활 하 게 마이그레이션할 수 있는 기능이 없습니다. 따라서 유지 관리 가동 중지 시간은 대상 및 비즈니스에 가장 적합 한 방식으로 조정 해야 합니다.

V 2에서 V3로 콘텐츠를 이동 하기 전에 Media Services을 사용 하 여 라이브 이벤트를 배달 하는 새로운 방법을 테스트 합니다. 다음은에서 사용할 수 있는 V3 기능이 며 마이그레이션을 고려 합니다.

- 인코딩에 대 한 새 v3 [라이브 이벤트](live-events-outputs-concept.md#live-events) 를 만듭니다. [1080p 및 720p 인코딩 사전 설정을](live-event-types-comparison.md#system-presets)사용 하도록 설정할 수 있습니다.
- 프로그램 대신 [라이브 출력](live-events-outputs-concept.md#live-outputs) 엔터티 사용
- [스트리밍 로케이터](streaming-locators-concept.md)를 만듭니다.
- [HLS 및 대시](dynamic-packaging-overview.md) 라이브 스트리밍에 대 한 요구 사항을 고려 하세요.
- 라이브 이벤트의 빠른 시작이 필요한 경우 새 [대기 모드](live-events-outputs-concept.md#standby-mode) 기능을 탐색 합니다.
- 라이브 이벤트가 발생 하는 동안 높여줄 하려면 새 [라이브](live-transcription.md) 기록 기능을 탐색 합니다.
- 더 긴 스트리밍 기간이 필요한 경우 v3에서 연중 무휴 라이브 이벤트를 만듭니다.
- [Event Grid](monitor-events-portal-how-to.md) 사용 하 여 라이브 이벤트를 모니터링 합니다.

특정 단계는 라이브 이벤트 개념, 자습서 및 아래 가이드를 참조 하세요.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>라이브 이벤트 개념, 자습서 및 방법 가이드

### <a name="concepts"></a>개념

- [Azure Media Services v3를 통한 라이브 스트리밍](live-streaming-overview.md)
- [Media Services의 라이브 이벤트 및 라이브 출력](live-events-outputs-concept.md)
- [확인 된 온-프레미스 라이브 스트리밍 인코더](recommended-on-premises-live-encoders.md)
- [시간 이동 및 라이브 출력을 사용 하 여 주문형 비디오 재생 만들기](live-event-cloud-dvr.md)
- [라이브 기록 (미리 보기)](live-transcription.md)
- [라이브 이벤트 유형 비교](live-event-types-comparison.md)
- [라이브 이벤트 상태 및 청구](live-event-states-billing.md)
- [라이브 이벤트 낮은 대기 시간 설정](live-event-latency.md)
- [Media Services 라이브 이벤트 오류 코드](live-event-error-codes.md)

### <a name="tutorials-and-quickstarts"></a>자습서 및 빠른 시작

- [자습서: Media Services로 라이브 스트리밍](stream-live-tutorial-with-api.md)
- [OBS를 사용하여 Azure Media Services 라이브 스트림 만들기](live-events-obs-quickstart.md)
- [빠른 시작: 포털을 사용하여 콘텐츠 업로드, 인코딩 및 스트리밍](manage-assets-quickstart.md)
- [빠른 시작: Wirecast를 사용 하 여 Azure Media Services 라이브 스트림 만들기](live-events-wirecast-quickstart.md)

## <a name="samples"></a>샘플

[코드 샘플에서 V2 및 V3 코드도 비교할](migrate-v-2-v-3-migration-samples.md)수 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]

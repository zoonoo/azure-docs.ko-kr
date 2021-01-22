---
title: Azure Media Services v 2에서 v3 소개로 마이그레이션 | Microsoft Docs
description: 이 문서에서는 Media Services v 2에서 v3로 마이그레이션하는 방법에 대해 소개 합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: fb9abd8f3186405edc31b4af48ee98482e080c68
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690588"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Media Services v 2에서 v3 소개로 마이그레이션

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

Media Services 마이그레이션 가이드를 통해 현재 사용할 수 있는 새로운 기능 및 기능을 활용 하는 마이그레이션에 따라 Media Services V2 Api에서 V3 Api로 마이그레이션할 수 있습니다. 최고의 판단을 사용 하 고 시나리오에 가장 적합 한 결정을 해야 합니다.

## <a name="how-to-use-this-guide"></a>이 가이드를 사용하는 방법

### <a name="navigating"></a>탐색

가이드 전체에서 다음과 같은 그래픽이 표시 됩니다.

![마이그레이션 단계](./media/migration-guide/steps.svg)<br/>

사용자가 수행 하는 단계는 다음과 같이 단계 번호의 색 변경으로 표시 됩니다.

![마이그레이션 2 단계](./media/migration-guide/steps-2.svg)<br/>

각 페이지의 끝에 **다음 단계** 제목 아래에서 읽을 수 있는 나머지 마이그레이션 문서에 대 한 링크가 표시 됩니다.

### <a name="guidance"></a>지침

여기에 제공 된 지침은 *일반적* 입니다. 여기에는 현재 V3에서 사용할 수 있는 기능 및 Media Services 워크플로에서 변경 된 기능에 대 한 인식을 향상 시킬 수 있는 콘텐츠가 포함 되어 있습니다.

스크린샷 및 개념적 그래픽을 비롯 한 자세한 지침을 보려면 각 시나리오 기반 항목에서 개념, 자습서, 빠른 시작, 샘플 및 API 참조에 대 한 링크가 있습니다. V2 API를 V3 API와 비교 하는 데 도움이 되는 샘플도 나열 되어 있습니다.

## <a name="migration-guidance-overview"></a>마이그레이션 지침 개요

마이그레이션 중에 따라야 하는 네 가지 일반적인 단계가 있습니다.

## <a name="step-1-benefits"></a>1 단계 이점

<hr color="#5ea0ef" size="10">

Media Services API V3로 마이그레이션하는 [이점에 대해 알아봅니다](migrate-v-2-v-3-migration-benefits.md) .

## <a name="step-2-differences"></a>2 단계 차이점

<hr color="#5ea0ef" size="10">

Media Services V2 API와 V3 API 간의 차이점을 이해 합니다.

- [API 액세스](migrate-v-2-v-3-differences-api-access.md)
- [기능 간격](migrate-v-2-v-3-differences-feature-gaps.md)
- [용어 및 엔터티 변경](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>3 단계 SDK 설치

<hr color="#5ea0ef" size="10">

SDK 차이점을 이해 하 고 [V3 REST API 또는 클라이언트 SDK로 마이그레이션하도록 설정](migrate-v-2-v-3-migration-setup.md)합니다.

## <a name="step-4-scenario-based-guidance"></a>4 단계 시나리오 기반 지침

<hr color="#5ea0ef" size="10">

Media Services V2의 응용 프로그램은 고유할 수 있습니다. 따라서 이전에 media services *를 사용 하는 방법* 및 서비스의 각 기능에 대 한 단계를 기준으로 다음과 같은 시나리오 기반 지침을 제공 합니다.

- [인코딩](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [라이브 스트리밍](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [패키징 및 제공](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [콘텐츠 보호](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [MRU (미디어 예약 단위)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)

## <a name="next-steps"></a>다음 단계

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]

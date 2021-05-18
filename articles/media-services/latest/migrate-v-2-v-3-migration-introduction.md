---
title: Media Services v2에서 v3으로 마이그레이션 소개
description: 이 문서에서는 Media Services v2에서 v3로 마이그레이션하는 방법을 소개합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 32e502ff175a9222faa0eb79fb53f2cc3f76bfb7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559743"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Media Services v2에서 v3으로 마이그레이션 소개

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

Media Services 마이그레이션 가이드는 현재 사용할 수 있는 새로운 기능을 활용하는 마이그레이션을 기반으로 Media Services V2 API에서 V3 API로 마이그레이션하는 데 도움이 됩니다. 사용자의 시나리오에 가장 적합한 방법을 잘 판단해 결정해야 합니다.

## <a name="how-to-use-this-guide"></a>이 가이드를 사용하는 방법

### <a name="navigating"></a>탐색

가이드 전체에서 다음과 같은 그래픽을 보게 됩니다.

![마이그레이션 단계](./media/migration-guide/steps.svg)<br/>

현재 단계는 다음과 같이 단계 번호의 바뀐 색상으로 표시됩니다.

![마이그레이션 2단계](./media/migration-guide/steps-2.svg)<br/>

각 페이지의 끝에는 **다음 단계** 제목 아래에 읽어 볼 수 있는 나머지 마이그레이션 문서의 링크가 나와 있습니다.

### <a name="guidance"></a>지침

여기 제공된 지침은 일반적입니다. 이 지침에는 현재 V3에서 사용할 수 있는 기능 및 Media Services 워크플로에서 변경된 기능에 대한 이해를 높일 수 있는 콘텐츠가 포함되어 있습니다.

각 시나리오 기반 항목에 있는 개념, 자습서, 빠른 시작, 샘플 및 API 참조에 대한 링크를 통해 스크린샷과 개념적 그래픽을 비롯한 자세한 지침을 볼 수 있습니다. V2 API를 V3 API와 비교하는 데 도움이 되는 샘플도 나열되어 있습니다.

## <a name="migration-guidance-overview"></a>마이그레이션 지침 개요

마이그레이션 중에 따라야 하는 네 가지 일반적인 단계는 다음과 같습니다.

## <a name="step-1-benefits"></a>1단계 이점

<hr color="#5ea0ef" size="10">

Media Services API V3로 마이그레이션하는 [이점을 이해](migrate-v-2-v-3-migration-benefits.md)합니다.

## <a name="step-2-differences"></a>2단계 차이점

<hr color="#5ea0ef" size="10">

Media Services V2 API와 V3 API의 차이점을 이해합니다.

- [API 액세스](migrate-v-2-v-3-differences-api-access.md)
- [기능 차이](migrate-v-2-v-3-differences-feature-gaps.md)
- [용어 및 엔터티 변경](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>3단계 SDK 설정

<hr color="#5ea0ef" size="10">

SDK 차이점을 이해하고 [V3 REST API 또는 클라이언트 SDK로 마이그레이션하도록 설정](migrate-v-2-v-3-migration-setup.md)합니다.

## <a name="step-4-scenario-based-guidance"></a>4단계 시나리오 기반 지침

<hr color="#5ea0ef" size="10">

Media Services V2를 활용하는 방법은 사용자마다 고유할 수 있습니다. 따라서 사용자의 과거 미디어 서비스 사용 방법과 다음과 같은 서비스의 각 기능을 바탕으로 시나리오 기반 지침을 제공했습니다.

- [인코딩](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [라이브 스트리밍](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [패키징 및 제공](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [콘텐츠 보호](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [MRU(미디어 예약 단위)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)

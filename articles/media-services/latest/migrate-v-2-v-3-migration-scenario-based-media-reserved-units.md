---
title: Mru (미디어 예약 단위) 마이그레이션 지침
description: 이 문서에서는 Azure Media Services v 2에서 V3로 마이그레이션하는 데 도움이 되는 MRU 시나리오 기반 지침을 제공 합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 00e1fa623fc625a568f6580998443758c3916be8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563075"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>Mru (미디어 예약 단위) 시나리오 기반 마이그레이션 지침

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2 단계](./media/migration-guide/steps-4.svg)

이 문서에서는 Azure Media Services v 2에서 V3로 마이그레이션하는 데 도움이 되는 MRU 시나리오 기반 지침을 제공 합니다.

- Azure Portal에서 만들었거나 V3 API의 2020-05-01 버전을 사용 하 여 만든 새 V3 계정의 경우 Mru (미디어 예약 단위)를 설정 하는 데 더 이상 필요 하지 않습니다. 이제 시스템이 부하에 따라 자동으로 확장 및 축소 됩니다.
- 2020-05-01 버전의 API 이전에 만든 V3 또는 V2 계정이 있는 경우 미디어 예약 단위를 사용 하 여 작업의 동시성 및 성능을 계속 제어할 수 있습니다. 자세한 내용은 미디어 처리 크기 조정을 참조하세요. Media Services V3에 대해 CLI 2.0을 사용 하거나 Azure Portal를 사용 하 여 Mru를 관리할 수 있습니다.  
- Azure Portal에서 Mru를 관리 하는 옵션이 표시 되지 않으면 2020-05-01 API 이상으로 만든 계정을 실행 하 고 있는 것입니다.
- MRU 유형을 s 3으로 설정 하는 방법을 잘 알고 있는 경우 성능이 향상 되거나 동일 하 게 유지 됩니다.
- 기존 V2 고객의 경우 마이그레이션이 완료 되기 전에 기존 응용 프로그램을 지원 하기 위해 새 V2 계정을 만들어야 합니다. 
- 완전히 사용 되지 않는 인덱서 V1 또는 기타 미디어 프로세서는 다시 사용 하도록 설정 해야 할 수 있습니다. 

Mru에 대 한 자세한 내용은 [미디어 예약 단위](concept-media-reserved-units.md) 및 [미디어 예약 단위 크기 조정 방법](media-reserved-units-cli-how-to.md)을 참조 하세요.

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>MRU 개념, 자습서 및 방법 가이드

### <a name="concepts"></a>개념

[미디어 예약 단위](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>방법 가이드

[미디어 예약 단위 크기를 조정 하는 방법](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>샘플

[코드 샘플에서 V2 및 V3 코드도 비교할](migrate-v-2-v-3-migration-samples.md)수 있습니다.

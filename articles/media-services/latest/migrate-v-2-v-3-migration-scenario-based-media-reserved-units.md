---
title: MRU(미디어 예약 단위) 마이그레이션 참고 자료
description: 이 문서에서는 Azure Media Services V2에서 V3로 마이그레이션하는 데 도움이 되는 MRU 시나리오 기반 참고 자료를 제공합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 00e1fa623fc625a568f6580998443758c3916be8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563075"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>MRU(미디어 예약 단위) 시나리오 기반 마이그레이션 참고 자료

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2단계](./media/migration-guide/steps-4.svg)

이 문서에서는 Azure Media Services V2에서 V3로 마이그레이션하는 데 도움이 되는 MRU 시나리오 기반 참고 자료를 제공합니다.

- Azure Portal에서 만들었거나 V3 API의 2020-05-01 버전을 사용하여 만든 새 V3 계정의 경우 MRU(미디어 예약 단위)의 설정은 더 이상 필요하지 않습니다. 이제 시스템이 부하에 따라 자동으로 스케일 업/다운합니다.
- 2020-05-01 버전 API 이전에 만든 V3 또는 V2 계정이 있는 경우 미디어 예약 단위를 사용하여 작업의 동시성 및 성능을 계속 제어할 수 있습니다. 자세한 내용은 미디어 처리 크기 조정을 참조하세요. Media Services V3용 CLI 2.0 또는 Azure Portal을 사용하여 MRU를 관리할 수 있습니다.  
- Azure Portal에서 MRU를 관리하는 옵션이 표시되지 않으면 2020-05-01 API 혹은 그 이후에 만든 계정을 실행하고 있는 것입니다.
- MRU 형식을 S3로 설정하는 데 능숙하다면 성능이 향상되거나 동일하게 유지됩니다.
- 기존 V2 고객의 경우 마이그레이션이 완료되기 전에 기존 애플리케이션을 지원하기 위해 새 V2 계정을 만들어야 합니다. 
- 완전히 사용되지 않는 인덱서 V1 또는 기타 미디어 프로세서는 다시 사용하도록 설정해야 할 수 있습니다. 

MRU에 대한 자세한 내용은 [미디어 예약 단위](concept-media-reserved-units.md) 및 [미디어 예약 단위 크기 조정 방법](media-reserved-units-cli-how-to.md)을 참조하세요.

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>MRU 개념, 자습서 및 방법 가이드

### <a name="concepts"></a>개념

[미디어 예약 단위](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>방법 가이드

[미디어 예약 단위를 스케일링하는 방법](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>샘플

또한 [코드 샘플에서 V2와 V3 코드를 비교](migrate-v-2-v-3-migration-samples.md)할 수 있습니다.

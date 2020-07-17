---
title: Azure 피어 링 서비스 연결 원격 분석
description: Microsoft Azure 피어 링 서비스 연결 원격 분석에 대 한 자세한 정보
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871303"
---
# <a name="peering-service-connection-telemetry"></a>피어 링 서비스 연결 원격 분석

연결 원격 분석은 고객의 위치와 Microsoft 네트워크 간의 연결에 대해 수집된 인사이트를 제공합니다. 고객은 Azure Portal에서 연결을 등록 하 여 Azure 피어 링 서비스 연결에 대 한 원격 분석을 얻을 수 있습니다. 이 기능은 네트워크 성능에 대 한 접두사 보안 및 통찰력을 제공 합니다.


연결 원격 분석은 다음 범위로 구성 됩니다.

### <a name="latency-measurement"></a>대기 시간 측정

 대기 시간은 클라이언트에서 피어 링 서비스 내에 등록 된 접두사에 대해 Microsoft Edge PoP로 측정 됩니다.

### <a name="route-prefix-monitoring-and-protection"></a>경로 접두사 모니터링 및 보호

라우팅 경로는 이벤트 로그에 캡처되는 모든 의심 스러운 활동에 대해 모니터링 됩니다. 예를 들어, 다음 요소 중 일부에 대해 이벤트 로그가 생성 됩니다.

- 하이재킹 등 접두사
- 출금 접두사
- 경로 누수

## <a name="next-steps"></a>다음 단계

- Peering Service 연결에 대해 알아보려면 [Peering Service 연결](connection.md)을 참조하세요.
- 피어 링 서비스 연결을 등록 하려면 [피어 링 서비스 모델 온 보 딩](onboarding-model.md)을 참조 하세요.
- 원격 분석을 측정하려면 [연결 원격 분석 측정](measure-connection-telemetry.md)을 참조하세요.

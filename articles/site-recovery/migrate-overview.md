---
title: Azure로의 마이그레이션에 대해 Azure Migrate 및 Site Recovery 비교
description: 마이그레이션에 Site Recovery 대신 Azure Migrate를 사용하는 경우의 이점을 요약합니다.
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: c4d30b8b21bf3a0ea27467d2120b04a107f5a73f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581414"
---
# <a name="migrating-to-azure"></a>Azure로 마이그레이션

마이그레이션할 경우 Azure Site Recovery 서비스보다는 Azure Migrate 서비스를 사용하여 VM 및 서버를 Azure로 마이그레이션하는 것이 좋습니다. Azure Migrate에 대해 [알아봅니다](../migrate/migrate-services-overview.md).


## <a name="why-use-azure-migrate"></a>Azure Migrate를 사용하는 이유

마이그레이션에 Azure Migrate를 사용하면 다음과 같은 여러 가지 이점이 있습니다.
 
 
- Azure Migrate는 Azure에 검색, 평가 및 마이그레이션을 위한 중앙화된 허브를 제공합니다.
- Azure Migrate를 사용하면 Azure Migrate 도구, 다른 Azure 서비스 및 타사 도구와의 상호 운용성 및 향후의 확장성이 제공됩니다.
- Azure Migrate:서버 마이그레이션 도구는 Azure로의 서버 마이그레이션을 위해 특별히 개발되었습니다. 마이그레이션에 최적화되어 있습니다. 마이그레이션과 직접적인 관련이 없는 개념이나 시나리오는 몰라도 됩니다. 
- VM에 복제가 시작된 시점부터 180일 동안 마이그레이션에 대한 도구 사용 요금은 없습니다. 이는 마이그레이션을 완료할 시간을 줍니다. 복제에 사용된 스토리지와 네트워크 리소스, 그리고 테스트 마이그레이션 중 사용된 컴퓨팅 요금에 대해서만 비용을 청구합니다.
- Azure Migrate는 Site Recovery에서 지원되는 모든 마이그레이션 시나리오를 지원합니다. 또한 VMware VM에 대해 Azure Migrate는 에이전트 없는 마이그레이션 옵션을 제공합니다.
- Azure Migrate:Server Migration 도구 전용인 새로운 마이그레이션 기능에 우선 순위를 두고 있습니다. 해당 기능은 Site Recovery 대상이 아닙니다.

## <a name="when-to-use-site-recovery"></a>Site Recovery를 언제 사용하나요?

Site Recovery는 다음과 같은 경우에 사용해야 합니다.

- Azure로 온-프레미스 컴퓨터의 재해 복구를 하는 경우입니다.
- Azure 지역 간 Azure VM 재해 복구의 경우.

Azure Migrate를 사용하여 온-프레미스 서버를 Azure로 마이그레이션하는 것이 좋지만 이미 Site Recovery를 사용하여 마이그레이션 여정을 시작한 경우에는 그대로 계속 사용하여 마이그레이션을 완료할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

> Azure Migrate에 대한 [일반적인 질문을 검토](../migrate/resources-faq.md)합니다.

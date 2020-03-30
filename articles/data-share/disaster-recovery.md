---
title: Azure 데이터 공유에 대한 재해 복구
description: Azure 데이터 공유에 대한 재해 복구
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483181"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure 데이터 공유에 대한 재해 복구

이 문서에서는 Azure 데이터 공유에 대 한 재해 복구 환경을 구성 하는 방법을 살펴보겠습니다. Azure 데이터 센터 중단은 드물지만 몇 분에서 몇 시간까지 지속될 수 있습니다. 데이터 센터 가동 중단으로 인해 데이터 공급자가 공유하는 데이터에 의존하는 환경이 중단될 수 있습니다. 이 문서에서 자세히 설명한 단계에 따라 데이터 공급자는 데이터 공유를 호스팅하는 기본 지역에 대한 데이터 센터 가동 중단이 발생할 경우 데이터 소비자와 데이터를 계속 공유할 수 있습니다. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Azure 데이터 공유에 대한 비즈니스 연속성 달성

데이터 센터 중단에 대비하기 위해 데이터 공급자는 보조 지역에 데이터 공유 환경을 프로비전할 수 있습니다. 데이터 센터 가동 중단이 발생할 경우 원활한 장애 조치를 보장하는 조치가 취해질 수 있습니다. 

데이터 공급자는 추가 리전에서 보조 Azure 데이터 공유 리소스를 프로비전할 수 있습니다. 이러한 데이터 공유 리소스는 기본 데이터 공유 환경에 있는 데이터 집합을 포함하도록 구성할 수 있습니다. DR 환경을 구성할 때 데이터 소비자를 데이터 공유에 추가하거나 나중에 추가할 수 있습니다(예: 수동 장애 조치 단계의 일부로 사용됩니다).

데이터 소비자가 DR 용으로 프로비전된 보조 환경에서 활성 공유 구독을 가지고 있는 경우 장애 조치의 일부로 스냅숏 일정을 활성화할 수 있습니다. 데이터 소비자가 DR 을 위해 보조 지역을 구독하지 않으려면 나중에 보조 데이터 공유에 초대할 수 있습니다. 

데이터 소비자는 DR 을 위해 유휴 상태인 활성 공유 구독을 가질 수 있거나 데이터 공급자는 수동 장애 조치 절차의 일부로 나중에 추가될 수 있습니다. 

## <a name="related-information"></a>관련 정보

- [비즈니스 연속성 및 재해 복구](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [BCDR 전략에 고가용성 구축](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.





---
title: 워크로드 관리
description: Azure Synapse 분석에서 워크로드 관리를 구현하기 위한 지침입니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 06fc9edd55aa51c985cbb981fc5a6892d0ca75e5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583116"
---
# <a name="what-is-workload-management"></a>워크로드 관리란?

혼합 워크로드를 실행하면 사용 중인 시스템에서 리소스 문제가 발생할 수 있습니다.  솔루션 아키텍트에서는 SLA에 도달할 수 있는 충분한 리소스가 있는지 확인하기 위해 기존 데이터 웨어하우징 활동(예: 데이터 로드, 변환 및 쿼리)을 분리하는 방법을 모색합니다.  

물리적 서버 격리로 인해 활용도가 낮거나 초과 예약되거나 캐시가 하드웨어 시작 및 중지로 항상 준비되는 상태의 인프라가 부족할 수 있습니다.  성공적인 워크로드 관리 체계는 리소스를 효과적으로 관리하고, 매우 효율적인 리소스 활용을 보장하며, 투자 수익(ROI)을 극대화합니다.

데이터 웨어하우스 워크로드는 데이터 웨어하우스와 관련하여 발생하는 모든 작업을 나타냅니다. 이러한 구성 요소의 깊이와 폭은 데이터 웨어하우스의 완성도 수준에 따라 달라집니다.  데이터 웨어하우스 워크로드에는 다음이 포함됩니다. 
- 웨어하우스에 데이터를 로드하는 전체 프로세스 
- 데이터 웨어하우스 분석 및 보고 수행
- 데이터 웨어하우스의 데이터 관리 
- 데이터 웨어하우스에서 데이터 내보내기

데이터 웨어하우스의 성능 용량은 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)에 의해 결정됩니다.
- 모든 성능 프로필에 할당된 리소스를 보려면 [메모리 및 동시성 제한을](memory-concurrency-limits.md)참조하십시오.
- 용량을 조정하려면 [확장 하거나 축소할](quickstart-scale-compute-portal.md)수 있습니다.


## <a name="workload-management-concepts"></a>워크로드 관리 개념

과거에는 Azure Synapse의 Synapse SQL 풀에서 리소스 [클래스를](resource-classes-for-workload-management.md)통해 쿼리 성능을 관리했습니다.  역할 멤버 자격에 따라 쿼리에 메모리를 할당할 수 있는 리소스 클래스입니다.  리소스 클래스의 주요 과제는 일단 구성되면 워크로드를 제어할 거버넌스나 기능이 없다는 것입니다.  

예를 들어 임시 사용자 역할 멤버 자격을 smallrc에 부여하면 해당 사용자가 시스템에서 메모리의 100%를 사용할 수 있습니다.  리소스 클래스에서는 중요한 워크로드에 리소스를 예약하고 사용할 수 있는지 확인할 수 없습니다.

Azure Synapse의 Synapse SQL 풀 워크로드 관리는 워크로드 [분류,](sql-data-warehouse-workload-classification.md) [워크로드 중요도](sql-data-warehouse-workload-importance.md) 및 [워크로드 격리의](sql-data-warehouse-workload-isolation.md)세 가지 상위 개념으로 구성됩니다.  이러한 기능을 통해 워크로드가 시스템 리소스를 활용하는 방법을 보다 세한 제어할 수 있습니다.

워크로드 분류는 워크로드 그룹에 요청을 할당하고 중요도 수준을 설정하는 개념입니다.  지금까지 이 할당은 [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class)사용하여 역할 구성원 자격을 통해 수행되었습니다.  이제 [워크로드 분류 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)를 통해 이 작업을 수행할 수 있습니다.  분류 기능은 레이블, 세션 및 요청을 분류하는 시간과 같은 다양한 옵션 집합을 제공합니다.

워크로드 중요도는 요청이 리소스에 액세스하는 순서에 영향을 미칩니다.  사용 중인 시스템에서 중요도가 높은 요청은 리소스에 먼저 액세스할 수 있습니다.  중요도는 잠금에 대한 정렬된 액세스를 보장할 수도 있습니다. 

워크로드 격리는 워크로드 그룹에 대한 리소스를 보유합니다.  워크로드 그룹에 예약된 리소스는 실행을 보장하기 위해 해당 워크로드 그룹에만 보관됩니다.  워크로드 그룹을 사용하면 리소스 클래스와 마찬가지로 요청당 할당된 리소스의 양을 정의할 수도 있습니다.  워크로드 그룹은 요청 집합이 사용할 수 있는 리소스의 양을 예약하거나 제한할 수 있는 기능을 제공합니다.  마지막으로 워크로드 그룹은 쿼리 시간 시간(예: 요청을)에 규칙을 적용하는 메커니즘입니다.  


## <a name="next-steps"></a>다음 단계

- 워크로드 분류에 대한 자세한 내용은 [워크로드 분류](sql-data-warehouse-workload-classification.md)를 참조하십시오.  
- 워크로드 격리에 대한 자세한 내용은 [워크로드 격리](sql-data-warehouse-workload-isolation.md)를 참조하십시오.  
- 워크로드 중요도에 대한 자세한 내용은 [워크로드 중요도](sql-data-warehouse-workload-importance.md)를 참조하십시오.  
- 워크로드 관리 모니터링에 대한 자세한 내용은 [워크로드 관리 포털 모니터링](sql-data-warehouse-workload-management-portal-monitor.md)을 참조하십시오.  

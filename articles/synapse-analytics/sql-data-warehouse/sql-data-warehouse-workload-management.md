---
title: 워크로드 관리
description: Azure Synapse Analytics에서 워크로드 관리를 구현하는 방법에 대한 지침입니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e54e0ed1a3292cee400774d02f61514f54370151
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208538"
---
# <a name="what-is-workload-management"></a>워크로드 관리란?

혼합된 워크로드를 실행하면 사용량이 많은 시스템에서 리소스 문제가 발생할 수 있습니다.  솔루션 설계자는 SLA를 충족하는 데 충분한 리소스가 있도록 보장하기 위해 클래식 데이터 웨어하우징 작업(예: 데이터 로드, 변환 및 쿼리)을 분리하는 방법을 찾습니다.  

물리적 서버 격리로 인해 인프라가 충분히 활용되지 않거나, 과도하게 예약되거나, 하드웨어를 시작하고 중지하면서 캐시를 지속적으로 준비하는 상태가 될 수 있습니다.  성공적인 워크로드 관리 체계는 리소스를 효과적으로 관리하고, 매우 효율적인 리소스 사용률을 보장하며, ROI(투자 수익률)를 극대화합니다.

데이터 웨어하우스 워크로드는 데이터 웨어하우스와 관련하여 수행되는 모든 작업을 나타냅니다. 이러한 구성 요소의 깊이와 범위는 데이터 웨어하우스의 완성도 수준에 따라 달라집니다.  데이터 웨어하우스 워크로드에는 다음이 포함됩니다.

- 데이터를 웨어하우스에 로드하는 전체 프로세스
- 데이터 웨어하우스 분석 및 보고 수행
- 데이터 웨어하우스에서 데이터 관리
- 데이터 웨어하우스에서 데이터 내보내기

데이터 웨어하우스의 성능 용량은 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)에 의해 결정됩니다.

- 모든 성능 프로필에 할당되는 리소스를 확인하려면 [메모리 및 동시성 제한](memory-concurrency-limits.md)을 참조하세요.
- 용량을 조정하려면 용량을 [확장 또는 축소](quickstart-scale-compute-portal.md)합니다.

## <a name="workload-management-concepts"></a>워크로드 관리 개념

이전에는 Azure Synapse의 Synapse SQL에서 [리소스 클래스](resource-classes-for-workload-management.md)를 통해 쿼리 성능을 관리했습니다.  이는 역할 멤버 자격에 따라 메모리를 쿼리에 할당할 수 있는 리소스 클래스입니다.  리소스 클래스에 대한 주요 과제는 구성된 후에는 워크로드를 제어할 수 있는 거버넌스 또는 기능이 없다는 것입니다.  

예를 들어 임시 사용자 역할 멤버 자격을 smallrc에 부여하면 해당 사용자가 시스템의 메모리를 100% 사용할 수 있습니다.  리소스 클래스를 사용하면 리소스를 예약하고 중요한 워크로드에 사용할 수 있도록 보장할 수 없습니다.

Azure Synapse의 Synapse SQL 풀 워크로드 관리는 [워크로드 분류](sql-data-warehouse-workload-classification.md), [워크로드 중요도](sql-data-warehouse-workload-importance.md) 및 [워크로드 격리](sql-data-warehouse-workload-isolation.md)의 세 가지 상위 수준 개념으로 구성됩니다.  이러한 기능을 통해 워크로드에서 시스템 리소스를 활용하는 방법을 더 효과적으로 제어할 수 있습니다.

워크로드 분류는 요청을 워크로드 그룹에 할당하고 중요도 수준을 설정하는 방법에 대한 개념입니다.  지금까지 이 할당은 [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class)를 사용하여 역할 멤버 자격을 통해 수행되었습니다.  이 작업은 이제 [CREATE WORKLOAD CLASSIFER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 통해 수행할 수 있습니다.  분류 기능은 레이블, 세션 및 요청을 분류하는 시간과 같은 다양한 옵션 세트를 제공합니다.

워크로드 중요도는 요청에서 리소스에 액세스하는 순서에 영향을 줍니다.  사용량이 많은 시스템에서 중요도가 높은 요청은 리소스에 먼저 액세스할 수 있습니다.  또한 중요도는 잠금에 대한 순차적 액세스를 보장할 수 있습니다.

워크로드 격리는 워크로드 그룹에 대한 리소스를 예약합니다.  워크로드 그룹에 예약된 리소스는 실행을 보장하기 위해 해당 워크로드 그룹 전용으로 유지됩니다.  또한 워크로드 그룹을 사용하면 리소스 클래스와 마찬가지로 요청당 할당되는 리소스의 양을 정의할 수 있습니다.  워크로드 그룹은 요청 세트에서 사용할 수 있는 리소스의 양을 예약하거나 제한할 수 있는 기능을 제공합니다.  마지막으로, 워크로드 그룹은 쿼리 시간 제한과 같은 규칙을 요청에 적용하는 메커니즘입니다.  

## <a name="next-steps"></a>다음 단계

- 워크로드 분류에 대한 자세한 내용은 [워크로드 분류](sql-data-warehouse-workload-classification.md)를 참조하세요.  
- 워크로드 격리에 대한 자세한 내용은 [워크로드 격리](sql-data-warehouse-workload-isolation.md)를 참조하세요.  
- 워크로드 중요도에 대한 자세한 내용은 [워크로드 중요도](sql-data-warehouse-workload-importance.md)를 참조하세요.  
- 워크로드 관리 모니터링에 대한 자세한 내용은 [워크로드 관리 포털 모니터링](sql-data-warehouse-workload-management-portal-monitor.md)을 참조하세요.  

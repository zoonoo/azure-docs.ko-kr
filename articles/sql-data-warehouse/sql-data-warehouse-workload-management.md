---
title: 작업 관리
description: Azure SQL Data Warehouse에서 작업 관리를 구현 하기 위한 지침입니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/13/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 287ad5467f9f3aac7eb8c9d7c19ea15c380c6879
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935405"
---
# <a name="what-is-workload-management"></a>워크로드 관리란?

혼합 워크 로드를 실행 하면 사용 중인 시스템에서 리소스 문제가 발생할 수 있습니다.  솔루션 설계자는 클래식 데이터 웨어하우징 활동 (예: 데이터 로드, 변환 및 쿼리)을 분리 하 여 Sla에 충분 한 리소스가 있는지 확인 하는 방법을 검색 합니다.  

물리적 서버 격리는 사용이 과다 하거나, 과도 하 게 예약 되어 있거나, 캐시가 지속적으로 하드웨어 시작 및 중지에 게이머 되는 상태에 있는 인프라의 포켓으로 이어질 수 있습니다.  성공적인 워크 로드 관리 체계는 효과적으로 리소스를 관리 하 고, 매우 효율적인 리소스 사용률을 보장 하 고, ROI (투자 수익률)를 극대화 합니다.

데이터 웨어하우스 작업은 데이터 웨어하우스와 관련 하 여 수행 되는 모든 작업을 나타냅니다. 이러한 구성 요소의 깊이와 범위는 데이터 웨어하우스의 완성도 수준에 따라 달라 집니다.  데이터 웨어하우스 워크 로드에는 다음이 포함 됩니다. 
- 웨어하우스에 데이터를 로드 하는 전체 프로세스 
- 데이터 웨어하우스 분석 및 보고 수행
- 데이터 웨어하우스의 데이터 관리 
- 데이터 웨어하우스에서 데이터 내보내기

데이터 웨어하우스의 성능 용량은 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)에 의해 결정됩니다.
- 모든 성능 프로필에 할당 된 리소스를 보려면 [메모리 및 동시성 제한](memory-concurrency-limits.md)을 참조 하세요.
- 용량을 조정 하려면 [확장 하거나 축소할](quickstart-scale-compute-portal.md)수 있습니다.


## <a name="workload-management-concepts"></a>워크 로드 관리 개념
이전에는 [리소스 클래스](resource-classes-for-workload-management.md)를 통해 SQL Data Warehouse에서 쿼리 성능을 관리 했습니다.  리소스 클래스는 역할 멤버 자격을 기반으로 쿼리에 메모리를 할당 하는 데 사용할 수 있습니다.  리소스 클래스에 대 한 주요 과제는 구성 된 후에는 작업을 제어 하는 거 버 넌 스 나 기능이 없다는 점입니다.  

예를 들어 smallrc에 임시 사용자 역할 멤버 자격을 부여 하면 해당 사용자가 시스템에서 100%의 메모리를 소비할 수 있습니다.  리소스 클래스를 사용 하면 예약 하 고 중요 한 작업에 리소스를 사용할 수 있는지 확인할 방법이 없습니다.

SQL Data Warehouse에 대 한 워크 로드 관리는 [작업 분류](sql-data-warehouse-workload-classification.md), [워크 로드 중요도](sql-data-warehouse-workload-importance.md) 및 [워크 로드 격리](sql-data-warehouse-workload-isolation.md)의 세 가지 상위 수준 개념으로 구성 됩니다.  이러한 기능을 통해 워크 로드에서 시스템 리소스를 활용 하는 방법을 보다 효과적으로 제어할 수 있습니다.

작업 분류는 작업 그룹에 요청을 할당 하 고 중요도 수준을 설정 하는 개념입니다.  지금까지이 할당은 [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class)를 사용 하 여 역할 멤버 자격을 통해 수행 되었습니다.  이제 [분류자 만들기 작업](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)을 통해이 작업을 수행할 수 있습니다.  분류 기능은 레이블, 세션, 요청을 분류 하는 시간 등 다양 한 옵션 집합을 제공 합니다.

작업 중요도는 요청에서 리소스에 액세스 하는 순서에 영향을 미칩니다.  사용 중인 시스템에서 중요도가 더 높은 요청은 리소스에 처음으로 액세스할 수 있습니다.  또한 중요도는 잠금에 대 한 순차적 액세스를 보장할 수 있습니다. 

작업 격리는 작업 그룹에 대 한 리소스를 예약 합니다.  작업 그룹에 예약 된 리소스는 실행을 위해 해당 작업 그룹에 대해서만 유지 됩니다.  작업 그룹을 사용 하 여 리소스 클래스 처럼 요청 별로 할당 된 리소스의 양을 정의할 수도 있습니다.  작업 그룹은 요청 집합에서 사용할 수 있는 리소스의 양을 예약 하거나 상한으로 지정할 수 있는 기능을 제공 합니다.  마지막으로 작업 그룹은 요청에 쿼리 제한 시간 등의 규칙을 적용 하는 메커니즘입니다.  


## <a name="next-steps"></a>다음 단계

- 작업 분류에 대 한 자세한 내용은 [작업 분류](sql-data-warehouse-workload-classification.md)를 참조 하세요.  
- 워크 로드 격리에 대 한 자세한 내용은 [워크 로드 격리](sql-data-warehouse-workload-isolation.md)를 참조 하세요.  
- 작업 중요도에 대 한 자세한 내용은 [워크 로드 중요도](sql-data-warehouse-workload-importance.md)를 참조 하세요.  
- 워크 로드 관리 모니터링에 대 한 자세한 내용은 [작업 관리 포털 모니터링](sql-data-warehouse-workload-management-portal-monitor.md)을 참조 하세요.  

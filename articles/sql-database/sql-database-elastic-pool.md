---
title: "탄력적 풀이란? 여러 SQL Database 관리 - Azure | Microsoft Docs"
description: "탄력적 풀을 사용하여 수백 및 수천 개의 SQL Database를 관리하고 크기를 조정합니다. 필요한 경우 배포할 수는 리소스에 대한 단일 가격"
keywords: "여러 데이터베이스, 데이터베이스 리소스, 데이터베이스 성능"
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 03/06/2017
ms.author: ddove
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 7490fe7261c760f2945d67a7f819091fd69b04f8
ms.lasthandoff: 04/15/2017


---

# <a name="how-elastic-pools-help-you-manage-and-scale-multiple-sql-databases"></a>탄력적 풀이 여러 SQL Database를 관리하고 크기를 조정하는 데 도움을 주는 방식

SQL Database 탄력적 풀은 사용 요구가 다양하고 예측하기 어려운 여러 데이터베이스를 관리하고 크기를 조정하기 위한 간단하고 비용 효과적인 솔루션입니다. 탄력적 풀의 데이터베이스는 단일 Azure SQL Database 서버에 있으며 설정된 가격으로 설정된 수의 리소스를 공유합니다.

[Azure Portal](sql-database-elastic-pool-manage-portal.md), [PowerShell](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), [C#](sql-database-elastic-pool-manage-csharp.md) 및 REST API를 사용하여 탄력적 풀을 만들고 관리할 수도 있습니다. 탄력적 풀 리소스는 [eDTU](sql-database-what-is-a-dtu.md)로 측정됩니다.


> [!NOTE]
> 탄력적 풀은 현재 미리 보기 상태인 인도 서부를 제외한 모든 Azure 지역에서 일반 공급(GA) 상태입니다.  이 영역에서 탄력적 풀의 GA는 가능한 한 빨리 수행될 예정입니다.
>
>


## <a name="how-do-elastic-pools-help-manage-database-resources"></a>탄력적 풀이 데이터베이스 리소스를 관리하는 데 어떻게 도움을 주나요?

일반적인 SaaS 응용 프로그램 패턴은 단일 테넌트 데이터베이스 모델이므로 각 고객(데이터베이스)에게 자체 데이터베이스가 제공됩니다. 각 고객에는 메모리, IO 및 CPU에 대한 예측할 수 없는 리소스 요구 사항이 있습니다. 이러한 요구의 최대치와 최저치를 사용하여 리소스를 효율적이면서 효과적으로 할당하는 방법은 무엇입니까? 일반적으로 두 가지 옵션, 즉 (1) 최대 사용량에 따른 리소스 오버프로비전과 과다 지불 또는 (2) 최대 사용 기간 동안 성능 및 고객 만족은 저하되지만 비용을 절감하는 언더프로비전이 있었습니다. 탄력적 풀은 데이터베이스가 필요한 성능 리소스를 필요할 때 얻도록 하여 이 문제를 해결합니다. 예측 가능한 예산 내에서 간단한 리소스 할당 메커니즘을 제공합니다. 탄력적 풀을 사용한 SaaS 응용 프로그램의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

SQL Database에서 리소스 수요를 처리하는 데이터베이스의 기능에 대한 상대 측정값은 단일 데이터베이스에 대한 DTU(데이터베이스 트랜잭션 단위) 및 탄력적 풀의 데이터베이스에 대한 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 표시됩니다. DTU 및 eDTU에 대한 자세한 내용은 [SQL 데이터베이스 소개](sql-database-technical-overview.md)를 참조하세요.

탄력적 풀에는 설정된 가격에 대한 설정된 수의 eDTU가 제공됩니다. 풀 내에서 개별 데이터베이스는 집합 매개 변수 내에서 자동 크기 조정에 유연성이 제공됩니다. 부하가 높은 상태에서 데이터베이스는 더 많은 eDTU를 사용하여 수요를 충족할 수 있습니다. 낮은 부하량에서 데이터베이스는 적게 사용하고 부하가 없는 데이터베이스는 eDTU를 사용하지 않습니다. 단일 데이터베이스가 아닌 전체 풀에 대한 리소스 프로비저닝은 관리 작업을 간소화합니다. 또한 풀에 대한 예측 가능한 예산이 있습니다.

새 eDTU 예약에 대한 추가 계산 리소스를 제공하기 위해 데이터베이스를 이동해야 한다는 점을 제외하면 데이터베이스 가동 중지 시간을 발생시키지 않고 추가 eDTU를 기존 풀에 추가할 수 있습니다. 마찬가지로, 추가 eDTU가 더 이상 필요하지 않은 경우에는 언제든지 기존 풀에서 제거할 수 있습니다.

풀에 데이터베이스를 추가하거나 뺄 수 있습니다. 데이터베이스가 예측 가능한 방식으로 리소스를 사용하는 경우 리소스를 이동합니다.

## <a name="which-databases-go-in-a-pool"></a>어떤 데이터베이스를 풀로 이동해야 합니까?
![탄력적 풀에서 eDTU를 공유하는 SQL 데이터베이스][1]

탄력적 풀을 사용하기에 좋은 데이터베이스에는 일반적으로 활동하는 시기와 활동이 없는 시기가 있습니다. 위의 예에서 단일 데이터베이스, 4개의 데이터베이스, 마지막으로 20개의 데이터베이스가 포함된 탄력적 풀의 활동을 볼 수 있습니다. 시간에 따라 활동 수준이 달라지는 데이터베이스는 동시에 모두 활성화되지 않아 eDTU를 공유할 수 있으므로 탄력적 풀에 잘 어울립니다. 일부 데이터베이스는 이러한 패턴에 맞지 않습니다. 더 지속적으로 리소스를 요구하는 데이터베이스는 리소스가 개별적으로 할당되는 Basic, Standard, Premium 및 Premium RS 서비스 계층에 더 적합합니다.

[탄력적 풀의 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)

## <a name="edtu-and-storage-limits-for-elastic-pools"></a>탄력적 풀에 대한 eDTU 및 저장소 제한

다음 표에서는 Basic, Standard, Premium 및 Premium RS 탄력적 풀의 특징을 설명합니다.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

탄력적 풀의 모든 DTU가 사용되었다면 풀에 있는 각 데이터베이스는 쿼리를 처리할 같은 크기의 리소스를 받습니다.  SQL 데이터베이스 서비스는 같은 분량의 계산 시간을 보장하여 데이터베이스 간의 공정성을 공유할 리소스를 제공합니다. 탄력적 풀 리소스 공유 공정성은 데이터베이스당 DTU 최소값이 0이 아닌 값으로 설정될 때 각 데이터베이스에 보장된 리소스에 적용됩니다.

## <a name="elastic-pool-properties"></a>탄력적 풀 속성

다음 테이블에서는 탄력적 풀 및 풀링된 데이터베이스에 대한 제한을 설명합니다.

### <a name="limits-for-elastic-pools"></a>탄력적 풀에 대한 제한
| 속성 | 설명 |
|:--- |:--- |
| 서비스 계층 |Basic, Standard, Premium, Premium RS가 있습니다. 서비스 계층에 따라 구성 가능한 성능 및 저장소 제한 범위와 비즈니스 연속성 선택 항목이 결정됩니다. 풀 내의 모든 데이터베이스는 풀과 동일한 서비스 계층을 가집니다. "서비스 계층"을 "에디션"이라고도 합니다. |
| 풀당 eDTU |풀의 데이터베이스에서 공유할 수 있는 최대 eDTU 수입니다. 풀의 데이터베이스에서 사용하는 총 eDTU는 동일한 시점에 이 한도를 초과할 수 없습니다. |
| 풀당 최대 저장소(GB) |풀의 데이터베이스에서 공유할 수 있는 최대 저장소 공간(GB)입니다. 풀의 모든 데이터베이스에서 사용하는 총 저장소는 이 한도를 초과할 수 없습니다. 이 한도는 풀당 eDTU에 따라 결정됩니다. 이 한도를 초과하면 모든 데이터베이스가 읽기 전용이 됩니다. 풀에 있는 데이터 파일의 최대 저장소를 의미하는 풀당 최대 저장소는 로그 파일에서 사용하는 공간을 포함하지 않습니다. |
| 풀당 최대 데이터베이스 수 |풀당 허용되는 최대 데이터베이스 수입니다. |
| 풀당 최대 동시 작업자 |풀에서 모든 데이터베이스에 대해 사용할 수 있는 최대 동시 작업자(요청) 수입니다. |
| 풀당 최대 동시 로그인 |풀에서 모든 데이터베이스에 대한 최대 동시 로그인 수입니다. |
| 풀당 최대 동시 세션 |풀에서 모든 데이터베이스에 대해 사용할 수 있는 최대 세션 수입니다. |
|||

### <a name="limits-for-pooled-databases"></a>풀링된 데이터베이스에 대한 제한
| 속성 | 설명 |
|:--- |:--- |
| 데이터베이스당 최대 eDTU |풀에 있는 다른 데이터베이스의 사용률에 따라 사용 가능한 경우 풀에 있는 임의 데이터베이스에서 사용할 수 있는 최대 eDTU 수입니다.  데이터베이스당 최대 eDTU는 데이터베이스에 대해 리소스를 보장하지 않습니다.  풀에 있는 모든 데이터베이스에 적용되는 전역 설정입니다. 데이터베이스 사용률의 최대치를 처리할 만큼 데이터베이스당 최대 eDTU를 충분히 높게 설정합니다. 풀은 일반적으로 모든 데이터베이스가 동시에 최대로 사용되지 않을 경우 데이터베이스에 대해 핫 및 콜드 사용률 패턴을 가정하므로 일정 수준의 오버커밋이 예상됩니다. 예를 들어, 데이터베이스당 최고 사용률이 20 eDTU이며 풀에 있는 100개의 데이터베이스 중 20%만 동시에 최대로 사용되는 것으로 가정합니다.  데이터베이스당 eDTU 최대값이 20 eDTU로 설정된 경우 풀을 5배만큼 오버커밋하고 풀당 eDTU를 400으로 설정하는 것이 적합합니다. |
| 데이터베이스당 최소 eDTU |풀에 있는 임의 데이터베이스에 보장되는 최소 eDTU 수입니다.  풀에 있는 모든 데이터베이스에 적용되는 전역 설정입니다. 데이터베이스당 최소 eDTU를 0으로 설정할 수 있으며 기본값이기도 합니다. 이 속성은 0과 데이터베이스당 평균 기록 eDTU 사용률 사이의 값으로 설정됩니다. 풀에 있는 데이터베이스 수와 데이터베이스당 최소 eDTU를 곱한 값은 풀당 eDTU를 초과할 수 없습니다.  예를 들어, 풀에 20개의 데이터베이스가 있고 데이터베이스당 eDTU 최소값이 10 eDTU로 설정되면 풀당 eDTU는 200 eDTU 이상이어야 합니다. |
| 데이터베이스당 최대 저장소(GB) |풀에 있는 데이터베이스에 대한 최대 저장소입니다. 풀링된 데이터베이스는 풀 저장소를 공유하므로 데이터베이스 저장소는 남은 풀 저장소와 데이터베이스당 최대 저장소 중 작은 값으로 제한됩니다. 데이터 파일의 최대 저장소를 의미하는 데이터베이스당 최대 저장소는 로그 파일에서 사용하는 공간을 포함하지 않습니다. |
|||

## <a name="elastic-jobs"></a>탄력적 작업
풀을 통해 **[탄력적 작업](sql-database-elastic-jobs-overview.md)**의 스크립트를 실행하여 관리 작업이 간소화됩니다. 탄력적 작업은 많은 수의 데이터베이스와 관련된 번거로움을 대부분 없애 줍니다. 시작하려면 [탄력적 작업 시작](sql-database-elastic-jobs-getting-started.md)을 참조하세요.

여러 데이터베이스를 사용하기 위한 다른 데이터베이스 도구에 대한 자세한 내용은 [Azure SQL Database를 사용하여 확장](sql-database-elastic-scale-introduction.md)을 참조합니다.

## <a name="business-continuity-features-for-databases-in-a-pool"></a>풀의 데이터베이스를 위한 비즈니스 연속성 기능
풀링된 데이터베이스는 일반적으로 단일 데이터베이스에서 사용할 수 있는 동일한 [비즈니스 연속성 기능](sql-database-business-continuity.md)을 지원합니다.

### <a name="point-in-time-restore"></a>특정 시점 복원
특정 시점 복원은 자동 데이터베이스 백업을 사용하여 풀에 있는 데이터베이스를 특정 시점으로 복원합니다. [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>지역 복원
지역 복원은 데이터베이스가 호스팅되는 지역에 사고가 발생하여 데이터베이스를 사용할 수 없게 되었을 때를 위한 기본 복구 옵션을 제공합니다. [Azure SQL 데이터베이스 복원 또는 보조 데이터베이스에 대한 장애 조치](sql-database-disaster-recovery.md)

### <a name="active-geo-replication"></a>활성 지역 복제
지역 복원에서 제공하는 것보다 더 까다로운 복구 요구 사항이 있는 응용 프로그램의 경우 [활성 지역 복제](sql-database-geo-replication-overview.md)를 구성합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](sql-database-elastic-pool-manage-portal.md), [PowerShell](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), [C#](sql-database-elastic-pool-manage-csharp.md) 및 REST API를 사용하여 탄력적 풀을 만들고 관리할 수도 있습니다.
* 탄력적 풀을 사용할 때의 지침은 [탄력적 풀 지침](sql-database-elastic-pool-guidance.md)을 참조하세요.
* 비디오는 [Azure SQL Database 탄력적 기능에 대한 Microsoft Virtual Academy 비디오 과정](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)을 참조하세요.

<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png


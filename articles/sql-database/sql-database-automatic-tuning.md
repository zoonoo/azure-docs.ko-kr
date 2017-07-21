---
title: "SQL Database - 자동 조정 | Microsoft Docs"
description: "SQL Database는 SQL 쿼리를 분석하고 사용자 워크로드에 자동으로 적용됩니다."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: jovanpop
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: f5552793db2d89542782b7d9e8f996314f62e429
ms.contentlocale: ko-kr
ms.lasthandoff: 06/17/2017


---
# <a name="automatic-tuning"></a>자동 조정

Azure SQL Database는 데이터베이스에서 실행되는 쿼리를 모니터링하는 완전히 관리되는 데이터 서비스이며 자동으로 작업의 성능을 향상시킬 수 있습니다. Azure SQL Database에는 데이터베이스를 작업에 동적으로 적용하여 자동으로 조정하고 쿼리의 성능을 향상시킬 수 있는 기본 제공 인텔리전스 메커니즘이 있습니다. Azure SQL Database의 자동 조정은 쿼리의 성능을 최적화하기 위해 Azure SQL Database에서 사용할 수는 가장 중요한 기능 중 하나일 수 있습니다.

Azure Portal을 사용하여 [자동 조정을 사용](sql-database-automatic-tuning-enable.md)하는 단계는 이 문서를 참조하세요.

## <a name="why-automatic-tuning"></a>자동 조정을 사용하는 이유는 무엇입니까?

클래식 데이터베이스 관리의 주요 작업 중 하나는 중요한 SQL 쿼리, 성능 향상을 위해 추가되어야 하는 인덱스 및 드물게 사용되는 인덱스를 식별하여 작업을 모니터링하는 것입니다. Azure SQL Database는 모니터링해야 하는 쿼리 및 인덱스에 대한 자세한 정보를 제공합니다. 그러나 지속적으로 데이터베이스를 모니터링하는 것은 특히 많은 데이터베이스를 처리할 때 힘들고 지루한 작업입니다. Azure SQL Database 및 Azure Portal에서 제공하는 모든 사용 가능한 도구 및 보고서를 사용하더라도 상당히 많은 데이터베이스를 효율적으로 관리하기 어려울 수 있습니다. 수동으로 데이터베이스를 모니터링하고 조정하는 대신 자동 조정 기능을 사용하여 Azure SQL Database에 대한 모니터링 및 조정 작업을 위임하도록 고려할 수 있습니다. 


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="how-does-automatic-tuning-work"></a>자동 조정의 작동 방식은 무엇입니까?

Azure SQL Database에는 지속적으로 워크로드의 특징을 학습하고 잠재적인 문제 및 향상된 기능을 식별하는 지속적인 성능 모니터링 및 분석 프로세스가 있습니다.

![자동 조정 프로세스](./media/sql-database-automatic-tuning/tuning-process.png)

이 프로세스를 통해 Azure SQL Database는 작업의 성능을 향상시킬 수 있는 인덱스 및 작업을 찾고 작업에 영향을 주는 인덱스를 찾아 작업에 동적으로 적용할 수 있습니다. 이러한 결과에 따라 자동 조정은 작업의 성능을 향상시키는 조정 작업을 적용합니다. 또한 Azure SQL Database는 자동 조정에서 변경 사항이 만들어진 후 작업의 성능을 향상시키는지 확인하도록 지속적으로 성능을 모니터링합니다. 성능을 향상시키지 않은 모든 작업은 자동으로 되돌려집니다. 이 확인 프로세스는 자동 조정에서 만든 변경 사항이 작업의 성능을 저하시키지 않도록 보장하는 주요 기능입니다.

Azure SQL Database에서 사용할 수 있는 두 가지 자동 조정 측면이 있습니다.

 -  데이터베이스에 추가되어야 하는 인덱스 및 제거되어야 하는 인덱스를 식별하는 **자동 인덱스 관리**
 -  문제가 있는 계획을 식별하고 SQL 계획 성능 문제를 해결하는 **자동 계획 수정**(출시 예정, SQL Server 2017에서 이미 사용 가능)

## <a name="automatic-index-management"></a>자동 인덱스 관리

Azure SQL Database에서 인덱스 관리는 Azure SQL Database에서 작업에 대해 학습하고 데이터가 항상 최적으로 인덱싱되도록 보장하기 때문에 매우 간단합니다. 적절한 인덱스 디자인은 작업의 최적의 성능을 위해 중요하고 자동 인덱스 관리는 인덱스를 최적화하는 데 도움이 될 수 있습니다. 자동 인덱스 관리는 올바르지 않게 인덱싱된 데이터베이스의 성능 문제를 해결하거나 기존 데이터베이스 스키마에 대한 인덱스를 유지 관리하고 향상시킬 수 있습니다. 

### <a name="why-do-you-need-index-management"></a>인덱스 관리가 필요한 이유는 무엇입니까?

인덱스는 테이블에서 데이터를 읽는 일부 쿼리의 속도를 높이지만 데이터를 업데이트하는 쿼리의 속도가 저하될 수 있습니다. 인덱스를 만들 시기와 인덱스에 포함해야 하는 열을 신중하게 분석해야 합니다. 일부 인덱스는 일정 시간이 지난 후 필요하지 않을 수 있습니다. 따라서 이점을 가져오지 않는 인덱스를 정기적으로 식별하고 삭제해야 합니다. 사용되지 않는 인덱스를 무시하는 경우 데이터를 읽는 쿼리에 대한 아무런 이점 없이 데이터를 업데이트하는 쿼리의 성능이 저하될 것입니다. 추가 업데이트에는 불필요한 로깅이 필요하기 때문에 사용되지 않는 인덱스도 시스템의 전반적인 성능에 영향을 줍니다.

테이블에서 데이터를 읽는 쿼리의 성능을 개선하고 업데이트에 최소의 영향을 주는 인덱스의 최적 집합을 찾는 데 지속적이고 복잡한 분석이 필요할 수 있습니다.

Azure SQL Database는 쿼리를 분석하고 현재 작업에 최적인 인덱스 및 제거될 수 있는 인덱스를 식별하는 기본 제공 인텔리전스, 고급 규칙을 사용합니다. Azure SQL Database는 다른 쿼리에 미치는 영향을 최소화하여 데이터를 읽는 쿼리를 최적화하는 최소한으로 필요한 인덱스 집합을 갖도록 합니다.

### <a name="how-to-identify-indexes-that-need-to-be-changed-in-your-database"></a>데이터베이스에서 변경되어야 하는 인덱스를 식별하는 방법은 무엇입니까?

Azure SQL Database는 인덱스 관리 프로세스를 쉽게 해줍니다. 수동 작업 분석 및 인덱스 모니터링의 번거로운 프로세스 대신 Azure SQL Database는 해당 작업을 분석하고 새 인덱스를 사용하여 더 빠르게 실행될 수 있는 쿼리를 식별하고 사용되지 않거나 중복된 인덱스를 식별합니다. [Azure Portal에서 인덱스 찾기 권장 사항](sql-database-advisor-portal.md)에서 변경되어야 하는 인덱스의 ID에 대한 자세한 정보를 찾아보세요.

### <a name="automatic-index-management-considerations"></a>자동 인덱스 관리 고려 사항

기본 제공 규칙이 데이터베이스의 성능을 향상시키는 것을 발견하는 경우 Azure SQL Database에서 자동으로 인덱스를 관리하도록 할 수도 있습니다.

Azure SQL Databases에서 필요한 인덱스를 만드는 데 필요한 작업은 리소스를 소비하고 일시적으로 작업 성능에 영향을 줄 수 있습니다. 인덱스 생성이 작업 성능에 미치는 영향을 최소화하기 위해 Azure SQL Database는 인덱스 관리 작업에 대한 적절한 기간을 찾습니다. 데이터베이스가 작업을 실행하기 위해 리소스가 필요한 경우 조정 작업은 지연되고 데이터베이스에 유지 관리 작업에 사용될 수 있는 사용되지 않는 충분한 리소스가 있는 경우 시작됩니다. 자동 인덱스 관리에서 중요한 기능 중 하나는 작업의 확인입니다. Azure SQL Database에서 인덱스를 만들거나 삭제할 때 모니터링 프로세스는 작업이 성능을 향상시켰는지 확인하기 위해 작업의 성능을 분석합니다. 큰 성능 향상을 가져오지 않은 경우 작업은 즉시 되돌려집니다. 이러한 방식으로 Azure SQL Database는 자동 작업이 작업 성능에 부정적인 영향을 주지 않도록 합니다. 자동 조정으로 만든 인덱스는 기본 스키마에서 유지 관리 작업에 대해 투명합니다. 열 삭제 또는 이름 바꾸기와 같은 스키마 변경 내용은 자동으로 만들어진 인덱스에 의해 차단되지 않습니다. Azure SQL Database에서 자동으로 생성된 인덱스는 관련 테이블 또는 열이 삭제될 때 즉시 삭제됩니다.

## <a name="automatic-plan-choice-correction"></a>자동 계획 선택 수정

자동 계획 수정은 오동작하는 SQL 계획을 식별하는 SQL Server 2017 CTP2.0에 추가된 새 자동 조정 기능입니다. 이 자동 조정 옵션은 Azure SQL Database에서 곧 제공됩니다. [SQL Server 2017에서 자동 조정](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)에서 자세한 정보를 찾아보세요.

## <a name="next-steps"></a>다음 단계

- Azure SQL Database에서 자동 조정을 사용하도록 설정하고 자동 조정 기능으로 작업을 완벽하게 관리하려면 [자동 조정 사용](sql-database-automatic-tuning-enable.md)을 참조하세요.
- 수동 조정을 사용하려면 [Azure Portal에서 조정 권장 사항](sql-database-advisor-portal.md)을 검토하고 쿼리의 성능을 개선하는 권장 사항을 직접 적용합니다.


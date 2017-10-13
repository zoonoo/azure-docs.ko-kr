---
title: "Azure SQL Database - 자동 조정 | Microsoft Docs"
description: "Azure SQL Database는 SQL 쿼리를 분석하고 사용자 워크로드에 자동으로 적용됩니다."
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
ms.date: 09/19/2017
ms.author: jovanpop
ms.openlocfilehash: 64abcf680199938c54be5005ee96670a8e580270
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL Database에서 자동 조정

Azure SQL Database는 데이터베이스에서 실행되는 쿼리를 모니터링하는 완전히 관리되는 데이터 서비스이며 데이터베이스 워크로드의 성능을 자동으로 향상시킬 수 있습니다. Azure SQL Database에는 데이터베이스를 워크로드에 동적으로 적용하여 자동으로 조정하고 쿼리의 성능을 향상시킬 수 있는 기본 제공 [자동 조정](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) 인텔리전스 메커니즘이 있습니다. Azure SQL Database의 자동 조정은 쿼리의 성능을 최적화하기 위해 Azure SQL Database에서 사용할 수는 가장 중요한 기능 중 하나일 수 있습니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="automatic-tuning-options"></a>자동 조정 옵션

Azure SQL Database에서 사용 가능한 [자동 조정](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) 옵션은 다음과 같습니다.
 1. **CREATE INDEX**: 워크로드의 성능을 향상시킬 수 있는 인덱스를 식별하고, 인덱스를 만들며 쿼리의 성능 향상을 확인합니다.
 2. **DROP INDEX**: 중복 및 복제 인덱스와 오랜 기간 사용되지 않은 인덱스를 식별합니다.
 3. **PLAN REGRESSION CORRECTION**: 이전 적합한 계획보다 느린 실행 계획을 사용하는 SQL 쿼리를 식별하고 회귀된 계획 대신 마지막으로 알려진 적합한 계획을 사용합니다.

Azure SQL Database는 데이터베이스를 최적화하고 Azure Portal에 표시할 수 있는 **CREATE INDEX**, **DROP INDEX** 및 **PLAN REGRESSION CORRECTION** 권장 사항을 식별합니다. [Azure Portal에서 인덱스 찾기 권장 사항](sql-database-advisor-portal.md)에서 변경되어야 하는 인덱스의 ID에 대한 자세한 정보를 찾아보세요. 포털을 사용하여 수동으로 권장 사항을 적용하거나 Azure SQL Database에서 권장 사항을 자동으로 적용하고 변경 후 워크로드를 모니터링하며 권장 사항으로 워크로드의 성능이 향상되었는지 확인할 수 있습니다.

자동 조정 옵션은 데이터베이스 별로 별도로 설정 또는 해제하거나 논리 서버에서 구성하고 서버에서 설정을 상속하는 모든 데이터베이스에서 적용할 수 있습니다. 서버에서 [자동 조정](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) 옵션을 구성하고 서버의 데이터베이스에서 설정을 상속하면 많은 수의 데이터베이스에서 자동 조정 옵션의 관리를 단순화할 수 있으므로 자동 조정 구성에 권장되는 방법입니다.

Azure Portal을 사용하여 [자동 조정을 사용](sql-database-automatic-tuning-enable.md)하는 단계는 이 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure SQL Database에서 자동 조정을 사용하도록 설정하고 자동 조정 기능으로 작업을 완벽하게 관리하려면 [자동 조정 사용](sql-database-automatic-tuning-enable.md)을 참조하세요.
- 수동 조정을 사용하려면 [Azure Portal에서 조정 권장 사항](sql-database-advisor-portal.md)을 검토하고 쿼리의 성능을 개선하는 권장 사항을 직접 적용합니다.
- [Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)를 조정하는 기본 제공 인텔리전스에 대해 자세히 알아보세요.
- Azure SQL Database 및 SQL Server 2017에서 [자동 조정](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)에 대해 자세히 알아보세요.

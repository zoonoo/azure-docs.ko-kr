---
title: "Azure SQL Database 성능 Insight | Microsoft Docs"
description: "Azure SQL 데이터베이스는 현재 쿼리 성능을 향상시킬 수 있는 영역을 식별하는 데 도움이 되는 성능 도구를 제공합니다."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: a60b75ac-cf27-4d73-8322-ee4d4c448aa2
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 071cc504cc4111e20391af86ffdc7819644ec434


---
# <a name="sql-database-performance-insight"></a>SQL 데이터베이스 성능 Insight
Azure SQL 데이터베이스는 인텔리전트 튜닝 작업 및 권장 사항을 제공하여 데이터베이스의 성능을 파악하고 향상시키는 데 도움이 되는 성능 도구를 제공합니다. 

1. [Azure Portal](http://portal.azure.com)에서 데이터베이스를 찾아 **모든 설정** > **성능** > **개요**를 클릭하면 **성능** 페이지가 열립니다. 
2. [SQL Database 관리자](#sql-database-advisor)를 열려면 **권장 사항**을 클릭하고 [Query Performance Insight](#query-performance-insight)를 열려면 **쿼리**를 클릭합니다.
   
    ![성능 보기](./media/sql-database-performance/entries.png)

## <a name="performance-overview"></a>성능 개요
**개요** 또는 **성능** 타일을 클릭하면 데이터베이스에 대한 성능 대시보드로 이동합니다. 이 보기에서는 데이터베이스 성능에 대한 요약을 제공하여 성능 튜닝 및 문제 해결에 도움이 됩니다. 

![성능](./media/sql-database-performance/performance.png)

* **권장 사항** 타일은 데이터베이스에 대한 튜닝 권장 사항을 분석하여 제공합니다(표시되는 내용이 많은 경우 상위 3가지 권장 사항이 표시됨). 이 타일을 클릭하면 **SQL 데이터베이스 관리자**로 이동합니다. 
* **튜닝 활동** 타일은 데이터베이스에 대해 진행 중이고 완료된 튜닝 작업을 제공하여 튜닝 활동의 기록을 간략히 볼 수 있습니다. 이 타일을 클릭하면 데이터베이스에 대한 전체 튜닝 기록 보기로 이동합니다.
* **자동 튜닝** 타일은 데이터베이스에 대한 자동 튜닝 구성을 보여 줍니다(데이터베이스에 자동으로 적용되도록 구성되는 튜닝 작업). 이 타일을 클릭하면 자동화 구성 대화 상자가 열립니다.
* **데이터베이스 쿼리** 타일은 데이터베이스에 대한 쿼리 성능 요약을 보여 줍니다(전체 DTU 사용량 및 최상위 리소스 사용 쿼리). 이 타일을 클릭하면 **Query Performance Insight**로 이동합니다.

## <a name="sql-database-advisor"></a>SQL 데이터베이스 관리자
[SQL 데이터베이스 관리자](sql-database-advisor.md) 는 데이터베이스의 성능 향상에 도움이 될 수 있는 인텔리전트 튜닝 권장 사항을 제공합니다. 

* 생성 또는 삭제할 인덱스에 대한 권장 사항(및 사용자 상호 작용 없이 인덱스 권장 사항을 자동으로 적용하고 성능에 부정적인 영향을 주는 권장 사항을 자동으로 롤백하는 옵션).
* 데이터베이스에서 스키마 문제가 확인될 때 권장 사항.
* 쿼리가 매개 변수화된 쿼리에서 이점을 얻을 수 있는 경우 권장 사항.

## <a name="query-performance-insight"></a>쿼리
[Query Performance Insight](sql-database-query-performance.md) 를 통해 다음을 제공하여 데이터베이스 성능 문제 해결 시간을 줄일 수 있습니다.

* 데이터베이스 리소스(DTU) 사용에 대한 보다 자세한 정보를 확인합니다. 
* 최상위 CPU 사용 쿼리는 향상된 성능을 위해 잠재적으로 조정될 수 있습니다. 
* 쿼리에 대한 세부 정보로 드릴다운할 수 있습니다. 

## <a name="additional-resources"></a>추가 리소스
* [단일 데이터베이스의 Azure SQL 데이터베이스 성능 지침](sql-database-performance-guidance.md)
* [탄력적 풀을 사용해야 하는 경우](sql-database-elastic-pool-guidance.md)




<!--HONumber=Dec16_HO2-->



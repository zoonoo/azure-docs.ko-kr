---
title: 성능 모니터링 및 향상
description: Azure SQL Database는 현재 쿼리 성능을 향상시킬 수 있는 영역을 식별하는 데 도움이 되는 성능 도구를 제공합니다.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 11/14/2019
ms.openlocfilehash: 474c2f4f00374ce785b81fe048e11cb353b3078a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151242"
---
# <a name="monitor-and-improve-performance"></a>성능 모니터링 및 향상

Azure SQL Database는 데이터베이스의 잠재적 문제를 파악하고 지능형 튜닝 작업 및 권장 사항을 제공하여 워크로드 성능을 높일 수 있는 작업을 권장합니다.

## <a name="performance-tuning-options"></a>성능 조정 옵션

Azure SQL Database 사용할 수 있는 성능 튜닝 옵션은 데이터베이스 탐색 메뉴의 "지능형 성능"에서 사용할 수 있습니다.

| 성능 조정 옵션 | 단일 데이터베이스 및 풀링된 데이터베이스 지원 | 인스턴스 데이터베이스 지원 |
| :----------------------------- | ----- | ----- |
| **성능 개요** -데이터베이스의 모든 성능 작업을 모니터링 합니다. | 예 | 아니오 | 
| **성능 권장 사항** -워크 로드의 성능을 향상 시킬 수 있는 성능 권장 사항을 표시 합니다. | 예 | 아니오 | 
| **Query Performance Insight** -데이터베이스에서 가장 많이 사용 되는 쿼리의 성능을 보여 줍니다. | 예 | 아니오 | 
| **자동 튜닝** -Azure SQL Database를 사용 하 여 데이터베이스 성능을 자동으로 최적화 합니다. | 예 | 아니오 | 

## <a name="performance-overview"></a>성능 개요

이 보기에서는 데이터베이스 성능에 대한 요약을 제공하여 성능 튜닝 및 문제 해결에 도움이 됩니다. 

![Azure SQL Database에 대 한 성능 개요](./media/sql-database-performance/performance-overview-annotated.png)

* **권장 사항** 타일은 데이터베이스에 대한 튜닝 권장 사항을 분석하여 제공합니다(표시되는 내용이 많은 경우 상위 3가지 권장 사항이 표시됨). 이 타일을 클릭하면  **[성능 권장 사항](#performance-recommendations)** 으로 이동합니다. 
* **튜닝 활동** 타일은 데이터베이스에 대해 진행 중이고 완료된 튜닝 작업을 제공하여 튜닝 활동의 기록을 간략히 볼 수 있습니다. 이 타일을 클릭하면 데이터베이스에 대한 전체 튜닝 기록 보기로 이동합니다.
* **자동 튜닝** 타일은 데이터베이스에 대한 [자동 튜닝 구성](sql-database-automatic-tuning-enable.md)을 보여 줍니다(데이터베이스에 자동으로 적용되는 튜닝 옵션). 이 타일을 클릭하면 자동화 구성 대화 상자가 열립니다.
* **데이터베이스 쿼리** 타일은 데이터베이스에 대한 쿼리 성능 요약을 보여 줍니다(전체 DTU 사용량 및 최상위 리소스 사용 쿼리). 이 타일을 클릭하면 **[Query Performance Insight](#query-performance-insight)** 로 이동합니다.

## <a name="performance-recommendations"></a>성능 권장 사항

이 페이지는 데이터베이스의 성능을 향상시킬 수 있는 지능형 [튜닝 권장 사항](sql-database-advisor.md)을 제공합니다. 다음과 같은 유형의 권장 사항이 이 페이지에 표시됩니다.

* 만들거나 삭제할 인덱스에 대한 권장 사항.
* 데이터베이스에서 스키마 문제가 확인될 때 권장 사항.
* 쿼리가 매개 변수화된 쿼리에서 이점을 얻을 수 있는 경우 권장 사항.

![Azure SQL Database에 대 한 성능 권장 사항](./media/sql-database-performance/performance-recommendations-annotated.png)

또한 이전에 적용되었던 튜닝 작업의 전체 기록을 찾을 수도 있습니다.

[성능 권장 사항 찾기 및 적용](sql-database-advisor-portal.md) 문서에서 성능 권장 사항을 찾아 적용하는 방법을 알아보세요.

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) 를 통해 다음을 제공하여 데이터베이스 성능 문제 해결 시간을 줄일 수 있습니다.

* 데이터베이스 리소스(DTU) 사용에 대한 보다 자세한 정보를 확인합니다. 
* 최상위 CPU 사용 쿼리는 향상된 성능을 위해 잠재적으로 조정될 수 있습니다. 
* 쿼리에 대한 세부 정보로 드릴다운할 수 있습니다. 

  ![Azure SQL Database에 대 한 쿼리 성능 정보](./media/sql-database-performance/query-performance-insights-annotated.png)

**[Query Performance Insight 사용 방법](sql-database-query-performance.md)** 문서에서 이 페이지에 대한 자세한 내용을 알아보세요.

## <a name="automatic-tuning"></a>자동 조정

Azure SQL 데이터베이스는 자동으로 [성능 권장 사항](sql-database-advisor.md)을 적용하여 데이터베이스 성능을 튜닝할 수 있습니다. 이 기능을 사용하려면 [자동 튜닝 사용 방법](sql-database-automatic-tuning-enable.md)을 읽어보세요.

  ![Azure SQL Database 자동 조정](./media/sql-database-performance/automatic-tuning-annotated.png)

자세한 내용은 [자동 튜닝 문서](sql-database-automatic-tuning.md)를 읽어보세요.

## <a name="additional-resources"></a>추가 리소스

* [단일 데이터베이스의 Azure SQL Database 성능 지침](sql-database-performance-guidance.md)
* [탄력적 풀을 사용해야 하는 경우](sql-database-elastic-pool-guidance.md)

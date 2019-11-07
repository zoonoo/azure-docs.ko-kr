---
title: 워크로드 중요도 구성
description: 요청 수준 중요도를 설정 하는 방법에 대해 알아봅니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 59ba4b936f6098b0d0b3f5e571f107af088206e0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692700"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에서 작업 중요도 구성

SQL Data Warehouse 중요도를 설정 하면 쿼리 예약에 영향을 줄 수 있습니다. 중요도가 더 높은 쿼리는 중요도가 낮은 쿼리 보다 먼저 실행 되도록 예약 됩니다. 쿼리에 중요도를 할당 하려면 작업 분류자를 만들어야 합니다.

## <a name="create-a-workload-classifier-with-importance"></a>중요도가 있는 작업 분류자 만들기

데이터 웨어하우스 시나리오에서는 쿼리가 신속 하 게 실행 되어야 하는 사용자가 있을 수 있습니다.  사용자는 보고서를 실행 해야 하는 회사의 임원 일 수도 있고 사용자가 임시 쿼리를 실행 하는 분석가가 될 수도 있습니다. 쿼리에 중요도를 할당 하는 작업 분류자를 만듭니다.  아래 예제에서는 새 [작업 분류자 만들기](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) 구문을 사용 하 여 두 개의 분류자를 만듭니다.  Membername은 단일 사용자 또는 그룹 일 수 있습니다. 개별 사용자 분류는 역할 분류 보다 우선적으로 적용 됩니다. 기존 데이터 웨어하우스 사용자를 찾으려면 다음을 실행 합니다.

```sql
Select name from sys.sysusers
```

중요도가 더 높은 사용자에 대해 작업 분류자를 만들려면 다음을 수행 합니다.

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

낮은 중요도 실행으로 임시 쿼리를 실행 하는 사용자에 대 한 작업 분류자를 만들려면 다음을 수행 합니다.  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>다음 단계
- 워크 로드 관리에 대 한 자세한 내용은 [작업 분류](sql-data-warehouse-workload-classification.md) 를 참조 하세요.
- 중요도에 대 한 자세한 내용은 [워크 로드 중요도](sql-data-warehouse-workload-importance.md) 를 참조 하세요.

> [!div class="nextstepaction"]
> [워크 로드 중요도 관리 및 모니터링으로 이동](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)

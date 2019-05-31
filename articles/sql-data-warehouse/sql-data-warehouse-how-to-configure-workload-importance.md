---
title: Azure SQL Data Warehouse의 워크 로드 중요도 구성 | Microsoft Docs
description: 요청 수준 중요도 설정 하는 방법에 알아봅니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 7121d2f022f9c9a5bbc02f04955d2857f3ec986f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241232"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse의 워크 로드 중요도 구성 합니다.

SQL Data Warehouse에서 중요도 설정 쿼리의 예약에 영향을 줄 수 있습니다. 높은 중요도 사용 하 여 쿼리 낮은 중요도 사용 하 여 쿼리 하기 전에 실행 되도록 예약 됩니다. 중요도 쿼리를 할당 하려면 작업 분류자 만들기 해야 합니다.

## <a name="create-a-workload-classifier-with-importance"></a>중요도 사용 하 여 워크 로드 분류자 만들기

종종 데이터 웨어하우스 시나리오에서 해당 쿼리가 신속 하 게 실행 해야 하는 사용자를 수 있습니다.  사용자는 보고서 또는 사용자를 실행 해야 하는 회사의 임원은 임시 쿼리를 실행 하는 분석가 수 수 있습니다. 쿼리 중요도 할당 하려면 작업 분류자를 만들어야 합니다.  아래 예제를 사용 하 여 새 [워크 로드 분류자 만들기](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) 두 분류자를 만드는 구문입니다.  단일 사용자 또는 그룹 Membername 수 있습니다. 개별 사용자 분류 역할 분류 보다 우선합니다. 기존 데이터 웨어하우스 사용자를 찾으려면 다음을 실행 합니다.

```sql
Select name from sys.sysusers
```

실행 하는 높은 중요도 사용 하 여 사용자에 대 한 워크 로드 분류자를 만들려면:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

실행 하는 낮은 중요도 사용 하 여 임시 쿼리를 실행 하는 사용자에 대 한 워크 로드 분류자를 만들려면:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>다음 단계
- 워크 로드 관리에 대 한 자세한 내용은 참조 하세요. [작업 분류](sql-data-warehouse-workload-classification.md)
- 중요도에 대 한 자세한 내용은 참조 하세요. [작업 중요도](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [관리로 이동한 후 모니터링 워크 로드 중요도 ](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)

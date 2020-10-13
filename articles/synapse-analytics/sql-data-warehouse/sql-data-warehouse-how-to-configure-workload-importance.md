---
title: 워크로드 중요도 구성
description: Azure Synapse Analytics에서 요청 수준 중요도를 설정하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 83170f4090909e3edcc163312383773d088d8c57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85212125"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 워크로드 중요도 구성

Azure Synapse에 대한 Synapse SQL의 중요도를 설정하면 쿼리 예약에 영향을 줄 수 있습니다. 중요도가 높은 쿼리는 중요도가 낮은 쿼리보다 먼저 실행되도록 예약됩니다. 쿼리에 중요도를 할당하려면 워크로드 분류자를 만들어야 합니다.

## <a name="create-a-workload-classifier-with-importance"></a>중요도가 포함된 워크로드 분류자 만들기

데이터 웨어하우스 시나리오에서 사용자는 사용 중인 시스템에서 쿼리를 신속하게 실행해야 하는 경우가 있습니다.  사용자는 보고서를 실행해야 하는 회사의 임원이거나 임시 쿼리를 실행하는 분석가일 수 있습니다. 중요도를 할당하려면 작업 분류자를 만들고 중요도를 쿼리에 할당합니다.  아래 예제에서는 [워크로드 분류자 만들기](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 구문을 사용하여 두 개의 분류자를 만듭니다. `Membername`은 단일 사용자 또는 그룹일 수 있습니다.  기존 데이터 웨어하우스 사용자를 찾으려면 다음을 실행합니다.

```sql
Select name from sys.sysusers
```

중요도가 높은 사용자를 위해 워크로드 분류자를 만들려면 다음을 실행합니다.

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

중요도가 낮은 임시 쿼리를 실행하는 사용자에 대한 워크로드 분류자를 만들려면 다음을 실행합니다.  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>다음 단계

- 워크로드 관리에 대한 자세한 내용은 [워크로드 분류](sql-data-warehouse-workload-classification.md)를 참조하세요.
- 중요도에 대한 자세한 내용은 [워크로드 중요도](sql-data-warehouse-workload-importance.md)를 참조하세요.

> [!div class="nextstepaction"]
> [워크로드 중요도 관리 및 모니터링으로 이동](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)

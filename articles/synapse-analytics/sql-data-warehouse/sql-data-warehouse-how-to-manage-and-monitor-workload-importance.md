---
title: 워크로드 중요도 관리 및 모니터링
description: Azure Synapse 분석에서 요청 수준 중요도를 관리하고 모니터링하는 방법을 알아봅니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 3efd8a776542616a9ceefba331b06406540905a8
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633333"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>Azure 시냅스 분석에서 워크로드 중요도 관리 및 모니터링

DMV 및 카탈로그 보기를 사용하여 Azure Synapse에서 시냅스 SQL 요청 수준 중요도를 관리하고 모니터링합니다.

## <a name="monitor-importance"></a>중요도 모니터링

[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 동적 관리 보기에서 새 중요도 열을 사용하여 중요도를 모니터링합니다.
아래 모니터링 쿼리에는 쿼리에 대한 제출 시간과 시작 시간이 표시됩니다. 중요도와 함께 제출 시간과 시작 시간을 검토하여 일정 수립에 얼마나 중요한 영향을 미쳤는지 확인합니다.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

쿼리가 예약되는 방법을 자세히 살펴보려면 카탈로그 보기를 사용합니다.

## <a name="manage-importance-with-catalog-views"></a>카탈로그 보기로 중요도 관리

sys.workload_management_workload_classifiers 카탈로그 보기에는 분류자에 대한 정보가 포함되어 있습니다. 리소스 클래스에 매핑되는 시스템 정의 분류기를 제외하려면 다음 코드를 실행합니다.

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

카탈로그 보기 [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)분류기 작성에 사용되는 매개 변수에 대한 정보가 포함되어 있습니다.  아래 쿼리는 ExecReportsClassifier가 ExecutiveReports를 ```membername``` 사용 하 인 값에 대 한 매개 변수에 만들어졌습니다.

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![쿼리 결과](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

잘못된 분류 문제 해결을 간소화하려면 워크로드 분류자를 만들 때 리소스 클래스 역할 매핑을 제거하는 것이 좋습니다. 아래 코드는 기존 리소스 클래스 역할 멤버 자격을 반환합니다. 해당 리소스 ```membername``` 클래스에서 반환된 각 리소스클래스에 대해 sp_droprolemember 실행합니다.
다음은 워크로드 분류기 삭제 전에 존재를 확인하는 예입니다.

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>다음 단계

- 분류에 대한 자세한 내용은 [워크로드 분류](sql-data-warehouse-workload-classification.md)를 참조하십시오.
- 중요도에 대한 자세한 내용은 [워크로드 중요도를](sql-data-warehouse-workload-importance.md) 참조하십시오.

> [!div class="nextstepaction"]
> [워크로드 중요도 구성으로 이동](sql-data-warehouse-how-to-configure-workload-importance.md)

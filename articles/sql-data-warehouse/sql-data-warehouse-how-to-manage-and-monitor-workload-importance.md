---
title: 관리 및 Azure SQL Data Warehouse의 워크 로드 중요도 모니터링 | Microsoft Docs
description: 요청 수준 중요도 모니터링 하는 방법을 알아봅니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: a39d71e0f8b8072cab6a3af9a2f0913973f303ee
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235934"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>관리 및 Azure SQL Data Warehouse의 워크 로드 중요도 모니터링 합니다.

관리 및 카탈로그 뷰와 Dmv를 사용 하 여 Azure SQL Data Warehouse의 요청 수준 중요도 모니터링 합니다.

## <a name="monitor-importance"></a>모니터 중요도

새 중요도 열을 사용 하 여 중요도 모니터링 합니다 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) 동적 관리 뷰.
아래 쿼리를 제출할 때 및 시작 쿼리에 대 한 시간 표시를 모니터링 합니다. 제출 시간을 검토 하 고 중요도 예약 영향을 보려는 중요도 함께 시간을 시작 합니다.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

확인 추가 방법 중인 쿼리가 일정을 카탈로그 뷰를 사용 합니다.

## <a name="manage-importance-with-catalog-views"></a>카탈로그 뷰를 사용 하 여 중요도 관리 합니다.

Sys.workload_management_workload_classifiers 카탈로그 뷰는 Azure SQL Data Warehouse 인스턴스에 분류자에 대 한 정보를 포함합니다. 제외할 시스템 정의 리소스 클래스에 매핑하는 분류자는 다음 코드를 실행 합니다.

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

카탈로그 뷰 [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), 분류자의 생성에 사용 된 매개 변수에 대 한 정보를 포함 합니다.  ExecReportsClassifier에서 만들어진 아래 쿼리는 ```membername``` ExecutiveReports 사용 하 여 값 매개 변수:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![쿼리 결과](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

오분류 문제를 간단히 해결하려면, 워크로드 분류자를 만들때 리소스 클래스 역할 매핑을 제거하는 것이 좋습니다. 아래 코드는 기존 리소스 클래스의 역할 멤버 자격을 반환합니다. Sp_droprolemember 각각에 대해 실행 ```membername``` 해당 리소스 클래스에서 반환 합니다.
다음은 작업 분류자를 삭제 하기 전에 있는지 확인 하는 예:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>다음 단계
- 분류에 대 한 자세한 내용은 참조 하세요. [워크 로드 분류](sql-data-warehouse-workload-classification.md)합니다.
- 중요도에 대 한 자세한 내용은 참조 하세요. [작업 중요도](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [워크 로드 중요도 구성으로 이동 ](sql-data-warehouse-how-to-configure-workload-importance.md)

---
title: 워크로드 중요도 관리 및 모니터링
description: Azure SQL Data Warehouse에서 요청 수준 중요도를 관리 하 고 모니터링 하는 방법을 알아봅니다.
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
ms.openlocfilehash: ee9acb873c5118733de142045457028c3f4d5f61
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692718"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에서 워크 로드 중요도 관리 및 모니터링

Dmv 및 카탈로그 뷰를 사용 하 여 Azure SQL Data Warehouse에서 요청 수준 중요도를 관리 하 고 모니터링 합니다.

## <a name="monitor-importance"></a>중요도 모니터링

[_Pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) 동적 관리 뷰에서 새 중요도 열을 사용 하 여 중요도를 모니터링 합니다.
아래 모니터링 쿼리는 쿼리 제출 시간 및 시작 시간을 보여 줍니다. 중요도와 함께 제출 시간 및 시작 시간을 검토 하 여 중요도의 영향을 받는 일정을 확인 합니다.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

쿼리를 예약 하는 방법에 대 한 자세한 내용을 보려면 카탈로그 뷰를 사용 합니다.

## <a name="manage-importance-with-catalog-views"></a>카탈로그 뷰를 사용 하 여 중요도 관리

Workload_management_workload_classifiers 카탈로그 뷰는 Azure SQL Data Warehouse 인스턴스의 분류자에 대 한 정보를 포함 합니다. 리소스 클래스에 매핑되는 시스템 정의 분류자를 제외 하려면 다음 코드를 실행 합니다.

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

카탈로그 뷰 [workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest)에는 분류자 생성에 사용 되는 매개 변수에 대 한 정보가 포함 되어 있습니다.  아래 쿼리는 ExecutiveReports를 사용 하 여 값에 대 한 ```membername``` 매개 변수에 ExecReportsClassifier를 만들었음을 보여 줍니다.

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![쿼리 결과](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

잘못 된 분류 문제 해결을 간소화 하려면 작업 분류자를 만들 때 리소스 클래스 역할 매핑을 제거 하는 것이 좋습니다. 아래 코드는 기존 리소스 클래스 역할 멤버 자격을 반환 합니다. 해당 하는 리소스 클래스에서 반환 된 각 ```membername```에 대해 sp_droprolemember를 실행 합니다.
다음은 작업 분류자를 삭제 하기 전에 존재 여부를 확인 하는 예제입니다.

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>다음 단계
- 분류에 대 한 자세한 내용은 [작업 분류](sql-data-warehouse-workload-classification.md)를 참조 하세요.
- 중요도에 대 한 자세한 내용은 [워크 로드 중요도](sql-data-warehouse-workload-importance.md) 를 참조 하세요.

> [!div class="nextstepaction"]
> [워크 로드 중요도 구성으로 이동](sql-data-warehouse-how-to-configure-workload-importance.md)

---
title: sys.external_job_streams(Transact-SQL) - Azure SQL Edge(미리 보기)
description: Azure SQL Edge(미리 보기)에서 sys.external_job_streams를 사용하는 방법에 대해 알아봅니다.
keywords: sys.external_job_streams, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 2634128f4d431e4283f59032c6474a71f2af364d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84233094"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams(Transact-SQL)

외부 스트리밍 작업에 매핑된 입력 또는 출력 외부 스트림 개체에 대한 각 행을 반환합니다.

|열 이름|데이터 형식|Description|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| 스트리밍 작업 개체의 개체 ID 번호입니다. 이 열은 sys.external_streaming_jobs의 object_id 열에 매핑됩니다.|
|**stream_id**|**int**| 스트리밍 개체의 개체 ID 번호입니다. 이 열은 sys.external_streams의 object_id 열에 매핑됩니다. |
|**is_input**|**bit**| 스트림 개체가 스트리밍 작업에 대한 입력을 사용하면 1이고, 그렇지 않으면 0입니다.|
|**is_output**|**bit**| 스트림 개체가 스트리밍 작업에 대한 출력을 사용하는 경우 1이고, 그렇지 않으면 0입니다.|

## <a name="example"></a>예제

이 카탈로그 뷰는 `sys.external_streams` 및 `sys.external_streaming_jobs` 카탈로그 뷰와 함께 사용됩니다. 다음은 샘플 쿼리입니다.

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>사용 권한

사용자가 소유하고 있거나 사용 권한을 부여 받은 보안 개체에 대해서만 카탈로그 뷰의 메타데이터를 볼 수 있습니다. 자세한 내용은 [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/)을 참조하세요.

## <a name="see-also"></a>참고 항목

- [카탈로그 뷰(Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [시스템 뷰(Transact-SQL)](/sql/t-sql/language-reference/)

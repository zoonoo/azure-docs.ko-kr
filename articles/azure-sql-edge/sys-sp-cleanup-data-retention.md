---
title: sys. sp_cleanup_data_retention (Transact-sql)-Azure SQL Edge
description: Azure SQL Edge에서 sp_cleanup_data_retention (Transact-sql)를 사용 하는 방법에 대해 알아봅니다.
keywords: sys. sp_cleanup_data_retention (Transact-sql), SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 9c0a6700a476d4f7f875af5373e3c99bc4e25af2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938634"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention(Transact-SQL)

**적용 대상:** Azure SQL Edge

데이터 보존 정책이 설정 된 테이블에서 사용 되지 않는 레코드를 정리 합니다. 자세한 내용은 [데이터 보존](data-retention-overview.md)을 참조 하세요. 

## <a name="syntax"></a>구문 

```sql
sys.sp_cleanup_data_retention   
    { [@schema_name = ] 'schema_name' },  
    { [@table_name = ] 'table_name' },   
    [ [@rowcount =] rowcount OUTPUT ]    

```

## <a name="arguments"></a>인수  
`[ @schema_name = ] schema_name`    
 정리를 수행 해야 하는 테이블의 소유 스키마 이름입니다. *schema_name* 는 **sysname**형식의 필수 매개 변수입니다.
  
`[ @table_name = ] 'table_name'`    
 정리 작업을 수행 해야 하는 테이블의 이름입니다. *table_name* 는 **sysname**형식의 필수 매개 변수입니다.

## <a name="output-parameter"></a>출력 매개 변수  

`[ @rowcount = ] rowcount OUTPUT`   
 rowcount는 테이블에서 레코드 정리의 수를 나타내는 선택적 출력 매개 변수입니다. *rowcount* 는 int입니다.
  
## <a name="permissions"></a>사용 권한    
 Db_owner 권한이 필요 합니다.

## <a name="next-steps"></a>다음 단계
- [데이터 보존 및 자동 데이터 제거](data-retention-overview.md)
- [보존 정책을 사용 하 여 기록 데이터 관리](data-retention-cleanup.md) 
- [데이터 보존 사용 및 사용 안 함](data-retention-enable-disable.md)

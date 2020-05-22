---
title: ALTER EXTERNAL STREAM(Transact-SQL) - Azure SQL Edge(미리 보기)
description: Azure SQL Edge(미리 보기)의 ALTER EXTERNAL STREAM 문에 대해 알아보기
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 0183972b5eb92d3f081b857940609bffc183b331
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595412"
---
# <a name="alter-external-stream-transact-sql"></a>ALTER EXTERNAL STREAM(Transact-SQL)

외부 스트림의 정의를 수정합니다. *Running* 상태의 스트리밍 작업에서 사용되는 외부 스트림을 수정할 수 없습니다. 



## <a name="syntax"></a>구문

```sql
  ALTER EXTERNAL STREAM external_stream_name 
  SET 
    [DATA_SOURCE] = <data_source_name> 
    , LOCATION = <location_name> 
    , EXTERNAL_FILE_FORMAT = <external_file_format_name> 
    , INPUT_OPTIONS = <input_options> 
    , OUTPUT_OPTIONS = <output_options> 
```

## <a name="arguments"></a>인수

Alter External Stream 명령 인수에 대한 자세한 내용은 [CREATE EXTERNAL STREAM(Transact-SQL)](create-external-stream-transact-sql.md)을 참조하세요.

## <a name="return-code-values"></a>반환 코드 값

성공하면 ALTER EXTERNAL STREAM은 0을 반환합니다. 0이 아닌 반환 값은 실패를 나타냅니다.


## <a name="see-also"></a>참고 항목

- [CREATE EXTERNAL STREAM(Transact-SQL)](create-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM(Transact-SQL)](drop-external-stream-transact-sql.md) 

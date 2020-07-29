---
title: ALTER EXTERNAL STREAM(Transact-SQL) - Azure SQL Edge(미리 보기)
description: Azure SQL Edge(미리 보기)의 ALTER EXTERNAL STREAM 문에 대해 알아보기
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 2559c4b4b875403b7c70671e27cb6222a3f1103a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84235208"
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

---
title: Azure Cosmos DB 쿼리 언어의 RegexMatch
description: Azure Cosmos DB에서 RegexMatch SQL 시스템 함수에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004ece7877358be54ba67c2f72eb3210f16fb0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098130"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

정규식 기능을 제공 합니다. 정규식은 텍스트 패턴을 찾기 위한 간결 하 고 유연한 표기법입니다. Azure Cosmos DB는 [PERL 호환 regular expressions (PCRE)](http://www.pcre.org/)를 사용 합니다. 

## <a name="syntax"></a>구문
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>인수
  
*str_expr1*  
   검색할 문자열 식입니다.  
  
*str_expr2*  
   는 정규식입니다.

*str_expr3*  
   정규식에 사용할 선택 된 한정자의 문자열입니다. 이 문자열 값은 선택 사항입니다. 한정자를 사용 하지 않고 RegexMatch를 실행 하려면 빈 문자열을 추가 하거나 전체를 생략할 수 있습니다. 

[Perl에서 정규식을 만들기 위한 구문](https://perldoc.perl.org/perlre)에 대해 알아볼 수 있습니다. 

Azure Cosmos DB는 다음과 같은 네 가지 한정자를 지원 합니다.

| 한정자 | 설명 |
| ------ | ----------- |
| `m` | 여러 줄로 검색 되도록 문자열 식을 처리 합니다. 이 옵션을 사용 하지 않으면 "^" 및 "$"는 각 개별 줄이 아니라 문자열의 시작 또는 끝 부분에서 일치 합니다. |
| `s` | 줄 바꿈 문자를 포함 하 여 모든 문자를 일치 시키려면 "."를 사용 합니다. | 
| `i` | 패턴 일치를 사용할 경우 대/소문자를 무시 합니다. |
| `x` | 모든 공백 문자를 무시 합니다. |

## <a name="return-types"></a>반환 형식
  
  부울 식을 반환합니다. 검색할 문자열 식, 정규식 또는 선택한 한정자가 잘못 된 경우 정의 되지 않은 값을 반환 합니다.
  
## <a name="examples"></a>예
  
다음 간단한 RegexMatch 예제에서는 몇 가지 다른 한정자를 사용 하 여 정규식 일치에 대해 문자열 "abcd"를 확인 합니다.
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

RegexMatch를 사용 하면 StartsWith, EndsWith, Contains 또는 StringEquals 시스템 함수를 사용 하 여 다른 방법으로는 불가능 한 보다 복잡 한 문자열 검색을 수행 하는 데 메타 문자를 사용할 수 있습니다. 다음은 [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo)를 통해 사용할 수 있는 영양 데이터 집합을 사용 하 여 실행할 수 있는 몇 가지 추가 예입니다. 

> [!NOTE] 
> 정규식에서 메타 문자를 사용 해야 하며 특별 한 의미가 없도록 하려면를 사용 하 여 메타 문자를 이스케이프 해야 합니다 `\` .

**"솔트" 단어가 정확히 한 번 포함 된 설명이 있는 항목을 확인 합니다.**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**0에서 99 사이의 숫자가 포함 된 설명이 있는 항목을 확인 합니다.**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**"S" 또는 "s"로 시작 하는 4 개의 문자로 된 단어가 포함 된 항목을 확인 합니다.**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>설명

정규식을 StartsWith, EndsWith, Contains 또는 StringEquals 시스템 함수로 나눌 수 있는 경우이 시스템 함수는 [범위 인덱스](index-policy.md#includeexclude-strategy) 를 활용 합니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)

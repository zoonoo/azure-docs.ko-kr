---
title: SQL 변환 적용
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 SQL 변환 적용 모듈을 사용하여 입력 데이터 집합에서 SQLite 쿼리를 실행하여 데이터를 변환하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314541"
---
# <a name="apply-sql-transformation"></a>SQL 변환 적용

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)의 모듈에 대해 설명합니다.

SQL 변환 적용 모듈을 사용하여 다음을 수행할 수 있습니다.
  
-   결과 테이블을 만들고 이식 가능 데이터베이스에 데이터 집합 저장  
  
-   데이터 형식에 대한 사용자 지정 변환을 수행하거나 집계 만들기  
  
-   SQL 쿼리 문을 실행하여 데이터를 필터링하거나 변경하고 쿼리 결과를 데이터 테이블로 반환  

> [!IMPORTANT]
> 이 모듈에 사용되는 SQL 엔진은 **SQLite**입니다. SQLite 구문에 대한 자세한 내용은 [SQLite에서 이해한 SQL을](https://www.sqlite.org/index.html) 참조하십시오.  

## <a name="how-to-configure-apply-sql-transformation"></a>SQL 변환 적용을 구성하는 방법  

모듈은 최대 세 개의 데이터 집합을 입력으로 사용할 수 있습니다. 각 입력 포트에 연결된 데이터 집합을 참조할 `t1`때 `t2`는 `t3` 테이블 숫자는 입력 포트의 인덱스를 나타냅니다.  
  
나머지 매개 변수는 SQLite 구문을 사용하는 SQL 쿼리입니다. **SQL Script** 텍스트 상자에 여러 줄을 입력할 때 세미콜론을 사용하여 각 문을 종료합니다. 그렇지 않으면 줄바꿈이 공백으로 변환됩니다.  

이 모듈은 SQLite 구문의 모든 표준 명령문을 지원합니다. 지원되지 않는 명령문 목록은 [기술 노트](#technical-notes) 섹션을 참조하십시오.

##  <a name="technical-notes"></a>기술 정보  

이 섹션에는 자주 묻는 질문에 대한 구현 세부 정보, 팁 및 답변이 포함되어 있습니다.

-   포트 1에는 항상 입력이 필요합니다.  
  
-   공백 이나 다른 특수 문자를 포함 하는 열 식별자의 경우 항상 대괄호 또는 이중 따옴표에 `SELECT` 열 `WHERE` 식별자를 둘러싸는 경우 또는 절의 열을 참조 할 때.  
  
### <a name="unsupported-statements"></a>지원되지 않는 명령문  

SQLite는 대부분의 ANSI SQL 표준을 지원하지만 상용 관계형 데이터베이스 시스템에서 지원되는 많은 기능을 포함하고 있지 않습니다. 자세한 내용은 [SQLite에서 이해한 SQL을](http://www.sqlite.org/lang.html)참조하십시오. 또한 SQL 문을 만들 때 다음과 같은 제한 사항에 유의하십시오.  
  
- SQLite에서는 대부분의 관계형 데이터베이스 시스템에서와 같이 열에 형식을 할당하는 것이 아니라 값의 동적 형식 지정을 사용합니다. SQLite는 약하게 형식화되며 암시적 형식 변환을 허용합니다.  
  
- `LEFT OUTER JOIN`구현되지만 그렇지 `RIGHT OUTER JOIN` `FULL OUTER JOIN`않습니다.  

- `RENAME TABLE` 및 `ADD COLUMN` 문을 `ALTER TABLE` 명령과 함께 사용할 수 있지만 `DROP COLUMN`, `ALTER COLUMN`, `ADD CONSTRAINT` 등의 다른 절은 지원되지 않습니다.  
  
- SQLite에서 VIEW를 만들 수 있지만 이후의 보기는 읽기 전용입니다. 보기에서 `DELETE`, `INSERT` 또는 `UPDATE` 문을 실행할 수 없습니다. 그러나 보기에서 `DELETE`, `INSERT` 또는 `UPDATE`를 시도할 때 발생하는 트리거를 만들고 트리거 본문에서 다른 작업을 수행할 수 있습니다.  
  

공식 SQLite 사이트에서 제공되는 지원되지 않는 함수 목록 외에도 다음 위키는 지원되지 않는 다른 기능 목록을 제공합니다: [SQLite - 지원되지 않는 SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 

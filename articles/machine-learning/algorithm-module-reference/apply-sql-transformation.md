---
title: SQL 변환 적용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 SQL 변환 적용 모듈을 사용 하 여 입력 데이터 집합에 대해 SQLite 쿼리를 실행 하 여 데이터를 변환 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 9a195497b4376633bd3c767d7d0ea029109fdf9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76314541"
---
# <a name="apply-sql-transformation"></a>SQL 변환 적용

이 문서에서는 Azure Machine Learning 디자이너의 모듈을 설명 합니다.

SQL 변환 적용 모듈을 사용 하 여 다음을 수행할 수 있습니다.
  
-   결과 테이블을 만들고 이식 가능 데이터베이스에 데이터 집합 저장  
  
-   데이터 형식에 대한 사용자 지정 변환을 수행하거나 집계 만들기  
  
-   SQL 쿼리 문을 실행하여 데이터를 필터링하거나 변경하고 쿼리 결과를 데이터 테이블로 반환  

> [!IMPORTANT]
> 이 모듈에서 사용 되는 SQL 엔진은 **SQLite**입니다. SQLite 구문에 대 한 자세한 내용은 [sqlite의 이해를 받은 SQL](https://www.sqlite.org/index.html) 을 참조 하세요.  

## <a name="how-to-configure-apply-sql-transformation"></a>SQL 변환 적용을 구성 하는 방법  

모듈은 최대 세 개의 데이터 집합을 입력으로 사용할 수 있습니다. 각 입력 포트에 연결 된 데이터 집합을 참조 하는 경우, 및 이름을 사용 해야 합니다 `t1` `t2` `t3` . 테이블 숫자는 입력 포트의 인덱스를 나타냅니다.  
  
나머지 매개 변수는 SQLite 구문을 사용하는 SQL 쿼리입니다. **SQL 스크립트** 텍스트 상자에 여러 줄을 입력할 때는 세미콜론을 사용 하 여 각 문을 종료 합니다. 그렇지 않으면 줄바꿈이 공백으로 변환됩니다.  

이 모듈은 SQLite 구문의 모든 표준 명령문을 지원합니다. 지원 되지 않는 문의 목록은 [기술 참고 사항](#technical-notes) 섹션을 참조 하세요.

##  <a name="technical-notes"></a>기술 정보  

이 섹션에는 구현 세부 정보, 팁 및 질문과 대답 (faq)이 포함 되어 있습니다.

-   포트 1에는 항상 입력이 필요 합니다.  
  
-   공백 또는 기타 특수 문자를 포함 하는 열 식별자의 경우 또는 절에서 열을 참조할 때 항상 열 식별자를 대괄호 또는 큰따옴표로 묶어야 `SELECT` `WHERE` 합니다.  
  
### <a name="unsupported-statements"></a>지원 되지 않는 문  

SQLite는 대부분의 ANSI SQL 표준을 지원하지만 상용 관계형 데이터베이스 시스템에서 지원되는 많은 기능을 포함하고 있지 않습니다. 자세한 내용은 [SQLite의 이해](http://www.sqlite.org/lang.html)를 참조 하십시오. 또한 SQL 문을 만들 때 다음 제한 사항에 유의 해야 합니다.  
  
- SQLite에서는 대부분의 관계형 데이터베이스 시스템에서와 같이 열에 형식을 할당하는 것이 아니라 값의 동적 형식 지정을 사용합니다. SQLite는 약하게 형식화되며 암시적 형식 변환을 허용합니다.  
  
- `LEFT OUTER JOIN` 는 구현 되지만 또는은 구현 되지 않습니다 `RIGHT OUTER JOIN` `FULL OUTER JOIN` .  

- `RENAME TABLE` 및 `ADD COLUMN` 문을 `ALTER TABLE` 명령과 함께 사용할 수 있지만 `DROP COLUMN`, `ALTER COLUMN`, `ADD CONSTRAINT` 등의 다른 절은 지원되지 않습니다.  
  
- SQLite에서 VIEW를 만들 수 있지만 이후의 보기는 읽기 전용입니다. 보기에서 `DELETE`, `INSERT` 또는 `UPDATE` 문을 실행할 수 없습니다. 그러나 보기에서 `DELETE`, `INSERT` 또는 `UPDATE`를 시도할 때 발생하는 트리거를 만들고 트리거 본문에서 다른 작업을 수행할 수 있습니다.  
  

공식 SQLite 사이트에서 제공 되는 지원 되지 않는 함수 목록 외에도, 다음 wiki는 지원 되지 않는 다른 기능 목록을 제공 합니다. [SQLite-지원 되지 않는 SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 

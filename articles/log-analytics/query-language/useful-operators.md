---
title: Azure Log Analytics 쿼리의 유용한 연산자 | Microsoft Docs
description: Log Analytics 쿼리에서 다양한 시나리오에 사용할 일반 함수입니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: ce397b1ba8d77d2916caa2798c0161ba55f51dbb
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42447119"
---
# <a name="useful-operators-in-log-analytics-queries"></a>Log Analytics 쿼리의 유용한 연산자

아래 표에서는 Log Analytics 쿼리에서 다양한 시나리오에 사용할 일반 함수를 제공합니다.

## <a name="useful-operators"></a>유용한 연산자

Category                                |관련 분석 함수
----------------------------------------|----------------------------------------
선택 영역 및 열 별칭            |`project`, `project-away`, `extend`
임시 테이블 및 상수          |`let scalar_alias_name = …;` <br> `let table_alias_name = (){ … &#124; … &#124; … };`
비교 및 문자열 연산자         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
일반 문자열 함수                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
일반 수식 함수                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
텍스트 구문 분석                            |`extract()`, `extractjson()`, `parse`, `split()`
출력 제한                         |`take`, `limit`, `top`, `sample`
날짜 함수                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
그룹화 및 집계                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
조인 및 공용 구조체                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
정렬, 순서                             |`sort`, `order` 
동적 개체(JSON 및 배열)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
논리 연산자                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Machine Learning                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>다음 단계

- [Log Analytics에서 쿼리를 작성하는 방법](get-started-queries.md)에 대한 단원을 진행합니다.
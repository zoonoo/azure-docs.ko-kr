---
title: "OMS Log Analytics 로그 검색의 정규식 | Microsoft Docs"
description: "Log Analytics 로그 검색에서 RegEx 키워드를 사용하여 정규식에 따라 결과를 필터링할 수 있습니다.  이 문서에서는 몇 가지 예제와 함께 이러한 식에 대한 구문을 제공합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 9746170f157ed5065adc953a31687ff18bd73708
ms.contentlocale: ko-kr
ms.lasthandoff: 08/11/2017

---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>정규식을 사용하여 Log Analytics에서 로그 검색 필터링

[로그 검색](log-analytics-log-searches.md)을 사용하면 Log Analytics 리포지토리에서 정보를 추출할 수 있습니다.  [필터 식](log-analytics-search-reference.md#filter-expressions)을 사용하면 특정 조건에 따라 검색 결과를 필터링할 수 있습니다.  **RegEx** 키워드를 사용하면 이 필터에 대한 정규식을 지정할 수 있습니다.  

이 문서에서는 Log Analytics에서 사용하는 정규식 구문에 대한 세부 정보를 제공합니다.

> [!NOTE]
> 검색 가능한 필드가 있는 RegEx만 사용할 수 있습니다.  검색 가능한 필드에 대한 자세한 내용은 [Log Analytics에서 로그 검색을 사용하여 데이터 찾기](log-analytics-log-searches.md#use-additional-filters)에서 **필드 형식**을 참조하세요.


## <a name="regex-keyword"></a>RegEx 키워드

다음 구문을 통해 로그 검색에서 **RegEx** 키워드를 사용합니다.  이 문서에서 다른 섹션을 사용하여 정규식 자체의 구문을 확인할 수 있습니다.

    field:Regex("Regular Expression")
    field=Regex("Regular Expression")

예를 들어, *경고* 또는 *오류* 유형의 경고 레코드를 반환하는 정규식을 사용하려면 다음 로그 검색을 사용합니다.

    Type=Alert AlertSeverity=RegEx("Warning|Error")

## <a name="partial-matches"></a>부분 일치
정규식은 속성의 전체 텍스트와 일치해야 합니다.  부분 일치는 레코드를 반환하지 않습니다.  예를 들어, srv01.contoso.com이라는 컴퓨터에서 레코드를 반환하려고 시도한 경우 다음 로그 검색은 레코드를 반환**하지** 않습니다.

    Computer=RegEx("srv..")

이름의 첫 번째 부분만 정규식과 일치하기 때문입니다.  다음 두 개의 로그 검색은 전체 이름과 일치하기 때문에 이 컴퓨터에서 레코드를 반환합니다.

    Computer=RegEx("srv..@")
    Computer=RegEx("srv...contoso.com")

## <a name="characters"></a>문자
다른 문자를 지정합니다.

| 문자 | 설명 | 예 | 샘플 일치 |
|:--|:--|:--|:--|
| a | 한 개의 문자를 발견합니다. | Computer=RegEx("srv01.contoso.com") | srv01.contoso.com |
| 을 참조하세요. | 단일 문자입니다. | Computer=RegEx("srv...contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| a? | 0개 또는 한 개의 문자를 발견합니다. | Computer=RegEx("srv01?.contoso.com") | srv0.contoso.com<br>srv01.contoso.com |
| a* | 0개 이상의 문자를 발견합니다. | Computer=RegEx("srv01*.contoso.com") | srv0.contoso.com<br>srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| a+ | 한 개 이상의 문자를 발견합니다. | Computer=RegEx("srv01+.contoso.com") | srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [*abc*] | 괄호에 있는 단일 문자와 일치합니다. | Computer=RegEx("srv0[123].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [*a*-*z*] | 범위에 있는 단일 문자와 일치합니다.  여러 범위를 포함할 수 있습니다. | Computer=RegEx("srv0[1-3].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [^*abc*] | 괄호에 있는 문자와 일치하지 않습니다. | Computer=RegEx("srv0[^123].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [^*a*-*z*] | 범위에 있는 문자와 일치하지 않습니다. | Computer=RegEx("srv0[^1-3].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [*n*-*m*] | 숫자의 범위와 일치합니다. | Computer=RegEx("srv[01-03].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| @ | 문자의 문자열입니다. | Computer=RegEx("srv@.contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>여러 문자를 발견합니다.
특정 문자를 여러 번 발견했음을 나타냅니다.

| 문자 | 설명 | 예 | 샘플 일치 |
|:--|:--|:--|:--|
| a{n} |  *n*개의 문자를 발견합니다. | Computer=RegEx("bw-win-sc01{3}.bwren.lab") | bw-win-sc0111.bwren.lab |
| a{n,} |  *n*개 이상의 문자를 발견합니다. | Computer=RegEx("bw-win-sc01{3,}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab<br>bw-win-sc0111111.bwren.lab |
| a{n,m} |  *n* ~ *m*개의 문자를 발견합니다. | Computer=RegEx("bw-win-sc01{3,5}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>논리 식
여러 값에서 선택합니다.

| 문자 | 설명 | 예 | 샘플 일치 |
|:--|:--|:--|:--|
| &#124; | 논리 OR.  두 식 중 하나가 일치하는 경우 결과를 반환합니다. | Type=Alert AlertSeverity=RegEx("Warning&#124;Error") | Warning<br>오류 |
| & | 논리 AND.  두 식이 모두 일치하는 경우 결과를 반환합니다. | EventData=regex("(Security.\*&.\*success.\*)") | 보안 감사 성공 |


## <a name="literals"></a>리터럴
특수 문자를 리터럴 문자로 변환합니다.  ?-\*^\[\]{}\(\)+\|.&과 같은 정규식에 기능을 제공하는 문자를 포함합니다.

| 문자 | 설명 | 예 | 샘플 일치 |
|:--|:--|:--|:--|
| \\ | 특수 문자를 리터럴 문자로 변환합니다. | Status_CF=\\[Error\\]@<br>Status_CF=Error\\-@ | [오류]파일을 찾을 수 없습니다.<br>오류-파일을 찾을 수 없습니다. |


## <a name="next-steps"></a>다음 단계

* [검색 로그](log-analytics-log-searches.md)에 대해 알아보고 Log Analytics 리포지토리에서 데이터를 보고 분석합니다.


---
title: "Azure Data Lake Analytics에서 R로 U-SQL 스크립트 확장 | Microsoft Docs"
description: "U-SQL 스크립트에서 R 코드를 실행하는 방법을 알아봅니다"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: ea837a735404d11b087cd552d1fce64184e88e00
ms.contentlocale: ko-kr
ms.lasthandoff: 05/02/2017


---

# <a name="tutorial-get-started-with-extending-u-sql-with-r"></a>자습서: R로 U-SQL 확장 시작

다음 예제에서는 R 코드를 배포하는 기본 단계를 보여 줍니다.
* REFERENCE ASSEMBLY 문을 사용하여 U-SQL 스크립트에 R 확장을 사용하도록 설정합니다.
* REDUCE 작업을 사용하여 키의 입력 데이터를 분할합니다.
* U-SQL용 R 확장에는 리듀서에 할당된 각 꼭짓점에서 R 코드를 실행하는 기본 제공 리듀서(Extension.R.Reducer)가 포함됩니다 
* 각각 inputFromUSQL 및 outputToUSQL이라는 명명된 전용 데이터 프레임을 사용하여 USQL과 R 간에 데이터를 전달합니다. 입력 및 출력 DataFrame 식별자 이름은 고정되어 있습니다. 즉 사용자가 입력 및 출력 DataFrame 식별자의 미리 정의된 이름을 변경할 수 없습니다.


--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-r-integrates-with-u-sql"></a>R과 U-SQL 통합 방법

### <a name="datatypes"></a>데이터 형식
* U-SQL의 문자열 및 숫자 열은 R DataFrame과 U-SQL 간에 있는 그대로 변환됩니다[지원되는 형식: double, string, bool, integer, byte].
* 요소 데이터 형식은 U-SQL에서 지원되지 않습니다.
* byte[]는 base64로 인코딩된 문자열로 직렬화되어야 합니다.
* 일단 U-SQL에서 R 입력 데이터 프레임을 만들거나 stringsAsFactors: true 리듀서 매개 변수를 설정하여 U-SQL 문자열을 R 코드의 요소로 변환할 수 있습니다.

### <a name="schemas"></a>스키마
* U-SQL 데이터 집합에는 열 이름이 중복될 수 없습니다.
* U-SQL 데이터 집합 열 이름은 문자열이어야 합니다.
* 열 이름은 U-SQL 및 R 스크립트에서 동일해야 합니다.
* 읽기 전용 열이 UDO의 출력 스키마의 일부이면 U-SQL 테이블에 자동으로 다시 주입되기 때문에 이 읽기 전용 열은 출력 데이터 프레임의 일부가 될 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](data-lake-analytics-use-window-functions.md)


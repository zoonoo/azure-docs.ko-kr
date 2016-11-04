---
title: 데이터 팩터리 함수 및 시스템 변수 | Microsoft Docs
description: Azure 데이터 팩터리 함수 및 시스템 변수 목록을 제공합니다.
documentationcenter: ''
author: sharonlo101
manager: jhubbard
editor: monicar
services: data-factory

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: shlo

---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure 데이터 팩터리 - 함수 및 시스템 변수
이 문서에서는 Azure 데이터 팩터리에서 지원하는 함수와 변수에 대한 정보를 제공합니다.

## <a name="data-factory-system-variables"></a>데이터 팩터리 시스템 변수
| 변수 이름 | 설명 | 개체 범위 | JSON 범위 및 사용 사례 |
| --- | --- | --- | --- |
| WindowStart |현재 작업 실행 창에 대한 시간 간격의 시작 |작업 |<ol><li>데이터 선택 쿼리를 지정합니다.  [데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 참조되는 커넥터 문서 참조)</li><li>Hive 스크립트(위의 샘플)에 매개 변수를 전달합니다.</li> |
| WindowEnd |현재 작업 실행 창에 대한 시간 간격의 끝 |작업 |위와 동일 |
| SliceStart |생성되는 데이터 조각에 대한 시간 간격 시작 |작업<br/>데이터 집합 |<ol><li>[Azure Blob](data-factory-azure-blob-connector.md) 및 [파일 시스템 데이터 집합](data-factory-onprem-file-system-connector.md)과 작업하는 동안 동적 폴더 경로 및 파일 이름을 지정합니다.</li><li>작업 입력 컬렉션에서 데이터 팩터리 함수에 입력 종속성을 지정합니다.</li></ol> |
| SliceEnd |생성되는 현재 데이터 조각에 대한 시간 간격 끝 |작업<br/>데이터 집합 |위와 동일. |

> [!NOTE]
> 현재 데이터 팩터리에서는 작업에 지정된 일정이 출력 데이터 집합의 가용성에 지정된 일정과 정확히 일치해야 합니다. 즉, WindowStart, WindowEnd와 SliceStart, SliceEnd가 항상 같은 기간과 단일 출력 조각으로 매핑됩니다.
> 
> 

## <a name="data-factory-functions"></a>데이터 팩터리 함수
데이터 팩터리의 함수를 위에 설명된 시스템 변수와 함께 다음과 같은 용도로 사용할 수 있습니다.

1. 데이터 선택 쿼리 지정([데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 참조되는 커넥터 문서 참조)
   
   데이터 팩터리 함수를 호출하는 구문은 데이터 선택 쿼리와 작업 및 데이터 집합의 기타 속성에 대해 **$$<function>**입니다.  
2. 작업 입력 컬렉션에서 데이터 팩터리 함수에 입력 종속성 지정(위 샘플 참조)
   
    $$는 입력 종속성 식을 지정할 때는 필요하지 않습니다.   

다음 샘플에서는 JSON 파일에서 **sqlReaderQuery** 속성을 **Text.Format** 함수가 반환하는 값에 할당합니다. 또한 작업 실행 창의 시작 시간을 나타내는 **WindowStart**라는 시스템 변수도 사용합니다.

    {
        "Type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
    }

### <a name="functions"></a>함수
다음 표에서는 Azure Data Factory의 모든 함수를 보여 줍니다.

| Category | 함수 | 매개 변수 | 설명 |
| --- | --- | --- | --- |
| Time |AddHours(X,Y) |X: DateTime  <br/><br/>Y: int |지정된 시간 X에 Y시간을 추가합니다. <br/><br/>예: 9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM |
| Time |AddMinutes(X,Y) |X: DateTime  <br/><br/>Y: int |X에 Y분을 추가합니다.<br/><br/>예: 9/15/2013 12: 00:00 PM + 15분 = 9/15/2013 12: 15:00 PM |
| Time |StartOfHour(X) |X: DateTime |X의 시간 구성 요소로 표현되는 시간에 대한 시작 시간을 가져옵니다. <br/><br/>예: 9/15/2013 05: 10:23 PM의 StartOfHour는 9/15/2013 05: 00:00 PM |
| Date |AddDays(X,Y) |X: DateTime <br/><br/>Y: int |X에 Y일을 추가합니다.<br/><br/>예: 9/15/2013 12:00:00 PM + 2일 = 9/17/2013 12:00:00 PM |
| Date |AddMonths(X,Y) |X: DateTime <br/><br/>Y: int |X에 Y개월을 추가합니다.<br/><br/>예: 9/15/2013 12:00:00 PM + 1개월 = 10/15/2013 12:00:00 PM |
| Date |AddQuarters(X,Y) |X: DateTime  <br/><br/>Y: int |X에 Y * 3개월을 추가합니다.<br/><br/>예:9/15/2013 12:00:00 PM + 1분기 = 12/15/2013 12:00:00 PM |
| Date |AddWeeks(X,Y) |X: DateTime <br/><br/>Y: int |X에 Y * 7일을 추가합니다.<br/><br/>예: 9/15/2013 12:00:00 PM + 1주 = 9/22/2013 12:00:00 PM |
| Date |AddYears(X,Y) |X: DateTime <br/><br/>Y: int |X에 Y년을 추가합니다.<br/><br/>예: 9/15/2013 12:00:00 PM + 1년 = 9/15/2014 12:00:00 PM |
| Date |Day(X) |X: DateTime |X의 일 구성 요소를 가져옵니다.<br/><br/>예: 9/15/2013 12:00:00 PM의 Day는 9임. |
| Date |DayOfWeek(X) |X: DateTime |X의 요일 구성 요소를 가져옵니다.<br/><br/>예: 9/15/2013 12:00:00 PM의 DayOfWeek는 Sunday임. |
| Date |DayOfYear(X) |X: DateTime |X의 연도 구성 요소로 표현되는 연도의 일을 가져옵니다.<br/><br/>예제:<br/>12/1/2015: 2015년의 335일<br/>12/31/2015: 2015년의 365일<br/>12/31/2016: 2016년의 366일(윤년)임. |
| Date |DaysInMonth(X) |X: DateTime |매개 변수 X의 월 구성 요소로 표현되는 월의 일을 가져옵니다.<br/><br/>예: 9월은 30일이므로 9/15/2013의 DaysInMonth는 30임. |
| Date |EndOfDay(X) |X: DateTime |X의 끝나는 날(일 구성 요소)을 나타내는 날짜-시간을 가져옵니다.<br/><br/>예: 9/15/2013 05:10:23 PM의 EndOfDay는 9/15/2013 11:59:59 PM임. |
| Date |EndOfMonth(X) |X: DateTime |매개 변수 X의 월 구성 요소로 표현되는 월의 끝을 가져옵니다. <br/><br/>예: 9/15/2013 05:10:23 PM의 EndOfMonth는 9/30/2013 11:59:59 PM(9월의 끝을 나타내는 날짜 시간)임. |
| Date |StartOfDay(X) |X: DateTime |매개 변수 X의 일 구성 요소로 표현되는 일의 시작을 가져옵니다.<br/><br/>예: 9/15/2013 05:10:23 PM의 StartOfDay는 9/15/2013 12:00:00 AM임. |
| DateTime |From(X) |X: String |문자열 X를 날짜 시간으로 구문 분석합니다. |
| DateTime |Ticks(X) |X: DateTime |매개 변수 X의 틱 속성을 가져옵니다. 1틱은 100나노초에 해당합니다. 이 속성 값은 0001년 1월 1일 자정 12:00:00 이후 경과된 틱 수를 나타냅니다. |
| 텍스트 |Format(X) |X: String 변수 |텍스트의 형식을 지정합니다. |

#### <a name="text.format-example"></a>Text.Format 예
    "defines": { 
        "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
        "Month" : "$$Text.Format('{0:MM}',WindowStart)",
        "Day" : "$$Text.Format('{0:dd}',WindowStart)",
        "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
    }

사용할 수 있는 다른 서식 옵션을 설명하는 [사용자 지정 날짜 및 시간 형식 문자열](https://msdn.microsoft.com/library/8kb3ddd4.aspx) (예: yy 대 yyyy) 토픽을 참조하세요. 

> [!NOTE]
> 다른 함수 내에서 함수를 사용할 경우 내부 함수에 대한 접두사로 **$$** 를 사용해야 합니다. 예: $$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' and RowKey ge \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). 이 예에서는 **Time.AddHours** 함수에 대해 **$$** 접두사가 사용되지 않았습니다. 
> 
> 

<!--HONumber=Oct16_HO2-->



---
title: Azure Data Lake에 대한 U-SQL 프로그래밍 기능 가이드
description: 적절한 USQL 스크립트를 만들 수 있도록 하는 Azure Data Lake Analytics의 U-SQL 개요 및 UDF 프로그래밍 기능에 대해 알아봅니다.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96510865"
---
# <a name="u-sql-programmability-guide-overview"></a>U-SQL 프로그래밍 기능 가이드 개요

U-SQL은 빅 데이터 유형의 워크로드를 위해 설계된 쿼리 언어입니다. U-SQL의 고유한 기능 중 하나는 SQL과 같은 선언적 언어와 C#에서 제공하는 확장성 및 프로그래밍 기능을 결합한 것입니다. 이 가이드에서는 C#에서 사용할 수 있는 U-SQL 언어의 확장성과 프로그래밍 기능을 집중적으로 설명합니다.

## <a name="requirements"></a>요구 사항

[Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)를 다운로드하고 설치합니다.

## <a name="get-started-with-u-sql"></a>U-SQL 시작  

다음 U-SQL 스크립트를 살펴보겠습니다.

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

이 스크립트는 두 개의 행 집합, `@a`과 `@results`를 정의합니다. `@results` 행 집합은 `@a`에서 정의됩니다.

## <a name="c-types-and-expressions-in-u-sql-script"></a>U-SQL 스크립트의 C# 형식 및 식

U-SQL 식은 `AND`, `OR` 및 `NOT`과 같은 U-SQL 논리 연산자와 결합된 C# 식입니다. U-SQL 식은 SELECT, EXTRACT, WHERE, HAVING, GROUP BY 및 DECLARE와 함께 사용할 수 있습니다. 예를 들어 다음 스크립트는 문자열을 DateTime 값으로 구문 분석합니다.

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

다음 코드 조각은 DECLARE 문에서 문자열을 DateTime 값으로 구문 분석합니다.

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>데이터 형식 변환에 C# 식 사용

다음 예제는 C# 식을 사용하여 datetime 데이터 변환을 수행하는 방법을 보여줍니다. 이 특정 시나리오에서 datetime 문자열 데이터는 자정 00:00:00 시간 표기법을 사용하는 표준 datetime으로 변환됩니다.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>오늘 날짜에 C# 식 사용

오늘 날짜를 끌어오려면 `DateTime.Now.ToString("M/d/yyyy")` C# 식을 사용할 수 있습니다.

다음은 스크립트에서 이 식을 사용하는 방법의 예입니다.

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>.NET 어셈블리 사용

U-SQL 확장성 모델은 .NET 어셈블리에서 사용자 지정 코드를 추가할 수 있는 기능에 크게 의존합니다. 

### <a name="register-a-net-assembly"></a>.NET 어셈블리 등록

`CREATE ASSEMBLY` 문을 사용하여 .NET 어셈블리를 U-SQL Database에 배치합니다. 그런 다음 U-SQL 스크립트에서 `REFERENCE ASSEMBLY` 문을 사용하여 이러한 어셈블리를 사용할 수 있습니다. 

다음 코드는 어셈블리를 등록하는 방법을 보여 줍니다.

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

다음 코드는 어셈블리를 참조하는 방법을 보여 줍니다.

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

이 항목을 더 자세히 설명하는 [어셈블리 등록 지침](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog)을 참조하세요.


### <a name="use-assembly-versioning"></a>어셈블리 버전 관리 사용
현재 U-SQL은 .NET Framework 버전 4.7.2를 사용합니다. 따라서 자신의 어셈블리가 해당 런타임 버전과 호환되는지 확인해야 합니다.

앞에서 언급했듯이 U-SQL은 64비트(x64) 형식 코드를 실행합니다. 따라서 코드가 x64에서 실행되도록 컴파일되었는지 확인합니다. 그렇지 않으면 앞에서 설명한 잘못된 형식 오류가 발생합니다.

업로드된 각 어셈블리 DLL, 리소스 파일(예: 다양한 런타임), 네이티브 어셈블리 또는 구성 파일의 크기는 최대 400MB입니다. DEPLOY RESOURCE를 통해 또는 참조 어셈블리를 통해 배포된 리소스 및 추가 파일의 총 크기는 3GB를 초과할 수 없습니다.

마지막으로 U-SQL 데이터베이스마다 지정된 어셈블리 버전을 하나만 포함할 수 있습니다. 예를 들어 NewtonSoft Json.Net 라이브러리의 버전 7과 버전 8이 모두 필요하면 서로 다른 두 데이터베이스에 등록해야 합니다. 또한 각 스크립트는 지정된 어셈블리 DLL의 한 가지 버전만 참조할 수 있습니다. 이러한 점에서 U-SQL은 C# 어셈블리 관리 및 버전 관리 의미 체계를 따릅니다.

## <a name="use-user-defined-functions-udf"></a>UDF(사용자 정의 함수) 사용
U-SQL UDF(사용자 정의 함수)는 매개 변수를 받아들이고, 작업(예: 복잡한 계산)을 수행하며, 해당 작업의 결과를 값으로 반환하는 프로그래밍 루틴입니다. UDF의 반환 값은 단지 단일 스칼라일 수 있습니다. U-SQL UDF는 다른 C# 스칼라 함수와 같이 U-SQL 기본 스크립트에서 호출할 수 있습니다.

U-SQL 사용자 정의 함수는 **public** 및 **static** 으로 초기화하는 것이 좋습니다.

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

먼저 UDF를 만드는 간단한 예제를 살펴보겠습니다.

이 사용 사례 시나리오에서는 특정 사용자에 대한 첫 번째 로그인의 회계 분기 및 회계 월을 포함하는 회계 기간을 결정해야 합니다. 시나리오에서 해당 연도의 첫 번째 회계 월은 6월입니다.

회계 기간을 계산하기 위해 다음 C# 함수를 도입합니다.

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

단순히 회계 월과 분기를 계산하고 문자열 값을 반환합니다. 첫 번째 회계 분기의 첫 번째 월인 6월에 대해서는 "Q1:P1"을 사용합니다. 7월에 대해서는 "Q1:P2"를 사용하고 이런 방식으로 계속 적용합니다.

이것은 U-SQL 프로젝트에 사용할 일반 C# 함수입니다.

이 시나리오의 코드 숨김 섹션은 다음과 같은 모양입니다.

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

이제 기본 U-SQL 스크립트에서 이 함수를 호출할 것입니다. 이렇게 하려면 함수에 대해 네임스페이스를 포함하는 정규화된 이름을 제공해야 합니다. 이 경우 NameSpace.Class.Function(매개 변수)입니다.
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

다음은 실제 U-SQL 기본 스크립트입니다.
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

다음은 스크립트 실행의 출력 파일입니다.

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

이 예제에서는 U-SQL에서 인라인 UDF를 간단하게 사용하는 방법을 보여 줍니다.

### <a name="keep-state-between-udf-invocations"></a>UDF 호출 사이 상태 유지
U-SQL C# 프로그래밍 기능 개체는 코드 숨김 전역 변수를 통한 상호 작용을 활용하여 더 정교해질 수 있습니다. 다음과 같은 비즈니스 사용 사례 시나리오를 살펴보겠습니다.

대규모 조직에서는 사용자가 다양한 내부 애플리케이션 간에 전환할 수 있습니다. 여기에는 Microsoft Dynamics CRM, PowerBI 등이 포함될 수 있습니다. 고객을 위해 사용자가 다양한 애플리케이션 사이를 전환하는 방법, 사용 추세 등에 대한 원격 분석을 적용하는 것이 필요할 수 있습니다. 비즈니스의 목표는 애플리케이션 사용을 최적화하는 것입니다. 다양한 애플리케이션이나 특정 로그인 루틴을 결합하는 것이 필요할 수 있습니다.

이러한 목표를 달성하려면 세션 ID 및 마지막으로 발생한 세션 간의 지연 시간을 판단해야 합니다.

이전 로그인을 찾은 다음 이 로그인을 동일한 애플리케이션에 생성되는 모든 세션에 할당해야 합니다. 첫 번째 과제는 U-SQL 기본 스크립트를 사용하면 LAG 함수로 이미 계산된 열에 계산을 적용할 수 없다는 점입니다. 두 번째 과제는 같은 기간 내의 모든 세션에 대해 특정 세션을 유지해야 한다는 것입니다.

이 문제를 해결하기 위해 코드 숨김 섹션 내에 전역 변수 `static public string globalSession;`을 사용하겠습니다.

이 전역 변수는 스크립트를 실행하는 동안 전체 행 집합에 적용됩니다.

다음은 U-SQL 프로그램의 코드 숨김 섹션입니다.

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

이 예제는 `getStampUserSession` 함수 내에 사용되고 Session 매개 변수가 변경될 때마다 다시 초기화되는 전역 변수 `static public string globalSession;`을 보여줍니다.

U-SQL 기본 스크립트는 다음과 같습니다.

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

여기서 `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` 함수는 두 번째 메모리 행 집합을 계산하는 동안 호출됩니다. `UserSessionTimestamp` 열을 전달하고 `UserSessionTimestamp`가 변경될 때까지 값을 반환합니다.

출력 파일은 다음과 같습니다.

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

이 예제는 전체 메모리 행 집합에 적용된 코드 숨김 섹션 내부에 전역 변수를 사용하는 보다 세밀한 사용 사례 시나리오를 보여줍니다.

## <a name="next-steps"></a>다음 단계
* [U-SQL 프로그램 기능 가이드 - UDT 및 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [U-SQL 프로그래밍 기능 가이드 - UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
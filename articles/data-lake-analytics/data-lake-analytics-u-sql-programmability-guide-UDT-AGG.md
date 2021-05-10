---
title: Azure Data Lake에 대한 U-SQL UDT 및 UDAGG 프로그래밍 기능 가이드
description: 적절한 USQL 스크립트를 만들 수 있도록 하는 Azure Data Lake Analytics의 U-SQL UDT 및 UDAGG 프로그래밍 기능에 대해 알아봅니다.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 10fcce9a667d9a08318f5adab804f482387052ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606660"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>U-SQL 프로그램 기능 가이드 - UDT 및 UDAGG



## <a name="use-user-defined-types-udt"></a>UDT(사용자 정의 형식) 사용
UDT(사용자 정의 형식)는 U-SQL의 또 다른 프로그래밍 기능입니다. U-SQL UDT는 일반 C# 사용자 정의 형식처럼 작동합니다. C#는 기본 제공 및 사용자 지정 UDT를 사용할 수 있는 강력한 형식의 언어입니다.

U-SQL은 UDT가 행 집합의 꼭짓점 간에 전달되는 동안 임의의 UDT를 암시적으로 직렬화하거나 역직렬화할 수 없습니다. 다시 말해 사용자가 IFormatter 인터페이스를 사용하여 명시적인 포맷터를 제공해야 합니다. 이렇게 하면 U-SQL에 UDT에 대한 직렬화 및 역직렬화 메서드가 제공됩니다.

> [!NOTE]
> U-SQL의 기본 제공 추출기 및 출력기는 현재 IFormatter 집합이 있는 파일 사이에서 UDT 데이터를 직렬화하거나 역직렬화할 수 없습니다. 따라서 OUTPUT 문이 있는 파일에 UDT 데이터를 작성하는 경우 또는 추출기로 UDT 데이터를 읽는 경우 UDT 데이터를 문자열 또는 바이트 배열로 전달해야 합니다. 그런 다음 직렬화 및 역직렬화 코드(즉 UDT의 ToString() 메서드)를 명시적으로 호출합니다. 반면 사용자 정의 추출기 및 출력자는 UDT를 읽고 쓸 수 있습니다.

다음과 같이 (이전 SELECT의 외부에 있는) EXTRACTOR 또는 OUTPUTTER에서 UDT를 사용하려고 시도하면:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

다음 오류가 발생합니다.

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Outptutter에서 UDT를 사용하려면 ToString() 메서드를 사용하여 문자열로 직렬화하거나 사용자 지정 Outputter를 만들어야 합니다.

UDT는 현재 GROUP BY에서 사용할 수 없습니다. GROUP BY에 UDT를 사용하면 다음과 같은 오류가 발생합니다.

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

UDT를 정의하려면 다음을 수행해야 합니다.

1. 다음 네임스페이스를 추가합니다.

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. UDT 인터페이스에 필요한 `Microsoft.Analytics.Interfaces`를 추가합니다. 더불어 `System.IO`가 IFormatter 인터페이스를 정의하는 데 필요할 수 있습니다.

3. SqlUserDefinedType 특성을 사용하여 사용자 정의 형식을 정의합니다.

**SqlUserDefinedType** 은 어셈블리의 형식 정의를 U-SQL UDT로 표시하는 데 사용됩니다. 특성의 속성에는 UDT의 물리적 특성이 반영됩니다. 이 클래스는 상속될 수 없습니다.

SqlUserDefinedType은 UDT 정의에 필요한 특성입니다.

클래스 생성자:  

* SqlUserDefinedTypeAttribute(형식 포맷터)

* 형식 포맷터: UDT 포맷터를 정의하는 필수 매개 변수이며 구체적으로 `IFormatter` 인터페이스 형식이 여기로 전달되어야 합니다.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* 다음 예제와 같이, 일반적인 UDT도 IFormatter 인터페이스에 대한 정의가 필요합니다.

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter` 인터페이스는 \<typeparamref name="T">의 루트 형식으로 개체 그래프를 직렬화하고 역직렬화합니다.

\<typeparam name="T">직렬화/역직렬화할 개체 그래프의 루트 형식입니다.

* **Deserialize**: 제공된 스트림의 데이터를 역직렬화하고 개체 그래프를 다시 구성합니다.

* **Serialize**: 제공된 스트림에 지정된 루트를 사용하여 개체 또는 개체 그래프를 직렬화합니다.

`MyType` instance: 해당 형식 인스턴스입니다.  
`IColumnWriter` writer / `IColumnReader` reader: 기본 열 스트림입니다.  
`ISerializationContext` context: 직렬화하는 동안 스트림에 대한 원본 또는 대상 컨텍스트를 지정하는 플래그 집합을 정의하는 열거형입니다.

* **Intermediate**: 원본 또는 대상 컨텍스트가 지속형 저장소가 아님을 지정합니다.

* **Persistence**: 원본 또는 대상 컨텍스트가 지속형 저장소임을 지정합니다.

일반 C# 형식으로 U-SQL UDT 정의는 +/==/!= 등의 연산자에 대한 재정의를 포함할 수 있습니다. 정적 메서드도 포함할 수 있습니다. 예를 들어 U-SQL MIN 집계 함수에 이 UDT를 매개 변수로 사용하는 경우 < 연산자 override를 정의해야 합니다.

이 가이드의 앞부분에서 `Qn:Pn (Q1:P10)` 형식의 특정 날짜를 회계 기간으로 식별하는 예제를 보여 주었습니다. 다음 예제에서는 회계 기간 값에 대한 사용자 지정 형식을 정의하는 방법을 보여 줍니다.

다음은 사용자 지정 UDT 및 IFormatter 인터페이스가 포함된 코드 숨김 섹션의 예제입니다.

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

정의된 형식에는 두 개의 숫자(Quarter 및 Month)가 포함되어 있습니다. 여기서는 `==/!=/>/<` 연산자 및 `ToString()` 정적 메서드가 정의됩니다.

앞에서 설명한 대로 UDT는 SELECT 식에서 사용할 수 있지만, 사용자 지정 직렬화 없이는 OUTPUTTER/EXTRACTOR에서 사용할 수 없습니다. `ToString()`을 사용하여 문자열로 직렬화하거나 사용자 지정 OUTPUTTER/EXTRACTOR와 함께 사용해야 합니다.

이제 UDT 사용법에 대해 살펴보겠습니다. 코드 숨김 섹션에서 GetFiscalPeriod 함수를 다음과 같이 변경했습니다.

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
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

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

여기서 볼 수 있듯이 FiscalPeriod 형식의 값을 반환합니다.

다음은 U-SQL 기본 스크립트에서 이 값을 사용하는 방법에 대한 예제입니다. 이 예제에서는 U-SQL 스크립트에서 UDT를 호출하는 다른 형식을 보여줍니다.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
        start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        fiscalquarter,
        fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

다음은 전체 코드 숨김 섹션의 예제입니다.

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
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

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>UDAGG(사용자 정의 집계) 사용
UDAGG(사용자 정의 집계)는 U-SQL에 제공되지 않는 집계 관련 함수입니다. 예제에는 사용자 지정 수학 계산, 문자열 연결 또는 문자열 조작 등을 수행하는 집계가 포함됩니다.

UDAGG(사용자 정의 집계) 기본 클래스 정의는 다음과 같습니다.

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** 는 UDAGG로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.

SqlUserDefinedType 특성은 UDAGG 정의의 **선택 사항** 입니다.


기본 클래스를 사용하면 세 개의 추상 매개 변수(두 개는 입력 매개 변수로, 하나는 결과로)를 전달할 수 있습니다. 데이터 형식은 변수이며, 클래스를 상속하는 동안 정의해야 합니다.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** 는 계산하는 동안 각 그룹에 대해 한 번씩 호출됩니다. 각 집계 그룹에 대한 초기화 루틴을 제공합니다.  
* **Accumulate** 는 각 값에 대해 한 번씩 실행됩니다. 집계 알고리즘의 주요 기능을 제공합니다. 클래스를 상속하는 동안 정의된 다양한 데이터 형식으로 값을 집계하는 데 사용할 수 있습니다. 변수 데이터 형식의 매개 변수 두 개를 사용할 수 있습니다.
* **Terminate** 는 처리가 끝날 때 집계 그룹당 한 번씩 실행되어 각 그룹에 대한 결과를 출력합니다.

올바른 입력 및 출력 데이터 형식을 선언하려면 다음과 같이 클래스 정의를 사용합니다.

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Accumulate의 첫 번째 매개 변수
* T2: Accumulate의 두 번째 매개 변수
* TResult: Terminate의 반환 형식

예를 들면 다음과 같습니다.

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

또는

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>U-SQL에서 UDAGG 사용
UDAGG를 사용하려면 먼저 코드 숨김에 정의하거나 앞에서 설명한 대로 기존 프로그래밍 기능 DLL에서 참조합니다.

그런 다음, 다음 구문을 사용합니다.

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

UDAGG의 예제는 다음과 같습니다.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

기본 U-SQL 스크립트:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

이 사용 사례 시나리오에서는 특정 사용자에 대한 GUID 클래스를 연결합니다.

## <a name="next-steps"></a>다음 단계
* [U-SQL 프로그램 기능 가이드 - 개요](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 프로그래밍 기능 가이드 - UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)

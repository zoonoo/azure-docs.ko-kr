---
title: U-SQL 사용자 정의 적용 자 Azure Data Lake에 대 한 프로그래밍 기능 가이드
description: U-SQL UDO 프로그래밍 기능 가이드-사용자 정의 적용 자에 대해 알아봅니다.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512640"
---
# <a name="use-user-defined-applier"></a>사용자 정의 적용자 사용 

## <a name="u-sql-udo-user-defined-applier"></a>U-SQL UDO: 사용자 정의 적용 자
U-SQL 사용자 정의 적용자를 사용하면 쿼리의 외부 테이블 식에서 반환한 각 행에 대해 사용자 지정 C # 함수를 호출할 수 있습니다. 오른쪽 입력은 왼쪽 입력에서 각 행에 대해 평가되고, 생성된 행은 최종 출력을 위해 결합됩니다. APPLY 연산자에서 생성한 열 목록은 왼쪽 및 오른쪽 입력에 있는 열 집합의 조합입니다.


## <a name="how-to-define-and-use-user-defined-applier"></a>사용자 정의 적용 자를 정의 하 고 사용 하는 방법
사용자 정의 적용자는 U-SQL SELECT 식의 일부로 호출됩니다.

사용자 정의 적용자에 대한 일반적인 호출은 다음과 같은 모양입니다.

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

SELECT 식의 적용자 사용에 대한 자세한 내용은 [U-SQL SELECT: CROSS APPLY 및 OUTER APPLY에서 선택](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply)(영문)을 참조하세요.

사용자 정의 적용자 기본 클래스 정의는 다음과 같습니다.

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

사용자 정의 적용자를 정의하려면 사용자 정의 적용자 정의에서 선택적인 [`SqlUserDefinedApplier`] 특성을 포함한 `IApplier` 인터페이스를 생성해야 합니다.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Apply는 Outer 테이블의 각 행에 대해 호출되며, `IUpdatableRow` 출력 행 집합을 반환합니다.
* 생성자 클래스는 사용자 정의 적용자에 매개 변수를 전달하는 데 사용됩니다.

**SqlUserDefinedApplier** 는 사용자 정의 적용자로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.

**SqlUserDefinedApplier** 는 사용자 정의 적용자 정의의 **선택 사항** 입니다.


주요 프로그래밍 개체는 다음과 같습니다.

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

입력 행 집합은 `IRow` 입력으로 전달됩니다. 출력 행은 `IUpdatableRow` 출력 인터페이스로 생성됩니다.

개별 열 이름은 `IRow` 스키마 메서드를 호출하여 결정될 수 있습니다.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

들어오는 `IRow`에서 실제 데이터 값을 가져오려면 `IRow` 인터페이스의 Get() 메서드를 사용합니다.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

또는 스키마 열 이름을 사용합니다.

```csharp
row.Get<int>(row.Schema[0].Name)
```

출력 값은 `IUpdatableRow` 출력으로 설정해야 합니다

```csharp
output.Set<int>("mycolumn", mycolumn)
```

사용자 지정 적용자는 `output.Set` 메서드 호출로 정의된 열과 값만 출력한다는 점을 이해하는 것이 중요합니다.

실제 출력은 `yield return output.AsReadOnly();`를 호출하여 트리거됩니다.

사용자 정의 적용자 매개 변수는 생성자에 전달될 수 있습니다. 적용자는 기본 U-SQL 스크립트에서 Applier를 호출하는 동안 정의되어야 하는 가변 개수의 열을 반환할 수 있습니다.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

다음은 사용자 정의 적용자 예제입니다.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

다음은 이 사용자 정의 적용자에 대한 기본 U-SQL 스크립트입니다.

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

이 사용 사례 시나리오에서 사용자 정의 적용자는 car fleet 속성에 대한 쉼표로 구분된 값 파서 역할을 수행합니다. 입력 파일 행은 다음과 같은 모양입니다.

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

이 파일은 제조업체 모델 등의 자동차 속성을 포함하는 속성 열이 탭으로 분리된 일반적인 TSV 파일입니다. 이러한 속성은 테이블 열로 구문 분석되어야 합니다. 제공된 적용자를 사용하면 전달되는 매개 변수를 기반으로 결과 행 집합에 동적인 수의 속성을 생성할 수 있습니다. 모든 속성을 생성하거나 특정 속성 집합만 생성할 수 있습니다.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

사용자 정의 적용자는 applier 개체의 새 인스턴스로 호출될 수 있습니다.

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

또는 래퍼 팩터리 메서드 호출을 통해 호출될 수 있습니다.

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>다음 단계
* [U-SQL 프로그래밍 기능 가이드-개요](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 프로그래밍 기능 가이드-UDT 및 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
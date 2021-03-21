---
title: Azure Data Lake에 대 한 U-SQL 사용자 정의 결합 자 프로그래밍 기능 가이드
description: U-SQL UDO 프로그래밍 기능 가이드-사용자 정의 결합 자에 대해 알아봅니다.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512699"
---
# <a name="use-user-defined-combiner"></a>사용자 정의 결합자 사용

## <a name="u-sql-udo-user-defined-combiner"></a>U-SQL UDO: 사용자 정의 결합 자
UDC(사용자 정의 결합자)를 사용하면 사용자 지정 논리를 기반으로 하여 왼쪽 및 오른쪽 행 집합의 행을 결합할 수 있습니다. UDC는 COMBINE 식으로 사용됩니다.

## <a name="how-to-define-and-use-user-defined-combiner"></a>사용자 정의 결합 자을 정의 하 고 사용 하는 방법

결합자는 입력 행 집합, 그룹화 열, 예상 결과 스키마 및 추가 정보에 대해 필요한 정보를 제공하는 COMBINE 식으로 호출됩니다.

기본 U-SQL 스크립트에서 결합자를 호출하려면 다음 구문을 사용합니다.

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

자세한 내용은 [COMBINE 식(U-SQL)](/u-sql/statements-and-expressions/combine-expression)을 참조하세요.

UDC를 정의하려면 UDC 정의에서 선택적인 [`SqlUserDefinedCombiner`] 속성을 포함한 `ICombiner` 인터페이스를 만들어야 합니다.

기본 `ICombiner` 클래스 정의:

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

`ICombiner` 인터페이스의 사용자 지정 구현은 `IEnumerable<IRow>` Combine override에 대한 정의를 포함해야 합니다.

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

**SqlUserDefinedCombiner** 특성은 사용자 정의 결합자로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.

**SqlUserDefinedCombiner** 는 결합자 모드 속성을 정의하는 데 사용됩니다. 사용자 정의 결합자 정의의 선택적 특성입니다.

CombinerMode Mode

CombinerMode 열거형은 다음 값을 포함할 수 있습니다.

* Full  (0) 모든 출력 행이 잠재적으로 왼쪽과 오른쪽에서 동일한 키 값을 가진 모든 입력 행에 따라 달라집니다.

* Left  (1) 모든 출력 행이 왼쪽에서 단일 입력 행(그리고 잠재적으로 오른쪽에서 동일한 키 값을 가진 모든 행)에 따라 달라집니다.

* Right (2)     모든 출력 행이 오른쪽에서 단일 입력 행(그리고 잠재적으로 왼쪽에서 동일한 키 값을 가진 모든 행)에 따라 달라집니다.

* Inner (3) 모든 출력 행이 왼쪽과 오른쪽에서 동일한 값을 가진 단일 입력 행에 따라 달라집니다.

예: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


주요 프로그래밍 개체:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

입력 행 집합은 **left** 및 **right** `IRowset` 형식의 인터페이스로 전달됩니다. 두 행 집합은 처리하기 위해 모두 열거되어야 합니다. 각 인터페이스를 한 번만 열거할 수 있으므로 필요에 따라 열거하고 캐시해야 합니다.

캐싱에 사용 하기 위해 \<T\> LINQ 쿼리 실행의 결과로 메모리 구조의 목록 유형을 만들 수 있습니다. 특히 목록<`IRow`> 합니다. 익명 데이터 형식도 열거하는 동안에 사용할 수 있습니다.

LINQ 쿼리에 대 한 자세한 내용은 [Linq 쿼리 소개 (c #)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) 및 ienumerable 인터페이스에 대 한 자세한 내용은 [ienumerable \<T\> interface](/dotnet/api/system.collections.generic.ienumerable-1) 를 참조 하십시오 \<T\> .

들어오는 `IRowset`에서 실제 데이터 값을 가져오려면 `IRow` 인터페이스의 Get() 메서드를 사용합니다.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

개별 열 이름은 `IRow` 스키마 메서드를 호출하여 결정될 수 있습니다.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

또는 스키마 열 이름을 사용합니다.

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

LINQ를 사용한 일반적인 열거형은 다음과 같은 모양입니다.

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

두 가지 행 집합을 열거한 후 모든 행을 반복할 예정입니다. 왼쪽 행 집합의 각 행에 대해 결합자의 조건을 충족하는 모든 행을 찾을 예정입니다.

출력 값은 `IUpdatableRow` 출력으로 설정해야 합니다.

```csharp
output.Set<int>("mycolumn", mycolumn)
```

실제 출력은 `yield return output.AsReadOnly();`를 호출하여 트리거됩니다.

다음은 결합자 예제입니다.

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

이 사용 사례 시나리오에서는 판매점에 대한 분석 보고서를 빌드합니다. 목표는 $20,000이 넘고 특정 시간 프레임 내에 일반 판매점보다 인터넷 웹 사이트를 통해 빠르게 판매된 모든 제품을 찾는 것입니다.

다음은 기본 U-SQL 스크립트이며, 일반 JOIN과 결합자 사이의 논리를 비교할 수 있습니다.

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

사용자 정의 결합자는 applier 개체의 새 인스턴스로 호출될 수 있습니다.

```csharp
USING new MyNameSpace.MyCombiner();
```


또는 래퍼 팩터리 메서드 호출을 통해 호출될 수 있습니다.

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>다음 단계
* [U-SQL 프로그래밍 기능 가이드-개요](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 프로그래밍 기능 가이드-UDT 및 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
---
title: Azure Data Lake에 대한 U-SQL 사용자 정의 리듀서 프로그래밍 기능 가이드
description: U-SQL UDO 프로그래밍 기능 가이드 - 사용자 정의 리듀서에 대해 알아봅니다.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512628"
---
# <a name="use-user-defined-reducer"></a>사용자 정의 리듀서 사용

## <a name="u-sql-udo-user-defined-reducer"></a>U-SQL UDO: 사용자 정의 리듀서

U-SQL을 사용하면 IReducer 인터페이스를 구현하고 사용자 정의 연산자 확장성 프레임워크를 사용하여 C#으로 사용자 지정 행 집합 리듀서를 작성할 수 있습니다.

UDR(사용자 정의 리듀서)은 데이터를 추출하는(가져오기) 동안 불필요한 행을 제거하는 데 사용할 수 있습니다. 행 및 열을 조작하고 평가하는 데 사용할 수도 있습니다. 프로그래밍 기능 논리를 기반으로 추출해야 하는 행을 정의할 수도 있습니다.

## <a name="how-to-define-and-use-user-defined-reducer"></a>사용자 정의 리듀서를 정의하고 사용하는 방법
UDR 클래스를 정의하려면 선택적 `SqlUserDefinedReducer` 특성을 사용하여 `IReducer` 인터페이스를 생성해야 합니다.

이 클래스 인터페이스는 `IEnumerable` 인터페이스 행 집합 override에 대한 정의를 포함해야 합니다.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

**SqlUserDefinedReducer** 특성은 사용자 정의 리듀서로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.
**SqlUserDefinedReducer** 는 UDR(사용자 정의 리듀서) 정의의 선택적 특성이며, IsRecursive 속성을 정의하는 데 사용됩니다.

* bool IsRecursive    
* **true** = 이 리듀서가 결합적이고 교환 가능한지 여부를 나타냅니다.

주 프로그래밍 개체는 **input** 및 **output** 입니다. input 개체는 입력 행을 열거하는 데 사용됩니다. Output 개체는 감소 작업의 결과로 출력 행을 설정하는 데 사용됩니다.

입력 행 열거형의 경우 `Row.Get` 메서드를 사용합니다.

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

`Row.Get` 메서드에 대한 매개 변수는 U-SQL 기본 스크립트의 `REDUCE` 문의 `PRODUCE` 클래스의 일부로 전달되는 열입니다. 여기서도 올바른 데이터 형식을 사용해야 합니다.

출력의 경우 `output.Set` 메서드를 사용합니다.

사용자 지정 리듀서는 `output.Set` 메서드 호출로 정의된 값만 출력한다는 것을 이해하는 것이 중요합니다.

```csharp
output.Set<string>("mycolumn", guid);
```

실제 리듀서 출력은 `yield return output.AsReadOnly();`를 호출하여 트리거됩니다.

다음은 리듀서 예제입니다.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

이 사용 사례 시나리오에서 리듀서는 사용자 이름이 비어 있는 행을 건너 뜁니다. 행 집합의 각 행에 대해 각각의 필수 열을 읽은 다음 사용자 이름의 길이를 평가합니다. 사용자 이름 값 길이가 0보다 큰 경우에만 실제 행을 출력합니다.

다음은 사용자 지정 리듀서를 사용하는 기본 U-SQL 스크립트입니다.

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

## <a name="next-steps"></a>다음 단계
* [U-SQL 프로그램 기능 가이드 - 개요](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 프로그램 기능 가이드 - UDT 및 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
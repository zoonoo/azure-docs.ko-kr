---
title: Azure Data Lake에 대한 U-SQL 사용자 정의 처리기 프로그래밍 기능 가이드
description: U-SQL UDO 프로그래밍 기능 가이드 - 사용자 정의 처리기에 대해 알아봅니다.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512688"
---
# <a name="use-user-defined-processor"></a>사용자 정의 처리기 사용

## <a name="u-sql-udo-user-defined-processor"></a>U-SQL UDO: 사용자 정의 처리기
UDP(사용자 정의 처리기)는 프로그래밍 기능을 적용하여 들어오는 행을 처리할 수 있는 U-SQL UDO의 형식입니다. UDP를 사용하면 열을 결합하고, 값을 수정하고, 필요한 경우 새 열을 추가할 수 있습니다. 기본적으로 필요한 데이터 요소를 생성하기 위해 행 집합을 처리하는 데 도움이 됩니다.

## <a name="how-to-define-and-use-user-defined-processor"></a>사용자 정의 처리기를 정의하고 사용하는 방법
UDP를 정의하려면 UDP의 선택 사항인 `SqlUserDefinedProcessor` 특성을 포함한 `IProcessor` 인터페이스를 생성해야 합니다.

다음 예제와 같이 이 인터페이스는 `IRow` 인터페이스 행 집합 override에 대한 정의를 포함해야 합니다.

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** 는 UDP로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.

SqlUserDefinedProcessor 특성은 UDP 정의의 **선택 사항** 입니다.

주 프로그래밍 개체는 **input** 및 **output** 입니다. input 개체는 입력 열을 열거하고, output 개체는 출력 데이터를 처리기 작업의 결과로 설정하는 데 사용됩니다.

입력 열을 열거하는 경우 `input.Get` 메서드를 사용합니다.

```csharp
string column_name = input.Get<string>("column_name");
```

`input.Get` 메서드에 대한 매개 변수는 U-SQL 기본 스크립트의 `PROCESS` 문의 `PRODUCE` 절의 일부로 전달되는 열입니다. 여기서는 올바른 데이터 형식을 사용해야 합니다.

출력의 경우 `output.Set` 메서드를 사용합니다.

사용자 지정 생산자는 `output.Set` 메서드 호출로 정의된 열과 값만을 출력한다는 점을 아는 것이 중요합니다.

```csharp
output.Set<string>("mycolumn", mycolumn);
```

실제 처리기 출력은 `return output.AsReadOnly();`를 호출하여 트리거됩니다.

다음은 처리기 예제입니다.

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

이 사용 사례 시나리오에서 처리기는 기존 열, 이 경우 “user” 대문자와 “des”를 결합하여 “full_description”이라는 새 열을 생성합니다. 또한 GUID를 다시 생성하고 원래 및 새 GUID 값을 반환합니다.

이전 예제에서 볼 수 있듯이 `output.Set` 메서드 호출 중에 C# 메서드를 호출할 수 있습니다.

다음은 사용자 지정 처리기를 사용하는 기본 U-SQL 스크립트의 예제입니다.

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>다음 단계
* [U-SQL 프로그램 기능 가이드 - 개요](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 프로그램 기능 가이드 - UDT 및 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
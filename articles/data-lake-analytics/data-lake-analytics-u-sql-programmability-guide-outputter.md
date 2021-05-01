---
title: Azure Data Lake에 대한 U-SQL 사용자 정의 출력자 프로그래밍 기능 가이드
description: U-SQL UDO 프로그래밍 기능 가이드 사용자 정의 출력자에 대해 알아봅니다.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512639"
---
# <a name="use-user-defined-outputter"></a>사용자 정의 출력자 사용

## <a name="u-sql-udo-user-defined-outputter"></a>U-SQL UDO: 사용자 정의 출력자
사용자 정의 출력자는 기본 제공 U-SQL 기능을 확장할 수 있는 또 다른 U-SQL UDO입니다. 추출기와 마찬가지로 여러 개의 기본 제공 출력자가 있습니다.

* *Outputters.Text()*: 다른 인코딩의 구분 기호로 분리된 텍스트 파일에 데이터를 씁니다.
* *Outputters.Csv()*: 다른 인코딩의 쉼표로 구분된 값(CSV) 파일에 데이터를 씁니다.
* *Outputters.Tsv()*: 다른 인코딩의 탭으로 구분된 값(TSV) 파일에 데이터를 씁니다.

사용자 지정 출력자를 사용하면 사용자 정의된 형식으로 데이터를 쓸 수 있습니다. 이 기능은 다음 작업에 유용할 수 있습니다.

* 반 구조화되거나 구조화되지 않은 파일에 데이터 쓰기
* 지원되지 않는 인코딩 데이터 쓰기
* 출력 데이터 수정 또는 사용자 지정 특성 추가

## <a name="how-to-define-and-use-user-defined-outputter"></a>사용자 정의 출력자를 정의하고 사용하는 방법
사용자 정의 출력자를 정의하려면 `IOutputter` 인터페이스를 생성해야 합니다.

다음은 기본 `IOutputter` 클래스 구현입니다.

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

열/행 구분 기호, 인코딩 등과 같은 출력자에 대한 모든 입력 매개 변수는 클래스의 생성자에서 정의해야 합니다. `IOutputter` 인터페이스는 `void Output` override에 대한 정의도 포함해야 합니다. `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` 특성은 원자성 파일 처리를 위해 선택적으로 설정될 수 있습니다. 자세한 내용은 다음 세부 정보를 참조하세요.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output`은 각 입력 행에 대해 호출됩니다. `IUnstructuredWriter output` 행 집합을 반환합니다.
* 생성자 클래스는 사용자 정의 출력자에 매개 변수를 전달하는 데 사용됩니다.
* `Close`는 비용이 높은 상태를 해제하거나 마지막 행이 기록된 시점을 판단하기 위해 선택적으로 재정의하는 데 사용됩니다.

**SqlUserDefinedOutputter** 특성은 사용자 정의 출력자로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.

SqlUserDefinedOutputter는 사용자 정의 출력자 정의의 선택적 특성이며, AtomicFileProcessing 속성을 정의하는 데 사용됩니다.

* bool AtomicFileProcessing   

* **true** 는 출력자에 원자성 출력 파일(JSON, XML 등)이 필요함을 나타냅니다.
* **false** 는 출력자에서 분할/분산 파일(CSV, SEQ 등)을 처리할 수 있음을 나타냅니다.

주요 프로그래밍 개체는 **row** 및 **output** 입니다. **row** 개체는 출력 데이터를 `IRow` 인터페이스로 나열하는 데 사용됩니다. **Output** 은 대상 파일에 출력 데이터를 설정하는 데 사용됩니다.

출력 데이터는 `IRow` 인터페이스를 통해 액세스됩니다. 출력 데이터는 한 번에 한 행씩 전달됩니다.

개별 값은 IRow 인터페이스의 Get 메서드를 호출하여 열거됩니다.

```csharp
row.Get<string>("column_name")
```

개별 열 이름은 `row.Schema`를 호출하여 결정될 수 있습니다.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

이렇게 하면 모든 메타데이터 스키마에 대해 유연한 출력자를 빌드할 수 있습니다.

출력 데이터는 `System.IO.StreamWriter`를 사용하여 파일에 기록됩니다. Stream 매개 변수는 `IUnstructuredWriter output`의 일부로 `output.BaseStream`로 설정됩니다.

각 행을 반복한 후에 파일에 데이터 버퍼를 플러시하는 것이 중요합니다. 또한 Disposable 특성이 활성화된 상태에서(기본값) **using** 키워드와 함께 `StreamWriter` 개체를 사용해야 합니다.

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

그렇지 않으면 매번 반복 후에 Flush() 메서드를 명시적으로 호출합니다. 이 내용은 다음 예제에 표시되어 있습니다.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>사용자 정의 출력자의 머리글 및 바닥글 설정
헤더를 설정하려면 단일 반복 실행 흐름을 사용합니다.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

첫 번째 `if (isHeaderRow)` 블록의 코드는 한 번만 실행됩니다.

바닥글의 경우 `System.IO.Stream` 개체(`output.BaseStream`)의 인스턴스에 대한 참조를 사용합니다. `IOutputter` 인터페이스의 Close() 메서드에 바닥글을 작성합니다.  (자세한 내용은 다음 예제를 참조하세요.)

다음의 사용자 정의 출력자에 대한 예제입니다.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

U-SQL 기본 스크립트:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

HTML 출력자이며, 테이블 데이터로 HTML 파일을 만듭니다.

### <a name="call-outputter-from-u-sql-base-script"></a>U-SQL 기본 스크립트에서 출력자 호출
기본 U-SQL 스크립트에서 사용자 지정 출력자를 호출하려면 출력자 개체의 새 인스턴스를 만들어야 합니다.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

앞의 예에서 설명했듯이, 기본 스크립트로 개체 인스턴스를 작성하지 않기 위해 함수 래퍼를 만들 수 있습니다.

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

이 경우 원래 호출은 다음과 같은 모양입니다.

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>다음 단계
* [U-SQL 프로그램 기능 가이드 - 개요](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 프로그램 기능 가이드 - UDT 및 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
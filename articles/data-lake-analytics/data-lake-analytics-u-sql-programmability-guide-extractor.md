---
title: Azure Data Lake용 U-SQL 사용자 정의 추출기 프로그래밍 가이드
description: U-SQL UDO 프로그래밍 가이드 - 사용자 정의 추출기에 대해 알아보세요.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: ad7f6336753903533771033de21aec8262425a61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97608020"
---
# <a name="use-user-defined-extractor"></a>사용자 정의 추출기 사용

## <a name="u-sql-udo-user-defined-extractor"></a>U-SQL UDO: 사용자 정의 추출기
U-SQL을 사용하면 EXTRACT 문을 사용하여 외부 데이터를 가져올 수 있습니다. EXTRACT 문은 기본 제공 UDO 추출기를 사용할 수 있습니다.  

* *Extractors.Text()*: 다른 인코딩의 구분 기호로 분리된 텍스트 파일에서 데이터를 추출합니다.

* *Extractors.Csv()*: 다른 인코딩의 쉼표로 구분된 값(CSV) 파일에서 데이터를 추출합니다.

* *Extractors.Tsv()*: 다른 인코딩의 탭으로 구분된 값(TSV) 파일에서 데이터를 추출합니다.

사용자 지정 추출기를 개발하는 것이 유용할 수 있습니다. 다음 작업 중 하나를 수행하려는 경우 데이터를 가져오는 동안 도움이 될 수 있습니다.

* 열을 분할하고 개별 값을 수정하여 입력 데이터를 수정합니다. PROCESSOR 기능이 열을 결합하는 데 더 좋습니다.
* 구조화되지 않은 데이터(예: 웹 페이지 및 전자 메일) 또는 반 구조화되지 않은 데이터(예: XML/JSON)를 구문 분석합니다.
* 지원되지 않는 인코딩 데이터를 구문 분석합니다.

## <a name="how-to-define-and-use-user-defined-extractor"></a>사용자 정의 추출기를 정의하고 사용하는 방법
UDE(사용자 정의 추출기)를 정의하려면 `IExtractor` 인터페이스를 만들어야 합니다. 열/행 구분 기호 및 인코딩 등과 같은 추출기에 대한 모든 입력 매개 변수는 클래스의 생성자에서 정의해야 합니다. 다음과 같이 `IExtractor` 인터페이스는 `IEnumerable<IRow>` override에 대한 정의도 포함해야 합니다.

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

**SqlUserDefinedExtractor** 특성은 UDE(사용자 정의 추출기)로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.

SqlUserDefinedExtractor는 UDE 정의의 선택적 특성이며, UDE 개체의 AtomicFileProcessing 속성을 정의하는 데 사용됩니다.

* bool AtomicFileProcessing   

* **true** 는 추출기에 원자성 입력 파일(JSON, XML 등)이 필요함을 나타냅니다.
* **false** 는 추출기에서 분할/분산 파일(CSV, SEQ 등)을 처리할 수 있음을 나타냅니다.

주 UDE 프로그래밍 개체는 **input** 및 **output** 입니다. input 개체는 입력 데이터를 `IUnstructuredReader`로 열거하는 데 사용됩니다. output 개체는 출력 데이터를 추출기 작업의 결과로 설정하는 데 사용됩니다.

입력 데이터는 `System.IO.Stream` 및 `System.IO.StreamReader`를 통해 액세스됩니다.

입력 열 열거형의 경우 먼저 행 구분 기호를 사용하여 입력 스트림을 분할합니다.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

그런 다음 입력 행을 열 파트로 세분합니다.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

출력 데이터를 설정하려면 `output.Set` 메서드를 사용합니다.

사용자 지정 추출기는 출력에 정의된 열 및 값만 출력한다는 점을 이해하는 것이 중요합니다. 메서드 호출을 설정합니다.

```csharp
output.Set<string>(count, part);
```

실제 추출기 출력은 `yield return output.AsReadOnly();`를 호출하여 트리거됩니다.

다음은 추출기 예제입니다.

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

이 사용 사례 시나리오에서 추출기는 “guid” 열에 대한 GUID를 다시 생성하고 “user” 열의 값을 대문자로 변환합니다. 사용자 지정 추출기는 입력 데이터를 구문 분석하고 이를 조작하여 더 복잡한 결과를 생성할 수 있습니다.

다음은 사용자 지정 추출기를 사용하는 기본 U-SQL 스크립트입니다.

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>다음 단계
* [U-SQL 프로그래밍 가이드 - 개요](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 프로그래밍 가이드 - UDT 및 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
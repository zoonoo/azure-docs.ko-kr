## <a name="specifying-formats"></a>형식 지정
### <a name="specifying-textformat"></a>TextFormat 지정
형식이 **TextFormat**으로 설정된 경우 **형식** 섹션에서 다음과 같은 **선택적** 속성을 지정할 수 있습니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| columnDelimiter |파일의 열을 구분하는 데 사용되는 문자입니다. |문자는 하나만 사용할 수 있습니다. 기본값은 쉼표(',')입니다. |아니요 |
| rowDelimiter |파일의 행을 구분하는 데 사용되는 문자입니다. |문자는 하나만 사용할 수 있습니다. 기본값은 읽기의 경우 ["\r\n", "\r", "\n"] 중 하나이고 쓰기의 경우 "\r\n"입니다. |아니요 |
| escapeChar |입력 파일 내용에서 열 구분 기호를 이스케이프 처리하는 데 사용되는 특수 문자입니다. <br/><br/>한 테이블에 대해 escapeChar 및 quoteChar을 둘 다 지정할 수는 없습니다. |문자는 하나만 사용할 수 있습니다. 기본값은 없습니다. <br/><br/>예: 열 구분 기호로 쉼표(',')를 지정했는데 텍스트에서도 "Hello, world"와 같이 쉼표 문자를 포함하려는 경우에는 이스케이프 문자로 '$'를 정의하고 원본에서 "Hello$, world" 문자열을 사용하면 됩니다. |아니요 |
| quoteChar |문자열 값을 인용하는 데 사용하는 문자입니다. 인용 문자 안의 열 및 행 구분 기호는 문자열 값의 일부분으로 처리됩니다. 이 속성은 입력 데이터 집합과 출력 데이터 집합에 모두 적용됩니다.<br/><br/>한 테이블에 대해 escapeChar 및 quoteChar을 둘 다 지정할 수는 없습니다. |문자는 하나만 사용할 수 있습니다. 기본값은 없습니다. <br/><br/>예: 열 구분 기호로 쉼표(',')를 지정했는데 텍스트에서도 <Hello, world>와 같이 쉼표 문자를 포함하려는 경우에는 인용 문자로 "(큰따옴표)를 정의하고 원본에서 "Hello, world" 문자열을 사용하면 됩니다. |아니요 |
| nullValue |null 값을 나타내는 데 사용되는 하나 이상의 문자입니다. |하나 이상의 문자입니다. 기본값은 읽기의 경우 "\N" 및 "NULL"이고 쓰기의 경우 "\N"입니다. |아니요 |
| encodingName |인코딩 이름을 지정합니다. |유효한 인코딩 이름입니다. [Encoding.EncodingName 속성](https://msdn.microsoft.com/library/system.text.encoding.aspx)을 참조하세요. windows-1250 또는 shift_jis 등을 예로 들 수 있습니다. 기본값은 UTF-8입니다. |아니요 |
| firstRowAsHeader |첫 번째 행을 머리글로 간주할지를 지정합니다. 입력 데이터 집합의 경우 Data Factory는 첫 번째 행을 머리글로 읽습니다. 출력 데이터 집합의 경우에는 첫 번째 행을 머리글로 씁니다. <br/><br/>샘플 시나리오는 [**firstRowAsHeader** 및 **skipLineCount** 사용 시나리오](#scenarios-for-using-firstrowasheader-and-skiplinecount)를 참조하세요. |True<br/>False(기본값) |아니요 |
| skipLineCount |입력 파일에서 데이터를 읽을 때 건너뛸 행의 수를 나타냅니다. SkipLineCount와 firstRowAsHeader를 둘 다 지정하면 해당하는 줄을 먼저 건너뛴 다음 입력 파일에서 머리글 정보를 읽습니다. <br/><br/>샘플 시나리오는 [firstRowAsHeader 및 skipLineCount 사용 시나리오](#scenarios-for-using-firstrowasheader-and-skiplinecount) 를 참조하세요. |Integer |아니요 |
| treatEmptyAsNull |입력 파일에서 데이터를 읽을 때 null 또는 빈 문자열을 null 값으로 처리할지를 지정합니다. |True(기본값)<br/>False |아니요 |

#### <a name="textformat-example"></a>TextFormat 예제
다음 예제는 TextFormat에 대한 서식 속성 중 일부를 보여줍니다.

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myblobname"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
            "firstRowAsHeader": true,
            "skipLineCount": 0,
            "treatEmptyAsNull": true
        }
    },

quoteChar 대신 escapeChar를 사용하려면 quoteChar가 있는 해당 줄을 다음 escapeChar로 바꿉니다.

    "escapeChar": "$",



### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>FirstRowAsHeader 및 skipLineCount 사용 시나리오
* 파일이 아닌 원본에서 텍스트 파일로 데이터를 복사할 때 SQL 스키마 등의 스키마 메타데이터가 포함된 머리글 줄을 추가하려고 합니다. 이 시나리오에서는 출력 데이터 집합에서 **firstRowAsHeader** 를 true로 지정합니다. 
* 머리글 줄이 포함된 텍스트 파일에서 파일이 아닌 싱크로 데이터를 복사할 때 해당 줄을 삭제하려고 합니다. 이 경우에는 입력 데이터 집합에서 **firstRowAsHeader** 를 true로 지정합니다.
* 텍스트 파일에서 데이터를 복사할 때 시작 부분에서 데이터도 없고 머리글 정보도 없는 몇 줄을 건너뛰려고 합니다. 이 경우에는 건너뛸 줄 수를 나타내도록 **skipLineCount** 를 지정합니다. 파일의 나머지 부분에 머리글 줄이 있으면 **firstRowAsHeader**도 지정할 수 있습니다. **skipLineCount**와 **firstRowAsHeader**를 둘 다 지정하면 해당하는 줄을 먼저 건너뛴 다음 입력 파일에서 머리글 정보를 읽습니다.

### <a name="specifying-avroformat"></a>AvroFormat 지정
서식이 AvroFormat으로 설정된 경우 typeProperties 섹션 내의 서식 섹션에서 속성을 지정할 필요가 없습니다. 예제:

    "format":
    {
        "type": "AvroFormat",
    }

Hive 테이블에서 Avro 형식을 사용하려는 경우 [Apache Hive의 자습서](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)를 참조하세요.

### <a name="specifying-jsonformat"></a>JsonFormat 지정
형식이 **JsonFormat**으로 설정된 경우 **형식** 섹션에서 다음과 같은 **선택적** 속성을 지정할 수 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| filePattern |각 JSON 파일에 저장된 데이터의 패턴을 나타냅니다. 사용 가능한 값은 **setOfObjects** 및 **arrayOfObjects**이고 **기본값**은 **setOfObjects**입니다. 이러한 패턴과 관련된 자세한 내용은 다음 섹션을 참조하세요. |아니요 |
| encodingName |인코딩 이름을 지정합니다. 유효한 인코딩 이름 목록은 [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) 속성을 참조하세요. 예: windows-1250 또는 shift_jis **기본값**은 **UTF-8**입니다. |아니요 |
| nestingSeparator |중첩 수준을 구분하는데 사용되는 문자입니다. 기본값은 '.'(점)입니다. |아니요 |

#### <a name="setofobjects-file-pattern"></a>setOfObjects 파일 패턴
각 파일에 단일 개체 또는 줄로 구분된/연결된 여러 개체가 포함되어 있습니다. 출력 데이터 집합에서 이 옵션을 선택하는 경우 복사 활동에서는 줄마다 개체가 하나씩 포함된(각 개체가 줄로 구분된) JSON 파일 하나를 생성합니다.

**단일 개체** 

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }

**줄로 구분된 JSON** 

    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**연결된 JSON**

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }
    {
        "time": "2015-04-29T07:13:21.0220000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "123436380",
        "callingnum2": "789037573",
        "switch1": "US",
        "switch2": "UK"
    }
    {
        "time": "2015-04-29T07:13:21.4370000Z",
        "callingimsi": "466923101048691",
        "callingnum1": "678901578",
        "callingnum2": "345626404",
        "switch1": "Germany",
        "switch2": "UK"
    }


#### <a name="arrayofobjects-file-pattern."></a>arrayOfObjects 파일 패턴
각 파일에 개체 배열이 포함됩니다. 

    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "789037573",
            "callingnum2": "789044691",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789044691",
            "switch1": "US",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:24.2120000Z",
            "callingimsi": "466922201102759",
            "callingnum1": "345698602",
            "callingnum2": "789097900",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:25.6320000Z",
            "callingimsi": "466923300236137",
            "callingnum1": "567850552",
            "callingnum2": "789086133",
            "switch1": "China",
            "switch2": "Germany"
        }
    ]

### <a name="jsonformat-example"></a>JsonFormat 예제
다음 내용을 포함하는 JSON 파일이 있고  

    {
        "Id": 1,
        "Name": {
            "First": "John",
            "Last": "Doe"
        },
        "Tags": ["Data Factory”, "Azure"]
    }

이 내용을 다음 형식으로 Azure SQL 테이블에 복사하려는 경우 

| Id | Name.First | Name.Middle | Name.Last | 태그 |
| --- | --- | --- | --- | --- |
| 1 |John |null |Doe |["Data Factory”, "Azure"] |

JsonFormat 형식의 입력 데이터 집합은 다음과 같이 정의됩니다(관련 부분만 있는 부분 정의).

    "properties": {
        "structure": [
            {"name": "Id", "type": "Int"},
            {"name": "Name.First", "type": "String"},
            {"name": "Name.Middle", "type": "String"},
            {"name": "Name.Last", "type": "String"},
            {"name": "Tags", "type": "string"}
        ],
        "typeProperties":
        {
            "folderPath": "mycontainer/myfolder",
            "format":
            {
                "type": "JsonFormat",
                "filePattern": "setOfObjects",
                "encodingName": "UTF-8",
                "nestingSeparator": "."
            }
        }
    }

구조를 정의하지 않는 경우 복사 작업이 진행되면서 기본적으로 구조가 평면화된 후 모든 내용이 복사됩니다. 

#### <a name="supported-json-structure"></a>지원되는 JSON 구조
다음 사항에 유의하세요. 

* 이름/값 쌍의 컬렉션이 있는 각 개체는 테이블 형식으로 단일 데이터 행에 매핑됩니다. 개체는 중첩될 수 있으며 기본적으로 중첩 구분 기호(.)를 사용해서 데이터 집합의 구조를 평면화하는 방법을 정의할 수 있습니다. 예제는 위의 [JsonFormat 예제](#jsonformat-example) 섹션을 참조하세요.  
* 구조가 Data Factory 데이터 집합에 정의되어 있지 않으면, 복사 작업이 진행되면서 첫 번째 개체의 스키마가 검색되고 전체 개체가 평면화됩니다. 
* JSON 입력에 배열이 있는 경우 복사 작업이 진행되어 전체 배열 값이 문자열로 변환됩니다. [열 매핑 또는 필터링](#column-mapping-with-translator-rules)을 사용하여 이 문자열을 건너뛸 수 있습니다.
* 동일한 수준에 중복된 이름이 있는 경우 복사 활동에서는 마지막 이름이 선택됩니다.
* 속성 이름은 대/소문자를 구분합니다. 이름은 같지만 대/소문자가 다른 두 속성은 별도의 두 속성으로 간주됩니다. 

### <a name="specifying-orcformat"></a>OrcFormat 지정
서식이 OrcFormat으로 설정된 경우 typeProperties 섹션 내의 서식 섹션에서 속성을 지정할 필요가 없습니다. 예제:

    "format":
    {
        "type": "OrcFormat"
    }

> [!IMPORTANT]
> 온-프레미스 및 클라우드 데이터 저장소 간에 ORC 파일을 **있는 그대로** 복사하지 않는 경우에는 게이트웨이 컴퓨터에 JRE 8(Java Runtime Environment)을 설치해야 합니다. 64비트 게이트웨이에는 64비트 JRE가 필요하고 32비트 게이트웨이에는 32비트 JRE가 필요합니다. [여기서](http://go.microsoft.com/fwlink/?LinkId=808605)두 버전이 모두 제공됩니다. 적합한 버전을 선택하세요.
> 
> 

다음 사항에 유의하세요.

* 복합 데이터 형식(구조체, 맵, 목록, 공용 구조체)은 지원되지 않습니다.
* ORC 파일에는 3개의 [압축 관련 옵션](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)(NONE, ZLIB, SNAPPY)이 있습니다. Data Factory에서는 이러한 압축 형식으로 된 데이터를 ORC 파일에서 읽을 수 있습니다. 메타데이터에 있는 압축 코덱을 사용하여 데이터를 읽습니다. 그러나 ORC 파일에 쓸 때 Data Factory는 ORC에 대한 기본값인 ZLIB를 선택합니다. 현재 이 동작을 재정의할 수 있는 옵션은 없습니다. 

### <a name="specifying-parquetformat"></a>ParquetFormat 지정
서식이 ParquetFormat으로 설정된 경우 typeProperties 섹션 내의 서식 섹션에서 속성을 지정할 필요가 없습니다. 예제:

    "format":
    {
        "type": "ParquetFormat"
    }

> [!IMPORTANT]
> 온-프레미스 및 클라우드 데이터 저장소 간에 Parquet 파일을 **있는 그대로** 복사하지 않는 경우에는 게이트웨이 컴퓨터에 JRE 8(Java Runtime Environment)을 설치해야 합니다. 64비트 게이트웨이에는 64비트 JRE가 필요하고 32비트 게이트웨이에는 32비트 JRE가 필요합니다. [여기서](http://go.microsoft.com/fwlink/?LinkId=808605)두 버전이 모두 제공됩니다. 적합한 버전을 선택하세요.
> 
> 

다음 사항에 유의하세요.

* 복합 데이터 형식(MAP, LIST)은 지원되지 않습니다.
* Parquet 파일에는 압축 관련 옵션인 NONE, SNAPPY, GZIP 및 LZO가 포함되어 있습니다. Data Factory에서는 이러한 압축 형식으로 된 데이터를 ORC 파일에서 읽을 수 있습니다. 메타데이터에 있는 압축 코덱을 사용하여 데이터를 읽습니다. 그러나 Parquet 파일에 쓸 때 Data Factory는 Parquet 서식에 대한 기본값인 SNAPPY를 선택합니다. 현재 이 동작을 재정의할 수 있는 옵션은 없습니다. 

<!--HONumber=Oct16_HO2-->



### JsonFormat 지정
형식이 **JsonFormat**으로 설정된 경우 **Format** 섹션에서 다음과 같은 **선택적** 속성을 지정할 수 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| filePattern |각 JSON 파일에 저장된 데이터의 패턴을 나타냅니다. 허용되는 값: **setOfObjects** 및 **arrayOfObjects** **기본값**: **setOfObjects** 이러한 패턴에 대한 자세한 내용은 아래 섹션을 참조하세요. |아니요 |
| encodingName |인코딩 이름을 지정합니다. 올바른 인코딩 이름 목록은 [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) 속성을 참조하세요. 예: windows-1250 또는 shift\_jis **기본값**: **UTF-8** |아니요 |
| nestingSeparator |중첩 수준을 구분하는데 사용되는 문자입니다. **기본값**: **. (dot)** |아니요 |

#### setOfObjects 파일 패턴
각 파일에 단일 개체 또는 줄로 구분된/연결된 여러 개체가 포함되어 있습니다. 출력 데이터 집합에서 이 옵션을 선택하는 경우 줄마다 각 개체와 단일 JSON 파일이 복사됩니다(줄로 구분).

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


#### arrayOfObjects 파일 패턴
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

### JsonFormat 예제
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

#### 지원되는 JSON 구조
다음 사항에 유의하세요.

* 이름/값 쌍의 컬렉션이 있는 각 개체는 테이블 형식으로 단일 데이터 행에 매핑됩니다. 개체는 중첩될 수 있으며 기본적으로 중첩 구분 기호(.)를 사용해서 데이터 집합의 구조를 평면화하는 방법을 정의할 수 있습니다. 예제를 보려면 위의 [JsonFormat 예제](#jsonformat-example) 섹션을 참조하세요.  
* 구조가 Data Factory 데이터 집합에 정의되어 있지 않으면, 복사 작업이 진행되면서 첫 번째 개체의 스키마가 검색되고 전체 개체가 평면화됩니다. 
* JSON 입력에 배열이 있는 경우 복사 작업이 진행되어 전체 배열 값이 문자열로 변환됩니다. [열 매핑 또는 필터링](#column-mapping-with-translator-rules)을 사용하여 건너뛰도록 선택할 수 있습니다.
* 동일한 수준에 중복된 이름이 있는 경우 복사 작업 중에 마지막 이름이 선택됩니다.
* 속성 이름은 대/소문자를 구분합니다. 이름은 같지만 대/소문자가 다른 두 속성은 별도의 두 속성으로 간주됩니다. 

<!---HONumber=AcomDC_0316_2016-->
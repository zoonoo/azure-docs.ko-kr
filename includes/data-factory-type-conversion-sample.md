### <a name="type-conversion-sample"></a>형식 변환 샘플
다음 샘플은 형식 변환을 통해 Blob에서 Azure 테이블로 데이터를 복사하기 위한 것입니다.

Blob 데이터 집합이 CSV 형식이며 3개의 열을 포함하고 있다고 가정합니다. 그 중 하나는 요일에 축약된 프랑스 이름을 사용하여 사용자 지정 datetime 형식을 사용하는 datetime 열입니다.

열에 대한 유형 정의가 다음과 같으면 Blob 원본 데이터 집합을 정의합니다.

```json
{
    "name": "AzureBlobTypeSystemInput",
    "properties":
    {
         "structure": 
          [
                { "name": "userid", "type": "Int64"},
                { "name": "name", "type": "String"},
                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
위에서 SQL 형식과 .NET 형식 매핑을 지정하면 다음 스키마로 Azure QL 테이블에서 테이블을 정의하게 됩니다.

| 열 이름 | SQL 형식 |
| --- | --- |
| userId |bigint |
| name |텍스트 |
| lastlogindate |datetime |

다음으로, Azure SQL 데이터 집합을 다음과 같이 정의합니다. 

> [!NOTE]
> 형식 정보가 기본 데이터 저장소에 이미 지정되어 있으므로 형식 정보로 **structure** 섹션을 지정할 필요가 없습니다.

```json
{
    "name": "AzureSQLOutput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

이 경우 Blob에서 Azure SQL로 데이터를 이동하면 데이터 팩터리는 fr-fr 문화권을 사용하여 사용자 지정 datetime 서식으로 Datetime 필드를 포함하는 형식 변환을 자동으로 수행합니다


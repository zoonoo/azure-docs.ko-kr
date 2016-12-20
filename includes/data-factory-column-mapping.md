## <a name="column-mapping-with-translator-rules"></a>변환기 규칙이 포함된 열 매핑
열 매핑은 원본 테이블 맵의 “structure”에서 지정한 열을 싱크 테이블의 “structure”에서 지정한 열과 매핑하는 방법을 지정하는 데 사용할 수 있습니다. **columnMapping** 속성은 Copy 작업의 **typeProperties** 섹션에서 사용할 수 있습니다.

열 매핑에서는 다음과 같은 시나리오가 지원됩니다.

* 원본 테이블 "structure"에 있는 모든 열이 싱크 테이블 "structure"의 모든 열에 매핑됩니다.
* 원본 테이블 "structure"에 있는 열의 하위 집합이 싱크 테이블 "structure"의 모든 열에 매핑됩니다.

다음 오류 조건 및 예외가 발생합니다.

* 더 적은 열 또는 더 많은 열 "의 구조에서" 싱크 테이블 보다 매핑에 지정 합니다.
* 중복 매핑
* SQL 쿼리 결과에는 매개 변수 매핑에서 지정한 열 이름이 없습니다.

## <a name="column-mapping-samples"></a>열 매핑 예제
> [!NOTE]
> 아래 예제는 Azure SQL 및 Azure Blob에 대한 것이지만, 직사각 데이터 집합을 지원하는 모든 데이터 저장소에 적용할 수 있습니다. 아래 예에서 관련 데이터 원본의 데이터를 가리키도록 데이터 집합과 연결 서비스를 조정해야 합니다. 
> 
> 

### <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>예제 1 – Azure SQL에서 Azure Blob으로의 열 매핑
이 예제에서 입력 테이블에는 구조가 있고 그 구조가 Azure SQL 데이터베이스에 있는 SQL 테이블을 가리킵니다.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

이 예제에서 출력 테이블에는 구조가 있으며 그 구조가 Azure Blob 저장소에 있는 Blob을 가리킵니다.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
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
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

이 작업에 대한 JSON은 아래와 같습니다. 원본의 열과 싱크(**columnMappings**)의 열 간 매핑은 **Translator** 속성을 사용하여 이루어집니다.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**열 매핑 흐름:**

![열 매핑 흐름 ](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>예제 2 – SQL 쿼리를 사용하여 Azure SQL에서 Azure Blob로 열 매핑
이 예제에서는 단순히 “structure” 섹션에서 테이블 이름과 열 이름을 지정하는 대신 SQL 쿼리를 사용하여 Azure SQL의 데이터를 추출합니다. 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
이 경우 쿼리 결과가 먼저 원본의 "structure"에서 지정된 열에 매핑됩니다. 다음으로, 원본 "structure"의 열이 columnMappings에서 지정된 규칙을 통해 싱크 "structure"의 열에 매핑됩니다.  이 쿼리가 5개 열, 2개의 추가 열, 원본의 “structure”에서 지정된 열을 차례로 반환한다고 가정합니다.

**열 매핑 흐름**

![열 매핑 흐름 - 2 ](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)



<!--HONumber=Nov16_HO3-->



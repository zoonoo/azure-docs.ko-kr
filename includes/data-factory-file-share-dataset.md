## <a name="fileshare-dataset-type-properties"></a>FileShare 데이터 집합 형식 속성
데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](../articles/data-factory/data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다.

**typeProperties** 섹션은 데이터 집합의 각 형식마다 다릅니다. 데이터 집합 형식에 대한 정보를 제공합니다. **FileShare** 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| folderPath |폴더에 대한 하위 경로. 문자열의 특수 문자에 이스케이프 문자 '\'를 사용합니다. 예제를 살펴보려면 [연결된 서비스 및 데이터 집합 정의 샘플](#sample-linked-service-and-dataset-definitions) 을 참조하세요.<br/><br/>이 속성을 **partitionBy** 와 결합하여 조각 시작/종료 날짜/시간을 기준으로 폴더 경로를 지정할 수 있습니다. |예 |
| fileName |폴더에서 특정 파일을 참조하기 위해 테이블을 사용하려는 경우 **folderPath** 에 있는 파일의 이름을 지정합니다. 이 속성에 값을 지정하지 않으면 테이블은 폴더에 있는 모든 파일을 가리킵니다.<br/><br/>출력 데이터 집합에 대한 fileName이 지정되는 경우 생성되는 파일의 이름 형식은 다음과 같습니다. <br/><br/>Data.<Guid>.txt(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |아니요 |
| fileFilter |모든 파일이 아닌 folderPath의 파일 하위 집합을 선택하는데 사용할 필터를 지정합니다.<br/><br/>허용 되는 값은 `*`(여러 문자) 및 `?`(하나의 문자)입니다.<br/><br/>예 1: `"fileFilter": "*.log"`<br/>예 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter는 FileShare 입력 데이터 집합에 적용할 수 있습니다. 이 속성은 HDFS에는 지원되지 않습니다. |아니요 |
| partitionedBy |동적 folderPath, 시계열 데이터에 대한 filename을 지정하는 데 partitionedBy를 사용할 수 있습니다. 예를 들어 매시간 데이터에 대한 매개 변수가 있는 folderPath입니다. |아니요 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](#specifying-textformat), [Json 형식](#specifying-jsonformat), [Avro 형식](#specifying-avroformat), [Orc 형식](#specifying-orcformat) 및 [Parquet 형식](#specifying-parquetformat) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사**하려는 경우 입력 및 출력 데이터 집합 정의 둘 다에서 형식 섹션을 건너뜁니다. |아니요 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**이고 지원되는 수준은 **최적** 및 **가장 빠름**입니다. 자세한 내용은 [압축 지정](#specifying-compression) 섹션을 참조하세요. |아니요 |
| useBinaryTransfer |이전 전송 모드를 사용할지 여부를 지정합니다. 이진 모드인 경우 true이고 ASCII인 경우 false입니다. 기본값은 True입니다. 이 속성은 연결된 서비스 유형이 FtpServer인 경우에만 사용할 수 있습니다. |아니요 |

> [!NOTE]
> filename 및 fileFilter는 동시에 사용할 수 없습니다.
>
>

### <a name="using-partionedby-property"></a>partionedBy 속성 사용
이전 섹션에서 설명했듯이 동적 folderPath, partitionedBy된 시계열 데이터에 대한 filename을 지정할 수 있습니다. 지정된 데이터 조각에 대한 논리적 기간을 나타내는 데이터 팩터리 매크로 및 시스템 변수 SliceStart, SliceEnd를 사용하여 이 작업을 수행할 수 있습니다.

시계열 데이터 집합, 예약 및 조각에 대한 자세한 내용은 [데이터 집합 만들기](../articles/data-factory/data-factory-create-datasets.md), [일정 예약 및 실행](../articles/data-factory/data-factory-scheduling-and-execution.md) 및 [파이프라인 만들기](../articles/data-factory/data-factory-create-pipelines.md) 문서를 참조하세요.

#### <a name="sample-1"></a>샘플 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
이 예제에서 {Slice}는 지정된 형식(YYYYMMDDHH)의 Data Factory 시스템 변수 SliceStart 값으로 대체됩니다. SliceStart는 조각의 시작 시간을 가리킵니다. folderPath는 각 조각에 따라 다릅니다. 예를 들어 wikidatagateway/wikisampledataout/2014100103 또는 wikidatagateway/wikisampledataout/2014100104입니다.

#### <a name="sample-2"></a>샘플 2:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
이 예제에서 SliceStart의 연도, 월, 일 및 시간은 folderPath 및 fileName 속성에서 사용하는 별도 변수로 추출됩니다.


<!--HONumber=Jan17_HO3-->



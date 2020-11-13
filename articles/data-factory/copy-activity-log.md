---
title: 복사 활동의 세션 로그
description: Azure Data Factory의 복사 작업에서 세션 로그를 사용 하도록 설정 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: e56a840da07a2f6e966867699506f0122a0e7956
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593650"
---
#  <a name="session-log-in-copy-activity"></a>복사 활동의 세션 로그

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

복사 작업에서 복사한 파일 이름을 기록할 수 있습니다. 그러면 복사 작업 세션 로그에서 복사 된 파일을 검토 하 여 데이터를 원본 저장소에서 대상 저장소로 복사할 뿐만 아니라 원본 및 대상 저장소 간에 일관성을 유지할 수 있습니다.  

복사 작업에서 잘못 된 데이터를 건너뛰는 내결함성 설정을 사용 하도록 설정 하면 건너뛴 파일 및 건너뛴 행도 기록 될 수 있습니다.  [복사 작업에서 내결함성을](copy-activity-fault-tolerance.md)통해 자세한 정보를 얻을 수 있습니다. 

## <a name="configuration"></a>구성
다음 예제에서는 복사 작업에서 세션 로그를 사용 하도록 설정 하는 JSON 정의를 제공 합니다. 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
    },
    "validateDataConsistency": true,
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
}
```

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | -------- 
enableCopyActivityLog | True로 설정 하면 복사 된 파일을 기록 하거나, 건너뛴 파일 또는 건너뛴 행을 저장할 수 있습니다.  | True<br/>False(기본값) | 예
logLevel | "정보"는 복사한 모든 파일, 건너뛴 파일 및 건너뛴 행을 기록 합니다. "경고"는 건너뛴 파일을 기록 하 고 행만 건너뛰었습니다.  | 정보<br/>경고(기본값) | 예
enableReliableLogging | True 이면 신뢰할 수 있는 모드의 복사 작업에서 각 파일이 대상에 복사 되 면 즉시 로그를 플러시합니다.  복사 작업에서 사용할 수 있는 안정적인 로깅 모드를 사용 하 여 대량의 파일을 복사 하는 경우 각 파일을 복사 하는 데 이중 쓰기 작업이 필요 하므로 복사 처리량은 영향을 받게 됩니다. 한 요청은 대상 저장소에, 다른 요청은 로그 저장소 저장소에 대 한 요청입니다.  최상의 작업 모드의 복사 작업은 일정 기간 내에 레코드 일괄 처리로 로그를 플러시합니다 .이 경우 복사 처리량이 훨씬 더 영향을 받게 됩니다. 복사 작업이 실패 했을 때 로그 이벤트의 마지막 일괄 처리가 로그 파일에 플러시되지 않았을 수 있는 몇 가지 가능성이 있으므로 로깅의 완전성과 적시성는이 모드에서 보장 되지 않습니다. 현재는 대상에 복사 된 몇 개의 파일이 로깅되지 않습니다.  | True<br/>False(기본값) | 예
logLocationSettings | 세션 로그를 저장할 위치를 지정 하는 데 사용할 수 있는 속성 그룹입니다. | | 예
linkedServiceName | 세션 로그 파일을 저장할 [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) 또는 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties)의 연결된 서비스입니다. | 로그 파일을 저장하는 데 사용되는 인스턴스를 참조하는 `AzureBlobStorage` 또는 `AzureBlobFS` 형식의 연결된 서비스 이름입니다. | 예
경로 | 로그 파일의 경로입니다. | 로그 파일을 저장할 경로를 지정합니다. 경로를 지정하지 않으면 서비스가 대신 컨테이너를 만듭니다. | 예


## <a name="monitoring"></a>모니터링

### <a name="output-from-copy-activity"></a>복사 활동의 출력
복사 작업이 완전히 실행 된 후에는 각 복사 작업 실행의 출력에서 로그 파일의 경로를 확인할 수 있습니다. 로그 파일은 경로에서 찾을 수 있습니다 `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  로그 파일은 csv 파일입니다. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

### <a name="the-schema-of-the-log-file"></a>로그 파일의 스키마입니다.

다음은 로그 파일의 스키마입니다.

열 | Description 
-------- | -----------  
타임스탬프 | ADF가 개체를 읽거나, 쓰고, 건너뛰는 타임 스탬프입니다.
Level | 이 항목의 로그 수준입니다. ' 경고 ' 또는 "정보" 일 수 있습니다.
OperationName | 각 개체에서 ADF 복사 작업 작동 동작 ' FileRead ', ' FileWrite ', ' FileSkip ' 또는 ' TabularRowSkip ' 일 수 있습니다.
OperationItem | 파일이 나 건너뛴 행의 이름입니다.
메시지 | 원본 저장소에서 파일을 읽거나 대상 저장소에 쓸지 여부를 표시 하는 추가 정보입니다. 파일이 나 행을 건너뛰는 이유도 될 수도 있습니다.

다음은 로그 파일의 예입니다. 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
위 로그 파일에서 원본 저장소와 대상 저장소 간에 일관된 것으로 확인되지 않았기 때문에 sample1.csv를 건너뛰었음을 알 수 있습니다. sample1.csv가 일관되지 않은 이유가 ADF 복사 활동이 복사하는 동안 동시에 다른 애플리케이션이 해당 파일을 변경하고 있었기 때문임을 나타내는 자세한 정보를 확인할 수 있습니다. sample2.csv 원본 저장소에서 대상 저장소로 복사 되었는지 확인할 수도 있습니다.

여러 분석 엔진을 사용 하 여 로그 파일을 추가로 분석할 수 있습니다.  Sql 쿼리를 사용 하 여 테이블 이름이 SessionLogDemo 인 SQL database로 csv 로그 파일을 가져와 로그 파일을 분석 하는 몇 가지 예는 다음과 같습니다.  

-   복사한 파일 목록을 지정 합니다. 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   특정 시간 범위 내에 복사 된 파일 목록을 제공 합니다. 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   특정 파일에 복사 된 시간 및 메타 데이터를 제공 합니다. 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   시간 범위 내에서 메타 데이터가 복사 된 파일의 목록을 제공 합니다. 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   건너뛴 파일 목록을 지정 합니다. 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   특정 파일을 건너뛴 이유를 알려 주세요. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   "Blob 파일이 없습니다." 라는 이유로 인해 건너뛴 파일 목록을 제공 합니다. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   복사 하는 데 가장 긴 시간이 필요한 파일 이름을 지정 합니다.
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 내결함성](copy-activity-fault-tolerance.md)
- [복사 작업 데이터 일관성](copy-activity-data-consistency.md)
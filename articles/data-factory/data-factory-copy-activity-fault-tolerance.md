---
title: "호환되지 않는 행을 건너뛰어 Azure Data Factory 복사 작업에 내결함성 추가 | Microsoft Docs"
description: "복사 중에 호환되지 않는 행을 건너뛰어 Azure Data Factory 복사 작업에 내결함성을 추가하는 방법 알아보기"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: e2a108752259d5da3b401666c6bdbaad13b7ea90
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>호환되지 않는 행을 건너뛰어 복사 작업에 내결함성 추가

Azure Data Factory [복사 작업](data-factory-data-movement-activities.md)은 원본 및 싱크 데이터 저장소 간에 데이터를 복사할 때 호환되지 않는 행을 처리하는 2가지 방법을 제공합니다.

- 호환되지 않는 데이터가 나타날 때 복사 작업을 중단하고 실패한 것으로 처리할 수 있습니다(기본 동작).
- 내결함성을 추가하고 호환되지 않는 데이터 행을 건너뛰어 모든 데이터를 계속 복사할 수 있습니다. 또한 Azure Blob Storage에 호환되지 않는 행을 기록할 수 있습니다. 그런 후 로그를 검토하여 실패의 원인을 파악하고, 데이터 원본에서 데이터를 수정하고, 복사 작업을 다시 시도할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오
복사 작업은 호환되지 않는 데이터의 검색, 건너뛰기 및 로깅에 대한 다음 세 가지 시나리오를 지원합니다.

- **원본 데이터 형식 및 싱크 원시 형식 간의 비호환성**

    예제: 3가지 **INT** 형식 열을 포함하는 스키마 정의를 사용하여 Blob Storage에 있는 CSV 파일의 데이터를 SQL Database로 복사합니다. `123,456,789`와 같은 숫자 데이터를 포함하는 CSV 파일 행은 싱크 저장소에 성공적으로 복사됩니다. 그러나 `123,456,abc`와 같은 숫자가 아닌 값을 포함하는 행을 호환되지 않는 것으로 감지하고 건너뜁니다.

- **원본과 싱크 간의 열 수 불일치**

    예제: 6개의 열이 포함된 스키마 정의를 사용하여 Blob Storage에 있는 CSV 파일의 데이터를 SQL Database로 복사합니다. 6개의 열이 포함된 CSV 파일 행은 싱크 저장소에 성공적으로 복사됩니다. 6개보다 많거나 적은 열을 포함하는 CSV 파일 행을 호환되지 않는 것으로 감지하고 건너뜁니다.

- **관계형 데이터베이스에 쓰는 중 기본 키 위반**

    예제: SQL Server에서 SQL Database로 데이터를 복사합니다. 기본 키가 싱크 SQL Database에 정의되어 있지만 이러한 기본 키가 원본 SQL Server에 정의되어 있지 않습니다. 원본에 있는 중복된 행을 싱크로 복사할 수 없습니다. 복사 작업은 원본 데이터의 첫 번째 행만 싱크에 복사합니다. 중복된 기본 키 값을 포함하는 후속 원본 행을 호환되지 않는 것으로 감지하고 건너뜁니다.

## <a name="configuration"></a>구성
다음 예제에서는 복사 작업에서 호환되지 않는 행을 건너뛰도록 구성하기 위한 JSON 정의를 제공합니다.

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| 속성 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | 복사 중 호환되지 않는 행을 건너뛸지 여부를 설정합니다. | True<br/>False(기본값) | 아니요 |
| **redirectIncompatibleRowSettings** | 호환되지 않는 행을 기록하려는 경우 지정할 수 있는 속성 그룹입니다. | &nbsp; | 아니요 |
| **linkedServiceName** | 건너뛰는 행을 포함하는 로그를 저장하는 Azure Storage의 연결된 서비스입니다. | 로그 파일을 저장하는 데 사용할 저장소 인스턴스를 참조하는 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 또는 [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 연결된 서비스의 이름입니다. | 아니요 |
| **path** | 건너뛴 행을 포함하는 로그 파일의 경로입니다. | 호환되지 않는 데이터를 기록하는 데 사용하려는 Blob Storage 경로를 지정합니다. 경로를 지정하지 않으면 서비스가 대신 컨테이너를 만듭니다. | 아니요 |

## <a name="monitoring"></a>모니터링
복사 작업 실행이 완료되면 모니터링 섹션에서 건너뛴 행의 수를 볼 수 있습니다.

![건너뛰는 호환되지 않는 행 모니터링](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

호환되지 않는 행을 기록하도록 구성한 경우 `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` 경로에서 해당 로그 파일을 찾을 수 있습니다. 이 로그 파일에서 건너뛴 행과 비호환성의 근본 원인을 확인할 수 있습니다.

원본 데이터와 해당 오류가 파일에 모두 기록됩니다. 로그 파일 내용의 예는 다음과 같습니다.
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory 복사 작업에 대해 자세히 알아보려면 [복사 작업을 사용하여 데이터 이동](data-factory-data-movement-activities.md)을 참조하세요.

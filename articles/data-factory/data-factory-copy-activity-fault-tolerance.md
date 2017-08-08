---
title: "Azure Data Factory 복사 작업 내결함성 - 호환되지 않는 행 건너뛰기 | Microsoft Docs"
description: "Azure Data Factory를 사용하여 복사하는 동안 호환되는 행을 건너뛰어 내결함성에 대해 알아봅니다."
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
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d613537657af3bbe379a53e92532bf6b184d762b
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="copy-activity-fault-tolerance---skip-incompatible-rows"></a>복사 작업 내결함성 - 호환되지 않는 행 건너뛰기

[복사 작업](data-factory-data-movement-activities.md)에는 원본 및 싱크 데이터 저장소 간에 데이터를 복사할 때 호환되지 않는 행을 처리하는 옵션이 여러 가지입니다. 호환되지 않는 데이터가 발생하면 복사를 중단하고 실패하도록 선택하거나(기본 동작) 호환되지 않는 행을 건너뛰고 모든 데이터를 계속 복사하도록 선택할 수 있습니다. 또한 실패의 원인을 검토하고 데이터 원본에서 데이터를 수정하여 다시 시도할 수 있도록 Azure Blob에 호환되지 않는 행을 기록하는 옵션도 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오
현재 복사 작업은 복사 중에 다음과 같은 호환되지 않는 상태를 감지하고 건너뛰고 기록하도록 지원합니다.

- **원본 및 싱크 원시 형식 간의 데이터 형식 비호환성**

    예: Azure Blob의 CSV 파일에서 Azure SQL Database로 복사하려는데 Azure SQL Database에 정의된 스키마에 *INT* 형식 열이 세 개 있는 경우 원본 CSV 파일에서 숫자 데이터(예: `123,456,789`)가 있는 행은 성공적으로 복사되지만 숫자가 아닌 값(예: `123,456,abc`)이 포함된 행은 호환되지 않는 행이므로 건너뜁니다.

- **원본 및 싱크 사이에 일치하지 않는 열의 수**

    예: Azure BLOB의 CSV 파일에서 Azure SQL Database로 복사하려는데 SQL Azure에 정의된 스키마에 열이 6개 있는 경우 원본 CSV의 열 6개가 포함된 행은 성공적으로 복사되지만 나머지 열은 호환되지 않는 행이므로 건너뜁니다.

- **관계형 데이터베이스에 쓰는 중 기본 키 위반**

    예: SQL Server에서 Azure SQL Database로 복사하려는데 싱크 Azure SQL Database에 기본 키가 정의되어 있지만 원본 SQL Server에는 해당 기본 키가 정의되지 않은 경우 원본에 존재할 수 있는 중복 행은 싱크에 쓸 때 허용되지 않습니다. 복사 작업은 첫 번째 행만 싱크에 복사하고 원본에서 싱크로 중복 기본 키 값이 있는 두 번째 이상의 행은 건너뜁니다.

## <a name="configuration"></a>구성
다음 예제에서는 복사 작업에서 호환되지 않는 행을 건너뛰도록 구성하는 방법에 대한 JSON 정의를 제공합니다.

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
| enableSkipIncompatibleRow | 복사 중 호환되지 않는 행을 건너뛸지 여부를 설정합니다. | True<br/>False(기본값) | 아니요 |
| redirectIncompatibleRowSettings | 호환되지 않는 행을 기록하려는 경우 지정할 수 있는 속성 그룹입니다. | &nbsp; | 아니요 |
| linkedServiceName | 건너뛰는 모든 행을 포함하는 로그를 저장하는 Azure Storage의 연결된 서비스입니다. | 로그 파일을 저장하는 데 사용할 Storage 인스턴스를 참조하여 이름을 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 또는 [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 연결된 서비스로 지정합니다. | 아니요 |
| path | 건너뛴 모든 행이 포함된 로그 파일의 경로입니다. | 호환되지 않는 데이터를 기록하려는 Blob Storage 경로를 지정합니다. 경로를 지정하지 않으면 서비스가 대신 컨테이너를 만듭니다. | 아니요 |

## <a name="monitoring"></a>모니터링
복사 작업 실행이 완료되면 다음과 같이 모니터링 섹션에서 건너뛴 행의 수를 볼 수 있습니다.

![호환되지 않는 행 건너뛰기 모니터링](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

호환되지 않는 행을 기록하도록 구성한 경우 건너뛴 행과 비호환성의 근본 원인을 알아보기 위해 다음 경로에서 로그 파일을 찾을 수 있습니다. `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`.

원본 데이터와 해당 오류가 파일에 모두 기록됩니다. 로그 파일 내용의 예는 다음과 같습니다.
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).,
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory 복사 작업에 대해 자세히 알아보려면 [복사 작업을 사용하여 데이터 이동](data-factory-data-movement-activities.md)을 참조하세요.

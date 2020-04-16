---
title: Azure 데이터 팩터리에서 메타데이터 작업 가져옵니다.
description: 데이터 팩터리 파이프라인에서 메타데이터 받기 작업을 사용하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jingwang
ms.openlocfilehash: 344ad8e106c119c1de59570d1ec4e3df5e1cc8af
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417116"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Azure 데이터 팩터리에서 메타데이터 작업 가져옵니다.
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

메타데이터 받기 활동을 사용하여 Azure 데이터 팩터리에서 데이터의 메타데이터를 검색할 수 있습니다. 다음 시나리오에서 이 활동을 사용할 수 있습니다.

- 모든 데이터의 메타데이터의 유효성을 검사합니다.
- 데이터가 준비/사용 가능할 때 파이프라인을 트리거합니다.

제어 흐름에서 다음 기능을 사용할 수 있습니다.

- 조건부 식에서 메타데이터 받기 활동의 출력을 사용하여 유효성 검사를 수행할 수 있습니다.
- Do Until 루핑을 통해 조건이 충족되면 파이프라인을 트리거할 수 있습니다.

## <a name="capabilities"></a>기능

메타데이터 받기 활동은 데이터 집합을 입력으로 사용하여 메타데이터 정보를 출력으로 반환합니다. 현재 다음 커넥터와 해당 검색 가능한 메타데이터가 지원됩니다. 반환되는 메타데이터의 최대 크기는 2MB입니다.

>[!NOTE]
>자체 호스팅 통합 런타임에서 메타데이터 받기 작업을 실행하는 경우 버전 3.6 이상에서 최신 기능이 지원됩니다.

### <a name="supported-connectors"></a>지원되는 커넥터

**파일 저장**

| 커넥터/메타데이터 | itemName<br>(파일/폴더) | itemType<br>(파일/폴더) | 크기<br>(파일) | created<br>(파일/폴더) | lastModified<br>(파일/폴더) |childItems<br>(폴더) |contentMD5<br>(파일) | structure<br/>(파일) | columnCount<br>(파일) | exists<br>(파일/폴더) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob 스토리지](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure 파일](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [파일 시스템](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- 폴더에 대해 메타데이터 받기 작업을 사용하는 경우 지정된 폴더에 대한 LIST/EXECUTE 권한이 있는지 확인합니다.
- Amazon S3 및 Google `lastModified` 클라우드 스토리지의 경우 버킷과 키에 적용되지만 `exists` 가상 폴더에는 적용되지 않으며 버킷과 키에는 적용되지만 접두사 나 가상 폴더에는 적용되지 않습니다.
- Azure Blob 저장소의 경우 컨테이너 및 Blob에는 `lastModified` 적용되지만 가상 폴더에는 적용되지 않습니다.
- `lastModified`필터는 현재 자식 항목을 필터링하지만 지정된 폴더/파일 자체는 적용되지 않습니다.
- 폴더/파일의 와일드카드 필터는 메타데이터 받기 활동에 대해 지원되지 않습니다.

**관계형 데이터베이스**

| 커넥터/메타데이터 | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL 데이터베이스 관리 인스턴스](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>메타데이터 옵션

메타데이터 받기 작업 필드 목록에서 다음 메타데이터 유형을 지정하여 해당 정보를 검색할 수 있습니다.

| 메타데이터 유형 | Description |
|:--- |:--- |
| itemName | 파일 또는 폴더의 이름입니다. |
| itemType | 파일 또는 폴더의 형식입니다. 반환된 `File` 값은 또는 . `Folder` |
| 크기 | 파일의 크기(바이트)입니다. 파일에만 적용됩니다. |
| created | 파일 또는 폴더를 만든 날짜/시간입니다. |
| lastModified | 파일 또는 폴더를 마지막으로 수정한 날짜/시간입니다. |
| childItems | 지정된 폴더의 하위 폴더 및 파일 목록입니다. 폴더에만 적용됩니다. 반환된 값은 각 하위 항목의 이름 및 유형 목록입니다. |
| contentMD5 | 파일의 MD5입니다. 파일에만 적용됩니다. |
| structure | 파일 또는 관계형 데이터베이스 테이블의 데이터 구조입니다. 반환된 값은 열 이름 및 열 유형의 목록입니다. |
| columnCount | 파일 또는 관계형 테이블의 열 수입니다. |
| exists| 파일, 폴더 또는 테이블이 있는지 여부입니다. 메타데이터 `exists` 받기 필드 목록에 지정되어 있으면 파일, 폴더 또는 테이블이 존재하지 않더라도 활동이 실패하지 않습니다. 대신 `exists: false` 출력에서 반환됩니다. |

>[!TIP]
>파일, 폴더 또는 테이블이 있는지 확인하려면 메타데이터 받기 작업 필드에 지정합니다. `exists` 그런 다음 활동 `exists: true/false` 출력에서 결과를 확인할 수 있습니다. 필드 `exists` 목록에 지정되지 않은 경우 개체를 찾을 수 없는 경우 메타데이터 받기 작업이 실패합니다.

>[!NOTE]
>파일 저장소에서 메타데이터를 가져와 `modifiedDatetimeStart` `modifiedDatetimeEnd`구성하거나 `childItems` 구성하거나 구성하면 in output에는 지정된 범위 내에서 마지막으로 수정된 시간이 있는 지정된 경로에 파일만 포함됩니다. 에서 하위 폴더에 항목을 포함 하지 않습니다.

## <a name="syntax"></a>구문

**메타데이터 활동 받기**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**데이터 세트**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>형식 속성

현재 메타데이터 받기 활동은 다음과 같은 메타데이터 정보를 반환할 수 있습니다.

속성 | Description | 필수
-------- | ----------- | --------
fieldList | 필요한 메타데이터 정보의 유형입니다. 지원되는 메타데이터에 대한 자세한 내용은 이 문서의 [메타데이터 옵션](#metadata-options) 섹션을 참조하세요. | 예 
데이터 세트 | 메타데이터 받기 활동에서 메타데이터를 검색할 참조 데이터 집합입니다. 지원되는 커넥터에 대한 자세한 내용은 [기능](#capabilities) 섹션을 참조하십시오. 데이터 집합 구문 세부 정보는 특정 커넥터 항목을 참조하십시오. | 예
형식설정 | 형식 형식 데이터 집합을 사용할 때 적용됩니다. | 예
매장 설정 | 형식 형식 데이터 집합을 사용할 때 적용됩니다. | 예

## <a name="sample-output"></a>샘플 출력

메타데이터 받기 결과는 활동 출력에 표시됩니다. 다음은 광범위한 메타데이터 옵션을 보여 주며 두 가지 샘플입니다. 후속 활동에서 결과를 사용하려면 다음 패턴을 `@{activity('MyGetMetadataActivity').output.itemName}`사용합니다.

### <a name="get-a-files-metadata"></a>파일의 메타데이터 가져오기

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>폴더의 메타데이터 가져오기

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계
데이터 팩터리에서 지원하는 다른 제어 흐름 활동에 대해 알아봅니다.

- [파이프라인 활동 실행](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [조회 활동](control-flow-lookup-activity.md)
- [웹 활동](control-flow-web-activity.md)

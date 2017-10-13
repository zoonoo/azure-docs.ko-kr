---
title: "Azure Data Factory에서 메타데이터 가져오기 작업 | Microsoft Docs"
description: "SQL Server 저장 프로시저 작업을 사용하여 데이터 팩터리 파이프라인으로 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스에서 저장 프로시저를 호출하는 방법을 알아봅니다."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: shlo
ms.openlocfilehash: 99182b2ed91f6d60f499be0078077bf52fe8b366
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Azure Data Factory에서 메타데이터 가져오기 작업
GetMetadata 작업은 Azure Data Factory에 있는 모든 데이터의 메타데이터를 검색하는 데 사용할 수 있습니다. 이 작업은 Data Factory 버전 2에만 지원됩니다. 다음과 같은 시나리오에서 사용할 수 있습니다.

- 모든 데이터의 메타데이터 정보 유효성 검사
- 데이터가 준비되거나 사용할 수 있을 때 파이프라인 트리거

제어 흐름에서 다음 기능을 사용할 수 있습니다.
- GetMetadata 작업의 출력은 유효성 검사를 수행할 조건식에 사용할 수 있습니다.
- Do-Until 루프를 통해 조건이 충족되면 파이프라인을 트리거할 수 있습니다.

GetMetadata 작업에서는 데이터 집합을 필수 입력으로 사용하고 출력으로 사용할 수 있는 메타데이터 정보를 출력합니다. 현재 Azure Blob 데이터 집합만 지원됩니다. 지원되는 메타데이터 필드는 size, structure 및 lastModified 시간입니다.  

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory V1 설명서](v1/data-factory-introduction.md)를 참조하세요.


## <a name="syntax"></a>구문

### <a name="get-metadata-activity-definition"></a>메타데이터 가져오기 작업 정의:
다음 예제에서 GetMetadata 작업은 MyDataset로 표시된 데이터에 대한 메타데이터를 반환합니다. 

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
### <a name="dataset-definition"></a>데이터 집합 정의:

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
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>출력
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>형식 속성
현재 GetMetadata 작업은 Azure Sorage 데이터 집합에서 다음과 같은 유형의 메타데이터 정보를 가져올 수 있습니다.

속성 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
fieldList | 필요한 메타데이터 정보의 유형을 나열합니다.  | <ul><li>size</li><li>structure</li><li>lastModified</li></ul> |    아니요<br/>비어 있는 경우 작업은 지원되는 3개의 메타데이터 정보를 모두 반환합니다. 
데이터 집합 | GetMetadata 작업 시 메타데이터 작업을 검색할 참조 데이터 집합입니다. <br/><br/>현재 지원되는 데이터 집합 유형은 Azure Blob입니다. 다음은 두 개의 하위 속성입니다. <ul><li><b>referenceName</b>: 기존 Azure Blob 데이터 집합에 대한 참조입니다.</li><li><b>type</b>: 데이터 집합이 참조되므로 "DatasetReference" 형식입니다.</li></ul> |    <ul><li>문자열</li><li>DatasetReference</li></ul> | 예

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요. 

- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
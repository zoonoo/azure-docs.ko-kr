---
title: Azure Data Factory의 복사 작업
description: Azure Data Factory의 복사 작업에 대해 알아봅니다. 지원 되는 원본 데이터 저장소에서 지원 되는 싱크 데이터 저장소로 데이터를 복사 하는 데 사용할 수 있습니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: jingwang
ms.openlocfilehash: 8a84c9979bdfac1165d44d03572567ab1ea7ab1f
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995337"
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure Data Factory의 복사 작업

> [!div class="op_single_selector" title1="사용 중인 Data Factory의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-data-movement-activities.md)
> * [현재 버전](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory에서 복사 작업을 사용 하 여 온-프레미스 및 클라우드에 있는 데이터 저장소 간에 데이터를 복사할 수 있습니다. 데이터를 복사한 후 다른 활동을 사용 하 여 추가 변환 및 분석을 수행할 수 있습니다. 또한 복사 작업을 사용 하 여 BI (비즈니스 인텔리전스) 및 응용 프로그램 사용에 대 한 변환 및 분석 결과를 게시할 수 있습니다.

![복사 활동의 역할](media/copy-activity-overview/copy-activity.png)

복사 작업은 [통합 런타임에서](concepts-integration-runtime.md)실행 됩니다. 다양 한 데이터 복사 시나리오에 대해 다양 한 유형의 통합 런타임을 사용할 수 있습니다.

* 모든 IP에서 인터넷을 통해 공개적으로 액세스할 수 있는 두 데이터 저장소 간에 데이터를 복사 하는 경우 복사 작업에 Azure integration runtime을 사용할 수 있습니다. 이 통합 런타임은 안전 하 고 안정적 이며 확장 가능 하며 [전역적으로 사용할 수](concepts-integration-runtime.md#integration-runtime-location)있습니다.
* 온-프레미스에 있는 데이터 저장소에서 데이터를 복사 하는 경우 또는 액세스 제어를 사용 하는 네트워크 (예: Azure virtual network)에서 데이터를 복사 하는 경우 자체 호스팅 통합 런타임을 설정 해야 합니다.

통합 런타임을 각 원본 및 싱크 데이터 저장소와 연결 해야 합니다. 복사 작업에서 사용할 통합 런타임을 결정 하는 방법에 대 한 자세한 내용은 [사용할 IR 결정](concepts-integration-runtime.md#determining-which-ir-to-use)을 참조 하세요.

원본에서 싱크로 데이터를 복사 하기 위해 복사 작업을 실행 하는 서비스는 다음 단계를 수행 합니다.

1. 원본 데이터 저장소에서 데이터를 읽습니다.
2. Serialization/deserialization, 압축/압축 해제, 열 매핑 등을 수행 합니다. 입력 데이터 집합, 출력 데이터 집합 및 복사 작업의 구성에 따라 이러한 작업을 수행 합니다.
3. 싱크/대상 데이터 저장소에 데이터를 씁니다.

![복사 작업 개요](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>지원되는 데이터 저장소 및 형식

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>지원되는 파일 형식

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

복사 작업을 사용 하 여 두 파일 기반 데이터 저장소 간에 파일을 있는 그대로 복사할 수 있습니다 .이 경우 데이터는 직렬화 나 deserialization 없이 효율적으로 복사 됩니다. 또한 지정 된 형식의 파일을 구문 분석 하거나 생성할 수도 있습니다. 예를 들어 다음을 수행할 수 있습니다.

* SQL Server 데이터베이스에서 데이터를 복사 하 고 Parquet 형식으로 Azure Data Lake Storage Gen2에 씁니다.
* 온-프레미스 파일 시스템에서 텍스트 (CSV) 형식의 파일을 복사 하 여 Avro 형식으로 Azure Blob 저장소에 씁니다.
* 온-프레미스 파일 시스템에서 압축 된 파일을 복사 하 여 즉시 압축을 풀고 압축을 푼 파일을 Azure Data Lake Storage Gen2에 기록 합니다.
* Azure Blob storage에서 Gzip CSV (압축 텍스트) 형식으로 데이터를 복사 하 여 Azure SQL Database에 기록 합니다.
* Serialization/deserialization, 압축/압축 해제가 필요한 많은 작업이 있습니다.

## <a name="supported-regions"></a>지원되는 지역

복사 작업을 사용 하도록 설정 하는 서비스는 [Azure integration runtime 위치](concepts-integration-runtime.md#integration-runtime-location)에 나열 된 지역 및 지역에서 전역적으로 사용할 수 있습니다. 전역적으로 사용 가능한 토폴로지에서는 대개 지역 간 홉이 없는 효율적인 데이터 이동이 가능합니다. 특정 지역에서 Data Factory 및 데이터 이동의 가용성을 확인 하려면 [지역별 제품](https://azure.microsoft.com/regions/#services) 을 참조 하세요.

## <a name="configuration"></a>구성

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

일반적으로 Azure Data Factory의 복사 작업을 사용 하려면 다음을 수행 해야 합니다.

1. **원본 데이터 저장소 및 싱크 데이터 저장소에 대 한 연결 된 서비스를 만듭니다.** 지원 되는 커넥터 목록은이 문서의 [지원 되는 데이터 저장소 및 형식](#supported-data-stores-and-formats) 섹션에서 찾을 수 있습니다. 구성 정보 및 지원 되는 속성은 커넥터 문서의 "연결 된 서비스 속성" 섹션을 참조 하세요. 
2. **원본 및 싱크에 대 한 데이터 집합을 만듭니다.** 구성 정보 및 지원 되는 속성은 원본 및 싱크 커넥터 문서의 "데이터 집합 속성" 섹션을 참조 하세요.
3. **복사 작업을 사용 하 여 파이프라인을 만듭니다.** 다음 섹션에서 예제를 제공합니다.

### <a name="syntax"></a>구문

복사 작업의 다음 템플릿에는 지원 되는 속성의 전체 목록이 포함 되어 있습니다. 시나리오에 적합한 속성을 지정하세요.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>구문 세부 정보

| 속성 | 설명 | 필수 여부 |
|:--- |:--- |:--- |
| type | 복사 활동의 경우를로 설정 합니다. `Copy` | 예 |
| 입력 | 원본 데이터를 가리키는 만든 데이터 집합을 지정 합니다. 복사 작업은 단일 입력만 지원 합니다. | 예 |
| outputs | 싱크 데이터를 가리키는 만든 데이터 집합을 지정 합니다. 복사 작업은 단일 출력만 지원 합니다. | 예 |
| typeProperties | 속성을 지정 하 여 복사 작업을 구성 합니다. | 예 |
| source | 복사 원본 유형 및 데이터 검색을 위한 해당 속성을 지정 합니다.<br/>자세한 내용은 [지원 되는 데이터 저장소 및 형식](#supported-data-stores-and-formats)에 나열 된 커넥터 문서의 "복사 작업 속성" 섹션을 참조 하세요. | 예 |
| 싱크 | 데이터를 쓰기 위한 복사 싱크 유형과 해당 속성을 지정 합니다.<br/>자세한 내용은 [지원 되는 데이터 저장소 및 형식](#supported-data-stores-and-formats)에 나열 된 커넥터 문서의 "복사 작업 속성" 섹션을 참조 하세요. | 예 |
| 번역기 | 원본에서 싱크로의 명시적 열 매핑을 지정합니다. 이 속성은 기본 복사 동작이 사용자의 요구를 충족 하지 않는 경우에 적용 됩니다.<br/>자세한 내용은 [복사 활동의 스키마 매핑](copy-activity-schema-and-type-mapping.md)을 참조 하세요. | 아니요 |
| dataIntegrationUnits | [Azure integration runtime](concepts-integration-runtime.md) 에서 데이터 복사에 사용 하는 전력의 양을 나타내는 측정값을 지정 합니다. 이러한 단위는 이전에는 DMU (클라우드 데이터 이동 단위)로 알려져 있었습니다. <br/>자세한 내용은 [데이터 통합 단위](copy-activity-performance-features.md#data-integration-units)를 참조 하세요. | 아니요 |
| parallelCopies | 원본에서 데이터를 읽고 싱크에 데이터를 쓸 때 복사 작업에서 사용할 병렬 처리를 지정 합니다.<br/>자세한 내용은 [병렬 복사](copy-activity-performance-features.md#parallel-copy)를 참조 하세요. | 아니요 |
| 보존 | 데이터를 복사 하는 동안 메타 데이터/Acl을 유지할지 여부를 지정 합니다. <br/>자세한 내용은 [메타 데이터 유지](copy-activity-preserve-metadata.md)를 참조 하세요. |아니요 |
| enableStaging<br/>stagingSettings | 원본에서 싱크로 데이터를 직접 복사 하는 대신 Blob 저장소에서 중간 데이터를 준비할 지 여부를 지정 합니다.<br/>유용한 시나리오 및 구성 세부 정보에 대 한 자세한 내용은 [준비 된 복사](copy-activity-performance-features.md#staged-copy)를 참조 하세요. | 아니요 |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 원본에서 싱크로 데이터를 복사할 때 호환 되지 않는 행을 처리 하는 방법을 선택 합니다.<br/>자세한 내용은 [내결함성](copy-activity-fault-tolerance.md)을 참조 하세요. | 예 |

## <a name="monitoring"></a>모니터링

Azure Data Factory에서 실행 되는 복사 작업을 시각적으로 나 프로그래밍 방식으로 모니터링할 수 있습니다. 자세한 내용은 [복사 작업 모니터링](copy-activity-monitoring.md)을 참조 하세요.

## <a name="incremental-copy"></a>증분 복사

Data Factory를 사용 하면 원본 데이터 저장소에서 싱크 데이터 저장소로 델타 데이터를 증분 복사할 수 있습니다. 자세한 내용은 [자습서: 데이터 증분 복사](tutorial-incremental-copy-overview.md)를 참조 하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝

[복사 작업 모니터링](copy-activity-monitoring.md) 환경에는 각 작업 실행에 대 한 복사 성능 통계가 표시 됩니다. [복사 작업 성능 및 확장성 가이드](copy-activity-performance.md) 에서는 Azure Data Factory의 복사 작업을 통한 데이터 이동의 성능에 영향을 주는 주요 요소에 대해 설명 합니다. 또한 테스트 중 관찰 된 성능 값을 나열 하 고 복사 작업의 성능을 최적화 하는 방법을 설명 합니다.

## <a name="resume-from-last-failed-run"></a>마지막으로 실패 한 실행에서 다시 시작

복사 작업은 파일 기반 저장소 간에 이진 형식을 사용 하 여 큰 파일 크기를 그대로 복사 하 고, Amazon s 3에서 Azure Data Lake Storage Gen2로 데이터를 마이그레이션하기 위해 원본에서 싱크로 폴더/파일 계층 구조를 유지 하도록 선택 하는 경우 마지막으로 실패 한 실행에서 다시 시작을 지원 합니다. 이는 [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)및 [SFTP](connector-sftp.md)와 같은 파일 기반 커넥터에 적용 됩니다.

다음 두 가지 방법으로 복사 작업 다시 시작을 활용할 수 있습니다.

- **작업 수준 다시 시도:** 복사 작업에 대해 재시도 횟수를 설정할 수 있습니다. 파이프라인을 실행 하는 동안이 복사 작업이 실행 되지 않으면 다음 자동 다시 시도가 마지막 평가판의 오류 지점에서 시작 됩니다.
- **실패 한 작업에서 다시 실행:** 파이프라인 실행이 완료 된 후 ADF UI 모니터링 보기의 실패 한 작업에서 다시 실행을 트리거하거나 프로그래밍 방식을 실행할 수도 있습니다. 실패 한 작업이 복사 작업 인 경우 파이프라인은이 작업에서 다시 실행 될 뿐만 아니라 이전 실행의 오류 지점에서 다시 시작 합니다.

    ![이력서 복사](media/copy-activity-overview/resume-copy.png)

유의 사항:

- 다시 시작은 파일 수준에서 발생 합니다. 파일을 복사할 때 복사 작업이 실패 하는 경우 다음 실행에서이 특정 파일이 다시 복사 됩니다.
- 다시 시작이 제대로 작동 하려면 다시 실행 간에 복사 작업 설정을 변경 하지 마십시오.
- Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 및 Google Cloud Storage에서 데이터를 복사 하는 경우 복사 작업은 복사 된 파일의 임의 수에서 다시 시작할 수 있습니다. 원본으로 사용 되는 파일 기반 커넥터의 나머지 부분에서는 현재 복사 작업이 제한 된 개수의 파일에서 다시 시작을 지원 합니다 .이는 일반적으로 수십의 범위에 있고 파일 경로의 길이에 따라 달라 집니다. 이 값을 초과 하는 파일은 다시 실행 하는 동안 다시 복사 됩니다.

이진 파일 복사 이외의 다른 시나리오의 경우 복사 작업 다시 실행은 처음부터 시작 됩니다.

## <a name="preserve-metadata-along-with-data"></a>데이터와 함께 메타 데이터 유지

Data lake migration과 같은 시나리오에서 원본에서 싱크로 데이터를 복사 하는 동안 복사 작업을 사용 하 여 데이터와 함께 메타 데이터와 Acl을 유지 하도록 선택할 수도 있습니다. 자세한 내용은 [메타 데이터 유지](copy-activity-preserve-metadata.md) 를 참조 하세요.

## <a name="schema-and-data-type-mapping"></a>스키마 및 데이터 형식 매핑

복사 작업에서 원본 데이터를 싱크에 매핑하는 방법에 대 한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md) 을 참조 하세요.

## <a name="add-additional-columns-during-copy"></a>복사 하는 동안 다른 열 추가

원본 데이터 저장소에서 싱크로 데이터를 복사 하는 것 외에도 싱크에 따라 복사할 데이터 열을 추가 하도록를 구성할 수 있습니다. 예:

- 파일 기반 원본에서 복사 하는 경우 데이터를 가져온 파일에서 추적할 추가 열로 상대 파일 경로를 저장 합니다.
- 지정한 원본 열을 다른 열로 복제 합니다. 
- ADF 식으로 열을 추가 하 고, 파이프라인 이름/파이프라인 ID와 같은 ADF 시스템 변수를 연결 하거나, 업스트림 활동의 출력에서 다른 동적 값을 저장 합니다.
- 정적 값을 사용 하 여 다운스트림 소비 요구를 충족 하는 열을 추가 합니다.

복사 작업 원본 탭에서 다음 구성을 찾을 수 있습니다. 또한 정의 된 열 이름을 사용 하 여 일반적으로 복사 활동 [스키마 매핑에서](copy-activity-schema-and-type-mapping.md#schema-mapping) 이러한 추가 열을 매핑할 수 있습니다. 

![복사 작업에서 열 추가](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>이 기능은 최신 데이터 집합 모델에서 작동 합니다. UI에서이 옵션이 표시 되지 않으면 새 데이터 집합을 만들어 보세요.

프로그래밍 방식으로 구성 하려면 `additionalColumns` 복사 작업 원본에 속성을 추가 합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| additionalColumns | 추가 데이터 열을 추가 하 여 싱크에 복사 합니다.<br><br>배열의 각 개체 `additionalColumns` 는 추가 열을 나타냅니다. 는 `name` 열 이름을 정의 하 고은 해당 `value` 열의 데이터 값을 나타냅니다.<br><br>허용 되는 데이터 값은 다음과 같습니다.<br>- **`$$FILEPATH`** -예약 변수는 데이터 집합에 지정 된 폴더 경로에 소스 파일의 상대 경로를 저장 함을 나타냅니다. 파일 기반 원본에 적용 합니다.<br>- **`$$COLUMN:<source_column_name>`** -예약 된 변수 패턴은 지정한 원본 열을 다른 열로 복제 함을 나타냅니다.<br>- **식**<br>- **정적 값** | 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>싱크 테이블 자동 생성

SQL database/Azure Synapse Analytics로 데이터를 복사 하는 경우 대상 테이블이 존재 하지 않는 경우 복사 작업은 원본 데이터를 기반으로 하 여 자동으로 생성을 지원 합니다. 데이터 로드를 빠르게 시작 하 고 SQL database/Azure Synapse Analytics를 평가 하는 데 도움이 됩니다. 데이터 수집 후에는 필요에 따라 싱크 테이블 스키마를 검토 하 고 조정할 수 있습니다.

이 기능은 원본에서 다음 싱크 데이터 저장소로 데이터를 복사할 때 지원 됩니다. *ADF 제작 UI* – > *복사 작업 싱크* – > *테이블 옵션* – > *자동 생성 테이블*또는 `tableOption` 복사 작업 싱크 페이로드의 속성을 통해이 옵션을 찾을 수 있습니다.

- [Azure SQL Database](connector-azure-sql-database.md)
- [Azure SQL Database Managed Instance](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics(이전의 SQL Data Warehouse)](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![싱크 테이블 만들기](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>내결함성

기본적으로 복사 작업은 원본 데이터 행이 싱크 데이터 행과 호환 되지 않는 경우 데이터 복사를 중지 하 고 오류를 반환 합니다. 복사를 성공적으로 수행 하려면 호환 되지 않는 행을 건너뛰고 기록 하 고 호환 되는 데이터만 복사 하도록 복사 작업을 구성할 수 있습니다. 자세한 내용은 [복사 작업 내결함성](copy-activity-fault-tolerance.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계
다음 퀵 스타트, 자습서 및 샘플을 참조하세요.

- [동일한 Azure Blob storage 계정의 한 위치에서 다른 위치로 데이터를 복사 합니다.](quickstart-create-data-factory-dot-net.md)
- [Azure Blob 저장소에서 Azure SQL Database로 데이터 복사](tutorial-copy-data-dot-net.md)
- [SQL Server 데이터베이스에서 Azure로 데이터 복사](tutorial-hybrid-copy-powershell.md)

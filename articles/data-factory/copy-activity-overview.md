---
title: Azure 데이터 팩터리에서 활동 복사
description: Azure 데이터 팩터리의 복사 활동에 대해 알아봅니다. 지원되는 원본 데이터 저장소에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 데 사용할 수 있습니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: edb80c604951a140d21e3775eec3f1dc6d55af73
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421408"
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure 데이터 팩터리에서 활동 복사

> [!div class="op_single_selector" title1="사용 하는 데이터 팩터리의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-data-movement-activities.md)
> * [현재 버전](copy-activity-overview.md)

Azure 데이터 팩터리에서 복사 활동을 사용하여 온-프레미스 및 클라우드에 있는 데이터 저장소 간에 데이터를 복사할 수 있습니다. 데이터를 복사한 후 다른 활동을 사용하여 데이터를 추가로 변환하고 분석할 수 있습니다. 복사 활동을 사용하여 BI(비즈니스 인텔리전스) 및 응용 프로그램 사용에 대한 변환 및 분석 결과를 게시할 수도 있습니다.

![복사 활동의 역할](media/copy-activity-overview/copy-activity.png)

복사 활동은 [통합 런타임에](concepts-integration-runtime.md)실행됩니다. 다양한 데이터 복사 시나리오에 대해 다양한 유형의 통합 런타임을 사용할 수 있습니다.

* 모든 IP에서 인터넷을 통해 공개적으로 액세스할 수 있는 두 데이터 저장소 간에 데이터를 복사하는 경우 복사 활동에 Azure 통합 런타임을 사용할 수 있습니다. 이 통합 런타임은 안전하고 안정적이며 확장 가능하며 [전 세계적으로 사용할 수 있습니다.](concepts-integration-runtime.md#integration-runtime-location)
* 온-프레미스 또는 액세스 제어가 있는 네트워크(예: Azure 가상 네트워크)에 있는 데이터 저장소로 데이터를 복사하는 경우 자체 호스팅 통합 런타임을 설정해야 합니다.

통합 런타임은 각 원본 및 싱크 데이터 저장소와 연결되어야 합니다. 복사 활동이 사용할 통합 런타임을 결정하는 방법에 대한 자세한 내용은 [을 참조하여 사용할 IR을 확인합니다.](concepts-integration-runtime.md#determining-which-ir-to-use)

원본에서 싱크로 데이터를 복사하려면 복사 활동을 실행하는 서비스가 다음 단계를 수행합니다.

1. 원본 데이터 저장소에서 데이터를 읽습니다.
2. 직렬화/역직렬화, 압축/압축 해제, 열 매핑 등을 수행합니다. 입력 데이터 집합, 출력 데이터 집합 및 복사 활동의 구성에 따라 이러한 작업을 수행합니다.
3. 싱크/대상 데이터 저장소에 데이터를 씁니다.

![복사 작업 개요](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>지원되는 데이터 저장소 및 형식

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>지원 파일 형식

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

복사 활동을 사용하여 두 파일 기반 데이터 저장소 사이의 있는 것처럼 파일을 복사할 수 있으며, 이 경우 직렬화 또는 직렬화 없이 데이터를 효율적으로 복사할 수 있습니다. 또한 지정된 형식의 파일을 구문 분석하거나 생성할 수도 있습니다.

* 온-프레미스 SQL Server 데이터베이스에서 데이터를 복사하고 Parquet 형식으로 Azure Data Lake Storage Gen2에 씁니다.
* 온-프레미스 파일 시스템에서 텍스트(CSV) 형식으로 파일을 복사하고 Avro 형식으로 Azure Blob 저장소에 씁니다.
* 온-프레미스 파일 시스템에서 압축이 있는 파일을 복사하고, 즉시 압축을 풀고, 추출된 파일을 Azure Data Lake Storage Gen2에 씁니다.
* Azure Blob 저장소에서 Gzip 압축 텍스트(CSV) 형식의 데이터를 복사하여 Azure SQL 데이터베이스에 씁니다.
* 직렬화/역직렬화 또는 압축/압축 해제가 필요한 더 많은 활동.

## <a name="supported-regions"></a>지원되는 지역

복사 작업을 활성화하는 서비스는 [Azure 통합 런타임 위치에](concepts-integration-runtime.md#integration-runtime-location)나열된 지역 및 지역에서 전역적으로 사용할 수 있습니다. 전역적으로 사용 가능한 토폴로지에서는 대개 지역 간 홉이 없는 효율적인 데이터 이동이 가능합니다. [지역별 제품을](https://azure.microsoft.com/regions/#services) 참조하여 특정 지역의 데이터 팩터리 및 데이터 이동의 가용성을 확인합니다.

## <a name="configuration"></a>구성

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

일반적으로 Azure 데이터 팩터리에서 복사 작업을 사용하려면 다음을 수행해야 합니다.

1. **원본 데이터 저장소 및 싱크 데이터 저장소에 대해 연결된 서비스를 만듭니다.** 지원되는 커넥터 목록은 [지원되는 데이터 저장소 및](#supported-data-stores-and-formats) 이 문서의 형식 섹션에서 찾을 수 있습니다. 구성 정보 및 지원되는 속성은 커넥터 문서의 "연결된 서비스 속성" 섹션을 참조하십시오. 
2. **원본 및 싱크에 대한 데이터 집합을 만듭니다.** 구성 정보 및 지원되는 속성에 대한 소스 및 싱크 커넥터 문서의 "데이터 집합 속성" 섹션을 참조하십시오.
3. **복사 활동이 있는 파이프라인을 만듭니다.** 다음 섹션에서 예제를 제공합니다.

### <a name="syntax"></a>구문

복사 활동의 다음 템플릿에는 지원되는 속성의 전체 목록이 포함되어 있습니다. 시나리오에 적합한 속성을 지정하세요.

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

| 속성 | Description | 필수 여부 |
|:--- |:--- |:--- |
| type | 복사 활동의 경우`Copy` | 예 |
| inputs | 원본 데이터를 가리키는 데이터 집합을 지정합니다. 복사 활동은 단일 입력만 지원합니다. | 예 |
| outputs | 싱크 데이터를 가리키는 데이터 집합을 지정합니다. 복사 활동은 단일 출력만 지원합니다. | 예 |
| typeProperties | 복사 활동을 구성하는 속성을 지정합니다. | 예 |
| source | 복사 원본 유형과 데이터 검색을 위한 해당 속성을 지정합니다.<br/>자세한 내용은 [지원되는 데이터 저장소 및 형식에](#supported-data-stores-and-formats)나열된 커넥터 문서의 "활동 속성 복사" 섹션을 참조하십시오. | 예 |
| 싱크 | 복사 싱크 유형과 데이터 작성에 대한 해당 속성을 지정합니다.<br/>자세한 내용은 [지원되는 데이터 저장소 및 형식에](#supported-data-stores-and-formats)나열된 커넥터 문서의 "활동 속성 복사" 섹션을 참조하십시오. | 예 |
| 번역기 | 원본에서 싱크로의 명시적 열 매핑을 지정합니다. 이 속성은 기본 복사 동작이 요구 사항을 충족하지 않는 경우에 적용됩니다.<br/>자세한 내용은 [복사 활동의 스키마 매핑을](copy-activity-schema-and-type-mapping.md)참조하십시오. | 예 |
| dataIntegrationUnits | [Azure 통합 런타임이](concepts-integration-runtime.md) 데이터 복사본에 사용하는 전력량을 나타내는 측정값을 지정합니다. 이러한 단위는 이전에 클라우드 데이터 이동 단위(DMU)로 알려졌습니다. <br/>자세한 내용은 [데이터 통합 단위](copy-activity-performance-features.md#data-integration-units)를 참조하십시오. | 예 |
| parallelCopies | 원본에서 데이터를 읽고 싱크에 데이터를 쓸 때 복사 작업을 사용할 병렬처리를 지정합니다.<br/>자세한 내용은 [병렬 복사](copy-activity-performance-features.md#parallel-copy)를 참조하십시오. | 예 |
| 보존 | 데이터 복사 중에 메타데이터/ACL을 보존할지 여부를 지정합니다. <br/>자세한 내용은 [메타데이터 보존](copy-activity-preserve-metadata.md)을 참조하십시오. |예 |
| enableStaging<br/>stagingSettings | 원본에서 싱크로 데이터를 직접 복사하는 대신 Blob 저장소에 중간 데이터를 단계화할지 여부를 지정합니다.<br/>유용한 시나리오 및 구성 세부 정보에 대한 자세한 내용은 [준비된 복사본](copy-activity-performance-features.md#staged-copy)을 참조하십시오. | 예 |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 원본에서 싱크로 데이터를 복사할 때 호환되지 않는 행을 처리하는 방법을 선택합니다.<br/>자세한 내용은 [내결함성](copy-activity-fault-tolerance.md)을 참조하십시오. | 예 |

## <a name="monitoring"></a>모니터링

Azure Data Factory에서 실행되는 복사 작업을 시각적 및 프로그래밍 방식으로 모니터링할 수 있습니다. 자세한 내용은 [복사 활동 모니터를](copy-activity-monitoring.md)참조하십시오.

## <a name="incremental-copy"></a>증분 복사

데이터 팩터리에서는 원본 데이터 저장소에서 싱크 데이터 저장소로 델타 데이터를 증분 복사할 수 있습니다. 자세한 내용은 [자습서: 증분 복사 데이터를](tutorial-incremental-copy-overview.md)참조하십시오.

## <a name="performance-and-tuning"></a>성능 및 튜닝

[복사 활동 모니터링](copy-activity-monitoring.md) 환경은 실행된 각 활동에 대한 복사 성능 통계를 보여 줍니다. [복사 활동 성능 및 확장성 가이드는](copy-activity-performance.md) Azure Data Factory의 복사 작업을 통해 데이터 이동 성능에 영향을 주는 주요 요소를 설명합니다. 또한 테스트 중에 관찰된 성능 값을 나열하고 복사 작업의 성능을 최적화하는 방법에 대해서도 설명합니다.

## <a name="resume-from-last-failed-run"></a>마지막으로 실패한 실행에서 다시 시작

복사 활동은 파일 기반 저장소 간에 이진 형식으로 큰 크기의 파일을 복사하고 소스에서 싱크로 폴더/파일 계층 구조를 유지하도록 선택할 때 마지막으로 실패한 실행에서 다시 시작됩니다(예: Amazon S3에서 Azure Data Lake Storage Gen2로 데이터를 마이그레이션하는 경우). 다음 파일 기반 커넥터에 적용 됩니다: [아마존 S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure 데이터 레이크 스토리지 Gen1,](connector-azure-data-lake-store.md)Azure 데이터 호수 저장소 [Gen2,](connector-azure-data-lake-storage.md) [Azure 파일 저장소,](connector-azure-file-storage.md)파일 [시스템,](connector-file-system.md) [FTP,](connector-ftp.md) [Google 클라우드 스토리지,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md)및 [SFTP.](connector-sftp.md)

다음 두 가지 방법으로 복사 활동 이력서를 활용할 수 있습니다.

- **활동 수준 재시도:** 복사 활동에 대해 다시 시도 수를 설정할 수 있습니다. 파이프라인 실행 중에 이 복사 활동 실행이 실패하면 다음 자동 재시도가 마지막 평가판의 실패 지점에서 시작됩니다.
- **실패한 활동에서 다시 실행:** 파이프라인 실행이 완료된 후 ADF UI 모니터링 보기에서 실패한 활동에서 다시 실행하거나 프로그래밍 방식으로 트리거할 수도 있습니다. 실패한 활동이 복사 활동인 경우 파이프라인은 이 활동에서 다시 실행될 뿐만 아니라 이전 실행의 실패 지점에서다시 시작됩니다.

    ![이력서 복사](media/copy-activity-overview/resume-copy.png)

주의해야 할 몇 가지 사항은 다음과 같은 것입니다.

- 다시 시작은 파일 수준에서 수행됩니다. 다음 실행에서 파일을 복사할 때 복사 작업이 실패하면 이 특정 파일이 다시 복사됩니다.
- 다시 시작이 제대로 작동하려면 다시 실행 간에 복사 활동 설정을 변경하지 마십시오.
- Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 및 Google 클라우드 저장소에서 데이터를 복사하면 복사 된 파일의 임의 개수에서 복사 작업을 다시 시작할 수 있습니다. 나머지 파일 기반 커넥터의 나머지 소스에 대 한 동안 현재 복사 활동 지원 파일의 제한 된 수에서 다시 시작, 일반적으로 수만의 범위에서 파일 경로의 길이 따라 달라 집니다. 이 숫자를 초과하는 파일은 재실행 중에 다시 복사됩니다.

이진 파일 복사본 이외의 다른 시나리오의 경우 복사 활동 다시 실행이 처음부터 시작됩니다.

## <a name="preserve-metadata-along-with-data"></a>데이터와 함께 메타데이터 보존

원본에서 싱크로 데이터를 복사하는 동안 데이터 레이크 마이그레이션과 같은 시나리오에서는 복사 활동을 사용하는 데이터와 함께 메타데이터 및 ACL을 보존하도록 선택할 수도 있습니다. 자세한 내용은 [메타데이터 보존을](copy-activity-preserve-metadata.md) 참조하십시오.

## <a name="schema-and-data-type-mapping"></a>스키마 및 데이터 형식 매핑

복사 활동이 원본 데이터를 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 유형 매핑을](copy-activity-schema-and-type-mapping.md) 참조하십시오.

## <a name="add-additional-columns-during-copy"></a>복사하는 동안 열 추가

원본 데이터 저장소에서 싱크까지 데이터를 복사하는 것 외에도 싱크에 따라 복사할 데이터 열을 추가하도록 구성할 수도 있습니다. 다음은 그 예입니다.

- 파일 기반 원본에서 복사할 때 상대 파일 경로를 추가 열로 저장하여 데이터가 출처를 추적합니다.
- ADF 식이 있는 열을 추가하여 파이프라인 이름/파이프라인 ID와 같은 ADF 시스템 변수를 연결하거나 업스트림 활동의 출력에서 다른 동적 값을 저장합니다.
- 다운스트림 소비 요구를 충족하기 위해 정적 값이 있는 열을 추가합니다.

복사 활동 소스 탭에서 다음 구성을 찾을 수 있습니다. 

![복사 활동에 추가 열 추가](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>이 기능은 최신 데이터 집합 모델에서 작동합니다. UI에서 이 옵션이 표시되지 않으면 새 데이터 집합을 만들어 보십시오.

프로그래밍 방식으로 구성하려면 복사 `additionalColumns` 활동 원본에 속성을 추가합니다.

| 속성 | Description | 필수 |
| --- | --- | --- |
| 추가열 | 복사할 데이터 열을 추가하여 싱크합니다.<br><br>배열 아래의 `additionalColumns` 각 개체는 추가 열을 나타냅니다. 는 `name` 열 이름을 정의하고 `value` 해당 열의 데이터 값을 나타냅니다.<br><br>허용된 데이터 값은 다음과 같습니다.<br>- **`$$FILEPATH`**- 예약된 변수는 데이터 집합에 지정된 폴더 경로에 소스 파일의 상대 경로를 저장하도록 나타냅니다. 파일 기반 소스에 적용합니다.<br>- **식**<br>- **정적 값** | 예 |

**예제:**

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

## <a name="fault-tolerance"></a>내결함성

기본적으로 복사 활동은 데이터 복사를 중지하고 원본 데이터 행이 싱크 데이터 행과 호환되지 않을 때 오류를 반환합니다. 복사가 성공하도록 하려면 복사 활동을 구성하여 호환되지 않는 행을 건너뛰고 기록하고 호환되는 데이터만 복사할 수 있습니다. 자세한 내용은 [활동 내결함성 복사를](copy-activity-fault-tolerance.md) 참조하십시오.

## <a name="next-steps"></a>다음 단계
다음 퀵 스타트, 자습서 및 샘플을 참조하세요.

- [동일한 Azure Blob 저장소 계정의 한 위치에서 다른 위치로 데이터 복사](quickstart-create-data-factory-dot-net.md)
- [Azure Blob 저장소에서 Azure SQL 데이터베이스로 데이터 복사](tutorial-copy-data-dot-net.md)
- [온-프레미스 SQL Server 데이터베이스에서 Azure로 데이터 복사](tutorial-hybrid-copy-powershell.md)

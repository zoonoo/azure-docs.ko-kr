---
title: Azure Data Factory에서 parquet 형식 | Microsoft Docs
description: 이 항목에서는 Azure Data Factory에서 Parquet 형식으로 처리 하는 방법을 설명 합니다.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 360b794f0d8ba9c145a92f015f264eb624fbb0f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65144890"
---
# <a name="parquet-format-in-azure-data-factory"></a>Azure Data Factory에서 parquet 형식

하려는 경우이 문서를 따릅니다 **Parquet 파일을 구문 분석 하거나 Parquet 형식으로 데이터를 쓰려면**합니다. 

Parquet 형식 다음 커넥터에 사용할 수 있습니다. [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md)를 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)를 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)를 [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md)를 [FTP](connector-ftp.md)를 [Google 클라우드 Storage](connector-google-cloud-storage.md)를 [HDFS](connector-hdfs.md)를 [HTTP](connector-http.md), 및 [ SFTP](connector-sftp.md)합니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Parquet 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 자산         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| 형식             | 데이터 집합의 type 속성 설정 해야 합니다 **Parquet**합니다. | 예      |
| location         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 고유한 위치 형식이 및 지원 되는 속성에서 `location`합니다. **자세한 내용은 커넥터 문서에서-> 데이터 집합 속성 섹션**합니다. | 예      |
| compressionCodec | Parquet 파일에 쓸 때 사용할 압축 코덱. Parquet 파일을 읽을 때 Data Factory는 자동으로 파일 메타 데이터를 기반으로 압축 코덱을 결정 합니다.<br>지원 되는 유형은 "**none**","**gzip**","**snappy**" (기본값) 및 "**lzo**"입니다. Note 현재 복사 작업 LZO를 지원 하지 않습니다. | 아닙니다.       |

> [!NOTE]
> 열 이름에 공백 Parquet 파일에 대 한 지원 되지 않습니다.

다음은 Azure Blob Storage에서 Parquet 데이터 집합의 예:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Parquet 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="parquet-as-source"></a>Parquet 원본으로

복사 작업에서 다음 속성이 지원 됩니다 ***\*소스\**** 섹션입니다.

| 자산      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| 형식          | 복사 활동 source의 type 속성 설정 해야 합니다 **ParquetSource**합니다. | 예      |
| storeSettings | 그룹 데이터 저장소에서 데이터를 읽는 방법에 대 한 속성입니다. 각 파일 기반 커넥터 설정이 고유한 지원 되는 읽기에서 `storeSettings`합니다. **자세한 내용은 커넥터 문서에서 복사 활동 속성 섹션->** 합니다. | 아닙니다.       |

### <a name="parquet-as-sink"></a>Parquet으로 싱크

복사 작업에서 다음 속성이 지원 됩니다 ***\*싱크\**** 섹션입니다.

| 자산      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| 형식          | 복사 활동 source의 type 속성 설정 해야 합니다 **ParquetSink**합니다. | 예      |
| storeSettings | 그룹 데이터 저장소로 데이터를 작성 하는 방법에 대 한 속성입니다. 각 파일 기반 커넥터에서 자체 지원 되는 쓰기 설정이 `storeSettings`합니다. **자세한 내용은 커넥터 문서에서 복사 활동 속성 섹션->** 합니다. | 아닙니다.       |

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

자세한 내용은 [소스 변환](data-flow-source.md) 하 고 [변환 싱크](data-flow-sink.md) 매핑 데이터 흐름에서.

## <a name="data-type-support"></a>데이터 형식 지원

Parquet 복합 데이터 형식은 현재 없습니다 (예:: MAP, LIST, 구조체)를 지원 합니다.

## <a name="using-self-hosted-integration-runtime"></a>자체 호스팅된 통합 런타임을 사용 하 여

> [!IMPORTANT]
> 자체 호스팅 Integration Runtime에 권한을 부여한 복사(예: 온-프레미스 및 클라우드 데이터 저장소 간)의 경우 Parquet 파일을 **있는 그대로** 복사하지 않으면 IR 머신에 **64비트 JRE(Java Runtime Environment) 8 또는 OpenJDK**를 설치해야 합니다. 자세한 내용은 다음 단락을 참조하세요.

자체 호스팅 IR에서 Parquet 파일 직렬화/역직렬화를 사용하여 실행되는 복사의 경우 ADF는 먼저 JRE에 대한 *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 레지스트리를 검사하고, 없는 경우 OpenJDK에 대한 *`JAVA_HOME`* 시스템 변수를 검사하여 Java 런타임을 찾습니다.

- **JRE 사용**: 64비트 IR에는 64비트 JRE가 필요합니다. [여기](https://go.microsoft.com/fwlink/?LinkId=808605)서 찾을 수 있습니다.
- **OpenJDK 사용**: IR 버전 3.13부터 지원됩니다. 다른 모든 필수 OpenJDK 어셈블리와 함께 jvm.dll을 자체 호스팅 IR 머신으로 패키지하고, 이에 따라 JAVA_HOME 시스템 환경 변수를 설정합니다.

> [!TIP]
> 자체 호스팅 Integration Runtime을 사용하여 데이터를 Parquet 형식으로 또는 그 반대로 복사하고 “java를 호출할 때 오류가 발생함, 메시지: **java.lang.OutOfMemoryError:Java heap space**”라는 오류가 발생하는 경우 JVM의 최소/최대 힙 크기를 조정하도록 자체 호스팅 IR을 호스트하는 머신에서 `_JAVA_OPTIONS` 환경 변수를 추가하여 그러한 복사 기능을 강화한 다음, 파이프라인을 다시 실행할 수 있습니다.

![자체 호스팅 IR에서 JVM 힙 크기 설정](C:/AzureContent/azure-docs-pr/articles/data-factory/media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

예: 변수 `_JAVA_OPTIONS`를 `-Xms256m -Xmx16g` 값으로 설정합니다. 플래그 `Xms`는 JVM(Java Virtual Machine)의 초기 메모리 할당 풀을 지정하고, `Xmx`는 최대 메모리 할당 풀을 지정합니다. 즉, JVM은 `Xms`의 메모리 양으로 시작하고 최대 `Xmx`의 메모리 양을 사용할 수 있음을 의미합니다. 기본적으로 ADF는 최소 64MB 및 최대 1G를 사용합니다.

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
---
title: Azure 데이터 팩터리의 ORC 형식
description: 이 항목에서는 Azure 데이터 팩터리에서 ORC 형식을 처리하는 방법을 설명합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: e104c4c8e976207859b75212d5406558f04c6377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597493"
---
# <a name="orc-format-in-azure-data-factory"></a>Azure 데이터 팩터리의 ORC 형식

**ORC 파일을 구문 분석하거나 데이터를 ORC 형식으로 쓰려면**이 문서를 따르십시오. 

ORC 형식은 다음과 같은 커넥터에 대 한 지원 됩니다: [아마존 S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure 데이터 호수 스토리지 Gen1,](connector-azure-data-lake-store.md)Azure 데이터 호수 스토리지 [Gen2,](connector-azure-data-lake-storage.md) [Azure 파일 스토리지,](connector-azure-file-storage.md) [파일 시스템,](connector-file-system.md) [FTP,](connector-ftp.md) [구글 클라우드 스토리지,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP,](connector-http.md)및 [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 ORC 데이터 집합에서 지원하는 속성 목록을 제공합니다.

| 속성         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 형식 속성은 **Orc로**설정해야 합니다. | yes      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 고유한 위치 유형과 `location`지원되는 속성이 있습니다. **커넥터 문서 -> 데이터 집합 속성 섹션의 세부 정보를 참조하십시오.** | yes      |

다음은 Azure Blob 저장소의 ORC 데이터 집합의 예입니다.

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

다음 사항에 유의하세요.

* 복합 데이터 형식(구조체, 매핑, 목록, 공용 구조체)은 지원되지 않습니다.
* 열 이름에는 공백이 지원되지 않습니다.
* ORC 파일에는 3개의 [압축 관련 옵션](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)(NONE, ZLIB, SNAPPY)이 있습니다. Data Factory에서는 이러한 압축 형식으로 된 데이터를 ORC 파일에서 읽을 수 있습니다. 메타데이터에 있는 압축 코덱을 사용하여 데이터를 읽습니다. 그러나 ORC 파일에 쓸 때 Data Factory는 ORC에 대한 기본값인 ZLIB를 선택합니다. 현재 이 동작을 재정의할 수 있는 옵션은 없습니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 ORC 소스 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="orc-as-source"></a>ORC를 소스로

다음 속성은 복사 활동 *** \*소스\* *** 섹션에서 지원됩니다.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 활동 소스의 형식 속성은 **OrcSource**로 설정해야 합니다. | yes      |
| 매장 설정 | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 에서 `storeSettings`자체 지원되는 읽기 설정이 있습니다. **커넥터 문서 -> 복사 활동 속성 섹션의 세부 정보를 참조하십시오.** | 예       |

### <a name="orc-as-sink"></a>싱크대와 같은 ORC

다음 속성은 복사 활동 *** \*싱크\* *** 섹션에서 지원됩니다.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 활동 소스의 형식 속성은 **OrcSink**로 설정해야 합니다. | yes      |
| 매장 설정 | 데이터 저장소에 데이터를 작성하는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 에서 `storeSettings`자체 지원되는 쓰기 설정이 있습니다. **커넥터 문서 -> 복사 활동 속성 섹션의 세부 정보를 참조하십시오.** | 예       |

## <a name="using-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 사용

> [!IMPORTANT]
> 온-프레미스 및 클라우드 데이터 저장소 간에 자체 호스팅 통합 런타임을 통해 권한을 부여받은 복사본의 경우 ORC 파일을 **있는 그대로**복사하지 않는 경우 IR 컴퓨터에 **64비트 JRE 8(Java 런타임 환경) 또는 OpenJDK** 및 **Microsoft Visual C++ 2010 재배포 가능한 패키지를** 설치해야 합니다. 자세한 내용은 다음 단락을 확인하십시오.

ORC 파일 직렬화/역직렬화를 사용하여 자체 호스팅 IR에서 실행되는 복사본의 경우 ADF는 *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 먼저 JRE에 대한 레지스트리를 확인하여 *`JAVA_HOME`* Java 런타임을 찾습니다(찾을 수 없는 경우) OpenJDK에 대한 시스템 변수를 두 번째로 확인합니다.

- **JRE를 사용하려면**: 64 비트 IR은 64 비트 JRE가 필요합니다. [여기](https://go.microsoft.com/fwlink/?LinkId=808605)서 찾을 수 있습니다.
- **OpenJDK를 사용하려면**: IR 버전 3.13부터 지원됩니다. 다른 모든 필수 OpenJDK 어셈블리와 함께 jvm.dll을 자체 호스팅 IR 머신으로 패키지하고, 이에 따라 JAVA_HOME 시스템 환경 변수를 설정합니다.
- **Visual C++ 2010 재배포 가능 패키지 설치 :** Visual C++ 2010 재배포 가능 패키지는 자체 호스팅 IR 설치와 함께 설치되지 않습니다. [여기](https://www.microsoft.com/download/details.aspx?id=14632)서 찾을 수 있습니다.

> [!TIP]
> 자체 호스팅 통합 런타임을 사용하여 ORC 형식으로 데이터를 복사하고 "java.lang.OutOfMemoryError:Java **힙 공간"을** `_JAVA_OPTIONS` 호출할 때 오류가 발생했습니다.

![자체 호스팅 IR에서 JVM 힙 크기 설정](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

예: 변수 `_JAVA_OPTIONS`를 `-Xms256m -Xmx16g` 값으로 설정합니다. 플래그 `Xms`는 JVM(Java Virtual Machine)의 초기 메모리 할당 풀을 지정하고, `Xmx`는 최대 메모리 할당 풀을 지정합니다. 즉, JVM은 `Xms`의 메모리 양으로 시작하고 최대 `Xmx`의 메모리 양을 사용할 수 있음을 의미합니다. 기본적으로 ADF는 최소 64MB및 최대 1G를 사용합니다.

## <a name="next-steps"></a>다음 단계

- [활동 개요 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

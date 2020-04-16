---
title: 활동 성능 최적화 기능 복사
description: Azure 데이터 팩터리에서 복사 작업 성능을 최적화하는 데 도움이 되는 주요 기능에 대해 알아보십시오.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/09/2020
ms.openlocfilehash: fd7844340553809e1429097a9dda70f6bdb3e075
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414185"
---
# <a name="copy-activity-performance-optimization-features"></a>활동 성능 최적화 기능 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory에서 활용할 수 있는 복사 활동 성능 최적화 기능에 대해 간략하게 설명합니다.

## <a name="data-integration-units"></a>데이터 통합 단위

데이터 통합 단위는 Azure Data Factory에서 단일 단위의 전원(CPU, 메모리 및 네트워크 리소스 할당의 조합)을 나타내는 측정값입니다. 데이터 통합 단위는 [Azure 통합 런타임에만](concepts-integration-runtime.md#azure-integration-runtime)적용되지만 [자체 호스팅 통합 런타임에는](concepts-integration-runtime.md#self-hosted-integration-runtime)적용되지 않습니다.

복사 활동 실행에 권한을 부여하는 허용된 DIUs는 **2에서 256 사이입니다.** 지정하지 않거나 UI에서 "자동"을 선택하면 데이터 팩터리는 소스 싱크 쌍 및 데이터 패턴에 따라 최적의 DIU 설정을 동적으로 적용합니다. 다음 표에는 지원되는 DIU 범위와 다양한 복사 시나리오의 기본 동작이 나열되어 있습니다.

| 복사 시나리오 | 지원되는 DIU 범위 | 서비스에 따라 결정되는 기본 DIU |
|:--- |:--- |---- |
| 파일 저장소 간 |- **복사 또는 단일 파일로 복사**: 2-4 <br>- **파일의**수와 크기에 따라 2-256에서 여러 파일로 복사 <br><br>예를 들어 대용량 파일이 4개인 폴더에서 데이터를 복사하고 계층 구조를 유지하도록 선택하면 최대 유효 DIU는 16입니다. 파일을 병합하도록 선택하면 최대 유효 DIU는 4입니다. |파일의 수와 크기에 따라 4에서 32 사이 |
| 파일 저장소에서 비파일 저장소로 |- **단일 파일에서 복사**: 2-4 <br/>- **여러 파일에서 복사**: 파일의 수와 크기에 따라 2-256 <br/><br/>예를 들어 대용량 파일이 4개인 폴더에서 데이터를 복사하는 경우 최대 유효 DIU는 16입니다. |- **Azure SQL 데이터베이스 또는 Azure Cosmos DB로 복사**: 싱크 계층(DTUs/R) 및 소스 파일 패턴에 따라 4에서 16 사이<br>- PolyBase 또는 COPY 문을 사용하여 **Azure 시냅스 분석으로 복사:** 2<br>- 기타 시나리오: 4 |
| 비파일 저장소에서 파일 저장소로 |- **파티션 옵션 지원 데이터 저장소(Oracle** [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)포함): 폴더에 쓸 때 2-256, 단일 파일에 쓸 때 2-4로 복사합니다. 소스 데이터 파티션당 최대 4개의 DIUs를 사용할 수 있습니다.<br>- **기타 시나리오**: 2-4 |- **나머지 또는 HTTP에서 복사**: 1<br/>- 언로드를 사용하여 **아마존 레드 시프트에서 복사** : 2<br>- **기타 시나리오**: 4 |
| 파일이 아닌 저장소 간 |- **파티션 옵션 지원 데이터 저장소(Oracle** [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)포함): 폴더에 쓸 때 2-256, 단일 파일에 쓸 때 2-4로 복사합니다. 소스 데이터 파티션당 최대 4개의 DIUs를 사용할 수 있습니다.<br/>- **기타 시나리오**: 2-4 |- **나머지 또는 HTTP에서 복사**: 1<br>- **기타 시나리오**: 4 |

복사 활동 모니터링 보기 또는 활동 출력에서 각 복사본 실행에 사용되는 DIUs를 볼 수 있습니다. 자세한 내용은 [활동 모니터링 복사를](copy-activity-monitoring.md)참조하십시오. 이 기본값을 재정의하려면 다음과 `dataIntegrationUnits` 같이 속성에 대한 값을 지정합니다. 런타임 시 복사 작업에서 사용하는 *실제 DIU 수*는 데이터 패턴에 따라 구성된 값보다 작거나 같습니다.

사용 된 **DIU \* 복사 기간 \* 단가 /DIU 시간의 #** 요금이 부과됩니다. 현재 가격은 [여기를](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)참조하십시오. 구독 유형에 따라 현지 통화 및 별도의 할인이 적용될 수 있습니다.

**예제:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>자체 호스팅 통합 런타임 확장성

더 높은 처리량을 달성하려면 자체 호스팅 IR을 확장하거나 확장할 수 있습니다.

- 자체 호스팅 IR 노드의 CPU 및 사용 가능한 메모리가 완전히 사용되지 않지만 동시 작업의 실행이 한계에 도달하면 노드에서 실행할 수 있는 동시 작업 수를 늘려 확장해야 합니다.  지침은 [여기를](create-self-hosted-integration-runtime.md#scale-up) 참조하십시오.
- 반면에 자체 호스팅 IR 노드에서 CPU가 높거나 사용 가능한 메모리가 부족한 경우 새 노드를 추가하여 여러 노드에서 부하를 확장할 수 있습니다.  지침은 [여기를](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 참조하십시오.

다음 시나리오에서 단일 복사 활동 실행은 여러 자체 호스팅 IR 노드를 활용할 수 있습니다.

- 파일의 수와 크기에 따라 파일 기반 저장소에서 데이터를 복사합니다.
- 데이터 파티션 수에 따라 파티션 옵션 지원 데이터 [저장소(오라클,](connector-oracle.md#oracle-as-source) [네테자,](connector-netezza.md#netezza-as-source) [테라데이타,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP 테이블](connector-sap-table.md#sap-table-as-source)및 [SAP 오픈 허브](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)포함)에서 데이터를 복사합니다.

## <a name="parallel-copy"></a>병렬 복사

복사 활동에 병렬`parallelCopies` 복사(속성)를 설정하여 복사 활동을 사용할 병렬성을 나타낼 수 있습니다. 이 속성은 원본에서 읽거나 싱크 데이터 저장소에 병렬로 쓰는 복사 작업 내의 최대 스레드 수로 생각할 수 있습니다.

병렬 복사본은 [데이터 통합 단위](#data-integration-units) 또는 자체 호스팅 IR [노드에](#self-hosted-integration-runtime-scalability)대한 직교입니다. 모든 DIU 또는 자체 호스팅 IR 노드에서 계산됩니다.

각 복사 활동 실행에 대해 기본적으로 Azure Data Factory는 소스 싱크 쌍 및 데이터 패턴에 따라 최적의 병렬 복사 설정을 동적으로 적용합니다. 

> [!TIP]
> 병렬 복사본의 기본 동작은 일반적으로 소스 싱크 쌍, 데이터 패턴 및 DIU 수 또는 자체 호스팅 IR의 CPU/메모리/노드 수에 따라 ADF에 의해 자동으로 결정되는 최상의 처리량을 제공합니다. 병렬 [복사본을](copy-activity-performance-troubleshooting.md) 조정할 시기에 대한 문제 해결 복사 작업 성능을 참조하십시오.

다음 표에는 병렬 복사 동작이 나열되어 있습니다.

| 복사 시나리오 | 병렬 복사 동작 |
| --- | --- |
| 파일 저장소 간 | `parallelCopies`파일 수준에서 병렬 **처리가**결정됩니다. 각 파일 내의 청크는 자동으로 투명하게 아래에 발생합니다. 지정된 데이터 저장소 유형에 가장 적합한 청크 크기를 사용하여 데이터를 병렬로 로드하도록 설계되었습니다. <br/><br/>런타임에 사용하는 실제 병렬 복사본 복사 작업 수는 사용자가 사용하는 파일 수에 불과합니다. 복사 비헤이비어가 file 싱크에 **mergeFile인** 경우 복사 활동은 파일 수준 병렬성을 활용할 수 없습니다. |
| 파일 저장소에서 비파일 저장소로 | - Azure SQL Database 또는 Azure Cosmos DB로 데이터를 복사할 때 기본 병렬 복사본도 싱크 계층(DTUs/R)에 따라 달라집니다.<br>- Azure Table에 데이터를 복사할 때 기본 병렬 복사본은 4입니다. |
| 비파일 저장소에서 파일 저장소로 | - 파티션 옵션 지원 데이터 [저장소(오라클,](connector-oracle.md#oracle-as-source) [네테자,](connector-netezza.md#netezza-as-source) [테라데이타,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP 테이블](connector-sap-table.md#sap-table-as-source)및 [SAP 오픈 허브](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)포함)에서 데이터를 복사할 때 기본 병렬 복사본은 4입니다. 런타임에 사용하는 실제 병렬 복사 복사 작업 수는 데이터 파티션 수에 불과합니다. 자체 호스팅 통합 런타임을 사용하고 Azure Blob/ADLS Gen2에 복사하는 경우 IR 노드당 최대 유효 병렬 복사본은 4 또는 5입니다.<br>- 다른 시나리오의 경우 병렬 복사본이 적용되지 않습니다. 병렬 처리가 지정되더라도 적용되지 않습니다. |
| 파일이 아닌 저장소 간 | - Azure SQL Database 또는 Azure Cosmos DB로 데이터를 복사할 때 기본 병렬 복사본도 싱크 계층(DTUs/R)에 따라 달라집니다.<br/>- 파티션 옵션 지원 데이터 [저장소(오라클,](connector-oracle.md#oracle-as-source) [네테자,](connector-netezza.md#netezza-as-source) [테라데이타,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP 테이블](connector-sap-table.md#sap-table-as-source)및 [SAP 오픈 허브](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)포함)에서 데이터를 복사할 때 기본 병렬 복사본은 4입니다.<br>- Azure Table에 데이터를 복사할 때 기본 병렬 복사본은 4입니다. |

데이터 저장소를 호스트하는 컴퓨터의 부하를 제어하거나 복사 성능을 조정하려면 기본값을 재정의하고 `parallelCopies` 속성에 대한 값을 지정할 수 있습니다. 값은 1 이상의 정수여야 합니다. 런타임에 최상의 성능을 위해 복사 활동은 설정한 값보다 적거나 동일한 값을 사용합니다.

`parallelCopies` 속성값을 지정할 때 원본의 부하 증가를 고려하고 데이터 저장소를 싱크합니다. 또한 복사 활동에 의해 권한을 부여하는 경우 자체 호스팅 통합 런타임에 대한 부하 증가를 고려합니다. 이 부하 증가는 특히 동일한 데이터 저장소에 대해 실행되는 동일한 활동의 여러 활동 또는 동시 실행이 있는 경우에 발생합니다. 데이터 저장소 또는 자체 호스팅 통합 런타임이 부하에 과부하가 걸리지 `parallelCopies` 않으면 값을 줄이면 부하를 완화할 수 있습니다.

**예제:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>준비된 복사

원본 데이터 스토리지에서 싱크 데이터 스토리지에 데이터를 복사할 경우 중간 준비 스토리지로 Blob Storage를 사용하도록 선택할 수 있습니다. 준비는 다음과 같은 경우에 특히 유용합니다.

- **PolyBase를 통해 다양한 데이터 저장소의 데이터를 SQL 데이터 웨어하우스로 수집하려고 합니다.** SQL Data Warehouse는 많은 양의 데이터를 SQL Data Warehouse에 로드하는 처리량이 높은 메커니즘인 PolyBase를 사용합니다. 원본 데이터는 Blob 저장소 또는 Azure Data Lake 저장소에 있어야 하며 추가 기준을 충족해야 합니다. Blob Storage 또는 Azure Data Lake Store가 아닌 데이터 저장소에서 데이터를 로드하는 경우 중간 준비 Blob Storage를 통해 데이터 복사를 활성화할 수 있습니다. 이 경우 Azure Data Factory는 PolyBase의 요구 사항을 충족하는지 확인하기 위해 필요한 데이터 변환을 수행합니다. 그런 다음 PolyBase를 사용하여 데이터를 SQL Data Warehouse에 효과적으로 로드합니다. 자세한 내용은 [PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 참조하세요.
- **때로는 느린 네트워크 연결을 통해 온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 복사하는 하이브리드 데이터 이동을 수행하는 데 시간이 걸립니다.** 성능을 향상시키기 위해 준비된 복사본을 사용하여 온-프레미스에서 데이터를 압축하여 클라우드의 스테이징 데이터 저장소로 데이터를 이동하는 데 걸리는 시간을 줄여줄 수 있습니다. 그런 다음 대상 데이터 저장소에 로드하기 전에 스테이징 저장소의 데이터 압축을 풀 수 있습니다.
- **회사 IT 정책으로 인해 방화벽에서 포트 80 및 포트 443 이외의 포트를 열고 싶지 는 않습니다.** 예를 들어 온-프레미스 데이터 저장소에서 Azure SQL Database 싱크 또는 Azure SQL Data Warehouse 싱크에 데이터를 복사할 경우 Windows 방화벽 및 회사 방화벽 모두에 대한 포트 1433에서 아웃바운드 TCP 통신을 활성화해야 합니다. 이 시나리오에서 단계적 복사본은 자체 호스팅 된 통합 런타임을 활용하여 포트 443에서 HTTP 또는 HTTPS를 통해 Blob 저장소 스테이징 인스턴스에 데이터를 먼저 복사할 수 있습니다. 그런 다음 Blob 저장소 스테이징에서 SQL Database 또는 SQL 데이터 웨어하우스에 데이터를 로드할 수 있습니다. 이 흐름에서는 포트 1433을 사용하도록 설정하지 않아도 됩니다.

### <a name="how-staged-copy-works"></a>준비 복사의 작동 방법

준비 기능을 활성화하면 먼저 데이터가 원본 데이터 저장소에서 준비 Blob Storage(직접 준비)로 복사됩니다. 그 다음, 데이터가 준비 데이터 저장소에서 싱크 데이터 저장소로 복사됩니다. Azure 데이터 팩터리는 자동으로 2단계 흐름을 관리합니다. 또한 Azure Data Factory는 데이터 이동이 완료된 후 스테이징 저장소에서 임시 데이터를 정리합니다.

![준비된 복사](media/copy-activity-performance/staged-copy.png)

스테이징 저장소를 사용하여 데이터 이동을 활성화할 때 원본 데이터 저장소에서 중간 또는 스테이징 데이터 저장소로 데이터를 이동하기 전에 데이터를 압축할지 여부를 지정한 다음 중간 데이터 저장소또는 스테이징 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하기 전에 압축해제할 수 있습니다.

현재 는 준비된 복사본과 함께 또는 준비된 복사본없이 서로 다른 자체 호스팅 된 IRs를 통해 연결된 두 데이터 저장소 간에 데이터를 복사 할 수 없습니다. 이러한 시나리오의 경우 명시적으로 연결된 두 개의 복사 활동을 구성하여 소스에서 스테이징으로 복사한 다음 스테이징에서 싱크로 복사할 수 있습니다.

### <a name="configuration"></a>구성

복사 활동의 **enableStaging** 설정을 구성하여 대상 데이터 저장소에 로드하기 전에 Blob 저장소에 데이터를 스테이징할지 여부를 지정합니다. **을 사용하도록** 설정할 `TRUE`때 다음 표에 나열된 추가 속성을 지정합니다. 또한 Azure 저장소 또는 저장소 공유 액세스 서명 링크 된 서비스를 준비에 대 한 하나를 없는 경우 만들어야 합니다.

| 속성 | Description | 기본값 | 필수 |
| --- | --- | --- | --- |
| enableStaging |중간 준비 저장소를 통해 데이터를 복사할지 여부를 지정합니다. |False |예 |
| linkedServiceName |중간 준비 저장소로 사용할 Storage 인스턴스를 참조하여 이름을 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 연결 서비스로 지정합니다. <br/><br/> 공유 액세스 시그니처가 있는 저장소를 사용하여 PolyBase를 통해 SQL 데이터 웨어하우스에 데이터를 로드할 수 없습니다. 다른 모든 시나리오에서는 사용할 수 있습니다. |해당 없음 |예, **enableStaging**이 TRUE로 설정된 경우입니다. |
| 경로 |준비 데이터를 포함할 Blob Storage 경로를 지정합니다. 경로를 제공하지 않으면 서비스는 임시 데이터를 저장하는 컨테이너를 만듭니다. <br/><br/> 공유 액세스 서명을 포함한 스토리지를 사용하거나 특정 위치에 임시 데이터가 필요한 경우에만 경로를 지정합니다. |해당 없음 |예 |
| enableCompression |데이터를 대상으로 복사하기 전에 압축할지 여부를 지정합니다. 이 설정은 전송되는 데이터 양을 줄입니다. |False |예 |

>[!NOTE]
> 압축을 사용하도록 설정한 준비된 복사본을 사용하는 경우 Blob 연결 서비스 스테이징에 대한 서비스 주체 또는 MSI 인증이 지원되지 않습니다.

다음은 앞의 표에 설명된 속성이 있는 복사 활동에 대한 샘플 정의입니다.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>준비된 복사 청구 요소

복사 기간 및 복사 유형이라는 두 단계에 따라 요금이 청구됩니다.

* 클라우드 데이터 저장소에서 다른 클라우드 데이터 저장소로 데이터를 복사하는 클라우드 복사본 중에 스테이징을 사용하는 경우 Azure 통합 런타임에 의해 강화되는 두 단계 모두 [1단계 및 2단계의 복사 기간 합계] x [클라우드 복사 단가]가 청구됩니다.
* 온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 데이터를 복사하는 하이브리드 복사본 중에 스테이징을 사용하는 경우 자체 호스팅 통합 런타임으로 권한 부여되는 한 단계는 [하이브리드 복사 기간] x [하이브리드 복사 단가] + [클라우드 복사 기간] x [클라우드 복사 단가]에 대한 요금이 부과됩니다.

## <a name="next-steps"></a>다음 단계
다른 복사 활동 문서를 참조하십시오.

- [활동 개요 복사](copy-activity-overview.md)
- [복사 활동 성능 및 확장성 가이드](copy-activity-performance.md)
- [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)
- [Azure 데이터 팩터리를 사용하여 데이터 레이크 또는 데이터 웨어하우스에서 Azure로 데이터를 마이그레이션합니다.](data-migration-guidance-overview.md)
- [Amazon S3에서 Azure Storage로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
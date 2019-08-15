---
title: Azure Data Factory |의 복사 작업 성능 및 튜닝 가이드 Microsoft Docs
description: 복사 작업을 사용할 때 Azure Data Factory에서 데이터 이동의 성능에 영향을 주는 주요 요소에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: d8ce0a4f6bacdd1c8c858d474e6f3957a23c6357
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967367"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>복사 작업 성능 및 튜닝 가이드
> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-copy-activity-performance.md)
> * [현재 버전](copy-activity-performance.md)


Azure Data Factory 복사 작업은 최고 수준의 안전 하 고 안정적인 고성능 데이터 로드 솔루션을 제공 합니다. 이를 사용 하 여 다양 한 클라우드 및 온-프레미스 데이터 저장소에서 매일 수십 테라바이트의 데이터를 복사할 수 있습니다. 빠른 데이터 로드 성능은 핵심 빅 데이터 문제에 초점을 맞출 수 있도록 하는 핵심입니다. 고급 분석 솔루션을 구축 하 고 모든 데이터에서 심층 통찰력을 얻는 것입니다.

Azure는 엔터프라이즈급 데이터 저장소 및 데이터 웨어하우스 솔루션 집합을 제공 합니다. 복사 작업은 쉽게 구성 하 고 설정할 수 있는 매우 최적화 된 데이터 로드 환경을 제공 합니다. 단일 복사 작업을 사용 하 여 다음으로 데이터를 로드할 수 있습니다.

* 1\.2 GBps에서 Azure SQL Data Warehouse 합니다.
* 1\.0 GBps의 Azure Blob storage입니다.
* 1\.0 GBps에서 Azure Data Lake Store 합니다.

이 문서에서는 다음을 설명합니다.

* 프로젝트를 계획 하는 데 도움이 되는 지원 되는 원본 및 싱크 데이터 저장소에 대 한 [성능 참조 번호](#performance-reference) 입니다.
* 다양 한 시나리오에서 복사 처리량을 높일 수 있는 기능으로, dius ( [데이터 통합 단위](#data-integration-units) ), [병렬 복사](#parallel-copy), [준비 된 복사](#staged-copy)를 포함 합니다.
* 성능 조정 방법 및 복사 성능에 영향을 줄 수 있는 주요 요소에 대 한 성능 [조정 지침](#performance-tuning-steps)

> [!NOTE]
> 일반적으로 복사 작업에 익숙하지 않은 경우이 문서를 읽기 전에 [복사 작업 개요](copy-activity-overview.md) 를 참조 하세요.
>

## <a name="performance-reference"></a>성능 참조

다음 표에서는 내부 테스트를 기반으로 실행 되는 단일 복사 작업에서 지정 된 원본 및 싱크 쌍에 대 한 복사 처리량 번호 (MBps)를 보여 줍니다. 비교를 위해 [데이터 통합 단위](#data-integration-units) 또는 [자체 호스팅 integration runtime 확장성](concepts-integration-runtime.md#self-hosted-integration-runtime) (여러 노드)의 다양 한 설정이 복사 성능에 도움이 될 수 있는 방법도 보여 줍니다.

![성능 매트릭스](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Azure integration runtime에서 복사 작업을 실행 하는 경우 허용 되는 최소 데이터 통합 단위 (이전의 데이터 이동 단위)는 2입니다. 지정 하지 않으면 [데이터 통합 단위](#data-integration-units)에서 사용 되는 기본 데이터 통합 단위를 참조 하세요.

**주의할 사항:**

* 처리량은 다음 수식을 사용 하 여 계산 됩니다. [원본에서 읽은 데이터 크기]/[복사 작업 실행 기간].
* 테이블의 성능 참조 번호는 단일 복사 작업 실행에서 [TPC-H](http://www.tpc.org/tpch/) 데이터 집합을 사용 하 여 측정 되었습니다. 파일 기반 저장소에 대 한 테스트 파일은 크기가 10gb 인 여러 파일입니다.
* Azure 데이터 저장소에서는 원본 및 싱크는 동일한 Azure 지역에 있습니다.
* 온-프레미스 및 클라우드 데이터 저장소 간의 하이브리드 복사의 경우 각 자체 호스팅 통합 런타임 노드가 다음 사양을 사용 하 여 데이터 저장소와 분리 된 컴퓨터에서 실행 되었습니다. 단일 작업이 실행 중인 경우 복사 작업은 테스트 컴퓨터의 CPU, 메모리 또는 네트워크 대역폭의 작은 부분만 사용합니다.
    <table>
    <tr>
        <td>CPU</td>
        <td>32 코어 2.20GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>메모리</td>
        <td>128GB</td>
    </tr>
    <tr>
        <td>네트워크</td>
        <td>인터넷 인터페이스: 10Gbps; 인트라넷 인터페이스: 40Gbps</td>
    </tr>
    </table>


> [!TIP]
> 더 많은 DIUs를 사용 하 여 더 높은 처리량을 달성할 수 있습니다. 예를 들어 100 DIUs를 사용 하면 Azure Blob storage의 데이터를 1.0 GBps의 Azure Data Lake Store로 복사할 수 있습니다. 이 기능과 지원 되는 시나리오에 대 한 자세한 내용은 [데이터 통합 단위](#data-integration-units) 섹션을 참조 하세요. 

## <a name="data-integration-units"></a>데이터 통합 단위

데이터 통합 단위는 Azure Data Factory의 단일 단위에 대 한 전원 (CPU, 메모리 및 네트워크 리소스 할당의 조합)을 나타내는 척도입니다. 데이터 통합 단위는 [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime)에만 적용 되 고 [자체 호스팅 통합 런타임에](concepts-integration-runtime.md#self-hosted-integration-runtime)는 적용 되지 않습니다.

복사 작업 실행을 강화 하는 최소는 2입니다. 지정되지 않은 경우, 다양한 복사 시나리오에서 사용되는 기본 DIU가 나열된 다음 표를 참조하세요.

| 복사 시나리오 | 서비스에 따라 결정되는 기본 DIU |
|:--- |:--- |
| 파일 기반 저장소 간의 데이터 복사 | 파일의 수와 크기에 따라 4에서 32 사이 |
| Azure SQL Database 또는 Azure Cosmos DB에 데이터 복사 |싱크 Azure SQL Database의 또는 Cosmos DB 계층 (Dtu/RUs 수)에 따라 4에서 16 사이 |
| 다른 모든 복사 시나리오 | 4 |

이 기본값을 재정의하려면 **dataIntegrationUnits** 속성의 값을 다음과 같이 지정합니다. **DataIntegrationUnits** 속성에 허용 되는 *값* 은 최대 256입니다. 런타임 시 복사 작업에서 사용하는 *실제 DIU 수*는 데이터 패턴에 따라 구성된 값보다 작거나 같습니다. 특정 복사 원본 및 싱크에 대해 더 많은 단위를 구성할 때 얻을 수 있는 성능상 이점 수준에 대한 자세한 내용은 [성능 참조](#performance-reference)를 참조하세요.

작업 실행을 모니터링할 때 복사 작업 출력에서 각 복사 실행에 사용 되는 DIUs를 확인할 수 있습니다. 자세한 내용은 [복사 작업 모니터링](copy-activity-overview.md#monitoring)을 참조 하세요.

> [!NOTE]
> Azure Storage, Azure Data Lake Storage, Amazon S3, Google Cloud Storage, cloud FTP 또는 cloud SFTP에서 다른 클라우드 데이터 저장소로 여러 파일을 복사 하는 경우에만 현재 4 개 보다 큰 DIUs 설정이 적용 됩니다.
>

**예제**

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
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="data-integration-units-billing-impact"></a>데이터 통합 단위 청구 영향

복사 작업의 총 시간을 기준으로 요금이 청구 됩니다. 데이터 이동에 대해 청구 되는 총 기간은 DIUs의 기간 합계입니다. 두 클라우드 단위로 1시간이 걸렸던 복사 작업이 이제 8개의 클라우드에서 15분이 소요된다면 전체 청구 금액은 거의 동일한 상태로 유지됩니다.

## <a name="parallel-copy"></a>병렬 복사

**ParallelCopies** 속성을 사용 하 여 복사 작업에서 사용 하려는 병렬 처리를 나타낼 수 있습니다. 이 속성은 소스에서 읽거나 싱크 데이터 저장소에 동시에 쓸 수 있는 복사 작업 내의 최대 스레드 수로 간주할 수 있습니다.

각 복사 작업 실행에 대해 Azure Data Factory는 데이터를 원본 데이터 저장소에서 대상 데이터 저장소로 복사 하는 데 사용할 병렬 복사본 수를 결정 합니다. 사용 하는 병렬 복사의 기본 수는 사용 중인 원본 및 싱크의 유형에 따라 달라 집니다.

| 복사 시나리오 | 서비스에 의해 결정되는 기본 병렬 복사 개수 |
| --- | --- |
| 파일 기반 저장소 간의 데이터 복사 |는 파일의 크기와 두 클라우드 데이터 저장소 간에 데이터를 복사 하는 데 사용 된 DIUs의 수와 자체 호스팅 통합 런타임 컴퓨터의 물리적 구성에 따라 달라 집니다. |
| 모든 원본 저장소에서 Azure Table storage로 데이터 복사 |4 |
| 그 밖의 모든 복사 시나리오 |1 |

> [!TIP]
> 파일 기반 저장소 간에 데이터를 복사 하는 경우 기본 동작은 일반적으로 최상의 처리량을 제공 합니다. 기본 동작은 소스 파일 패턴에 따라 자동으로 결정 됩니다.

데이터 저장소를 호스트 하는 컴퓨터에서 로드를 제어 하거나 복사 성능을 조정 하기 위해 기본값을 재정의 하 고 **parallelCopies** 속성에 대 한 값을 지정할 수 있습니다. 값은 1 이상의 정수여야 합니다. 런타임 시 최상의 성능을 위해 복사 작업은 사용자가 설정한 값 보다 작거나 같은 값을 사용 합니다.

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

**주의할 사항:**

* 파일 기반 저장소 간에 데이터를 복사 하는 경우 **parallelCopies** 는 파일 수준에서 병렬 처리를 결정 합니다. 단일 파일 내의 청크는 자동으로 투명 하 게 발생 합니다. 지정 된 원본 데이터 저장소 형식에 가장 적합 한 청크 크기를 사용 하 여 데이터를 병렬로 로드 하 고 **parallelCopies**에 직교 하도록 디자인 되었습니다. 런타임 시 데이터 이동 서비스에서 복사 작업에 사용하는 병렬 복사의 실제 수는 사용자가 보유한 파일의 수를 넘지 않습니다. 복사 동작이 **Mergefile**인 경우 복사 작업은 파일 수준 병렬 처리를 사용할 수 없습니다.
* 파일 기반이 아닌 저장소 ( [Oracle](connector-oracle.md#oracle-as-source), [Teradata](connector-teradata.md#teradata-as-source), [Sap 테이블](connector-sap-table.md#sap-table-as-source)및 [sap Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) 커넥터를 사용 하는 원본으로 데이터 분할을 사용 하는 원본으로)에서 데이터를 복사 하는 경우 데이터 이동 서비스 **parallelCopies** 속성을 무시 합니다. 병렬 처리를 지정하더라도 이 경우에는 적용되지 않습니다.
* **ParallelCopies** 속성은 **dataIntegrationUnits**와 직교 합니다. 전자는 모든 데이터 통합 단위에서 계산됩니다.
* **ParallelCopies** 속성의 값을 지정 하는 경우 원본 및 싱크 데이터 저장소에 대 한 부하 증가를 고려해 야 합니다. 또한 하이브리드 복사의 경우와 같이 복사 작업을 통해 권한을 부여 하는 경우 자체 호스팅 통합 런타임에 대 한 부하 증가를 고려해 야 합니다. 이러한 부하가 증가 하는 것은 특히 동일한 데이터 저장소에 대해 실행 되는 동일한 활동의 여러 활동 또는 동시 실행이 있는 경우에 발생 합니다. 데이터 저장소 또는 자체 호스팅 통합 런타임이 부하가 많은 경우에는 부하를 완화 하기 위해 **parallelCopies** 값을 줄입니다.

## <a name="staged-copy"></a>준비된 복사

원본 데이터 스토리지에서 싱크 데이터 스토리지에 데이터를 복사할 경우 중간 준비 스토리지로 Blob Storage를 사용하도록 선택할 수 있습니다. 준비는 다음과 같은 경우에 특히 유용합니다.

- **PolyBase를 통해 다양 한 데이터 저장소의 데이터를 SQL Data Warehouse으로 수집 하려고 합니다.** SQL Data Warehouse는 많은 양의 데이터를 SQL Data Warehouse에 로드하는 처리량이 높은 메커니즘인 PolyBase를 사용합니다. 원본 데이터는 Blob storage 또는 Azure Data Lake Store에 있어야 하 고 추가 조건을 충족 해야 합니다. Blob Storage 또는 Azure Data Lake Store가 아닌 데이터 저장소에서 데이터를 로드하는 경우 중간 준비 Blob Storage를 통해 데이터 복사를 활성화할 수 있습니다. 이 경우 Azure Data Factory는 필요한 데이터 변환을 수행 하 여 PolyBase의 요구 사항을 충족 하는지 확인 합니다. 그런 다음 PolyBase를 사용하여 데이터를 SQL Data Warehouse에 효과적으로 로드합니다. 자세한 내용은 [PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 참조하세요.
- **저속 네트워크 연결을 통해 하이브리드 데이터 이동 (즉, 온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 복사)을 수행 하는 데 시간이 오래 걸리는 경우가 있습니다.** 성능을 향상 시키려면 준비 된 복사본을 사용 하 여 온-프레미스로 데이터를 압축 하 여 클라우드의 준비 데이터 저장소로 데이터를 이동 하는 데 걸리는 시간을 줄일 수 있습니다. 그런 다음 대상 데이터 저장소에 로드 하기 전에 준비 저장소에서 데이터의 압축을 해제할 수 있습니다.
- **회사 IT 정책 때문에 방화벽에서 포트 80 및 포트 443 이외의 포트를 열지 않으려고 합니다.** 예를 들어 온-프레미스 데이터 저장소에서 Azure SQL Database 싱크 또는 Azure SQL Data Warehouse 싱크에 데이터를 복사할 경우 Windows 방화벽 및 회사 방화벽 모두에 대한 포트 1433에서 아웃바운드 TCP 통신을 활성화해야 합니다. 이 시나리오에서 준비 된 복사는 자체 호스팅 integration runtime을 활용 하 여 포트 443에서 HTTP 또는 HTTPS를 통해 Blob storage 준비 인스턴스로 데이터를 먼저 복사할 수 있습니다. 그런 다음 Blob storage 스테이징에서 SQL Database 또는 SQL Data Warehouse에 데이터를 로드할 수 있습니다. 이 흐름에서는 포트 1433을 사용하도록 설정하지 않아도 됩니다.

### <a name="how-staged-copy-works"></a>준비 복사의 작동 방법

준비 기능을 활성화하면 먼저 데이터가 원본 데이터 저장소에서 준비 Blob Storage(직접 준비)로 복사됩니다. 그 다음, 데이터가 준비 데이터 저장소에서 싱크 데이터 저장소로 복사됩니다. Azure Data Factory은 2 단계 흐름을 자동으로 관리 합니다. 데이터 이동이 완료 된 후에도 준비 저장소에서 임시 데이터를 정리 하는 Azure Data Factory.

![준비된 복사](media/copy-activity-performance/staged-copy.png)

준비 저장소를 사용 하 여 데이터 이동을 활성화 하는 경우 원본 데이터 저장소에서 중간 또는 준비 데이터 저장소로 데이터를 이동 하기 전에 데이터를 압축할지 여부를 지정 하 고, 중간 또는 준비 dat에서 데이터를 이동 하기 전에 압축을 해제할 수 있습니다. 싱크 데이터 저장소에 대 한 저장소입니다.

현재 다른 자체 호스팅 IRs을 통해 연결 된 두 데이터 저장소 간에 데이터를 복사할 수 없습니다. 이러한 시나리오의 경우 원본에서 준비로 복사 하 고 스테이징에서 싱크로 복사 하기 위해 명시적으로 연결 된 복사 활동 두 개를 구성할 수 있습니다.

### <a name="configuration"></a>Configuration

복사 작업에서 **Enablestaging** 설정을 구성 하 여 데이터를 대상 데이터 저장소에 로드 하기 전에 Blob 저장소에 준비 해야 하는지 여부를 지정 합니다. **Enablestaging** 을로 `TRUE`설정 하는 경우 다음 표에 나열 된 추가 속성을 지정 합니다. 또한 준비에 대 한 Azure Storage 또는 저장소 공유 액세스 서명 연결 된 서비스가 없는 경우 만들어야 합니다.

| 속성 | 설명 | 기본값 | 필수 |
| --- | --- | --- | --- |
| enableStaging |중간 준비 저장소를 통해 데이터를 복사할지 여부를 지정합니다. |False |아니요 |
| linkedServiceName |중간 준비 저장소로 사용할 Storage 인스턴스를 참조하여 이름을 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 연결 서비스로 지정합니다. <br/><br/> PolyBase를 통해 SQL Data Warehouse에 데이터를 로드 하는 데 공유 액세스 서명이 포함 된 저장소를 사용할 수 없습니다. 다른 모든 시나리오에서는 사용할 수 있습니다. |해당 없음 |예, **enableStaging**이 TRUE로 설정된 경우입니다. |
| path |준비 데이터를 포함할 Blob Storage 경로를 지정합니다. 경로를 제공 하지 않으면 서비스에서 임시 데이터를 저장 하는 컨테이너를 만듭니다. <br/><br/> 공유 액세스 서명을 포함한 스토리지를 사용하거나 특정 위치에 임시 데이터가 필요한 경우에만 경로를 지정합니다. |해당 없음 |아니요 |
| enableCompression |대상에 복사 하기 전에 데이터를 압축 해야 하는지 여부를 지정 합니다. 이 설정은 전송되는 데이터 양을 줄입니다. |False |아니요 |

>[!NOTE]
> 압축을 사용 하는 준비 된 복사를 사용 하는 경우 스테이징 blob 연결 된 서비스에 대 한 서비스 주체 또는 MSI 인증이 지원 되지 않습니다.

위의 표에서 설명 하는 속성을 사용 하는 복사 작업의 샘플 정의는 다음과 같습니다.

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

복사 기간 및 복사 형식의 두 단계를 기준으로 요금이 청구 됩니다.

* 클라우드 복사 중에 준비를 사용 하 여 클라우드 데이터 저장소에서 다른 클라우드 데이터 저장소로 데이터를 복사 하는 경우, 두 단계 모두 Azure integration runtime에 의해 사용 되는 경우 [1 단계 및 2 단계에 대 한 복사 기간의 합계 합계] x [클라우드 복사 단가]에 대해 요금이 청구 됩니다.
* 온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 데이터를 복사 하는 하이브리드 복사 중에 준비를 사용 하는 경우 1 단계는 자체 호스팅 통합 런타임으로 사용 되며, [하이브리드 복사 기간] x [하이브리드 복사 단가] + [클라우드 복사 기간]에 대 한 요금이 청구 됩니다. x [클라우드 복사 단가].

## <a name="performance-tuning-steps"></a>성능 튜닝 단계

이러한 단계를 수행 하 여 복사 작업으로 Azure Data Factory 서비스의 성능을 조정 합니다.

1. **기준선을 설정 합니다.** 개발 단계에서 대표 데이터 샘플에 대 한 복사 작업을 사용 하 여 파이프라인을 테스트 합니다. [복사 작업 모니터링](copy-activity-overview.md#monitoring)을 수행 하 여 실행 세부 정보 및 성능 특징을 수집 합니다.

2. **성능을 진단 하 고 최적화 합니다.** 관찰 하는 성능이 예상과 일치 하지 않으면 성능 병목 상태를 확인 합니다. 그런 다음 성능을 최적화하여 병목 현상의 효과를 제거하거나 줄입니다.

    경우에 따라 복사 작업을 Azure Data Factory에서 실행 하면 다음 예제와 같이 [복사 작업 모니터링 페이지](copy-activity-overview.md#monitor-visually)위에 "성능 튜닝 팁" 메시지가 표시 됩니다. 메시지는 지정 된 복사 실행에 대해 확인 된 병목 상태를 알려 줍니다. 또한 복사 처리량을 향상 시키기 위해 변경 해야 하는 사항에 대해 안내 합니다. 성능 튜닝 팁은 현재 다음과 같은 제안 사항을 제공 합니다.

    - 데이터를 Azure SQL Data Warehouse에 복사할 때 PolyBase를 사용 합니다.
    - 데이터 저장소 쪽의 리소스가 병목 상태일 때 Azure Cosmos DB 요청 단위 또는 Azure SQL Database Dtu (데이터베이스 처리량 단위)를 늘립니다.
    - 불필요 하 게 준비 된 복사본을 제거 합니다.

    성능 튜닝 규칙도 점진적으로 보강됩니다.

    **예제: 성능 튜닝 팁을 사용 하 여 Azure SQL Database에 복사**

    이 샘플에서 복사를 실행 하는 동안 싱크 Azure SQL Database 높은 DTU 사용률에 도달 하 여 쓰기 작업의 속도를 저하 시키는 Azure Data Factory 합니다. 더 많은 Dtu를 사용 하 여 Azure SQL Database 계층을 늘리는 것이 좋습니다. 

    ![성능 튜닝 팁을 사용한 복사 모니터링](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    성능 튜닝과 함께 일반적으로 고려해야 하는 사항은 다음과 같습니다. 이 문서에서는 성능 진단에 대해 자세히 설명하지는 않습니다.

   * 성능 기능:
     * [병렬 복사](#parallel-copy)
     * [데이터 통합 단위](#data-integration-units)
     * [준비된 복사](#staged-copy)
     * [자체 호스팅 통합 런타임 확장성](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [자체 호스팅 통합 런타임](#considerations-for-self-hosted-integration-runtime)
   * [원본](#considerations-for-the-source)
   * [싱크](#considerations-for-the-sink)
   * [직렬화 및 역직렬화](#considerations-for-serialization-and-deserialization)
   * [압축](#considerations-for-compression)
   * [열 매핑](#considerations-for-column-mapping)
   * [기타 고려 사항](#other-considerations)

3. **전체 데이터 집합에 대 한 구성을 확장 합니다.** 실행 결과 및 성능에 만족 하는 경우 정의 및 파이프라인을 확장 하 여 전체 데이터 집합을 포함할 수 있습니다.

## <a name="considerations-for-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임에 대 한 고려 사항

복사 작업이 자체 호스팅 통합 런타임에서 실행 되는 경우 다음에 유의 하세요.

**설정**: 전용 컴퓨터를 사용 하 여 integration runtime을 호스트 하는 것이 좋습니다. [자체 호스팅 integration runtime 사용에 대 한 고려 사항을](concepts-integration-runtime.md)참조 하세요.

**규모 확장**: 하나 이상의 노드가 포함 된 단일 논리 자체 호스팅 통합 런타임은 동시에 여러 개의 복사 작업 실행을 지원할 수 있습니다. 동시 복사 작업 실행 수가 많거나 복사할 데이터 양이 많은 하이브리드 데이터 이동이 필요한 경우 복사를 위한 추가 리소스를 프로 비전 하려면 [자체 호스팅 통합 런타임을 확장 하는](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 것이 좋습니다.

## <a name="considerations-for-the-source"></a>원본에 대한 고려 사항

### <a name="general"></a>일반

기본 데이터 저장소는에서 실행 되는 다른 워크 로드에 의해 지나치게 부담 되지 않아야 합니다.

Microsoft 데이터 저장소의 경우 데이터 저장소와 관련 된 [모니터링 및 튜닝 항목](#performance-reference) 을 참조 하세요. 이러한 항목에서 데이터 저장소 성능 특성을 이해하고 응답 시간을 최소화하고 처리량을 최대화하는 방법을 파악할 수 있습니다.

* Blob 저장소에서 SQL Data Warehouse로 데이터를 복사 하는 경우에는 PolyBase를 사용 하 여 성능을 향상 하는 것이 좋습니다. 자세한 내용은 [PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 참조하세요.
* HDFS에서 Azure Blob 저장소 또는 Azure Data Lake Store로 데이터를 복사 하는 경우 DistCp를 사용 하 여 성능을 향상 시키는 것이 좋습니다. 자세한 내용은 [DistCp를 사용 하 여 HDFS에서 데이터 복사](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)를 참조 하세요.
* Redshift에서 Azure SQL Data Warehouse, Azure BLob storage 또는 Azure Data Lake Store로 데이터를 복사 하는 경우, UNLOAD를 사용 하 여 성능을 향상 시키는 것이 좋습니다. 자세한 내용은 [UNLOAD를 사용 하 여 Amazon Redshift에서 데이터 복사](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)를 참조 하세요.

### <a name="file-based-data-stores"></a>파일 기반 데이터 저장소

* **평균 파일 크기 및 파일 개수**: 복사 작업은 한 번에 하나의 파일에 데이터를 전송 합니다. 동일한 양의 데이터를 이동하는 경우 각 파일에 대한 부트스트랩 단계이기 때문에 적은 수의 큰 파일보다는 많은 수의 작은 파일로 데이터가 구성되는 경우 전체 처리량은 느려집니다. 가능 하면 작은 파일을 더 큰 파일에 결합 하 여 더 높은 처리량을 얻을 수 있습니다.
* **파일 형식 및 압축**: 성능을 향상하는 다양한 방법은 [직렬화 및 역직렬화에 대한 고려 사항](#considerations-for-serialization-and-deserialization) 및 [압축에 대한 고려 사항](#considerations-for-compression) 섹션을 참조하세요.

### <a name="relational-data-stores"></a>관계형 데이터 저장소

* **데이터 패턴**: 테이블 스키마는 복사본 처리량에 영향을 줍니다. 행 크기가 크면 동일한 양의 데이터를 복사 하는 데 작은 행 크기 보다 더 나은 성능을 제공 합니다. 원인은 데이터베이스가 적은 수의 행을 포함하는 더 적은 배치의 데이터보다 더욱 효율적으로 검색할 수 있기 때문입니다.
* **쿼리 또는 저장 프로시저**: 데이터를 보다 효율적으로 페치 하기 위해 복사 작업 원본에서 지정 하는 쿼리 또는 저장 프로시저의 논리를 최적화 합니다.

## <a name="considerations-for-the-sink"></a>싱크에 대한 고려 사항

### <a name="general"></a>일반

기본 데이터 저장소는에서 실행 되는 다른 워크 로드에 의해 지나치게 부담 되지 않아야 합니다.

Microsoft 데이터 저장소의 경우 데이터 저장소와 관련 된 [모니터링 및 튜닝 항목](#performance-reference) 을 참조 하세요. 이러한 항목에서 데이터 저장소 성능 특성을 이해하고 응답 시간을 최소화하고 처리량을 최대화하는 방법을 파악할 수 있습니다.

* 데이터 저장소에서 Azure SQL Data Warehouse로 데이터를 복사 하는 경우에는 PolyBase를 사용 하 여 성능을 향상 하는 것이 좋습니다. 자세한 내용은 [PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 참조하세요.
* HDFS에서 Azure Blob 저장소 또는 Azure Data Lake Store로 데이터를 복사 하는 경우 DistCp를 사용 하 여 성능을 향상 시키는 것이 좋습니다. 자세한 내용은 [DistCp를 사용 하 여 HDFS에서 데이터 복사](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)를 참조 하세요.
* Redshift에서 Azure SQL Data Warehouse, Azure Blob storage 또는 Azure Data Lake Store로 데이터를 복사 하는 경우, UNLOAD를 사용 하 여 성능을 향상 시키는 것이 좋습니다. 자세한 내용은 [UNLOAD를 사용 하 여 Amazon Redshift에서 데이터 복사](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)를 참조 하세요.

### <a name="file-based-data-stores"></a>파일 기반 데이터 저장소

* **복사 동작**: 다른 파일 기반 데이터 저장소에서 데이터를 복사 하는 경우 복사 작업에는 **Copybehavior** 속성을 통해 세 가지 옵션이 있습니다. 계층 구조를 유지하고 평면화하며 파일을 병합합니다. 계층 구조를 유지 또는 평면화하는 작업은 성능 오버 헤드가 거의 또는 전혀 발생하지 않는 반면 파일을 병합하는 작업은 성능 오버 헤드가 증가합니다.
* **파일 형식 및 압축**: 성능을 향상하는 다양한 방법은 [직렬화 및 역직렬화에 대한 고려 사항](#considerations-for-serialization-and-deserialization) 및 [압축에 대한 고려 사항](#considerations-for-compression) 섹션을 참조하세요.

### <a name="relational-data-stores"></a>관계형 데이터 저장소

* **복사 동작 및 성능 함축**: SQL 싱크에 데이터를 작성 하는 방법에는 여러 가지가 있습니다. [Azure SQL Database에 데이터를 로드 하는 모범 사례](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database)를 자세히 알아보세요.

* **데이터 패턴 및 배치 크기**:
  * 테이블 스키마는 복사본 처리량에 영향을 줍니다. 동일한 양의 데이터를 복사하려면 데이터베이스가 데이터에서 적은 배치를 보다 효율적으로 커밋할 수 있기 때문에 행 크기가 크면 행 크기가 작은 경우 보다 더 성능이 나아집니다.
  * 복사 작업은 일련의 일괄 처리로 데이터를 삽입 합니다. **writeBatchSize** 속성을 사용하여 배치에서 행 수를 설정할 수 있습니다. 데이터에 작은 크기의 행이 있으면 높은 값을 가진 **writeBatchSize** 속성을 설정하여 적은 수의 배치 오버헤드와 높은 처리량의 혜택을 얻을 수 있습니다. 데이터의 행 크기가 큰 경우 **writeBatchSize**를 늘릴 때 주의하세요. 이 값이 높으면 데이터베이스에 오버로드가 발생하여 복사에 실패할 수 있습니다.

### <a name="nosql-stores"></a>NoSQL 저장소

* **Table Storage**:
  * **파티션**: 인터리브 파티션에 데이터를 작성하면 성능이 크게 저하됩니다. 파티션 키를 기준으로 원본 데이터를 정렬 하 여 데이터가 다른 파티션에 효율적으로 삽입 되도록 합니다. 또는 데이터를 단일 파티션에 쓰도록 논리를 조정할 수 있습니다.

## <a name="considerations-for-serialization-and-deserialization"></a>직렬화 및 역직렬화에 대한 고려 사항

직렬화 및 deserialization은 입력 데이터 집합 또는 출력 데이터 집합이 파일인 경우에 발생할 수 있습니다. 복사 작업에서 지원 되는 파일 형식에 대 한 자세한 내용은 [지원 되는 파일 및 압축 형식](supported-file-formats-and-compression-codecs.md)을 참조 하세요.

**복사 동작**:

* 파일 기반 데이터 저장소 간에 파일 복사:
  * 입력 및 출력 데이터 집합에 모두 같거나 파일 형식 설정이 없는 경우 데이터 이동 서비스는 serialization 또는 deserialization 없이 *이진 복사* 를 실행 합니다. 원본 및 싱크 파일 형식 설정이 서로 다른 시나리오에 비해 더 나은 처리량이 나타납니다.
  * 입력 및 출력 데이터 집합이 모두 텍스트 형식이 고 인코딩 형식만 다른 경우 데이터 이동 서비스는 인코딩 변환만 수행 합니다. 이진 복사에 비해 성능 오버헤드를 유발하는 어떠한 직렬화 및 역직렬화도 수행하지 않습니다.
  * 입력 및 출력 데이터 집합에 서로 다른 파일 형식이 나 구분 기호와 같은 다른 구성이 있는 경우 데이터 이동 서비스는 원본 데이터를 deserialize 하 고 변환한 다음 사용자가 지정한 출력 형식으로 serialize 합니다. 이 작업으로 다른 시나리오에 비해 훨씬 더 심각한 성능 오버헤드가 발생합니다.
* 파일 기반 저장소에서 관계형 저장소로 파일을 기반으로 하지 않는 데이터 저장소 간에 파일을 복사 하는 경우에는 serialization 또는 deserialization 단계가 필요 합니다. 이 단계로 상당한 성능 오버헤드가 발생합니다.

**파일 형식**: 선택한 파일 형식에 따라 복사 성능이 달라질 수 있습니다. 예를 들어 Avro는 데이터를 사용하여 메타데이터를 저장하는 간단한 이진 형식입니다. 처리 및 쿼리에 대한 Hadoop 에코시스템을 광범위하게 지원합니다. Avro는 serialization 및 deserialization에 더 많은 비용이 들기 때문에 텍스트 형식에 비해 더 낮은 복사 처리량이 발생 합니다. 

파일 형식의 선택은 처리 흐름 전체적으로 수행합니다. 시작:

- 원본 데이터 저장소에 저장 되거나 외부 시스템에서 추출 되는 데이터의 형태입니다.
- 저장소, 분석 처리 및 쿼리에 가장 적합 한 형식입니다.
- 보고 및 시각화 도구를 위해 데이터 마트에 데이터를 내보내야 하는 형식입니다.

경우에 따라 읽기 및 쓰기 성능에 대해 최적이 아닌 파일 형식은 전체 분석 프로세스를 고려할 때 적합한 선택일 수 있습니다.

## <a name="considerations-for-compression"></a>압축에 대한 고려 사항

입력 또는 출력 데이터 집합이 파일인 경우 대상에 데이터를 쓸 때 복사 작업을 설정 하 여 압축 또는 압축 풀기를 수행할 수 있습니다. 압축을 선택할 때 입력/출력(I/O) 및 CPU 간에 균형을 유지합니다. 컴퓨팅 리소스에서 데이터를 압축하는 데 추가 비용이 발생합니다. 대신에, 네트워크 I/O 및 스토리지는 감소합니다. 데이터에 따라 전체 복사 처리량이 증가 하는 것을 볼 수 있습니다.

**코덱**: 압축 코덱에는 각각 장점이 있습니다. 예를 들어 bzip2는 가장 낮은 복사 처리량을 갖지만 처리를 위해 분할될 수 있으므로 bzip2로 최상의 Hive 쿼리 성능을 얻게 됩니다. Gzip은 가장 균형 있는 옵션이 며 가장 자주 사용 됩니다. 엔드투엔드 시나리오에 가장 적합한 코덱을 선택합니다.

**수준**: 각 압축 코덱의 경우 빠른 압축 및 최적 압축이라는 두 옵션 중에서 선택할 수 있습니다. 가장 빠른 압축 옵션은 결과 파일이 최적으로 압축 되지 않은 경우에도 최대한 빨리 데이터를 압축 합니다. 최적으로 압축된 옵션은 압축에 더 많은 시간을 사용하고 최소한의 데이터를 생성합니다. 두 옵션 모두 테스트하여 어떤 옵션이 사용자에게 더 나은 성능을 제공하는지 확인할 수 있습니다.

**고려 사항**: 온-프레미스 저장소와 클라우드 간에 더 많은 데이터를 복사하려면 압축을 활성화한 상태에서 [준비된 복사](#staged-copy)를 사용하는 것이 좋습니다. 중간 저장소를 사용 하면 회사 네트워크 및 Azure 서비스의 대역폭이 제한 요인이 고 입력 데이터 집합 및 출력 데이터 집합이 모두 압축 되지 않은 형식이 되도록 할 때 유용 합니다.

## <a name="considerations-for-column-mapping"></a>열 매핑에 대한 고려 사항

복사 작업에서 **columnmappings** 속성을 설정 하 여 입력 열의 전체 또는 하위 집합을 출력 열에 매핑할 수 있습니다. 데이터 이동 서비스는 원본에서 데이터를 읽은 후에 데이터를 싱크에 쓰기 전에 데이터에 열 매핑을 수행해야 합니다. 이 추가 처리는 복사 처리량을 감소시킵니다.

원본 데이터 스토리지를 쿼리할 수 있는 경우 예를 들어 SQL Database 또는 SQL Server와 같은 관계형 스토리지이거나 Table Storage 또는 Azure Cosmos DB와 같은 NoSQL 스토리지인 경우 열 매핑을 사용하는 대신, 열 필터링 및 재정렬 논리를 **query** 속성에 푸시하는 것이 좋습니다. 이러한 방식으로 데이터 이동 서비스가 원본 데이터 저장소에서 데이터를 읽는 동안 프로젝션이 발생 하며이는 훨씬 더 효율적입니다.

[Copy 활동 스키마 매핑](copy-activity-schema-and-type-mapping.md)에서 자세히 알아보세요.

## <a name="other-considerations"></a>기타 고려 사항

복사할 데이터의 크기가 큰 경우 데이터를 추가로 분할 하도록 비즈니스 논리를 조정할 수 있습니다. 실행 되는 각 복사 작업의 데이터 크기를 줄이기 위해 복사 작업이 더 자주 실행 되도록 예약할 수 있습니다.

동일한 데이터 저장소에 동시에 연결 하는 데 Azure Data Factory 해야 하는 데이터 집합 및 복사 작업 수에 주의 하세요. 많은 동시 복사 작업은 데이터 저장소를 제한하고 성능 저하, 복사 작업 내부 재시도 및 일부 경우 실행 오류를 발생시킬 수 있습니다.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>샘플 시나리오: 온-프레미스 SQL server에서 Blob 저장소로 복사

**시나리오**: 파이프라인은 온-프레미스 SQL server에서 Blob 저장소로 CSV 형식으로 데이터를 복사 하도록 빌드됩니다. 복사 작업을 더 빠르게 하려면 CSV 파일이 bzip2 형식으로 압축되어야 합니다.

**테스트 및 분석**: 복사 작업의 처리량은 2 MBps 미만이 며 성능 벤치 마크 보다 훨씬 느립니다.

**성능 분석 및 튜닝**: 성능 문제를 해결하기 위해 데이터가 처리되고 이동되는 방법을 살펴보겠습니다.

- **데이터 읽기**: Integration runtime이 SQL Server에 대 한 연결을 열고 쿼리를 보냅니다. SQL Server는 인트라넷을 통해 통합 런타임으로 데이터 스트림을 전송 하 여 응답 합니다.
- **데이터 직렬화 및 압축**: Integration runtime은 데이터 스트림을 CSV 형식으로 직렬화 하 고 데이터를 bzip2 스트림으로 압축 합니다.
- **데이터 쓰기**: 통합 런타임은 bzip2 스트림을 인터넷을 통해 Blob 저장소에 업로드 합니다.

여기서 볼 수 있듯이 데이터는 스트리밍 방식으로 처리 되 고 이동 됩니다. SQL Server > LAN > 통합 런타임 > WAN > Blob 스토리지 전반적인 성능은 파이프라인 전체의 최소 처리량에 의해 제어 됩니다.

![데이터 흐름](./media/copy-activity-performance/case-study-pic-1.png)

다음 중 하나 이상의 요인으로 성능 병목 현상이 발생할 수 있습니다.

* **원본**: SQL Server 자체가 과도한 로드로 인해 처리량이 낮아집니다.
* **자체 호스팅 통합 런타임**:
  * **LAN**: 통합 런타임이 SQL Server 머신에서 멀리 떨어져 있고 낮은 대역폭 연결을 사용합니다.
  * **통합 런타임**: 통합 런타임은 다음 작업을 수행하는 로드 제한에 도달했습니다.
    * **직렬화**: CSV 형식에 대한 데이터 스트림을 직렬화하면 처리량이 느려집니다.
    * **압축**: 코어 i7를 사용 하는 2.8 MBps 인 저속 압축 코덱 (예: bzip2)을 선택 했습니다.
  * **WAN**: 회사 네트워크와 Azure 서비스 간의 대역폭이 낮습니다 (예: T1 = 1544 kbps). T2 = 6312 kbps
* **싱크**: Blob 스토리지의 처리량이 낮습니다. 이 시나리오는 SLA (서비스 수준 계약)가 최소 60 MBps를 보장 하기 때문에 발생할 수 있습니다.

이 경우 bzip2 데이터 압축은 전체 파이프라인을 느리게 만들 수 있습니다. gzip 압축 코덱을 전환하면 병목 상태를 완화할 수 있습니다.

## <a name="references"></a>참조 항목

다음은 지원되는 데이터 저장소에 대한 몇 가지 성능 모니터링 및 튜닝 참조입니다.

* Blob storage 및 Table storage를 포함 하는 Azure Storage: [확장성 목표를 Azure Storage](../storage/common/storage-scalability-targets.md) 하 고 [성능 및 확장성 검사 목록을 Azure Storage](../storage/common/storage-performance-checklist.md)합니다.
* Azure SQL Database: [성능을 모니터링](../sql-database/sql-database-single-database-monitor.md) 하 고 DTU (데이터베이스 트랜잭션 단위) 비율을 확인할 수 있습니다.
* Azure SQL Data Warehouse: 해당 기능은 DWUs (데이터 웨어하우스 단위)로 측정 됩니다. [Azure SQL Data Warehouse에서 계산 능력 관리 (개요)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)를 참조 하세요.
* Azure Cosmos DB는 [Azure Cosmos DB 성능 수준](../cosmos-db/performance-levels.md)
* 온-프레미스 SQL Server: [성능을 모니터링 하 고 조정](https://msdn.microsoft.com/library/ms189081.aspx)합니다.
* 온-프레미스 파일 서버: [파일 서버 성능 조정](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조 하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 활동 스키마 매핑](copy-activity-schema-and-type-mapping.md)
- [복사 작업 내결함성](copy-activity-fault-tolerance.md)

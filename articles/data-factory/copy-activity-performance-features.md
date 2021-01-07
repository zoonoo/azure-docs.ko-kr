---
title: 복사 작업 성능 최적화 기능
description: Azure Data Factory 합니다 .에서 복사 작업 성능을 최적화 하는 데 도움이 되는 주요 기능에 대해 알아봅니다.
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
ms.date: 09/24/2020
ms.openlocfilehash: 8e46e9b323657b747fd73bad3b25ed66390f3aa9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324334"
---
# <a name="copy-activity-performance-optimization-features"></a>복사 작업 성능 최적화 기능

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory에서 활용할 수 있는 복사 작업 성능 최적화 기능에 대해 간략하게 설명 합니다.

## <a name="data-integration-units"></a>데이터 통합 단위

데이터 통합 단위는 Azure Data Factory의 단일 단위에 대 한 전원 (CPU, 메모리 및 네트워크 리소스 할당의 조합)을 나타내는 척도입니다. 데이터 통합 단위는 [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime)에만 적용 되 고 [자체 호스팅 통합 런타임에](concepts-integration-runtime.md#self-hosted-integration-runtime)는 적용 되지 않습니다.

복사 작업 실행을 강화 하는 데 허용 되는 DIUs는 **2에서 256 사이**입니다. 지정 하지 않거나 UI에서 "Auto"를 선택 하면 Data Factory는 소스 싱크 쌍 및 데이터 패턴에 따라 최적의 DIU 설정을 동적으로 적용 합니다. 다음 표에서는 다양 한 복사 시나리오에서 지원 되는 DIU 범위 및 기본 동작을 보여 줍니다.

| 복사 시나리오 | 지원 되는 DIU 범위 | 서비스에 따라 결정되는 기본 DIU |
|:--- |:--- |---- |
| 파일 저장소 간 |- **단일 파일에서 복사**: 2-4 <br>- 파일의 수와 크기에 따라 **여러 파일에 복사**: 2-256 <br><br>예를 들어 파일 크기가 4 개인 폴더에서 데이터를 복사 하 고 계층을 보존 하도록 선택 하는 경우 최대 유효 DIU는 16입니다. 파일 병합을 선택 하면 최대 유효 DIU가 4입니다. |파일의 수와 크기에 따라 4에서 32 사이 |
| 파일 저장소에서 비 파일 저장소로 |- **단일 파일에서 복사**: 2-4 <br/>- **여러 파일에서 복사**: 2-256 파일의 수와 크기에 따라 달라 집니다. <br/><br/>예를 들어 파일 크기가 4 개인 폴더에서 데이터를 복사 하는 경우 최대 유효 DIU는 16입니다. |- **Azure SQL Database 또는 Azure Cosmos DB에 복사**: 싱크 계층 (Dtu/RUs) 및 소스 파일 패턴에 따라 4에서 16 사이입니다.<br>- PolyBase 또는 COPY 문을 사용 하 여 **Azure Synapse Analytics에 복사** : 2<br>-기타 시나리오: 4 |
| 파일이 아닌 저장소에서 파일 저장소로 |- **파티션 옵션 사용 데이터 저장소** ( [AZURE SQL DATABASE](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source), [Teradata](connector-teradata.md#teradata-as-source)등)에서 복사 하 여 폴더에 쓸 때 2-256 및 2-4를 단일 파일에 쓸 때 참고로 원본 데이터 파티션만 최대 4 개의 DIUs를 사용할 수 있습니다.<br>- **기타 시나리오**: 2-4 |- **REST에서 복사 또는 HTTP**: 1<br/>- UNLOAD를 사용 하 여 **Amazon Redshift에서 복사** : 2<br>- **기타 시나리오**: 4 |
| 파일이 아닌 저장소 간 |- **파티션 옵션 사용 데이터 저장소** ( [AZURE SQL DATABASE](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source), [Teradata](connector-teradata.md#teradata-as-source)등)에서 복사 하 여 폴더에 쓸 때 2-256 및 2-4를 단일 파일에 쓸 때 참고로 원본 데이터 파티션만 최대 4 개의 DIUs를 사용할 수 있습니다.<br/>- **기타 시나리오**: 2-4 |- **REST에서 복사 또는 HTTP**: 1<br>- **기타 시나리오**: 4 |

복사 작업 모니터링 보기 또는 작업 출력에서 각 복사 실행에 사용 되는 DIUs를 볼 수 있습니다. 자세한 내용은 [복사 작업 모니터링](copy-activity-monitoring.md)을 참조 하세요. 이 기본값을 재정의 하려면 다음과 같이 속성의 값을 지정 `dataIntegrationUnits` 합니다. 런타임 시 복사 작업에서 사용하는 *실제 DIU 수*는 데이터 패턴에 따라 구성된 값보다 작거나 같습니다.

**사용 된 DIUs \* 복사 기간 \* 단가/dius 시간에 대 한**요금이 청구 됩니다. [여기](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)에서 현재 가격을 참조 하세요. 로컬 통화 및 별도의 크기 구독 유형별로 적용 될 수 있습니다.

**예:**

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

높은 처리량을 얻으려면 자체 호스팅 IR을 강화 하거나 규모를 확장할 수 있습니다.

- 자체 호스팅 IR 노드의 CPU 및 사용 가능한 메모리가 완전히 활용 되지 않지만 동시 작업의 실행이 한도에 도달 하는 경우에는 노드에서 실행할 수 있는 동시 작업 수를 늘려서 확장 해야 합니다.  지침은 [여기](create-self-hosted-integration-runtime.md#scale-up) 를 참조 하세요.
- 반면에 CPU가 자체 호스팅 IR 노드에 있거나 사용 가능한 메모리가 부족 한 경우에는 새 노드를 추가 하 여 여러 노드에서 부하를 확장할 수 있습니다.  지침은 [여기](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 를 참조 하세요.

참고 다음 시나리오에서 단일 복사 작업 실행은 자체 호스팅 IR 노드를 여러 개 활용할 수 있습니다.

- 파일의 수와 크기에 따라 파일 기반 저장소에서 데이터를 복사 합니다.
- 데이터 파티션 수에 따라 파티션 옵션 사용 데이터 저장소 ( [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [sap Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [sap Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)및 [Teradata](connector-teradata.md#teradata-as-source))에서 데이터를 복사 합니다.

## <a name="parallel-copy"></a>병렬 복사

복사 `parallelCopies` 작업에 대해 병렬 복사 (속성)를 설정 하 여 복사 작업에서 사용할 병렬 처리를 나타낼 수 있습니다. 이 속성은 소스에서 읽거나 싱크 데이터 저장소에 병렬로 쓰는 복사 작업 내의 최대 스레드 수로 간주할 수 있습니다.

병렬 복사는 [데이터 통합 단위나](#data-integration-units) [자체 호스팅 IR 노드와](#self-hosted-integration-runtime-scalability)직교 합니다. 모든 DIUs 또는 자체 호스팅 IR 노드에 걸쳐 계산 됩니다.

각 복사 작업 실행에 대해 기본적으로 Azure Data Factory는 원본 싱크 쌍 및 데이터 패턴에 따라 최적의 병렬 복사 설정을 동적으로 적용 합니다. 

> [!TIP]
> 병렬 복사의 기본 동작은 일반적으로 원본 싱크 쌍, 데이터 패턴 및 DIUs 또는 자체 호스팅 IR의 CPU/메모리/노드 수를 기반으로 ADF에 의해 자동으로 결정 되는 최상의 처리량을 제공 합니다. 병렬 복사를 튜닝할 때 [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md) 을 참조 하세요.

다음 표에서는 병렬 복사 동작을 보여 줍니다.

| 복사 시나리오 | 병렬 복사 동작 |
| --- | --- |
| 파일 저장소 간 | `parallelCopies`**파일 수준에서**병렬 처리를 결정 합니다. 각 파일 내의 청크는 자동으로 투명 하 게 발생 합니다. 지정 된 데이터 저장소 형식에 가장 적합 한 청크 크기를 사용 하 여 데이터를 병렬로 로드 하도록 설계 되었습니다. <br/><br/>런타임에 사용 되는 병렬 복사 복사 작업의 실제 수는 사용자가 보유 한 파일 수를 초과할 수 없습니다. 복사 동작이 파일 싱크에 대 한 **Mergefile** 인 경우 복사 작업은 파일 수준 병렬 처리를 사용할 수 없습니다. |
| 파일 저장소에서 비 파일 저장소로 | -Azure SQL Database 또는 Azure Cosmos DB 데이터를 복사 하는 경우 기본 병렬 복사도 싱크 계층 (Dtu/RUs 수)에 따라 달라 집니다.<br>-Azure 테이블에 데이터를 복사 하는 경우 기본 병렬 복사본은 4입니다. |
| 파일이 아닌 저장소에서 파일 저장소로 | -파티션 옵션 사용 데이터 저장소에서 데이터를 복사 하는 경우 ( [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [sap Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [sap Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)및 [Teradata](connector-teradata.md#teradata-as-source)포함) 기본 병렬 복사본은 4입니다. 런타임에 사용 되는 병렬 복사 복사 작업의 실제 수는 데이터 파티션 수를 초과 하지 않습니다. 자체 호스팅 Integration Runtime 사용 하 여 Azure Blob/ADLS Gen2에 복사 하는 경우 최대 유효 병렬 복사는 IR 노드당 4 또는 5입니다.<br>-다른 시나리오의 경우 병렬 복사는 적용 되지 않습니다. 병렬 처리를 지정 하더라도이는 적용 되지 않습니다. |
| 파일이 아닌 저장소 간 | -Azure SQL Database 또는 Azure Cosmos DB 데이터를 복사 하는 경우 기본 병렬 복사도 싱크 계층 (Dtu/RUs 수)에 따라 달라 집니다.<br/>-파티션 옵션 사용 데이터 저장소에서 데이터를 복사 하는 경우 ( [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [sap Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [sap Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)및 [Teradata](connector-teradata.md#teradata-as-source)포함) 기본 병렬 복사본은 4입니다.<br>-Azure 테이블에 데이터를 복사 하는 경우 기본 병렬 복사본은 4입니다. |

데이터 저장소를 호스트 하는 컴퓨터에서 로드를 제어 하거나 복사 성능을 조정 하려면 기본값을 재정의 하 고 속성에 대 한 값을 지정할 수 있습니다 `parallelCopies` . 값은 1 이상의 정수여야 합니다. 런타임 시 최상의 성능을 위해 복사 작업은 사용자가 설정한 값 보다 작거나 같은 값을 사용 합니다.

속성의 값을 지정 하는 경우 `parallelCopies` 원본 및 싱크 데이터 저장소에 대 한 부하 증가를 고려 합니다. 또한 복사 작업을 통해 권한을 부여 하는 경우 자체 호스팅 통합 런타임에 대 한 부하 증가를 고려해 야 합니다. 이러한 부하가 증가 하는 것은 특히 동일한 데이터 저장소에 대해 실행 되는 동일한 활동의 여러 활동 또는 동시 실행이 있는 경우에 발생 합니다. 데이터 저장소 또는 자체 호스팅 통합 런타임이 부하가 많은 경우에는 값을 줄여서 `parallelCopies` 로드를 완화 합니다.

**예:**

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

원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 복사 하는 경우 Azure Blob storage 또는 Azure Data Lake Storage Gen2을 임시 준비 저장소로 사용 하도록 선택할 수 있습니다. 준비는 다음과 같은 경우에 특히 유용합니다.

- **PolyBase를 통해 다양 한 데이터 저장소의 데이터를 Azure Synapse Analytics (이전의 SQL Data Warehouse)로 수집 하거나, 데이터를 눈송이로 복사 하거나, Amazon Redshift/HDFS performantly에서 데이터를 수집 하려고 합니다.** 다음에서 자세한 정보를 알아보세요.
  - [PolyBase를 사용 하 여 Azure Synapse Analytics로 데이터를 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics)합니다.
  - [눈송이 커넥터](connector-snowflake.md)
  - [Amazon Redshift 커넥터](connector-amazon-redshift.md)
  - [HDFS 커넥터](connector-hdfs.md)
- **회사 IT 정책 때문에 방화벽에서 포트 80 및 포트 443 이외의 포트를 열지 않으려고 합니다.** 예를 들어 온-프레미스 데이터 저장소에서 Azure SQL Database 또는 Azure Synapse Analytics로 데이터를 복사 하는 경우 Windows 방화벽 및 회사 방화벽 둘 다에 대해 포트 1433에서 아웃 바운드 TCP 통신을 활성화 해야 합니다. 이 시나리오에서 준비 된 복사는 자체 호스팅 integration runtime을 활용 하 여 포트 443에서 HTTP 또는 HTTPS를 통해 준비 저장소로 데이터를 복사한 다음 스테이징에서 SQL Database 또는 Azure Synapse Analytics로 데이터를 로드할 수 있습니다. 이 흐름에서는 포트 1433을 사용하도록 설정하지 않아도 됩니다.
- **저속 네트워크 연결을 통해 하이브리드 데이터 이동 (즉, 온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 복사)을 수행 하는 데 시간이 오래 걸리는 경우가 있습니다.** 성능을 향상 시키려면 준비 된 복사본을 사용 하 여 온-프레미스로 데이터를 압축 하 여 클라우드의 준비 데이터 저장소로 데이터를 이동 하는 데 걸리는 시간을 줄일 수 있습니다. 그런 다음 대상 데이터 저장소에 로드 하기 전에 준비 저장소에서 데이터의 압축을 해제할 수 있습니다.

### <a name="how-staged-copy-works"></a>준비 복사의 작동 방법

준비 기능을 활성화 하면 먼저 데이터가 원본 데이터 저장소에서 준비 저장소로 복사 됩니다 (사용자 고유의 Azure Blob 또는 Azure Data Lake Storage Gen2로 전환). 그런 다음 데이터가 스테이징에서 싱크 데이터 저장소로 복사 됩니다. Azure Data Factory 복사 작업은 2 단계 흐름을 자동으로 관리 하 고 데이터 이동이 완료 된 후에도 준비 저장소에서 임시 데이터를 정리 합니다.

![준비된 복사](media/copy-activity-performance/staged-copy.png)

준비 저장소를 사용 하 여 데이터 이동을 활성화 하는 경우 원본 데이터 저장소에서 준비 저장소로 데이터를 이동 하기 전에 데이터를 압축할지 여부를 지정한 다음 중간 또는 준비 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동 하기 전에 압축을 해제할 수 있습니다.

현재 다른 자체 호스팅 IRs을 통해 연결 된 두 데이터 저장소 간에 데이터를 복사할 수 없습니다. 이러한 시나리오의 경우 원본에서 준비로 복사 하 고 스테이징에서 싱크로 복사 하기 위해 명시적으로 연결 된 복사 활동 두 개를 구성할 수 있습니다.

### <a name="configuration"></a>구성

복사 작업에서 **Enablestaging** 설정을 구성 하 여 데이터를 대상 데이터 저장소에 로드 하기 전에 저장소에 준비 해야 하는지 여부를 지정 합니다. **Enablestaging** 을로 설정 하는 경우 `TRUE` 다음 표에 나열 된 추가 속성을 지정 합니다. 

| 속성 | 설명 | 기본값 | 필수 |
| --- | --- | --- | --- |
| enableStaging |중간 준비 저장소를 통해 데이터를 복사할지 여부를 지정합니다. |False |예 |
| linkedServiceName |임시 스테이징 저장소로 사용 하는 저장소 인스턴스를 참조 하는 [Azure Blob storage](connector-azure-blob-storage.md#linked-service-properties) 또는 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) 연결 된 서비스의 이름을 지정 합니다. |N/A |예, **enableStaging**이 TRUE로 설정된 경우입니다. |
| path |준비 된 데이터를 포함할 경로를 지정 합니다. 경로를 제공 하지 않으면 서비스에서 임시 데이터를 저장 하는 컨테이너를 만듭니다. |해당 없음 |아니요 |
| enableCompression |대상에 복사 하기 전에 데이터를 압축 해야 하는지 여부를 지정 합니다. 이 설정은 전송되는 데이터 양을 줄입니다. |False |예 |

>[!NOTE]
> 압축을 사용 하는 준비 된 복사를 사용 하는 경우 스테이징 blob 연결 된 서비스에 대 한 서비스 주체 또는 MSI 인증이 지원 되지 않습니다.

위의 표에서 설명 하는 속성을 사용 하는 복사 작업의 샘플 정의는 다음과 같습니다.

```json
"activities":[
    {
        "name": "CopyActivityWithStaging",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
            },
            "sink": {
                "type": "SqlDWSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path"
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>준비된 복사 청구 요소

복사 기간 및 복사 형식의 두 단계를 기준으로 요금이 청구 됩니다.

* 클라우드 복사 중에 준비를 사용 하 여 클라우드 데이터 저장소에서 다른 클라우드 데이터 저장소로 데이터를 복사 하는 경우, 두 단계 모두 Azure integration runtime에 의해 사용 되는 경우 [1 단계 및 2 단계에 대 한 복사 기간의 합계 합계] x [클라우드 복사 단가]에 대해 요금이 청구 됩니다.
* 온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 데이터를 복사 하는 하이브리드 복사 중에 준비를 사용 하는 경우 1 단계는 자체 호스팅 통합 런타임으로 사용 되며, [하이브리드 복사 기간] x [하이브리드 복사 단가] + [클라우드 복사 기간] x [클라우드 복사 단가]에 대해 요금이 청구 됩니다.

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 성능 및 확장성 가이드](copy-activity-performance.md)
- [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)
- [Azure Data Factory를 사용 하 여 data lake 또는 데이터 웨어하우스의 데이터를 Azure로 마이그레이션](data-migration-guidance-overview.md)
- [Amazon S3에서 Azure Storage로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
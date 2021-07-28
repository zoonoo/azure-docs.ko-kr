---
title: Azure Data Factory로 연결
description: 이 문서에서는 Azure Data Factory 및 Azure Purview를 연결하여 데이터 계보를 추적하는 방법을 설명합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/24/2021
ms.openlocfilehash: 9d728160413d470383f3eee78fc430aed0125ed7
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107905237"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Azure Data Factory와 Azure Purview를 연결하는 방법

이 문서에서는 데이터 계보를 추적하기 위해 Azure Purview 계정에 Azure Data Factory 계정을 연결하는 데 필요한 단계를 설명합니다. 또한 이 문서에서는 검사 범위 및 지원되는 계보 패턴에 대한 세부 정보를 제공합니다.

## <a name="view-existing-data-factory-connections"></a>기존 Data Factory 연결 보기

여러 Azure Data Factory는 단일 Azure Purview Data Catalog에 연결하여 계보 정보를 푸시할 수 있습니다. 현재 한도를 사용하면 Purview 관리 센터에서 한 번에 10개의 Data Factory 계정을 연결할 수 있습니다. Purview Data Catalog에 연결된 Data Factory 계정 목록을 표시하려면 다음을 수행합니다.

1. 왼쪽 탐색 창에서 **관리 센터** 를 선택합니다.
2. **외부 연결** 에서 **Data Factory 연결** 을 선택합니다.
3. Data Factory 연결 목록이 표시됩니다.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="데이터 팩터리 연결 목록을 보여주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. 연결 **상태** 에 대한 다양한 값을 확인합니다.

    - **연결됨**: 데이터 팩터리가 데이터 카탈로그에 연결됩니다.
    - **연결 끊김**: 데이터 팩터리가 카탈로그에 액세스할 수 있지만 다른 카탈로그에 연결되어 있습니다. 따라서 데이터 계보는 카탈로그에 자동으로 보고되지 않습니다.
    - **액세스 불가**: 현재 사용자에게 데이터 팩터리에 대한 액세스 권한이 없으므로 연결 상태를 알 수 없습니다.
 >[!Note]
 >Data Factory 연결을 보려면 Purview 역할 중 하나를 할당해야 합니다. 관리 그룹에서의 역할 상속은 **지원되지 않습니다**.
 >- 참가자
 >- 소유자
 >- 읽기 권한자
 >- 사용자 액세스 관리자

## <a name="create-new-data-factory-connection"></a>새 Data Factory 연결을 만듭니다.

>[!Note]
>Data Factory 연결을 추가하거나 제거하려면 Purview 역할 중 하나를 할당해야 합니다. 관리 그룹에서의 역할 상속은 **지원되지 않습니다**.
>- 소유자
>- 사용자 액세스 관리자
>
> 뿐만 아니라 사용자가 Data Factory의 "소유자" 또는 "참가자" 여야 합니다. 

Purview Data Catalog에 기존 Data Factory 계정을 연결하려면 다음 단계를 수행합니다.

1. 왼쪽 탐색 창에서 **관리 센터** 를 선택합니다.
2. **외부 연결** 에서 **Data Factory 연결** 을 선택합니다.
3. **Data Factory 연결** 페이지에서 **새로 만들기** 를 선택합니다.

4. 목록에서 Data Factory 계정을 선택하고 **확인** 을 선택합니다. 구독 이름을 기준으로 필터링하여 목록을 제한할 수도 있습니다.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Azure Data Factory 연결하는 방법을 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    데이터 팩터리가 현재 Purview 계정에 이미 연결되어 있거나 데이터 팩터리에 관리 ID가 없는 경우 일부 Data Factory 인스턴스를 사용하지 못할 수 있습니다.

    선택한 데이터 팩터리가 Purview 범위 계정에 이미 연결되어 있으면 경고 메시지가 표시됩니다. 확인을 선택하여 다른 부서의 범위 계정에 대한 Data Factory 연결을 끊습니다. 추가 확인은 필요하지 않습니다.


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Azure Data Factory 연결을 끊을 경고를 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>이제 10개 이하의 데이터 팩터리를 한 번에 추가할 수 있습니다. 10개 이상의 데이터 팩터리를 한 번에 추가하려면 지원 티켓을 제출하세요.

### <a name="how-does-the-authentication-work"></a>인증은 어떻게 작동합니까?

Purview 사용자가 액세스 권한이 있는 Data Factory를 등록하면 백 엔드에서 다음 작업이 수행됩니다.

1. **Data Factory 관리 ID** 는 Purview RBAC 역할에 추가됩니다.: **Purview Data 큐레이터**.

    :::image type="content" source="./media/how-to-link-azure-data-factory/adf-msi.png" alt-text="Azure Data Factory MSI를 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/adf-msi.png":::
     
2. 계보 메타데이터를 Purview에 다시 푸시할 수 있도록 Data Factory 파이프라인을 다시 실행해야 합니다.
3. 실행 후 Data Factory 메타데이터가 Purview에 푸시됩니다.

### <a name="remove-data-factory-connections"></a>데이터 팩터리 연결 제거
데이터 팩터리 연결을 제거하려면 다음을 수행합니다.

1. **Data Factory 연결** 페이지에서 하나 이상의 데이터 팩터리 연결 옆에 있는 **제거** 단추를 선택합니다.
2. 선택한 데이터 팩터리 연결을 삭제하려면 팝업에서 **확인** 을 선택합니다.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="데이터 팩터리를 선택하여 연결을 제거하는 방법을 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-integration-runtime-to-collect-lineage"></a>자체 호스팅 Integration Runtime를 구성하여 계보 수집

Data Factory 복사 작업의 계보는 SQL 데이터베이스와 같은 온-프레미스 데이터 저장소에 사용 가능합니다. Azure Data Factory를 사용하여 데이터 이동에 대해 자체 호스팅 통합 런타임을 실행하고 Azure Purview에서 계보를 캡처하려면 버전이 5.0 이상인지 확인합니다. 자체 호스팅 통합 런타임에 대한 자세한 내용은 [자체 호스팅 통합 런타임을 만들고 구성하는 방법](../data-factory/create-self-hosted-integration-runtime.md)을 참조하세요.

## <a name="supported-azure-data-factory-activities"></a>지원되는 Azure Data Factory 작업

Azure Purview는 다음과 같은 Azure Data Factory 작업에서 런타임 계보를 캡처합니다.

- [데이터 복사](../data-factory/copy-activity-overview.md)
- [데이터 흐름](../data-factory/concepts-data-flow-overview.md)
- [SSIS 패키지 실행](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

> [!IMPORTANT]
> 원본 또는 대상이 지원되지 않는 데이터 스토리지 시스템을 사용하는 경우 Azure Purview가 계보를 삭제합니다.

Data Factory와 Purview 간의 통합은 다음 섹션에 설명된 대로 Data Factory에서 지원하는 데이터 시스템의 하위 집합만 지원합니다.

### <a name="data-factory-copy-activity-support"></a>Data Factory 복사 작업 지원

| 데이터 저장소 | 지원됨 | 
| ------------------- | ------------------- | 
| Azure Blob Storage | 예 |
| Azure Cognitive Search | Yes | 
| Azure Cosmos DB (SQL API) \* | Yes | 
| Azure Cosmos DB의 API for MongoDB \* | Yes |
| Azure 데이터 탐색기 \* | 예 | 
| Azure Data Lake Storage Gen1 | 예 | 
| Azure Data Lake Storage Gen2 | 예 | 
| Azure Database for Maria DB \* | Yes | 
| Azure Database for MySQL \* | Yes | 
| Azure Database for PostgreSQL \* | 예 |
| Azure File Storage | 예 | 
| Azure SQL 데이터베이스 \* | Yes | 
| Azure SQL Managed Instance \* | Yes | 
| Azure Synapse Analytics \* | 예 | 
| Azure Table Storage | 예 |
| Amazon S3 | Yes | 
| Hive \* | Yes | 
| SAP ECC \* | Yes |
| SAP 테이블 | Yes |
| SQL Server \* | Yes | 
| Teradata \* | Yes |

*\* Azure Purview는 현재 계보 또는 스캔에 대한 쿼리 또는 저장 프로시저를 지원하지 않습니다. 계보는 테이블 및 뷰 원본으로만 제한됩니다.*

> [!Note]
> Data Factory 복사 작업에서 계보 기능은 특정 성능 오버 헤드가 있습니다. Purview에서 데이터 팩터리 연결을 설정하는 사용자의 경우 완료하는 데 시간이 오래 걸리는 특정 복사 작업을 확인할 수 있습니다. 대부분의 영향은 무시할 수 없습니다. 복사 작업을 완료하는 데 평소보다 시간이 오래 걸리는 경우 지원 담당자에 게 문의하세요.

#### <a name="known-limitations-on-copy-activity-lineage"></a>복사 작업 계보에 대해 알려진 제한 사항

현재 다음과 같은 복사 작업 기능을 사용하는 경우 해당 계보는 아직 지원되지 않습니다.

- 이진 형식을 사용하여 Azure Data Lake Storage Gen1에 데이터를 복사합니다.
- PolyBase 또는 COPY 문을 사용하여 Azure Synapse Analytics에 데이터를 복사합니다.
- 이진 파일, 구분 기호로 분리된 텍스트, Excel, JSON 및 XML 파일에 대한 압축 설정입니다.
- Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server 및 SAP 테이블의 원본 파티션 옵션입니다.
- 파일 기반 저장소에 대한 원본 파티션 검색 옵션입니다.
- 파일당 최대 행 수 설정을 사용하여 파일 기반 싱크로 데이터를 복사합니다.
- 복사 중에 열을 추가합니다.

추가 계보에는 다음 커넥터에 대한 데이터 자산 스키마 (자산-> 스키마 탭에 표시)가 보고됩니다.

- Azure Blob, Azure 파일 스토리지, ADLS Gen1, ADLS Gen2 및 Amazon S3의 CSV 및 Parquet 파일
- Azure Data Explorer, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server, Teradata

### <a name="data-factory-data-flow-support"></a>Data Factory 데이터 흐름 지원

| 데이터 저장소 | 지원됨 |
| ------------------- | ------------------- | 
| Azure Blob Storage | 예 |
| Azure Data Lake Storage Gen1 | 예 |
| Azure Data Lake Storage Gen2 | 예 |
| Azure SQL 데이터베이스 \* | Yes |
| Azure Synapse Analytics \* | Yes |

*\* Azure Purview는 현재 계보 또는 스캔에 대한 쿼리 또는 저장 프로시저를 지원하지 않습니다. 계보는 테이블 및 뷰 원본으로만 제한됩니다.*

### <a name="data-factory-execute-ssis-package-support"></a>Data Factory SSIS 패키지 실행 지원

| 데이터 저장소 | 지원됨 |
| ------------------- | ------------------- |
| Azure Blob Storage | 예 |
| Azure Data Lake Storage Gen1 | 예 |
| Azure Data Lake Storage Gen2 | 예 |
| Azure File Storage | 예 |
| Azure SQL 데이터베이스 \* | Yes |
| Azure SQL Managed Instance \*| Yes |
| Azure Synapse Analytics \* | Yes |
| SQL Server \* | Yes |

*\* Azure Purview는 현재 계보 또는 스캔에 대한 쿼리 또는 저장 프로시저를 지원하지 않습니다. 계보는 테이블 및 뷰 원본으로만 제한됩니다.*

> [!Note]
> 이제 Azure Data Lake Storage Gen2가 일반 공급됩니다. 오늘부터 사용을 시작하는 것이 좋습니다. 자세한 내용은 [제품 페이지](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)를 참조하세요.

## <a name="supported-lineage-patterns"></a>지원되는 계보 패턴

Azure Purview에서 지원하는 몇 가지 계보 패턴이 있습니다. 생성된 계보 데이터는 Data Factory 활동에서 사용되는 원본 및 싱크 유형을 기반으로 합니다. Data Factory는 80 이상의 원본 및 싱크를 지원하지만 [지원되는 Azure Data Factory 활동](#supported-azure-data-factory-activities)에 나열된 대로 Azure Purview는 하위 집합만 지원합니다.

계보 정보를 보내도록 Data Factory를 구성하려면 [계보의 시작](catalog-lineage-user-guide.md#get-started-with-lineage)을 참조하세요.

계보 뷰에서 정보를 찾는 몇 가지 추가 방법에는 다음이 포함됩니다.

- **계보** 탭에서 도형을 가리키면 도구 설명의 자산에 대한 추가 정보를 미리 볼 수 있습니다.
- 노드 또는 가장자리를 선택하여 해당하는 자산 유형을 확인하거나 자산을 전환합니다.
- 데이터 세트의 열은 **계보** 탭의 왼쪽에 표시됩니다. 열 수준 계보에 대한 자세한 내용은 [데이터 세트 열 계보](catalog-lineage-user-guide.md#dataset-column-lineage)를 참조하세요.

### <a name="data-lineage-for-11-operations"></a>1:1 작업에 대한 데이터 계보

데이터 계보를 캡처하기 위해 가장 일반적으로 사용되는 패턴은 단일 입력 데이터 집합에서 단일 출력 데이터 집합으로 두 프로세스 간에 데이터를 이동하는 것입니다.

이러한 패턴의 예는 다음과 같습니다.

- 1 원본/입력: *Customer* (SQL 테이블)
- 1 싱크/출력: *Customer1.csv* (Azure Blob)
- 1 프로세스: *CopyCustomerInfo1 \#Customer1.csv* (Data Factory 복사 작업)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="일대일 Data Factory 복사 작업의 계보를 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>1:1 계보 및 와일드카드를 지원하는 데이터 이동

계보 캡처에 대한 또다른 일반적인 시나리오는 와일드카드를 사용하여 단일 입력 데이터 세트에서 단일 출력 데이터 세트로 파일을 복사하는 것입니다. 와일드카드를 사용하면 복사 작업에서 파일 이름의 공통 부분을 사용하여 복사하기 위해 여러 파일을 매치시킬 수 있습니다. Azure Purview는 해당 복사 작업에서 복사한 각 개별 파일에 대한 파일 수준 계보를 캡처합니다.

이러한 패턴의 예는 다음과 같습니다.

* 원본/입력: *CustomerCall \*.csv* (ADLS Gen2 경로)
* 싱크/출력: *CustomerCall \*.Csv* (Azure blob 파일)
* 1 프로세스: *CopyGen2ToBlob \#CustomerCall.csv* (Data Factory 복사 작업)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="와일드카드 지원을 사용하는 일대일 복사 작업의 계보를 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>n:1 계보를 사용한 데이터 이동

Data Flow 작업을 사용하여 병합, 조인 등과 같은 데이터 작업을 수행할 수 있습니다. 두 개 이상의 원본 데이터 세트를 사용하여 대상 데이터 세트를 생성할 수 있습니다. 이 예제에서 Azure Purview는 개별 입력 파일에 대한 파일 수준 계보를 데이터 흐름 활동의 일부인 SQL 테이블에 캡처합니다.

이러한 패턴의 예는 다음과 같습니다.

* 2 원본/입력: *Customer.csv*, *Sales.parquet* (ADLS Gen2 경로)
* 1 싱크/출력: *Company data* (Azure SQL 테이블)
* 1 프로세스: *DataFlowBlobsToSQL* (Data Factory Data Flow 작업)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="n:1의 A D F Data Flow 작업에 대한 계보를 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>리소스 집합에 대한 계보

리소스 집합은 카탈로그의 논리 개체로, 기본 스토리지의 많은 파티션 파일을 나타냅니다. 자세한 내용은 [리소스 집합 이해](concept-resource-sets.md)를 참조하세요. Azure Purview는 Azure Data Factory에서 계보를 캡처하면, 개별 파티션 파일을 정규화하는 규칙을 적용하여 단일 논리적 개체를 만듭니다.

다음 예제에서는 Azure Blob에서 Azure Data Lake Gen2 리소스 집합이 생성됩니다.

* 1 원본/입력: *Employee \_management.csv* (Azure Blob)
* 1 싱크/출력: *Employee\_management.csv* (Azure Data Lake Gen 2)
* 1 프로세스: *CopyBlobToAdlsGen2\_RS* (Data Factory 복사 작업)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="리소스 집합에 대한 계보를 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>다음 단계

- [카탈로그 계보 사용자 가이드](catalog-lineage-user-guide.md)
- [계보에 대한 Azure Data Share 링크](how-to-link-azure-data-share.md)

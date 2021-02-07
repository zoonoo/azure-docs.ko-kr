---
title: Azure Data Factory에 연결
description: 이 문서에서는 Azure Data Factory 및 Azure 부서의 범위를 연결 하 여 데이터 계보를 추적 하는 방법을 설명 합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/22/2020
ms.openlocfilehash: 010cfc307d2b2c10c31168fce73673fb1fb611b8
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807651"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Azure Data Factory 및 Azure 부서의 범위를 연결 하는 방법

이 문서에서는 데이터 계보를 추적 하기 위해 Azure 부서의 범위 계정에 Azure Data Factory 계정을 연결 하는 데 필요한 단계를 설명 합니다. 또한이 문서에서는 검사 범위 및 지원 되는 계보 패턴에 대 한 세부 정보를 제공 합니다.

## <a name="view-existing-data-factory-connections"></a>기존 Data Factory 연결 보기

여러 Azure 데이터 팩터리는 단일 Azure 부서의 범위 Data Catalog에 연결 하 여 계보 정보를 푸시할 수 있습니다. 현재 한도를 사용 하면 부서의 범위 관리 센터에서 한 번에 10 개의 Data Factory 계정을 연결할 수 있습니다. 부서의 범위 Data Catalog에 연결 된 Data Factory 계정 목록을 표시 하려면 다음을 수행 합니다.

1. 왼쪽 탐색 창에서 **관리 센터** 를 선택 합니다.
2. **외부 연결** 에서 **Data Factory 연결** 을 선택 합니다.
3. Data Factory 연결 목록이 표시 됩니다.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="데이터 팩터리 연결 목록을 보여 주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. 연결 **상태** 에 대 한 다양 한 값을 확인 합니다.

    - **연결** 됨: 데이터 팩터리가 데이터 카탈로그에 연결 됩니다.
    - **연결 끊김**: 데이터 팩터리가 카탈로그에 액세스할 수 있지만 다른 카탈로그에 연결 되어 있습니다. 따라서 데이터 계보는 카탈로그에 자동으로 보고 되지 않습니다.
    - **Cannotaccess**: 현재 사용자에 게 data factory에 대 한 액세스 권한이 없으므로 연결 상태를 알 수 없습니다.
 >[!Note]
 >Data Factory 연결을 보려면 부서의 범위 역할 중 하나를 할당 해야 합니다.
 >- 참가자
 >- 소유자
 >- 읽기 권한자
 >- 사용자 액세스 관리자

## <a name="create-new-data-factory-connection"></a>새 Data Factory 연결 만들기

>[!Note]
>Data Factory 연결을 추가 하거나 제거 하려면 부서의 범위 역할 중 하나를 할당 해야 합니다.
>- 소유자
>- 사용자 액세스 관리자
>
> 뿐만 아니라 사용자가 data factory의 "소유자" 또는 "참가자" 여야 합니다. 

부서의 범위 Data Catalog에 기존 Data Factory 계정을 연결 하려면 다음 단계를 수행 합니다.

1. 왼쪽 탐색 창에서 **관리 센터** 를 선택 합니다.
2. **외부 연결** 에서 **Data Factory 연결** 을 선택 합니다.
3. **Data Factory 연결** 페이지에서 **새로 만들기** 를 선택 합니다.

4. 목록에서 Data Factory 계정을 선택 하 고 **확인** 을 선택 합니다. 구독 이름을 기준으로 필터링 하 여 목록을 제한할 수도 있습니다.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Azure Data Factory 연결 하는 방법을 보여 주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    데이터 팩터리가 현재 부서의 범위 계정에 이미 연결 되어 있거나 데이터 팩터리에 관리 되는 id가 없는 경우 일부 Data Factory 인스턴스를 사용 하지 못할 수 있습니다.

    선택한 데이터 팩터리가 다른 부서의 범위 계정에 이미 연결 되어 있으면 경고 메시지가 표시 됩니다. 확인을 선택 하 여 다른 부서의 범위 계정에 대 한 Data Factory 연결의 연결을 끊습니다. 추가 확인은 필요 하지 않습니다.


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Azure Data Factory 연결을 끊을 경고를 보여 주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>이제 10 개 이하의 데이터 팩터리를 한 번에 추가할 수 있습니다. 10 개 이상의 데이터 팩터리를 한 번에 추가 하려면 지원 티켓을 제출 하세요.

### <a name="how-does-the-authentication-work"></a>인증은 어떻게 작동 하나요?

부서의 범위 사용자가 액세스 권한이 있는 Data Factory를 등록 하면 백 엔드에서 다음 작업이 수행 됩니다.

1. **DATA FACTORY MSI** 가 부서의 범위 RBAC 역할: **부서의 범위 Data 큐레이터** 에 추가 됩니다.

    :::image type="content" source="./media/how-to-link-azure-data-factory/adf-msi.png" alt-text="MSI Azure Data Factory를 보여 주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/adf-msi.png":::
     
2. 계보 메타 데이터를 부서의 범위에 다시 푸시할 수 있도록 Data Factory 파이프라인을 다시 실행 해야 합니다.
3. 실행 후 Data Factory 메타 데이터가 부서의 범위에 푸시됩니다.

### <a name="remove-data-factory-connections"></a>데이터 팩터리 연결 제거
Data factory 연결을 제거 하려면 다음을 수행 합니다.

1. **Data Factory 연결** 페이지에서 하나 이상의 데이터 팩터리 연결 옆에 있는 **제거** 단추를 선택 합니다.
2. 선택한 data factory 연결을 삭제 하려면 팝업에서 **확인** 을 선택 합니다.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="데이터 팩터리를 선택 하 여 연결을 제거 하는 방법을 보여 주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-ir-to-collect-lineage-from-on-prem-sql"></a>자체 호스팅 IR을 구성 하 여 온-프레미스 SQL에서 계보 수집

Data Factory 복사 작업의 계보는 온-프레미스 SQL 데이터베이스에 사용할 수 있습니다. Azure Data Factory를 사용 하 여 데이터 이동에 대해 자체 호스팅 통합 런타임을 실행 하 고 Azure 부서의 범위에서 계보를 캡처하려면 버전이 4.8.7418.1 이상 인지 확인 합니다. 자체 호스팅 통합 런타임에 대 한 자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성](../data-factory/create-self-hosted-integration-runtime.md)을 참조 하세요.

## <a name="supported-azure-data-factory-activities"></a>지원 되는 Azure Data Factory 활동

Azure 부서의 범위는 다음과 같은 Azure Data Factory 작업에서 런타임 계보를 캡처합니다.

- 데이터 복사
- 데이터 흐름
- SSIS 패키지 실행

> [!IMPORTANT]
> 원본 또는 대상이 지원 되지 않는 데이터 저장소 시스템을 사용 하는 경우 Azure 부서의 범위가 계보를 삭제 합니다.

Data Factory와 부서의 범위 간의 통합은 다음 섹션에 설명 된 대로 Data Factory에서 지 원하는 데이터 시스템의 하위 집합만 지원 합니다.

### <a name="data-factory-copy-data-support"></a>Data Factory 데이터 복사 지원

| 데이터 저장소 시스템 | 원본으로 지원됨 | 
| ------------------- | ------------------- | 
| ADLS Gen1 | 예 | 
| ADLS Gen2 | 예 | 
| Azure Blob | 예 |
| Azure Cosmos DB (SQL API) | 예 | 
| Azure Cosmos DB (Mongo API) | 예 |
| Azure Cognitive Search | 예 | 
| Azure Data Explorer | 예 | 
| Azure Database for 민 DB \* | 예 | 
| MYSQL 용 Azure 데이터베이스 \* | 예 | 
| Azure Database for PostgreSQL \* | 예 |
| Azure File Storage | 예 | 
| Azure Table Storage | 예 |
| Azure SQL Database \* | 예 | 
| Azure SQL MI \* | 예 | 
| Azure Synapse Analytics (이전의 SQL DW) \* | 예 | 
| SQL Server 온-프레미스  \* | 예 | 
| Amazon S3 | 예 | 
| Teradata | 예 | 
| SAP 테이블 커넥터 | 예 |
| SAP ECC | 예 | 
| Hive | 예 | 

> [!Note]
> 계보 기능의 Data Factory 복사 작업에는 특정 성능 오버 헤드가 있습니다. 부서의 범위에서 data factory 연결을 설정 하는 사용자의 경우 완료 하는 데 시간이 오래 걸리는 특정 복사 작업을 확인할 수 있습니다. 대부분의 영향은 무시할 수 없습니다. 복사 작업을 완료 하는 데 평소 보다 시간이 오래 걸리는 경우 지원 담당자에 게 문의 하세요.

### <a name="data-factory-data-flow-support"></a>Data Factory 데이터 흐름 지원

| 데이터 저장소 시스템 | 지원됨 |
| ------------------- | ------------------- | 
| ADLS Gen1 | 예 |
| ADLS Gen2 | 예 |
| Azure Blob | 예 |
| Azure SQL Database \* | 예 |
| Azure Synapse Analytics (이전의 SQL DW) \* | 예 |

### <a name="data-factory-execute-ssis-package-support"></a>SSIS 패키지 지원 Data Factory 실행

| 데이터 저장소 시스템 | 지원됨 |
| ------------------- | ------------------- |
| Azure Blob | 예 |
| ADLS Gen1 | 예 |
| ADLS Gen2 | 예 |
| Azure SQL Database \* | 예 |
| Azure SQL MI \*| 예 |
| Azure Synapse Analytics (이전의 SQL DW) \* | 예 |
| SQL Server 온-프레미스 \* | 예 |
| Azure File Storage | 예 |

*\* SQL (Azure 및 온-프레미스) 시나리오의 경우 Azure 부서의 범위는 계보 또는 검색에 대 한 저장 프로시저 또는 스크립트를 지원 하지 않습니다. 계보는 테이블 및 뷰 원본 으로만 제한 됩니다.*

> [!Note]
> 이제 Azure Data Lake Storage Gen2가 일반 공급됩니다. 오늘부터 사용을 시작하는 것이 좋습니다. 자세한 내용은 [제품 페이지](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)를 참조하세요.

## <a name="supported-lineage-patterns"></a>지원 되는 계보 패턴

Azure 부서의 범위에서 지 원하는 몇 가지 계보 패턴이 있습니다. 생성 된 계보 데이터는 Data Factory 활동에서 사용 되는 원본 및 싱크 유형을 기반으로 합니다. Data Factory는 80 원본 및 싱크를 지원 하지만 [지원 되는 Azure Data Factory 활동](#supported-azure-data-factory-activities)에 나열 된 대로 Azure 부서의 범위는 하위 집합만 지원 합니다.

계보 정보를 보내도록 Data Factory를 구성 하려면 [계보 시작](catalog-lineage-user-guide.md#get-started-with-lineage)을 참조 하세요.

계보 뷰에서 정보를 찾는 몇 가지 추가 방법에는 다음이 포함 됩니다.

- **계보** 탭에서 셰이프를 가리키면 도구 설명의 자산에 대 한 추가 정보를 미리 볼 수 있습니다.
- 노드 또는 가장자리를 선택 하 여 해당 하는 자산 유형을 확인 하거나 자산을 전환 합니다.
- 데이터 집합의 열은 **계보** 탭의 왼쪽에 표시 됩니다. 열 수준 계보에 대 한 자세한 내용은 [열 수준 계보](catalog-lineage-user-guide.md#column-level-lineage)를 참조 하세요.

### <a name="data-lineage-for-11-operations"></a>1:1 작업에 대 한 데이터 계보

데이터 계보를 캡처하기 위해 가장 일반적으로 사용 되는 패턴은 단일 입력 데이터 집합에서 단일 출력 데이터 집합으로 데이터를 이동 하는 것입니다.

이 패턴의 예는 다음과 같습니다.

- 1 원본/입력: *고객* (SQL 테이블)
- 1 싱크/출력: *Customer1.csv* (Azure Blob)
- 1 프로세스: *CopyCustomerInfo1 \#Customer1.csv* (Data Factory 복사 작업)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="일대일 Data Factory 복사 작업의 계보를 보여 주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>1:1 계보 및 와일드 카드를 지 원하는 데이터 이동

계보 캡처에 대 한 또 다른 일반적인 시나리오는 와일드 카드를 사용 하 여 단일 입력 데이터 집합에서 단일 출력 데이터 집합으로 파일을 복사 하는 것입니다. 와일드 카드를 사용 하면 복사 작업에서 파일 이름의 공통 부분을 사용 하 여 복사 하기 위해 여러 파일을 일치 시킬 수 있습니다. Azure 부서의 범위는 해당 복사 작업에서 복사한 각 개별 파일에 대 한 파일 수준 계보를 캡처합니다.

이 패턴의 예는 다음과 같습니다.

* 원본/입력: *Customercall \* .csv* (ADLS Gen2 경로)
* 싱크/출력: *Customercall \* .Csv* (Azure blob 파일)
* 1 프로세스: *CopyGen2ToBlob \#CustomerCall.csv* (Data Factory 복사 작업)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="와일드 카드 지원을 사용 하는 일대일 복사 작업의 계보를 보여 주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>N:1 계보를 사용 하 여 데이터 이동

데이터 흐름 작업을 사용 하 여 병합, 조인 등과 같은 데이터 작업을 수행할 수 있습니다. 두 개 이상의 원본 데이터 집합을 사용 하 여 대상 데이터 집합을 생성할 수 있습니다. 이 예제에서 Azure 부서의 범위는 개별 입력 파일에 대 한 파일 수준 계보를 데이터 흐름 활동의 일부인 SQL 테이블에 캡처합니다.

이 패턴의 예는 다음과 같습니다.

* 2 원본/입력: *Customer.csv*, *Parquet* (ADLS Gen2 경로)
* 1 싱크/출력: *회사 데이터* (Azure SQL 테이블)
* 1 프로세스: *DataFlowBlobsToSQL* (Data Factory 데이터 흐름 작업)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="N 개에서 1 개의 D F 데이터 흐름 작업에 대 한 계보를 보여 주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>리소스 집합에 대 한 계보

리소스 집합은 기본 저장소의 많은 파티션 파일을 나타내는 카탈로그의 논리적 개체입니다. 자세한 내용은 [리소스 집합 이해](concept-resource-sets.md)를 참조 하세요. Azure 부서의 범위는 Azure Data Factory에서 계보를 캡처하면 개별 파티션 파일을 정규화 하는 규칙을 적용 하 여 단일 논리적 개체를 만듭니다.

다음 예제에서는 Azure Blob에서 Azure Data Lake Gen2 리소스 집합이 생성 됩니다.

* 1 원본/입력: *Employee \_management.csv* (Azure Blob)
* 1 싱크/출력: *직원 \_management.csv* (Azure Data Lake Gen 2)
* 1 프로세스: *CopyBlobToAdlsGen2 \_ RS* (Data Factory 복사 활동)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="리소스 집합에 대 한 계보를 보여 주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>다음 단계

- [카탈로그 계보 사용자 가이드](catalog-lineage-user-guide.md)
- [계보 용 Azure 데이터 공유에 대 한 링크](how-to-link-azure-data-share.md)

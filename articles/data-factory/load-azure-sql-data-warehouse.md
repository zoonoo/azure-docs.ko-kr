---
title: Azure Synapse Analytics에 데이터 로드
description: Azure Data Factory를 사용 하 여 Azure Synapse Analytics로 데이터 복사
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/08/2020
ms.openlocfilehash: c90590ac4c47f6ac8d51273fecfb653dfe056b1d
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89485773"
---
# <a name="load-data-into-azure-synapse-analytics-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure Synapse Analytics에 데이터 로드

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 는 관계형 데이터와 비관계형 데이터를 모두 처리할 수 있는 클라우드 기반 규모 확장 데이터베이스입니다. Azure Synapse Analytics는 엔터프라이즈 데이터 웨어하우스 워크 로드에 최적화 된 MPP (대규모 parallel processing) 아키텍처를 기반으로 합니다. 스토리지를 확장하고 개별적으로 계산할 수 있는 클라우드 탄력성을 유연하게 제공합니다.

이제 Azure Data Factory를 사용 하는 경우 보다 쉽게 Azure Synapse Analytics를 시작할 수 있습니다. Azure Data Factory는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 서비스를 사용 하 여 Azure Synapse Analytics를 기존 시스템의 데이터로 채우고 분석 솔루션을 빌드할 때 시간을 절약할 수 있습니다.

Azure Data Factory는 Azure Synapse Analytics로 데이터를 로드 하는 다음과 같은 이점을 제공 합니다.

* **간편한 설정**: 스크립팅이 필요 없는 직관적인 5단계 마법사.
* **다양한 데이터 저장소 지원**: 다양한 온-프레미스 및 클라우드 기반 데이터 저장소 집합에 대한 기본 제공 지원. 자세한 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.
* **보안 및 규정 준수**: 데이터가 HTTPS 또는 Express 경로를 통해 전송됩니다. 글로벌 서비스가 제공되므로 데이터가 지리적 경계를 벗어나지 않습니다.
* **Polybase를 사용 하는 뛰어난 성능**: polybase는 데이터를 Azure Synapse Analytics로 이동 하는 가장 효율적인 방법입니다. 스테이징 Blob 기능을 사용하여 Azure Blob Storage 및 Data Lake Store를 포함하여 모든 유형의 데이터 스토리지에서 높은 로드 속도를 얻습니다. Polybase는 기본적으로 Azure Blob storage 및 Azure Data Lake Store을 지원 합니다. 자세한 내용은 [복사 작업 성능](copy-activity-performance.md)을 참조 하세요.

이 문서에서는 Data Factory 데이터 복사 도구를 사용 하 여 _Azure SQL Database에서 Azure Synapse Analytics로 데이터를 로드_하는 방법을 보여 줍니다. 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

> [!NOTE]
> 자세한 내용은 [Azure Data Factory를 사용 하 여 Azure Synapse Analytics 간에 데이터 복사](connector-azure-sql-data-warehouse.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure Synapse Analytics: 데이터 웨어하우스는 SQL 데이터베이스에서 복사 된 데이터를 보관 합니다. Azure Synapse Analytics가 없는 경우 [Azure Synapse Analytics 만들기](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)의 지침을 참조 하세요.
* Azure SQL Database:이 자습서에서는 Azure SQL Database의 놀이 Works LT 샘플 데이터 집합에서 데이터를 복사 합니다. [Azure SQL Database에서 예제 데이터베이스 만들기](../azure-sql/database/single-database-create-quickstart.md)의 지침에 따라 SQL Database에서이 예제 데이터베이스를 만들 수 있습니다.
* Azure 스토리지 계정: Azure Storage는 대량 복사 작업에서 _스테이징_ Blob으로 사용됩니다. Azure storage 계정이 없는 경우 [저장소 계정 만들기](../storage/common/storage-account-create.md)의 지침을 참조 하세요.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 왼쪽 메뉴에서 **리소스 만들기** > **데이터 + 분석** > **Data Factory**를 차례로 선택합니다.

2. **새 데이터 팩터리** 페이지에서 다음 항목에 대 한 값을 제공 합니다.

    * **이름**: 이름에 *LoadSQLDWDemo* 를 입력 합니다. 데이터 팩터리의 이름은 전역적으로 고유 해야 합니다. "Data factory name ' LoadSQLDWDemo '을 (를) 사용할 수 없습니다." 라는 오류가 표시 되 면 데이터 팩터리에 대해 다른 이름을 입력 합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory**라는 이름을 사용할 수 있습니다. 데이터 팩터리를 다시 만들어 봅니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
    * **구독**: 데이터 팩터리를 만들 Azure 구독을 선택합니다. 
    * **리소스 그룹**: 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 옵션을 선택하고 리소스 그룹의 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.  
    * **버전**: **V2**를 선택합니다.
    * **위치**: 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소가 다른 위치 및 지역에 있어도 됩니다. 이러한 데이터 저장소는 Azure Data Lake Store, Azure Storage, Azure SQL Database 등을 포함합니다.

3. **만들기**를 선택합니다.
4. 만들기가 완료되면 데이터 팩터리로 이동합니다. 다음 그림과 같이 **데이터 팩터리** 홈페이지가 표시됩니다.

   ![데이터 팩터리 홈페이지](./media/doc-common-process/data-factory-home-page.png)

   **작성 및 모니터링** 타일을 선택하여 별도의 탭에서 데이터 통합 애플리케이션을 시작합니다.

## <a name="load-data-into-azure-synapse-analytics"></a>Azure Synapse Analytics에 데이터 로드

1. **시작** 페이지에서 **데이터 복사** 타일을 선택 하 여 데이터 복사 도구를 시작 합니다.

1. **속성** 페이지에서 **작업 이름** 필드에 대해 **CopyFromSQLToSQLDW** 를 지정 하 고 **다음**을 선택 합니다.

    ![속성 페이지](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. **원본 데이터 저장소** 페이지에서 다음 단계를 완료합니다.
    >[!TIP]
    >이 자습서에서는 원본 데이터 저장소에 대 한 인증 형식으로 *SQL 인증* 을 사용 하지만, 필요한 경우 다른 지원 되는 인증 방법 (*서비스 주체* 및 *관리 id* )을 선택할 수 있습니다. 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties)의 해당 섹션을 참조하세요.
    >데이터 저장소에 대한 비밀을 안전하게 저장하려면 Azure Key Vault를 사용하는 것도 좋습니다. 자세한 그림은 [이 문서](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)를 참조하세요.

    a. **+ 새 연결 만들기**를 클릭 합니다.

    b. 갤러리에서 **Azure SQL Database**를 선택하고 **계속**을 선택합니다. 검색 상자에 "SQL"을 입력하여 커넥터를 필터링할 수 있습니다.

    ![Azure SQL DB 선택](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    다. **새 연결 된 서비스** 페이지의 드롭다운 목록에서 서버 이름과 DB 이름을 선택 하 고 사용자 이름 및 암호를 지정 합니다. **연결 테스트** 를 클릭 하 여 설정의 유효성을 검사 한 다음, **만들기**를 선택 합니다.

    ![Azure SQL DB 구성](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. 새로 만든 연결된 서비스를 원본으로 선택하고 **다음**을 클릭합니다.

1. **데이터를 복사할 테이블 선택 또는 사용자 지정 쿼리 사용** 페이지에서 **SalesLT**를 입력하여 테이블을 필터링합니다. **(모두 선택)** 상자를 선택 하 여 복사본에 대 한 모든 테이블을 사용 하 고 **다음**을 선택 합니다.

    ![원본 테이블 선택](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. **필터 적용** 페이지에서 설정을 지정 하거나 **다음**을 선택 합니다.

1. **대상 데이터 저장소** 페이지에서 다음 단계를 완료합니다.
    >[!TIP]
    >이 자습서에서는 대상 데이터 저장소에 대 한 인증 형식으로 *SQL 인증* 을 사용 하지만, 필요한 경우 다른 지원 되는 인증 방법 (*서비스 주체* 및 *관리 id* )을 선택할 수 있습니다. 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#linked-service-properties)의 해당 섹션을 참조하세요.
    >데이터 저장소에 대한 비밀을 안전하게 저장하려면 Azure Key Vault를 사용하는 것도 좋습니다. 자세한 그림은 [이 문서](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)를 참조하세요.

    a. **+ 새 연결 만들기**를 클릭하여 연결을 추가합니다.

    b. 갤러리에서 **Azure Synapse Analytics (이전의 SQL Data Warehouse)** 를 선택 하 고 **계속**을 선택 합니다. 검색 상자에 "SQL"을 입력하여 커넥터를 필터링할 수 있습니다.

    ![Azure Synapse Analytics 선택](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    다. **새 연결 된 서비스** 페이지의 드롭다운 목록에서 서버 이름과 DB 이름을 선택 하 고 사용자 이름 및 암호를 지정 합니다. **연결 테스트** 를 클릭 하 여 설정의 유효성을 검사 한 다음, **만들기**를 선택 합니다.

    ![Azure Synapse Analytics 구성](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. 새로 만든 연결된 서비스를 싱크로 선택하고 **다음**을 클릭합니다.

1. **테이블 매핑** 페이지에서 콘텐츠를 검토하고, **다음**을 선택합니다. 지능형 테이블 매핑이 표시됩니다. 원본 테이블은 테이블 이름에 따라 대상 테이블에 매핑됩니다. 원본 테이블이 대상에 없으면 기본적으로 Azure Data Factory는 같은 이름으로 대상 테이블을 만듭니다. 기존 대상 테이블에 원본 테이블을 매핑할 수도 있습니다.

   > [!NOTE]
   > Azure Synapse Analytics 싱크에 대 한 자동 테이블 생성은 SQL Server 또는 Azure SQL Database 원본이 될 때 적용 됩니다. 다른 원본 데이터 저장소에서 데이터를 복사 하는 경우 데이터 복사를 실행 하기 전에 싱크 Azure Synapse Analytics에서 스키마를 미리 만들어야 합니다.

   ![테이블 매핑 페이지](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. **열 매핑** 페이지에서 콘텐츠를 검토 하 고 **다음**을 선택 합니다. 지능형 테이블 매핑은 열 이름을 기반으로 합니다. 데이터 팩터리에서 테이블을 자동으로 만들도록 하는 경우 원본 및 대상 저장소 간에 비호환성 문제가 있을 때 데이터 형식 변환이 발생할 수 있습니다. 원본 및 대상 열 간에 지원되지 않는 데이터 형식 변환이 있는 경우 해당 테이블 옆에 오류 메시지가 표시됩니다.

    ![열 매핑 페이지](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. **설정** 페이지에서 다음 단계를 완료합니다.

    a. **준비 설정** 섹션에서 **+ 새로 만들기**를 클릭하여 준비 스토리지를 새로 만듭니다. 저장소는 PolyBase를 사용 하 여 Azure Synapse Analytics로 로드 하기 전에 데이터를 준비 하는 데 사용 됩니다. 복사가 완료 되 면 Azure Blob Storage의 중간 데이터가 자동으로 정리 됩니다.

    b. **새 연결 된 서비스** 페이지에서 저장소 계정을 선택 하 고 **만들기** 를 선택 하 여 연결 된 서비스를 배포 합니다.

    다. **고급 설정** 섹션에서 **형식 기본값 사용** 옵션의 선택을 취소한 다음, **다음**을 선택합니다.

    ![PolyBase 구성](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. **요약** 페이지에서 설정을 검토 하 고 **다음**을 선택 합니다.

    ![요약 페이지](./media/load-azure-sql-data-warehouse/summary-page.png)

1. **배포 페이지**에서 **모니터**를 선택하여 파이프라인(작업)을 모니터링합니다. 
 
1. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. 파이프라인 실행이 성공적으로 완료 되 면 **파이프라인 이름** 열 아래의 **CopyFromSQLToSQLDW** 링크를 선택 하 여 작업 실행 세부 정보를 보거나 파이프라인을 다시 실행 합니다.

    [![파이프라인 실행 모니터링](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox)

1. 파이프라인 실행 보기로 다시 전환 하려면 위쪽의 **모든 파이프라인 실행** 링크를 선택 합니다. **새로 고침**을 선택하여 목록을 새로 고칩니다.

    ![작업 실행 모니터링](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. 각 복사 작업의 실행 세부 정보를 모니터링 하려면 작업 실행 보기의 **작업 이름** 아래에서 **세부 정보** 링크 (안경 아이콘)를 선택 합니다. 원본에서 싱크로 복사 되는 데이터 볼륨, 데이터 처리량, 해당 기간에 대 한 실행 단계 및 사용 된 구성과 같은 세부 정보를 모니터링할 수 있습니다.
    ![작업 실행 세부 정보 모니터링](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![작업 실행 세부 정보 모니터링](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>다음 단계

Azure Synapse Analytics 지원에 대해 알아보려면 다음 문서로 이동 합니다.

> [!div class="nextstepaction"]
>[Azure Synapse Analytics 커넥터](connector-azure-sql-data-warehouse.md)

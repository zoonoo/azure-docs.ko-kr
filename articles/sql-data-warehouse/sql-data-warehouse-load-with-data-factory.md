---

title: "Azure SQL Data Warehouse에 데이터 로드 – Data Factory | Microsoft Docs"
description: "이 자습서에서는 Azure Data Factory를 사용하여 Azure SQL Data Warehouse에 데이터를 로드하고 데이터 원본으로 SQL Server 데이터베이스를 사용합니다."
services: sql-data-warehouse
documentationcenter: NA
author: linda33wj
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: loading
ms.date: 02/08/2017
ms.author: jingwang;kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 6474104846eefa1aa7e137e7914b7a7f1ee8a83a
ms.openlocfilehash: aad76a633b127d23d59dae995d7a503023c5eac7
ms.lasthandoff: 02/09/2017



---

# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Data Factory와 함께 SQL Data Warehouse로 데이터 로드

Azure Data Factory를 사용하여 [지원되는 원본 데이터 저장소](../data-factory/data-factory-data-movement-activities.md#supported-data-stores-and-formats)에서 Azure SQL Data Warehouse로 데이터를 로드할 수 있습니다. 예를 들어 Data Factory를 사용하여 Azure SQL Database 또는 Oracle 데이터베이스에서 SQL Data Warehouse로 데이터를 로드할 수 있습니다. 이 문서의 자습서에서는 온-프레미스 SQL Server 데이터베이스에서 SQL Data Warehouse로 데이터를 로드하는 방법을 보여 줍니다.

**예상 시간**: 이 자습서를 완료하는 데는 필수 조건이 충족된 경우 10-15분이 소요됩니다.

## <a name="prerequisites"></a>필수 조건

- SQL Data Warehouse에 복사할 데이터가 포함된 테이블이 있는 **SQL Server 데이터베이스**가 필요합니다.  

- 온라인 **SQL Data Warehouse**가 필요합니다. 데이터 웨어하우스가 아직 없는 경우 [Azure SQL Data Warehouse를 만드는 방법](sql-data-warehouse-get-started-provision.md)을 알아보세요.

- **Azure Storage 계정**이 필요합니다. 저장소 계정이 아직 없을 경우 [저장소 계정을 만드는 방법](../storage/storage-create-storage-account.md)을 알아보세요. 최적의 성능을 위해 저장소 계정 및 데이터 웨어하우스를 동일한 Azure 지역에 배치합니다.

## <a name="configure-a-data-factory"></a>Data Factory 구성
1. [Azure 포털][]에 로그인합니다.
2. 데이터 웨어하우스를 찾고 클릭하여 엽니다.
3. 주 블레이드에서 **데이터 로드** > **Azure Data Factory**를 클릭합니다.

    ![데이터 로드 마법사 시작](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. Azure 구독에 Data Factory가 없으면 브라우저의 별도 탭에 **새 Data Factory** 대화 상자가 나타납니다. 요청된 정보를 입력하고 **만들기**를 클릭합니다. Data Factory를 만들면 **새 Data Factory** 대화 상자가 닫히고 **Data Factory 선택** 대화 상자가 표시됩니다.

    Azure 구독에 이미 하나 이상의 Data Factory가 있는 경우 **Data Factory 선택** 대화 상자가 표시됩니다. 이 대화 상자에서 기존 Data Factory를 선택하거나 **새 Data Factory 만들기**를 클릭하여 새 Data Factory를 만들 수 있습니다.

    ![데이터 팩터리 구성](media/sql-data-warehouse-load-with-data-factory/configure-data-factory.png)

5. **데이터 팩터리 선택** 대화 상자에는 **데이터 로드** 옵션이 기본적으로 선택되어 있습니다. **다음**을 클릭하여 데이터 로드 작업 만들기를 시작합니다.

## <a name="configure-the-data-factory-properties"></a>데이터 팩터리 속성 구성
이제 데이터 팩터리를 만들었고 다음 단계에서는 데이터 로딩 일정을 구성합니다.

1. **작업 이름**에 **DWLoadData-fromSQLServer**를 입력합니다.
2. 기본 **지금 한 번 실행** 옵션을 사용하고 **다음**을 클릭합니다.

    ![로드 일정 구성](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-source-data-store-and-gateway"></a>원본 데이터 저장소 및 게이트웨이 구성
이제 Data Factory에 데이터를 로드할 원본 온-프레미스 SQL Server 데이터베이스에 대한 정보를 알려 줍니다.

1. 지원되는 원본 데이터 저장소 카탈로그에서 **SQL Server**를 선택하고 **다음**을 클릭합니다.

    ![SQL Server 원본 선택](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

2. **온-프레미스 SQL Server 데이터베이스 지정** 대화 상자가 나타납니다. 첫 번째 **연결 이름** 필드는 자동으로 입력됩니다. 두 번째 필드는 **게이트웨이** 이름을 요청합니다. 이미 게이트웨이가 있는 기존 Data Factory를 사용하는 경우 드롭다운 목록에서 게이트웨이를 선택하여 재사용할 수 있습니다. **게이트웨이 만들기** 링크를 클릭하여 데이터 관리 게이트웨이를 만듭니다.  

    > [!NOTE]
    > 원본 데이터 저장소가 온-프레미스 또는 Azure IaaS 가상 컴퓨터에 있는 경우 데이터 관리 게이트웨이가 필요합니다. 게이트웨이는 Data Factory와 1-1 관계를 갖습니다. 다른 Data Factory에서는 사용할 수 없지만 동일한 Data Factory에서 여러 데이터 로드 작업으로 사용할 수 있습니다. 게이트웨이는 데이터 로드 작업을 실행할 때 여러 데이터 저장소에 연결하는 데 사용할 수 있습니다.
    >
    > 게이트웨이에 대한 자세한 내용은 [데이터 관리 게이트웨이](../data-factory/data-factory-data-management-gateway.md) 문서를 참조하세요.

3. **게이트웨이 만들기** 대화 상자가 나타납니다. 이름에는 **GatewayForDWLoading**을 입력하고 **만들기**를 클릭합니다.

4. **게이트웨이 구성** 대화 상자가 나타납니다. **이 컴퓨터에서 빠른 설치 시작**을 클릭하여 현재 컴퓨터에 데이터 관리 게이트웨이를 자동으로 다운로드, 설치 및 등록합니다. 진행 상태가 팝업 창에 표시됩니다. 컴퓨터를 데이터 저장소에 연결할 수 없는 경우 데이터 저장소에 연결할 수 있는 컴퓨터에 수동으로 [게이트웨이를 다운로드하고 설치](https://www.microsoft.com/download/details.aspx?id=39717)한 다음 키를 사용하여 등록할 수 있습니다.
    > [!NOTE]
    > 빠른 설치는 Microsoft Edge 및 Internet Explorer에서 고유하게 작동합니다. Google Chrome을 사용 중인 경우 먼저 Chrome 웹 스토어에서 ClickOnce 확장을 설치합니다.

    ![빠른 설치 시작](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

5. 게이트웨이 설치가 완료될 때까지 기다립니다. 게이트웨이가 성공적으로 등록되고 온라인 상태이면 팝업 창이 닫히고 새 게이트웨이가 게이트웨이 필드에 나타납니다. 그런 다음 나머지 필수 필드를 다음과 같이 채운 후 **다음**을 클릭합니다.
    - **서버 이름**: 온-프레미스 SQL Server의 이름입니다.
    - **데이터베이스 이름**: SQL Server 데이터베이스.
    - **자격 증명 암호화**: 기본값인 "웹 브라우저"를 사용합니다.
    - **인증 유형**: 사용 중인 인증 유형을 선택합니다.
    - **사용자 이름** 및 **암호**: 데이터를 복사할 권한이 있는 사용자에 대한 사용자 이름 및 암호를 입력합니다.

    ![빠른 설치 시작](media/sql-data-warehouse-load-with-data-factory/configure-sql-server.png)

6. 다음 단계는 데이터를 복사할 원본 테이블을 선택하는 것입니다. 키워드를 사용하여 테이블을 필터링할 수 있습니다. 아래쪽 패널에서 데이터 및 테이블 스키마를 미리 볼 수 있습니다. 선택을 완료했으면 **다음**을 클릭합니다.

    ![테이블 선택](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>대상으로 SQL Data Warehouse 구성

이제 Data Factory에 대상 정보를 알려줍니다.

1. SQL Data Warehouse 연결 정보가 자동으로 채워집니다. 사용자 이름 암호를 입력합니다. **다음**을 클릭합니다.

    ![대상 구성](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

2. 테이블 이름에 따라 원본을 대상 테이블에 매핑하는 지능형 테이블 매핑이 나타납니다. 테이블이 대상에 없는 경우 기본적으로 ADF가 같은 이름을 사용하여 하나를 만듭니다(SQL Server 또는 Azure SQL Database에 원본으로 적용됨). 또한 기존 테이블에 매핑할 수도 있습니다. 검토하고 **다음**을 클릭합니다.

    ![맵 테이블](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

3. 스키마 매핑을 검토하고 오류 또는 경고 메시지를 확인합니다. 지능형 매핑은 열 이름을 기반으로 합니다. 원본 및 대상 열 간에 지원되지 않는 데이터 형식이 있는 경우 해당 테이블과 함께 오류 메시지가 표시됩니다. 데이터 팩터리에서 테이블을 자동으로 만들 수 있도록 선택하는 경우 소스 및 대상 저장소 간의 비 호환성을 해결할 필요성이 있으면 적절한 데이터 형식 변환이 발생할 수 있습니다.

    ![맵 스키마](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

4. **Next**를 클릭합니다.

## <a name="configure-the-performance-settings"></a>성능 설정 구성
성능 구성에서 [PolyBase](sql-data-warehouse-best-practices.md#use-polybase-to-load-and-export-data-quickly)를 사용하여 성능 기준에 맞게 데이터를 SQL Data Warehouse에 로드하기 전에 데이터 준비에 사용할 Azure Storage 계정을 구성합니다. 복사가 완료되면 저장소의 중간 데이터는 자동으로 정리됩니다.

기존 Azure Storage 계정을 선택하거나 **다음**을 클릭합니다.

![준비 Blob 구성](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>요약 정보 검토 및 파이프라인 배포

구성을 검토하고 **마침** 단추를 클릭하여 파이프라인을 배포합니다.

![데이터 팩터리 배포](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>데이터 로드 진행 상태 모니터

**배포** 페이지서 배포 진행률 및 결과를 볼 수 있습니다.

1. 배포가 완료되면 **복사 파이프라인을 모니터링하려면 여기를 클릭** 링크를 클릭하여 데이터 로딩 진행 상태를 모니터링합니다.

    ![파이프라인 모니터링](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

2. 새로 만든 **DWLoadData fromSQLServer** 데이터 로드 파이프라인이 왼쪽의 **리소스 탐색기**에서 자동 선택됩니다.

    ![파이프라인 보기](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

3. 중간 패널에서 파이프라인을 클릭하여 활동에 매핑되는 각 테이블에 대한 자세한 상태를 확인합니다.

    ![테이블 활동 보기](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

4. 활동을 추가로 클릭하면 오른쪽 창에 데이터 크기, 행, 처리량 등 데이터 로딩 세부 정보가 표시됩니다.

    ![테이블 활동 세부 정보 보기](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

5. 이 모니터링 보기를 나중에 시작하려면 SQL Data Warehouse로 이동하고 **데이터 로드 > Azure Data Factory**를 클릭한 후 팩터리를 선택하고 **기존 로딩 작업 모니터링**을 선택합니다.

## <a name="next-steps"></a>다음 단계

데이터베이스를 SQL Data Warehouse로 마이그레이션하려면 [마이그레이션 개요](sql-data-warehouse-overview-migrate.md)를 참조하세요.

Azure Data Factory 및 데이터 이동 기능에 대한 자세한 내용을 보려면 다음 문서를 참조하세요.

- [Azure Data Factory 소개](../data-factory/data-factory-introduction.md)
- [복사 작업을 사용하여 데이터 이동](../data-factory/data-factory-data-movement-activities.md)
- [Azure Data Factory를 사용하여 Azure SQL Data Warehouse 간 데이터 이동](../data-factory/data-factory-azure-sql-data-warehouse-connector.md)

SQL Data Warehouse의 데이터를 탐색하려면 다음 문서를 참조하세요.

- [Visual Studio 및 SSDT를 사용하여 SQL Data Warehouse에 연결](sql-data-warehouse-query-visual-studio.md)
- [Power BI를 사용하여 시각적 데이터](sql-data-warehouse-get-started-visualize-with-power-bi.md)

<!-- Azure references -->
[Azure 포털]: https://portal.azure.com


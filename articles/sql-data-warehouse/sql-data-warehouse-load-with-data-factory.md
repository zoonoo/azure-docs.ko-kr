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
ms.date: 11/21/2016
ms.author: jingwang;kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5c8b3ef69cd2bc663d6ee744c2351da9c22adb94
ms.openlocfilehash: 24b9e5c2f423f7ef8bb29ab18359318f93c1d88c


---

# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Data Factory와 함께 SQL Data Warehouse로 데이터 로드

Azure Data Factory를 사용하여 [지원되는 원본 데이터 저장소](../data-factory/data-factory-data-movement-activities.md#supported-data-stores-and-formats)에서 Azure SQL Data Warehouse로 데이터를 로드할 수 있습니다. 예를 들어 Data Factory를 사용하여 Azure SQL Database 또는 Oracle 데이터베이스에서 SQL Data Warehouse로 데이터를 로드할 수 있습니다. 이 문서의 자습서에서는 온-프레미스 SQL Server 데이터베이스에서 SQL Data Warehouse로 데이터를 로드하는 방법을 보여 줍니다.

**예상 시간**: 이 자습서를 완료하는 데는 필수 조건이 충족된 경우 10-15분이 소요됩니다.

## <a name="prerequisites"></a>필수 조건

- SQL Data Warehouse에 복사할 데이터가 포함된 테이블이 있는 SQL Server 데이터베이스입니다.  

- Azure Storage 계정이 필요합니다. [무료 Azure 계정을 열거나](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) 또는 [Visual Studio 구독자 혜택을 활성화](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다. 

- 온라인 SQL Data Warehouse가 필요합니다. 데이터 웨어하우스가 아직 없는 경우 [Azure SQL Data Warehouse를 만드는 방법](sql-data-warehouse-get-started-provision.md)을 알아보세요. 최적의 성능을 위해 저장소 계정 및 데이터 웨어하우스를 동일한 Azure 지역에 배치합니다.

- 하나 이상의 테이블 스키마를 만들어 들어오는 데이터를 수신하기 위해 데이터 웨어하우스를 준비합니다. [SQL Data Warehouse 마이그레이션 유틸리티](sql-data-warehouse-migrate-migration-utility.md)를 사용하여 스키마에 대한 스크립트를 만들 수 있습니다. 

## <a name="configure-a-new-data-factory"></a>새 데이터 팩터리 구성
1. [Azure 포털][]에 로그인합니다.
2. 데이터 웨어하우스를 찾고 클릭하여 엽니다. 
3. **속성** 블레이드에서 **데이터 로드 > Azure Data Factory**를 클릭합니다.

    ![데이터 로드 마법사 시작](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)
4. Azure 구독에 Data Factory가 없으면 브라우저의 별도 탭에 **새 Data Factory** 대화 상자가 나타납니다. 요청된 정보를 입력하고 **만들기**를 클릭합니다. Data Factory를 만들면 **새 Data Factory** 대화 상자가 닫히고 **Data Factory 선택** 대화 상자가 표시됩니다.

    Azure 구독에 이미 하나 이상의 Data Factory가 있는 경우 **Data Factory 선택** 대화 상자가 표시됩니다. 이 대화 상자에서 기존 Data Factory를 선택하거나 **새 Data Factory 만들기**를 클릭하여 새 Data Factory를 만들 수 있습니다. 
5. **데이터 팩터리 선택** 대화 상자에는 **데이터 로드** 옵션이 기본적으로 선택되어 있습니다. **다음**을 클릭하여 데이터 로드 작업 만들기를 시작합니다. 

## <a name="configure-the-data-factory-properties"></a>데이터 팩터리 속성 구성
이제 데이터 팩터리를 만들었고 다음 단계에서는 데이터 로딩 일정을 구성합니다. 

1. **속성**을 선택하고 요청된 정보를 입력합니다.
2. **작업 이름**에 **DWLoadData-fromSQLServer**를 입력합니다.
3. **다음**을 클릭합니다.

    ![로드 일정 구성](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-data-factory-source-and-gateway"></a>데이터 팩터리 원본 및 게이트웨이 구성
이제 Data Factory에 데이터를 로드할 원본 온-프레미스 SQL Server 데이터베이스에 대한 정보를 알려 줍니다.

1. **원본**을 클릭합니다.
2. 지원되는 원본 데이터 저장소 카탈로그에서 **SQL Server**를 선택하고 **다음**을 클릭합니다.

    ![SQL Server 원본 선택](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

3. **온-프레미스 SQL Server 데이터베이스 지정** 대화 상자가 나타납니다. 다음과 같이 필수 필드를 입력합니다.

    - **연결 이름**: 연결에 대한 새 이름을 지정합니다.
    - **서버 이름**: 온-프레미스 SQL Server의 이름입니다.
    - **데이터베이스 이름**: SQL Server 데이터베이스.
    - **자격 증명 암호화**: 없음. 
    - **인증 유형**: 사용 중인 인증 유형을 선택합니다.
    - **사용자 이름** 및 **암호**: 데이터를 복사할 권한이 있는 사용자에 대한 사용자 이름 및 암호를 입력합니다.

4. 마지막 필드는 게이트웨이 이름을 요청합니다. 원본 데이터 저장소가 온-프레미스 또는 Azure IaaS 가상 컴퓨터에 있는 경우 게이트웨이가 필요합니다. 이미 게이트웨이가 있는 기존 Data Factory를 사용하는 경우 드롭다운 목록에서 게이트웨이를 선택하여 재사용할 수 있습니다. **게이트웨이 만들기** 링크를 클릭하여 데이터 관리 게이트웨이를 만듭니다.  

    > [!NOTE]
    > 게이트웨이는 Data Factory와 1-1 관계를 갖습니다. 다른 Data Factory에서는 사용할 수 없지만 동일한 Data Factory에서 여러 데이터 로드 작업으로 사용할 수 있습니다. 게이트웨이는 데이터 로드 작업을 실행할 때 여러 데이터 저장소에 연결하는 데 사용할 수 있습니다.
    > 
    > 게이트웨이에 대한 자세한 내용은 [데이터 관리 게이트웨이](../data-factory/data-factory-data-management-gateway.md) 문서를 참조하세요. 

5. **게이트웨이 만들기** 대화 상자가 나타납니다. 이름에는 **GatewayForDWLoading**을 입력하고 **만들기**를 클릭합니다.

6. **게이트웨이 구성** 대화 상자가 나타납니다.  
    ![빠른 설치 시작](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

7. 현재 컴퓨터(포털에 액세스하는 데 사용 중인 컴퓨터)에 데이터 관리 게이트웨이를 다운로드, 설치 및 등록하려면 **이 컴퓨터에서 빠른 설치 시작**을 클릭합니다. 컴퓨터를 데이터 저장소에 연결할 수 없는 경우 데이터 저장소에 연결할 수 있는 컴퓨터에 수동으로 [게이트웨이를 다운로드하고 설치](https://www.microsoft.com/download/details.aspx?id=39717)한 다음 키를 사용하여 등록할 수 있습니다. 진행 상태가 팝업 창에 표시됩니다.

    > [!NOTE]
    > 빠른 설치는 Microsoft Edge 및 Internet Explorer에서 고유하게 작동합니다. Google Chrome을 사용 중인 경우 먼저 Chrome 웹 스토어에서 ClickOnce 확장을 설치합니다. 

8. 게이트웨이 설치가 완료될 때까지 기다립니다. 게이트웨이가 성공적으로 등록되고 온라인 상태이면 팝업 창이 닫히고 새 게이트웨이가 게이트웨이 필드에 나타납니다. **Next**를 클릭합니다.

9. 다음 단계는 데이터를 복사할 원본 테이블을 선택하는 것입니다. 키워드를 사용하여 테이블을 필터링할 수 있습니다. 아래쪽 패널에서 데이터 및 테이블 스키마를 미리 볼 수 있습니다. 선택을 완료했으면 **다음**을 클릭합니다.

    ![테이블 선택](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>대상으로 SQL Data Warehouse 구성

1. **대상**을 클릭합니다. SQL Data Warehouse 연결 정보가 자동으로 채워집니다.
2. 사용자 이름 암호를 입력합니다. **다음**을 클릭합니다.

    ![대상 구성](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

3. 유사한 이름에 따라 원본을 대상 테이블에 매핑하는 지능형 테이블 매핑이 나타납니다. Ad
4.  테이블을 위한 매핑이며 나머지는 예제를 통한 학습에 의해 자동으로 매핑됩니다. 
5. 검토하고 **다음**을 클릭합니다.

    ![맵 테이블](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

6. 스키마 매핑을 검토하고 오류 메시지를 확인합니다. 지능형 매핑은 열 이름을 기반으로 합니다. 원본 및 대상 열 간에 지원되지 않는 데이터 형식이 있는 경우 해당 테이블과 함께 오류 메시지가 표시됩니다.

    ![맵 스키마](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

7. **Next**를 클릭합니다.

## <a name="configure-the-performance-settings"></a>성능 설정 구성
성능 구성에서 데이터를 SQL Data Warehouse에 로드하기 전에 데이터 준비에 사용할 Azure 저장소 계정을 구성합니다.

1. **성능**을 클릭합니다. 
2. 기존 Azure Storage 계정을 선택하거나 **다음**을 클릭합니다.

    ![준비 Blob 구성](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>요약 정보 검토 및 파이프라인 배포

1. **요약**을 클릭하고 정보를 검토합니다.
2. **마침** 단추를 클릭하여 파이프라인을 배포합니다.

    ![데이터 팩터리 배포](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>데이터 로드 진행 상태 모니터

배포가 완료되면 왼쪽 메뉴에 **배포** 옵션이 나타납니다. 

1. **배포**를 클릭합니다.
2. 데이터 로딩 진행 상태를 모니터링하려면 **복사 파이프라인을 모니터링하려면 여기를 클릭** 링크를 클릭합니다.

    ![파이프라인 모니터링](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

3. **리소스 탐색기**에서 파이프라인 노드를 확장하고 이 자습서에서 만든 **DWLoadData-fromSQL
4. Server** 데이터 로딩 파이프라인을 클릭합니다.

    ![파이프라인 보기](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

5. 파이프라인을 클릭하여 활동에 매핑되는 각 테이블에 대한 자세한 상태를 확인합니다.

    ![테이블 활동 보기](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

6. 활동을 추가로 클릭하면 오른쪽 창에 데이터 크기, 행, 처리량 등 데이터 로딩 세부 정보가 표시됩니다.

    ![테이블 활동 세부 정보 보기](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

7. 이 모니터링 보기를 나중에 시작하려면 SQL Data Warehouse로 이동하고 **데이터 로드 > Azure Data Factory**를 클릭한 후 팩터리를 선택하고 **기존 로딩 작업 모니터링**을 선택합니다.

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


<!--HONumber=Dec16_HO2-->



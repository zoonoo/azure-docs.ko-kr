---
title: "Azure Data Factory를 사용하여 Azure SQL Data Warehouse에 데이터 로드 | Microsoft Docs"
description: "Azure Data Factory를 사용하여 Azure SQL Data Warehouse로 데이터 복사"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 36e24da50386d1abc441e2beb09f570a9612a346
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure SQL Data Warehouse에 데이터 로드

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)는 거대한 양의 관계형 및 비관계형 데이터를 처리할 수 있는 클라우드 기반 규모 확장 데이터베이스입니다.  대규모 병렬 처리(MPP) 아키텍처를 기반으로 하는 SQL Data Warehouse는 엔터프라이즈 데이터 웨어하우스 워크로드에 최적화됩니다.  저장소를 확장하고 개별적으로 계산할 수 있는 클라우드 탄력성을 유연하게 제공합니다.

이제는 Azure SQL Data Warehouse를 시작하는 것이 **Azure Data Factory**를 사용하는 것보다 더 쉽습니다.  Azure Data Factory는 완벽하게 관리되는 클라우드 기반 데이터 통합 서비스이며, SQL Data Warehouse를 기존 시스템에서 나온 데이터로 채우는 데 사용되므로 SQL Data Warehouse를 평가하고 분석 솔루션을 빌드하는 동안 소중한 시간을 절약할 수 있습니다. Azure Data Factory를 사용하여 Azure SQL Data Warehouse로 데이터를 로드하는 방법의 주요 이점은 다음과 같습니다.

* **간편한 설정**: 스크립팅이 필요하지 않은 직관적인 5단계 마법사.
* **풍부한 데이터 저장소 지원**: 풍부한 온-프레미스 및 클라우드 기반 데이터 저장소에 대한 기본 제공 지원은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표의 자세한 목록을 참조하세요.
* **보안 및 호환**: 데이터가 HTTPS 또는 ExpressRoute를 통해 전송되고 글로벌 서비스를 제공하므로 데이터가 지리적 경계를 벗어나지 않음
* **PolyBase를 통해 제공되는 뛰어난 성능**: 데이터를 Azure SQL Data Warehouse로 이동하는 가장 효율적인 방법은 Polybase를 사용하는 것입니다. 스테이징 Blob 기능을 사용하면 Polybase가 기본적으로 지원하는 Azure Blob Storage 및 Data Lake Store를 제외한 모든 유형의 데이터 저장소에서 높은 로드 속도를 얻을 수 있습니다. 자세한 내용은 [복사 작업 성능](copy-activity-performance.md)을 참조하세요.

이 문서에서는 Data Factory 데이터 복사 도구를 사용하여 **Azure SQL Database의 데이터를 Azure SQL Data Warehouse로 로드**하는 방법을 설명합니다. 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

> [!NOTE]
>  Azure SQL Data Warehouse 간에 데이터를 복사하는 Data Factory 기능에 대한 일반적인 내용은 [Azure Data Factory를 사용하여 Azure SQL Data Warehouse 간 데이터 복사](connector-azure-sql-data-warehouse.md) 문서를 참조하세요.
>
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 복사 작업](v1/data-factory-data-movement-activities.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
* **Azure SQL Data Warehouse** 이 데이터 웨어하우스에는 SQL Database에서 복사된 데이터를 보관하고 있습니다. 아직 없는 경우 Azure SQL Data Warehouse를 만드는 단계는 [SQL Data Warehouse 만들기](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) 문서를 참조하세요.
* **Azure SQL Database**. 이 자습서에서는 [Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md) 문서에 따라 만들 수 있는 Adventure Works LT 샘플 데이터가 포함된 Azure SQL Database의 데이터를 복사합니다. 
* **Azure Storage 계정**. Azure Storage는 대량 복사 작업에서 **스테이징** Blob로 사용됩니다. 아직 없는 경우 Azure Storage 계정을 만드는 단계는 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**을 클릭하고 **Data Factory**를 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 아래 스크린샷에 표시된 대로 값을 제공합니다.
      
     ![새 데이터 팩터리 페이지](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
   * **이름.** 데이터 팩터리의 고유한 이름을 입력합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameADFTutorialDataFactory) 다시 만듭니다. Data Factory 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
            `Data factory name "LoadSQLDWDemo" is not available`

    * **구독.** 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
    * **리소스 그룹.** 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 옵션을 선택하고 리소스 그룹의 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
    * **버전.** **V2(미리 보기)**를 선택합니다.
    * **위치.** 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용하는 데이터 저장소(Azure Storage, Azure SQL Database 등)는 다른 위치/지역에 있을 수 있습니다. 

3. **만들기**를 클릭합니다.
4. 만들기가 완료된 다음, 데이터 팩터리로 이동하면 이미지와 같은 **Data Factory** 페이지가 표시됩니다. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 데이터 통합 응용 프로그램을 시작합니다.
   
   ![데이터 팩터리 홈페이지](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

## <a name="load-data-into-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 데이터 로드

1. 시작 페이지에서 **데이터 복사** 타일을 클릭하여 데이터 복사 도구를 실행합니다. 

   ![데이터 복사 도구 타일](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. 데이터 복사 도구의 **속성** 페이지에서 **작업 이름**에 대해 **CopyFromSQLToSQLDW**를 지정하고 **다음**을 클릭합니다. 

    ![데이터 복사 도구 - 속성 페이지](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. **원본 데이터 저장소** 페이지에서 **Azure SQL Database**를 선택하고 **다음**을 클릭합니다.

    ![원본 데이터 저장소 페이지](./media/load-azure-sql-data-warehouse/specify-source.png)
4. **Azure SQL Database 지정** 페이지에서 다음 단계를 수행합니다. 
    1. **서버 이름**에 대해 Azure SQL Server를 선택합니다.
    2. **데이터베이스 이름**에 대해 Azure SQL Database를 선택합니다.
    3. **사용자 이름**에 대해 사용자의 이름을 지정합니다.
    4. **암호**에 대해 사용자의 암호를 지정합니다.
    5. **다음**을 클릭합니다. 

        ![Azure SQL DB 지정](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. **데이터를 복사할 테이블 선택또 는 사용자 지정 쿼리 사용** 페이지의 입력 상자에 **SalesLT**를 지정하여 테이블을 필터링한 후 **(모두 선택)** 확인란을 선택하여 모든 테이블을 선택하고 **다음**을 클릭합니다. 

    ![입력 파일 또는 폴더 선택](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. **대상 데이터 저장소** 페이지에서 **Azure SQL Data Warehouse**를 선택하고 **다음**을 클릭합니다. 

    ![대상 데이터 저장소 페이지](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. **Azure SQL Data Warehouse 지정** 페이지에서 다음 단계를 수행합니다. 

    1. **서버 이름**에 대해 Azure SQL Server를 선택합니다.
    2. **데이터베이스 이름**에 대해 Azure SQL Data Warehouse를 선택합니다.
    3. **사용자 이름**에 대해 사용자의 이름을 지정합니다.
    4. **암호**에 대해 사용자의 암호를 지정합니다.
    5. **다음**을 클릭합니다. 

        ![Azure SQL Data Warehouse 지정](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. **테이블 매핑** 페이지에서 내용을 검토한 후 **다음**을 클릭합니다. 테이블 이름에 따라 원본을 대상 테이블에 매핑하는 지능형 테이블 매핑이 나타납니다. 해당 테이블이 대상에 없으면, 기본적으로 Azure Data Factory는 같은 이름의 테이블을 만듭니다. 또한 기존 테이블에 매핑할 수도 있습니다. 

    > [!NOTE]
    > SQL Data Warehouse 싱크에 대한 자동 테이블 만들기는 SQL Server 또는 Azure SQL Database가 원본일 때 적용됩니다. 다른 원본 데이터 저장소에서 데이터를 복사하는 경우, 데이터 복사 전에 싱크 Azure SQL Data Warehouse에서 스키마를 미리 만들어야 합니다.

    ![테이블 매핑 페이지](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. **스키마 매핑** 페이지에서 내용을 검토하고 **다음**을 클릭합니다. 지능형 매핑은 열 이름을 기반으로 합니다. 데이터 팩터리에서 테이블을 자동으로 만들 수 있도록 선택하는 경우 소스 및 대상 저장소 간의 비 호환성을 해결할 필요성이 있으면 적절한 데이터 형식 변환이 발생할 수 있습니다. 원본 및 대상 열 간에 지원되지 않는 데이터 형식이 있는 경우 해당 테이블과 함께 오류 메시지가 표시됩니다.

    ![스키마 매핑 페이지](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. **설정** 페이지의 **저장소 계정 이름** 드롭다운 목록에서 Azure Storage를 선택합니다. PolyBase를 사용하여 SQL Data Warehouse에 로드하기 전에, 데이터를 준비하는 데 사용됩니다. 복사가 완료되면 저장소의 중간 데이터는 자동으로 정리됩니다. 

    "고급 설정"에서 "사용 유형 기본값" 옵션을 선택 취소합니다.

    ![설정 페이지](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. **요약** 페이지에서 설정을 검토하고 **다음**을 클릭합니다.

    ![요약 페이지](./media/load-azure-sql-data-warehouse/summary-page.png)
13. **배포 페이지**에서 **모니터**를 클릭하여 파이프라인(작업)을 모니터링합니다.

    ![배포 페이지](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에서 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 표시됩니다. 

    ![파이프라인 실행 모니터링](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 클릭합니다. 파이프라인에는 각각이 하나의 데이터 테이블을 복사하는 10개의 복사 작업이 있습니다. 파이프라인 실행 보기로 다시 전환하려면 위쪽의 **파이프라인** 링크를 클릭합니다. **새로 고침**을 클릭하여 목록을 새로 고칩니다. 

    ![작업 실행 모니터링](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. 작업 모니터링 보기의 **작업** 아래에 있는 **세부 정보** 링크를 클릭하여 각 복사 작업의 실행 세부 정보를 추가로 모니터링할 수 있습니다. 여기에는 원본에서 싱크로 복사되는 데이터의 양, 처리량, 해당 기간 동안 진행하는 단계 및 사용된 구성을 포함한 정보가 표시됩니다.

    ![작업 실행 세부 정보 모니터링](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>다음 단계

Azure SQL Data Warehouse 지원에 대한 자세한 내용은 다음 문서를 참조하세요. 

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse 커넥터](connector-azure-sql-data-warehouse.md)
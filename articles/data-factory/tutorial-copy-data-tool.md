---
title: "Azure 데이터 복사 도구를 사용하여 데이터 복사 | Microsoft Docs"
description: "Azure 데이터 팩터리를 만든 다음, 데이터 복사 도구를 사용하여 Azure Blob Storage에서 Azure SQL Database로 데이터를 복사합니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: a8c7035ebf462bb168147e9d8eb60742333ce8b8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-copy-data-tool"></a>데이터 복사 도구를 사용하여 Azure Blob에서 Azure SQL Database로 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [버전 2 - 미리 보기](tutorial-copy-data-tool.md)

이 자습서에서는 Azure Portal을 사용하여 데이터 팩터리를 만듭니다. 그런 다음 데이터 복사 도구를 사용하여 Azure Blob Storage에서 Azure SQL Database로 데이터를 복사하는 파이프라인을 만듭니다. 

> [!NOTE]
> - Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.
>
> - 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.


이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 복사 도구를 사용하여 파이프라인 만들기
> * 파이프라인 및 작업 실행을 모니터링합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
* **Azure Storage 계정**. Blob 저장소를 **원본** 데이터 저장소로 사용합니다. 아직 없는 경우 Azure Storage 계정을 만드는 단계는 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요.
* **Azure SQL Database**. 데이터베이스를 **싱크** 데이터 저장소로 사용합니다. 아직 없는 경우 Azure SQL Database를 만드는 단계는 [Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md) 문서를 참조하세요.

### <a name="create-a-blob-and-a-sql-table"></a>Blob 및 SQL 테이블 만들기

이제 다음 단계를 수행하여 자습서에서 사용할 Azure Blob 및 Azure SQL Database를 준비합니다.

#### <a name="create-a-source-blob"></a>원본 Blob 만들기

1. 메모장을 시작합니다. 다음 텍스트를 복사하여 **inputEmp.txt** 파일로 디스크에 저장합니다.

    ```
    John|Doe
    Jane|Doe
    ```

2. [Azure Storage 탐색기](http://storageexplorer.com/)와 같은 도구를 사용하여 **adfv2tutorial** 컨테이너를 만들고 **inputEmp.txt** 파일을 이 컨테이너에 업로드합니다.

#### <a name="create-a-sink-sql-table"></a>싱크 SQL 테이블 만들기

1. 다음 SQL 스크립트를 사용하여 Azure SQL Database에 **dbo.emp** 테이블을 만듭니다.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Azure 서비스에서 SQL 서버에 액세스하도록 허용합니다. Data Factory 서비스에서 Azure SQL 서버에 데이터를 쓸 수 있도록 Azure SQL 서버에 대해 **Azure 서비스 방문 허용** 설정을 사용하도록 설정되어 있는지 확인합니다. 이 설정을 확인하고 켜려면 다음 단계를 수행합니다.

    1. 왼쪽의 **더 많은 서비스** 허브를 클릭하고 **SQL 서버**를 클릭합니다.
    2. 서버를 선택하고 **설정** 아래의 **방화벽**을 클릭합니다.
    3. **방화벽 설정** 페이지에서 **Azure 서비스에 대한 액세스 허용**에 대해 **켜기**를 클릭합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 이름 필드에 대해 다음과 같은 오류가 표시되면 데이터 팩터리의 이름을 변경합니다(예: yournameADFTutorialDataFactory). Data Factory 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
     ![새 데이터 팩터리 페이지](./media/tutorial-copy-data-tool/name-not-available-error.png)
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
      리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)**를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 위치/지역에 있을 수 있습니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media/tutorial-copy-data-tool/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/tutorial-copy-data-tool/data-factory-home-page.png)
10. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스)를 시작합니다. 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>데이터 복사 도구를 사용하여 파이프라인 만들기

1. 시작 페이지에서 **데이터 복사** 타일을 클릭하여 데이터 복사 도구를 실행합니다. 

   ![데이터 복사 도구 타일](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. 데이터 복사 도구의 **속성** 페이지에서 **작업 이름**에 대해 **CopyFromBlobToSqlPipeline**을 지정하고 **다음**을 클릭합니다. Data Factory UI는 작업 이름에 대해 지정한 이름이 있는 파이프라인을 만듭니다. 

    ![데이터 복사 도구 - 속성 페이지](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. **원본 데이터 저장소 페이지**에서 **Azure Blob Storage**를 선택하고 **다음**을 클릭합니다. 원본 데이터는 Azure Blob 저장소에 있습니다. 

    ![원본 데이터 저장소 페이지](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. **Azure Blob Storage 계정 지정** 페이지에서 다음 단계를 수행합니다. 
    1. **연결 이름**에 대해 **AzureStorageLinkedService**를 입력합니다.
    2. 드롭다운 목록에서 **저장소 계정 이름**을 선택합니다.
    3. **다음**을 클릭합니다. 

        ![Blob 저장소 계정 지정](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

        연결된 서비스는 데이터 저장소 또는 계산을 데이터 팩터리에 연결합니다. 여기서는 Azure Storage 연결된 서비스를 만들어 Azure 저장소 계정을 데이터 저장소에 연결합니다. 연결된 서비스에는 런타임에 Data Factory 서비스에서 Blob 저장소에 연결하는 데 사용하는 연결 정보가 있습니다. 데이터 집합은 원본 데이터가 포함된 컨테이너, 폴더 및 파일(선택 사항)을 지정합니다. 
5. **입력 파일 또는 폴더 선택** 페이지에서 다음 단계를 수행합니다.
    
    1. **adfv2tutorial/input** 폴더로 이동합니다.
    2. **inputEmp.txt**를 선택합니다.
    3. **선택**을 클릭합니다. 또는 **inputEmp.txt**를 두 번 클릭할 수도 있습니다. 
    4. **다음**을 클릭합니다. 

    ![입력 파일 또는 폴더 선택](./media/tutorial-copy-data-tool/choose-input-file-folder.png)
6. **파일 형식 설정** 페이지에서 도구가 열과 행 구분 기호를 자동으로 검색하고 **다음**을 클릭합니다. 또한 이 페이지에서 데이터를 미리 보고 입력 데이터의 스키마를 볼 수도 있습니다. 

    ![파일 형식 설정 페이지](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. **대상 데이터 저장소** 페이지에서 **Azure SQL Database**를 선택하고 **다음**을 클릭합니다. 

    ![대상 데이터 저장소 페이지](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. **Azure SQL Database 지정** 페이지에서 다음 단계를 수행합니다. 

    1. **연결 이름**에 대해 **AzureSqlDatabaseLinkedService**를 지정합니다. 
    2. **서버 이름**에 대해 Azure SQL Server를 선택합니다.
    3. **데이터베이스 이름**에 대해 Azure SQL Database를 선택합니다.
    4. **사용자 이름**에 대해 사용자의 이름을 지정합니다.
    5. **암호**에 대해 사용자의 암호를 지정합니다.
    6. **다음**을 클릭합니다. 

        ![Azure SQL Database 지정](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

        데이터 집합은 연결된 서비스와 연결되어야 합니다. 연결된 서비스에는 런타임에 Data Factory 서비스에서 Azure SQL Database에 연결하는 데 사용하는 연결 문자열 있습니다. 데이터 집합은 데이터가 복사될 컨테이너, 폴더 및 파일(선택 사항)을 지정합니다.
9.  **테이블 매핑** 페이지에서 **[dbo].[emp]**를 선택하고 **다음**을 클릭합니다. 

    ![테이블 매핑 페이지](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. **스키마 매핑** 페이지에서 입력 파일의 첫 번째 및 두 번째 열이 **emp** 테이블의 **FirstName** 및 **LastName** 열에 매핑됩니다. 

    ![스키마 매핑 페이지](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. **설정** 페이지에서 **다음**을 클릭합니다. 

    ![설정 페이지](./media/tutorial-copy-data-tool/settings-page.png)
12. **요약** 페이지에서 설정을 검토하고 **다음**을 클릭합니다.

    ![요약 페이지](./media/tutorial-copy-data-tool/summary-page.png)
13. **배포 페이지**에서 **모니터**를 클릭하여 파이프라인(작업)을 모니터링합니다.

    ![배포 페이지](./media/tutorial-copy-data-tool/deployment-page.png)
14. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에서 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 표시됩니다. **새로 고침**을 클릭하여 목록을 새로 고칩니다. 

    ![파이프라인 실행 모니터링](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 클릭합니다. 파이프라인에는 하나의 활동(복사 활동)만 있으므로 하나의 항목만 표시됩니다. 복사 활동에 대한 자세한 내용은 **작업** 열에서 **세부 정보** 링크(안경 아이콘)를 클릭합니다. 파이프라인 실행 보기로 다시 전환하려면 위쪽의 **파이프라인** 링크를 클릭합니다. 보기를 새로 고치려면 **새로 고침**을 클릭합니다. 

    ![작업 실행 모니터링](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. 편집기 모드로 전환하려면 왼쪽의 **편집** 탭을 클릭합니다. 편집기를 사용하여 도구에서 만든 연결된 서비스, 데이터 집합 및 파이프라인을 업데이트할 수 있습니다. **코드**를 클릭하여 편집기에서 열린 엔터티와 연결된 JSON 코드를 살펴봅니다. Data Factory UI에서 이러한 엔터티를 편집하는 방법에 대한 자세한 내용은 [이 자습서의 Azure Portal 버전](tutorial-copy-data-portal.md)을 참조하세요.

    ![편집기 탭](./media/tutorial-copy-data-tool/edit-tab.png)
17. 데이터가 Azure SQL Database의 **emp** 테이블에 삽입되어 있는지 확인합니다. 

    ![SQL 출력 확인](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인이 Azure Blob Storage에서 Azure SQL Database로 데이터를 복사합니다. 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 복사 도구를 사용하여 파이프라인 만들기
> * 파이프라인 및 작업 실행을 모니터링합니다.

온-프레미스에서 클라우드로 데이터를 복사하는 방법을 알아보려면 다음 자습서로 진행하세요. 

> [!div class="nextstepaction"]
>[온-프레미스에서 클라우드로 데이터 복사](tutorial-hybrid-copy-data-tool.md)
---
title: Azure Portal을 사용하여 Data Factory 파이프라인 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure Portal을 사용하여 파이프라인이 있는 데이터 팩터리를 만드는 방법에 대한 단계별 지침을 제공합니다. 파이프라인은 복사 활동을 사용하여 Azure Blob 저장소에서 SQL 데이터베이스로 데이터를 복사합니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 47fc3b44719caf430edf026bf776c4e85764ad08
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048440"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Blob 저장소에서 SQL 데이터베이스로 데이터 복사
이 자습서에서는 Azure Data Factory UI(사용자 인터페이스)를 사용하여 데이터 팩터리를 만듭니다. 데이터 팩터리의 파이프라인은 Azure Blob 저장소에서 SQL 데이터베이스로 데이터를 복사합니다. 이 자습서의 구성 패턴은 파일 기반 데이터 저장소에서 관계형 데이터 저장소로 복사하는 데 적용됩니다. 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

> [!NOTE]
> - Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 자습서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 복사 활동이 있는 파이프라인 만들기
> * 파이프라인 실행 테스트
> * 수동으로 파이프라인 트리거
> * 일정에 따라 파이프라인 트리거
> * 파이프라인 및 작업 실행을 모니터링합니다.

## <a name="prerequisites"></a>필수 조건
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
* **Azure Storage 계정**. Blob 저장소를 *원본* 데이터 저장소로 사용합니다. 저장소 계정이 없는 경우 [Azure 저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하세요.
* **Azure SQL Database**. 데이터베이스를 *싱크* 데이터 저장소로 사용합니다. SQL 데이터베이스가 없는 경우 [SQL 데이터베이스 만들기](../sql-database/sql-database-get-started-portal.md)를 참조하세요.

### <a name="create-a-blob-and-a-sql-table"></a>Blob 및 SQL 테이블 만들기

이제 다음 단계를 수행하여 자습서에서 사용할 Blob 저장소 및 SQL 데이터베이스를 준비합니다.

#### <a name="create-a-source-blob"></a>원본 Blob 만들기

1. 메모장을 시작합니다. 다음 텍스트를 복사하고 디스크에 **emp.txt** 파일로 저장합니다.

    ```
    John,Doe
    Jane,Doe
    ```

2. Blob 저장소에 **adftutorial**이라는 컨테이너를 만듭니다. 이 컨테이너에 **input**이라는 폴더를 만듭니다. 그런 다음 **emp.txt** 파일을 **input** 폴더에 업로드합니다. 이러한 작업을 수행하려면 Azure Portal 또는 [Azure Storage 탐색기](http://storageexplorer.com/)와 같은 도구를 사용합니다.

#### <a name="create-a-sink-sql-table"></a>싱크 SQL 테이블 만들기

1. 다음 SQL 스크립트를 사용하여 SQL 데이터베이스에 **dbo.emp** 테이블을 만듭니다.

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

2. Azure 서비스에서 SQL Server에 액세스하도록 허용합니다. Data Factory에서 SQL Server에 데이터를 쓸 수 있도록 SQL Server에 대해 **Azure 서비스 방문 허용**이 **켜기**로 설정되어 있는지 확인합니다. 이 설정을 확인하고 설정하려면 다음 단계를 수행합니다.

    a. 왼쪽에서 **더 많은 서비스** > **SQL 서버**를 차례로 선택합니다.

    나. 서버를 선택하고 **설정** 아래에서 **방화벽**을 선택합니다.

    다. **방화벽 설정** 페이지에서 **Azure 서비스 방문 허용**에 대해 **켜기**를 선택합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
이 단계에서는 데이터 팩터리를 만들고, Data Factory UI를 시작하여 파이프라인을 데이터 팩터리에 만듭니다. 

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 엽니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. 왼쪽 메뉴에서 **새로 만들기** > **데이터 + 분석** > **Data Factory**를 차례로 선택합니다. 
  
   ![새 데이터 팩터리 만들기](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
3. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 data factory](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 *전역적으로 고유*해야 합니다. 이름 필드에 대해 다음과 같은 오류 메시지가 표시되면 데이터 팩터리의 이름을 변경합니다(예: yournameADFTutorialDataFactory). Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory 명명 규칙](naming-rules.md)을 참조하세요.
  
   ![오류 메시지](./media/tutorial-copy-data-portal/name-not-available-error.png)
4. 데이터 팩터리를 만들 Azure **구독**을 선택합니다. 
5. **리소스 그룹**에 대해 다음 단계 중 하나를 사용합니다.
     
    a. **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.

    나. **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요. 
6. **버전**에서 **V2**를 선택합니다.
7. **위치**에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(예: Azure Storage, SQL Database) 및 계산(예: Azure HDInsight)은 다른 지역에 있을 수 있습니다.
8. **대시보드에 고정**을 선택합니다. 
9. **만들기**를 선택합니다. 
10. 대시보드에서 **데이터 팩터리 배포 중** 상태의 다음과 같은 타일이 표시됩니다. 

    ![Data Factory 배포 중 타일](media/tutorial-copy-data-portal/deploying-data-factory.png)
11. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
    ![데이터 팩터리 홈페이지](./media/tutorial-copy-data-portal/data-factory-home-page.png)
12. **작성 및 모니터링**을 선택하여 별도의 탭에서 Data Factory UI를 선택합니다.

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
이 단계에서는 복사 활동이 있는 파이프라인을 데이터 팩터리에 만듭니다. 복사 활동은 Blob 저장소에서 SQL Database로 데이터를 복사합니다. [빠른 시작 자습서](quickstart-create-data-factory-portal.md)에서 다음 단계에 따라 파이프라인을 만들었습니다.

1. 연결된 서비스를 만듭니다. 
2. 입력 및 출력 데이터 집합을 만듭니다.
3. 파이프라인을 만듭니다.

이 자습서에서는 파이프라인을 만드는 것부터 시작합니다. 그런 다음, 파이프라인을 구성해야 할 때 연결된 서비스와 데이터 집합을 만듭니다. 

1. **시작** 페이지에서 **파이프라인 만들기**를 선택합니다. 

   ![파이프라인 만들기](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
2. 파이프라인의 **일반** 탭에 있는 파이프라인의 **이름**에 대해 **CopyPipeline**을 입력합니다.

3. **활동** 도구 상자에서 **데이터 흐름** 범주를 펼치고, **복사** 활동을 도구 상자에서 파이프라인 디자이너 화면으로 끌어서 놓습니다. **이름**에 대해 **CopyFromBlobToSql**을 지정합니다.

    ![복사 활동](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>원본 구성

1. **원본** 탭으로 이동합니다. **+ 새로 만들기**를 선택하여 원본 데이터 집합을 만듭니다. 

2. **새 데이터 집합** 창에서 **Azure Blob Storage**를 선택한 다음, **마침**을 선택합니다. 원본 데이터는 Blob 저장소에 있으므로 원본 데이터 집합으로 **Azure Blob Storage**를 선택합니다. 

    ![저장소 선택](./media/tutorial-copy-data-portal/select-azure-blob-dataset.png)

3. Blob 데이터 집합에 대해 열린 새 탭이 표시됩니다. **속성** 창의 아래쪽에 있는 **일반** 탭의 **이름**에 대해 **SourceBlobDataset**을 입력합니다.

    ![데이터 집합 이름](./media/tutorial-copy-data-portal/dataset-name.png)

4. **속성** 창의 **연결** 탭으로 이동합니다. **연결된 서비스** 텍스트 상자 옆에 있는 **+ 새로 만들기**를 선택합니다. 

    ![새 연결된 서비스 단추](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)

5. **새로 연결된 서비스** 창에서 **AzureStorageLinkedService**를 이름으로 입력하고, **저장소 계정 이름** 목록에서 저장소 계정의 이름을 선택한 다음, **저장**을 선택하여 연결된 서비스를 배포합니다.

    ![새 연결된 서비스](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)

6. 연결된 서비스를 만든 후에 데이터 집합 설정으로 돌아갑니다. **파일 경로** 옆에 있는 **찾아보기**를 선택합니다.

    ![파일 경로에 대한 찾아보기 단추](./media/tutorial-copy-data-portal/file-browse-button.png)

7. **adftutorial/input** 폴더로 이동하고, **emp.txt** 파일을 선택한 다음, **마침**을 선택합니다. 

    ![입력 파일 선택](./media/tutorial-copy-data-portal/select-input-file.png)

8. **파일 형식**이 **텍스트 형식**으로 설정되고, **열 구분 기호**가 **쉼표(`,`)** 로 설정되어 있는지 확인합니다. 원본 파일에서 다른 행 및 열 구분 기호를 사용하는 경우 **파일 형식**에 대해 **텍스트 형식 검색**을 선택할 수 있습니다. 데이터 복사 도구에서 파일 형식과 구분 기호를 자동으로 검색합니다. 이러한 값은 여전히 재정의할 수 있습니다. 이 페이지에서 데이터를 미리 보려면 **데이터 미리 보기**를 선택합니다.

    ![텍스트 형식 검색](./media/tutorial-copy-data-portal/detect-text-format.png)

9. **속성** 창의 **스키마** 탭으로 이동하고 **스키마 가져오기**를 선택합니다. 응용 프로그램이 원본 파일에서 두 개의 열을 검색했습니다. 원본 데이터 저장소의 열을 싱크 데이터 저장소에 매핑할 수 있도록 스키마를 여기로 가져옵니다. 열을 매핑할 필요가 없으면 이 단계를 건너뛸 수 있습니다. 이 자습서에서는 스키마를 가져옵니다.

    ![원본 스키마 검색](./media/tutorial-copy-data-portal/detect-source-schema.png)  

10. 이제 다시 파이프라인 -> **원본** 탭으로 이동하고 **SourceBlobDataset**을 선택했는지 확인합니다. 이 페이지에서 데이터를 미리 보려면 **데이터 미리 보기**를 선택합니다. 
    
    ![원본 데이터 집합](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>싱크 구성

1. **싱크** 탭으로 이동하고, **+ 새로 만들기**를 선택하여 싱크 데이터 집합을 만듭니다. 

    ![싱크 데이터 집합](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
2. **새 데이터 집합** 창에서 검색 상자에 "SQL"을 입력하고, **Azure SQL Database**를 선택한 다음, **마침**을 선택합니다. 이 자습서에서는 데이터를 SQL 데이터베이스에 복사합니다. 

    ![SQL 데이터베이스 선택](./media/tutorial-copy-data-portal/select-azure-sql-dataset.png)
3. **속성** 창에 있는 **일반** 탭의 **이름**에서 **OutputSqlDataset**를 설정합니다. 
    
    ![출력 데이터 집합 이름](./media/tutorial-copy-data-portal/output-dataset-name.png)
4. **연결** 탭으로 이동하여 **연결된 서비스** 옆에 있는 **+ 새로 만들기**를 선택합니다. 데이터 집합은 연결된 서비스와 연결되어야 합니다. 연결된 서비스에는 런타임에 Data Factory에서 SQL 데이터베이스에 연결하는 데 사용하는 연결 문자열 있습니다. 데이터 집합은 데이터가 복사될 컨테이너, 폴더 및 파일(선택 사항)을 지정합니다. 
    
    ![연결된 서비스](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
5. **새 연결된 서비스** 창에서 다음 단계를 수행합니다. 

    a. **이름** 아래에서 **AzureSqlDatabaseLinkedService**를 입력합니다.

    나. **서버 이름** 아래에서 SQL Server 인스턴스를 선택합니다.

    다. **데이터베이스 이름** 아래에서 SQL 데이터베이스를 선택합니다.

    d. **사용자 이름** 아래에서 사용자의 이름을 입력합니다.

    e. **암호** 아래에서 사용자의 암호를 입력합니다.

    f. **연결 테스트**를 선택하여 연결을 테스트합니다.

    g. **저장**을 선택하여 연결된 서비스를 저장합니다. 
    
    ![새 연결된 서비스 저장](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

6. **테이블**에서 **[dbo].[emp]** 를 선택합니다. 

    ![테이블](./media/tutorial-copy-data-portal/select-emp-table.png)
7. **스키마** 탭으로 이동하고 **스키마 가져오기**를 선택합니다. 

    ![스키마 가져오기 선택](./media/tutorial-copy-data-portal/import-destination-schema.png)
8. **ID** 열을 선택한 다음, **삭제**를 선택합니다. **ID** 열은 SQL 데이터베이스의 ID 열이므로 복사 활동에서 이 열에 데이터를 삽입할 필요가 없습니다.

    ![ID 열 삭제](./media/tutorial-copy-data-portal/delete-id-column.png)
9. 파이프라인이 있는 탭으로 이동하고, **싱크 데이터 집합**에서 **OutputSqlDataset**가 선택되어 있는지 확인합니다.

    ![파이프라인 탭](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        

### <a name="confugure-mapping"></a>매핑 구성

**속성** 창의 아래쪽에 있는 **매핑** 탭으로 이동하고 **스키마 가져오기**를 선택합니다. 원본 파일의 첫 번째 및 두 번째 열은 SQL 데이터베이스의 **FirstName** 및 **LastName**에 매핑됩니다.

![스키마 매핑](./media/tutorial-copy-data-portal/map-schemas.png)

## <a name="validate-the-pipeline"></a>파이프라인 유효성 검사
파이프라인에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사**를 선택합니다.
 
오른쪽 위에서 **코드**를 클릭하여 파이프라인과 연결된 JSON 코드를 볼 수 있습니다.

## <a name="debug-and-publish-the-pipeline"></a>파이프라인 디버그 및 게시
아티팩트(연결된 서비스, 데이터 집합 및 파이프라인)를 Data Factory 또는 고유한 Visual Studio Team Services Git 리포지토리에 게시하기 전에 파이프라인을 디버그할 수 있습니다. 

1. 파이프라인을 디버그하려면 도구 모음에서 **디버그**를 선택합니다. 창의 아래쪽에 있는 **출력** 탭에서 파이프라인 실행 상태가 표시됩니다. 

2. 파이프라인이 성공적으로 실행될 수 있다면 최상위 도구 모음에서 **모두 게시**를 선택합니다. 이 작업은 사용자가 만든 엔터티(데이터 집합 및 파이프라인)를 Data Factory에 게시합니다.

    ![게시](./media/tutorial-copy-data-portal/publish-button.png)

4. **게시됨** 메시지가 표시될 때까지 기다립니다. 알림 메시지를 보려면 왼쪽 위(종 단추)에서 **알림 표시**를 클릭합니다. 

## <a name="trigger-the-pipeline-manually"></a>수동으로 파이프라인 트리거
이 단계에서는 이전 단계에서 게시한 파이프라인을 수동으로 트리거합니다. 

1. 도구 모음에서 **트리거**를 선택한 다음 **지금 트리거**를 선택합니다. **파이프라인 실행** 페이지에서 **마침**을 선택합니다.  

2. 왼쪽의 **모니터** 탭으로 이동합니다. 수동 트리거로 트리거되는 파이프라인 실행이 표시됩니다. **작업** 열의 링크를 사용하여 활동 세부 정보를 보고 파이프라인을 다시 실행할 수 있습니다.

    ![파이프라인 실행 모니터링](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. 파이프라인 실행과 연결된 활동 실행을 확인하려면 **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 이 예에서는 활동이 하나뿐이므로 목록에 하나의 항목만 표시됩니다. 복사 활동에 대한 자세한 내용은 **작업** 열에서 **세부 정보** 링크(안경 아이콘)를 선택합니다. 위쪽의 **파이프라인**을 선택하여 **파이프라인 실행** 보기로 돌아갑니다. 보기를 새로 고치려면 **새로 고침**을 선택합니다.

    ![작업 실행 모니터링](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. SQL 데이터베이스의 **emp** 테이블에 둘 이상의 행이 추가되어 있는지 확인합니다. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>일정에 따라 파이프라인 트리거
이 일정에서 파이프라인에 대한 스케줄러 트리거를 만듭니다. 트리거는 지정된 일정(예: 매시간, 매일)에 따라 파이프라인을 실행합니다. 이 예에서는 지정된 종료 날짜/시간까지 매분마다 실행되도록 트리거를 설정합니다. 

1. 왼쪽의 모니터링 탭 위에서 **작성** 탭으로 이동합니다. 

2. 파이프라인으로 이동하고, 도구 모음에서 **트리거**를 클릭하고, **새로 만들기/편집**을 선택합니다. 

3. **트리거 추가** 창에서 **트리거 선택**, **새로 만들기**를 차례로 선택합니다. 

    ![새 단추](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. **새 트리거** 창에서 다음 단계를 수행합니다. 

    a. **이름** 아래에서 **RunEveryMinute**를 입력합니다.

    나. **종료** 아래에서 **날짜**를 선택합니다.

    다. **종료 날짜/시간** 아래에서 드롭다운 목록을 선택합니다.

    d. **현재 날짜** 옵션을 선택합니다. 기본적으로 종료 날짜는 다음 날로 설정됩니다.

    e. **분** 부분은 현재 날짜/시간 이후 몇 분으로 업데이트합니다. 트리거는 변경 내용을 게시한 후에만 활성화됩니다. 몇 분 이후로 설정하고 그때까지 게시하지 않으면 트리거 실행이 표시되지 않습니다.

    f. **적용**을 선택합니다. 

    ![트리거 속성](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. **활성화됨** 옵션을 선택합니다. 이 확인란을 사용하여 비활성화할 수 있으며, 나중에 활성화할 수 있습니다.

    h. **다음**을 선택합니다.

    ![활성화됨 단추](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > 비용은 각 파이프라인 실행과 연결되므로 종료 날짜를 적절하게 설정합니다. 
5. **트리거 실행 매개 변수** 페이지에서 경고를 검토한 다음, **마침**을 선택합니다. 다음 예의 파이프라인은 매개 변수를 사용하지 않습니다. 

    ![트리거 실행 매개 변수](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)

6. **모두 게시**를 클릭하여 변경 내용을 게시합니다. 

7. 왼쪽의 **모니터** 탭으로 이동하여 트리거된 파이프라인 실행을 확인합니다. 

    ![트리거된 파이프라인 실행](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
8. **파이프라인 실행** 보기에서 **트리거 실행** 보기로 전환하려면 **파이프라인 실행**을 선택한 다음, **트리거 실행**을 선택합니다.
    
    ![트리거 실행](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
9. 목록에서 트리거 실행이 표시됩니다. 

    ![트리거 실행 목록](./media/tutorial-copy-data-portal/trigger-runs-list.png)
10. 지정된 종료 시간까지 각 파이프라인 실행에 대해 분당 두 행이 **emp** 테이블에 삽입되었는지 확인합니다. 

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인이 Blob 저장소의 위치 간에 데이터를 복사합니다. 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 복사 활동이 있는 파이프라인 만들기
> * 파이프라인 실행 테스트
> * 수동으로 파이프라인 트리거
> * 일정에 따라 파이프라인 트리거
> * 파이프라인 및 작업 실행을 모니터링합니다.


온-프레미스에서 클라우드로 데이터를 복사하는 방법을 알아보려면 다음 자습서로 계속 진행하세요. 

> [!div class="nextstepaction"]
>[온-프레미스에서 클라우드로 데이터 복사](tutorial-hybrid-copy-portal.md)

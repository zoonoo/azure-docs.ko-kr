---
title: "Azure Data Factory를 사용하여 SQL Server에서 Blob Storage로 데이터 복사 | Microsoft Docs"
description: "Azure Data Factory에서 자체 호스팅 Integration Runtime을 사용하여 온-프레미스 데이터 저장소에서 Azure 클라우드로 데이터를 복사하는 방법을 알아봅니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: 64cd758e2f40ff2b18abbff1194a7e57389d8a54
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>자습서: 온-프레미스 SQL Server 데이터베이스에서 Azure Blob Storage로 데이터 복사
이 자습서에서는 Azure Data Factory UI(사용자 인터페이스)를 사용하여 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 데이터 팩터리 파이프라인을 만듭니다. 온-프레미스와 클라우드 데이터 저장소 간에 데이터를 이동하는, 자체 호스팅된 통합 런타임을 생성하고 사용합니다. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Azure Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
> 
> 이 문서는 Data Factory 서비스를 자세히 소개하지 않습니다. 자세한 내용은 [Azure Data Factory 소개](introduction.md)를 참조하세요. 

이 자습서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 통합 런타임을 만듭니다.
> * SQL Server 및 Azure Storage 연결된 서비스를 만듭니다. 
> * SQL Server 및 Azure Blob 데이터 집합을 만듭니다.
> * 데이터를 이동하는 복사 작업으로 파이프라인을 만듭니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 실행을 모니터링합니다.

## <a name="prerequisites"></a>필수 조건
### <a name="azure-subscription"></a>Azure 구독
시작하기 전에 Azure 구독이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Azure 역할
Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 *참여자* 또는 *소유자* 역할로 할당되거나 Azure 구독의 *관리자*여야 합니다. 

구독에서 사용 권한을 보려면 Azure Portal로 이동하고, 오른쪽 위 모서리에 있는 사용자 이름을 선택한 다음 **사용 권한**을 선택합니다. 여러 구독에 액세스할 수 있는 경우 적절한 구독을 선택합니다. 사용자를 역할에 추가하는 방법에 대한 샘플 지침은 [역할 추가](../billing/billing-add-change-azure-subscription-administrator.md) 문서를 참조하세요.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 및 2017
이 자습서에서는 온-프레미스 SQL Server 데이터베이스를 *원본* 데이터 저장소로 사용합니다. 이 자습서에서 만든 데이터 팩터리의 파이프라인은 온-프레미스 SQL Server 데이터베이스(원본)에서 Azure Blob Storage(싱크)로 데이터를 복사합니다. 그런 다음 SQL Server 데이터베이스에 **emp**라는 테이블을 만들고 테이블에 동일한 두 개의 샘플 항목을 삽입합니다. 

1. SQL Server Management Studio를 시작합니다. 컴퓨터에 아직 설치되어 있지 않으면 [SQL Server Management Studio 다운로드](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)로 이동합니다. 

2. 자격 증명을 사용하여 SQL server 인스턴스에 연결합니다. 

3. 샘플 데이터베이스 만들기 트리 뷰에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭한 다음 **새 데이터베이스**를 선택합니다. 
 
4. **새 데이터베이스** 창에서 데이터베이스의 이름을 입력하고 **확인**을 선택합니다. 

5. **emp** 테이블을 만들고 일부 샘플 데이터를 이 테이블에 삽입하려면 데이터베이스에 대해 다음 쿼리 스크립트를 실행합니다.

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

6. 트리 뷰에서 생성한 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.

### <a name="azure-storage-account"></a>Azure Storage 계정
이 자습서에서는 범용 Azure Storage 계정(특히 Blob Storage)을 대상/싱크 데이터 저장소로 사용합니다. 범용 Azure Storage 계정이 없는 경우 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하세요. 이 자습서에서 만든 데이터 팩터리의 파이프라인은 온-프레미스 SQL Server 데이터베이스(원본)에서 이 Azure Blob Storage(싱크)로 데이터를 복사합니다. 

#### <a name="get-storage-account-name-and-account-key"></a>저장소 계정 이름 및 계정 키 가져오기
이 자습서에서 Azure Storage 계정 이름 및 키를 사용합니다. 다음을 수행하여 저장소 계정의 이름 및 키를 가져옵니다. 

1. Azure 사용자 이름 및 암호를 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. 왼쪽 창에서 **추가 서비스**를 선택하고, **저장소** 키워드를 사용하여 필터링한 다음 **저장소 계정**을 선택합니다.

    ![저장소 계정 검색](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. 저장소 계정 목록에서 저장소 계정(필요한 경우)을 필터링한 다음 저장소 계정을 선택합니다. 

4. **저장소 계정** 창에서 **액세스 키**를 선택합니다.

    ![저장소 계정 이름 및 키 가져오기](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. **저장소 계정 이름** 및 **key1** 상자에서 값을 복사한 다음 메모장 또는 나중에 자습서에서 사용할 다른 편집기에 붙여넣습니다. 

#### <a name="create-the-adftutorial-container"></a>adftutorial 컨테이너 만들기 
이 섹션에서는 Azure Blob Storage에 **adftutorial**이라는 Blob 컨테이너를 만듭니다. 

1. **저장소 계정** 창에서 **개요**로 전환한 다음, **Blob**을 선택합니다. 

    ![Blob 옵션 선택](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. **Blob service** 창에서 **컨테이너**를 선택합니다. 

    ![컨테이너 단추 추가](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. **새 컨테이너** 창의 **이름** 상자에 **adftutorial**을 입력한 후 **확인**을 선택합니다. 

    ![컨테이너 이름 입력](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. 컨테이너 목록에서 **adftutorial**을 선택합니다.  

    ![컨테이너를 선택합니다.](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. **adftutorial**의 **컨테이너** 창을 열어 둡니다. 이 자습서의 끝부분에서 출력을 확인하는 데 사용합니다. 데이터 팩터리는 이 컨테이너에서 출력 폴더를 자동으로 만듭니다. 따라서 새로 만들 필요가 없습니다.

    ![컨테이너 창](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
이 단계에서는 데이터 팩터리를 만들고, Azure Data Factory UI를 시작하여 파이프라인을 데이터 팩터리에 만듭니다. 

1. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 이름 필드에 대해 다음과 같은 오류가 표시되면 데이터 팩터리의 이름을 변경합니다(예: yournameADFTutorialDataFactory). Data Factory 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
     ![새 데이터 팩터리 페이지](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
        리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)**를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.      
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
10. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 Azure Data Factory UI를 실행합니다. 


## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.

1. **시작** 페이지에서 **파이프라인 만들기**를 클릭합니다. 파이프라인이 자동으로 만들어집니다. 트리 뷰와 열려 있는 편집기에서 파이프라인이 표시됩니다. 

   ![시작 페이지](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. 아래쪽의 **속성** 창에 있는 **일반** 탭에서 **이름**에 대해 **SQLServerToBlobPipeline**을 입력합니다.

   ![파이프라인 이름](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
2. **활동** 도구 상자에서 **데이터 흐름**을 펼치고, **복사** 활동을 파이프라인 디자인 화면으로 끌어서 놓습니다. 활동 이름을 **CopySqlServerToAzureBlobActivity**로 설정합니다.

   ![작업 이름](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
3. 속성 창에서 **원본** 탭으로 전환하고 **+ 새로 만들기**를 클릭합니다.

   ![새 원본 데이터 집합 - 단추](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
4. **새 데이터 집합** 창에서 **SQL Server**를 검색하고, **SQL Server**를 선택하고, **마침**을 클릭합니다. **SqlServerTable1**이라는 제목의 새 탭이 표시됩니다. 또한 왼쪽의 트리 뷰에도 **SqlServerTable1** 데이터 집합이 표시됩니다. 

   ![SQL Server 선택](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
5. [속성] 창의 **일반** 탭에서 **이름**에 대해 **SqlServerDataset**를 입력합니다.
    
   ![원본 데이터 집합 - 이름](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
6. **연결** 탭으로 전환하고 **+ 새로 만들기**를 클릭합니다. 이 단계에서는 원본 데이터 저장소(SQL Server 데이터베이스)에 대한 연결을 만듭니다. 

   ![원본 데이터 집합 - 새 연결 단추](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
7. **새 연결된 서비스** 창에서 **새 통합 런타임**을 클릭합니다. 이 섹션에서는 자체 호스팅 Integration Runtime을 만들고 이를 Microsoft SQL Server 데이터베이스와 함께 온-프레미스 컴퓨터에 연결합니다. 자체 호스팅된 Integration Runtime은 사용자 컴퓨터의 SQL Server 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 구성 요소입니다. 

   ![새 통합 런타임](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
8. **Integration Runtime 설정** 창에서 **사설망**을 선택하고 **다음**을 클릭합니다. 

   ![사설망 선택](./media/tutorial-hybrid-copy-portal/select-private-network.png)
9. 통합 런타임에 대한 이름을 입력하고 **다음**을 클릭합니다.  
    
   ![Integration Runtime - 이름](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
10. **옵션 1: 빠른 설치** 섹션에서 **Click here to launch the express setup for this computer**(이 컴퓨터에 대한 빠른 설치를 시작하려면 여기를 클릭하세요.)를 클릭합니다. 

   ![빠른 설치 링크 클릭](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
11. **Integration Runtime(자체 호스팅) 빠른 설치** 창에서 **닫기**를 클릭합니다. 

   ![Integration Runtime 설정 - 성공](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
12. 웹 브라우저의 **Integration Runtime 설정** 창에서 **마침**을 클릭합니다. 

   ![통합 런타임 설정 - 완료](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
13. **새 연결된 서비스** 창에서 다음 단계를 수행합니다.

    1. **이름**에 대해 **SqlServerLinkedService**를 입력합니다.
    2. **통합 런타임을 통해 연결**에 대해 앞에서 만든 자체 호스팅 통합 런타임이 표시되는지 확인합니다.
    3. **서버 이름**에 대해 SQL Server의 이름을 지정합니다. 
    4. **데이터베이스 이름** 필드에 대해 **emp** 테이블이 있는 데이터베이스의 이름을 지정합니다. 
    5. Data Factory가 SQL Server 데이터베이스에 연결하는 데 사용해야 하는 적절한 **인증 유형**을 선택합니다. 
    6. **사용자 이름** 및 **암호**를 입력합니다. 백슬래시(\\)를 사용자 계정 또는 서버 이름에 사용해야 하는 경우 앞에 이스케이프 문자(\\)를 사용합니다. 예를 들어 *mydomain\\\\myuser*를 사용합니다. 
    7. **연결 테스트**를 클릭합니다. 이 단계를 수행하여 만든 자체 호스팅 통합 런타임을 통해 Data Factory 서비스에서 SQL Server 데이터베이스에 연결할 수 있는지 확인합니다. 
    8. 연결된 서비스를 저장하려면 **저장**을 클릭합니다.

       ![SQL Server 연결된 서비스 - 설정](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
14. 원본 데이터 집합이 열려 있는 창으로 다시 전환됩니다. **속성** 창의 **연결**에서 다음 단계를 수행합니다. 

    1. **연결된 서비스**에 대해 **SqlServerLinkedService**가 표시되는지 확인합니다. 
    2. **테이블**에 대해 **[dbo].[emp]**를 선택합니다.

        ![원본 데이터 집합 - 연결 정보](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
15. SQLServerToBlobPipeline이 있는 탭으로 전환하거나 트리 뷰에서 **SQLServerToBlobPipeline**을 클릭합니다. 

    ![파이프라인 탭](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
16. **속성** 창에서 **싱크** 탭으로 전환하고 **+ 새로 만들기**를 클릭합니다. 

    ![싱크 데이터 집합 - 새로 만들기 단추](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
17. **새 데이터 집합** 창에서 **Azure Blob Storage**를 선택하고 **마침**을 클릭합니다. 데이터 집합에 대해 열린 새 탭이 표시됩니다. 또한 트리 뷰에도 데이터 집합이 표시됩니다. 

    ![Azure Blob Storage 선택](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
18. **이름**에 대해 **AzureBlobDataset**를 입력합니다.

    ![싱크 데이터 집합 - 이름](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
19. **속성** 창에서 **연결** 탭으로 전환하고, **연결된 서비스**에 대한 **+ 새로 만들기**를 클릭합니다. 

    ![새 연결된 서비스 - 단추](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
20. **새 연결된 서비스** 창에서 다음 단계를 수행합니다.

    1. **이름**에 대해 **AzureStorageLinkedService**를 입력합니다.
    2. **저장소 계정 이름**에 대해 Azure Storage 계정을 선택합니다. 
    3. **연결 테스트**를 클릭하여 Azure 저장소 계정에 대한 연결을 테스트합니다.
    4. **저장**을 클릭합니다.

        ![Azure Storage 연결된 서비스 - 설정](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
21.  싱크 데이터 집합이 열려 있는 창으로 다시 전환됩니다. **연결**에서 다음 단계를 수행합니다. 

        1. **연결된 서비스**에 대해 **AzureStorageLinkedService**가 선택되어 있는지 확인합니다.
        2. **파일 경로**의 **폴더** 부분에 대해 **adftutorial/fromonprem**을 입력합니다. 출력 폴더가 adftutorial 컨테이너에 없으면 Data Factory 서비스에서 출력 폴더를 자동으로 만듭니다.
        3. **파일 경로**의 **파일 이름**에 대해 `@CONCAT(pipeline().RunId, '.txt')`를 입력합니다.

            ![싱크 데이터 집합 - 연결](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
22. 파이프라인이 열려 있는 탭으로 전환하거나 **트리 뷰**에서 **파이프라인**을 클릭합니다. **싱크 데이터 집합**에 대해 **AzureBlobDataset**가 선택되어 있는지 확인합니다. 

    ![싱크 데이터 집합 선택 ](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
23. 파이프라인 설정에 대한 유효성을 검사하려면 파이프라인에 대한 도구 모음에서 [유효성 검사]를 클릭합니다. 오른쪽 모서리에서 **X**를 클릭하여 **파이프라인 유효성 검사 보고서**를 닫습니다. 

    ![파이프라인 유효성 검사](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
1. **게시**를 클릭하여 만든 엔터티를 Azure Data Factory 서비스에 게시합니다.

    ![게시 단추](./media/tutorial-hybrid-copy-portal/publish-button.png)
24. **게시 성공** 팝업이 표시될 때까지 기다립니다. 또한 왼쪽의 **알림 표시** 링크를 클릭하여 게시 상태를 확인할 수도 있습니다. **X**를 클릭하여 알림 창을 닫습니다. 

    ![게시 성공](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>파이프라인 실행 트리거
파이프라인에 대한 도구 모음에서 **트리거**, **지금 트리거**를 차례로 클릭합니다.

![지금 트리거](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

1. **모니터** 탭으로 전환합니다. 이전 단계에서 수동으로 트리거하는 파이프라인이 표시됩니다. 

    ![파이프라인 실행](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 클릭합니다. 파이프라인에는 하나의 활동만 있으므로 활동 실행만 표시됩니다. 복사 작업에 대한 세부 정보를 보려면 **작업** 열에서 **세부 정보** 링크(안경 아이콘)를 클릭합니다. 위쪽의 **파이프라인**을 클릭하여 파이프라인 실행 보기로 다시 전환할 수 있습니다.

    ![작업 실행](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>출력 확인
파이프라인은 자동으로 `adftutorial` Blob 컨테이너에서 *fromonprem*이라는 출력 폴더를 만듭니다. 출력 폴더에서 *dbo.emp.txt* 파일이 표시되는지 확인합니다. 

1. Azure Portal의 **adftutorial** 컨테이너 창에서 출력 폴더를 보려면 **새로 고침**을 선택합니다.

    ![만든 출력 폴더](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. 폴더 목록에서 `fromonprem`을 선택합니다. 
3. `dbo.emp.txt`이라는 파일이 표시됨을 확인합니다.

    ![출력 파일](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob Storage의 위치 간에 데이터를 복사합니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 통합 런타임을 만듭니다.
> * SQL Server 및 Azure Storage 연결된 서비스를 만듭니다. 
> * SQL Server 및 Azure Blob 데이터 집합을 만듭니다.
> * 데이터를 이동하는 복사 작업으로 파이프라인을 만듭니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 실행을 모니터링합니다.

Data Factory에서 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.

원본에서 대상으로 데이터를 대량으로 복사하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[대량 데이터 복사](tutorial-bulk-copy-portal.md)

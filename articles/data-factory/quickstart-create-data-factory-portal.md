---
title: "Azure Data Factory UI를 사용하여 Azure 데이터 팩터리 만들기 | Microsoft Docs"
description: "이 자습서에서는 Azure Blob Storage의 한 폴더에서 다른 폴더로 데이터를 복사하는 파이프라인이 있는 데이터 팩터리를 만드는 방법을 보여 줍니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: ebce4e0d137d2e56cc914efad357593af7abb255
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-data-factory-using-the-azure-data-factory-ui"></a>Azure Data Factory UI를 사용하여 데이터 팩터리 만들기
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [버전 2 - 미리 보기](quickstart-create-data-factory-portal.md)

이 빠른 시작에서는 Azure Data Factory UI를 사용하여 데이터 팩터리를 만들고 모니터링하는 방법에 대해 설명합니다. 이 데이터 팩터리에서 만든 파이프라인은 Azure Blob Storage의 한 폴더에서 다른 폴더로 데이터를 **복사합니다**. Azure Data Factory를 사용하여 데이터를 **변환**하는 방법에 대한 자습서는 [자습서: Apache Spark를 사용하여 데이터 변환](tutorial-transform-data-spark-portal.md)을 참조하세요. 


> [!NOTE]
> Azure Data Factory를 처음 사용하는 경우 이 빠른 시작을 수행하기 전에 [Azure Data Factory 소개](data-factory-introduction.md)를 참조하세요. 
>
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)인 Data Factory 버전 1을 사용하는 경우 [Data Factory 버전 1 - 자습서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>비디오 
이 비디오를 시청하면 Data Factory UI를 이해하는 데 도움이 됩니다. 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다. 
2. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 이름 필드에 대해 다음과 같은 오류가 표시되면 데이터 팩터리의 이름을 변경합니다(예: yournameADFTutorialDataFactory). Data Factory 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
     ![사용할 수 없는 이름 - 오류](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)**를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. Data Factory에서 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 위치에 있을 수 있습니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
    ![데이터 팩터리 홈페이지](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
10. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스) 응용 프로그램을 시작합니다. 
11. 시작 페이지에서 다음 이미지와 같이 왼쪽 패널의 **편집** 탭으로 전환합니다. 

    ![시작 페이지](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기
이 단계에서는 Azure Storage 계정을 데이터 팩터리에 연결하기 위한 연결된 서비스를 만듭니다. 연결된 서비스에는 Data Factory 서비스가 런타임에 연결하는 데 사용하는 연결 정보가 있습니다.

2. **연결**을 클릭한 다음, 도구 모음에서 **새로 만들기** 단추를 클릭합니다. 

    ![새 연결](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
3. **새 연결된 서비스** 페이지에서 **Azure Blob Storage**를 선택하고 **계속**을 클릭합니다. 

    ![Azure Storage 선택](./media/quickstart-create-data-factory-portal/select-azure-storage.png)
4. **새 연결된 서비스** 페이지에서 다음 단계를 수행합니다. 

    1. **이름**에 대해 **AzureStorageLinkedService**를 입력합니다.
    2. **저장소 계정 이름**에 대해 Azure Storage 계정의 이름을 선택합니다.
    3. **연결 테스트**를 클릭하여 Data Factory 서비스에서 저장소 계정에 연결할 수 있는지 확인합니다. 
    4. **저장**을 클릭하여 연결된 서비스를 저장합니다. 

        ![Azure Storage 연결된 서비스 설정](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 
5. 연결된 서비스 목록에서 **AzureStorageLinkedService**가 표시되는지 확인합니다. 

    ![목록의 Azure Storage 연결된 서비스](./media/quickstart-create-data-factory-portal/azure-storage-linked-service-in-list.png)

## <a name="create-datasets"></a>데이터 집합 만들기
이 단계에서는 **InputDataset** 및 **OutputDataset**라는 두 개의 데이터 집합을 만듭니다. 이러한 데이터 집합은 **AzureBlob** 유형입니다. 이 데이터 집합은 이전 단계에서 만든 **Azure Storage 연결된 서비스**를 참조합니다. 

입력 데이터 집합은 입력 폴더의 원본 데이터를 나타냅니다. 입력 데이터 집합 정의에서 원본 데이터가 포함된 Blob 컨테이너(**adftutorial**), 폴더(**input**) 및 파일(**emp.txt**)을 지정합니다. 

출력 데이터 집합은 대상에 복사되는 데이터를 나타냅니다. 출력 데이터 집합 정의에서 Blob 컨테이너(**adftutorial**), 폴더(**output**) 및 데이터가 복사될 파일을 지정합니다. 파이프라인의 각 실행에는 연결된 고유 ID가 있으며, 이는 **RunId** 시스템 변수를 사용하여 액세스할 수 있습니다. 출력 파일의 이름은 파이프라인의 실행 ID에 따라 동적으로 평가됩니다.   

연결된 서비스 설정에서 원본 데이터가 포함된 Azure 저장소 계정을 지정했습니다. 원본 데이터 집합 설정에서 원본 데이터가 있는 정확한 위치(Blob 컨테이너, 폴더 및 파일)를 지정합니다. 싱크 데이터 집합 설정에서 데이터가 복사될 위치(Blob 컨테이너, 폴더 및 파일)를 지정합니다. 
 
1. **+(더하기)** 단추를 클릭하고 **데이터 집합**을 선택합니다.

    ![새 데이터 집합 메뉴](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
2. **새 데이터 집합** 페이지에서 **Azure Blob Storage**를 선택하고 **마침**을 클릭합니다. 

    ![Azure Blob Storage 선택](./media/quickstart-create-data-factory-portal/select-azure-blob-storage.png)
3. 데이터 집합의 **속성** 창에서 **이름**에 대해 **InputDataset**를 입력합니다. 

    ![데이터 집합 일반 설정](./media/quickstart-create-data-factory-portal/dataset-general-page.png)
4. **연결** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. 연결된 서비스에 대해 **AzureStorageLinkedService**를 선택합니다. 
    2. **파일 경로**에 대해 **찾아보기** 단추를 클릭합니다. 
        ![입력 파일에 대한 찾아보기](./media/quickstart-create-data-factory-portal/file-path-browse-button.png)
    3. **파일 또는 폴더 선택** 창에서 **adftutorial** 컨테이너의 **input** 폴더로 이동하여 **emp.txt** 파일을 선택하고 **마침**을 클릭합니다.

        ![입력 파일에 대한 찾아보기](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    4. (선택 사항) **데이터 미리 보기**를 클릭하여 emp.txt 파일의 데이터를 미리 봅니다.     
5. 단계를 반복하여 출력 데이터 집합을 만듭니다.  

    1. 왼쪽 창에서 **+(더하기)** 단추를 클릭하고 **데이터 집합**을 선택합니다.
    2. **새 데이터 집합** 페이지에서 **Azure Blob Storage**를 선택하고 **마침**을 클릭합니다.
    3. 이름에 대해 **OutputDataset**를 지정합니다.
    4. 폴더에 대해 **adftutorial/output**을 입력합니다. 출력 폴더가 없으면 복사 활동에서 해당 폴더를 만듭니다. 
    5. 파일 이름에 대해 `@CONCAT(pipeline().RunId, '.txt')`를 입력합니다. 파이프라인을 실행할 때마다 파이프라인 실행에는 이와 연결된 고유 ID가 있습니다. 식에서 파이프라인의 실행 ID를 **.txt**와 연결하여 출력 파일 이름을 평가합니다. 지원되는 시스템 변수 및 식에 대한 목록은 [시스템 변수](control-flow-system-variables.md) 및 [식 언어](control-flow-expression-language-functions.md)를 참조하세요.

        ![출력 데이터 집합 설정](./media/quickstart-create-data-factory-portal/output-dataset-settings.png)

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다. 
이 단계에서는 입력 및 출력 데이터 집합을 사용하는 **복사** 활동이 있는 파이프라인을 만들고 유효성을 검사합니다. 복사 활동은 입력 데이터 집합 설정에 지정된 파일의 데이터를 출력 데이터 집합 설정에 지정된 파일로 복사합니다. 입력 데이터 집합에서 폴더(파일 이름 제외)만 지정하면 복사 활동에서 원본 폴더의 모든 파일을 대상에 복사합니다. 

1. **+(더하기)** 단추를 클릭하고 **파이프라인**을 선택합니다. 

    ![새 파이프라인 메뉴](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
2. **속성** 창에서 **이름**에 대해 **CopyPipeline**을 지정합니다. 

    ![파이프라인 일반 설정](./media/quickstart-create-data-factory-portal/pipeline-general-settings.png)
3. **활동** 도구 상자에서 **데이터 흐름**을 펼치고, **복사** 활동을 **활동** 도구 상자에서 파이프라인 디자이너 화면으로 끌어서 놓습니다. 또한 **활동** 도구 상자에서 활동을 검색할 수도 있습니다. **이름**에 대해 **CopyFromBlobToBlob**을 지정합니다.

    ![복사 활동 일반 설정](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
4. 복사 활동 설정의 **원본** 탭으로 전환하고, **원본 데이터 집합**에 대해 **InputDataset**를 선택합니다.

    ![복사 활동 원본 설정](./media/quickstart-create-data-factory-portal/copy-activity-source-settings.png)    
5. 복사 활동 설정의 **싱크** 탭으로 전환하고, **싱크 데이터 집합**에 대해 **OutputDataset**를 선택합니다.

    ![복사 활동 싱크 설정](./media/quickstart-create-data-factory-portal/copy-activity-sink-settings.png)    
7. **유효성 검사**를 클릭하여 파이프라인 설정에 대한 유효성을 검사합니다. 파이프라인에 대한 유효성이 성공적으로 검사되었는지 확인합니다. 유효성 검사 출력을 닫으려면 **오른쪽 화살표**(>>) 단추를 클릭합니다. 

    ![파이프라인 유효성 검사](./media/quickstart-create-data-factory-portal/pipeline-validate-button.png)

## <a name="test-run-the-pipeline"></a>파이프라인 실행 테스트
이 단계에서는 Data Factory에 파이프라인을 배포하기 전에 이를 테스트합니다. 

1. 파이프라인에 대한 도구 모음에서 **실행 테스트**를 클릭합니다. 
    
    ![파이프라인 실행 테스트](./media/quickstart-create-data-factory-portal/pipeline-test-run.png)
2. 파이프라인 설정의 **출력** 탭에서 파이프라인 실행 상태가 표시되는지 확인합니다. 

    ![실행 테스트 출력](./media/quickstart-create-data-factory-portal/test-run-output.png)    
3. **adftutorial** 컨테이너의 **outputfolder** 폴더에 있는 출력 파일이 표시되는지 확인합니다. 출력 폴더가 없으면 Data Factory 서비스에서 자동으로 만듭니다. 
    
    ![출력 확인](./media/quickstart-create-data-factory-portal/verify-output.png)


## <a name="trigger-the-pipeline-manually"></a>수동으로 파이프라인 트리거
이 단계에서는 엔터티(연결된 서비스, 데이터 집합, 파이프라인)를 Azure Data Factory에 배포합니다. 그런 다음 파이프라인 실행을 수동으로 트리거합니다. 또한 [다른 자습서](tutorial-copy-data-portal.md?#configure-code-repository)에서 설명하는 사용자 고유의 VSTS GIT 리포지토리에 엔터티를 게시할 수도 있습니다.

1. 파이프라인을 트리거하기 전에 엔터티를 Data Factory에 게시해야 합니다. 게시하려면 왼쪽 창에서 **게시**를 클릭합니다. 

    ![게시 단추](./media/quickstart-create-data-factory-portal/publish-button.png)
2. 파이프라인을 수동으로 트리거하려면 도구 모음에서 **트리거**를 클릭하고 **지금 트리거**를 선택합니다. 
    
    ![지금 트리거](./media/quickstart-create-data-factory-portal/pipeline-trigger-now.png)

## <a name="monitor-the-pipeline"></a>파이프라인 모니터링

1. 왼쪽의 **모니터** 탭으로 전환합니다. **새로 고침** 단추를 사용하여 목록을 새로 고칩니다.

    ![파이프라인 모니터링](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
2. **작업** 아래에서 **활동 실행 보기** 링크를 클릭합니다. 이 페이지에서 복사 활동의 상태가 표시됩니다. 

    ![파이프라인 활동 실행](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
3. 복사 활동에 대한 세부 정보를 보려면 **작업** 열에서 **세부 정보**(안경 이미지) 링크를 클릭합니다. 속성에 대한 자세한 내용은 [복사 활동 개요](copy-activity-overview.md)를 참조하세요. 

    ![복사 작업 세부 정보](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
4. **output** 폴더에 있는 새 파일이 표시되는지 확인합니다. 
5. **파이프라인** 링크를 클릭하여 **활동 실행** 보기에서 **파이프라인 실행** 보기로 다시 전환할 수 있습니다. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>일정에 따라 파이프라인 트리거
이 단계는 이 자습서에서 선택 사항입니다. **스케줄러 트리거**를 만들어 파이프라인이 주기적으로(매시간, 매일 등) 실행되도록 예약할 수 있습니다. 이 단계에서는 종료 날짜로 지정한 날짜/시간까지 매분마다 실행하는 트리거를 만듭니다. 

1. **편집** 탭으로 전환합니다. 

    ![편집 탭으로 전환](./media/quickstart-create-data-factory-portal/switch-edit-tab.png)
1. 메뉴에서 **트리거**를 클릭하고 **새로 만들기/편집**을 클릭합니다. 

    ![새 트리거 메뉴](./media/quickstart-create-data-factory-portal/new-trigger-menu.png)
2. **트리거 추가** 페이지에서 **트리거 선택...**을 클릭하고 **새로 만들기**를 클릭합니다. 

    ![트리거 추가 - 새 트리거](./media/quickstart-create-data-factory-portal/add-trigger-new-button.png)
3. **새 트리거** 페이지에서 **종료** 필드에 대해 **날짜**를 선택하고, 종료 시간을 현재 시간 이후 몇 분으로 지정하고, **적용**을 클릭합니다. 각 파이프라인 실행과 관련된 비용이 있으므로 종료 시간은 시작 시간과는 별도로 분 단위로만 지정합니다. 같은 날인지 확인합니다. 그러나 게시 시간과 종료 시간 사이에 파이프라인이 실행되는 데 충분한 시간이 있는지 확인합니다. 트리거는 UI에 트리거를 저장할 때가 아니라 Data Factory에 솔루션을 게시한 후에만 적용됩니다. 

    ![트리거 설정](./media/quickstart-create-data-factory-portal/trigger-settings.png)
4. **새 트리거** 페이지에서 **활성화됨** 옵션을 확인하고 **다음**을 클릭합니다. 

    ![트리거 설정 - 다음 단추](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
5. **새 트리거** 페이지에서 경고 메시지를 검토하고 **마침**을 클릭합니다.

    ![트리거 설정 - 마침 단추](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
6. **게시**를 클릭하여 변경 내용을 Data Factory에 클릭합니다. 

    ![게시 단추](./media/quickstart-create-data-factory-portal/publish-2.png)
8. 왼쪽의 **모니터** 탭으로 전환합니다. **새로 고침**을 클릭하여 목록을 새로 고칩니다. 파이프라인이 게시 시간에서 종료 시간까지 매분 한 번씩 실행되는 것을 볼 수 있습니다. **트리거 기준** 열의 값을 확인합니다. 수동 트리거 실행은 이전 단계(**지금 트리거**)에서 수행되었습니다. 

    ![트리거된 실행 모니터링](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
9. **파이프라인 실행** 옆의 아래쪽 화살표를 클릭하여 **트리거 실행** 보기로 전환합니다. 

    ![트리거 실행 모니터링](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
10. 지정된 종료 날짜/시간까지의 모든 파이프라인 실행에 대한 **출력 파일**이 **output** 폴더에 만들어졌는지 확인합니다. 

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사합니다. [자습서](tutorial-copy-data-portal.md)를 통해 더 많은 시나리오에서의 데이터 팩터리 사용에 관해 알아보세요. 
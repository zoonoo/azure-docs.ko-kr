---
title: 시간 분할 된 파일 이름에 따라 새 파일을 점진적으로 복사
description: Azure 데이터 팩터리를 만든 다음 데이터 복사 도구를 사용하여 분할된 시간 파일 이름에 따라 새 파일을 점진적으로 로드합니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/17/2020
ms.openlocfilehash: 50e82362f39da2d3f3ecfad2de1ed07b68f8a6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501625"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>데이터 복사 도구를 사용하여 분할된 시간 파일 이름에 따라 새 파일을 점진적으로 복사합니다.

이 자습서에서는 Azure Portal을 사용하여 데이터 팩터리를 만듭니다. 그런 다음 데이터 복사 도구를 사용하여 Azure Blob 저장소에서 Azure Blob 저장소에 대한 시간 분할 된 파일 이름을 기반으로 새 파일을 점진적으로 복사하는 파이프라인을 만듭니다.

> [!NOTE]
> Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 자습서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 복사 도구를 사용하여 파이프라인 만들기
> * 파이프라인 및 작업 실행을 모니터링합니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**: Azure 구독이 없는 경우 시작하기 전에 [무료 계정을](https://azure.microsoft.com/free/) 만듭니다.
* **Azure 저장소 계정**: Blob 저장소를 _원본_ 및 _싱크_ 데이터 저장소로 사용합니다. Azure Storage 계정이 없는 경우 [스토리지 계정 만들기](../storage/common/storage-account-create.md)의 지침을 참조하세요.

### <a name="create-two-containers-in-blob-storage"></a>Blob 저장소에 컨테이너 2개 만들기

다음 단계를 수행하여 자습서에 대한 Blob 저장소를 준비합니다.

1. **소스라는**컨테이너를 만듭니다.  컨테이너에 **2020/03/17/03으로** 폴더 경로를 만듭니다. 빈 텍스트 파일을 만들고 **file1.txt로**이름을 지정합니다. 저장소 계정의 폴더 경로 **소스/2020/03/17/03에** file1.txt를 업로드합니다.  [Azure Storage Explorer](https://storageexplorer.com/)와 같은 다양한 도구를 사용하여 이러한 작업을 수행할 수 있습니다.

    ![파일 업로드](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > UTC 시간에 따라 폴더 이름을 조정하십시오.  예를 들어 현재 UTC 시간이 2020년 3월 17일 오전 3시 38분인 경우 **소스/2020/03/17/03/소스** 규칙에 따라 폴더 경로를 만들 수 **있습니다.}/{Year}/{Day}/{A}/**.

2. **대상이라는**컨테이너를 만듭니다. [Azure Storage Explorer](https://storageexplorer.com/)와 같은 다양한 도구를 사용하여 이러한 작업을 수행할 수 있습니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 왼쪽 메뉴에서 리소스 > **데이터 만들기 + 분석** > **데이터 팩터리** **만들기를**선택합니다.

   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory**를 입력합니다.

    데이터 팩터리 이름은 _전역적으로 고유_해야 합니다. 다음과 같은 오류 메시지가 표시될 수 있습니다.

   ![새 데이터 팩터리 오류 메시지](./media/doc-common-process/name-not-available-error.png)

   이름 값에 대한 오류 메시지가 표시되면 데이터 팩터리에 대한 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory**를 사용합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
3. 새 데이터 팩터리를 만들 Azure **구독**을 선택합니다.
4. **리소스 그룹**에 대해 다음 단계 중 하나를 사용합니다.

    a. **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.

    b. **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.

5. **버전** 아래에서 버전에 대해 **V2**를 선택합니다.
6. **위치**에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(예: Azure Storage, SQL Database) 및 계산(예: Azure HDInsight)은 다른 위치와 지역에 있을 수 있습니다.
7. **만들기**를 선택합니다.
8. 만들기가 완료되면 **Data Factory** 홈페이지가 표시됩니다.
9. 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스)를 시작하려면 **작성 및 모니터링** 타일을 선택합니다.

    ![데이터 팩터리 홈페이지](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>데이터 복사 도구를 사용하여 파이프라인 만들기

1. **시작하자** 페이지에서 **데이터 복사** 제목을 선택하여 데이터 복사 도구를 시작합니다.

   ![데이터 복사 도구 타일](./media/doc-common-process/get-started-page.png)

2. **속성** 페이지에서 다음 단계를 수행합니다.

    a. **작업 이름**아래에서 **델타복사프프롬블로브 파이프라인**을 입력합니다.

    b. **작업 흐름 또는 작업 일정에서**일정에 따라 정기적으로 **실행을**선택합니다.

    다. **트리거 유형에서** **텀블링 창을 선택합니다.**

    d. **되풀이**에서 1 **시간 (들)을 입력합니다.**

    e. **다음**을 선택합니다.

    Data Factory UI에서 지정한 작업 이름이 있는 파이프라인을 만듭니다.

    ![속성 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. **원본 데이터 저장소** 페이지에서 다음 단계를 완료합니다.

    a. **+ 새 연결 만들기**를 클릭하여 연결을 추가합니다.
    
    b. 갤러리에서 Azure Blob Storage를 선택한 다음, 계속을 선택합니다.
    
    다. 새 **연결된 서비스(Azure Blob 저장소)** 페이지에서 연결된 서비스의 이름을 입력합니다. Azure 구독을 선택하고 **저장소 계정 이름** 목록에서 저장소 계정을 선택합니다. 연결을 테스트한 다음 **을 선택합니다.**

    ![원본 데이터 저장소 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. **소스 데이터 저장소** 페이지에서 새로 만든 연결된 서비스를 선택한 다음 다음 을 **클릭합니다.**

4. **입력 파일 또는 폴더 선택** 페이지에서 다음 단계를 수행합니다.

    a. **소스** 컨테이너를 찾아서 선택한 다음 **을 선택합니다.**

    ![입력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. **파일 로드 동작에서** **증분 로드: 시간 분할된 폴더/파일 이름을**선택합니다.

    다. 동적 폴더 경로를 **소스/{연도}/{월}/{day}/{시간}/로**작성하고 다음 스크린샷과 같이 형식을 변경합니다. **이진 복사를** 확인하고 **다음을**클릭합니다.

    ![입력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. 대상 **데이터 저장소** 페이지에서 데이터 원본 저장소저장소 계정인 **AzureBlobStorage를**선택한 다음 다음 을 **클릭합니다.**

    ![대상 데이터 저장소 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. 출력 **파일 또는 폴더 선택** 페이지에서 다음 단계를 수행합니다.

    a. **대상** 폴더를 찾아보고 선택한 다음 **선택을**클릭합니다.

    b. 동적 폴더 경로를 **대상/{연도}/{월}/{일}/{시간}/로**작성하고 다음과 같이 형식을 변경합니다.

    ![출력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    다. **다음**을 클릭합니다.

    ![출력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. **설정** 페이지에서 **다음**을 선택합니다.

8. **요약** 페이지에서 설정을 검토하고 **다음**을 선택합니다.

    ![요약 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. **배포 페이지**에서 **모니터**를 선택하여 파이프라인(작업)을 모니터링합니다.
    ![배포 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다.  파이프라인이 자동으로 트리거될 때(약 1시간 후) 파이프라인 실행을 기다려야 합니다. 실행 될 때 클릭 파이프라인 이름 링크 **DeltaCopyFromBlob파이프라인** 활동 실행 세부 정보를 보거나 파이프라인을 다시 실행 합니다. **새로 고침**을 선택하여 목록을 새로 고칩니다.

    ![파이프라인 실행 모니터링](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 소스 **및** **대상** 열의 열 너비를 조정 (필요한 경우) 자세한 내용을 표시 하려면, 소스 파일 (file1.txt)에서 복사 된 소스 파일을 볼 수 있습니다 *2020/03/17/03/* *대상/2020/03/17/03/* 동일한 파일 이름으로. 

    ![파이프라인 실행 모니터링](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Azure 저장소 탐색기(파일을https://storageexplorer.com/) 검색하는)를 사용하여 동일한 것을 확인할 수도 있습니다.

    ![파이프라인 실행 모니터링](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. **file2.txt로**새 이름으로 다른 빈 텍스트 파일을 만듭니다. storage 계정의 폴더 경로 **소스/2020/03/17/04에** file2.txt 파일을 업로드합니다. [Azure Storage Explorer](https://storageexplorer.com/)와 같은 다양한 도구를 사용하여 이러한 작업을 수행할 수 있습니다.

    > [!NOTE]
    > 새 폴더 경로를 만들어야 한다는 것을 알고 있을 수 있습니다. UTC 시간에 따라 폴더 이름을 조정하십시오.  예를 들어 현재 UTC 시간이 2020년 3월 17일 오전 4:20인 **경우{Year}/{Month}/{Day}/{A}/{시간}/의**규칙에 따라 폴더 경로를 **소스/2020/03/17/04/로** 만들 수 있습니다.

13. **파이프라인 실행** 보기로 돌아가려면 **모든 파이프라인 실행을**선택하고 동일한 파이프라인이 한 시간 후에 자동으로 다시 트리거될 때까지 기다립니다.  

    ![파이프라인 실행 모니터링](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. 두 번째 파이프라인 실행시 새 **DeltaCopyFromBlob파이프라인** 링크를 선택하고 세부 사항을 검토하기 위해 동일한 작업을 수행합니다. 소스 파일 (file2.txt)이 **소스 / 2020 / 03 / 17 / 04 /** 대상 / **2020 / 03 / 17 / 04 /** 동일한 파일 이름으로 복사 된 것을 볼 수 있습니다. Azure Storagehttps://storageexplorer.com/) Explorer(대상 컨테이너에서 파일을 검색)를 사용하여 **destination** 동일한 것을 확인할 수도 있습니다.


## <a name="next-steps"></a>다음 단계
Azure에서 Spark 클러스터를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[클라우드에서 Spark 클러스터를 사용하여 데이터 변환](tutorial-transform-data-spark-portal.md)

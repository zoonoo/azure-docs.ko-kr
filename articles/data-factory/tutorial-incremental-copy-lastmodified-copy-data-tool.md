---
title: 새 파일과 업데이트된 파일을 증분 방식으로 복사하는 데이터 도구
description: Azure 데이터 팩터리를 만든 다음 Data 복사 도구를 사용하여 LastModifiedDate를 기반으로 새 파일을 점진적으로 로드합니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/18/2020
ms.openlocfilehash: 3098ca0d3d5e41c298d3058ffa84fcf129648281
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399477"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>데이터 복사 도구를 사용하여 LastModifiedDate를 기반으로 새 파일과 변경된 파일을 점진적으로 복사합니다.

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 Azure 포털을 사용하여 데이터 팩터리를 만듭니다. 그런 다음 데이터 복사 도구를 사용하여 Azure Blob 저장소에서 Azure Blob 저장소에 새 파일과 변경된 파일만 점진적으로 복사하는 파이프라인을 만듭니다. 복사할 `LastModifiedDate` 파일을 결정하는 데 사용합니다.

여기서 단계를 완료하면 Azure Data Factory는 원본 저장소의 모든 파일을 검색하고, `LastModifiedDate`에 의해 파일 필터를 적용하고, 마지막으로 업데이트되었거나 업데이트된 파일만 대상 저장소에 복사합니다. Data Factory에서 많은 수의 파일을 검사하는 경우에도 긴 기간을 예상해야 합니다. 파일 검색은 복사된 데이터의 양이 줄어드는 경우에도 시간이 많이 걸립니다.

> [!NOTE]
> Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 복사 도구를 사용하여 파이프라인 만들기
> * 파이프라인 및 작업 실행을 모니터링합니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure 저장소 계정**: 원본 및 싱크 데이터 저장소에 Blob 저장소를 사용합니다. Azure Storage 계정이 없는 경우 [저장소 계정 만들기의](../storage/common/storage-account-create.md)지침을 따릅니다.

## <a name="create-two-containers-in-blob-storage"></a>Blob 저장소에 컨테이너 2개 만들기

다음 단계를 완료하여 자습서에 대한 Blob 저장소를 준비합니다.

1. **소스라는**컨테이너를 만듭니다. 다양한 도구를 사용하여 [Azure 저장소 탐색기](https://storageexplorer.com/)와 같은 이 작업을 수행할 수 있습니다.

2. **대상이라는**컨테이너를 만듭니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 왼쪽 창에서 **리소스 만들기**를 선택합니다. **분석** > **데이터 팩토리**선택 :

   ![데이터 팩터리 선택](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory**를 입력합니다.

   데이터 팩터리 이름은 전역적으로 고유해야 합니다. 이 오류 메시지가 발생할 수 있습니다.

   ![이름을 사용할 수 없는 오류 메시지](./media/doc-common-process/name-not-available-error.png)

   이름 값에 대한 오류 메시지가 표시되면 데이터 팩터리에 대한 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory**를 사용합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
3. **구독에서**새 데이터 팩터리를 만들 Azure 구독을 선택합니다.
4. **리소스 그룹에서**다음 단계 중 하나를 수행합니다.

    * **기존 사용을** 선택한 다음 목록에서 기존 리소스 그룹을 선택합니다.

    * **새 만들기를** 선택한 다음 리소스 그룹에 대한 이름을 입력합니다.
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.

5. **버전**에서 **V2**를 선택합니다.
6. **위치** 아래에서 데이터 팩터리에 대한 위치를 선택합니다. 지원되는 위치만 목록에 표시됩니다. 데이터 저장소(예: Azure 저장소 및 Azure SQL Database)와 데이터 팩터리에서 사용하는 계산(예: Azure HDInsight)은 다른 위치 및 지역에 있을 수 있습니다.
8. **만들기**를 선택합니다.
9. 데이터 팩터리를 만든 후 데이터 팩터리 홈 페이지가 나타납니다.
10. 별도의 탭에서 Azure 데이터 팩터리 사용자 인터페이스(UI)를 열려면 **모니터 작성** & 선택합니다.

    ![데이터 팩터리 홈페이지](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>데이터 복사 도구를 사용하여 파이프라인 만들기

1. **시작하자** 페이지에서 **데이터 복사** 타일을 선택하여 데이터 복사 도구를 엽니다.

   ![데이터 타일 복사](./media/doc-common-process/get-started-page.png)

2. **속성** 페이지에서 다음 단계를 수행합니다.

    a. **작업 이름**아래에서 **델타복사프프롬블로브 파이프라인**을 입력합니다.

    b. **작업 흐름 또는 작업 일정에서**일정에 따라 정기적으로 **실행을**선택합니다.

    다. **트리거 유형에서** **텀블링 창을 선택합니다.**

    d. **되풀이**에서 **15 분 (들)을 입력합니다.**

    e. **다음**을 선택합니다.

    데이터 팩터리는 지정된 작업 이름으로 파이프라인을 만듭니다.

    ![데이터 속성 페이지 복사](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. 원본 **데이터 저장소** 페이지에서 다음 단계를 완료합니다.

    a. 연결을 추가하려면 **새 연결 만들기를** 선택합니다.

    b. 갤러리에서 **Azure Blob 저장소를** 선택한 다음 **계속을**선택합니다.

    ![Azure 블로그 저장소 선택](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    다. 새 **연결된 서비스(Azure Blob Storage)** 페이지에서 **저장소 계정 이름** 목록에서 저장소 계정을 선택합니다. 연결을 테스트한 다음 **을 선택합니다.**

    d. 새 연결된 서비스를 선택한 다음 **다음을 선택합니다.**

   ![새 연결된 서비스 선택](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. **입력 파일 또는 폴더 선택** 페이지에서 다음 단계를 완료합니다.

    a. **소스** 폴더를 찾아보고 선택한 다음 **을 선택합니다.**

    ![입력 파일 또는 폴더 선택](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. **파일 로드 동작에서** **증분 로드: LastModifiedDate**를 선택합니다.

    다. **이진 복사를** 선택한 다음 **다음을**선택합니다.

     ![입력 파일 또는 폴더 페이지 선택](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. 대상 **데이터 저장소** 페이지에서 만든 **AzureBlobStorage** 서비스를 선택합니다. 원본 데이터 저장소와 동일한 저장소 계정입니다. 그런 후 **다음**을 선택합니다.

6. **출력 파일 또는 폴더 선택** 페이지에서 다음 단계를 완료합니다.

    a. **대상** 폴더를 찾아보고 선택한 다음 **다음 을**선택합니다.

    ![출력 파일 또는 폴더 페이지 선택](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. **다음**을 선택합니다.

7. **설정** 페이지에서 **다음**을 선택합니다.

8. **요약** 페이지에서 설정을 검토한 다음 **다음을**선택합니다.

    ![요약 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. **배포 페이지**에서 **모니터**를 선택하여 파이프라인(작업)을 모니터링합니다.

    ![배포 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. 응용 프로그램이 **모니터** 탭으로 전환됩니다. 파이프라인의 상태가 표시됩니다. **새로 고침**을 선택하여 목록을 새로 고칩니다. **파이프라인 이름** 아래의 링크를 선택하여 활동 실행 세부 정보를 보거나 파이프라인을 다시 실행합니다.

    ![목록 새로 고침 및 활동 실행 세부 정보 보기](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. 파이프라인에는 하나의 활동(복사 활동)만 있으므로 항목은 하나만 표시됩니다. 복사 작업에 대한 자세한 내용은 **활동 이름** 열에서 **세부 정보** 링크(안경 아이콘)를 선택합니다. 속성에 대한 자세한 내용은 [활동 복사 개요를](copy-activity-overview.md)참조하십시오.

    ![파이프라인의 활동 복사](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Blob 저장소 계정에 원본 컨테이너에 파일이 없기 때문에 계정의 대상 컨테이너에 복사된 파일이 표시되지 않습니다.

    ![원본 컨테이너 또는 대상 컨테이너에 파일이 없습니다.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. 빈 텍스트 파일을 만들고 **file1.txt**의 이름을 지정합니다. 저장소 계정의 원본 컨테이너에 이 텍스트 파일을 업로드합니다. 다양한 도구를 사용하여 [Azure 저장소 탐색기](https://storageexplorer.com/)와 같은 이러한 작업을 수행할 수 있습니다.

    ![file1.txt를 만들고 소스 컨테이너에 업로드합니다.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. **파이프라인 실행** 보기로 돌아가려면 **모든 파이프라인 실행을**선택하고 동일한 파이프라인이 자동으로 다시 트리거될 때까지 기다립니다.  

    ![모든 파이프라인 실행 선택](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. 두 번째 파이프라인 실행이 완료되면 앞에서 설명한 것과 동일한 단계를 수행하여 활동 실행 세부 정보를 검토합니다.  

    한 파일(file1.txt)이 원본 컨테이너에서 Blob 저장소 계정의 대상 컨테이너로 복사되었음을 알 수 있습니다.

    ![file1.txt가 소스 컨테이너에서 대상 컨테이너로 복사되었습니다.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. 다른 빈 텍스트 파일을 만들고 **file2.txt**의 이름을 지정합니다. Blob 저장소 계정의 원본 컨테이너에 이 텍스트 파일을 업로드합니다.

16. 두 번째 텍스트 파일에 대해 13단계와 14단계를 반복합니다. 이 파이프라인을 실행하는 동안 원본 컨테이너에서 저장소 계정의 대상 컨테이너로 새 파일(file2.txt)만 복사된 것을 볼 수 있습니다.  

    [Azure Storage 탐색기를](https://storageexplorer.com/) 사용하여 파일을 검색하여 하나의 파일만 복사되었는지 확인할 수도 있습니다.

    ![Azure 저장소 탐색기를 사용하여 파일 검색](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>다음 단계
Azure에서 아파치 스파크 클러스터를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 이동하십시오.

> [!div class="nextstepaction"]
>[아파치 스파크 클러스터를 사용하여 클라우드의 데이터 변환](tutorial-transform-data-spark-portal.md)

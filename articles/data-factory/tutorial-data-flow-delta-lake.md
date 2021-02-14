---
title: 데이터 흐름이 포함 된 델타 lake ETL
description: 이 자습서에서는 데이터 흐름을 사용 하 여 델타 lake에서 데이터를 변환 및 분석 하는 방법에 대 한 단계별 지침을 제공 합니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 02/09/2021
ms.openlocfilehash: cb8d44353e826df14ed3baab2c4ca66ffed4a569
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417663"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>매핑 데이터 흐름을 사용 하 여 델타 lake의 데이터 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 자습서에서는 데이터 흐름 캔버스를 사용 하 여 Azure Data Lake Storage (ADLS) Gen2에서 데이터를 분석 및 변환 하 고 델타 Lake에 저장할 수 있는 데이터 흐름을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**. ADLS 저장소를 *원본* 및 *싱크* 데이터 저장소로 사용 합니다. 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.

이 자습서에서 변형 하는 파일은 [여기](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv)에서 찾을 수 있는 MoviesDB.csv 되었습니다. GitHub에서 파일을 검색 하려면 해당 내용을 원하는 텍스트 편집기에 복사 하 여 로컬에 .csv 파일로 저장 합니다. 저장소 계정에 파일을 업로드 하려면 [Azure Portal을 사용 하 여 Blob 업로드](../storage/blobs/storage-quickstart-blobs-portal.md)를 참조 하세요. 예제는 ' sample-s t r i n s ' 라는 컨테이너를 참조 합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

이 단계에서는 데이터 팩터리를 만들고 Data Factory UX를 열어 데이터 팩터리에 파이프라인을 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 을 엽니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저 에서만 지원 됩니다.
1. 왼쪽 메뉴에서 **리소스 통합 만들기** 를 선택  >    >  **Data Factory**
1. **새 데이터 팩터리** 페이지의 **이름** 아래에 **ADFTutorialDataFactory** 을 입력 합니다.
1. 데이터 팩터리를 만들 Azure **구독** 을 선택합니다.
1. **리소스 그룹** 에 대해 다음 단계 중 하나를 사용합니다.

    a. **기존 항목 사용** 을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.

    b. **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요. 
1. **버전** 에서 **V2** 를 선택합니다.
1. **위치** 에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용 하는 데이터 저장소 (예: Azure Storage 및 SQL Database) 및 계산 (예: Azure HDInsight)은 다른 지역에 있을 수 있습니다.
1. **만들기** 를 선택합니다.
1. 만들기가 완료되면 알림 센터에 알림이 표시됩니다. **리소스로 이동** 을 선택하여 Data Factory 페이지로 이동합니다.
1. **작성 및 모니터링** 을 선택하여 별도의 탭에서 Data Factory UI를 선택합니다.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>데이터 흐름 작업을 사용 하 여 파이프라인 만들기

이 단계에서는 데이터 흐름 작업을 포함 하는 파이프라인을 만듭니다.

1. **시작** 페이지에서 **파이프라인 만들기** 를 선택합니다.

   ![파이프라인 만들기](./media/doc-common-process/get-started-page.png)

1. 파이프라인의 **일반** 탭에서 파이프라인 **이름** 에 **DeltaLake** 을 입력 합니다.
1. 팩터리 위쪽 막대에서의 **데이터 흐름 디버그** 슬라이더를 밉니다. 디버그 모드에서는 라이브 Spark 클러스터에 대 한 변환 논리의 대화형 테스트를 수행할 수 있습니다. 데이터 흐름 클러스터는 준비 하는 데 5-7 분 정도 걸리며, 사용자가 데이터 흐름 개발을 수행할 계획인 경우 먼저 디버그를 설정 하는 것이 좋습니다. 자세한 내용은 [디버그 모드](concepts-data-flow-debug-mode.md)를 참조하세요.

    ![데이터 흐름 작업](media/tutorial-data-flow/dataflow1.png)
1. **작업** 창에서 **이동 및 변환** 아코디언을 확장 합니다. **데이터 흐름** 활동을 창에서 파이프라인 캔버스로 끌어다 놓습니다.

    ![데이터 흐름 작업을 삭제할 수 있는 파이프라인 캔버스를 보여 주는 스크린샷](media/tutorial-data-flow/activity1.png)
1. **데이터 흐름 추가** 팝업에서 **새 데이터 흐름 만들기** 를 선택 하 고 데이터 흐름의 이름을 **DeltaLake** 로 설정 합니다. 완료되었으면 마침을 클릭합니다.

    ![새 데이터 흐름을 만들 때 데이터 흐름의 이름을 나타내는 위치를 보여 주는 스크린샷](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>데이터 흐름 캔버스의 빌드 변환 논리

이 자습서에서는 두 개의 데이터 흐름을 생성 합니다. 펀치 데이터 흐름은 위의 영화 CSV 파일에서 새 델타 Lake를 생성 하는 간단한 원본 싱크입니다. 마지막으로, 아래에서이 흐름 디자인을 만들어 델타 Lake의 데이터를 업데이트 합니다.

![최종 흐름](media/data-flow/data-flow-tutorial-6.png "최종 흐름")

### <a name="tutorial-objectives"></a>자습서 목표

1. 위의 MoviesCSV 데이터 집합 원본을 가져와 새 델타 Lake를 형성 합니다.
1. 1988 영화에 대 한 등급을 업데이트 하는 논리를 ' 1 '로 빌드
1. 1950에서 모든 영화 삭제
1. 1960에서 영화를 복제 하 여 2021에 대 한 새 영화를 삽입 합니다.

### <a name="start-from-a-blank-data-flow-canvas"></a>빈 데이터 흐름 캔버스에서 시작

1. 원본 변환을 클릭 합니다.
1. 아래쪽 패널 1의 데이터 집합 옆에 있는 새로 만들기를 클릭 하 ADLS Gen2에 대 한 새 연결 된 서비스 만들기를 클릭 합니다.
1. 데이터 집합 형식에 대해 구분 된 텍스트 선택
1. 데이터 집합 이름을 "MoviesCSV"로 
1. 위의 저장소에 업로드 한 MoviesCSV 파일을 가리킵니다.
1. 쉼표로 구분 하 여 첫 번째 행에 머리글을 포함 하도록 설정 합니다. 
1. 원본 프로젝션 탭으로 이동 하 여 "데이터 형식 검색"을 클릭 합니다.
1. 투영을 설정한 후에는 계속할 수 있습니다. 
1. 싱크 변환 추가
1. 델타는 인라인 데이터 집합 형식입니다. ADLS Gen2 storage 계정을 가리켜야 합니다.
   
   ![인라인 데이터 집합](media/data-flow/data-flow-tutorial-5.png "인라인 데이터 집합")

1. ADF에서 델타 호수를 만들 저장소 컨테이너의 폴더 이름을 선택 합니다.
1. 파이프라인 디자이너로 돌아가서 디버그를 클릭 하 여 캔버스의이 데이터 흐름 작업 으로만 파이프라인을 디버그 모드로 실행 합니다. 그러면 새 델타 Lake가 ADLS Gen2에 생성 됩니다.
1. 팩터리 리소스에서 새로 만들기 > 데이터 흐름을 클릭 합니다. 
1. MoviesCSV를 다시 원본으로 사용 하 고 "데이터 형식 검색"을 다시 클릭 합니다.
1. 그래프에서 원본 변환에 필터 변환 추가
1. 사용 하려는 3 년과 일치 하는 영화 행만 1950, 1988 및 1960이 될 수 있도록 허용 합니다.
1. 이제 필터 변환에 파생 열 변환을 추가 하 여 각 1988 영화에 대 한 등급을 ' 1 '로 업데이트 합니다.
1. 동일한 파생 열에서 기존 연도를 가져와 연도를 2021로 변경 하 여 2021에 대 한 영화를 만듭니다. 1960을 선택 하겠습니다.
1. 이는 세 개의 파생 열이 표시 되는 모양입니다.

   ![파생 열](media/data-flow/data-flow-tutorial-2.png "파생 열")
   
1. ```Update, insert, delete, and upsert``` alter Row transform에서 정책이 만들어집니다. 파생 열 뒤에 alter row 변환을 추가 합니다.
1. Alter row 정책은 다음과 같이 표시 됩니다.

   ![행 변경](media/data-flow/data-flow-tutorial-3.png "행 변경")
   
1. 각 alter row 형식에 대 한 적절 한 정책을 설정 했으므로 싱크 변환에 적절 한 업데이트 규칙이 설정 되었는지 확인 합니다.

   ![싱크](media/data-flow/data-flow-tutorial-4.png "sink")
   
1. 여기서는 ADLS Gen2 data lake에 대 한 델타 Lake sink를 사용 하 고 삽입, 업데이트 또는 삭제를 허용 합니다. 
1. 키 열은 Movie primary key 열 및 year 열로 구성 된 복합 키입니다. 이는 1960 행을 복제 하 여 가짜 2021 영화를 만들기 때문입니다. 이렇게 하면 고유성을 제공 하 여 기존 행을 조회할 때 충돌을 방지 합니다.

### <a name="download-completed-sample"></a>다운로드 완료 샘플
[다음은 lake에서 행을 업데이트/삭제 하는 데이터 흐름이 포함 된 델타 파이프라인에 대 한 샘플 솔루션입니다.](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>다음 단계

[데이터 흐름 식 언어](data-flow-expression-functions.md)에 대해 자세히 알아보세요.

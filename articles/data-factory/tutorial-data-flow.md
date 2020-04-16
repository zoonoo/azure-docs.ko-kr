---
title: 매핑 데이터 흐름을 사용하여 데이터 변환
description: 이 자습서에서는 Azure Data Factory를 사용하여 매핑 데이터 흐름을 사용하여 데이터를 변환하는 단계별 지침을 제공합니다.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: 917a8d6edf04d8a160c3a6a5ac59949623dfee5c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418680"
---
# <a name="transform-data-using-mapping-data-flows"></a>매핑 데이터 흐름을 사용하여 데이터 변환

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 자습서에서는 AZURE Data Factory 사용자 인터페이스(UX)를 사용하여 매핑 데이터 흐름을 사용하여 Azure Data Lake Storage(ADLS) Gen2 원본에서 ADLS Gen2 원본으로 데이터를 복사하고 변환하는 파이프라인을 만듭니다. 이 자습서의 구성 패턴을 매핑 데이터 흐름을 사용 하 여 데이터를 변환할 때 확장할 수 있습니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 흐름 활동이 있는 파이프라인을 만듭니다.
> * 네 가지 변환으로 매핑 데이터 흐름을 빌드합니다.
> * 파이프라인 실행 테스트
> * 데이터 흐름 활동 모니터링

## <a name="prerequisites"></a>사전 요구 사항
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure 저장소 계정**. ADLS 저장소를 *원본으로* 사용하고 데이터 저장소를 *싱크합니다.* 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.

이 자습서에서 변환하는 파일은 [여기에서](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)찾을 수 있는 MoviesDB.csv입니다. GitHub에서 파일을 검색하려면 내용을 텍스트 편집기로 복사하여 .csv 파일로 로컬로 저장합니다. 저장소 계정에 파일을 업로드하려면 [Azure Portal을 사용하여 Blob 업로드를](../storage/blobs/storage-quickstart-blobs-portal.md)참조하세요. 예제에서는 '샘플 데이터'라는 컨테이너를 참조합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

이 단계에서는 데이터 팩터리를 만들고 데이터 팩터리 UX를 열어 데이터 팩터리에서 파이프라인을 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome**을 엽니다. 현재 데이터 팩토리 UI는 마이크로소프트 에지와 구글 크롬 웹 브라우저에서만 지원됩니다.
2. 왼쪽 메뉴에서 > 리소스**분석** > **데이터 팩터리** **만들기를**선택합니다.

   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/doc-common-process/new-azure-data-factory-menu.png)

3. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory**를 입력합니다.

   Azure 데이터 팩터의 이름은 *전역적으로 고유해야*합니다. 이름 값에 대한 오류 메시지가 표시되면 데이터 팩터리에 대한 다른 이름을 입력합니다. (예: yournameADFTutorialDataFactory). Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory 명명 규칙](naming-rules.md)을 참조하세요.

     ![새 data factory](./media/doc-common-process/name-not-available-error.png)
4. 데이터 팩터리를 만들 Azure **구독**을 선택합니다.
5. **리소스 그룹**에 대해 다음 단계 중 하나를 사용합니다.

    a. **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.

    b. **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요. 
6. **버전**에서 **V2**를 선택합니다.
7. **위치**에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 저장소(예: Azure 저장소 및 SQL 데이터베이스)와 데이터 팩터리에서 사용하는 계산(예: Azure HDInsight)은 다른 지역에 있을 수 있습니다.
8. **만들기**를 선택합니다.
9. 만들기가 완료되면 알림 센터에 알림이 표시됩니다. **리소스로 이동**을 선택하여 Data Factory 페이지로 이동합니다.
10. **작성 및 모니터링**을 선택하여 별도의 탭에서 Data Factory UI를 선택합니다.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>데이터 흐름 활동이 있는 파이프라인 만들기

이 단계에서는 데이터 흐름 활동이 포함된 파이프라인을 만듭니다.

1. **시작** 페이지에서 **파이프라인 만들기**를 선택합니다.

   ![파이프라인 만들기](./media/doc-common-process/get-started-page.png)

1. 파이프라인의 **일반** 탭에서 파이프라인 **이름에** 대한 **TransformMovies를** 입력합니다.
1. 팩터리 상단 막대에서 **데이터 흐름 디버그** 슬라이더를 밉합니다. 디버그 모드를 사용하면 라이브 스파크 클러스터에 대해 변환 논리를 대화형으로 테스트할 수 있습니다. 데이터 흐름 클러스터를 워밍업하는 데 5~7분이 걸리며 데이터 흐름 개발을 수행하려는 경우 먼저 디버그를 설정하는 것이 좋습니다. 자세한 내용은 [디버그 모드를](concepts-data-flow-debug-mode.md)참조하십시오.

    ![데이터 흐름 활동](media/tutorial-data-flow/dataflow1.png)
1. **활동** 창에서 **이동 및 변환** 아코디언을 확장합니다. 창에서 파이프라인 캔버스로 **데이터 흐름** 활동을 끌어 놓습니다.

    ![데이터 흐름 활동](media/tutorial-data-flow/activity1.png)
1. 데이터 **흐름 추가** 팝업에서 **새 데이터 흐름 만들기를** 선택한 다음 데이터 흐름의 이름을 **TransformMovies**로 지정합니다. 완료되었으면 마침을 클릭합니다.

    ![데이터 흐름 활동](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>데이터 흐름 캔버스에서 변환 논리 빌드

데이터 흐름을 만들면 데이터 흐름 캔버스로 자동으로 전송됩니다. 이 단계에서는 ADLS 저장소에서 moviesDB.csv를 가져와 1910년부터 2000년까지의 평균 코미디 등급을 집계하는 데이터 흐름을 빌드합니다. 그런 다음 이 파일을 ADLS 저장소에 다시 씁니다.

1. 데이터 흐름 캔버스에서 소스 추가 상자를 클릭하여 **원본을 추가합니다.**

    ![데이터 흐름 캔버스](media/tutorial-data-flow/dataflow2.png)
1. 소스 **영화DB의**이름을 지정합니다. 새 원본 데이터 집합을 만들려면 **새로 만들기를** 클릭합니다.

    ![데이터 흐름 캔버스](media/tutorial-data-flow/dataflow3.png)
1. **Azure 데이터 레이크 저장소 Gen2를**선택합니다. 계속을 클릭합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset1.png)
1. **디미지텍스트**. 계속을 클릭합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset2.png)
1. 데이터 집합 의 이름을 **MoviesDB.** 연결된 서비스 드롭다운에서 **새**을 선택합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset3.png)
1. 연결된 서비스 생성 화면에서 ADLS gen2 연결 서비스 **ADLSGen2의** 이름을 지정하고 인증 방법을 지정합니다. 그런 다음 연결 자격 증명을 입력합니다. 이 자습서에서는 계정 키를 사용하여 저장소 계정에 연결합니다. **테스트 연결을** 클릭하여 자격 증명이 올바르게 입력되었는지 확인할 수 있습니다. 작업을 마쳤으면 만들기를 클릭합니다.

    ![연결된 서비스](media/tutorial-data-flow/ls1.png)
1. 데이터 집합 생성 화면으로 돌아가면 파일 **경로** 필드 아래에 파일이 있는 위치를 입력합니다. 이 자습서에서는 파일 moviesDB.csv 컨테이너 샘플 데이터에 있습니다. 파일에 헤더가 있기 때문에 **첫 번째 행을 헤더로**선택합니다. 저장소의 파일에서 직접 헤더 스키마를 가져오려면 **연결/저장소에서** 선택합니다. 완료되면 확인을 클릭합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset4.png)
1. 디버그 클러스터가 시작된 경우 원본 변환의 **데이터 미리 보기** 탭으로 이동하여 새로 **고침을** 클릭하여 데이터의 스냅숏을 가져옵니다. 데이터 미리 보기를 사용하여 변환이 올바르게 구성되었는지 확인할 수 있습니다.

    ![데이터 흐름 캔버스](media/tutorial-data-flow/dataflow4.png)
1. 데이터 흐름 캔버스의 소스 노드 옆에서 더하기 아이콘을 클릭하여 새 변환을 추가합니다. 추가하는 첫 번째 변환은 **필터**입니다.

    ![데이터 흐름 캔버스](media/tutorial-data-flow/dataflow5.png)
1. 필터 변환 **필터연도의**이름을 지정합니다. **[식** 작성기]를 열려면 Filter on 옆에 있는 식 상자를 클릭합니다. 여기에서 필터링 조건을 지정합니다.

    ![Assert](media/tutorial-data-flow/filter1.png)
1. 데이터 흐름 표현식 빌더를 사용하면 다양한 변환에 사용할 식을 대화식으로 빌드할 수 있습니다. 식에는 기본 제공 함수, 입력 스키마의 열 및 사용자 정의 매개 변수가 포함될 수 있습니다. 식을 작성하는 방법에 대한 자세한 내용은 [데이터 흐름 식 작성기](concepts-data-flow-expression-builder.md)를 참조하십시오.

    이 자습서에서는 1910년과 2000년 사이에 나온 장르 코미디 영화를 필터링하려고 합니다. 연도는 현재 문자열이기 때문에 ```toInteger()``` 함수를 사용하여 정수로 변환해야 합니다. (>=)보다 크거나 같고 (<=) 연산자와 같거나 같아서 리터럴 연도 값 1910 및 200-과 비교합니다. 이러한 식을 && 연산자와 함께 결합합니다. 식은 다음과 같이 나옵니다.

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    어떤 영화가 코미디인지 찾으려면 이 ```rlike()``` 기능을 사용하여 열 장르에서 패턴 '코미디'를 찾을 수 있습니다. 연도 비교와 같은 표현을 결합하여 얻을 수 있습니다.

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    디버그 클러스터가 활성화된 경우 **새로 고침을** 클릭하여 논리를 확인하여 사용된 입력과 비교하여 식 출력을 볼 수 있습니다. 데이터 흐름 표현 언어를 사용하여 이 논리를 수행하는 방법에 대한 정답이 두 개 이상 있습니다.

    ![Assert](media/tutorial-data-flow/filter2.png)

    표현식이 완료되면 **저장 및 완료를** 클릭합니다.

1. 데이터 **미리 보기를** 가져 와서 필터가 제대로 작동하는지 확인합니다.

    ![Assert](media/tutorial-data-flow/filter3.png)
1. 다음 변환은 **스키마 수정자**아래의 **집계** 변환입니다.

    ![집계](media/tutorial-data-flow/agg1.png)
1. 집계 변환 **의**이름을 집계 코미디 등급 . **그룹별** 탭에서 드롭다운에서 **연도를** 선택하여 동영상이 나온 연도별로 집계를 그룹화합니다.

    ![집계](media/tutorial-data-flow/agg2.png)
1. 집계 탭으로 **이동합니다.** 왼쪽 텍스트 상자에서 집계 열의 이름을 **평균코미디 등급**. 오른쪽 표현식 상자를 클릭하여 식 작성기를 통해 집계 식을 입력합니다.

    ![집계](media/tutorial-data-flow/agg3.png)
1. 열 **등급의**평균을 얻으려면 ```avg()``` 집계 함수를 사용합니다. **등급은** 문자열이며 ```avg()``` 숫자 입력을 사용하므로 ```toInteger()``` 함수를 통해 값을 숫자로 변환해야 합니다. 이 표현식은 다음과 같습니다.

    ```avg(toInteger(Rating))```

    저장 **및 완료를** 클릭합니다.

    ![집계](media/tutorial-data-flow/agg4.png)
1. 변환 출력을 보려면 **데이터 미리 보기** 탭으로 이동합니다. 두 개의 열, **연도** 및 **평균 코미디 등급이**있습니다.

    ![집계](media/tutorial-data-flow/agg3.png)
1. 그런 다음 **대상**에서 **싱크** 변환을 추가 하려고 합니다.

    ![sink](media/tutorial-data-flow/sink1.png)
1. 싱크대 이름을 **지정합니다.** **새** 를 클릭하여 싱크 데이터 집합을 만듭니다.

    ![sink](media/tutorial-data-flow/sink2.png)
1. **Azure 데이터 레이크 저장소 Gen2를**선택합니다. 계속을 클릭합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset1.png)
1. **디미지텍스트**. 계속을 클릭합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset2.png)
1. 싱크 데이터 집합 의 이름을 **지정합니다.** 연결된 서비스의 경우 6단계에서 만든 ADLS gen2 연결 서비스를 선택합니다. 출력 폴더를 입력하여 데이터를 작성합니다. 이 자습서에서는 컨테이너 '샘플 데이터'의 폴더 '출력'에 쓰고 있습니다. 폴더는 미리 존재할 필요가 없으며 동적으로 만들 수 있습니다. **첫 번째 행을 헤더로 true로** 설정하고 **가져오기 스키마에**대해 **없음을** 선택합니다. 마침을 클릭합니다.

    ![sink](media/tutorial-data-flow/sink3.png)

이제 데이터 흐름 빌드를 완료했습니다. 파이프라인에서 실행할 준비가 되었습니다.

## <a name="running-and-monitoring-the-data-flow"></a>데이터 흐름 실행 및 모니터링

파이프라인을 게시하기 전에 파이프라인을 디버깅할 수 있습니다. 이 단계에서는 데이터 흐름 파이프라인의 디버그 실행을 트리거합니다. 데이터 미리 보기는 데이터를 쓰지 않지만 디버그 실행은 싱크 대상에 데이터를 씁니다.

1. 파이프라인 캔버스로 이동합니다. **디버그** 실행을 트리거하려면 디버그를 클릭합니다.

    ![파이프라인](media/tutorial-data-flow/pipeline1.png)
1. 데이터 흐름 활동의 파이프라인 디버그는 활성 디버그 클러스터를 사용하지만 초기화하는 데 최소 1분이 소요됩니다. **출력** 탭을 통해 진행 상황을 추적할 수 있습니다. 실행이 성공하면 안경 아이콘을 클릭하여 모니터링 창을 엽니다.

    ![파이프라인](media/tutorial-data-flow/pipeline2.png)
1. 모니터링 창에서 각 변환 단계에서 소요된 행 수와 시간을 확인할 수 있습니다.

    ![모니터링](media/tutorial-data-flow/pipeline3.png)
1. 데이터의 열 및 분할에 대한 자세한 정보를 보려면 변환을 클릭합니다.

    ![모니터링](media/tutorial-data-flow/pipeline4.png)

이 자습서를 올바르게 수행한 경우 싱크 폴더에 83 개의 행과 2 개의 열을 작성해야합니다. Blob 저장소를 확인하여 데이터가 올바른지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서의 파이프라인은 1910년부터 2000년까지의 평균 코미디 등급을 집계하고 데이터를 ADLS에 기록하는 데이터 흐름을 실행합니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 흐름 활동이 있는 파이프라인을 만듭니다.
> * 네 가지 변환으로 매핑 데이터 흐름을 빌드합니다.
> * 파이프라인 실행 테스트
> * 데이터 흐름 활동 모니터링

데이터 흐름 [표현 언어에](data-flow-expression-functions.md)대해 자세히 알아봅니다.

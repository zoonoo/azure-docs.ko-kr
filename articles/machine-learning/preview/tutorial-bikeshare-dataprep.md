---
title: "자전거 공유 자습서 - Azure Machine Learning Workbench에서 고급 데이터 준비"
description: "Azure Machine Learning Workbench를 사용하는 종단 간 데이터 준비 자습서"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial, azure
ms.topic: article
ms.date: 09/21/2017
ms.openlocfilehash: 722657c9bbae23a051a63972a8800d3cc40e7e40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="bike-share-tutorial-advanced-data-preparation-with-azure-machine-learning-workbench"></a>자전거 공유 자습서: Azure Machine Learning Workbench에서 고급 데이터 준비
Azure Machine Learning 서비스(미리 보기)는 데이터를 준비하고, 실험을 개발하고, 클라우드 범위에서 모델을 배포할 수 있는 전문 데이터 과학자를 위한 종단 간 데이터 과학 및 고급 분석 통합 솔루션입니다.

이 자습서에서는 Azure Machine Learning 서비스(미리 보기)를 사용하여 다음을 수행하는 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure Machine Learning 데이터 준비 도구를 사용하여 대화형으로 데이터 준비
> * 테스트 데이터 집합 가져오기, 변환 및 만들기
> * 데이터 준비 패키지 생성
> * Python을 사용하여 데이터 준비 패키지 실행
> * 추가 입력 파일에 대한 데이터 준비 패키지를 다시 사용하여 학습 데이터 집합 생성

> [!IMPORTANT]
> 이 자습서에는 데이터 준비할 뿐 예측 모델을 빌드하지 않습니다.
>
> 준비된 데이터를 사용하여 고유한 예측 모델을 학습할 수 있습니다. 예를 들어 2시간 동안 자전거 수요를 예측하도록 모델을 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* Azure Machine Learning Workbench는 로컬에 설치되어야 합니다. 자세한 내용은 [설치 빠른 시작](quickstart-installation.md)을 따르세요.
* Workbench에서 새 프로젝트를 만드는 데 익숙해야 합니다.

## <a name="data-acquisition"></a>데이터 취득
이 자습서에서는 [Boston Hubway 데이터 집합](https://s3.amazonaws.com/hubway-data/index.html) 및 [NOAA](http://www.noaa.gov/)의 보스턴 날씨 데이터를 사용합니다.

1. 다음 링크의 데이터 파일을 로컬 개발 환경에 다운로드합니다. 
   * [보스턴 날씨 데이터](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv) 
   * Hubway 웹 사이트의 Hubway 여행 데이터

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. 각 .zip 파일을 다운로드한 후에 압축을 풉니다.

## <a name="learn-about-the-datasets"></a>데이터 집합에 대한 자세한 정보
1. __보스턴 날씨__ 파일에는 시간 단위로 보고된 다음과 같은 날씨 관련 필드가 포함됩니다.
   * DATE
   * REPORTTPYE
   * HOURLYDRYBULBTEMPF
   * HOURLYRelativeHumidity
   * HOURLYWindSpeed

2. __Hubway__ 데이터는 연별 및 월별 파일로 구성됩니다. 예를 들어 `201501-hubway-tripdata.zip`이라는 파일에는 2015년 1월에 대한 데이터를 포함하는 .csv 파일이 포함됩니다. 데이터에는 각 행이 자전거 여행을 나타내는 다음 필드가 포함되어 있습니다.

   * 여행 기간(초)
   * 시작 시간 및 날짜
   * 중지 시간 및 날짜
   * 시작 스테이션 이름 및 ID
   * 끝 스테이션 이름 및 ID
   * 자전거 ID
   * 사용자 유형(일반 = 24시간 또는 72시간 패스 사용자, 멤버 = 월간 또는 연간 멤버)
   * 우편 번호(사용자가 멤버인 경우)
   * 성별(멤버별 자체 보고)

## <a name="create-a-new-project"></a>새 프로젝트 만들기
1. 시작 메뉴 또는 실행 프로그램에서 **Azure Machine Learning Workbench**를 시작합니다.

2. 새 Azure Machine Learning 프로젝트를 만듭니다.  **프로젝트** 페이지에서 **+** 단추 또는 **파일** > **새로 만들기**를 클릭합니다.
   - **빈 프로젝트** 템플릿을 사용합니다.
   - 프로젝트 이름을 **자전거 공유**로 지정합니다. 

## <a id="newdatasource"></a>새 데이터 원본 만들기

1. 새 데이터 원본을 만듭니다. 왼쪽 도구 모음에서 **데이터** 단추(실린더 아이콘)를 클릭합니다. **데이터 뷰**를 표시합니다.

   ![데이터 뷰 탭의 이미지](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. 데이터 원본을 추가합니다. **+** 아이콘을 선택하고 나서 **데이터 원본 추가**를 선택합니다.

   ![데이터 원본 추가 항목의 이미지](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>날씨 데이터 추가

1. **데이터 저장소**: 데이터를 포함하는 데이터 저장소를 선택합니다. 파일을 사용하고 있으므로 **파일/디렉터리**를 선택합니다. **다음**을 선택하여 계속합니다.

   ![파일/디렉터리 항목의 이미지](media/tutorial-bikeshare-dataprep/datasources.png)

2. **파일 선택 영역**: 날씨 데이터를 추가합니다. 이전에 다운로드한 `BostonWeather.csv` 파일로 이동하고 선택합니다. **다음**을 누릅니다.

   ![BostonWeater.csv를 선택한 파일 선택 영역의 이미지](media/tutorial-bikeshare-dataprep/pickweatherdatafile.png)

3. **파일 세부 정보**: 검색된 파일 스키마를 확인합니다. Azure Machine Learning Workbench는 파일에서 데이터를 분석하고 사용할 스키마를 유추합니다.

   ![파일 세부 정보의 이미지](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > Workbench는 경우에 따라 올바른 스키마를 검색할 수 없습니다. 매개 변수가 데이터 집합에 대해 올바른지 항상 확인해야 합니다. 날씨 데이터의 경우 다음과 같은 값으로 설정되어 있는지 확인합니다.
   >
   > * __파일 형식__: 분리된 파일(csv, tsv, txt 등)
   > * __구분 기호__: 쉽표[,]
   > * __주석 줄 문자__: 값이 설정되지 않습니다.
   > * __건너뛰기 선 모드__: 건너뛰지 않습니다.
   > * __파일 인코딩__: utf-8
   > * __승격 헤더 모드__: 첫 번째 파일에서 헤더를 사용합니다.

   데이터의 미리 보기는 다음과 같은 열에 표시되어야 합니다.
   * **Path**
   * **DATE**
   * **REPORTTYPE**
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

   계속하려면 **다음**을 선택합니다.

4. **데이터 형식**: 자동으로 검색되는 데이터 형식을 검토합니다. Azure Machine Learning Workbench는 파일에서 데이터를 분석하고 사용할 데이터 형식을 유추합니다.

   이 데이터의 경우 모든 열의 `DATA TYPE`을 `String`으로 변경합니다.

   > [!NOTE]
   > `String`은 이 자습서의 뒷부분에 나오는 Workbench의 기능을 강조 표시하는 데 사용됩니다. 

   ![데이터 형식의 이미지](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   계속하려면 __다음__을 선택합니다. 

5. **샘플링**: 샘플링 구성표를 만들려면 **+ 새로 만들기** 단추를 선택합니다. 추가된 새 __상위 10000__ 행을 선택하고 __편집__을 선택합니다. __샘플 전략__을 **전체 파일**로 설정한 후 **적용**을 선택합니다.

   ![새로운 샘플링 전략을 추가하는 이미지](media/tutorial-bikeshare-dataprep/weatherdatasampling.png)

   __전체 파일__ 전략을 사용하려면 __전체 파일__ 항목을 선택하고 __활성으로 설정__을 선택합니다. 별이 __전체 파일__ 옆에 나타나면 해당 항목이 활성 전략임을 나타냅니다.

   ![활성 전략인 전체 파일의 이미지](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   계속하려면 **다음**을 선택합니다.

6. **경로 열**: __경로 열__ 섹션을 사용하면 가져온 데이터의 열로 전체 파일 경로를 포함할 수 있습니다. __경로 열을 포함하지 않습니다.__를 선택합니다.

   > [!TIP]
   > 다른 파일 이름을 가진 많은 파일의 폴더를 가져오는 경우에 경로를 열로 포함하는 것이 유용합니다. 파일 이름이 나중에 추출하려는 정보를 포함하는 경우에도 유용합니다.

   ![포함하지 않을 경로 열 집합의 이미지](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **마침**: 데이터 원본 만들기를 완료하려면 **마침** 단추를 선택합니다.

    __BostonWeather__라는 새 데이터 원본 탭이 열립니다. 데이터의 샘플은 그리드 뷰에 표시됩니다. 샘플은 이전에 지정한 활성 샘플링 체계를 기반으로 합니다.

    화면 오른쪽에 있는 **단계** 창에 이 데이터 원본을 만드는 동안 수행되는 개별 작업을 표시합니다.

   ![데이터 원본, 예제 및 단계를 표시하는 이미지](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>데이터 원본 메트릭 보기

탭 그리드 뷰의 왼쪽 위에서 __메트릭__ 단추를 선택합니다. 이 뷰는 샘플링된 데이터의 배포 및 기타 집계 통계를 표시합니다.

![메트릭 표시 이미지](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> 통계의 표시 여부를 구성하려면 **메트릭 선택** 드롭다운을 사용합니다. 여기에서 메트릭을 선택하고 선택을 취소하여 그리드 뷰를 변경합니다.

__데이터 뷰__를 반환하려면 페이지의 왼쪽 위에서 __데이터__를 선택합니다.

## <a name="add-data-source-to-data-preparation-package"></a>데이터 준비 패키지에 데이터 원본 추가

1. __준비__를 선택하여 데이터 준비를 시작합니다. 

2. 메시지가 표시되면 데이터 준비 패키지의 이름을 `BikeShare Data Prep`과 같이 입력합니다. 

3. __확인__ 을 선택하여 계속합니다.

   ![준비 대화 상자의 이미지](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. **자전거 공유 데이터 준비**라는 새 패키지가 __데이터__ 탭의 __데이터 준비__ 섹션 아래에 표시됩니다. 

   패키지를 표시하려면 이 항목을 선택합니다. 

5. **>>** 단추를 선택하여 패키지에 포함된 __데이터 흐름__을 표시하도록 확장합니다. 이 예제에서 __BostonWeather__는 유일한 데이터 흐름입니다.

   > [!IMPORTANT]
   > 패키지에는 여러 데이터 흐름이 포함될 수 있습니다.

   ![패키지에 포함된 데이터 흐름의 이미지](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>값으로 데이터 필터링
1. 데이터를 필터링하려면 특정 값을 포함한 셀을 마우스 오른쪽 단추로 클릭하고 __필터__ 및 필터의 형식을 선택합니다.

2. 이 자습서의 경우 `FM-15` 값이 포함된 셀을 선택하고 필터를 **같음**이라는 필터로 설정합니다.  이제 데이터는 __REPORTTYPE__이 `FM-15`인 경우에만 행을 반환하도록 필터링됩니다.

   ![필터 대화 상자의 이미지](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15는 METAR(Meteorological Terminal Aviation Routine Weather Report)의 형식입니다. FM-15 보고서는 경험적으로 가장 완벽하고 누락된 데이터가 없다고 보여집니다.

## <a name="remove-a-column"></a>열 제거

__REPORTTYPE__ 열이 더 이상 필요하지 않습니다. 열 헤더를 마우스 오른쪽 단추로 클릭하고 **열 제거**를 선택합니다.

   ![열 제거 옵션의 이미지](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>데이터 형식 변경 및 오류 제거
1. 열 헤더를 선택하면서 동시에 __Ctrl__ 키를 누르면 한 번에 여러 열을 선택할 수 있습니다. 이를 사용하여 다음 열 헤더를 선택합니다.
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

2. 선택한 열 헤더 중 하나를 **마우스 오른쪽 단추로 클릭**하고 **필드 형식을 숫자로 변환**을 선택합니다. 그러면 열의 데이터 형식을 숫자로 변환합니다.

   ![여러 열을 숫자로 변환](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. 오류 값을 필터링합니다. 일부 열에는 데이터 형식 변환 문제가 발생합니다. 이 문제는 열의 __데이터 품질 목표__에서 빨간색으로 표시됩니다.

   오류가 있는 행을 제거하려면 **HOURLYDRYBULBTEMPF** 열 제목을 마우스 오른쪽 단추로 클릭합니다. **필터 열**을 선택합니다. 기본 **관심 사항**을 **행 유지**로 사용합니다. **조건** 드롭다운을 변경하여 **오류가 아님**을 선택합니다. 필터를 적용하려면 **확인**을 선택합니다.

4. 다른 열에서 나머지 오류 행을 제거하려면 **HOURLYRelativeHumidity** 및 **HOURLYWindSpeed** 열에서 이 필터 프로세스를 반복합니다.

## <a name="use-by-example-transformations"></a>예제 변환에서 사용

두 시간 블록 동안 예측에서 데이터를 사용하려면 2시간 동안 평균 날씨 조건을 계산해야 합니다. 이렇게 하려면 다음 작업을 사용할 수 있습니다.

* **DATE** 열을 별도의 **Date** 및 **Time** 열로 분할합니다. 자세한 단계는 다음 섹션을 참조하세요.

* **Time** 열에서 **Hour_Range** 열이 파생됩니다. 자세한 단계는 다음 섹션을 참조하세요.

* **DATE** 및 **Hour_Range** 열에서 **Date\_Hour\_Range** 열이 파생됩니다. 자세한 단계는 다음 섹션을 참조하세요.

### <a name="split-column-by-example"></a>예제별 열 분할

1. **DATE** 열을 별도의 날짜 및 시간 열로 분할합니다. **DATE** 열 헤더를 마우스 오른쪽 단추로 클릭하고 **예제별 열 분할**을 선택합니다.

   ![예제 항목별 열 분할의 이미지](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Azure Machine Learning Workbench는 자동으로 의미 있는 구분 기호를 식별하고 데이터를 날짜 및 시간 값으로 분할하여 두 개의 열을 만듭니다. 

3. __확인__을 선택하여 분할 작업 결과를 수용합니다.

   ![분할 열 DATE_1 및 DATE_2의 이미지](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>예제별 열 파생

1. 두 시간 범위를 파생하려면 __DATE\_2__ 열 헤더를 마우스 오른쪽 단추로 클릭하고 **예제별 열 파생**을 선택합니다.

   ![예제 항목별 열 파생의 이미지](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   새롭게 비어 있는 열은 null 값으로 추가됩니다.

2. 새 열에 첫 번째 빈 셀을 클릭합니다. 새 열에서 `12AM-2AM`을 입력하여 원하는 시간 범위의 예제를 제공하고 Enter 키를 누릅니다.

   ![오전 12시~오전 2시인 새 열의 이미지](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Azure ML Workbench는 사용자가 제공하는 예제에 따라 프로그램을 합성하고 나머지 행에서 동일한 프로그램을 적용합니다. 다른 모든 행은 사용자가 제공한 예제에 따라 자동으로 채워집니다. 또한 Workbench는 데이터를 분석하고 에지 사례를 식별하려고 합니다. 

3. 그리드 위에 있는 **데이터 분석** 텍스트는 Workbench에서 에지 사례를 검색하고 있음을 나타냅니다. 작업을 완료하면 상태가 **다음 제안된 행 검토** 또는 **제안 없음**으로 변경됩니다. 이 예제에서 **다음 제안된 행 검토**가 반환됩니다.

4. 제안된 변경 내용을 검토하려면 **다음 제안된 행 검토**를 선택합니다. (필요한 경우)검토하고 수정해야 하는 셀이 화면에서 강조 표시됩니다.

   ![검토 행의 이미지](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    __확인__을 선택하여 변환을 적용합니다.
 
5. __BostonWeather__에 대한 데이터의 그리드 뷰로 돌아갑니다. 이제 그리드는 이전에 추가한 세 개의 열을 포함합니다.

   ![추가된 행이 있는 그리드 뷰의 이미지](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   >  모든 변경 내용이 **단계** 창에서 보존됩니다. **단계** 창에서 만든 단계로 이동하고 아래쪽 화살표를 클릭하고 **편집**을 선택합니다. **예제별 열 파생**의 고급 창이 표시됩니다. 모든 예제가 여기에서 유지됩니다. 아래 그리드에서 행을 두 번 클릭하여 예제를 수동으로 추가할 수도 있습니다. **취소**를 선택하여 변경 내용을 적용하지 않고 기본 그리드로 돌아갑니다. **예제별 열 파생** 변환을 수행하는 동안 **고급 모드**를 선택하여 이 뷰에 액세스할 수도 있습니다.

6. 열 이름을 바꾸려면 열 헤더를 두 번 클릭하고 **시간 범위**를 입력합니다. **Enter** 키를 눌러 변경 내용을 저장합니다.

   ![열 이름 바꾸기](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. 날짜 및 시간 범위를 파생하려면 **Date\_1** 및 **시간 범위** 열을 다중 선택하고 마우스 오른쪽 단추로 클릭한 후 **예제별 열 파생**을 선택합니다.

   ![예제별 열 파생](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   `Jan 01, 2015 12AM-2AM`을 첫 번째 행에 대한 예로 입력하고 **Enter** 키를 누릅니다.

   Workbench는 사용자가 입력한 예제에 따라 변환을 결정합니다. 이 예제에서 결과적으로 날짜 형식이 변경되고 두 시간 창과 연결됩니다.

   ![예제 '2015년 1월 1일 오전 12시~오전 2시의 이미지](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

8. 상태가 **데이터 분석**에서 **다음 제안된 행 검토**로 변경되기를 기다립니다. 이 작업은 몇 초 정도 걸릴 수 있습니다. 상태 링크를 선택하여 제안된 행으로 이동합니다. 

   ![검토할 제안된 행의 이미지](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   원본 날짜 값이 dd/mm/yyyy 또는 mm/dd/yyyy가 될 수 있으므로 행에 null 값이 있습니다. `Jan 13, 2015 2AM-4AM`이라는 올바른 값을 입력하고 **Enter** 키를 누릅니다. Workbench는 나머지 행에 대한 파생을 개선하는 데 두 가지 예제를 사용합니다.

   ![올바른 형식의 데이터 이미지](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. **확인**을 선택하여 변환을 적용합니다.

   ![완료된 변환 그리드의 이미지](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > **단계** 창에서 아래쪽 화살표를 클릭하여 이 단계에서 **예제별 열 파생**이라는 고급 모드를 사용할 수 있습니다. 데이터 그리드에는 **DATE\_1** 및 **시간 범위** 열이라는 열 이름 옆에 확인란이 있습니다. **시간 범위** 열 옆에 있는 확인란의 선택을 취소하여 출력을 변경하는 방법을 확인합니다. 입력인 **시간 범위** 열이 없는 경우 **오전 12시~오전 2시**는 상수로 처리되고 파생된 값에 추가됩니다. **취소**를 선택하여 변경 내용을 적용하지 않고 기본 그리드로 돌아갑니다.

10. 열 이름을 바꾸려면 헤더를 두 번 클릭합니다. 이름을 **날짜 시간 범위**로 변경하고 **Enter** 키를 누릅니다.

11. **DATE**, **DATE\_1**, **DATE\_2** 및 **시간 범위** 열을 다중 선택합니다. 마우스 오른쪽 단추로 클릭한 후 **열 삭제**를 선택합니다.

## <a name="summarize-data-mean"></a>데이터 요약(평균)

다음 단계에서는 시간 범위 별로 그룹화된 값의 평균을 수행하여 날씨 조건을 요약합니다.

1. **날짜 시간 범위** 열을 선택한 후 **변환** 메뉴에서 **요약**을 선택합니다.

    ![변환 메뉴](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. 데이터를 요약하려면 페이지의 맨 아래에서 위의 왼쪽 및 오른쪽 창에 그리드의 열을 끌어옵니다. 왼쪽 창에는 **데이터를 그룹화하려면 여기에 열을 끌어오세요.**라는 텍스트가 포함됩니다. 오른쪽 창에는 **데이터를 요약하려면 여기에 열을 끌어오세요.**라는 텍스트가 포함됩니다. 

    아래쪽에 있는 그리드에서 왼쪽 창으로 **날짜 시간 범위** 열을 끌어옵니다. 오른쪽 창으로 **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity** 및 **HOURLYWindSpeed**를 끌어옵니다. 

    오른쪽 창에서 **평균**을 각 열에 대한 **집계** 측정값으로 선택합니다. **확인**을 클릭하여 요약을 완료합니다.

   ![요약된 데이터 화면의 이미지](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-using-script"></a>스크립트를 사용한 데이터 흐름 변환

숫자 열에 있는 데이터를 0~1이라는 범위로 변경하면 일부 모델을 신속하게 변환할 수 있습니다. 현재 일반적으로 이 변환을 수행하기 위한 기본 제공 변환이 없지만 Python 스크립트를 사용하여 이 작업을 수행할 수 있습니다.

1. **변환** 메뉴에서 **데이터 흐름 변환**을 선택합니다.

2. 표시되는 텍스트 상자에 다음 코드를 입력합니다. 열 이름을 사용한 경우 수정하지 않고 코드가 작동해야 합니다. Python으로 간단한 최소 최대 정규화 논리를 작성합니다.

    > [!WARNING]
    > 스크립트는 이 자습서에서는 이전에 사용한 열 이름을 사용합니다. 다른 열 이름이 있는 경우 스크립트의 이름을 변경해야 합니다.

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > Python 스크립트는 끝에 `df`를 반환해야 합니다. 이 값은 그리드를 채우는 데 사용됩니다.
    
    ![데이터 흐름 스크립트 변환 대화 상자](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. __확인__을 선택하여 스크립트를 사용합니다. 이제 그리드의 숫자 열에는 0~1이라는 범위의 값이 포함됩니다.

    ![0~1 사이의 값을 포함하는 그리드](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

날씨 데이터를 준비했습니다. 다음으로 여행 데이터를 준비합니다.

## <a name="load-trip-data"></a>여행 데이터 로드

1. `201701-hubway-tripdata.csv` 파일을 가져오려면 [새 데이터 원본 만들기](#newdatasource) 섹션의 단계를 사용합니다. 가져오기 프로세스 중에 다음 옵션을 사용합니다.

    * __샘플링 구성표__: **전체 파일** 샘플링 구성표는 샘플을 활성화하고 
    * __데이터 형식__: 기본값 적용합니다.

2. 데이터를 가져온 후에 __준비__ 단추를 선택하여 데이터 준비를 시작합니다. 기존 **BikeShare Data Prep.dprep** 패키지를 선택한 후 __확인__을 선택합니다.

    이 프로세스는 파일을 새로 만드는 대신 **데이터 흐름**을 기존 **데이터 준비** 파일에 추가합니다.

    ![기존 패키지를 선택하는 이미지](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. 그리드가 로드되면 __DATAFLOWS__를 확장합니다. **BostonWeather** 및 **201701-hubway-tripdata**라는 두 개의 데이터 흐름이 있습니다. **201701-hubway-tripdata** 항목을 선택합니다.

    ![201701-hubway-tripdata 항목의 이미지](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-map-inspector"></a>맵 검사기 사용

데이터 준비의 경우 문자열, 숫자 및 지역 데이터의 **검사자**라는 여러 가지 유용한 시각화 개체가 있어서 데이터를 이해하고 이상값을 식별할 수 있습니다. 다음 단계를 사용하여 맵 검사기를 사용합니다.

1. **시작 스테이션 위도** 및 **시작 스테이션 경도** 열을 다중 선택합니다. 열 중 하나를 마우스 오른쪽 단추로 클릭한 다음 **맵**을 선택합니다.

    > [!TIP]
    > 다중 선택을 사용하려면 __Ctrl__ 키를 누른 채 각 열의 헤더를 선택합니다.

    ![맵 시각화의 이미지](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. 맵 시각화를 최대화하려면 **최대화** 아이콘을 선택합니다. 맵을 창에 맞추려면 시각화의 왼쪽 위에 있는 **E** 아이콘을 선택합니다.

    ![최대화된 이미지](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. **최소화** 단추를 클릭하여 그리드 뷰로 돌아갑니다.

## <a name="use-column-statistics-inspector"></a>열 통계 검사기 사용

열 통계 검사기를 사용하려면 __대여 시간__ 열을 마우스 오른쪽 단추로 클릭하고 __열 통계__를 선택합니다.

![열 통계 항목](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

이 프로세스는 __검사자__ 창에서 __대여 시간 통계__라는 제목의 새 시각화를 추가합니다.

![대여 시간 통계 검사기의 이미지](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> 여행 기간의 최대 값은 2년 정도인 **961,814분**입니다. 데이터 집합에 이상값이 있는 것 같습니다.

## <a name="use-histogram-inspector"></a>히스토그램 검사기 사용

이상값을 식별하려면 __대여 시간__ 열을 마우스 오른쪽 단추로 클릭하고 __히스토그램__을 선택합니다.

![히스토그램 검사기](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

이상값이 그래프를 기울이기 때문에 히스토그램이 도움이 되지 않습니다.

## <a name="add-column-using-script"></a>스크립트를 사용하여 열 추가

1. **대여 시간**을 마우스 오른쪽 단추로 클릭하고 **열 추가(스크립트)**를 선택합니다.

    ![열 추가(스크립트) 메뉴의 이미지](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. __열 추가(스크립트)__ 대화 상자에서 다음 값을 사용합니다.

    * __새 열 이름__: logtripduration
    * __다음 뒤에 이 새 열 삽입__: tripduration
    * __새 열 코드__: `math.log(row.tripduration)`
    * __코드 블록 형식__: 식

   ![열 추가(스크립트) 대화 상자](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. __확인__을 선택하여 **logtripduration** 열을 추가합니다.

4. 열을 마우스 오른쪽 단추로 클릭하고 **히스토그램**을 선택합니다.

    ![logtripduration 열의 히스토그램](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

  시각적으로 이 히스토그램은 비정상적인 꼬리가 있는 정규 분포처럼 보입니다.

## <a name="use-advanced-filter"></a>고급 필터 사용

데이터에 필터를 사용하여 새 배포에서 검사기를 업데이트합니다. **logtripduration**을 마우스 오른쪽 단추로 클릭하고 **필터 열**을 선택합니다. __편집__ 대화 상자에 다음 값을 사용합니다.

* __이 숫자 열 필터링__: logtripduration
* __관심 사항__: 행 유지
* __시기__: 아래 조건 중 하나라도 True(논리적 OR)인 경우
* __이 열 조건__: 다음보다 적음
* __값__: 9

![필터 옵션](media/tutorial-bikeshare-dataprep/loftripfilter.png)

필터를 적용하려면 __확인__을 선택합니다.

![필터를 적용한 후에 업데이트된 히스토그램](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>Halo 효과

1. **logtripduration** 히스토그램 최대화 회색 히스토그램에 오버레이된 파란색 히스토그램이 있습니다. 이 화면을 **Halo 효과**라고 합니다.

    * **회색 히스토그램**은 작업 전에 분포를 나타냅니다(이 경우 필터링 작업).
    * **파란색 히스토그램**은 작업 후에 히스토그램을 나타냅니다. 

   Halo 효과를 통해 데이터에 대한 작업의 효과를 시각화할 수 있습니다.

   ![Halo 효과의 이미지](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > 파란색 히스토그램이 이전에 비해 짧게 표시됩니다. 새 범위에서 데이터를 자동으로 다시 버킷하기 때문입니다.

2. Halo를 제거하려면 __편집__을 선택하고 __Halo 표시__ 선택을 취소합니다.

    ![히스토그램의 옵션](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. **확인**을 선택하여 Halo 효과를 사용하지 않도록 설정하고 히스토그램을 최소화합니다.

### <a name="remove-columns"></a>열 제거

여행 데이터에서 각 행은 자전거 픽업 이벤트를 나타냅니다. 이 자습서에서는 **starttime** 및 **스테이션 시작** 열만 필요합니다. 이러한 두 개의 열을 다중 선택하여 다른 열을 제거하고 열 헤더를 마우스 오른쪽 단추로 클릭하고 **열 유지**를 선택합니다. 다른 열이 제거됩니다.

![열 유지 옵션의 이미지](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>데이터 요약(총계)

2시간 동안 자전거 수요를 요약하려면 파생 열을 사용합니다.

1. **starttime**을 마우스 오른쪽 단추로 클릭하고 **예제별 열 파생**을 선택합니다.

    ![예제 옵션별 파생의 이미지](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. 예를 들어 첫 번째 행에 `Jan 01, 2017 12AM-2AM` 값을 입력합니다.

    > [!IMPORTANT]
    > 파생 열의 이전 예제에서는 날짜 및 시간 기간을 포함하는 열을 파생시키는 여러 단계를 사용했습니다. 이 예제에서는 최종 출력의 예제를 제공하여 단일 단계로 이 작업을 수행할 수 있는지 확인합니다.

    > [!NOTE]
    > 행에 대한 예제를 제공할 수 있습니다. 이 예제에서 데이터의 첫 번째 행에 `Jan 01, 2017 12AM-2AM` 값이 유효합니다.

    ![예제 데이터의 이미지](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

3. 응용 프로그램이 모든 행에 대한 값을 계산할 때까지 기다립니다. 이 작업은 몇 초 정도 걸릴 수 있습니다. 분석이 완료되면 __다음 제안된 행 검토__ 링크를 사용하여 데이터를 검토합니다.

   ![검토 링크를 사용하여 완성된 분석의 이미지](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    계산된 값이 정확한지 확인합니다. 그렇지 않으면 값을 예상 값으로 업데이트하고 Enter 키를 누릅니다. 분석을 완료할 때까지 기다립니다. **제안 없음**이 표시되면 **다음 제안된 행 검토** 프로세스가 완료됩니다. **제안 없음**이 표시되면 응용 프로그램이 에지 사례를 살펴보고 만든 프로그램을 수용했음을 나타냅니다. 변환을 수락하기 전에 변환된 데이터의 시각적 검사를 수행하는 것이 좋습니다. 

4. **확인**을 선택하여 변환을 적용합니다. 새로 만든 열의 이름을 **날짜 시간 범위**로 바꿉니다.

    ![이름을 바꾼 열의 이미지](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. **starttime** 열 헤더를 마우스 오른쪽 단추로 클릭하고 **열 제거**를 선택합니다.

6. 데이터를 요약하려면 __변환__ 메뉴에서 __요약__을 선택합니다. 변환을 만들려면 다음 작업을 수행합니다.

    * __날짜 시간 범위__ 및 __시작 스테이션 ID__를 왼쪽 (그룹별) 창으로 끌어옵니다.
    * __시작 스테이션 ID__를 오른쪽 (데이터 요약) 창으로 끌어옵니다.

   ![요약 옵션의 이미지](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. **확인**을 선택하여 요약 결과를 적용합니다.

## <a name="join-dataflows"></a>데이터 흐름 조인

여정 데이터와 날씨 데이터를 조인하려면 다음 단계를 사용합니다.

1. __변환__ 메뉴에서 __조인__을 선택합니다.

2. __테이블__: **BostonWeather**를 왼쪽 데이터 흐름으로 선택하고 **201701-hubway-tripdata**를 오른쪽 데이터 흐름으로 선택합니다. 계속하려면 **다음**을 선택합니다.

    ![테이블 선택 영역의 이미지](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __키 열__: 두 테이블에서 **날짜 시간 범위** 열을 선택하고 __다음__을 선택합니다.

    ![키 열에서 조인의 이미지](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __조인 형식__: __행 일치__를 조인 형식으로 선택하고 __마침__을 선택합니다.

    ![행 조인 형식 일치](media/tutorial-bikeshare-dataprep/joinscreen.png)

    이 프로세스에서는 __조인 결과__라는 새 데이터 흐름을 만듭니다.

## <a name="create-additional-features"></a>추가 기능 만들기

1. 요일을 포함하는 열을 만들려면 **날짜 시간 범위** 열을 마우스 오른쪽 단추로 클릭하고 **예제별 열 파생**을 선택합니다. 일요일에 발생한 날짜에 __일요일__ 값을 사용합니다. 예를 들어 **2017년 1월 1일 오전 12시~오전 2시**와 같습니다. **Enter** 키를 누르고 **확인**을 선택합니다. 이 열의 이름을 __평일__로 변경합니다.

    ![요일을 포함하는 새 열을 만드는 이미지](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. 행에 시간을 포함하는 열을 만들려면 **날짜 시간 범위** 열을 마우스 오른쪽 단추로 클릭하고 **예제별 열 파생**을 선택합니다. **2017년 1월 1일 오전 12시~오전 2시**를 포함하는 행에 **오전 12시~오전 2시** 값을 사용합니다. **Enter** 키를 누르고 **확인**을 선택합니다. 이 열의 이름을 **기간**으로 변경합니다.

    ![기간 열의 이미지](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. **날짜 시간 범위** 및 **rDate 시간 범위** 열을 제거하려면 **Ctrl** 키를 누르고 각 열 헤더를 선택합니다. 마우스 오른쪽 단추로 클릭한 후 **열 삭제**를 선택합니다.

## <a name="read-data-from-python"></a>Python의 데이터를 읽기

Python 또는 PySpark의 데이터 준비 패키지를 실행하고 결과를 **데이터 프레임**으로 검색할 수 있습니다.

예제 Python 스크립트를 생성하려면 __자전거 공유 데이터 준비__를 마우스 오른쪽 단추로 클릭하고 __데이터 액세스 코드 파일 생성__을 선택합니다. 예제 Python 파일은 **프로젝트 폴더**에서 만들어지고 Workbench 내의 탭에서 로드됩니다. 다음 Python 스크립트는 생성되는 코드의 예제입니다.

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

이 자습서의 경우 파일의 이름은 `BikeShare Data Prep.py`입니다. 이 파일은 자습서의 뒷부분에서 사용됩니다.

## <a name="save-test-data-as-a-csv-file"></a>CSV 파일로 테스트 데이터 저장

**조인 결과** 데이터 흐름을 .CSV 파일로 저장하려면 `BikeShare Data Prep.py` 스크립트를 변경해야 합니다. 다음 코드를 사용하여 Python 스크립트를 업데이트합니다.

```python
from azureml.dataprep.package import run

# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTest.csv
df.to_csv('Your Test Data File Path here')
```

화면 맨 위에서 **실행**을 선택합니다. 스크립트는 로컬 컴퓨터에 **작업**으로 전송됩니다. 작업 상태가 __완료됨__으로 변경되면 파일이 지정된 위치에 작성됩니다.

## <a name="substitute-data-sources"></a>데이터 원본 대체

이전 단계에서는 `201701-hubway-tripdata.csv` 및 `BostonWeather.csv` 데이터 원본을 사용하여 테스트 데이터를 준비했습니다. 다른 여행 데이터 파일을 포함한 패키지를 사용하려면 다음 단계를 사용합니다.

1. 프로세스를 다음과 같이 변경하고 이전에 지정된 단계를 사용하여 새 **데이터 원본**을 만듭니다.

    * __파일 선택 영역__: 파일을 선택할 때 나머지 6개의 여행 데이터 .CSV 파일을 중복 선택합니다.

        ![나머지 6개의 파일 로드](media/tutorial-bikeshare-dataprep/selectsixfiles.png)

        > [!NOTE]
        > __+5__ 항목은 나열된 하나의 항목 이외에도 5개의 추가 파일이 있음을 나타냅니다.

    * __파일 세부 정보__: __승격 헤더 모드__를 **모든 파일에 동일한 헤더 포함**으로 설정합니다. 이 값은 파일이 각각 동일한 헤더를 포함하고 있음을 나타냅니다.

        ![파일 세부 정보 선택 영역](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   이 데이터 원본이 이후 단계에서 사용되므로 이름을 저장합니다.

2. 프로젝트에서 파일을 보려는 폴더 아이콘을 선택합니다. __aml\_config__ 디렉터리를 확장하고 `local.runconfig` 파일을 선택합니다.

    ![local.runconfig의 위치 이미지](media/tutorial-bikeshare-dataprep/localrunconfig.png) 

3. `local.runconfig` 파일의 끝에 다음 줄을 추가하고 파일을 저장할 디스크 아이콘을 선택합니다.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    이러한 변경으로 인해 데이터 원본을 6개의 여행 데이터 파일을 포함하는 데이터로 바꿉니다.

## <a name="save-training-data-as-a-csv-file"></a>학습 데이터를 CSV 파일로 저장

이전에 편집한 Python 파일 `BikeShare Data Prep.py`로 이동하고 학습 데이터를 저장할 다른 파일 경로를 제공합니다.

```python
from azureml.dataprep.package import run
# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTrain.csv
df.to_csv('Your Training Data File Path here')
```

새 작업을 제출하려면 페이지 맨 위에 있는 **실행** 아이콘을 사용합니다. **작업**은 새 구성과 함께 제출됩니다. 이 작업의 출력은 학습 데이터입니다. 이 데이터는 이전에 만든 동일한 데이터 준비 단계를 사용하여 생성됩니다. 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
자전거 공유 데이터 준비 자습서를 완료했습니다. 이 자습서에서는 Azure Machine Learning 서비스(미리 보기)를 사용하여 다음을 수행하는 방법에 대해 알아보았습니다.
> [!div class="checklist"]
> * Azure Machine Learning 데이터 준비 도구를 사용하여 대화형으로 데이터 준비
> * 테스트 데이터 집합 가져오기, 변환 및 만들기
> * 데이터 준비 패키지 생성
> * Python을 사용하여 데이터 준비 패키지 실행
> * 추가 입력 파일에 대한 데이터 준비 패키지를 다시 사용하여 학습 데이터 집합 생성

다음으로 데이터 준비에 대해 알아보겠습니다.
> [!div class="nextstepaction"]
> [데이터 준비 사용자 가이드](data-prep-user-guide.md)

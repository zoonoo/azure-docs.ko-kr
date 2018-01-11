---
title: "Azure Machine Learning 서비스(미리 보기)에서 아이리스 자습서 분류를 위한 데이터 준비 | Microsoft Docs"
description: "이 자습서 전체에서 Azure Machine Learning 서비스(미리 보기)를 사용하는 방법을 보여 줍니다. 1부이며 데이터 준비를 설명합니다."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 09/28/2017
ms.openlocfilehash: f417154c2c2a27b356cefb94739838bd2136e756
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2018
---
# <a name="classify-iris-part-1-prepare-the-data"></a>아이리스 분류 1부: 데이터 준비
Azure Machine Learning 서비스(미리 보기)는 데이터를 준비하고, 실험을 개발하고, 클라우드 범위에서 모델을 배포할 수 있는 전문 데이터 과학자를 위한 종단 간 데이터 과학 및 고급 분석 통합 솔루션입니다.

이 자습서는 3부로 구성된 시리즈 중 제1부입니다. 이 자습서에서는 Azure Machine Learning 서비스(미리 보기)의 기본 사항을 살펴봅니다. 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure Machine Learning Workbench에서 프로젝트 만들기
> * 데이터 준비 패키지 만들기
> * 데이터 준비 패키지를 호출하는 Python/PySpark 코드 생성

이 자습서에서는 변함 없는 [아이리스 꽃 데이터 집합](https://en.wikipedia.org/wiki/Iris_flower_data_set)을 사용합니다. 스크린샷은 Windows 전용이지만 Mac OS 환경에서도 거의 동일합니다.

## <a name="prerequisites"></a>필수 조건
- Azure Machine Learning 실험 계정을 만듭니다.
- Azure Machine Learning Workbench를 설치합니다.

[설치 및 만들기 빠른 시작](quickstart-installation.md) 문서의 지침에 따라 Azure Machine Learning Workbench 응용 프로그램을 설치할 수 있습니다. 이 설치에는 Azure 플랫폼 간 명령줄 도구 또는 Azure CLI도 포함됩니다.

## <a name="create-a-new-project-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench에서 새 프로젝트 만들기
1. Azure Machine Learning Workbench 앱을 열고 필요한 경우 로그인합니다. **프로젝트** 창에서 더하기 기호(**+**)를 선택하여 **새 프로젝트**를 만듭니다.

   ![새 작업 영역](media/tutorial-classifying-iris/new_ws.png)

2. **새 프로젝트 만들기** 세부 정보를 채웁니다. 

   ![새 프로젝트](media/tutorial-classifying-iris/new_project.png)

   - **프로젝트 이름** 상자를 프로젝트 이름으로 채웁니다. 예를 들어 **myIris** 값을 사용합니다.
   - 프로젝트가 만들어지는 **프로젝트 디렉터리**를 선택합니다. 예를 들어 `C:\Temp\` 값을 사용합니다. 
   - 선택 사항인 **프로젝트 설명**을 입력합니다. 
   - **Git 리포지토리** 필드도 선택 사항이며 비워둘 수 있습니다. Visual Studio Team Services에 비어 있는 기존의 Git 리포지토리(마스터 분기가 없는 리포지토리)를 제공할 수 있습니다. 이미 있는 Git 리포지토리를 사용하는 경우 나중에 시나리오 로밍 및 공유를 사용할 수 있습니다. 자세한 내용은 [Git 리포지토리 사용](using-git-ml-project.md)을 참조하세요. 
   - **작업 영역**을 선택합니다. 예를 들어 이 자습서에서는 **IrisGarden**을 사용합니다. 
   - 프로젝트 템플릿 목록에서 **Iris 분류** 템플릿을 선택합니다. 

3. **만들기** 단추를 선택합니다. 이제 프로젝트가 만들어지고 열립니다.

## <a name="create-a-data-preparation-package"></a>데이터 준비 패키지 만들기
1. **파일 보기**에서 **iris.csv** 파일을 엽니다. 이 파일은 5열 150행이 있는 테이블입니다. 여기에는 4개의 숫자 기능 열과 문자열 대상 열이 있지만, 열 머리글은 없습니다.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > 특히 파일 크기가 크면 프로젝트 폴더에 해당 데이터 파일을 포함하지 마십시오. **iris.csv**는 크기가 작기 때문에 데모용으로 이 템플릿에 포함됩니다. 자세한 내용은 [큰 데이터 파일을 읽고 쓰는 방법](how-to-read-write-files.md)을 참조하세요.

2. **데이터 뷰**에서 더하기 기호(**+**)를 선택하여 새 데이터 원본을 추가합니다. **데이터 원본 추가** 페이지가 열립니다. 

   ![데이터 뷰](media/tutorial-classifying-iris/data_view.png)

3. 기본 값을 그대로 두고 **다음** 단추를 선택합니다.  
 
   ![아이리스 선택](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >이 연습에서는 현재 프로젝트 디렉터리에 있는 **iris.csv** 파일을 선택해야 합니다. 그렇지 않으면 이후 단계가 실패할 수 있습니다.
   
4. 파일을 선택한 후 **마침** 단추를 선택합니다.

4. **iris-1.dsource**라는 새 파일이 만들어집니다. 샘플 프로젝트에는 번호가 지정되지 않은 **iris.dsource** 파일이 이미 있으므로 파일 이름이 대시가 있는 1로 지정되었습니다.  

   파일이 열리고 데이터가 표시됩니다. 일련의 열 머리글(**Column1**에서 **Column5**까지)이 이 데이터 집합에 자동으로 추가됩니다. 아래로 스크롤하여 데이터 집합의 마지막 행이 비어 있음을 확인합니다. 행은 추가 줄 바꿈이 CSV 파일에 있기 때문에 비어 있습니다.

   ![아이리스 데이터 뷰](media/tutorial-classifying-iris/iris_data_view.png)

5. **메트릭** 단추를 선택합니다. 히스토그램을 관찰합니다. 각 열에 대한 통계의 전체 집합이 계산됩니다. **데이터** 단추를 선택하여 데이터를 다시 볼 수도 있습니다. 

   ![아이리스 데이터 뷰](media/tutorial-classifying-iris/iris_metrics_view.png)

6. **준비** 단추를 선택합니다. **준비** 대화 상자가 열립니다. 

   샘플 프로젝트는 **iris.dprep** 파일과 함께 제공됩니다. 기본적으로 이미 있는 **iris.dprep** 데이터 준비 패키지에 새 데이터 흐름을 만들도록 합니다. 

   드롭다운 메뉴에서 **+ 새 데이터 준비 패키지**를 선택하고, 패키지 이름에 대한 새 값을 입력하고, **iris-1**을 사용한 다음 **확인**을 선택합니다.

   ![아이리스 데이터 뷰](media/tutorial-classifying-iris/new_dprep.png)

   **iris-1.dprep**라는 새 데이터 준비 패키지가 만들어지고 데이터 준비 편집기에서 열립니다.

7. 이제 기본적인 데이터 준비 작업 몇 가지를 수행해 보겠습니다. 열 이름을 바꿉니다. 각 열 머리글을 선택하여 헤더 텍스트를 편집할 수 있도록 합니다. 

   5가지 열 각각에 대해 **꽃받침 길이**, **꽃받침 너비**, **꽃잎 길이**, **꽃잎 너비** 및 **종류**를 입력합니다.

   ![열 이름 바꾸기](media/tutorial-classifying-iris/rename_column.png)

8. 고유 값을 계산하려면 **종류** 열을 선택한 다음 마우스 오른쪽 단추로 클릭하여 선택합니다. 드롭다운 메뉴에서 **값 계산**을 선택합니다. 

   ![값 개수 선택](media/tutorial-classifying-iris/value_count.png)

   이 작업으로 **검사기** 창이 열리고 4개 막대에 대한 히스토그램이 표시됩니다. 대상 열에 **Iris_virginica**, **Iris_versicolor**, **Iris-setosa**의 3가지 고유 값과 **(null)** 값이 있습니다.

9. Null 값을 필터링하려면 Null 값을 나타내는 그래프에서 막대를 선택합니다. **(null)** 값이 있는 행 하나가 있습니다. 이 행을 제거하려면 빼기 기호(**-**)를 선택합니다.

   ![값 개수 히스토그램](media/tutorial-classifying-iris/filter_out.png)

10. **단계** 창에 자세히 설명된 개별 단계를 확인합니다. 열 이름을 변경하고 Null 값 행을 필터링했으므로 각 작업은 데이터 준비 단계로 기록되지 않았습니다. 개별 단계를 편집하여 설정을 조정하고, 단계 순서를 조정하고, 단계를 제거할 수 있습니다.

   ![단계](media/tutorial-classifying-iris/steps.png)

11. 데이터 준비 편집기를 닫습니다. 그래프 아이콘으로 **iris-1** 탭에서 **닫기**(x)를 선택합니다. 작업은 **데이터 준비** 제목 아래 표시된 **iris-1.dprep** 파일에 자동으로 저장됩니다.

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>데이터 준비 패키지를 호출하는 Python/PySpark 코드 생성

1. **iris-1.dprep** 파일을 마우스 오른쪽 단추로 클릭하여 상황에 맞는 메뉴를 표시한 다음 **데이터 액세스 코드 파일 생성**을 선택합니다. 

   ![코드 생성](media/tutorial-classifying-iris/generate_code.png)

2. **iris-1.py**라는 새 파일이 다음 코드 줄로 열립니다.

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   이 코드 조각에서는 데이터 준비 패키지로 만든 논리를 호출합니다. 이 코드가 실행되는 컨텍스트에 따라 `df`는 다양한 종류의 데이터 프레임을 나타냅니다. Python 런타임에서 실행될 때는 [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)이 사용되고 Spark 컨텍스트에서 실행될 때는 [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html)이 사용됩니다. 

   Azure Machine Learning Workbench에서 데이터를 준비하는 방법에 대한 자세한 내용은 [데이터 준비 시작](data-prep-getting-started.md) 가이드를 참조하세요.

## <a name="next-steps"></a>다음 단계
3부 자습서 시리즈의 제1부에서는 Azure Machine Learning Workbench를 사용하여 다음 작업을 수행했습니다.
> [!div class="checklist"]
> * 새 프로젝트 만들기 
> * 데이터 준비 패키지 만들기
> * 데이터 준비 패키지를 호출하는 Python/PySpark 코드 생성

Azure Machine Learning 모델을 빌드하는 방법을 배우는 시리즈의 다음 부분으로 이동할 준비가 되었습니다.
> [!div class="nextstepaction"]
> [모델 작성](tutorial-classifying-iris-part-2.md)

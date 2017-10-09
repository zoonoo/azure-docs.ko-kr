---
title: "Machine Learning 서비스(미리 보기)에서 Iris 자습서 분류를 위한 데이터 준비 | Microsoft Docs"
description: "이 자습서 전체에서 Azure Machine Learning 서비스(미리 보기)를 사용하는 방법을 보여 줍니다. 이는 제1부 데이터 준비입니다."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e88b6bf74b7492353e5d5d004bde12fa4787e405
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-1-prepare-data"></a>Iris 분류 1부: 데이터 준비
Azure Machine Learning 서비스(미리 보기)는 데이터를 준비하고, 실험을 개발하고, 클라우드 범위에서 모델을 배포할 수 있는 전문 데이터 과학자를 위한 종단 간 데이터 과학 및 고급 분석 통합 솔루션입니다.

이 자습서는 3부로 구성된 시리즈 중 제1부입니다. 이 자습서에서는 Azure Machine Learning 서비스(미리 보기)의 기본 사항을 살펴보겠습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure Machine Learning Workbench에서 프로젝트 만들기
> * 데이터 준비 패키지 만들기
> * 데이터 준비 패키지를 호출하는 Python/PySpark 코드 생성

이 자습서에서는 작업을 단순하게 유지하기 위해 변함 없는 [Iris 꽃 데이터 집합](https://en.wikipedia.org/wiki/Iris_flower_data_set)을 사용합니다. 스크린샷은 Windows 전용이지만 macOS 환경에서도 거의 동일합니다.

## <a name="launch-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench 시작
[빠른 시작 설치 및 만들기](quickstart-installation.md)에 따라 CLI(명령줄 인터페이스)도 포함한 Azure Machine Learning Workbench 응용 프로그램을 설치합니다. Azure Machine Learning Workbench 앱을 시작하고 필요한 경우 로그인합니다.

## <a name="create-a-new-project"></a>새 프로젝트 만들기
1. **프로젝트** 창에서 **+** 아이콘을 클릭하여 **새 프로젝트**를 만듭니다.

   ![새 작업 영역](media/tutorial-classifying-iris/new_ws.png)

2. **새 프로젝트 만들기** 세부 정보를 채웁니다. 

   ![새 프로젝트](media/tutorial-classifying-iris/new_project.png)

   - **프로젝트 이름** 필드를 프로젝트 이름으로 채웁니다. 예를 들어 **myIris** 값을 사용합니다.
   - 프로젝트가 만들어지는 **프로젝트 디렉터리**를 선택합니다. 예를 들어 **C:\Temp** 값을 사용합니다. 
   - **프로젝트 설명**을 입력합니다. 
   - **Git 리포지토리** 필드는 선택 사항이며 비워둘 수 있습니다. VSTS에 비어 있는 기존의 Git 리포지토리(마스터 분기가 없는 리포지토리)를 제공할 수 있습니다. 이렇게 하면 나중에 로밍 및 공유 시나리오를 사용하도록 설정할 수 있습니다. 자세한 내용은 [Git 리포지토리 사용](using-git-ml-project.md) 문서를 참조하세요. 
   - **작업 영역**을 선택합니다. 예를 들어 이 자습서에서는 **IrisGarden**을 사용합니다. 
   - 프로젝트 템플릿 목록에서 **Iris 분류** 템플릿을 선택합니다. 

3. **만들기** 단추를 클릭하여 프로젝트를 만듭니다. 이제 프로젝트가 만들어지고 열립니다.

## <a name="create-a-data-preparation-package"></a>데이터 준비 패키지 만들기
1. **파일 보기**에서 `iris.csv` 파일을 엽니다. 이 파일은 5열 150행이 있는 간단한 테이블입니다. 여기에는 4개의 숫자 기능 열과 문자열 대상 열이 있지만, 열 머리글은 없습니다.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   >참고: 특히 파일 크기가 크면 프로젝트 폴더에 해당 데이터 파일을 포함하지 않는 것이 좋습니다. 크기가 작기 때문에 `iris.csv`가 데모용으로 이 템플릿에 포함되었습니다. 자세한 내용은 [큰 데이터 파일을 읽고 쓰는 방법](how-to-read-write-files.md) 문서를 참조하세요.

2. **데이터 뷰**에서 **+** 아이콘을 클릭하여 새 데이터 원본을 추가합니다. **데이터 원본 추가** 마법사가 시작됩니다. 

   ![데이터 뷰](media/tutorial-classifying-iris/data_view.png)

3. **파일/디렉터리** 옵션을 선택하고 `iris.csv` 로컬 파일을 선택합니다. 각 화면에서 기본 설정을 적용하고, 마지막으로 **마침**을 클릭합니다. 

   ![iris 선택](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >이 연습에서는 현재 프로젝트 디렉터리에 있는 `iris.csv` 파일을 선택해야 합니다. 그렇지 않으면 두 번째 단계가 실패할 수 있습니다. 

4. 새 `iris-1.dsource` 파일이 만들어집니다. 샘플 프로젝트에는 번호가 지정되지 않은 `iris.dsource` 파일이 이미 있으므로 파일 이름이 대시가 있는 1로 지정되었습니다.  파일이 열리고 데이터가 표시됩니다. 일련의 열 머리글(`Column1`에서 `Column5`까지)이 이 데이터 집합에 자동으로 추가됩니다. 아래로 스크롤하여 데이터 집합의 마지막 행이 비어 있음을 확인합니다. 이는 csv 파일에 여분의 줄 바꿈이 있기 때문입니다.

   ![iris 데이터 뷰](media/tutorial-classifying-iris/iris_data_view.png)

5. 이제 **메트릭** 단추를 클릭합니다. 히스토그램과 각 열에 대해 계산된 전체 통계 집합을 확인합니다. **데이터** 단추를 클릭하여 데이터를 다시 볼 수도 있습니다. 

   ![iris 데이터 뷰](media/tutorial-classifying-iris/iris_metrics_view.png)

6. **메트릭** 단추(메트릭 보기에 있는 경우 **데이터** 단추) 옆에 있는 **준비** 단추를 클릭합니다. **준비** 대화 상자가 표시됩니다. 샘플 프로젝트에는 이미 `iris.dprep` 파일이 제공되므로 기본적으로 기존 **iris.dprep** 데이터 준비 패키지에 새 데이터 흐름을 만들도록 요청합니다. 드롭다운 값을 **+ 새 데이터 준비 패키지**로 변경하고 새 "iris-1" 값을 입력한 다음 **확인**을 클릭합니다.

   ![iris 데이터 뷰](media/tutorial-classifying-iris/new_dprep.png)

`iris-1.dprep`이라는 새 데이터 준비 패키지가 만들어지고 데이터 준비 편집기에서 열립니다.

이제 간단한 데이터 준비 작업 몇 가지를 수행해 보겠습니다. 각 열 머리글을 클릭하여 열 이름을 바꾸고 머리글 텍스트를 편집할 수 있게 합니다. 5개 열 각각에 대해 `Sepal Length`, `Sepal Width`, `Petal Length`, `Petal Width` 및 `Species`를 입력합니다.

![열 이름 바꾸기](media/tutorial-classifying-iris/rename_column.png)

`Species` 열을 선택하고 마우스 오른쪽 단추로 클릭합니다. **값 개수**를 선택합니다. 

![값 개수](media/tutorial-classifying-iris/value_count.png)

이 작업에서는 네 개의 막대가 있는 히스토그램을 만듭니다. 대상 열에는 세 개의 고유 값, `Iris_virginica`, `Iris_versicolor`, `Iris-setosa`가 있습니다. 또한 `(null)` 값이 있는 행 하나가 있습니다. null 값을 나타내는 막대를 선택하여 이 행을 제거하겠으며, **-** 필터 단추를 클릭하여 제거합니다. 

![값 개수](media/tutorial-classifying-iris/filter_out.png)

열 이름 바꾸기 및 null 값 행 필터링 작업을 수행하므로 수행하는 각 작업은 **단계** 창에서 데이터 준비 단계로 기록됩니다. 이러한 설정을 편집(수정)하거나, 순서를 변경하거나, 제거할 수 있습니다.

![단계](media/tutorial-classifying-iris/steps.png)

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>데이터 준비 패키지를 호출하는 Python/PySpark 코드 생성

이제 데이터 준비 편집기를 닫습니다. (걱정하지 마세요! 자동으로 저장됩니다.) **iris-1.dprep** 파일을 마우스 오른쪽 단추로 클릭하여 상황에 맞는 메뉴를 표시하고, **데이터 액세스 코드 파일 생성**을 선택합니다. 

![코드 생성](media/tutorial-classifying-iris/generate_code.png)

**iris-1.py** 파일은 몇 가지 주석과 함께 미리 채워진 다음 두 줄의 코드로 만들어집니다.

```python
# This code snippet will load the referenced package and return a DataFrame.
# If the code is run in a PySpark environment, the code will return a
# Spark DataFrame. If not, the code will return a Pandas DataFrame.

from azureml.dataprep.package import run
df = run('iris.dprep', dataflow_idx=0)
```
이 코드 조각에서는 데이터 준비 패키지로 만든 논리를 호출합니다. 이 코드가 실행되는 컨텍스트에 따라 `df`는 Python 런타임에서 실행되는 경우 [pandas 데이터 프레임](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html), Spark 컨텍스트에서 실행되는 경우 [Spark 데이터 프레임](https://spark.apache.org/docs/latest/sql-programming-guide.html)이 됩니다. Azure Machine Learning Workbench에서 데이터를 준비하는 방법에 대한 자세한 내용은 [데이터 준비 시작 가이드](data-prep-getting-started.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
3부 자습서 시리즈의 제1부에서는 Azure Machine Learning Workbench를 사용하여 다음 작업을 수행했습니다.
> [!div class="checklist"]
> * 새 프로젝트 만들기 
> * 데이터 준비 패키지 만들기
> * 데이터 준비 패키지를 호출하는 Python/PySpark 코드 생성

기계 학습 모델을 작성할 준비가 되었으면 시리즈의 다음 문서로 진행하세요.
> [!div class="nextstepaction"]
> [모델 작성](tutorial-classifying-iris-part-2.md)


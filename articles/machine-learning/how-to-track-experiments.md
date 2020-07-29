---
title: Log ML 실험 & 메트릭
titleSuffix: Azure Machine Learning
description: Azure ML 실험과 실행 메트릭을 모니터링하여 모델 생성 프로세스를 향상시킵니다. 학습 스크립트에 로깅을 추가하고 기록된 실행 결과를 확인합니다.  run.log, Run.start_logging 또는 ScriptRunConfig를 사용합니다.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9833c0974af9a5bcc069ad41cfb57631dbed34dc
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320955"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Azure ML 실험 실행 및 메트릭 모니터링
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

실험을 추적하고 실행 메트릭을 모니터링하여 모델 생성 프로세스를 개선합니다. 이 문서에서는 학습 스크립트에 로깅 코드를 추가하고, 실험 실행을 제출하여 모니터링하고, Azure Machine Learning 결과를 검사하는 방법에 대해 알아봅니다.

> [!NOTE]
> Azure Machine Learning은 자동화된 기계 학습 실행 또는 학습 작업을 실행하는 Docker 컨테이너와 같이 학습 중 다른 원본의 정보를 기록할 수도 있습니다. 이러한 로그는 문서화되어 있지 않습니다. 문제가 발생하여 Microsoft 지원에 문의하는 경우 이러한 로그를 사용하여 문제를 해결할 수 있습니다.

> [!TIP]
> 이 문서의 정보는 주로 모델 학습 프로세스를 모니터링하는 데이터 과학자 및 개발자를 위한 것입니다. 할당량, 완료된 학습 실행 또는 완료된 모델 배포와 같이 Azure Machine Learning의 리소스 사용 및 이벤트를 모니터링하는 데 관심이 있는 관리자는 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.

## <a name="available-metrics-to-track"></a>추적할 수 있는 메트릭

실험을 학습하는 동안 실행에 추가할 수 있는 메트릭은 다음과 같습니다. 실행 시 추적할 수 있는 메트릭에 대한 자세한 목록을 보려면 [Run 클래스 참조 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)를 참조하세요.

|Type| Python 함수 | 메모|
|----|:----|:----|
|스칼라 값 |함수:<br>`run.log(name, value, description='')`<br><br>예제:<br>run.log(“accuracy”, 0.95) |숫자 또는 문자열 값을 지정된 이름의 실행에 기록합니다. 메트릭을 실행에 기록하면 해당 메트릭이 실험의 실행 기록에 저장됩니다.  하나의 실행 내에서 동일한 메트릭을 여러 번 기록할 수 있으며 결과는 해당 메트릭의 벡터로 간주됩니다.|
|목록|함수:<br>`run.log_list(name, value, description='')`<br><br>예제:<br>run.log_list(“accuracies”, [0.6, 0.7, 0.87]) | 값 목록을 지정된 이름의 실행에 기록합니다.|
|행|함수:<br>`run.log_row(name, description=None, **kwargs)`<br>예제:<br>run.log_row(“Y over X”, x=1, y=0.4) | *log_row*를 사용하여 kwargs에 설명된 대로 열이 여러 개 있는 메트릭을 만듭니다. 명명된 각 매개 변수는 지정된 값이 있는 열을 생성합니다.  *log_row*는 임의의 튜플을 로깅하기 위해 한 번 호출되거나 루프에서 여러 번 호출되어 전체 테이블을 생성할 수 있습니다.|
|테이블|함수:<br>`run.log_table(name, value, description='')`<br><br>예제:<br>run.log_table(“Y over X”, {”x”:[1, 2, 3], “y”:[0.6, 0.7, 0.89]}) | 사전 개체를 지정된 이름의 실행에 기록합니다. |
|이미지|함수:<br>`run.log_image(name, path=None, plot=None)`<br><br>예제:<br>`run.log_image("ROC", plot=plt)` | 이미지를 실행 기록에 로깅합니다. log_image를 사용하여 .PNG 이미지 파일이나 matplotlib 도면을 실행에 기록합니다.  이러한 이미지는 실행 기록에서 볼 수 있고 비교할 수 있습니다.|
|실행 태그 지정|함수:<br>`run.tag(key, value=None)`<br><br>예제:<br>run.tag(“selected”, “yes”) | 문자열 키와 선택적 문자열 값을 사용하여 실행에 대한 태그를 지정합니다.|
|파일 또는 디렉터리 업로드|함수:<br>`run.upload_file(name, path_or_stream)`<br> <br> 예제:<br>run.upload_file("best_model.pkl", "./model.pkl") | 파일을 실행 기록에 업로드합니다. 실행은 지정된 출력 디렉터리에서 파일을 자동으로 캡처합니다. 이 디렉터리는 대부분의 실행 형식에 대해 기본적으로 "./outputs"로 지정됩니다.  pload_file은 추가 파일을 업로드해야 하거나 출력 디렉터리를 지정하지 않은 경우에만 사용합니다. outputs 디렉터리에 업로드되도록 이름에 `outputs`를 추가하는 것이 좋습니다. `run.get_file_names()`를 호출하여 이 실행 기록와 연결된 모든 파일을 나열할 수 있습니다.|

> [!NOTE]
> 스칼라, 목록, 행 및 테이블에 대한 메트릭은 부동 소수점, 정수 또는 문자열 형식일 수 있습니다.

## <a name="choose-a-logging-option"></a>로깅 옵션 선택

실험을 추적하거나 모니터링하려면 실행을 제출할 때 로깅을 시작하는 코드를 추가해야 합니다. 실행 제출을 트리거하는 방법은 다음과 같습니다.
* __Run.start_logging__ - 학습 스크립트에 로깅 함수를 추가하고 지정된 실험에서 대화형 로깅 세션을 시작합니다. **start_logging**은 노트북과 같은 시나리오에서 사용할 대화형 실행을 만듭니다. 세션 중에 기록된 모든 메트릭이 실험의 실행 기록에 추가됩니다.
* __ScriptRunConfig__ - 학습 스크립트에 로깅 함수를 추가하고 실행을 사용하여 전체 스크립트 폴더를 로드합니다.  **ScriptRunConfig**는 스크립트 실행 구성을 설정하는 클래스입니다. 이 옵션을 사용하면 완료 알림을 받거나 모니터링할 시각적 위젯을 가져오는 모니터링 코드를 추가할 수 있습니다.
* __디자이너 로깅__ - __Python 스크립트 실행__ 모듈을 사용하여 끌어서 놓기 디자이너 파이프라인에 로깅 함수를 추가합니다. 로그 디자이너 실험에 Python 코드를 추가합니다. 

## <a name="set-up-the-workspace"></a>작업 영역 설정
로깅을 추가하고 실험을 제출하기 전에 작업 영역을 설정해야 합니다.

1. 작업 영역을 로드합니다. 작업 영역 구성을 설정하는 방법에 대한 자세한 내용은 [작업 영역 구성 파일](how-to-configure-environment.md#workspace)을 참조하세요.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_ws)]


## <a name="option-1-use-start_logging"></a>옵션 1: start_logging 사용

**start_logging**은 노트북과 같은 시나리오에서 사용할 대화형 실행을 만듭니다. 세션 중에 기록된 모든 메트릭이 실험의 실행 기록에 추가됩니다.

다음 예제에서는 로컬 Jupyter 노트북에서 간단한 sklearn Ridge 모델을 로컬로 학습합니다. 다른 환경에 실험을 제출하는 방법에 대해 자세히 알아 보려면 [Azure Machine Learning을 사용하여 모델을 학습하기 위한 컴퓨팅 대상 설정](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets)을 참조하세요.

### <a name="load-the-data"></a>데이터 로드

이 예에서는 scikit-learn과 함께 제공되는 잘 알려진 작은 데이터 세트인 당뇨병 데이터 세트를 사용합니다. 이 셀은 데이터 세트를 로드하고 임의의 학습 및 테스트 집합으로 분할합니다.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_data)]

### <a name="add-tracking"></a>추적 추가
Azure Machine Learning SDK를 사용하여 실험 추적을 추가하고 지속형 모델을 실험 실행 기록에 업로드합니다. 다음 코드는 태그를 지정하고, 기록하고, 모델 파일을 실험 실행에 업로드합니다.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

스크립트는 ```run.complete()```로 끝나며 실행이 완료됨으로 표시됩니다.  이 함수는 일반적으로 대화형 노트북 시나리오에서 사용됩니다.

## <a name="option-2-use-scriptrunconfig"></a>옵션 2: ScriptRunConfig 사용

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py)는 스크립트 실행 구성을 설정하는 클래스입니다. 이 옵션을 사용하면 완료 알림을 받거나 모니터링할 시각적 위젯을 가져오는 모니터링 코드를 추가할 수 있습니다.

이 예제에서는 위의 기본 sklearn Ridge 모델을 확장합니다. 실험의 실행에서 메트릭 및 학습된 모델을 캡처하기 위해 모델의 알파 값을 스윕하는 간단한 매개 변수 스윕을 수행합니다. 예제는 사용자 관리 환경에 대해 로컬로 실행됩니다. 

1. 학습 스크립트 `train.py`를 만듭니다.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. `train.py` 스크립트는 Ridge 모델에서 사용할 알파 값 목록을 가져올 수 있는 `mylib.py`를 참조합니다.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. 사용자 관리 로컬 환경을 구성합니다.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=user_managed_env)]


4. ```train.py``` 스크립트를 제출하여 사용자 관리 환경에서 실행합니다. 이 스크립트 폴더 전체는 ```mylib.py``` 파일을 포함하여 학습을 위해 제출됩니다.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

## <a name="option-3-log-designer-experiments"></a>옵션 3: 로그 디자이너 실험

__Python 스크립트 실행__ 모듈을 사용하여 디자이너 실험에 로깅 논리를 추가합니다. 이 워크플로를 사용하여 모든 값을 기록할 수 있지만 __모델 평가__ 모듈에서 메트릭을 기록하여 여러 실행 간에 모델 성능을 추적하는 것에 특히 유용합니다.

1. __Python 스크립트 실행__ 모듈을 __모델 평가__ 모듈의 출력에 연결합니다. __모델 평가__ 는 2 개 모델의 평가 결과를 출력할 수 있습니다. 다음 예에서는 부모 실행 수준에서 2 개의 출력 포트 메트릭을 기록 하는 방법을 보여 줍니다. 

    ![Python 스크립트 실행 모듈을 모델 평가 모듈에 연결](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. 다음 코드를 __Python 스크립트 실행__ 코드 편집기에 붙여넣어 학습된 모델의 평균 절대 오차를 기록합니다.

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.

        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        run.parent.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])

        # Log right output port result of Evaluate Model.
        run.parent.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])
    
        return dataframe1,
    ```

1. 파이프라인 실행이 완료 되 면 실험 페이지에서 *Mean_Absolute_Error* 를 확인할 수 있습니다.

    ![Python 스크립트 실행 모듈을 모델 평가 모듈에 연결](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="manage-a-run"></a>실행 관리

[학습 실행 시작, 모니터링 및 취소](how-to-manage-runs.md) 문서에서는 실험을 관리하는 방법에 대한 특정 Azure Machine Learning 워크플로를 중점적으로 설명합니다.

## <a name="view-run-details"></a>실행 세부 정보 보기

### <a name="view-activequeued-runs-from-the-browser"></a>브라우저에서 활성 실행/큐 대기 실행 보기

모델 학습에 사용되는 컴퓨팅 대상은 공유 리소스입니다. 따라서 지정된 시간에 여러 실행이 큐 대기 또는 활성 상태일 수 있습니다. 브라우저에서 특정 컴퓨팅 대상에 대한 실행을 확인하려면 다음 단계를 따릅니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com/)에서 작업 영역을 선택한 다음 페이지의 왼쪽에서 __컴퓨팅__을 선택합니다.

1. 학습에 사용되는 컴퓨팅 대상 목록을 표시하려면 __학습 클러스터__를 선택합니다. 그런 다음 클러스터를 선택합니다.

    ![학습 클러스터 선택](./media/how-to-track-experiments/select-training-compute.png)

1. __실행__을 선택합니다. 이 클러스터를 사용하는 실행 목록이 표시됩니다. 특정 실행에 대한 세부 정보를 보려면 __실행__ 열의 링크를 사용합니다. 실험에 대한 세부 정보를 보려면 __실험__ 열의 링크를 사용합니다.

    ![학습 클러스터에 대한 실행 선택](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > 실행에는 자식 실행이 포함될 수 있으므로 하나의 학습 작업으로 여러 항목이 생성될 수 있습니다.

실행이 완료되면 이 페이지에 더 이상 표시되지 않습니다. 완료된 실행에 대한 정보를 보려면 스튜디오의 __실험__ 섹션을 방문하여 실험 및 실행을 선택합니다. 자세한 내용은 [실행 메트릭 쿼리](#queryrunmetrics) 섹션을 참조하세요.

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Jupyter Notebook 위젯을 사용하여 실행 모니터링
**ScriptRunConfig** 메서드를 사용하여 실행을 제출하면 [Jupyter 위젯](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)을 통해 실행 진행 상황을 확인할 수 있습니다. 실행 제출과 마찬가지로, 위젯은 비동기적이며 작업이 완료될 때까지 10~15초 간격으로 라이브 업데이트를 제공합니다.

1. 실행이 완료될 때까지 기다리는 동안 Jupyter 위젯을 봅니다.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Jupyter 노트북 위젯의 스크린샷](./media/how-to-track-experiments/run-details-widget.png)

   작업 영역에서 동일한 표시에 대한 링크를 가져올 수도 있습니다.

   ```python
   print(run.get_portal_url())
   ```

2. **[자동화된 기계 학습 실행의 경우]** 이전 실행에서 차트에 액세스합니다. `<<experiment_name>>`을 적절한 실험 이름으로 바꿉니다.

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![자동화된 기계 학습을 위한 Jupyter Notebook 위젯](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


파이프라인의 세부 정보를 보려면 테이블에서 살펴보려는 파이프라인을 클릭합니다. Azure Machine Learning Studio의 팝업 항목에 차트가 렌더링됩니다.

### <a name="get-log-results-upon-completion"></a>완료 시 로그 결과 가져오기

모델 학습 및 모니터링은 백그라운드에서 수행되므로 기다리는 동안 다른 작업을 실행할 수 있습니다. 또한 모델에서 학습을 완료할 때까지 기다린 후에 더 많은 코드를 실행할 수도 있습니다. **ScriptRunConfig**를 사용하면 ```run.wait_for_completion(show_output = True)```를 사용하여 모델 학습이 완료된 시점을 표시할 수 있습니다. ```show_output``` 플래그는 자세한 정보를 출력합니다. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>실행 메트릭 쿼리

```run.get_metrics()```를 사용하여 학습된 모델의 메트릭을 볼 수 있습니다. 이제 위의 예제에 기록된 모든 메트릭을 가져와 최상의 모델을 결정할 수 있습니다.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>[Azure Machine Learning 스튜디오](https://ml.azure.com)에서 작업 영역의 실험 보기

실험에서 실행이 완료되면 기록된 실험 실행 기록을 찾아볼 수 있습니다. [Azure Machine Learning 스튜디오](https://ml.azure.com)에서 기록에 액세스할 수 있습니다.

실험 탭으로 이동하여 실험을 선택합니다. 실험 실행 대시보드로 이동하여 각 실행에 대해 기록된 추적 메트릭과 차트를 볼 수 있습니다. 

실행 목록 테이블을 편집 하 여 실행에 대해 기록 된 마지막 값, 최소값 또는 최대값을 표시할 수 있습니다. 실행 목록에서 여러 실행을 선택 하거나 선택 취소할 수 있으며, 선택한 실행이 차트를 데이터로 채웁니다. 새 차트를 추가 하거나 차트를 편집 하 여 여러 실행에서 기록 된 메트릭 (최소, 최대, 마지막 또는 모든 값)을 비교할 수도 있습니다. 데이터를 더 효과적으로 탐색 하기 위해 차트를 최대화할 수도 있습니다.

:::image type="content" source="media/how-to-track-experiments/experimentation-tab.gif" alt-text="Azure Machine Learning 스튜디오에서 세부 정보 실행":::

특정 실행으로 드릴다운하여 해당 출력 또는 로그를 보거나 제출한 실험의 스냅샷을 다운로드하여 다른 사용자와 실험 폴더를 공유할 수 있습니다.

### <a name="viewing-charts-in-run-details"></a>실행 세부 정보에서 차트 보기

로깅 API를 사용하여 실행하는 동안 다른 유형의 메트릭을 기록하고 이를 Azure Machine Learning Studio에서 차트로 확인하는 여러 가지 방법이 있습니다.

|기록된 값|예제 코드| 포털에서 보기|
|----|----|----|
|숫자 값의 배열 기록| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|단일 변수 꺾은선형 차트|
|반복적으로 사용되는 동일한 메트릭 이름(for 루프 내에서와 같이)을 사용하여 단일 숫자 값 기록| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 단일 변수 꺾은선형 차트|
|2개의 숫자 열을 반복적으로 사용하여 행 기록|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|두 개의 변수 꺽은선형 차트|
|두 개의 숫자 열을 사용하여 테이블 기록|`run.log_table(name='Sine Wave', value=sines)`|두 개의 변수 꺽은선형 차트|


## <a name="example-notebooks"></a>노트북 예제
이 문서의 개념을 보여 주는 노트북은 다음과 같습니다.
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

다음 단계를 수행하여 Python용 Azure Machine Learning SDK를 사용하는 방법을 알아봅니다.

* 최상의 모델을 등록하고 배포하는 방법에 대한 예제는 [Azure Machine Learning으로 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 자습서를 참조하세요.

* [Azure Machine Learning을 사용하여 PyTorch 모델을 학습하는 방법](how-to-train-pytorch.md)을 알아보세요.

---
title: 학습 실행 중의 로그 메트릭
titleSuffix: Azure Machine Learning service
description: 실험을 추적 하 고 메트릭을 모니터링 하 여 모델 생성 프로세스를 향상 시킬 수 있습니다. 학습 스크립트에 로깅을 추가 하는 방법, 실험을 제출 하는 방법, 실행 중인 작업의 진행 상황을 확인 하는 방법 및 실행의 기록 된 결과를 보는 방법에 대해 알아봅니다.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/11/2019
ms.custom: seodec18
ms.openlocfilehash: 7b14ed2c18c1106477e21062afaa4cc8f672c203
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946378"
---
# <a name="log-metrics-during-training-runs"></a>학습 실행 중의 로그 메트릭

실험 및 모니터링 메트릭을 추적 하 여 모델 생성 프로세스를 개선 합니다. 이 문서에서는 학습 스크립트에 로깅을 추가 하 고, 실험 실행을 제출 하 고, 실행을 모니터링 하 고, Azure Machine Learning 서비스에서 실행 한 결과를 확인 하는 방법에 대해 알아봅니다.

> [!NOTE]
> Azure Machine Learning 서비스는 학습 작업을 실행 하는 Docker 컨테이너 또는 AutoML과 같은 학습 중 다른 원본의 정보를 기록할 수도 있습니다. 이러한 로그는 문서화 되어 있지 않습니다. 문제가 발생 하 고 Microsoft 지원에 문의 하는 경우 문제 해결 중에 이러한 로그를 사용할 수 있습니다.

## <a name="list-of-training-metrics"></a>학습 메트릭 목록 

실험을 학습하는 동안 실행에 추가할 수 있는 메트릭은 다음과 같습니다. 실행 시 추적할 수 있는 메트릭에 대한 자세한 목록을 보려면 [Run 클래스 참조 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)를 참조하세요.

|형식| Python 함수 | 참고|
|----|:----|:----|
|스칼라 값 |함수:<br>`run.log(name, value, description='')`<br><br>예제:<br>run.log(“accuracy”, 0.95) |숫자 또는 문자열 값을 지정된 이름의 실행에 기록합니다. 메트릭을 실행에 기록하면 해당 메트릭이 실험의 실행 기록에 저장됩니다.  하나의 실행 내에서 동일한 메트릭을 여러 번 기록할 수 있으며 결과는 해당 메트릭의 벡터로 간주됩니다.|
|목록|함수:<br>`run.log_list(name, value, description='')`<br><br>예제:<br>run.log_list(“accuracies”, [0.6, 0.7, 0.87]) | 값 목록을 지정된 이름의 실행에 기록합니다.|
|행|함수:<br>`run.log_row(name, description=None, **kwargs)`<br>예제:<br>run.log_row(“Y over X”, x=1, y=0.4) | *log_row*를 사용하여 kwargs에 설명된 대로 열이 여러 개 있는 메트릭을 만듭니다. 명명된 각 매개 변수는 지정된 값이 있는 열을 생성합니다.  *log_row*는 임의의 튜플을 로깅하기 위해 한 번 호출되거나 루프에서 여러 번 호출되어 전체 테이블을 생성할 수 있습니다.|
|Table|함수:<br>`run.log_table(name, value, description='')`<br><br>예제:<br>run.log_table(“Y over X”, {”x”:[1, 2, 3], “y”:[0.6, 0.7, 0.89]}) | 사전 개체를 지정된 이름의 실행에 기록합니다. |
|이미지|함수:<br>`run.log_image(name, path=None, plot=None)`<br><br>예제:<br>`run.log_image("ROC", plt)` | 이미지를 실행 기록에 로깅합니다. log_image를 사용하여 이미지 파일이나 matplotlib 도면을 실행에 기록합니다.  이러한 이미지는 실행 기록에서 볼 수 있고 비교할 수 있습니다.|
|실행 태그 지정|함수:<br>`run.tag(key, value=None)`<br><br>예제:<br>run.tag(“selected”, “yes”) | 문자열 키와 선택적 문자열 값을 사용하여 실행에 대한 태그를 지정합니다.|
|파일 또는 디렉터리 업로드|함수:<br>`run.upload_file(name, path_or_stream)`<br> <br> 예제:<br>run.upload_file("best_model.pkl", "./model.pkl") | 파일을 실행 기록에 업로드합니다. 실행은 지정된 출력 디렉터리에서 파일을 자동으로 캡처합니다. 이 디렉터리는 대부분의 실행 형식에 대해 기본적으로 "./outputs"로 지정됩니다.  pload_file은 추가 파일을 업로드해야 하거나 출력 디렉터리를 지정하지 않은 경우에만 사용합니다. outputs 디렉터리에 업로드되도록 이름에 `outputs`를 추가하는 것이 좋습니다. `run.get_file_names()`를 호출하여 이 실행 기록와 연결된 모든 파일을 나열할 수 있습니다.|

> [!NOTE]
> 스칼라, 목록, 행 및 테이블에 대한 메트릭은 부동 소수점, 정수 또는 문자열 형식일 수 있습니다.

## <a name="start-logging-metrics"></a>로깅 메트릭 시작

실험을 추적하거나 모니터링하려면 실행을 제출할 때 로깅을 시작하는 코드를 추가해야 합니다. 실행 제출을 트리거하는 방법은 다음과 같습니다.
* __Run.start_logging__ - 학습 스크립트에 로깅 함수를 추가하고 지정된 실험에서 대화형 로깅 세션을 시작합니다. **start_logging**은 노트북과 같은 시나리오에서 사용할 대화형 실행을 만듭니다. 세션 중에 기록된 모든 메트릭이 실험의 실행 기록에 추가됩니다.
* __ScriptRunConfig__ - 학습 스크립트에 로깅 함수를 추가하고 실행을 사용하여 전체 스크립트 폴더를 로드합니다.  **ScriptRunConfig**는 스크립트 실행 구성을 설정하는 클래스입니다. 이 옵션을 사용하면 완료 알림을 받거나 모니터링할 시각적 위젯을 가져오는 모니터링 코드를 추가할 수 있습니다.

## <a name="set-up-the-workspace"></a>작업 영역 설정
로깅을 추가하고 실험을 제출하기 전에 작업 영역을 설정해야 합니다.

1. 작업 영역을 로드합니다. 작업 영역 구성을 설정 하는 방법에 대해 자세히 알아보려면 [작업 영역 구성 파일](how-to-configure-environment.md#workspace)을 참조 하세요.

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>옵션 1: start_logging 사용

**start_logging**은 노트북과 같은 시나리오에서 사용할 대화형 실행을 만듭니다. 세션 중에 기록된 모든 메트릭이 실험의 실행 기록에 추가됩니다.

다음 예제에서는 로컬 Jupyter 노트북에서 간단한 sklearn Ridge 모델을 로컬로 학습합니다. 다른 환경에 실험을 제출하는 방법에 대해 자세히 알아 보려면 [Azure Machine Learning 서비스를 사용하여 모델을 학습하기 위한 컴퓨팅 대상 설정](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets)을 참조하세요.

1. 로컬 Jupyter 노트북에 학습 스크립트를 만듭니다. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Azure Machine Learning 서비스 SDK를 사용하여 실험 추적을 추가하고 지속형 모델을 실험 실행 기록에 업로드합니다. 다음 코드는 태그를 지정하고, 기록하고, 모델 파일을 실험 실행에 업로드합니다.

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    스크립트는 ```run.complete()```로 끝나며 실행이 완료됨으로 표시됩니다.  이 함수는 일반적으로 대화형 노트북 시나리오에서 사용됩니다.

## <a name="option-2-use-scriptrunconfig"></a>옵션 2: ScriptRunConfig 사용

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 는 스크립트 실행에 대 한 구성을 설정 하기 위한 클래스입니다. 이 옵션을 사용하면 완료 알림을 받거나 모니터링할 시각적 위젯을 가져오는 모니터링 코드를 추가할 수 있습니다.

이 예제에서는 위의 기본 sklearn Ridge 모델을 확장합니다. 실험의 실행에서 메트릭 및 학습된 모델을 캡처하기 위해 모델의 알파 값을 스윕하는 간단한 매개 변수 스윕을 수행합니다. 예제는 사용자 관리 환경에 대해 로컬로 실행됩니다. 

1. 학습 스크립트 `train.py`를 만듭니다.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. `train.py` 스크립트는 Ridge 모델에서 사용할 알파 값 목록을 가져올 수 있는 `mylib.py`를 참조합니다.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. 사용자 관리 로컬 환경을 구성합니다.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. ```train.py``` 스크립트를 제출하여 사용자 관리 환경에서 실행합니다. 이 스크립트 폴더 전체는 ```mylib.py``` 파일을 포함하여 학습을 위해 제출됩니다.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>실행 관리

[학습 실행 시작, 모니터링 및 취소](how-to-manage-runs.md) 문서는 실험을 관리 하는 방법에 대 한 특정 Azure Machine Learning 워크플로를 강조 표시 합니다.

## <a name="view-run-details"></a>실행 세부 정보 보기

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Jupyter 노트북 위젯을 사용 하 여 모니터 실행
**ScriptRunConfig** 메서드를 사용 하 여 실행을 제출 하는 경우 [Jupyter 위젯을](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)사용 하 여 실행 진행률을 볼 수 있습니다. 실행 제출과 마찬가지로, 위젯은 비동기적이며 작업이 완료될 때까지 10~15초 간격으로 라이브 업데이트를 제공합니다.

1. 실행이 완료될 때까지 기다리는 동안 Jupyter 위젯을 봅니다.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Jupyter 노트북 위젯의 스크린샷](./media/how-to-track-experiments/run-details-widget.png)

작업 영역에서 동일한 표시에 대 한 링크를 가져올 수도 있습니다.

```python
print(run.get_portal_url())
```

2. **[자동화된 기계 학습 실행의 경우]** 이전 실행에서 차트에 액세스합니다. 를 `<<experiment_name>>` 적절 한 실험 이름으로 바꿉니다.

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![자동화된 기계 학습을 위한 Jupyter Notebook 위젯](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


파이프라인의 세부 정보를 보려면 테이블에서 살펴보려는 파이프라인을 클릭합니다. Azure Portal의 팝업 항목에 차트가 렌더링됩니다.

### <a name="get-log-results-upon-completion"></a>완료 시 로그 결과 가져오기

모델 학습 및 모니터링은 백그라운드에서 수행되므로 기다리는 동안 다른 작업을 실행할 수 있습니다. 또한 모델에서 학습을 완료할 때까지 기다린 후에 더 많은 코드를 실행할 수도 있습니다. **ScriptRunConfig**를 사용하면 ```run.wait_for_completion(show_output = True)```를 사용하여 모델 학습이 완료된 시점을 표시할 수 있습니다. ```show_output``` 플래그는 자세한 정보를 출력합니다. 


### <a name="query-run-metrics"></a>실행 메트릭 쿼리

```run.get_metrics()```를 사용하여 학습된 모델의 메트릭을 볼 수 있습니다. 이제 위의 예제에 기록된 모든 메트릭을 가져와 최상의 모델을 결정할 수 있습니다.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>Azure Portal에서 실험 보기

실험에서 실행이 완료되면 기록된 실험 실행 기록을 찾아볼 수 있습니다. 다음 두 가지 방법으로 기록에 액세스할 수 있습니다.

* URL을 실행으로 직접 가져옵니다(```print(run.get_portal_url())```).
* 실행 이름(여기서는 ```run```)을 제출하여 실행 세부 정보를 봅니다. 이렇게 하면 실험 이름, ID, 유형, 상태, 세부 정보 페이지, Azure Portal에 대한 링크 및 문서에 대한 링크가 표시됩니다.

실행 링크를 클릭하면 Azure Portal의 실행 세부 정보 페이지로 바로 이동합니다. 여기서는 실험에 기록된 모든 속성, 추적된 메트릭, 이미지 및 차트를 볼 수 있습니다. 이 경우 MSE와 알파 값을 기록했습니다.

  ![Azure Portal의 실행 세부 정보](./media/how-to-track-experiments/run-details-page.png)

또한 실행에 대한 모든 출력 또는 로그를 보거나 제출한 실험의 스냅샷을 다운로드하여 실험 폴더를 다른 사용자와 공유할 수도 있습니다.

### <a name="viewing-charts-in-run-details"></a>실행 세부 정보에서 차트 보기

로깅 API를 사용하여 실행하는 동안 다른 유형의 메트릭을 기록하고 이를 Azure Portal에서 차트로 확인하는 여러 가지 방법이 있습니다. 

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
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

다음 단계를 수행하여 Python용 Azure Machine Learning SDK를 사용하는 방법을 알아봅니다.

* 최상의 모델을 등록하고 배포하는 방법에 대한 예제는 [Azure Machine Learning으로 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 자습서를 참조하세요.

* [Azure Machine Learning을 사용하여 PyTorch 모델을 학습하는 방법](how-to-train-pytorch.md)을 알아보세요.

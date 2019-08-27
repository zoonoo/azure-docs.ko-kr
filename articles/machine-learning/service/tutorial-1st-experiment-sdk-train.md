---
title: '자습서: 첫 번째 ML 모델 학습'
titleSuffix: Azure Machine Learning service
description: 이 자습서에서는 Azure Machine Learning Service의 기본 디자인 패턴을 알아보고, 당뇨병 데이터 세트를 기반으로 하는 간단한 scikit-learn 모델을 학습시킵니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: 17fee1f01bf883aa2a9845fe4f2817fb806056dd
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516233"
---
# <a name="tutorial-train-your-first-ml-model"></a>자습서: 첫 번째 ML 모델 학습

이 자습서는 **2부로 구성된 자습서 시리즈 중 제2부**입니다. 이전 자습서에서는 [작업 영역을 만들고 개발 환경을 선택](tutorial-1st-experiment-sdk-setup.md)했습니다. 이 자습서에서는 Azure Machine Learning Service의 기본 디자인 패턴을 알아보고, 당뇨병 데이터 세트를 기반으로 하는 간단한 scikit-learn 모델을 학습시킵니다. 이 자습서가 완료되면 SDK에 대한 실용적인 지식을 습득하여 더 복잡한 실험과 워크플로를 개발하도록 크기 조정할 수 있습니다.

이 자습서에서는 다음 작업에 대해 알아봅니다.

> [!div class="checklist"]
> * 작업 영역 연결 및 실험 만들기
> * 데이터 로드 및 scikit-learn 모델 학습
> * 포털에서 학습 결과 보기
> * 최적 모델 검색

## <a name="prerequisites"></a>필수 조건

유일한 필수 조건은 이전의 [환경 및 작업 영역 설정](tutorial-1st-experiment-sdk-setup.md) 자습서를 실행하는 것입니다.

## <a name="connect-workspace-and-create-experiment"></a>작업 영역 연결 및 실험 만들기

`Workspace` 클래스를 가져오고, `from_config().` 함수를 사용하여 `config.json` 파일에서 구독 정보를 로드합니다. 이 함수는 기본적으로 현재 디렉터리에서 JSON 파일을 찾지만, `from_config(path="your/file/path")`를 사용하여 파일을 가리키는 경로 매개 변수를 지정할 수도 있습니다. 작업 영역의 클라우드 Notebook 서버에서 이 Notebook을 실행하는 경우 파일은 자동으로 루트 디렉터리에 있습니다.

다음 코드에서 추가 인증을 요청하는 경우 링크를 브라우저에 붙여넣고 인증 토큰을 입력하기만 하면 됩니다.


```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

이제 실험을 작업 영역에 만듭니다. 실험은 시험(개별 모델 실행)의 컬렉션을 나타내는 또 다른 기반 클라우드 리소스입니다. 이 자습서에서는 Azure Portal에서 실험을 사용하여 실행을 만들고 모델 학습을 추적할 수 있습니다. 매개 변수에는 작업 영역 참조와 실험에 대한 문자열 이름이 포함됩니다.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>데이터 로드 및 학습 준비

이 자습서에서는 scikit-learn에 포함되어 미리 정규화된 데이터 세트인 당뇨병 데이터 세트를 사용합니다. 이 데이터 세트는 입니다. 이 데이터 세트는 나이, 성별, BMI와 같은 기능을 사용하여 당뇨병의 진행을 예측합니다. `load_diabetes()` 정적 함수에서 데이터를 로드하고, `train_test_split()`을 사용하여 학습 및 테스트 세트로 분할합니다. 이 함수는 학습 후 테스트에 사용할 보이지 않는 데이터를 제공하기 위해 데이터를 분리합니다.


```python
from sklearn.datasets import load_diabetes
from sklearn.model_selection import train_test_split

X, y = load_diabetes(return_X_y = True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>모델 학습

간단한 scikit-learn 모델 학습은 소규모 학습을 위해 로컬로 쉽게 수행할 수 있지만 수십 가지의 다양한 기능 순열과 하이퍼 매개 변수 설정을 사용하여 여러 반복을 학습하는 경우 학습시킨 모델과 학습 방법에 대한 추적을 쉽게 놓칠 수 있습니다. 다음 디자인 패턴에서는 SDK를 활용하여 클라우드에서 학습을 쉽게 추적하는 방법을 보여 줍니다.

다양한 하이퍼 매개 변수 알파 값을 통해 루프에서 Ridge 모델을 학습시키는 스크립트를 작성합니다.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

위의 코드에서 수행하는 작업은 다음과 같습니다.

1. `alphas` 배열의 각 알파 하이퍼 매개 변수 값에 대한 새 실행이 실험 내에 만들어집니다. 알파 값이 각 실행을 구분하기 위해 기록됩니다.
1. 각 실행에서 Ridge 모델은 인스턴스화되고 학습되며 예측을 실행하는 데 사용됩니다. 평균 제곱근 오차(RMSE, root-mean-squared-error)가 실제 값 및 예측 값에 대해 계산된 다음, 실행에 기록됩니다. 이 시점에서 실행에는 알파 값과 RMSE 정확도 모두에 연결된 메타데이터가 있습니다.
1. 다음으로 각 실행에 대한 모델이 직렬화되어 해당 실행에 업로드됩니다. 이렇게 하면 포털의 실행에서 모델 파일을 다운로드할 수 있습니다.
1. 각 반복이 끝날 때마다 `run.complete()`을 호출하여 실행이 완료됩니다.

학습이 완료되면 `experiment` 변수를 호출하여 포털의 실험에 대한 링크를 가져옵니다.

```python
experiment
```

<table style="width:100%"><tr><th>Name</th><th>작업 영역</th><th>보고서 페이지</th><th>문서 페이지</th></tr><tr><td>diabetes-experiment</td><td>your-workspace-name</td><td>Azure Portal에 연결</td><td>설명서 링크</td></tr></table>

## <a name="view-training-results-in-portal"></a>포털에서 학습 결과 보기

**Azure Portal에 연결**을 따르면 기본 실험 페이지로 이동합니다. 여기에는 실험의 개별 실행이 모두 표시됩니다. 사용자 지정 로그 값(이 경우 `alpha_value` 및 `rmse`)은 각 실행에 대한 필드가 되며, 실험 페이지 위쪽의 차트 및 타일에서도 사용할 수 있습니다. 로그 메트릭을 차트 또는 타일에 추가하려면 마우스로 해당 차트 또는 타일 위를 가리키고 편집 단추를 클릭하고 사용자 지정 로그 메트릭을 찾습니다.

모델이 수백 수천 개의 개별 실행을 통해 대규모로 학습되는 경우 이 페이지에서 학습시킨 모든 모델, 특히 학습시킨 방법 및 시간이 지남에 따라 고유 메트릭이 변경되는 상황을 쉽게 확인할 수 있습니다.

![포털의 기본 실험 페이지](./media/tutorial-quickstart/experiment-main.png)

`RUN NUMBER` 열의 실행 번호 링크를 클릭하면 각 개별 실행에 대한 페이지로 이동합니다. 기본 **세부 정보** 탭에는 각 실행에 대한 자세한 정보가 표시됩니다. **출력** 탭으로 이동합니다. 그러면 각 학습 반복 중에 실행에 업로드된 모델에 대한 `.pkl` 파일이 표시됩니다. 여기서는 모델 파일을 수동으로 다시 학습시키지 않고 다운로드할 수 있습니다.

![포털의 실행 세부 정보 페이지](./media/tutorial-quickstart/model-download.png)

## <a name="get-the-best-model"></a>최적 모델 가져오기

모델 파일은 포털의 실험에서 다운로드할 수 있을 뿐만 아니라 프로그래밍 방식으로도 다운로드할 수 있습니다. 다음 코드에서는 실험의 각 실행을 반복하고 기록된 실행 메트릭과 실행 세부 정보(run_id 포함) 모두에 액세스합니다. 그러면 최상의 실행(이 경우 평균 제곱근 오차가 가장 낮은 실행)을 추적합니다.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

최상의 실행 ID를 사용하여 실험 개체와 함께 `Run` 생성자를 사용하여 개별 실행을 가져옵니다. 그런 다음 `get_file_names()`를 호출하여 이 실행에서 다운로드할 수 있는 모든 파일을 표시합니다. 이 경우 학습 중에 각 실행에 대해 하나의 파일만 업로드했습니다.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

실행 개체에서 `download()`를 호출하여 다운로드할 모델 파일 이름을 지정합니다. 기본적으로 이 함수는 현재 디렉터리에 다운로드합니다.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure Machine Learning Service 자습서를 실행하려면 이 섹션을 완료하지 마세요.

### <a name="stop-the-notebook-vm"></a>Notebook VM 중지

클라우드 Notebook 서버를 사용한 경우 비용을 줄이기 위해 VM을 사용하지 않을 때는 해당 VM을 중지합니다.

1. 작업 영역에서 **Notebook VM**을 선택합니다.

   ![VM 서버 중지](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. 목록에서 VM을 선택합니다.

1. **중지**를 선택합니다.

1. 서버를 다시 사용할 준비가 되면 **시작**을 선택합니다.

### <a name="delete-everything"></a>모든 항목 삭제

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

또한 리소스 그룹을 유지하면서 단일 작업 영역을 삭제할 수도 있습니다. 작업 영역 속성을 표시하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * 작업 영역 연결 및 실험 만들기
> * 데이터 로드 및 scikit-learn 모델 학습
> * 포털 및 검색된 모델에서 학습 결과 보기

Azure Machine Learning을 사용하여 [모델을 배포합니다](tutorial-deploy-models-with-aml.md).
[자동화된 기계 학습](tutorial-auto-train-models.md) 실험을 개발하는 하는 방법을 알아봅니다.

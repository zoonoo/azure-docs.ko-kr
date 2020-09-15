---
title: '자습서: Python에서 첫 번째 Azure ML 모델 학습'
titleSuffix: Azure Machine Learning
description: 이 자습서에서는 Azure Machine Learning의 기본 디자인 패턴을 알아보고, 당뇨병 데이터 세트를 기반으로 하는 간단한 scikit-learn 모델을 학습합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.date: 08/25/2020
ms.custom: devx-track-python
ms.openlocfilehash: 7052617eb83dbd07c2d6938dcbb7a38ba19f3aad
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536232"
---
# <a name="tutorial-train-your-first-ml-model"></a>자습서: 첫 번째 ML 모델 학습

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 자습서는 **2부로 구성된 자습서 시리즈 중 제2부**입니다. 이전 자습서에서는 [작업 영역을 만들고 개발 환경을 선택](tutorial-1st-experiment-sdk-setup.md)했습니다. 이 자습서에서는 Azure Machine Learning의 기본 디자인 패턴을 알아보고, 당뇨병 데이터 세트를 기반으로 하는 간단한 scikit-learn 모델을 학습합니다. 이 자습서가 완료되면 SDK에 대한 실용적인 지식을 습득하여 더 복잡한 실험과 워크플로를 개발하도록 크기 조정할 수 있습니다.

이 자습서에서는 다음 작업에 대해 알아봅니다.

> [!div class="checklist"]
> * 작업 영역 연결 및 실험 만들기
> * 데이터 로드 및 scikit-learn 모델 학습
> * 스튜디오에서 학습 결과 보기
> * 최적 모델 검색

## <a name="prerequisites"></a>필수 구성 요소

유일한 필수 조건은 이 자습서 중 하나인 [환경 및 작업 영역 설정](tutorial-1st-experiment-sdk-setup.md)을 실행하는 것입니다.

자습서의 이 부분에서는 1부 끝에서 열린 샘플 Jupyter Notebook *tutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb*에서 코드를 실행합니다. 이 문서에서는 Notebook에 있는 동일한 코드를 안내합니다.

## <a name="open-the-notebook"></a>Notebook 열기

1. [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인합니다.

1. [1부](tutorial-1st-experiment-sdk-setup.md#open)에 표시된 대로 폴더에서 **tutorial-1st-experiment-sdk-train.ipynb**를 엽니다.

Jupyter 인터페이스에서 *새로운* Notebook을 만들지 **마세요**. Notebook *tutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb*에는 이 자습서에 **필요한 모든 코드와 데이터**가 포함되어 있습니다.

## <a name="connect-workspace-and-create-experiment"></a>작업 영역 연결 및 실험 만들기

<!-- nbstart https://raw.githubusercontent.com/Azure/MachineLearningNotebooks/master/tutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb -->

> [!TIP]
> _tutorial-1st-experiment-sdk-train.ipynb_의 콘텐츠. 코드를 실행할 때 함께 읽도록 하려면 지금 Jupyter Notebook으로 전환합니다. Notebook에서 단일 코드 셀을 실행하려면 코드 셀을 클릭하고 **Shift+Enter** 키를 누릅니다. 또는 상단 도구 모음에서 **모두 실행**을 선택하여 전체 Notebook을 실행합니다.


`Workspace` 클래스를 가져오고 `from_config().` 함수를 사용하여 `config.json` 파일에서 구독 정보를 로드합니다. 이 함수는 기본적으로 현재 디렉터리에서 JSON 파일을 찾지만 `from_config(path="your/file/path")`를 사용하여 파일을 가리키는 경로 매개 변수를 지정할 수도 있습니다. 작업 영역의 클라우드 Notebook 서버에서 이 Notebook을 실행하는 경우 파일은 자동으로 루트 디렉터리에 있습니다.

다음 코드에서 추가 인증을 요청하는 경우 링크를 브라우저에 붙여넣고 인증 토큰을 입력하기만 하면 됩니다. 또한 사용자에게 연결된 테넌트가 둘 이상 있는 경우 다음 줄을 추가해야 합니다.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

인증에 대한 자세한 내용은 [Azure Machine Learning의 인증](https://aka.ms/aml-notebook-auth)을 참조하세요.


```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

이제 실험을 작업 영역에 만듭니다. 실험은 시험(개별 모델 실행)의 컬렉션을 나타내는 또 다른 기반 클라우드 리소스입니다. 이 자습서에서는 Azure Machine Learning Studio에서 실험을 사용하여 실행을 만들고 모델 학습을 추적할 수 있습니다. 매개 변수에는 작업 영역 참조와 실험에 대한 문자열 이름이 포함됩니다.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>데이터 로드 및 학습 준비

이 자습서에서는 나이와 성별, BMI 같은 특징을 사용하는 당뇨병 데이터 세트를 통해 당뇨병의 진행을 예측합니다. [Azure Open Dataset](https://azure.microsoft.com/services/open-datasets/) 클래스에서 데이터를 로드하고 `train_test_split()`을 사용하여 학습 및 테스트 세트로 분할합니다. 이 함수는 모델이 학습 후 테스트에 사용할 보지 않은 데이터를 제공하기 위해 데이터를 분리합니다.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
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
1. 다음으로 각 실행에 대한 모델이 직렬화되어 해당 실행에 업로드됩니다. 이렇게 하면 스튜디오의 실행에서 모델 파일을 다운로드할 수 있습니다.
1. 각 반복이 끝날 때마다 `run.complete()`을 호출하여 실행이 완료됩니다.

학습이 완료되면 `experiment` 변수를 호출하여 스튜디오의 실험에 대한 링크를 가져옵니다.

```python
experiment
```

<table style="width:100%"><tr><th>Name</th><th>작업 영역</th><th>보고서 페이지</th><th>문서 페이지</th></tr><tr><td>diabetes-experiment</td><td>your-workspace-name</td><td>Azure Machine Learning Studio에 연결</td><td>설명서 링크</td></tr></table>

## <a name="view-training-results-in-studio"></a>스튜디오에서 학습 결과 보기

**Azure Machine Learning Studio에 연결**에 따라 기본 실험 페이지로 이동합니다. 여기에는 실험의 개별 실행이 모두 표시됩니다. 사용자 지정 로그 값(이 경우 `alpha_value` 및 `rmse`)은 각 실행에 대한 필드가 되며 차트에서도 사용할 수 있게 됩니다. 기록된 메트릭을 사용하여 새 차트를 그리려면 '차트 추가'를 클릭하고 그리려는 메트릭을 선택합니다.

모델이 수백 수천 개의 개별 실행을 통해 대규모로 학습되는 경우 이 페이지에서 학습시킨 모든 모델, 특히 학습시킨 방법 및 시간이 지남에 따라 고유 메트릭이 변경되는 상황을 쉽게 확인할 수 있습니다.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/experiment-main.png" alt-text="스튜디오의 기본 실험 페이지.":::


`RUN NUMBER` 열에서 실행 번호 링크를 선택하여 개별 실행에 대한 페이지를 표시합니다. 기본 **세부 정보** 탭에는 각 실행에 대한 자세한 정보가 표시됩니다. **출력 + 로그** 탭으로 이동합니다. 그러면 각 학습 반복 중에 실행에 업로드된 모델에 대한 `.pkl` 파일이 표시됩니다. 여기서는 모델 파일을 수동으로 다시 학습시키지 않고 다운로드할 수 있습니다.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/model-download.png" alt-text="스튜디오에서 세부 정보 페이지를 실행합니다.":::

## <a name="get-the-best-model"></a>최적 모델 가져오기

모델 파일은 스튜디오의 실험에서 다운로드할 수 있을 뿐만 아니라 프로그래밍 방식으로도 다운로드할 수 있습니다. 다음 코드에서는 실험의 각 실행을 반복하고 기록된 실행 메트릭과 실행 세부 정보(run_id 포함) 모두에 액세스합니다. 그러면 최상의 실행(이 경우 평균 제곱근 오차가 가장 낮은 실행)을 추적합니다.

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
```output
Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
Best run_id rmse: 57.234760283951765
```

최상의 실행 ID를 사용하여 실험 개체와 함께 `Run` 생성자를 사용하여 개별 실행을 가져옵니다. 그런 다음 `get_file_names()`를 호출하여 이 실행에서 다운로드할 수 있는 모든 파일을 표시합니다. 이 경우 학습 중에 각 실행에 대해 하나의 파일만 업로드했습니다.


```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

```output
['model_alpha_0.1.pkl']
```

실행 개체에서 `download()`를 호출하여 다운로드할 모델 파일 이름을 지정합니다. 기본적으로 이 함수는 현재 디렉터리에 다운로드합니다.


```python
best_run.download_file(name="model_alpha_0.1.pkl")
```
<!-- nbend -->

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure Machine Learning 자습서를 실행하려면 이 섹션을 완료하지 마세요.

### <a name="stop-the-compute-instance"></a>컴퓨팅 인스턴스 중지

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>모든 항목 삭제

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

또한 리소스 그룹을 유지하면서 단일 작업 영역을 삭제할 수도 있습니다. 작업 영역 속성을 표시하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * 작업 영역 연결 및 실험 만들기
> * 데이터 로드 및 scikit-learn 모델 학습
> * 스튜디오 및 검색된 모델에서 학습 결과 보기

Azure Machine Learning을 사용하여 [모델을 배포합니다](tutorial-deploy-models-with-aml.md).
[자동화된 기계 학습](tutorial-auto-train-models.md) 실험을 개발하는 하는 방법을 알아봅니다.

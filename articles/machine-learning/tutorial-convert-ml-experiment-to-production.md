---
title: 기계 학습 실험 코드를 프로덕션 코드로 변환
titleSuffix: Azure Machine Learning
description: MLOpsPython 코드 템플릿을 사용하여 기계 학습 실험 코드를 프로덕션 코드로 변환하는 방법을 알아봅니다.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: f40c2b5f7134458b3f8cb492652bebf14388634c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477139"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>자습서: ML 실험 코드를 프로덕션 코드로 변환

기계 학습 프로젝트에는 실제 데이터 세트를 사용하여 Jupyter Notebook과 같은 Agile 도구로 가설을 테스트하는 실험이 필요합니다. 모델을 프로덕션에 사용할 준비가 되면, 모델 코드를 프로덕션 코드 리포지토리에 배치해야 합니다. 프로덕션 코드 리포지토리에 배치할 모델 코드를 Python 스크립트로 변환해야 하는 경우가 있습니다. 이 자습서에서는 실험 코드를 Python 스크립트로 내보내는 방법에 권장되는 방식을 설명합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 불필요한 코드 정리
> * Jupyter Notebook 코드를 함수로 리팩터링
> * 관련 작업을 위한 Python 스크립트 만들기
> * 단위 테스트 만들기

## <a name="prerequisites"></a>사전 요구 사항

- [MLOpsPython 템플릿](https://github.com/microsoft/MLOpsPython/generate)을 생성하고 `experimentation/Diabetes Ridge Regression Training.ipynb` 및 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 노트북을 사용합니다. 이러한 노트북은 실험에서 프로덕션으로 변환하는 예로 사용 됩니다. 이러한 Notebooks는 [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation)에서 찾을 수 있습니다.
- nbconvert를 설치합니다. [Installation](https://nbconvert.readthedocs.io/en/latest/install.html)(설치) 페이지의 __Installing nbconvert__(nbconvert 설치) 섹션에 있는 설치 지침을 따릅니다.

## <a name="remove-all-nonessential-code"></a>불필요한 코드 모두 제거

실험 중에 작성된 일부 코드는 탐색 목적으로만 사용됩니다. 따라서 실험 코드를 프로덕션 코드로 변환하는 첫 번째 단계는 이런 불필요한 코드를 제거하는 것입니다. 불필요한 코드를 제거하면 코드를 더 쉽게 유지 관리할 수 있습니다. 이 섹션에서는 `experimentation/Diabetes Ridge Regression Training.ipynb` 노트북에서 코드를 제거합니다. `X`와 `y`의 모양을 인쇄하는 명령문과 `features.describe`를 호출하는 셀은 데이터 탐색만을 위한 코드이며 제거될 수 있습니다. 불필요한 코드를 제거하고 나면 `experimentation/Diabetes Ridge Regression Training.ipynb`는 Markdown이 없는 다음 코드처럼 표시됩니다.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib

X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>코드를 함수로 리팩터링

둘째, Jupyter 코드를 함수로 리팩터링해야 합니다. 코드를 함수로 리팩터링하면 단위 테스트가 쉬워지고 코드 유지 관리가 더 수월해집니다 이 섹션에서는 다음을 리팩터링합니다.

- 당뇨병 릿지 회귀 학습 노트북(`experimentation/Diabetes Ridge Regression Training.ipynb`)
- 당뇨병 릿지 회귀 채점 노트북(`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>당뇨병 릿지 회귀 학습 노트북을 함수로 리팩터링

`experimentation/Diabetes Ridge Regression Training.ipynb`에서 다음 단계를 완료합니다.

1. `train_model`이라는 함수를 만듭니다. 이 함수는 매개 변수 `data`와 `alpha`를 사용하고 모델을 반환합니다.
1. "학습 집합의 모델 학습" 및 "유효성 검사 집합의 모델 유효성 검사" 제목 아래에 있는 코드를 `train_model` 함수에 복사합니다.

`train_model` 함수는 다음 코드와 같은 모양입니다.

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

`train_model` 함수가 만들어지면, "학습 집합의 모델 학습" 및 "유효성 검사 집합의 모델 유효성 검사" 제목 아래에 있는 코드를 다음 명령문으로 바꿉니다.

```python
reg = train_model(data, alpha)
```

이전 명령문은 `data` 및 `alpha` 매개 변수를 전달하는 `train_model` 함수를 호출하고 모델을 반환합니다.

`experimentation/Diabetes Ridge Regression Training.ipynb`에서 다음 단계를 완료합니다.

1. `main`이라는 함수를 새로 만듭니다. 이 함수는 매개 변수를 사용하지 않고 아무것도 반환하지 않습니다.
1. "데이터 로드", "데이터 학습 및 유효성 검사 집합으로 분할", "모델 저장" 제목 아래에 있는 코드를 `main` 함수에 복사합니다.
1. `train_model`에 대해 새로 만든 호출을 `main` 함수에 복사합니다.

`main` 함수는 다음 코드와 같은 모양입니다.

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

`main` 함수가 만들어지면 "데이터 로드", "데이터 학습 및 유효성 검사 집합으로 분할", "모델 저장" 제목 아래에 있는 모든 코드를 `train_model`에 대해 새로 만든 호출과 함께 다음 명령문으로 바꿉니다.

```python
main()
```

리팩터링 후 `experimentation/Diabetes Ridge Regression Training.ipynb`는 Markdown 없이 다음 코드처럼 표시됩니다.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>당뇨병 릿지 회귀 채점 노트북을 함수로 리팩터링

`experimentation/Diabetes Ridge Regression Scoring.ipynb`에서 다음 단계를 완료합니다.

1. `init`라는 함수를 새로 만듭니다. 이 함수는 매개 변수를 사용하지 않고 아무것도 반환하지 않습니다.
1. "모델 로드" 제목 아래에 있는 코드를 `init` 함수에 복사합니다.

`init` 함수는 다음 코드와 같은 모양입니다.

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

`init` 함수가 만들어지면, "모델 로드" 제목 아래에 있는 모든 코드를 다음과 같이 `init`에 대한 단일 호출로 바꿉니다.

```python
init()
```

`experimentation/Diabetes Ridge Regression Scoring.ipynb`에서 다음 단계를 완료합니다.

1. `run`이라는 함수를 새로 만듭니다. 이 함수는 매개 변수 `raw_data` 및 `request_headers`를 사용하고 다음과 같이 결과 사전을 반환합니다.

    ```python
    {"result": result.tolist()}
    ```

1. "데이터 준비" 및 "데이터 채점" 제목 아래에 있는 코드를 `run` 함수에 복사합니다.

    `run` 함수는 다음 코드와 같은 모양입니다. (`raw_data` 및 `request_headers` 변수를 설정하는 명령문을 제거해야 합니다. 이것은 `run` 함수가 호출될 때 사용됩니다.)

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

`run` 함수가 만들어지면 "데이터 준비" 및 "데이터 채점" 제목 아래에 있는 모든 코드를 다음 코드로 바꿉니다.

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

이전 코드는 `raw_data` 및 `request_header` 변수를 설정하고 `raw_data` 및 `request_header`로 `run` 함수를 호출하고 예측을 출력합니다.

리팩터링 후 `experimentation/Diabetes Ridge Regression Scoring.ipynb`는 Markdown 없이 다음 코드처럼 표시됩니다.

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Python 파일에서 관련 함수 결합

셋째, 코드 재사용이 수월하도록 관련 함수를 Python 파일에 병합해야 합니다. 이 섹션에서는 다음 노트북을 위한 Python 파일을 만듭니다.

- 당뇨병 릿지 회귀 학습 노트북(`experimentation/Diabetes Ridge Regression Training.ipynb`)
- 당뇨병 릿지 회귀 채점 노트북(`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>당뇨병 릿지 회귀 학습 노트북용 Python 파일 만들기

명령 프롬프트에서 다음 명령문을 실행하여 노트북을 실행 가능한 스크립트로 변환합니다. 여기에는 nbconvert 패키지와 `experimentation/Diabetes Ridge Regression Training.ipynb` 경로가 사용됩니다.

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

노트북이 `train.py`로 변환되면, 모든 주석을 제거합니다. `train.py` 파일은 다음 코드와 같은 모양입니다.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

MLOpsPython 리포지토리의 `diabetes_regression/training` 디렉터리에 있는 `train.py` 파일은 명령줄 인수(즉, `build_id`, `model_name`, `alpha`)를 지원합니다. 명령줄 인수에 대한 지원을 `train.py` 파일에 추가하여 동적 모델 이름과 `alpha` 값을 지원할 수 있지만, 코드가 성공적으로 실행될 필요는 없습니다.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>당뇨병 릿지 회귀 채점 노트북용 Python 파일 만들기

nbconvert 패키지와 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 경로가 사용되는 명령 프롬프트에서 다음 명령문을 실행하여 노트북을 실행 가능한 스크립트로 변환합니다.

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

노트북이 `score.py`로 변환되면, 모든 주석을 제거합니다. `score.py` 파일은 다음 코드와 같은 모양입니다.

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

전역 변수 모델을 인스턴스화하도록 `train_model` 함수를 수정해야 합니다. 그래야 스크립트 전체에 표시됩니다. 다음 명령문을 `init` 함수의 시작 부분에 추가합니다.

```python
global model
```

이전 명령문을 추가하면 `init` 함수는 다음 코드와 같은 모양입니다.

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>각 Python 파일에 대한 단위 테스트 만들기

넷째, 각 Python 파일에 대한 단위 테스트를 만들어야 합니다. 그러면 코드가 더 탄탄해지고 유지 관리가 더 쉬워집니다. 이 섹션에서는 `train.py`의 함수 중 하나에 대한 단위 테스트를 만듭니다.

`train.py`에는 `train_model` 및 `main`이라는 두 가지 함수가 포함되어 있습니다. 각 함수에는 단위 테스트가 필요하지만 이 자습서에서는 Pytest 프레임워크를 사용하여 `train_model` 함수에 대한 단위 테스트를 하나만 만듭니다. Pytest가 유일한 Python 단위 테스트 프레임워크는 아니지만 가장 자주 사용되는 것 중 하나입니다. 자세한 내용은 [Pytest](https://pytest.org)를 참조하세요.

단위 테스트는 일반적으로 다음과 같은 세가지 주요 동작을 포함합니다.

- 개체 정렬 - 필요한 개체 만들기 및 설정
- 개체에 대한 동작
- 예상되는 사항 어설션

`train_model`에 대한 일반적인 조건은 `data`와 `alpha` 값이 전달되는 경우입니다. 예상 결과는 `Ridge.train` 및 `Ridge.predict` 함수가 호출되는 것입니다. 기계 학습 교육 메서드는 빠르게 실행되지 않는 경우가 많기 때문에 `Ridge.train`에 대한 호출이 모의됩니다. `Ridge.train`의 반환 값이 모의 개체이기 때문에 `Ridge.predict`도 모의됩니다. `data` 및 `alpha` 값 전달 여부를 테스트하는 `train_model` 단위 테스트(모의 및 Pytest 프레임워크로 호출되는 `Ridge.train` 및 `Ridge.predict` 함수의 예상 결과 값은 다음 코드 형태를 갖춰야 함)

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch('Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>MLOpsPython 코드 템플릿으로 자체 모델 사용

이 가이드의 단계를 수행한 경우 MLOpsPython 리포지토리에 제공되는 학습/채점/테스트 스크립트와 상호 관련된 스크립트 세트를 갖게 됩니다.  위에 언급된 구조에 따라, 다음 단계에서는 사용자의 기계 학습 프로젝트에 이러한 파일을 사용하는 데 필요한 사항을 안내합니다.

1. MLOpsPython [시작](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) 가이드를 따릅니다.
2. MLOpsPython [부트스트랩 지침](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md)에 따라 프로젝트 시작 지점을 만듭니다.
3. 학습 코드 바꾸기
4. 채점 코드 바꾸기
5. 평가 코드 업데이트

### <a name="follow-the-getting-started-guide"></a>시작 가이드 수행하기
[시작](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) 가이드를 수행하는 단계는 MLOpsPython을 실행하기 위한 지원 인프라와 파이프라인을 갖추는 데 필요합니다.

### <a name="follow-the-bootstrap-instructions"></a>Bootstrap 지침을 따릅니다.

[MLOpsPython 리포지토리에서 부트스트랩](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) 가이드는 프로젝트에 대한 리포지토리를 신속하게 준비하는 데 도움이 됩니다.

**참고:** 부트스트랩 스크립트는 사용자가 선택한 프로젝트 이름으로 diabetes_regression 폴더의 이름을 변경하므로 경로가 관련될 때 프로젝트를 `[project name]`으로 참조합니다.

### <a name="replace-training-code"></a>학습 코드 바꾸기

솔루션이 사용자의 코드로 작동하려면 모델 학습에 사용된 코드를 바꾸고 해당 단위 테스트를 제거하거나 바꿔야 합니다. 특히 다음 단계를 수행합니다.

1. `[project name]/training/train.py`를 바꿉니다. 이 스크립트는 로컬 또는 Azure ML 컴퓨팅에서 모델을 학습시킵니다.
1. `[project name]/training/test_train.py`에서 찾은 학습 단위 테스트를 제거하거나 바꿉니다.

### <a name="replace-score-code"></a>채점 코드 바꾸기

모델이 실시간 유추 기능을 제공하려면 채점 코드를 바꿔야 합니다. MLOpsPython 템플릿은 채점 코드를 사용하여 ACI, AKS 또는 웹앱에서 실시간 채점을 수행하는 모델을 배포합니다. 채점을 유지하려면 `[project name]/scoring/score.py`를 바꿉니다.

### <a name="update-evaluation-code"></a>평가 코드 업데이트

MLOpsPython 템플릿은 evaluate_model 스크립트를 사용하여 평균 제곱 오차를 기반으로 새로 학습된 모델과 현재 프로덕션 모델의 성능을 비교합니다. 새로 학습된 모델의 성능이 현재 프로덕션 모델보다 좋으면 파이프라인이 계속됩니다. 그렇지 않으면 파이프라인이 취소됩니다. 평가를 유지하려면 `[project name]/evaluate/evaluate_model.py`의 모든 `mse` 인스턴스를 원하는 메트릭으로 바꿉니다.

평가를 제거하려면 `.pipelines/[project name]-variables-template.yml`에서 DevOps 파이프라인 변수 `RUN_EVALUATION`을 `false`로 설정합니다.

## <a name="next-steps"></a>다음 단계

실험 코드에서 프로덕션 코드로 변환하는 방법을 이해했으면, 다음 링크를 사용하여 웹 서비스로 배포된 실험 실행 및 모델을 모니터링하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Azure ML 실험 실행 및 메트릭 모니터링](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [ML 웹 서비스 엔드포인트의 데이터 모니터링 및 수집](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)

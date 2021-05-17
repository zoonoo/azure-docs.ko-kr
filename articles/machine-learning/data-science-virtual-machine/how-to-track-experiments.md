---
title: 실험 추적 및 배포 모델
titleSuffix: Azure Data Science Virtual Machine
description: Azure Machine Learning 또는 MLFlow를 사용하여 Data Science Virtual Machine에서 실험을 추적하고 기록하는 방법을 알아봅니다.
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 3795d531c5c4c543587ab817c05cd1cfeea6be06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518541"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>Azure Machine Learning에서 실험 추적 및 모델 배포

실험을 추적하고 실행 메트릭을 모니터링하여 모델 생성 프로세스를 개선합니다. 이 문서에서는 [MLflow](https://mlflow.org/) API를 사용하여 학습 스크립트에 로깅 코드를 추가하고 Azure Machine Learning에서 실험을 추적하는 방법을 알아봅니다.

다음 다이어그램에서는 MLflow 추적을 사용하여 Azure Machine Learning 작업 영역에 실험의 실행 메트릭과 모델 아티팩트를 저장하는 방법을 보여 줍니다.

![실험 추적](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>필수 조건

* [Azure Machine Learning 작업 영역을 프로비전](../how-to-manage-workspace.md#create-a-workspace)해야 합니다.

## <a name="create-a-new-notebook"></a>새 Notebook 만들기

Azure Machine Learning 및 MLFlow SDK는 Data Science VM에 미리 설치되며, **azureml_py36_ \*** conda 환경에서 액세스할 수 있습니다. JupyterLab에서 시작 관리자를 클릭하고 다음 커널을 선택합니다.

![커널 선택](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>작업 영역 설정

[Azure Portal](https://portal.azure.com)로 이동하고 필수 조건의 일부로 프로비전한 작업 영역을 선택합니다. __config.json 다운로드__ 가 표시됩니다(아래 참조). config를 다운로드하고 DSVM의 작업 디렉터리에 저장되었는지 확인하세요.

![config 파일 가져오기](./media/how-to-track-experiments/experiment-tracking-2.png)

config에는 작업 영역 이름, 구독 등의 정보가 포함되어 있으므로 이러한 매개 변수를 하드 코드로 작성하지 않아도 됩니다.

## <a name="track-dsvm-runs"></a>DSVM 실행 추적

Notebook(또는 스크립트)에 다음 코드를 추가하여 AzureML 작업 영역 개체를 설정합니다.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
추적 URI는 최대 1시간까지 유효합니다. 유휴 시간 이후 스크립트를 다시 시작하는 경우 get_mlflow_tracking_uri API를 사용하여 새 URI를 가져옵니다.

### <a name="load-the-data"></a>데이터 로드

이 예에서는 scikit-learn과 함께 제공되는 잘 알려진 작은 데이터 세트인 당뇨병 데이터 세트를 사용합니다. 이 셀은 데이터 세트를 로드하고 임의의 학습 및 테스트 집합으로 분할합니다.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>추적 추가

Azure Machine Learning SDK를 사용하여 실험 추적을 추가하고 지속형 모델을 실험 실행 기록에 업로드합니다. 다음 코드는 로그를 추가하고, 모델 파일을 실험 실행에 업로드합니다. 모델은 Azure Machine Learning 모델 레지스트리에 이미 등록되어 있습니다.

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>Azure Machine Learning에서 실행 보기

[Azure Machine Learning Studio](https://ml.azure.com)에서 실험 실행을 볼 수 있습니다. 왼쪽 메뉴에서 __실험__ 을 클릭하고 'experiment_with_mlflow'를 선택하거나 위 코드 조각에서 실험의 이름을 다르게 지정한 경우에는 사용된 이름을 클릭합니다.

![실험 선택](./media/how-to-track-experiments/mlflow-experiments.png)

로그된 MSE(평균 제곱 오차)가 표시되어야 합니다.

![MSE](./media/how-to-track-experiments/mlflow-experiments-2.png)

실행을 클릭하면 __출력+로그__ 에 다른 세부 정보와 pickle 처리된 모델도 표시됩니다.

## <a name="deploy-model-in-azure-machine-learning"></a>Azure Machine Learning에 모델 배포

이 섹션에서는 DSVM에서 학습된 모델을 Azure Machine Learning에 배포하는 방법을 간략하게 설명합니다.

### <a name="step-1-create-inference-compute"></a>1단계: 추론 컴퓨팅 만들기

[AzureML Studio](https://ml.azure.com)의 왼쪽 메뉴에서 __컴퓨팅__ 을 클릭한 다음 __추론 클러스터__ 탭을 클릭한 후 아래 설명된 대로 __+ 새로 만들기__ 를 클릭합니다.

![추론 컴퓨팅 만들기](./media/how-to-track-experiments/mlflow-experiments-6.png)

__새 추론 클러스터__ 창에서 다음의 세부 정보를 채웁니다.

* 컴퓨팅 이름
* Kubernetes 서비스 - 새로 만들기 선택
* 지역 선택
* VM 크기 선택(이 자습서에서는 Standard_D3_v2 기본값으로 충분)
* 클러스터 용도 - __Dev-test__ 선택
* 노드 수는 __1__ 과 같아야 함
* 네트워크 구성 - 기본

다음으로 __만들기__ 를 클릭합니다.

![컴퓨팅 세부 정보](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>2단계: 코드 없는 추론 서비스 배포

`register_model`을 사용하여 코드에 모델을 등록할 때 프레임워크를 sklearn으로 지정했습니다. Azure Machine Learning은 다음 프레임워크의 코드 없는 배포를 지원하지 않습니다.

* scikit-learn
* Tensorflow SaveModel 형식
* ONNX 모델 형식

코드 없는 배포는 특정 채점 스크립트를 지정하지 않고도 모델 아티팩트에서 바로 배포할 수 있음을 의미합니다.

당뇨병 모델을 배포하려면 [Azure Machine Learning Studio](https://ml.azure.com)의 왼쪽 메뉴로 이동하여 __모델__ 을 선택합니다. 그런 다음 등록된 diabetes_model을 클릭합니다.

![모델 선택](./media/how-to-track-experiments/mlflow-experiments-3.png)

다음으로 모델 세부 정보 창에서 __배포__ 단추를 클릭합니다.

![배포:](./media/how-to-track-experiments/mlflow-experiments-4.png)

1단계에서 만든 추론 클러스터(Azure Kubernetes Service)에 모델을 배포합니다. 서비스 이름과 1단계에서 만든 AKS 컴퓨팅 클러스터의 이름을 제공하여 아래 세부 정보를 채웁니다. __CPU 예약 용량__ 을 0.1에서 1로 늘리고 __메모리 예약 용량__ 을 0.5에서 1로 늘리는 것이 좋습니다. __고급__ 을 클릭하고 세부 정보를 채우면 늘릴 수 있습니다. 그런 다음 __배포__ 를 클릭합니다.

![배포 세부 정보](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>3단계: 사용

모델이 성공적으로 배포되면 다음이 표시됩니다(이 페이지로 이동하려면 왼쪽 메뉴에서 엔드포인트를 클릭한 다음 배포된 서비스의 이름을 클릭합니다).

![모델 사용](./media/how-to-track-experiments/mlflow-experiments-8.png)

배포 상태가 __전환 중__ 에서 __정상__ 으로 바뀌는 것이 표시되어야 합니다. 또한 이 세부 정보 섹션은 애플리케이션 개발자가 ML 모델을 앱에 통합하는 데 사용할 수 있는 REST 엔드포인트와 Swagger URL을 제공합니다.

[Postman](https://www.postman.com/)을 사용하여 엔드포인트를 테스트하거나 AzureML SDK를 사용할 수 있습니다.

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>4단계: 정리

진행 중인 컴퓨팅 요금이 발생하지 않도록 1단계에서 만든 추론 컴퓨팅을 삭제합니다. Azure Machine Learning Studio의 왼쪽 메뉴에서 컴퓨팅 > 추론 클러스터 > 컴퓨팅 선택 > 삭제를 클릭합니다.

## <a name="next-steps"></a>다음 단계

* [AzureML에 모델 배포](../how-to-deploy-and-where.md)에 대해 자세히 알아보기

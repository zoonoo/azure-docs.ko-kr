---
title: '자습서: Notebook을 사용하여 예측 모델 만들기(2-1부)'
titleSuffix: Azure Machine Learning
description: Jupyter Notebook의 코드를 사용하여 기계 학습 예측 모델을 작성하고 배포하는 방법을 알아보고, Microsoft Power BI에서 이를 사용하여 결과를 예측할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370843"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>자습서: Power BI 통합 - Notebook을 사용하여 예측 모델 만들기(2-1부)

이 자습서의 1부에서는 Jupyter Notebook의 코드를 사용하여 기계 학습 예측 모델을 학습시키고 배포합니다. 2부에서는 Microsoft Power BI에서 모델을 사용하여 결과를 예측합니다.

이 자습서에서는 다음과 같은 작업을 수행했습니다.

> [!div class="checklist"]
> * Jupyter Notebook 만들기
> * Azure Machine Learning 컴퓨팅 인스턴스 만들기
> * scikit-learn을 사용하여 회귀 모델 학습
> * 실시간 채점 엔드포인트에 모델 배포

Power BI에서 사용할 모델을 만들고 배포하는 세 가지 방법이 있습니다.  이 문서에서는 '옵션 A: Notebook을 사용하여 모델 학습 및 배포'에 대해 설명합니다.  이 옵션은 Azure Machine Learning 스튜디오에서 호스팅되는 Jupyter Notebook을 사용하는 코드 우선 제작 환경을 보여 줍니다. 

대신 다음을 사용할 수 있습니다.

* [옵션 B: 디자이너를 사용하여 모델 학습 및 배포](tutorial-power-bi-designer-model.md) - 디자이너를 사용하는 로우 코드 제작 환경(끌어서 놓기 사용자 인터페이스)입니다.
* [옵션 C: 자동화된 ML을 사용하여 모델 학습 및 배포](tutorial-power-bi-automated-model.md) - 데이터 준비 및 모델 학습을 완전히 자동화하는 코드 없음 제작 환경입니다.


## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독([평가판 사용 가능](https://aka.ms/AMLFree)) 
- Azure Machine Learning 작업 영역 작업 영역이 아직 없는 경우 [Azure Machine Learning 작업 영역을 만드는 방법](./how-to-manage-workspace.md#create-a-workspace)을 따르세요.
- Python 언어 및 기계 학습 워크플로에 대한 입문 지식

## <a name="create-a-notebook-and-compute"></a>Notebook 및 컴퓨팅 만들기

[Azure Machine Learning Studio](https://ml.azure.com) 홈페이지에서 **새로 만들기** 를 선택한 다음, **Notebook** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Notebook을 만드는 방법을 보여 주는 스크린샷":::
 
**새 파일 만들기** 대화 상자가 표시됩니다. 다음을 입력합니다.

1. Notebook 파일 이름(예: `my_model_notebook`)
1. **파일 형식** 을 **Notebook** 으로 변경

**만들기** 를 선택합니다. 다음으로, 코드 셀을 실행하기 위해 몇 가지 컴퓨팅을 만들어 Notebook에 연결해야 합니다. 이렇게 하려면 Notebook의 위쪽에서 더하기 아이콘을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="컴퓨팅 인스턴스를 만드는 방법을 보여 주는 스크린샷":::

다음으로, **컴퓨팅 인스턴스 만들기** 페이지에서 다음을 수행합니다.

1. CPU 가상 머신 크기를 선택합니다. 이 자습서에서는 **Standard_D11_v2**(2개 코어, 14GB RAM)를 사용하는 것이 좋습니다.
1. **다음** 을 선택합니다. 
1. **설정 구성** 페이지에서 올바른 **컴퓨팅 이름** 을 지정합니다(유효한 문자는 대문자, 소문자, 숫자 및 - 문자임).
1. **만들기** 를 선택합니다.

Notebook에서 **컴퓨팅** 옆의 원이 녹청색으로 바뀌어 컴퓨팅 인스턴스가 만들어지고 있음을 확인할 수 있습니다.

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="컴퓨팅이 만들어지고 있음을 보여 주는 스크린샷":::

> [!NOTE]
> 컴퓨팅을 프로비저닝하는 데 2~4분 정도 걸릴 수 있습니다.

컴퓨팅이 프로비저닝되면 Notebook을 사용하여 코드 셀을 실행할 수 있습니다. 예를 들어 다음을 셀에 입력합니다.

```python
import numpy as np

np.sin(3)
```

그런 다음, **Shift-Enter** 를 누릅니다(또는 **Control-Enter** 를 누르거나 셀 옆의 재생 단추를 선택함). 다음 출력이 표시됩니다.

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="셀 실행을 보여 주는 스크린샷":::

이제 Machine Learning 모델을 작성할 준비가 되었습니다.

## <a name="build-a-model-using-scikit-learn"></a>scikit-learn을 사용하여 모델 작성

이 자습서에서는 [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)에서 제공되는 [당뇨병 데이터 세트](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)를 사용합니다. 


### <a name="import-data"></a>데이터 가져오기

데이터를 가져오려면 아래 코드를 복사하여 Notebook의 새 **코드 셀** 에 붙여넣습니다.

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

`X_df` pandas 데이터 프레임에는 10개의 기준 입력 변수(예: 연령, 성별, 체질량 지수, 평균 혈압 및 6개 혈청 측정값)가 포함됩니다. `y_df` pandas 데이터 프레임은 기준 이후 1년간의 질병 진행에 대한 정량 측정값을 포함하는 대상 변수입니다. 총 442개의 레코드가 있습니다.

### <a name="train-model"></a>모델 학습

새 **코드 셀** 을 Notebook에 만들고, 아래 코드 조각을 복사하여 붙여넣습니다. 이 코드 조각은 능형 회귀 모델을 생성하고 Python의 pickle 형식을 사용하여 모델을 직렬화합니다.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>모델 등록

등록된 모델에는 모델 파일 자체의 내용 외에도 모델 메타데이터(모델 설명, 태그 및 프레임워크 정보)가 저장되므로 작업 영역에서 모델을 관리하고 배포하는 데 유용합니다. 예를 들어 태그를 사용하면 모델을 작업 영역에 나열할 때 모델을 분류하고 필터를 적용할 수 있습니다. 또한 scikit-learn 프레임워크를 사용하여 이 모델을 표시하면 나중에 살펴볼 수 있듯이 웹 서비스로 배포하는 작업이 간단해집니다.

아래 코드를 복사하여 Notebook의 새 **코드 셀** 에 붙여넣습니다.

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

왼쪽 메뉴에서 **엔드포인트** 로 이동하여 Azure Machine Learning Studio에서 모델을 볼 수도 있습니다.

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="모델을 보여 주는 스크린샷":::

### <a name="define-the-scoring-script"></a>채점 스크립트 정의

Microsoft Power BI에 통합할 모델을 배포하는 경우 Python *채점 스크립트* 및 사용자 지정 환경을 정의해야 합니다. 채점 스크립트에는 다음 두 가지 함수가 있습니다.

- `init()` - 서비스를 시작하면 이 함수가 실행됩니다. 이 함수는 모델을 로드하고(모델 레지스트리에서 모델이 자동으로 다운로드됨) 역직렬화합니다.
- `run(data)` - 채점할 필요가 있는 일부 입력 데이터를 사용하여 서비스를 호출하면 이 함수가 실행됩니다. 

>[!NOTE]
> Python 데코레이터를 사용하여 입력 및 출력 데이터의 스키마를 정의합니다. 이는 Microsoft Power BI 통합이 작동하는 데 중요합니다.

아래 코드를 복사하여 Notebook의 새 **코드 셀** 에 붙여넣습니다. 아래 코드 조각에는 코드를 score.py라는 파일에 쓰는 셀 매직이 있습니다.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>사용자 지정 환경 정의

다음으로, 모델을 채점하는 환경을 정의해야 합니다. 이 환경에서는 위에서 정의한 채점 스크립트(score.py)에 필요한 Python 패키지(예: pandas, scikit-learn 등)를 정의해야 합니다.

환경을 정의하려면 아래 코드를 복사하여 Notebook의 새 **코드 셀** 에 붙여넣습니다.

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>모델 배포

모델을 배포하려면 아래 코드를 복사하여 Notebook의 새 **코드 셀** 에 붙여넣습니다.

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> 서비스를 배포하는 데 2~4분 정도 걸릴 수 있습니다.

성공적으로 배포된 서비스에 대해 다음과 같은 출력이 표시됩니다.

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

왼쪽 메뉴에서 **엔드포인트** 로 이동하여 Azure Machine Learning Studio에서 서비스를 볼 수도 있습니다.

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="엔드포인트를 보여 주는 스크린샷":::

웹 서비스를 테스트하여 예상대로 작동하는지 확인하는 것이 좋습니다. Azure Machine Learning Studio의 왼쪽 메뉴에서 **Notebook** 을 선택하여 Notebook으로 다시 이동합니다. 서비스를 테스트하려면 아래 코드를 복사하여 Notebook의 새 **코드 셀** 에 붙여넣습니다.

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

출력은 `{'predict': [[205.59], [68.84]]}` json 구조와 같습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Microsoft Power BI에서 사용할 수 있는 방식으로 모델을 작성하고 배포하는 방법을 살펴보았습니다. 다음 2부에서는 Power BI 보고서에서 이 모델을 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: Power BI에서 모델 사용](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)

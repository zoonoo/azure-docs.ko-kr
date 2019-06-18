---
title: Azure Machine Learning 서비스를 사용 하 여 MLflow를 사용 하는 방법
titleSuffix: Azure Machine Learning service
description: 메트릭 및 Azure Machine Learning 서비스에 MLflow 라이브러리를 사용 하 여 아티팩트를 기록 하는 방법에 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 934e9b705ab5f399d29f24c915b4c60a3b06138b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082574"
---
# <a name="how-to-use-mlflow-with-azure-machine-learning-service-preview"></a>Azure Machine Learning 서비스 (미리 보기)를 사용 하 여 MLflow를 사용 하는 방법

이 문서에서는 MLflow의 URI를 추적 및 로깅 API를 사용 하는 방법을 전체적으로 라고도 MLflow 추적을 추적 하 고 실험 메트릭 및 아티팩트를 기록할 Azure Machine Learning 서비스를 사용 하 여 프로그램 [Azure Machine Learning 서비스 작업 영역](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace)합니다. 사용 하는 경우 이미 MLflow 추적 실험에 대해, 작업 영역 중앙이 고 안전 하며 확장성이 뛰어난 교육 메트릭 및 모델을 저장할 위치를 제공 합니다.

[MLflow](https://www.mlflow.org) 는 기계 학습 실험의 수명 주기를 관리 하기 위한 오픈 소스 라이브러리입니다. [MLFlow 추적](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) MLflow 로그 및 메트릭을 실행할 교육을 추적 하는의 구성 요소 이며 모델 아티팩트를 실험을 로컬로 실행 하는지 여부를 원격 컴퓨터나 가상 머신에서 계산 클러스터.
![azure machine learning 다이어그램을 사용 하 여 mlflow](media/how-to-use-mlflow/mlflow-diagram.png)

## <a name="compare-mlflow-and-azure-machine-learning-service-clients"></a>MLflow 및 Azure Machine Learning 서비스 클라이언트를 비교 합니다.

 아래 표에서 각 함수의 기능과 Azure Machine Learning 서비스를 사용할 수 있는 다른 클라이언트를 요약 합니다.

 MLflow 추적 제공 메트릭 로깅 및 아티팩트를 통해 사용할 수만 수 있는 저장소 기능을 [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)합니다.

| | MLflow 추적 | Azure Machine Learning <br> Python SDK |  Azure Machine Learning <br> CLI | Azure portal|
|-|-|-|-|-|
| 작업 영역 관리 |   | ✓ |  ✓ | ✓  |
| 데이터 저장소를 사용 합니다.  |   | ✓ |  ✓ |    |
| 메트릭을 기록      | ✓ | ✓ |    |    |
| 아티팩트 업로드 | ✓ | ✓ |    |    |
| 메트릭 보기     | ✓ | ✓ | ✓  | ✓ |
| 관리 계산   |   | ✓ | ✓  | ✓ |
| 모델 배포    |   | ✓ |   ✓ | ✓ |

## <a name="prerequisites"></a>필수 조건

* [MLflow를 설치 합니다.](https://mlflow.org/docs/latest/quickstart.html)
* [로컬 컴퓨터에 Azure Machine Learning Python SDK를 설치 하 고 Azure Machine Learning 작업 영역을 만들](setup-create-workspace.md#sdk)합니다. SDK은 MLflow 작업 영역 액세스에 대 한 연결을 제공 합니다.

## <a name="track-local-runs"></a>로컬 실행을 추적 합니다.

설치 된 `azureml-contrib-run` Jupyter Notebook 또는 코드 편집기에서 로컬로 실행 하는 실험에 Azure Machine Learning을 사용 하 여 MLflow 추적을 사용 하는 패키지입니다.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Azureml.contrib 네임 스페이스 서비스를 개선 하기 위한 노력으로 자주 변경 합니다. 따라서 이 네임 스페이스의 모든 것을 미리 보기로 간주하므로 Microsoft에서 완벽히 지원하지 않아도 됩니다.

가져오기의 `mlflow` 하 고 [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) MLflow 액세스 하기 위한 클래스의 URI를 추적 하 고 작업 영역 구성 합니다.

다음 코드에서를 `get_mlflow_tracking_uri()` 메서드는 작업 영역으로 고유한 추적 URI 주소를 할당 `ws`, 및 `set_tracking_uri()` URI 주소에 추적 MLflow 지점입니다.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>추적 URI 유효한 최대 한 시간 미만인 됩니다. 스크립트를 몇 가지 유휴 시간 후 다시 시작 하면 새 URI를 가져오려면 get_mlflow_tracking_uri API를 사용 합니다.

MLflow 실험 이름 설정 `set_experiment()` 사용 하 여 실행 하 여 교육을 받고 `start_run()`합니다. 사용 하 여 `log_metric()` MLflow 로깅 API를 활성화 하 여 메트릭을 실행할 교육을 로깅 하기 시작 합니다.

```Python
experiment_name = "experiment_with_mlflow"
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>원격 실행을 추적 합니다.

원격 실행을 통해 GPU 사용 가상 컴퓨터 또는 클러스터 Machine Learning Compute와 같은 더 강력한 계산에서 모델을 학습할 수 있습니다. 참조 [모델 학습을 위한 계산 대상 설정](how-to-set-up-training-targets.md) 다양 한 계산 옵션에 대해 자세히 알아보려면 합니다.

계산 및 교육 실행 환경과 구성 합니다 [ `Environment` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 클래스입니다. 포함 `mlflow` 하 고 `azure-contrib-run` 환경에서 패키지를 pip [ `CondaDependencies` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) 섹션입니다. 그런 다음 작성할 [ `ScriptRunConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) 계산 대상으로 원격 컴퓨터를 사용 하 여 합니다.

```Python

from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = "my_workspace",
                 name = "my_experiment")

mlflow_env = Environment(name="mlflow-env")

cd = CondaDependencies.create(pip_packages = ["mlflow", "azureml-contrib-run"])

mlflow_env.python.conda_dependencies=cd

src = ScriptRunConfig(source_directory="./my_script_location", script="my_training_script.py")

src.run_config.target = "my-remote-compute-compute"
src.run_config.environment = mlflow_env
```

학습 스크립트에서 가져올 `mlflow` 로깅 Api, MLflow를 사용 하 고 실행된 메트릭 로깅을 시작 합니다.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric("example", 1.23)
```

이 계산 및 교육 실행 구성을 사용 하 여는 `Experiment.submit("train.py")` 실행을 제출 하는 방법입니다. 이 자동으로 추적 URI MLflow 설정, 작업 영역에 MLflow에서 로깅을 보냅니다.

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>메트릭 보기 및 작업 영역에서 아티팩트

메트릭 및 로깅 MLflow에서에서 아티팩트 작업 영역에서에 유지 되는 [Azure portal](https://portal.azure.com)합니다. 언제 든 지 해당 명령을 보려면 작업 영역으로 이동한 다음 이름으로 실험을 찾습니다.

## <a name="clean-up-resources"></a>리소스 정리

작업 영역에는 기록 된 메트릭과 아티팩트를 사용 하지 않으려는 경우 개별적으로 삭제할 수는 현재 사용할 수 없는 경우 대신, 요금이 발생 하지 않도록 저장소 계정 및 작업 영역을 포함 하는 리소스 그룹을 삭제 합니다.

1. Azure Portal의 맨 왼쪽에서 **리소스 그룹**을 선택합니다.

   ![Azure Portal에서 삭제](media/how-to-use-mlflow/delete-resources.png)

1. 목록에서 만든 리소스 그룹을 선택합니다.

1. **리소스 그룹 삭제**를 선택합니다.

1. 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제**를 선택합니다.

## <a name="example-notebooks"></a>노트북 예제

합니다 [Azure ML notebook 사용 하 여 MLflow](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/mlflow) 이 문서의 개념을 보여 줍니다.

## <a name="next-steps"></a>다음 단계

* [모델을 배포 하는 방법을](how-to-deploy-and-where.md)합니다.
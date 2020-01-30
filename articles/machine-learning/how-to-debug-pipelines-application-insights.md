---
title: Application Insights에서 기계 학습 파이프라인 디버그 및 문제 해결
titleSuffix: Azure Machine Learning
description: 학습 및 일괄 처리 점수 매기기 파이프라인에 로깅을 추가 하 고 Application Insights에서 기록 된 결과를 확인 합니다.
services: machine-learning
author: aburek
ms.author: anrode
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: 85dcd9ef98deb2ea0117f2db280e49c4a57bf00f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776301"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>Application Insights에서 기계 학습 파이프라인 디버그 및 문제 해결
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[OpenCensus](https://opencensus.io/quickstart/python/) python 라이브러리를 사용 하 여 로그를 스크립트에서 Application Insights로 라우팅할 수 있습니다. 파이프라인 실행의 로그를 한 곳에서 집계 하면 쿼리를 작성 하 고 문제를 진단할 수 있습니다. Application Insights를 사용 하면 시간에 따른 로그를 추적 하 고 실행 간에 파이프라인 로그를 비교할 수 있습니다.

로그를 한 번에 배치 하면 예외 및 오류 메시지에 대 한 기록이 제공 됩니다. Application Insights는 Azure 경고와 통합 되기 때문에 Application Insights 쿼리를 기반으로 경고를 만들 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

* 단계에 따라 [Azure Machine Learning](./how-to-manage-workspace.md) 작업 영역을 만들고 [첫 번째 파이프라인을 만듭니다](./how-to-create-your-first-pipeline.md) .
* [개발 환경을 구성](./how-to-configure-environment.md)하여 Azure Machine Learning SDK를 설치합니다.
* [OpenCensus Azure Monitor 내보내기](https://pypi.org/project/opencensus-ext-azure/) 패키지를 로컬로 설치 합니다.
  ```python
  pip install opencensus-ext-azure
  ```
* [Application Insights 인스턴스](../azure-monitor/app/opencensus-python.md) 만들기 (이 문서에는 리소스에 대 한 연결 문자열을 가져오는 방법에 대 한 정보도 포함 되어 있습니다.)

## <a name="getting-started"></a>시작하기

이 섹션은 Azure Machine Learning 파이프라인에서 OpenCensus를 사용 하는 것과 관련 된 소개입니다. 자세한 자습서는 [OpenCensus Azure Monitor 내보내기](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure) 를 참조 하세요.

Azure ML 파이프라인에 PythonScriptStep를 추가 합니다. Opencensus에 대 한 종속성을 사용 하 여 [Runconfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 을 구성 합니다. `APPLICATIONINSIGHTS_CONNECTION_STRING` 환경 변수를 구성 합니다.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

`sample_step.py`라는 파일을 만듭니다. AzureLogHandler 클래스를 가져와서 로그를 Application Insights로 라우팅합니다. 또한 Python 로깅 라이브러리를 가져와야 합니다.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

그런 다음 AzureLogHandler를 python로 거에 추가 합니다.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>사용자 지정 차원으로 로깅
 
기본적으로 Application Insights에 전달 된 로그에는 실행 또는 실험을 다시 추적 하는 데 충분 한 컨텍스트가 없습니다. 문제를 진단 하기 위해 로그를 사용할 수 있도록 하려면 추가 필드가 필요 합니다. 

이러한 필드를 추가 하려면 로그 메시지에 컨텍스트를 제공 하기 위해 사용자 지정 차원을 추가할 수 있습니다. 한 가지 예는 다른 사람이 동일한 파이프라인 실행의 여러 단계에서 로그를 보려는 경우입니다.

사용자 지정 차원은 키-값 (문자열, 문자열) 쌍의 사전을 구성 합니다. 그러면 사전이 Application Insights 전송 되 고 쿼리 결과에 열로 표시 됩니다. 개별 차원은 [쿼리 매개](#additional-helpful-queries)변수로 사용할 수 있습니다.

### <a name="helpful-context-to-include"></a>포함할 유용한 컨텍스트

| 필드                          | 이유/예제                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | 는 각 개별 단계를 알아보는 대신 모든 단계에서 시간이 지남에 따라 로그를 볼 수 있도록 동일한 parent_run_id 있는 로그를 쿼리할 수 있습니다.                                        |
| step_id                        | 는 동일한 step_id를 사용 하 여 로그를 쿼리하여 개별 단계에만 좁은 범위에서 문제가 발생 한 위치를 확인할 수 있습니다.                                                        |
| step_name                      | 는 시간에 따른 단계별 성능을 볼 수 있도록 로그를 쿼리할 수 있습니다. 는 포털 UI에 대해 자세히 살펴보기 전에 최근 실행에 대 한 step_id를 찾는 데도 도움이 됩니다.                                          |
| experiment_name                | 시간이 지남에 따라 실험 성능을 볼 수 있도록 로그를 쿼리할 수 있습니다. 또한 포털 UI를 자세히 살펴보기 전에 최근 실행에 대 한 parent_run_id 또는 step_id를 찾을 수 있습니다.                   |
| run_url                 | 조사를 위해 실행에 바로 다시 링크를 제공할 수 있습니다. |

**기타 유용한 필드**

이러한 필드에는 추가 코드 계측이 필요할 수 있으며 실행 컨텍스트에서 제공 되지 않습니다.

| 필드                   | 이유/예제                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | CI/CD를 사용 하 여 배포 하는 경우이 필드는 단계 및 파이프라인 논리를 제공 하는 코드 버전과 로그의 상관 관계를 지정할 수 있습니다. 이 링크를 통해 문제를 진단 하거나 특정 특성 (로그/메트릭 값)을 사용 하 여 모델을 식별할 수 있습니다. |
| run_type                       | 서로 다른 모델 유형 또는 학습 및 점수 매기기 실행을 구분할 수 있습니다. |

### <a name="creating-a-custom-dimensions-dictionary"></a>사용자 지정 차원 사전 만들기

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>OpenCensus Python 로깅 고려 사항

OpenCensus AzureLogHandler는 Python 로그를 Application Insights로 라우팅하는 데 사용 됩니다. 따라서 Python 로깅 미묘한 차이 고려해 야 합니다. 로 거를 만들 때 기본 로그 수준이 있으며 해당 수준 보다 크거나 같은 로그를 표시 합니다. Python 로깅 기능 사용에 대 한 좋은 참조는 [로깅 Cookbook](https://docs.python.org/3/howto/logging-cookbook.html).

OpenCensus 라이브러리에 `APPLICATIONINSIGHTS_CONNECTION_STRING` 환경 변수가 필요 합니다. 이 환경 변수를 파이프라인 매개 변수로 전달 하는 대신이 변수를 설정 하 여 일반 텍스트 연결 문자열이 전달 되지 않도록 하는 것이 좋습니다.

## <a name="querying-logs-in-application-insights"></a>Application Insights에서 로그 쿼리

Application Insights로 라우팅되는 로그는 ' 추적 ' 또는 ' 예외 ' 아래에 표시 됩니다. 파이프라인 실행을 포함 하도록 시간 창을 조정 해야 합니다.

![Application Insights 쿼리 결과](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Application Insights 결과에는 로그 메시지 및 수준, 파일 경로 및 코드 줄 번호가 표시 됩니다. 또한 포함 된 사용자 지정 차원이 표시 됩니다. 이 이미지에서 customDimensions 사전은 이전 [코드 샘플](#creating-a-custom-dimensions-dictionary)의 키/값 쌍을 표시 합니다.

### <a name="additional-helpful-queries"></a>추가 유용한 쿼리

아래 쿼리에서는 ' customDimensions. Level '을 사용 합니다. 이러한 심각도 수준은 Python 로그가 원래 전송 된 수준에 해당 합니다. 추가 쿼리 정보는 [Azure Monitor 로그 쿼리](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)를 참조 하세요.

| 사용 사례                                                               | 쿼리                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| 특정 사용자 지정 차원에 대 한 로그 결과 (예: ' parent_run_id ') | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| 지난 7 일 동안 모든 학습 실행에 대 한 로그 결과                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| 지난 7 일 동안 severityLevel 오류가 발생 한 로그 결과              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| 지난 7 일 동안 severityLevel 오류가 발생 한 로그 결과 개수     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>다음 단계

Application Insights 인스턴스에 로그가 있으면 쿼리 결과에 따라 [Azure Monitor 경고](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) 를 설정 하는 데 사용할 수 있습니다.

추가 정보를 얻기 위해 쿼리 결과를 [Azure 대시보드에](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) 추가할 수도 있습니다.
---
title: 애플리케이션 인사이트에서 머신 러닝 파이프라인 디버그 및 문제 해결
titleSuffix: Azure Machine Learning
description: 교육 및 일괄 점수 매기기 파이프라인에 로깅을 추가하고 애플리케이션 인사이트에서 기록된 결과를 확인합니다.
services: machine-learning
author: aburek
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: 217a89f6ede4e4b1d2182eed79b088808432044f
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529371"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>애플리케이션 인사이트에서 머신 러닝 파이프라인 디버그 및 문제 해결
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[OpenCensus](https://opencensus.io/quickstart/python/) 파이썬 라이브러리는 스크립트에서 응용 프로그램 인사이트로 로그를 라우팅하는 데 사용할 수 있습니다. 파이프라인 에서 로그를 한 곳에서 집계하면 쿼리를 빌드하고 문제를 진단할 수 있습니다. Application Insights를 사용하면 시간에 따라 로그를 추적하고 실행 간에 파이프라인 로그를 비교할 수 있습니다.

한 번에 로그를 사용하면 예외 및 오류 메시지의 기록이 표시됩니다. 응용 프로그램 인사이트는 Azure 경고와 통합되므로 응용 프로그램 인사이트 쿼리를 기반으로 경고를 만들 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 단계에 따라 Azure [기계 학습](./how-to-manage-workspace.md) 작업 영역을 만들고 [첫 번째 파이프라인을 만듭니다.](./how-to-create-your-first-pipeline.md)
* [개발 환경을 구성](./how-to-configure-environment.md)하여 Azure Machine Learning SDK를 설치합니다.
* [OpenCensus Azure 모니터 내보내기](https://pypi.org/project/opencensus-ext-azure/) 패키지를 로컬로 설치합니다.
  ```python
  pip install opencensus-ext-azure
  ```
* 응용 [프로그램 인사이트 인스턴스](../azure-monitor/app/opencensus-python.md) 만들기(이 문서에는 리소스에 대한 연결 문자열 을 가져오는 방법에 대한 정보도 포함되어 있습니다).

## <a name="getting-started"></a>시작하기

이 섹션은 Azure 기계 학습 파이프라인에서 OpenCensus를 사용하는 데 관련된 소개입니다. 자세한 자습서는 [OpenCensus Azure 모니터 내보내기를](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure) 참조하십시오.

Azure ML 파이프라인에 PythonScriptStep을 추가합니다. opencensus-내선 Azure에 대한 종속성으로 [RunConfiguration을](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 구성합니다. 환경 `APPLICATIONINSIGHTS_CONNECTION_STRING` 변수를 구성합니다.

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

`sample_step.py`라는 파일을 만듭니다. AzureLogHandler 클래스를 가져와 로그를 응용 프로그램 인사이트로 라우팅합니다. 또한 파이썬 로깅 라이브러리를 가져와야 합니다.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

다음으로, 파이썬 로거에 AzureLogHandler를 추가합니다.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>사용자 지정 차원으로 로깅
 
기본적으로 응용 프로그램 인사이트로 전달된 로그에는 실행 또는 실험으로 추적할 컨텍스트가 충분하지 않습니다. 문제를 진단하기 위해 로그를 실행 가능하게 하려면 추가 필드가 필요합니다. 

이러한 필드를 추가하려면 사용자 지정 차원을 추가하여 로그 메시지에 컨텍스트를 제공할 수 있습니다. 한 가지 예는 동일한 파이프라인 실행의 여러 단계에서 로그를 보려는 경우입니다.

사용자 지정 차원은 키-값(문자열, 문자열로 저장됨) 쌍의 사전을 구성합니다. 그런 다음 사전이 응용 프로그램 인사이트로 전송되고 쿼리 결과의 열로 표시됩니다. 개별 차원을 [쿼리 매개 변수로](#additional-helpful-queries)사용할 수 있습니다.

### <a name="helpful-context-to-include"></a>포함할 유용한 컨텍스트

| 필드                          | 추론/예                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | 동일한 parent_run_id 있는 로그를 쿼리하여 각 개별 단계에 대해 자세히 살펴볼 필요 없이 모든 단계에 대한 시간 별 로그를 볼 수 있습니다.                                        |
| step_id                        | 동일한 step_id 로그를 쿼리하여 개별 단계로의 좁은 범위로 문제가 발생한 위치를 확인할 수 있습니다.                                                        |
| step_name                      | 시간에 따라 단계 성능을 확인하기 위해 로그를 쿼리할 수 있습니다. 또한 포털 UI로 다이빙하지 않고 최근 실행에 대한 step_id 찾는 데 도움이                                          |
| experiment_name                | 로그 간에 쿼리하여 시간에 따라 실험 실적을 확인할 수 있습니다. 또한 포털 UI로 다이빙하지 않고 최근 실행에 대한 parent_run_id 또는 step_id 찾는 데 도움이됩니다.                   |
| run_url                 | 조사를 위해 실행으로 직접 링크를 제공할 수 있습니다. |

**기타 유용한 필드**

이러한 필드는 추가 코드 계측이 필요할 수 있으며 실행 컨텍스트에서 제공되지 않습니다.

| 필드                   | 추론/예                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | CI/CD를 사용하여 배포하는 경우 이 필드는 로그를 단계 및 파이프라인 논리를 제공한 코드 버전과 상호 연관시킬 수 있습니다. 이 링크를 사용하면 문제를 진단하거나 특정 특성(로그/메트릭 값)을 가진 모델을 식별하는 데 도움이 될 수 있습니다. |
| run_type                       | 다양한 모델 유형 또는 교육 과 점수 매기기 실행을 구분할 수 있습니다. |

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

## <a name="opencensus-python-logging-considerations"></a>오픈인구파이썬 로깅 고려 사항

OpenCensus AzureLogHandler는 파이썬 로그를 응용 프로그램 인사이트로 라우팅하는 데 사용됩니다. 따라서 파이썬 로깅 뉘앙스를 고려해야합니다. 로거가 만들어지면 기본 로그 수준이 있으며 해당 수준보다 크거나 같은 로그가 표시됩니다. 파이썬 로깅 기능을 사용하기위한 좋은 참조는 [로깅 쿡북입니다.](https://docs.python.org/3/howto/logging-cookbook.html)

OpenCensus 라이브러리에 `APPLICATIONINSIGHTS_CONNECTION_STRING` 환경 변수가 필요합니다. 일반 텍스트 연결 문자열 을 통과하지 않도록 파이프라인 매개 변수로 전달하는 대신 이 환경 변수를 설정하는 것이 좋습니다.

## <a name="querying-logs-in-application-insights"></a>애플리케이션 인사이트 로그 쿼리

응용 프로그램 인사이트로 라우팅된 로그는 '추적' 또는 '예외' 아래에 표시됩니다. 파이프라인 실행을 포함하도록 시간 창을 조정해야 합니다.

![애플리케이션 인사이트 쿼리 결과](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Application Insights의 결과에는 로그 메시지와 수준, 파일 경로 및 코드 줄 번호가 표시됩니다. 또한 포함된 모든 사용자 지정 차원이 표시됩니다. 이 이미지에서 customDimensions 사전은 이전 [코드 샘플의](#creating-a-custom-dimensions-dictionary)키/값 쌍을 보여 주며.

### <a name="additional-helpful-queries"></a>추가 유용한 쿼리

아래 쿼리 중 일부는 'customDimensions.Level'을 사용합니다. 이러한 심각도 수준은 Python 로그가 원래 보낸 수준에 해당합니다. 추가 쿼리 정보는 [Azure 모니터 로그 쿼리 를](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)참조하십시오.

| 사용 사례                                                               | 쿼리                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| 특정 사용자 지정 측정기준(예: 'parent_run_id')에 대한 로그 결과 | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| 지난 7일 동안 의 모든 교육 실행에 대한 결과 기록                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| 지난 7일 동안 심각도 수준 오류로 결과를 기록합니다.              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| 지난 7일 동안 심각도 수준 오류가 있는 로그 결과 수     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>다음 단계

응용 프로그램 인사이트 인스턴스에 로그가 있으면 쿼리 결과에 따라 [Azure Monitor 경고를](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) 설정할 수 있습니다.

쿼리의 결과를 [Azure 대시보드에](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) 추가하여 추가 정보를 얻을 수도 있습니다.
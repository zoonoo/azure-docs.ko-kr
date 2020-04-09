---
title: 파이썬에서 교육 실행을 시작, 모니터링 및 취소
titleSuffix: Azure Machine Learning
description: 기계 학습 실험을 시작하고, 상태를 설정하고, 태그를 지정하고, 구성하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: c1b70aaef49cc2b993c873509dc935d71069efa2
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985918"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>파이썬에서 교육 실행을 시작, 모니터링 및 취소
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[파이썬용 Azure 기계 학습 SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) [기계 학습 CLI](reference-azure-machine-learning-cli.md)및 [Azure 기계 학습 스튜디오는](https://ml.azure.com) 학습 및 실험을 위해 실행을 모니터링, 구성 및 관리하는 다양한 방법을 제공합니다.

이 문서에서는 다음 작업의 예를 보여 주며 다음과 같은 작업을 보여 주며 다음과 같은 작업을 보여 주며 다음과 같은 작업을 보여 주며 다음과 같은 작업을 보여 주

* 실행 성능을 모니터링합니다.
* 실행을 취소하거나 실패합니다.
* 자식 실행을 만듭니다.
* 태그를 만들고 실행을 찾습니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 항목이 필요합니다.

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* [Azure 기계 학습 작업 영역](how-to-manage-workspace.md).

* 파이썬용 Azure 기계 학습 SDK(버전 1.0.21 이상). 최신 버전의 SDK를 설치하거나 업데이트하려면 [SDK 를 설치하거나 업데이트하십시오.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

    Azure 기계 학습 SDK 버전을 확인하려면 다음 코드를 사용합니다.

    ```python
    print(azureml.core.VERSION)
    ```

* Azure 기계 학습에 대한 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 및 [CLI 확장.](reference-azure-machine-learning-cli.md)

## <a name="start-a-run-and-its-logging-process"></a>실행 및 로깅 프로세스 시작

### <a name="using-the-sdk"></a>SDK 사용

[azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) 패키지에서 [작업 영역,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) [실험,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) [실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)및 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 클래스를 가져와 실험을 설정합니다.

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

메서드를 사용하여 실행 및 [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) 로깅 프로세스를 시작합니다.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>CLI 사용

실험 실행을 시작하려면 다음 단계를 사용합니다.

1. 셸 또는 명령 프롬프트에서 Azure CLI를 사용하여 Azure 구독을 인증합니다.

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

1. 학습 스크립트가 포함된 폴더에 작업 영역 구성을 연결합니다. Azure `myworkspace` 기계 학습 작업 영역으로 바꿉습니다. 작업 `myresourcegroup` 영역이 포함된 Azure 리소스 그룹으로 바꿉습니다.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    이 명령은 `.azureml` 예제 runconfig 및 conda 환경 파일을 포함하는 하위 디렉터리를 만듭니다. 또한 Azure `config.json` 기계 학습 작업 영역과 통신하는 데 사용되는 파일도 포함되어 있습니다.

    자세한 내용은 [az ml 폴더 첨부](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)를 참조하십시오.

2. 실행을 시작하려면 다음 명령을 사용합니다. 이 명령을 사용하는 경우 -c 매개 변수에 대해 \*runconfig 파일의 이름(파일 시스템을 보는 경우 .runconfig 앞의 텍스트)을 지정합니다.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > 이 `az ml folder attach` 명령은 `.azureml` 두 개의 예제 runconfig 파일을 포함하는 하위 디렉터리를 만들었습니다.
    >
    > 프로그래밍 방식으로 실행 구성 개체를 만드는 Python 스크립트가 있는 경우 [RunConfig.save()를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) 사용하여 runconfig 파일로 저장할 수 있습니다.
    >
    > 더 많은 예제 runconfig [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)파일에 대 한 참조.

    자세한 내용은 [az ml 실행 제출 스크립트를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)참조하십시오.

### <a name="using-azure-machine-learning-studio"></a>Azure 기계 학습 스튜디오 사용

디자이너(미리 보기)에서 파이프라인 실행 을 제출하려면 다음 단계를 사용합니다.

1. 파이프라인에 대한 기본 계산 대상을 설정합니다.

1. 파이프라인 캔버스 상단에서 **실행을** 선택합니다.

1. 파이프라인 실행을 그룹화할 실험을 선택합니다.

## <a name="monitor-the-status-of-a-run"></a>실행 상태 모니터링

### <a name="using-the-sdk"></a>SDK 사용

[`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) 메서드를 사용하여 실행 상태를 가져옵니다.

```python
print(notebook_run.get_status())
```

실행 ID, 실행 시간 및 실행에 대한 추가 세부 [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) 정보를 얻으려면 메서드를 사용합니다.

```python
print(notebook_run.get_details())
```

실행이 성공적으로 완료되면 메서드를 [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) 사용하여 완료된 것으로 표시합니다.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Python의 `with...as` 디자인 패턴을 사용하는 경우 실행이 범위를 벗어났을 때 실행이 완료된 것으로 자동으로 표시됩니다. 실행을 완료된 것으로 수동으로 표시할 필요가 없습니다.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>CLI 사용

1. 실험에 대한 실행 목록을 보려면 다음 명령을 사용합니다. 실험 `experiment` 이름으로 바꾸기:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    이 명령은 이 실험에 대한 실행에 대한 정보를 나열하는 JSON 문서를 반환합니다.

    자세한 내용은 [az ml 실험 목록을](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)참조하십시오.

2. 특정 실행에 대한 정보를 보려면 다음 명령을 사용합니다. 실행 `runid` ID로 바꾸기:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    이 명령은 실행에 대한 정보를 나열하는 JSON 문서를 반환합니다.

    자세한 내용은 [az ml 실행 쇼를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)참조하십시오.


### <a name="using-azure-machine-learning-studio"></a>Azure 기계 학습 스튜디오 사용

스튜디오에서 실험에 대한 활성 실행 수를 봅니다.

1. 실험 섹션으로 **이동합니다.** 

1. 실험을 선택합니다.

    실험 페이지에서 활성 계산 대상 의 수와 각 실행의 기간을 볼 수 있습니다. 

1. 특정 실행 번호를 선택합니다.

1. **로그** 탭에서 파이프라인 실행에 대한 진단 및 오류 로그를 찾을 수 있습니다.


## <a name="cancel-or-fail-runs"></a>실행 취소 또는 실패

실수를 발견하거나 실행을 완료하는 데 시간이 너무 오래 걸리는 경우 실행을 취소할 수 있습니다.

### <a name="using-the-sdk"></a>SDK 사용

SDK를 사용하여 실행을 취소하려면 [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) 다음 메서드를 사용합니다.

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

실행이 완료되었지만 오류가 포함된 경우(예: 잘못된 학습 스크립트가 사용된 경우) [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) 메서드를 사용하여 실패한 것으로 표시할 수 있습니다.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>CLI 사용

CLI를 사용하여 실행을 취소하려면 다음 명령을 사용합니다. 실행 `runid` ID로 바꾸기

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

자세한 내용은 [az ml 실행 취소를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)참조하십시오.

### <a name="using-azure-machine-learning-studio"></a>Azure 기계 학습 스튜디오 사용

다음 단계를 사용하여 스튜디오에서 실행을 취소하려면 다음 단계를 수행합니다.

1. 실험 또는 파이프라인 섹션에서 실행 중인 **파이프라인으로 이동합니다.** **Experiments** 

1. 취소할 파이프라인 실행 번호를 선택합니다.

1. 도구 모음에서 **취소를** 선택합니다.


## <a name="create-child-runs"></a>자식 실행 만들기

서로 다른 하이퍼매개 변수 튜닝 반복과 같이 하위 실행을 함께 그룹화하는 자식 실행 만들기

> [!NOTE]
> 자식 실행은 SDK를 사용하여만 만들 수 있습니다.

이 코드 예제에서는 메서드를 `hello_with_children.py` 사용하여 제출된 실행 내에서 5개의 [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) 자식 실행 일괄 처리를 만들기 위해 스크립트를 사용합니다.

```python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> 범위를 벗어나면 자식 실행이 완료된 것으로 자동으로 표시됩니다.

많은 자식 실행을 효율적으로 [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) 만들려면 메서드를 사용합니다. 각 생성은 네트워크 호출을 발생하므로 실행 일괄 처리를 하나씩 만드는 것보다 더 효율적입니다.

### <a name="submit-child-runs"></a>자식 실행 제출

상위 실행에서 자식 실행을 제출할 수도 있습니다. 이렇게 하면 부모 및 자식 실행의 계층을 만들 수 있습니다. 

자녀가 부모 실행과 다른 실행 구성을 사용하도록 할 수 있습니다. 예를 들어 자녀에게 GPU 기반 구성을 사용하는 동안 부모에 대해 덜 강력한 CPU 기반 구성을 사용할 수 있습니다. 또 다른 일반적인 욕망은 각 자식에게 서로 다른 인수와 데이터를 전달하는 것입니다. 자식 실행을 사용자 지정하려면 `RunConfiguration` 개체를 자식 `ScriptRunConfig` 생성자에게 전달합니다. 이 코드 예제는 상위 `ScriptRunConfig` 개체의 스크립트의 일부입니다.

- 명명된 `RunConfiguration` 계산 리소스 검색 을 만듭니다.`"gpu-compute"`
- 자식 `ScriptRunConfig` 개체에 전달될 다른 인수 값을 반복해서
- 사용자 지정 계산 리소스 및 인수를 사용하여 새 자식 실행을 만들고 제출합니다.
- 모든 자식이 실행될 때까지 블록

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig, RunConfiguration

run_config_for_aml_compute = RunConfiguration()
run_config_for_aml_compute.target = "gpu-compute"
run_config_for_aml_compute.environment.docker.enabled = True 

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], run_config = run_config_for_aml_compute)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

동일한 구성, 인수 및 입력을 사용하여 많은 자식 실행을 [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) 효율적으로 만들려면 메서드를 사용합니다. 각 생성은 네트워크 호출을 발생하므로 실행 일괄 처리를 하나씩 만드는 것보다 더 효율적입니다.

자식 실행 내에서 상위 실행 ID를 볼 수 있습니다.

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>쿼리 자식 실행

특정 부모의 자식 실행을 쿼리하려면 [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) 메서드를 사용합니다. 인수를 ``recursive = True`` 사용하면 자녀와 손자의 중첩 된 트리를 쿼리 할 수 있습니다.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>태그 및 실행 찾기

Azure 기계 학습에서 속성 및 태그를 사용하여 실행에서 중요한 정보를 구성하고 쿼리할 수 있습니다.

### <a name="add-properties-and-tags"></a>속성 및 태그 추가

#### <a name="using-the-sdk"></a>SDK 사용

검색 가능한 메타데이터를 실행에 추가하려면 [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) 메서드를 사용합니다. 예를 들어 다음 코드는 `"author"` 실행에 속성을 추가합니다.

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

속성은 변경할 수 없으므로 감사 목적으로 영구 레코드를 만듭니다. 다음 코드 예제에서는 이전 코드에서 `"azureml-user"` `"author"` 속성 값으로 이미 추가했기 때문에 오류가 발생합니다.

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

속성과 달리 태그는 변경할 수 있습니다. 실험의 소비자를 위해 검색 가능하고 의미 [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) 있는 정보를 추가하려면 이 방법을 사용합니다.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

간단한 문자열 태그를 추가할 수도 있습니다. 이러한 태그가 태그 사전에 키로 나타나면 `None`의 값이 있습니다.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>CLI 사용

> [!NOTE]
> CLI를 사용하면 태그를 추가하거나 업데이트할 수만 있습니다.

태그를 추가하거나 업데이트하려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

자세한 내용은 [az ml 실행 업데이트를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)참조하십시오.

### <a name="query-properties-and-tags"></a>쿼리 속성 및 태그

실험 내에서 실행을 쿼리하여 특정 속성 및 태그와 일치하는 실행 목록을 반환할 수 있습니다.

#### <a name="using-the-sdk"></a>SDK 사용

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>CLI 사용

Azure CLI는 속성 및 태그를 기반으로 실행을 필터링하는 데 사용할 수 있는 [JMESPath](http://jmespath.org) 쿼리를 지원합니다. Azure CLI에서 JMESPath 쿼리를 사용하려면 `--query` 매개 변수를 사용하여 지정합니다. 다음 예제는 속성 및 태그를 사용하는 기본 쿼리를 보여 주며 다음과 같은 예제를 보여 주며 있습니다.

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Azure CLI 결과 쿼리에 대한 자세한 내용은 [Azure CLI 명령 출력 쿼리를](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)참조하십시오.

### <a name="using-azure-machine-learning-studio"></a>Azure 기계 학습 스튜디오 사용

1. 파이프라인 섹션으로 **이동합니다.**

1. 검색 막대를 사용하여 태그, 설명, 실험 이름 및 제출자 이름을 사용하여 파이프라인을 필터링합니다.

## <a name="example-notebooks"></a>노트북 예제

다음 전자 필기공에서는 이 문서의 개념을 보여 줍니다.

* 로깅 API에 대한 자세한 내용은 [로깅 API 노트북을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)참조하십시오.

* Azure 기계 학습 SDK를 사용하는 실행 관리에 대한 자세한 내용은 [실행 관리 전자 필기장을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)참조하십시오.

## <a name="next-steps"></a>다음 단계

* 실험에 대한 메트릭을 기록하는 방법을 알아보려면 [교육 실행 중에 로그 메트릭을](how-to-track-experiments.md)참조하세요.
* Azure 기계 학습에서 리소스 및 로그를 모니터링하는 방법에 대해 알아보려면 [Azure 기계 학습 모니터링](monitor-azure-machine-learning.md)을 참조하십시오.
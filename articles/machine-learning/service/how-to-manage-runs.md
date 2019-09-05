---
title: Python에서 학습 실행 시작, 모니터링 및 취소
titleSuffix: Azure Machine Learning service
description: 기계 학습 실험을 시작 하 고, 상태를 설정 하 고, 태그를 구성 하 고, 구성 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2019
ms.openlocfilehash: b938d55749373711ddf2a19df12b987276b224ca
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376305"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Python에서 학습 실행 시작, 모니터링 및 취소

Python 및 [MACHINE LEARNING CLI](reference-azure-machine-learning-cli.md) [용 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 는 학습 및 실험을 위해 실행을 모니터링 하 고 구성 하 고 관리 하는 다양 한 방법을 제공 합니다.

이 문서에서는 다음 작업의 예를 보여 줍니다.

* 실행 성능을 모니터링 합니다.
* 취소 또는 실패 실행.
* 자식 실행을 만듭니다.
* 태그를 만들고 실행을 찾습니다.

## <a name="prerequisites"></a>필수 구성 요소

다음 항목이 필요 합니다.

* Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* [Azure Machine Learning 서비스 작업 영역](how-to-manage-workspace.md)입니다.

* Python 용 Azure Machine Learning SDK (버전 1.0.21 이상) 최신 버전의 SDK를 설치 하거나 업데이트 하려면 [Sdk 설치 또는 업데이트](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)를 참조 하세요.

    Azure Machine Learning SDK 버전을 확인 하려면 다음 코드를 사용 합니다.

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure Machine Learning 서비스에 대 한](reference-azure-machine-learning-cli.md) [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 및 CLI 확장입니다.

## <a name="start-a-run-and-its-logging-process"></a>실행 및 해당 로깅 프로세스를 시작 합니다.

### <a name="using-the-sdk"></a>SDK 사용

[Azureml](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) 패키지에서 [작업 영역](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [실험](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)및 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 클래스를 가져와서 실험을 설정 합니다.

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

[`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) 메서드를 사용 하 여 실행 및 해당 로깅 프로세스를 시작 합니다.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>CLI 사용

실험 실행을 시작 하려면 다음 단계를 사용 합니다.

1. 셸 또는 명령 프롬프트에서 Azure CLI를 사용 하 여 Azure 구독에 인증 합니다.

    ```azurecli-interactive
    az login
    ```

1. 학습 스크립트를 포함 하는 폴더에 작업 영역 구성을 연결 합니다. Azure Machine Learning `myworkspace` 서비스 작업 영역으로 대체 합니다. 을 `myresourcegroup` 작업 영역을 포함 하는 Azure 리소스 그룹으로 바꿉니다.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    이 명령은 .runconfig 및 `.azureml` conda environment 파일 예제를 포함 하는 하위 디렉터리를 만듭니다. 또한 Azure Machine Learning 작업 영역과 `config.json` 통신 하는 데 사용 되는 파일도 포함 되어 있습니다.

    자세한 내용은 [az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)항목을 참조 하세요.

2. 실행을 시작 하려면 다음 명령을 사용 합니다. 이 명령을 사용 하는 경우-c 매개 변수에 대해 .runconfig 파일의 이름 ( \*파일 시스템을 확인 하는 .runconfig 앞의 텍스트)을 지정 합니다.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > 명령은 .runconfig 파일의 `.azureml` 두 예제를 포함 하는 하위 디렉터리를 만들었습니다. `az ml folder attach`
    >
    > 실행 구성 개체를 프로그래밍 방식으로 만드는 Python 스크립트가 있는 경우 [.runconfig ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) 를 사용 하 여 .runconfig 파일로 저장할 수 있습니다.
    >
    > .Runconfig 파일 예제에 대 한 자세한 [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)내용은을 참조 하십시오.

    자세한 내용은 [az ml run submit-스크립트](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)를 참조 하세요.

## <a name="monitor-the-status-of-a-run"></a>실행 상태 모니터링

### <a name="using-the-sdk"></a>SDK 사용

[`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) 메서드를 사용 하 여 실행의 상태를 가져옵니다.

```python
print(notebook_run.get_status())
```

실행 ID, 실행 시간 및 실행에 대 한 추가 세부 정보를 가져오려면 [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) 메서드를 사용 합니다.

```python
print(notebook_run.get_details())
```

실행이 성공적으로 완료 되 면 [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) 메서드를 사용 하 여 완료 된 것으로 표시 합니다.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Python의 `with...as` 디자인 패턴을 사용 하는 경우 실행이 범위를 벗어나는 경우 자동으로 실행이 완료로 표시 됩니다. 실행을 완료 된 것으로 수동으로 표시할 필요가 없습니다.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>CLI 사용

1. 실험에 대 한 실행 목록을 보려면 다음 명령을 사용 합니다. 를 `experiment` 실험의 이름으로 바꿉니다.

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    이 명령은이 실험의 실행에 대 한 정보를 나열 하는 JSON 문서를 반환 합니다.

    자세한 내용은 [az ml 실험 list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)를 참조 하세요.

2. 특정 실행에 대 한 정보를 보려면 다음 명령을 사용 합니다. 을 `runid` 실행 ID로 바꿉니다.

    ```azurecli-interactive
    az ml run show -r runid
    ```

    이 명령은 실행에 대 한 정보를 나열 하는 JSON 문서를 반환 합니다.

    자세한 내용은 [az ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)를 참조 하세요.

## <a name="cancel-or-fail-runs"></a>취소 또는 실패 실행

오류가 발생 하거나 실행을 완료 하는 데 시간이 너무 오래 걸리는 경우 실행을 취소할 수 있습니다.

### <a name="using-the-sdk"></a>SDK 사용

SDK를 사용 하 여 실행을 취소 하려면 메서드 [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) 를 사용 합니다.

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

실행이 완료 되었지만 오류가 포함 된 경우 (예: 잘못 된 학습 스크립트를 사용한 경우) [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) 메서드를 사용 하 여 실패로 표시할 수 있습니다.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>CLI 사용

CLI를 사용 하 여 실행을 취소 하려면 다음 명령을 사용 합니다. 실행 `runid` ID로 대체 합니다.

```azurecli-interactive
az ml run cancel -r runid
```

자세한 내용은 [az ml run cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)을 참조 하십시오.

## <a name="create-child-runs"></a>자식 실행 만들기

다른 하이퍼 매개 변수 튜닝 반복의 경우와 같이 관련 실행을 함께 그룹화 하는 자식 실행을 만듭니다.

> [!NOTE]
> 자식 실행은 SDK를 사용 해야만 만들 수 있습니다.

이 코드 예제에서는 `hello_with_children.py` 스크립트를 사용 하 여 [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) 메서드를 사용 하 여 제출 된 실행 내에서 5 개의 자식 실행 일괄 처리를 만듭니다.

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
> 범위 밖으로 이동 하면 자식 실행이 자동으로 완료로 표시 됩니다.

많은 자식 실행을 효율적으로 만들려면 [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) 메서드를 사용 합니다. 각 생성에서 네트워크 호출이 발생 하기 때문에 실행 일괄 처리를 만드는 작업은 한 번에 하나씩 만드는 것 보다 효율적입니다.

### <a name="submit-child-runs"></a>자식 실행 제출

자식 실행은 부모 실행에서 제출할 수도 있습니다. 이를 통해 각각 서로 다른 계산 대상에서 실행 되는 부모 및 자식 실행의 계층 구조를 만들어 공통 부모 실행 ID로 연결할 수 있습니다.

[' Submit_child () '](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-config--tags-none----kwargs-) 메서드를 사용 하 여 부모 실행 내에서 자식 실행을 제출 합니다. 부모 실행 스크립트에서이 작업을 수행 하려면 컨텍스트 인스턴스의 ' ' submit_child ' ' ' 메서드를 사용 하 여 실행 컨텍스트를 가져오고 자식 실행을 제출 합니다.

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

자식 실행 내에서 부모 실행 ID를 볼 수 있습니다.

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>자식 쿼리 실행

특정 부모의 자식 실행을 쿼리하려면 [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) 메서드를 사용 합니다. ' ' ' Recursive = True ' ' ' 인수를 사용 하면 자식 및 손자의 중첩 된 트리를 쿼리할 수 있습니다.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>태그 및 실행 찾기

Azure Machine Learning 서비스에서 속성과 태그를 사용 하 여 중요 한 정보에 대 한 실행을 구성 하 고 쿼리 하는 데 도움이 됩니다.

### <a name="add-properties-and-tags"></a>속성 및 태그 추가

#### <a name="using-the-sdk"></a>SDK 사용

실행에 검색할 수 있는 [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) 메타 데이터를 추가 하려면 메서드를 사용 합니다. 예를 들어 다음 코드는 실행에 `"author"` 속성을 추가 합니다.

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

속성은 변경할 수 없으므로 감사를 위해 영구 레코드를 만듭니다. 다음 코드 예제에서는 이전 코드에서 `"azureml-user"` `"author"` 속성 값으로 이미를 추가 했으므로 오류가 발생 합니다.

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

속성과 달리 태그는 변경 가능 합니다. 실험의 소비자에 게 검색 가능 하 고 의미 있는 정보를 추가 [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) 하려면 메서드를 사용 합니다.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

간단한 문자열 태그를 추가할 수도 있습니다. 이러한 태그가 태그 사전에 키로 표시 되는 경우 값 `None`은입니다.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>CLI 사용

> [!NOTE]
> CLI를 사용 하 여 태그를 추가 하거나 업데이트할 수만 있습니다.

태그를 추가 하거나 업데이트 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

자세한 내용은 [az ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)를 참조 하십시오.

### <a name="query-properties-and-tags"></a>쿼리 속성 및 태그

특정 속성 및 태그와 일치 하는 실행 목록을 반환 하기 위해 실험 내에서 실행을 쿼리할 수 있습니다.

#### <a name="using-the-sdk"></a>SDK 사용

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>CLI 사용

Azure CLI는 속성 및 태그를 기준으로 실행을 필터링 하는 데 사용할 수 있는 [JMESPath](http://jmespath.org) 쿼리를 지원 합니다. Azure CLI와 함께 JMESPath 쿼리를 사용 하려면 `--query` 매개 변수를 사용 하 여 지정 합니다. 다음 예에서는 속성 및 태그를 사용 하는 기본 쿼리를 보여 줍니다.

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Azure CLI 결과를 쿼리 하는 방법에 대 한 자세한 내용은 [쿼리 Azure CLI 명령 출력](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)을 참조 하세요.

## <a name="example-notebooks"></a>노트북 예제

다음 노트북은이 문서의 개념을 보여 줍니다.

* 로깅 api에 대 한 자세한 내용은 [로깅 api 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb)을 참조 하세요.

* Azure Machine Learning SDK를 사용 하 여 실행을 관리 하는 방법에 대 한 자세한 내용은 [실행 중인 노트 관리](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 실험에 대 한 메트릭을 기록 하는 방법에 대 한 자세한 내용은 [학습 실행 중의 로그 메트릭](how-to-track-experiments.md)을 참조 하세요.

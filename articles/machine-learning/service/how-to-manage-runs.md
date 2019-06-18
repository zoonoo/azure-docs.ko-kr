---
title: 시작, 모니터링 및 python에서 교육 실행 취소
titleSuffix: Azure Machine Learning service
description: 시작 태그의 상태를 설정 하 고 기계 학습 실험을 구성 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: a93492b8ea97500fe3c761f3ac0c49f8c1342d09
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074968"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>시작, 모니터링 및 python에서 교육 실행 취소

합니다 [Python 용 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 하 고 [Machine Learning CLI](reference-azure-machine-learning-cli.md) 모니터링, 구성 및 학습 및 실험에 대 한 프로그램 실행을 관리 하는 다양 한 방법을 제공 합니다.

이 문서에서는 다음 작업의 예를 보여 줍니다.

* 성능 실행을 모니터링 합니다.
* 취소 메시지나 실행 됩니다.
* 자식 실행을 만듭니다.
* 태그를 찾아 실행 합니다.

## <a name="prerequisites"></a>필수 조건

다음 항목이 필요 합니다.

* Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* [Azure Machine Learning 서비스 작업 영역](setup-create-workspace.md)합니다.

* Python 용 Azure Machine Learning SDK (버전 1.0.21 이상). 를 설치 하거나 SDK의 최신 버전으로 업데이트를 참조 하세요 [설치 또는 업데이트 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)합니다.

    Azure Machine Learning SDK의 버전을 확인 하려면 다음 코드를 사용 합니다.

    ```Python
    print(azureml.core.VERSION)
    ```

* 합니다 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 하 고 [Azure Machine Learning 서비스에 대 한 CLI 확장](reference-azure-machine-learning-cli.md)합니다.

## <a name="start-a-run-and-its-logging-process"></a>실행 하 고 해당 로깅 프로세스를 시작 합니다.

### <a name="using-the-sdk"></a>SDK 사용

실험을 가져와서 설정 합니다 [작업 영역](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)를 [실험](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)를 [실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py), 및 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 클래스를 [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) 패키지 있습니다.

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

실행 하 고 해당 로깅 프로세스를 시작 합니다 [ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) 메서드.

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>CLI 사용

실험의 실행을 시작 하려면 다음 단계를 사용 합니다.

1. 셸 또는 명령 프롬프트에서 Azure CLI를 사용 하 여 Azure 구독에 인증.

    ```azurecli-interactive
    az login
    ```

1. 작업 영역 구성 학습 스크립트를 포함 하는 폴더에 연결 합니다. 대체 `myworkspace` Azure Machine Learning 서비스 작업 영역을 사용 하 여 합니다. 대체 `myresourcegroup` 작업 영역을 포함 하는 Azure 리소스 그룹을 사용 하 여:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    이 명령은 만듭니다는 `.azureml` 예제 runconfig 및 conda 환경 파일을 포함 하는 하위 디렉터리입니다. 또한 포함을 `config.json` Azure Machine Learning 작업 영역을 사용 하 여 통신 하는 데 사용 되는 파일입니다.

    자세한 내용은 [az ml 폴더 연결](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)합니다.

2. 실행을 시작 하려면 다음 명령을 사용 합니다. 이 명령을 사용할 때는 runconfig 파일의 이름을 지정 (앞에 있는 텍스트 \*.runconfig 파일 시스템에서 찾으려는 경우)-c 매개 변수에 대 한 합니다.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` 명령 생성을 `.azureml` 두 예제 runconfig 파일을 포함 하는 하위 디렉터리입니다. 
    >
    > Python 스크립트 실행된 구성 개체를 프로그래밍 방식으로 만드는 경우 사용할 수 [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) runconfig 파일으로 저장 합니다.
    >
    > 자세한 예제 runconfig 파일을 참조 하세요 [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)합니다.

    자세한 내용은 [제출 스크립트를 실행 하는 az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)합니다.

## <a name="monitor-the-status-of-a-run"></a>실행의 상태를 모니터링 합니다.

### <a name="using-the-sdk"></a>SDK 사용

사용 하 여 실행의 상태를 가져오려면 합니다 [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) 메서드.

```Python
print(notebook_run.get_status())
```

실행에 대 한 추가 세부 정보를 사용 합니다 [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) 메서드.

```Python
notebook_run.get_details()
```

실행 성공적으로 완료 되 면 사용 합니다 [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) 메서드를 완료 됨으로 표시 합니다.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Python의를 사용 하는 경우 `with...as` 패턴을 실행은 자동으로 표시 범위에서 실행 되 면 완료 된 것으로 자체입니다. 수동으로 실행을 완료로 표시 하지 않아도 됩니다.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

### <a name="using-the-cli"></a>CLI 사용

1. 실험에 대 한 실행의 목록을 보려면 다음 명령을 사용 합니다. 대체 `experiment` 실험의 이름:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    이 명령은이 실험에 대 한 실행에 대 한 정보를 나열 하는 JSON 문서를 반환 합니다.

    자세한 내용은 [az ml 실험 목록](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)합니다.

2. 특정 실행에 정보를 보려면 다음 명령을 사용 합니다. 대체 `runid` 실행의 ID를 사용 하 여:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    이 명령을 실행 하는 방법에 대 한 정보를 나열 하는 JSON 문서를 반환 합니다.

    자세한 내용은 [az ml 실행 표시](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)합니다.

## <a name="cancel-or-fail-runs"></a>취소 또는 실행 실패

실수 하는 경우, 실행 시간이 너무 오래 완료 하는 경우에 실행을 취소할 수 있습니다.

### <a name="using-the-sdk"></a>SDK 사용

SDK를 사용 하 여 실행을 취소 하려면 사용 합니다 [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) 메서드:

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

실행이 완료 되 면 오류가 포함 된 경우 (예를 들어 잘못 된 학습 스크립트)를 사용한 사용할 수는 [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) 실패로 표시 하는 방법입니다.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>CLI 사용

CLI를 사용 하 여 실행을 취소 하려면 다음 명령을 사용 합니다. 대체 `runid` 실행의 ID를 사용 하 여

```azurecli-interactive
az ml run cancel -r runid
```

자세한 내용은 [az ml 실행 취소](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)합니다.

## <a name="create-child-runs"></a>자식 실행 만들기

자식 그룹화 관련된 실행 같은 다양 한 하이퍼 매개 변수 튜닝 반복에 대 한 실행을 만듭니다.

> [!NOTE]
> SDK를 사용 하 여 자식 실행을 만들만 있습니다.

이 코드 예제에서는 합니다 `hello_with_children.py` 사용 하 여 제출 된 실행 내에서 5 개의 자식 실행의 일괄 처리를 만드는 스크립트를 [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) 메서드:

```Python
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
> 범위를 벗어납니다 전환 하며 완료 된 것으로 자식 실행은 자동으로 표시 됩니다.

자식에 하나씩 실행을 시작할 수도 있습니다 하지만 각 생성 한 네트워크 호출의 결과 이므로 실행의 일괄 처리를 전송 하는 보다 덜 효율적입니다.

특정 부모의 자식 실행 쿼리를 사용 합니다 [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) 메서드.

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>태그 및 실행 확인

Azure Machine Learning 서비스에서 속성 및 태그를 구성 하 고 중요 한 정보를 실행 하 고 쿼리 하는 데 사용할 수 있습니다.

### <a name="add-properties-and-tags"></a>속성 및 태그를 추가 합니다.

#### <a name="using-the-sdk"></a>SDK 사용

실행 하 고 검색 가능한 메타 데이터에 추가 하려면 사용 합니다 [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) 메서드. 예를 들어, 다음 코드를 추가 합니다 `"author"` 실행에 대 한 속성:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

감사 목적에 대 한 영구 레코드를 만들 속성은 변경할 수 없습니다. 다음 코드 예제에서는 오류가 이미 추가 했으므로 `"azureml-user"` 으로 `"author"` 위의 코드에서 속성 값:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

속성과 달리 태그는 변경할 수 있습니다. 실험의 소비자에 대 한 검색 및 의미 있는 정보를 추가 하려면 사용 합니다 [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) 메서드.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

또한 간단한 문자열 태그를 추가할 수 있습니다. 값이 있는 이러한 태그 태그 사전에 표시 되 면 `None`합니다.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>CLI 사용

> [!NOTE]
> CLI를 사용 하 여, 추가 하거나 태그 업데이트만 있습니다.

를 추가 하거나 태그를 업데이트 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

자세한 내용은 [업데이트를 실행 하는 az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)합니다.

### <a name="query-properties-and-tags"></a>쿼리 속성 및 태그

실행을 쿼리하면 특정 속성 및 태그와 일치 하는 실행의 목록을 반환 하는 실험 내에서.

#### <a name="using-the-sdk"></a>SDK 사용

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>CLI 사용

Azure CLI는 지원 [JMESPath](http://jmespath.org) 쿼리는 실행 속성 및 태그에 따라 필터링 할 수 있습니다. Azure CLI에서 JMESPath 쿼리를 사용 하려면 사용 하 여 지정 된 `--query` 매개 변수입니다. 다음 예제에서는 속성 및 태그를 사용 하 여 기본 쿼리를 보여 줍니다.

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Azure CLI 결과 쿼리에 대 한 자세한 내용은 참조 하세요. [쿼리 Azure CLI 명령 출력](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)합니다.

## <a name="example-notebooks"></a>노트북 예제

다음과 같은 notebook이 문서의 개념을 설명 합니다.

* 로깅 Api에 대 한 자세한 내용은 참조는 [로깅 API notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb)합니다.

* 관리 하는 방법에 대 한 자세한 내용은 Azure Machine Learning SDK를 사용 하 여 실행에 대 한 참조를 [notebook 실행 관리](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs)합니다.

## <a name="next-steps"></a>다음 단계

* 실험에 대 한 메트릭을 기록 하는 방법에 알아보려면 참조 [교육 실행 하는 동안 로그 메트릭](how-to-track-experiments.md)합니다.

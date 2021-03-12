---
title: Python에서 학습 실행 시작, 모니터링 및 취소
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Python SDK를 사용 하 여 기계 학습 실험을 시작 하 고, 상태를 관리 하 고, 관리 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 977498abb17fe592cef344f407a662d3b79749b7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634787"
---
# <a name="start-monitor-and-track-runs"></a>실행 시작, 모니터링 및 추적 

Python, [MACHINE LEARNING CLI](reference-azure-machine-learning-cli.md)및 [Azure Machine Learning STUDIO](https://ml.azure.com) [용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)는 학습 및 실험을 위해 실행을 모니터링 하 고 구성 하 고 관리 하는 다양 한 방법을 제공 합니다.

이 문서에서는 다음 작업의 예를 보여 줍니다.

* 실행 성능을 모니터링 합니다.
* 전자 메일 알림을 통해 실행 상태를 모니터링 합니다.
* 태그를 만들고 실행을 찾습니다.
* 실행 설명을 추가 합니다. 
* 검색을 실행 합니다. 
* 취소 또는 실패 실행.
* 자식 실행을 만듭니다.
 

> [!TIP]
> Azure Machine Learning 서비스 및 연결 된 Azure 서비스를 모니터링 하는 방법에 대 한 정보를 찾고 있는 경우 [Azure Machine Learning를 모니터링 하는 방법](monitor-azure-machine-learning.md)을 참조 하세요.
> 웹 서비스로 배포 된 모델 또는 IoT Edge 모듈을 모니터링 하는 방법에 대 한 정보를 찾고 있는 경우 [모델 데이터 수집](how-to-enable-data-collection.md) 및 [Application Insights로 모니터링](how-to-enable-app-insights.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

다음 항목이 필요 합니다.

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)입니다.

* Python 용 Azure Machine Learning SDK (버전 1.0.21 이상) 최신 버전의 SDK를 설치 하거나 업데이트 하려면 [Sdk 설치 또는 업데이트](/python/api/overview/azure/ml/install)를 참조 하세요.

    Azure Machine Learning SDK 버전을 확인 하려면 다음 코드를 사용 합니다.

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure Machine Learning에 대 한](reference-azure-machine-learning-cli.md) [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) 및 CLI 확장입니다.


## <a name="monitor-run-performance"></a>실행 성능 모니터링

* 실행 및 해당 로깅 프로세스를 시작 합니다.

    # <a name="python"></a>[Python](#tab/python)
    
    1. [Azureml](/python/api/azureml-core/azureml.core) 패키지에서 [작업 영역](/python/api/azureml-core/azureml.core.workspace.workspace), [실험](/python/api/azureml-core/azureml.core.experiment.experiment), [실행](/python/api/azureml-core/azureml.core.run%28class%29)및 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 클래스를 가져와서 실험을 설정 합니다.
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. 메서드를 사용 하 여 실행 및 해당 로깅 프로세스를 시작 [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) 합니다.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    실험 실행을 시작 하려면 다음 단계를 사용 합니다.
    
    1. 셸 또는 명령 프롬프트에서 Azure CLI를 사용 하 여 Azure 구독에 인증 합니다.
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. 학습 스크립트를 포함 하는 폴더에 작업 영역 구성을 연결 합니다. `myworkspace`을 Azure Machine Learning 작업 영역으로 바꿉니다. `myresourcegroup`을 작업 영역을 포함 하는 Azure 리소스 그룹으로 바꿉니다.
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        이 명령은 예제 runconfig 및 conda 환경 파일을 포함하는 `.azureml` 하위 디렉터리를 만듭니다. 또한 Azure Machine Learning 작업 영역과 통신하는 데 사용되는 `config.json` 파일을 포함합니다.
    
        자세한 내용은 [az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)를 참조하세요.
    
    2. 실행을 시작 하려면 다음 명령을 사용 합니다. 이 명령을 사용하는 경우 -c 매개 변수 가까이에 runconfig 파일의 이름(파일 시스템을 보고 있는 경우 \*.runconfig 앞의 텍스트)을 지정합니다.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > `az ml folder attach`명령은 `.azureml` .runconfig 파일의 두 예제를 포함 하는 하위 디렉터리를 만들었습니다.
        >
        > 실행 구성 개체를 프로그래밍 방식으로 만드는 Python 스크립트가 있는 경우 [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-)를 사용하여 runconfig 파일로 저장합니다.
        >
        > .Runconfig 파일 예제에 대 한 자세한 내용은을 참조 하십시오 [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        자세한 내용은 [az ml run submit-script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)를 참조하세요.

    # <a name="studio"></a>[스튜디오](#tab/azure-studio)

    Azure Machine Learning 디자이너에서 모델을 학습 하는 방법에 대 한 예제는 [자습서: 디자이너를 사용 하 여 자동차 가격 예측](tutorial-designer-automobile-price-train-score.md)을 참조 하세요.

    ---

* 실행 상태 모니터링

    # <a name="python"></a>[Python](#tab/python)
    
    * 메서드를 사용 하 여 실행의 상태를 가져옵니다 [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) .
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * 실행 ID, 실행 시간 및 실행에 대 한 추가 세부 정보를 가져오려면 메서드를 사용 [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) 합니다.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * 실행이 성공적으로 완료 되 면 메서드를 사용 하 여 완료 된 것 [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) 으로 표시 합니다.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Python의 디자인 패턴을 사용 하는 경우 실행 `with...as` 이 범위를 벗어나는 경우 자동으로 실행이 완료로 표시 됩니다. 실행을 완료 된 것으로 수동으로 표시할 필요가 없습니다.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    * 실험에 대 한 실행 목록을 보려면 다음 명령을 사용 합니다. `experiment`를 실험의 이름으로 바꿉니다.
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        이 명령은이 실험의 실행에 대 한 정보를 나열 하는 JSON 문서를 반환 합니다.
    
        자세한 내용은 [az ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)를 참조하세요.
    
    * 특정 실행에 대 한 정보를 보려면 다음 명령을 사용 합니다. 을 `runid` 실행 ID로 바꿉니다.
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        이 명령은 실행에 대 한 정보를 나열 하는 JSON 문서를 반환 합니다.
    
        자세한 내용은 [az ml run show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)를 참조 하세요.
    
    
    # <a name="studio"></a>[스튜디오](#tab/azure-studio)
    
    스튜디오에서 실행을 보려면 다음을 수행 합니다. 
    
    1. **실험** 탭으로 이동 합니다.
    
    1. **모든 실험** 을 선택 하 여 실험에서 모든 실행을 보거나 **모든 실행** 을 선택 하 여 작업 영역에서 제출 된 모든 실행을 표시 합니다.
    
        **모든 실행 '** 페이지에서 태그, 실험, 계산 대상 등을 기준으로 실행 목록을 필터링 하 여 작업을 보다 효율적으로 구성 하 고 범위를 지정할 수 있습니다.  
    
    1. 결과를 비교 하거나 차트를 추가 하거나 필터를 적용 하려면 실행을 선택 하 여 페이지에 대 한 사용자 지정을 수행 합니다. 이러한 변경 내용을 **사용자 지정 보기로** 저장할 수 있으므로 작업으로 쉽게 돌아갈 수 있습니다. 작업 영역 권한이 있는 사용자는 사용자 지정 보기를 편집 하거나 볼 수 있습니다. 또한 **공유 보기** 를 선택 하 여 향상 된 공동 작업을 위해 팀 멤버와 사용자 지정 보기를 공유 합니다.   
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="스크린샷: 사용자 지정 보기 만들기":::
    
    1. 실행 로그를 보려면 특정 실행을 선택 하 고 **출력 + 로그** 탭에서 실행에 대 한 진단 및 오류 로그를 확인할 수 있습니다.
    
    ---

## <a name="monitor-the-run-status-by-email-notification"></a>전자 메일 알림으로 실행 상태 모니터링

1. [Azure Portal](https://ms.portal.azure.com/)의 왼쪽 탐색 모음에서 **모니터** 탭을 선택 합니다. 

1. **진단 설정** 을 선택 하 고 **+ 진단 설정 추가** 를 선택 합니다.

    ![전자 메일 알림에 대 한 진단 설정 스크린샷](./media/how-to-manage-runs/diagnostic-setting.png)

1. 진단 설정에서 
    1. **범주 세부 정보** 에서 **Amlrunstatuschangedevent** 를 선택 합니다. 
    1. **대상 세부 정보** 에서 **Log Analytics 작업 영역으로 보내기** 를 선택 하 고 **구독** 및 **Log Analytics 작업 영역** 을 지정 합니다. 

    > [!NOTE]
    > **Azure Log Analytics 작업 영역은** **Azure Machine Learning 서비스 작업 영역과** 는 다른 유형의 azure 리소스입니다. 해당 목록에 옵션이 없는 경우 [Log Analytics 작업 영역을 만들](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace)수 있습니다. 
    
    ![전자 메일 알림을 저장할 위치](./media/how-to-manage-runs/log-location.png)

1. **로그** 탭에서 **새 경고 규칙** 을 추가 합니다. 

    ![새 경고 규칙](./media/how-to-manage-runs/new-alert-rule.png)

1. [Azure Monitor를 사용 하 여 로그 경고를 만들고 관리 하는 방법을](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log)참조 하세요.

## <a name="run-description"></a>실행 설명 

실행에 더 많은 컨텍스트 및 정보를 제공 하기 위해 실행에 실행 설명을 추가할 수 있습니다. 실행 목록에서 이러한 설명을 검색 하 고 실행 목록에 열로 실행 설명을 추가할 수도 있습니다. 

실행에 대 한 실행 **세부 정보** 페이지로 이동 하 여 편집 또는 연필 아이콘을 선택 하 여 실행에 대 한 설명을 추가, 편집 또는 삭제 합니다. 실행 목록에 대 한 변경 내용을 유지 하려면 기존 사용자 지정 뷰나 새 사용자 지정 보기에 대 한 변경 내용을 저장 합니다. Markdown format은 아래와 같이 이미지를 포함 하 고 딥 링크를 허용 하는 실행 설명에 대해 지원 됩니다.

:::image type="content" source="media/how-to-manage-runs/run-description.gif" alt-text="스크린샷: 실행 설명 만들기"::: 

## <a name="tag-and-find-runs"></a>태그 및 실행 찾기

Azure Machine Learning에서 속성과 태그를 사용 하 여 중요 한 정보에 대 한 실행을 구성 하 고 쿼리 하는 데 도움이 됩니다.

* 속성 및 태그 추가

    # <a name="python"></a>[Python](#tab/python)
    
    실행에 검색할 수 있는 메타 데이터를 추가 하려면 [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) 메서드를 사용 합니다. 예를 들어 다음 코드는 `"author"` 실행에 속성을 추가 합니다.
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    속성은 변경할 수 없으므로 감사를 위해 영구 레코드를 만듭니다. 다음 코드 예제에서는 `"azureml-user"` `"author"` 이전 코드에서 속성 값으로 이미를 추가 했으므로 오류가 발생 합니다.
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    속성과 달리 태그는 변경 가능 합니다. 실험의 소비자에 게 검색 가능 하 고 의미 있는 정보를 추가 하려면 메서드를 사용 [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) 합니다.
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    간단한 문자열 태그를 추가할 수도 있습니다. 이러한 태그가 태그 사전에 키로 표시 되는 경우 값은 `None` 입니다.
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    > [!NOTE]
    > CLI를 사용 하 여 태그를 추가 하거나 업데이트할 수만 있습니다.
    
    태그를 추가 하거나 업데이트 하려면 다음 명령을 사용 합니다.
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    자세한 내용은 [az ml run update](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)를 참조 하십시오.
    
    # <a name="studio"></a>[스튜디오](#tab/azure-studio)
    
    스튜디오에서 실행 태그를 추가, 편집 또는 삭제할 수 있습니다. 실행에 대 한 **실행 세부 정보** 페이지로 이동 하 여 편집 또는 연필 아이콘을 선택 하 여 실행에 대 한 태그를 추가, 편집 또는 삭제 합니다. 실행 목록 페이지에서 이러한 태그를 검색 하 고 필터링 할 수도 있습니다.
    
    :::image type="content" source="media/how-to-manage-runs/run-tags.gif" alt-text="스크린샷: 실행 태그 추가, 편집 또는 삭제":::
    
    ---

* 쿼리 속성 및 태그

    특정 속성 및 태그와 일치 하는 실행 목록을 반환 하기 위해 실험 내에서 실행을 쿼리할 수 있습니다.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Azure CLI는 속성 및 태그를 기준으로 실행을 필터링 하는 데 사용할 수 있는 [JMESPath](http://jmespath.org) 쿼리를 지원 합니다. Azure CLI와 함께 JMESPath 쿼리를 사용 하려면 매개 변수를 사용 하 여 지정 `--query` 합니다. 다음 예에서는 속성 및 태그를 사용 하는 몇 가지 쿼리를 보여 줍니다.
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Azure CLI 결과를 쿼리 하는 방법에 대 한 자세한 내용은 [쿼리 Azure CLI 명령 출력](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)을 참조 하세요.
    
    # <a name="studio"></a>[스튜디오](#tab/azure-studio)
    
    특정 실행을 검색 하려면  **모든 실행** 목록으로 이동 합니다. 여기에는 두 가지 옵션이 있습니다.
    
    1. **필터 추가** 단추를 사용 하 고 태그에 필터를 선택 하 여 실행에 할당 된 태그를 기준으로 실행을 필터링 합니다. <br><br>
    또는
    
    1. 실행 상태, 설명, 실험 이름 및 제출자 이름과 같은 실행 메타 데이터를 검색 하 여 신속 하 게 실행을 찾으려면 검색 표시줄을 사용 합니다. 
    
## <a name="cancel-or-fail-runs"></a>취소 또는 실패 실행

오류가 발생 하거나 실행을 완료 하는 데 시간이 너무 오래 걸리는 경우 실행을 취소할 수 있습니다.

# <a name="python"></a>[Python](#tab/python)

SDK를 사용 하 여 실행을 취소 하려면 메서드를 사용 합니다 [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) .

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

실행이 완료 되었지만 오류가 포함 된 경우 (예: 잘못 된 학습 스크립트를 사용한 경우) 메서드를 사용 [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) 하 여 실패로 표시할 수 있습니다.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI를 사용 하 여 실행을 취소 하려면 다음 명령을 사용 합니다. `runid`실행 ID로 대체 합니다.

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

자세한 내용은 [az ml run cancel](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)을 참조 하십시오.

# <a name="studio"></a>[스튜디오](#tab/azure-studio)

스튜디오에서 실행을 취소 하려면 다음 단계를 사용 합니다.

1. **실험** 또는 **파이프라인** 섹션에서 실행 중인 파이프라인으로 이동 합니다. 

1. 취소 하려는 파이프라인 실행 번호를 선택 합니다.

1. 도구 모음에서 **취소** 를 선택 합니다.

---

## <a name="create-child-runs"></a>자식 실행 만들기

다른 하이퍼 매개 변수 튜닝 반복의 경우와 같이 관련 실행을 함께 그룹화 하는 자식 실행을 만듭니다.

> [!NOTE]
> 자식 실행은 SDK를 사용 해야만 만들 수 있습니다.

이 코드 예제에서는 스크립트를 사용 하 여 `hello_with_children.py` 메서드를 사용 하 여 제출 된 실행 내에서 5 개의 자식 실행 일괄 처리를 만듭니다 [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) .

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> 범위 밖으로 이동 하면 자식 실행이 자동으로 완료로 표시 됩니다.

많은 자식 실행을 효율적으로 만들려면 메서드를 사용 [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) 합니다. 각 생성에서 네트워크 호출이 발생 하므로, 실행 일괄 처리를 만드는 작업은 하나를 하나씩 만드는 것 보다 효율적입니다.

### <a name="submit-child-runs"></a>자식 실행 제출

자식 실행은 부모 실행에서 제출할 수도 있습니다. 이렇게 하면 부모 및 자식 실행의 계층 구조를 만들 수 있습니다. Parentless 자식 실행을 만들 수 없습니다. 부모 실행은 아무 작업도 수행 하지 않지만 자식 실행을 시작 하더라도 계층 구조를 만들어야 합니다. 모든 실행의 상태는 독립적입니다. `"Completed"` 하나 이상의 자식 실행이 취소 되거나 실패 한 경우에도 부모는 성공 상태로 있을 수 있습니다.  

부모 실행과 다른 실행 구성을 사용 하 여 자녀의 실행을 원할 수 있습니다. 예를 들어, 부모에 대해 더 강력 하 고 CPU 기반 구성을 사용할 수 있으며, 자식에 대 한 GPU 기반 구성을 사용할 수 있습니다. 또 다른 일반적인 방법은 서로 다른 인수 및 데이터를 전달 하는 것입니다. 자식 실행을 사용자 지정 하려면 `ScriptRunConfig` 자식 실행에 대 한 개체를 만듭니다. 아래 코드에서는 다음을 수행 합니다.

- 작업 영역에서 라는 계산 리소스를 검색 `"gpu-cluster"` 합니다. `ws`
- 자식 개체에 전달 될 다른 인수 값을 반복 합니다. `ScriptRunConfig`
- 사용자 지정 계산 리소스 및 인수를 사용 하 여 새 자식 실행을 만들고 제출 합니다.
- 모든 자식 실행이 완료 될 때까지 차단 합니다.

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

동일한 구성, 인수 및 입력을 사용 하 여 여러 자식 실행을 효율적으로 만들려면 [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) 메서드를 사용 합니다. 각 생성에서 네트워크 호출이 발생 하므로, 실행 일괄 처리를 만드는 작업은 하나를 하나씩 만드는 것 보다 효율적입니다.

자식 실행 내에서 부모 실행 ID를 볼 수 있습니다.

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>자식 쿼리 실행

특정 부모의 자식 실행을 쿼리하려면 메서드를 사용 합니다 [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) . ``recursive = True``인수를 사용 하면 자식 및 손자의 중첩 트리를 쿼리할 수 있습니다.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>부모 또는 루트 실행에 기록

필드를 사용 `Run.parent` 하 여 현재 자식 실행을 시작한 실행에 액세스할 수 있습니다. 이에 대 한 일반적인 사용 사례는 로그 결과를 한 장소에 통합 하려는 경우입니다. 자식 실행은 비동기적으로 실행 되며 부모에서 자식 실행이 완료 될 때까지 대기 하는 기능 이상으로 인해 순서나 동기화가 보장 되지 않습니다.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="example-notebooks"></a>노트북 예제

다음 노트북은이 문서의 개념을 보여 줍니다.

* 로깅 api에 대 한 자세한 내용은 [로깅 api 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)을 참조 하세요.

* Azure Machine Learning SDK를 사용 하 여 실행을 관리 하는 방법에 대 한 자세한 내용은 [실행 중인 노트 관리](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 실험에 대 한 메트릭을 기록 하는 방법에 대 한 자세한 내용은 [학습 실행 중의 로그 메트릭](how-to-track-experiments.md)을 참조 하세요.
* Azure Machine Learning에서 리소스와 로그를 모니터링 하는 방법을 알아보려면 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조 하세요.

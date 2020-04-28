---
title: ParallelRunStep 디버그 및 문제 해결
titleSuffix: Azure Machine Learning
description: Python 용 Azure Machine Learning SDK의 machine learning 파이프라인에서 ParallelRunStep를 디버그 하 고 문제를 해결 합니다. 파이프라인을 사용 하 여 개발할 때 발생 하는 일반적인 문제 및 원격 실행 전후에 스크립트를 디버그 하는 데 도움이 되는 팁에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122965"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>ParallelRunStep 디버그 및 문제 해결
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)에서 [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) 클래스를 디버그 하 고 문제를 해결 하는 방법에 대해 알아봅니다.

## <a name="testing-scripts-locally"></a>로컬에서 스크립트 테스트

Machine learning 파이프라인에 대 한 [로컬 테스트 스크립트 섹션](how-to-debug-pipelines.md#testing-scripts-locally) 을 참조 하세요. ParallelRunStep는 ML 파이프라인에서 한 단계로 실행 되므로 동일한 답변이 둘 다에 적용 됩니다.

## <a name="debugging-scripts-from-remote-context"></a>원격 컨텍스트에서 스크립트 디버깅

실제 파이프라인에서 점수 매기기 스크립트를 디버깅 하기 위해 로컬에서 점수 매기기 스크립트를 디버깅 하는 것을 전환 하는 것은 어려울 수 있습니다. 포털에서 로그를 찾는 방법에 대 한 자세한 내용은 [원격 컨텍스트에서 스크립트 디버깅의 machine learning 파이프라인 섹션](how-to-debug-pipelines.md#debugging-scripts-from-remote-context)을. 이 단원의 정보는 병렬 단계 실행에도 적용 됩니다.

예를 들어 로그 파일 `70_driver_log.txt` 은 병렬 실행 단계 코드를 시작 하는 컨트롤러의 정보를 포함 합니다.

병렬 실행 작업의 분산 된 특성으로 인해 여러 원본의 로그가 있습니다. 그러나 높은 수준의 정보를 제공 하는 두 개의 통합 파일이 생성 됩니다.

- `~/logs/overview.txt`:이 파일은 지금까지 생성 된 미니 일괄 처리 (작업) 수와 지금까지 처리 된 미니 일괄 처리 수에 대 한 높은 수준의 정보를 제공 합니다. 이 끝에서는 작업의 결과를 보여 줍니다. 작업에 실패 한 경우 오류 메시지 및 문제 해결을 시작할 위치를 표시 합니다.

- `~/logs/sys/master.txt`:이 파일은 실행 중인 작업의 마스터 노드 (orchestrator 라고도 함)를 제공 합니다. 작업 만들기, 진행률 모니터링, 실행 결과를 포함 합니다.

EntryScript를 사용 하 여 항목 스크립트에서 생성 되는 로그입니다 .로 거 및 print 문은 다음 파일에 있습니다.

- `~/logs/user/<ip_address>/Process-*.txt`:이 파일은 EntryScript .로 거를 사용 하 여 entry_script에서 작성 한 로그를 포함 합니다. 또한 entry_script의 print 문 (stdout)이 포함 되어 있습니다.

각 노드가 점수 스크립트를 실행 하는 방법을 완전히 이해 해야 하는 경우 각 노드에 대 한 개별 프로세스 로그를 확인 합니다. 프로세스 로그는 작업자 노드로 그룹화 된 `sys/worker` 폴더에서 찾을 수 있습니다.

- `~/logs/sys/worker/<ip_address>/Process-*.txt`:이 파일은 작업자에서 선택 하거나 완료할 때 각 미니 일괄 처리에 대 한 자세한 정보를 제공 합니다. 각 미니 일괄 처리에 대해이 파일에는 다음이 포함 됩니다.

    - 작업자 프로세스의 IP 주소 및 PID 
    - 항목의 총 수, 성공적으로 처리 된 항목 수 및 실패 한 항목 수입니다.
    - 시작 시간, 기간, 프로세스 시간 및 실행 메서드 시간입니다.

또한 각 작업자에 대 한 프로세스의 리소스 사용에 대 한 정보를 찾을 수 있습니다. 이 정보는 CSV 형식이 며에 `~/logs/sys/perf/<ip_address>/`있습니다. 단일 노드의 경우 작업 파일은에서 `~logs/sys/perf`사용할 수 있습니다. 예를 들어 리소스 사용률을 확인 하는 경우 다음 파일을 확인 합니다.

- `Process-*.csv`: 작업자 프로세스 리소스 사용량을 기준으로 합니다. 
- `sys.csv`: 노드당 로그.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>원격 컨텍스트에서 사용자 스크립트의 로그를 어떻게 할까요? 하 시겠습니까?
아래 샘플 코드에 표시 된 것 처럼 EntryScript에서로 거를 가져와 포털의 **로그/사용자** 폴더에 로그가 표시 되도록 할 수 있습니다.

**로 거를 사용 하는 샘플 항목 스크립트입니다.**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>예를 들어, 조회 테이블을 포함 하는 파일 또는 파일을 모든 작업자에 게 전달 하려면 어떻게 해야 하나요?

측면 입력을 포함 하 고 작업 영역에 등록 하는 [데이터 집합](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) 개체를 생성 합니다. 그런 다음, 다음과 같이 유추 스크립트 (예: init () 메서드)에서 액세스할 수 있습니다.

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>다음 단계

* ParallelRunStep 클래스에 대 한 자세한 내용은 [패키지에](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) 대 [한 자세한 내용은](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) SDK 참조를 참조 하세요.

* 병렬 실행 단계에서 파이프라인 사용에 대 한 [고급 자습서](tutorial-pipeline-batch-scoring-classification.md) 를 따르세요.

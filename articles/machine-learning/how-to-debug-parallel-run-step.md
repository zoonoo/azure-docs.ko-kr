---
title: 병렬 실행 단계 디버그 및 문제 해결
titleSuffix: Azure Machine Learning
description: 파이썬용 Azure 기계 학습 SDK의 기계 학습 파이프라인에서 병렬 RunStep을 디버깅하고 해결합니다. 파이프라인 을 사용하여 개발하는 일반적인 함정과 원격 실행 전과 실행 중에 스크립트를 디버깅하는 데 도움이 되는 팁을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122965"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>병렬 실행 단계 디버그 및 문제 해결
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 [Azure 기계 학습 SDK에서](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) 클래스를 디버깅하고 해결하는 방법을 배웁니다.

## <a name="testing-scripts-locally"></a>로컬스크립트 테스트

기계 학습 파이프라인에 대한 [스크립트 테스트 섹션을](how-to-debug-pipelines.md#testing-scripts-locally) 참조하십시오. ParallelRunStep은 ML 파이프라인의 단계로 실행되므로 동일한 대답이 둘 다에 적용됩니다.

## <a name="debugging-scripts-from-remote-context"></a>원격 컨텍스트에서 스크립트 디버깅

로컬에서 채점 스크립트를 디버깅하는 것에서 실제 파이프라인에서 점수 매기기 스크립트를 디버깅하는 것으로 전환하는 것은 어려울 수 있습니다. 포털에서 로그를 찾는 방법에 대한 자세한 내용은 [원격 컨텍스트에서 스크립트를 디버깅하는 기계 학습 파이프라인 섹션입니다.](how-to-debug-pipelines.md#debugging-scripts-from-remote-context) 해당 섹션의 정보는 병렬 단계 실행에도 적용됩니다.

예를 들어 로그 `70_driver_log.txt` 파일에는 병렬 실행 단계 코드를 실행하는 컨트롤러의 정보가 포함되어 있습니다.

병렬 실행 작업의 분산 특성으로 인해 여러 다른 소스의 로그가 있습니다. 그러나 상위 수준 정보를 제공하는 두 개의 통합 파일이 만들어집니다.

- `~/logs/overview.txt`: 이 파일은 지금까지 생성된 미니 일괄 처리 수와 지금까지 처리된 미니 일괄 처리 수에 대한 고급 정보를 제공합니다. 이 끝에서 작업의 결과를 보여 표시합니다. 작업이 실패하면 오류 메시지와 트러블슈팅을 시작할 위치가 표시됩니다.

- `~/logs/sys/master.txt`: 이 파일은 실행 중인 작업의 마스터 노드(오케스트레이터라고도 함)를 제공합니다. 작업 생성, 진행률 모니터링, 실행 결과가 포함됩니다.

EntryScript.logger 및 인쇄 문을 사용하여 입력 스크립트에서 생성된 로그는 다음 파일에서 찾을 수 있습니다.

- `~/logs/user/<ip_address>/Process-*.txt`: 이 파일에는 EntryScript.logger를 사용하여 entry_script 작성된 로그가 포함되어 있습니다. 또한 entry_script 인쇄 문(stdout)도 포함되어 있습니다.

각 노드가 점수 스크립트를 실행하는 방법을 완전히 이해해야 하는 경우 각 노드에 대한 개별 프로세스 로그를 살펴봅니다. 프로세스 로그는 `sys/worker` 작업자 노드별로 그룹화된 폴더에서 찾을 수 있습니다.

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: 이 파일은 작업자가 픽업하거나 완료할 때 각 미니 배치에 대한 자세한 정보를 제공합니다. 각 미니 일괄 처리에 대해 이 파일에는 다음이 포함됩니다.

    - 작업자 프로세스의 IP 주소 및 PID입니다. 
    - 총 항목 수, 성공적으로 처리된 항목 수 및 실패한 항목 수입니다.
    - 시작 시간, 기간, 프로세스 시간 및 실행 방법 시간입니다.

각 작업자에 대한 프로세스의 리소스 사용에 대한 정보도 찾을 수 있습니다. 이 정보는 CSV 형식으로 되어 `~/logs/sys/perf/<ip_address>/`있으며 에 있습니다. 단일 노드의 경우 작업 파일은 `~logs/sys/perf`에서 사용할 수 있습니다. 예를 들어 리소스 사용률을 확인할 때 다음 파일을 살펴봅니다.

- `Process-*.csv`: 작업자당 프로세스 리소스 사용량입니다. 
- `sys.csv`: 노드 로그당.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>원격 컨텍스트에서 사용자 스크립트를 로그온하려면 어떻게 해야 합니까?
아래 샘플 코드에 표시된 대로 Entryr에서 로거를 사용하여 로그가 포털의 **로그/사용자** 폴더에 표시되도록 할 수 있습니다.

**로거를 사용하는 샘플 입력 스크립트:**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>조회 테이블이 포함된 파일이나 파일과 같은 측면 입력을 모든 작업자에게 전달하려면 어떻게 해야 합니까?

측면 입력이 포함된 [데이터 집합](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) 개체를 생성하고 작업 영역에 등록합니다. 그 후 다음과 같이 추론 스크립트 (예 : init() 메서드)에서 액세스 할 수 있습니다.

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>다음 단계

* [Azureml-contrib-파이프라인-단계](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) 패키지 및 ParallelRunStep 클래스에 대 한 [설명서에](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) 대 한 도움말은 SDK 참조를 참조 하십시오.

* 병렬 실행 단계가 있는 파이프라인 사용에 대한 [고급 자습서를](tutorial-pipeline-batch-scoring-classification.md) 따릅니다.

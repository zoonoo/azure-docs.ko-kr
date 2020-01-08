---
title: ParallelRunStep 디버그 및 문제 해결
titleSuffix: Azure Machine Learning
description: Python 용 Azure Machine Learning SDK에서 기계 학습 파이프라인을 디버그 하 고 문제를 해결 합니다. 파이프라인을 사용 하 여 개발할 때 발생 하는 일반적인 문제 및 원격 실행 전후에 스크립트를 디버그 하는 데 도움이 되는 팁에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: 20e88da6721a6715a7f31f5c423e305625528383
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540608"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>ParallelRun를 사용 하 여 디버그 및 문제 해결
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)에서 [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) 클래스를 디버그 하 고 문제를 해결 하는 방법에 대해 알아봅니다.

## <a name="testing-scripts-locally"></a>로컬에서 스크립트 테스트

Machine learning 파이프라인에 대 한 [로컬 테스트 스크립트 섹션](how-to-debug-pipelines.md#testing-scripts-locally) 을 참조 하세요. ParallelRunStep는 ML 파이프라인에서 한 단계로 실행 되므로 동일한 답변이 둘 다에 적용 됩니다.

## <a name="debugging-scripts-from-remote-context"></a>원격 컨텍스트에서 스크립트 디버깅

실제 파이프라인에서 점수 매기기 스크립트를 디버깅 하기 위해 로컬에서 점수 매기기 스크립트를 디버깅 하는 것을 전환 하는 것은 어려울 수 있습니다. 포털에서 로그를 찾는 방법에 대 한 자세한 내용은 [원격 컨텍스트에서 스크립트 디버깅의 machine learning 파이프라인 섹션](how-to-debug-pipelines.md#debugging-scripts-from-remote-context)을. 해당 섹션의 정보는 일괄 처리 유추 실행에도 적용 됩니다.

예를 들어 로그 파일에는 다음과 같은 `70_driver_log.txt` 포함 됩니다. 

* 스크립트를 실행 하는 동안 모든 인쇄 된 문
* 스크립트의 스택 추적입니다.

일괄 처리 유추 작업의 분산 특성으로 인해 서로 다른 여러 원본의 로그가 있습니다. 그러나 높은 수준의 정보를 제공 하는 두 개의 통합 파일이 생성 됩니다.

- `~/logs/overview.txt`:이 파일은 지금까지 생성 된 미니 일괄 처리 (작업)의 수와 지금까지 처리 된 미니 일괄 처리 수에 대 한 개략적인 정보를 제공 합니다. 이 끝에서는 작업의 결과를 보여 줍니다. 작업에 실패 한 경우 오류 메시지 및 문제 해결을 시작할 위치를 표시 합니다.

- `~/logs/master.txt`:이 파일은 실행 중인 작업의 마스터 노드 (orchestrator 라고도 함) 보기를 제공 합니다. 작업 만들기, 진행률 모니터링, 실행 결과를 포함 합니다.

각 노드가 점수 스크립트를 실행 하는 방법을 완전히 이해 해야 하는 경우 각 노드에 대 한 개별 프로세스 로그를 확인 합니다. 프로세스 로그는 작업자 노드로 그룹화 된 `worker` 폴더에서 찾을 수 있습니다.

- `~/logs/worker/<ip_address>/Process-*.txt`:이 파일은 작업자가 선택 하거나 완료할 때 각 미니 일괄 처리에 대 한 자세한 정보를 제공 합니다. 각 미니 일괄 처리에 대해이 파일에는 다음이 포함 됩니다.

    - 작업자 프로세스의 IP 주소 및 PID 
    - 총 항목 수와 성공적으로 처리 된 항목 수입니다. 
    - 벽 시계 시간 (`start1` 및 `end1`)의 시작 및 종료 시간입니다. 
    - 프로세서 시간 (`start2` 및 `end2`)의 시작 및 종료 시간입니다. 

또한 각 작업자에 대 한 프로세스의 리소스 사용에 대 한 정보를 찾을 수 있습니다. 이 정보는 CSV 형식이 며 `~/logs/performance/<ip_address>/`에 있습니다. 예를 들어 리소스 사용률을 확인 하는 경우 다음 파일을 확인 합니다.

- `process_resource_monitor_<ip>_<pid>.csv`: 작업자 프로세스 리소스 사용량을 기준으로 합니다. 
- `sys_resource_monitor_<ip>.csv`: 노드당 로그.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>원격 컨텍스트에서 사용자 스크립트의 로그를 어떻게 할까요? 하 시겠습니까?
다음 단계를 사용 하 여로 거를 설정 하 여 포털의 **로그/사용자** 폴더에 로그가 표시 되도록 할 수 있습니다.
1. 아래의 첫 번째 코드 섹션을 py entry_script_helper 파일에 저장 하 고 파일을 입력 스크립트와 동일한 폴더에 저장 합니다. 이 클래스는 AmlCompute 내의 경로를 가져옵니다. 로컬 테스트의 경우 `get_working_dir()`를 변경 하 여 로컬 폴더를 반환할 수 있습니다.
2. `init()` 메서드에서로 거를 구성 하 고 사용 합니다. 아래 두 번째 코드 섹션은 예제입니다. 

**entry_script_helper py:**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**로 거를 사용 하는 샘플 항목 스크립트입니다.**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>다음 단계

* ParallelRunStep 클래스에 대 한 자세한 내용은 [패키지에](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) 대 [한 자세한 내용은](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) SDK 참조를 참조 하세요.

* 일괄 처리 점수 매기기에 파이프라인 사용에 대 한 [고급 자습서](tutorial-pipeline-batch-scoring-classification.md) 를 따르세요.

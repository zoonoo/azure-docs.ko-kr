---
title: 기계 학습 파이프라인 디버그 및 문제 해결
titleSuffix: Azure Machine Learning
description: 파이썬용 Azure 기계 학습 SDK에서 기계 학습 파이프라인을 디버깅하고 해결합니다. 파이프라인 개발에 대한 일반적인 함정과 원격 실행 전과 실행 중에 스크립트를 디버깅하는 데 도움이 되는 팁을 알아봅니다. Visual Studio 코드를 사용하여 기계 학습 파이프라인을 대화형으로 디버깅하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 578e935ee742ad476aeafb53670f0a92035249e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064076"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>기계 학습 파이프라인 디버그 및 문제 해결
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 및 [Azure 기계 학습 디자이너(미리 보기)에서](https://docs.microsoft.com/azure/machine-learning/concept-designer) [기계 학습 파이프라인을](concept-ml-pipelines.md) 디버깅하고 해결하는 방법을 배웁니다. 방법에 대한 정보가 제공됩니다.

* Azure 기계 학습 SDK를 사용하여 디버그
* Azure 기계 학습 디자이너를 사용하여 디버그
* 응용 프로그램 인사이트를 사용하여 디버그
* 비주얼 스튜디오 코드(VS 코드)와 비주얼 스튜디오용 파이썬 도구(PTVSD)를 사용하여 대화식으로 디버그

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Azure 기계 학습 SDK에서 디버그 및 문제 해결
다음 섹션에서는 파이프라인을 빌드할 때의 일반적인 함정에 대한 개요와 파이프라인에서 실행 중인 코드를 디버깅하기 위한 다양한 전략을 제공합니다. 파이프라인을 예상대로 실행하는 데 문제가 있는 경우 다음 팁을 사용합니다.

### <a name="testing-scripts-locally"></a>로컬스크립트 테스트

파이프라인에서 가장 일반적인 오류 중 하나는 연결된 스크립트(데이터 정리 스크립트, 점수 매기기 스크립트 등)가 의도한 대로 실행되지 않거나 Azure Machine의 작업 영역에서 디버깅하기 어려운 원격 계산 컨텍스트의 런타임 오류가 포함되어 있다는 것입니다. 학습 스튜디오. 

파이프라인 자체는 로컬로 실행할 수 없지만 로컬 컴퓨터에서 격리된 스크립트를 실행하면 계산 및 환경 빌드 프로세스를 기다릴 필요가 없으므로 더 빠르게 디버깅할 수 있습니다. 이 작업을 수행하려면 일부 개발 작업이 필요합니다.

* 데이터가 클라우드 데이터스토어에 있는 경우 데이터를 다운로드하여 스크립트에서 사용할 수 있도록 해야 합니다. 데이터의 작은 샘플을 사용하면 런타임을 줄이고 스크립트 동작에 대한 피드백을 신속하게 얻을 수 있습니다.
* 중간 파이프라인 단계를 시뮬레이션하려는 경우 이전 단계에서 특정 스크립트가 예상하는 개체 형식을 수동으로 빌드해야 할 수 있습니다.
* 또한 사용자 고유의 환경을 정의하고 원격 계산 환경에 정의된 종속성을 복제해야 합니다.

로컬 환경에서 실행할 스크립트 설정이 있으면 다음과 같은 디버깅 작업을 수행하는 것이 훨씬 쉬워집니다.

* 사용자 지정 디버그 구성 첨부
* 실행 일시 중지 및 개체 상태 검사
* 런타임까지 노출되지 않는 형식 또는 논리적 오류 catch

> [!TIP] 
> 스크립트가 예상대로 실행되고 있는지 확인할 수 있으면 여러 단계가 있는 파이프라인에서 스크립트를 실행하기 전에 한 단계 파이프라인에서 스크립트를 실행하는 것이 좋습니다.

### <a name="debugging-scripts-from-remote-context"></a>원격 컨텍스트에서 스크립트 디버깅

스크립트를 로컬로 테스트하는 것은 파이프라인 빌드를 시작하기 전에 주요 코드 조각과 복잡한 논리를 디버깅하는 좋은 방법이지만, 특히 발생하는 동작을 진단할 때 실제 파이프라인 실행 중에 스크립트를 디버깅해야 할 수 있습니다. 파이프라인 단계 간의 상호 작용 중에 JavaScript 코드를 `print()` 디버깅하는 방법과 유사하게 원격 실행 중에 개체 상태와 예상 값을 볼 수 있도록 단계 스크립트에서 문을 자유롭게 사용하는 것이 좋습니다.

로그 `70_driver_log.txt` 파일에는 다음이 포함됩니다. 

* 스크립트 를 실행하는 동안 인쇄된 모든 명령문
* 스크립트의 스택 추적 

포털에서 이 파일과 다른 로그 파일을 찾으려면 먼저 작업 영역에서 파이프라인 실행을 클릭합니다.

![파이프라인 실행 목록 페이지](./media/how-to-debug-pipelines/pipelinerun-01.png)

파이프라인 실행 세부 정보 페이지로 이동합니다.

![파이프라인 실행 세부 정보 페이지](./media/how-to-debug-pipelines/pipelinerun-02.png)

특정 단계에 대한 모듈을 클릭합니다. 로그 탭으로 **이동합니다.** 다른 로그에는 환경 이미지 빌드 프로세스 및 단계 준비 스크립트에 대한 정보가 포함됩니다.

![파이프라인 실행 세부 정보 페이지 로그 탭](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> 게시된 *파이프라인에* 대한 실행은 작업 영역의 **끝점** 탭에서 찾을 수 있습니다. *게시되지 않은 파이프라인에* 대한 실행은 **실험** 또는 파이프라인에서 찾을 수 **있습니다.**

### <a name="troubleshooting-tips"></a>문제 해결 팁

다음 표에는 파이프라인 개발 중에 발생할 수 있는 솔루션이 포함된 일반적인 문제가 포함되어 있습니다.

| 문제 | 가능한 해결 방법 |
|--|--|
| 데이터를 디렉터리로 `PipelineData` 전달할 수 없음 | 파이프라인에서 단계 출력 데이터를 예상하는 위치에 해당하는 디렉터리를 만들었는지 확인합니다. 대부분의 경우 입력 인수는 출력 디렉터리를 정의한 다음 디렉터리를 명시적으로 만듭니다. 출력 `os.makedirs(args.output_dir, exist_ok=True)` 디렉토리를 만드는 데 사용합니다. 이 디자인 패턴을 보여 주는 점수 매기기 스크립트 예제는 [자습서를](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) 참조하세요. |
| 종속성 버그 | 스크립트를 로컬에서 개발하고 테스트했지만 파이프라인의 원격 계산에서 실행할 때 종속성 문제가 있는 경우 계산 환경 종속성 및 버전이 테스트 환경과 일치하는지 확인합니다. (환경 [건물, 캐싱 및 재사용](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse) 참조)|
| 계산 대상을 가진 모호한 오류 | 계산 대상을 삭제하고 다시 작성하면 계산 대상과 관련된 특정 문제를 해결할 수 있습니다. |
| 단계를 다시 사용하지 않는 파이프라인 | 단계 재사용은 기본적으로 활성화되어 있지만 파이프라인 단계에서 사용하지 않도록 설정하지 않았는지 확인합니다. 재사용이 비활성화된 `allow_reuse` 경우 단계의 매개 변수가 `False`로 설정됩니다. |
| 파이프라인이 불필요하게 다시 실행되고 있습니다. | 기본 데이터 또는 스크립트가 변경될 때만 단계를 다시 실행하려면 각 단계에 대한 디렉터리를 분리합니다. 여러 단계에 동일한 소스 디렉터리를 사용하는 경우 불필요한 재실행이 발생할 수 있습니다. 파이프라인 `source_directory` 단계 개체의 매개 변수를 사용하여 해당 단계에 대해 격리된 디렉터리를 가리키고 여러 단계에 대해 동일한 `source_directory` 경로를 사용하지 않는지 확인합니다. |

### <a name="logging-options-and-behavior"></a>로깅 옵션 및 동작

아래 표에서는 파이프라인에 대한 다양한 디버그 옵션에 대한 정보를 제공합니다. 여기에 표시된 Azure 기계 학습, 파이썬 및 OpenCensus 옵션 외에 다른 옵션이 존재하기 때문에 전체 목록이 아닙니다.

| 라이브러리                    | Type   | 예제                                                          | 대상                                  | 리소스                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | 메트릭 | `run.log(name, val)`                                             | Azure 기계 학습 포털 UI             | [실험을 추적하는 방법](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml.core.Run 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| 파이썬 인쇄/로깅    | 로그    | `print(val)`<br>`logging.info(message)`                          | 드라이버 로그, Azure 기계 학습 디자이너 | [실험을 추적하는 방법](how-to-track-experiments.md#available-metrics-to-track)<br><br>[파이썬 로깅](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| 오픈인구파이썬          | 로그    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | 애플리케이션 인사이트 - 트레이스                | [Application Insights에서 파이프라인 디버깅](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[파이썬 로깅 쿡북](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>로깅 옵션 예제

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Azure 기계 학습 디자이너의 디버그 및 문제 해결(미리 보기)

이 섹션에서는 디자이너에서 파이프라인 문제를 해결하는 방법에 대한 개요를 제공합니다.
디자이너에서 만든 파이프라인의 경우 작성 페이지 또는 파이프라인 실행 세부 정보 페이지에서 **로그 파일을** 찾을 수 있습니다.

### <a name="access-logs-from-the-authoring-page"></a>작성 페이지에서 로그에 액세스

파이프라인 실행을 제출하고 작성 페이지에 머물면 각 모듈에 대해 생성된 로그 파일을 찾을 수 있습니다.

1. 작성 캔버스에서 모듈을 선택합니다.
1. 모듈의 오른쪽 창에서 **Outputs+ogs** 탭으로 이동합니다.
1. 로그 파일 선택`70_driver_log.txt`

    ![페이지 모듈 로그 작성](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>파이프라인 실행에서 로그에 액세스

파이프라인 또는 **실험** 섹션에서 파이프라인 실행 세부 정보 페이지에서 특정 실행의 로그 파일을 찾을 수도 **있습니다.**

1. 디자이너에서 만든 파이프라인 실행을 선택합니다.
    ![파이프라인 실행 페이지](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. 미리 보기 창에서 모듈을 선택합니다.
1. 모듈의 오른쪽 창에서 **Outputs+ogs** 탭으로 이동합니다.
1. 로그 파일 선택`70_driver_log.txt`

## <a name="debug-and-troubleshoot-in-application-insights"></a>애플리케이션 인사이트 디버그 및 문제 해결
이러한 방식으로 OpenCensus 파이썬 라이브러리를 사용하는 방법에 대한 자세한 내용은 이 가이드: [응용 프로그램 인사이트에서 기계 학습 파이프라인 디버그 및 문제 해결](how-to-debug-pipelines-application-insights.md) 을 참조하십시오.

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>비주얼 스튜디오 코드에서 디버그 및 문제 해결

경우에 따라 ML 파이프라인에 사용되는 Python 코드를 대화형으로 디버깅해야 할 수 있습니다. 비주얼 스튜디오 코드(VS Code)와 PTVSD용 파이썬 도구(PTVSD)를 사용하여 학습 환경에서 실행되는 코드를 연결할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

* Azure __가상 네트워크를__사용하도록 구성된 __Azure 기계 학습 작업 영역입니다.__
* 파이프라인 단계의 일부로 파이썬 스크립트를 사용하는 __Azure 기계 학습__ 파이프라인입니다. 예를 들어, 파이썬스크립트스텝.
* Azure 기계 학습 계산 클러스터는 __가상 네트워크에__ 있으며 __파이프라인에서 학습에 사용됩니다.__
* __가상 네트워크에__있는 __개발 환경입니다.__ 개발 환경은 다음 중 하나일 수 있습니다.

    * 가상 네트워크의 Azure 가상 시스템
    * 가상 네트워크의 노트북 VM 계산 인스턴스
    * VPN(가상 사설망)을 통해 가상 네트워크에 연결된 클라이언트 시스템입니다.

Azure 기계 학습을 사용하는 Azure 가상 네트워크 사용에 대한 자세한 내용은 [Azure 가상 네트워크 내의 Secure Azure ML 실험 및 추론 작업을](how-to-enable-virtual-network.md)참조하십시오.

### <a name="how-it-works"></a>작동 방법

ML 파이프라인 단계는 파이썬 스크립트를 실행합니다. 이러한 스크립트는 다음 작업을 수행하도록 수정됩니다.
    
1. 실행 중인 호스트의 IP 주소를 기록합니다. IP 주소를 사용하여 디버거를 스크립트에 연결합니다.

2. PTVSD 디버그 구성 요소를 시작하고 디버거가 연결될 때까지 기다립니다.

3. 개발 환경에서 교육 프로세스에서 생성된 로그를 모니터링하여 스크립트가 실행 중인 IP 주소를 찾습니다.

4. VS 코드 IP 주소는 파일을 사용하여 디버거를 `launch.json` 연결하도록 지시합니다.

5. 디버거를 연결하고 스크립트를 대화형으로 단계별로 진행합니다.

### <a name="configure-python-scripts"></a>파이썬 스크립트 구성

디버깅을 사용하려면 ML 파이프라인의 단계에서 사용하는 Python 스크립트를 다음과 같은 변경 사항을 수행합니다.

1. 다음 가져오기 문을 추가합니다.

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. 다음 인수를 추가합니다. 이러한 인수를 사용하면 필요에 따라 디버거를 사용하도록 설정하고 디버거를 연결하기 위한 시간 시간을 설정할 수 있습니다.

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. 다음 문을 추가합니다. 이러한 문은 코드가 실행 중인 노드의 IP 주소를 로그할 수 있도록 현재 실행 컨텍스트를 로드합니다.

    ```python
    global run
    run = Run.get_context()
    ```

1. PTVSD를 `if` 시작하고 디버거가 연결될 때까지 기다리는 문을 추가합니다. 시간 시간 지정 전에 디버거를 연결하지 않으면 스크립트는 정상적으로 계속됩니다.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

다음 Python 예제에서는 `train.py` 디버깅을 가능하게 하는 기본 파일을 보여 줍니다.

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>ML 파이프라인 구성

PTVSD를 시작하고 실행 컨텍스트를 얻는 데 필요한 파이썬 패키지를 `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`제공하려면 환경을 만들고 설정합니다. 사용 중인 버전과 일치하도록 SDK 버전을 변경합니다. 다음 코드 조각에서는 환경을 만드는 방법을 보여 줍니다.

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

Python [스크립트 구성](#configure-python-scripts) 섹션에서 ML 파이프라인 단계에서 사용되는 스크립트에 두 개의 새 인수가 추가되었습니다. 다음 코드 조각에서는 이러한 인수를 사용하여 구성 요소에 대한 디버깅을 활성화하고 시간 시간을 설정하는 방법을 보여 줍니다. 또한 설정하여 `runconfig=run_config`이전에 만든 환경을 사용하는 방법을 보여 줍니다.

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

파이프라인이 실행되면 각 단계가 자식 실행을 만듭니다. 디버깅을 사용하도록 설정하면 수정된 스크립트는 자식 실행의 다음 텍스트와 `70_driver_log.txt` 유사한 정보를 기록합니다.

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

값을 `ip_address` 저장합니다. 해당 정보는 다음 섹션에서 사용됩니다.

> [!TIP]
> 이 파이프라인 단계에 대한 자식 실행에 대한 실행 로그에서 IP 주소를 찾을 수도 있습니다. 이 정보를 보는 방법에 대한 자세한 내용은 [Azure ML 실험 실행 및 메트릭 모니터를](how-to-track-experiments.md)참조하십시오.

### <a name="configure-development-environment"></a>개발 환경 구성

1. VS 코드 개발 환경에 PTVSD용 파이썬 도구(PTVSD)를 설치하려면 다음 명령을 사용합니다.

    ```
    python -m pip install --upgrade ptvsd
    ```

    VS 코드와 함께 PTVSD 사용에 대한 자세한 내용은 [원격 디버깅](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)을 참조하십시오.

1. 디버거를 실행 하는 Azure 기계 학습 계산과 통신 하는 VS 코드를 구성 하려면 새 디버그 구성을 만듭니다.

    1. VS 코드에서 __디버그__ 메뉴를 선택한 다음 __구성 열기를 선택합니다.__ __launch.json이라는__ 파일이 열립니다.

    1. __launch.json__ 파일에서 포함 `"configurations": [`된 줄을 찾아 다음 텍스트를 삽입합니다. 이전 `"host": "10.3.0.5"` 섹션에서 로그에 반환된 IP 주소로 항목을 변경합니다. 디버깅 중인 스크립트의 `"localRoot": "${workspaceFolder}/code/step"` 복사본이 포함된 로컬 디렉터리로 항목을 변경합니다.

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 구성 섹션에 이미 다른 항목이 있는 경우 삽입한 코드 다음의 쉼표(,)를 추가합니다.

        > [!TIP]
        > 가장 좋은 방법은 스크립트에 대한 리소스를 별도의 디렉터리로 `localRoot` 유지하는 것입니다. `/code/step1`
        >
        > 여러 스크립트를 디버깅하는 경우 다른 디렉터리에서 각 스크립트에 대해 별도의 구성 섹션을 만듭니다.

    1. __launch.json__ 파일을 저장합니다.

### <a name="connect-the-debugger"></a>디버거 연결

1. VS 코드를 열고 스크립트의 로컬 복사본을 엽니다.
2. 첨부한 후 스크립트를 중지할 중단점을 설정합니다.
3. 자식 프로세스가 스크립트를 `Timeout for debug connection` 실행하고 로그에 표시되는 동안 F5 키를 사용하거나 __디버그__를 선택합니다. 메시지가 표시되면 __Azure 기계 학습 계산: 원격 디버그__ 구성을 선택합니다. 사이드 바에서 디버그 아이콘을 선택할 수도 있습니다., __Azure 기계 학습:__ 디버그 드롭다운 메뉴에서 원격 디버그 항목, 다음 녹색 화살표를 사용 하 여 디버거를 연결 합니다.

    이 시점에서 VS 코드는 계산 노드에서 PTVSD에 연결하고 이전에 설정한 중단점에서 중지됩니다. 이제 코드를 실행하면서 단계별로 실행하고 변수를 볼 수 있습니다.

    > [!NOTE]
    > 로그에 이 항목이 `Debugger attached = False`표시되면 시간 시간이 만료되고 디버거 없이 스크립트가 계속됩니다. 파이프라인을 다시 제출하고 `Timeout for debug connection` 메시지 후 및 시간 지정이 만료되기 전에 디버거를 연결합니다.

## <a name="next-steps"></a>다음 단계

* [Azureml-파이프라인-코어](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 패키지 및 [azureml 파이프라인-단계 패키지에](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 대 한 도움말은 SDK 참조를 참조 하십시오.

* 디자이너 예외 [및 오류 코드](algorithm-module-reference/designer-error-codes.md)목록을 참조하십시오.

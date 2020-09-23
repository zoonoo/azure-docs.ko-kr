---
title: Visual Studio Code를 사용한 대화형 디버깅
titleSuffix: Azure Machine Learning
description: Visual Studio Code를 사용 하 여 Azure Machine Learning 코드, 파이프라인 및 배포를 대화형으로 디버그
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 08/06/2020
ms.openlocfilehash: 3c2934c92be668d4b4c05f97a98395e2e219b7dc
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907607"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Visual Studio Code를 사용한 대화형 디버깅



Visual Studio Code (VS Code) 및 [depugpy](https://github.com/microsoft/debugpy/)를 사용 하 여 Azure Machine Learning 파이프라인과 배포를 대화형으로 디버깅 하는 방법에 대해 알아봅니다.

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>기계 학습 파이프라인 디버그 및 문제 해결

ML 파이프라인에서 사용 되는 Python 코드를 대화형으로 디버깅 해야 하는 경우도 있습니다. VS Code 및 debugpy를 사용 하 여 학습 환경에서 실행 되는 코드에 연결할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

* __Azure Virtual Network__를 사용 하도록 구성 된 __Azure Machine Learning 작업 영역__ 입니다.
* 파이프라인 단계의 일부로 Python 스크립트를 사용 하는 __Azure Machine Learning 파이프라인__ 입니다. 예를 들면 PythonScriptStep입니다.
* __가상 네트워크에__ 있고 __파이프라인에서 학습에 사용__하는 Azure Machine Learning 계산 클러스터
* __가상 네트워크에__있는 __개발 환경__ 개발 환경은 다음 중 하나일 수 있습니다.

  * 가상 네트워크에 있는 Azure 가상 머신
  * 가상 네트워크에 있는 노트북 VM의 계산 인스턴스
  * VPN 또는 Express 경로를 통해 가상 네트워크에 대 한 개인 네트워크 연결이 있는 클라이언트 컴퓨터입니다.

Azure Machine Learning에서 Azure Virtual Network를 사용 하는 방법에 대 한 자세한 내용은 [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md)를 참조 하세요.

> [!TIP]
> 가상 네트워크 뒤에 있지 않은 Azure Machine Learning 리소스를 사용할 수 있지만 가상 네트워크를 사용 하는 것이 좋습니다.

### <a name="how-it-works"></a>작동 방식

ML 파이프라인 단계는 Python 스크립트를 실행 합니다. 이러한 스크립트는 다음 작업을 수행 하도록 수정 됩니다.

1. 실행 중인 호스트의 IP 주소를 기록 합니다. IP 주소를 사용 하 여 디버거를 스크립트에 연결 합니다.

2. Debugpy debug 구성 요소를 시작 하 고 디버거가 연결 될 때까지 기다립니다.

3. 개발 환경에서 학습 프로세스에 의해 생성 된 로그를 모니터링 하 여 스크립트가 실행 되는 IP 주소를 찾을 수 있습니다.

4. 파일을 사용 하 여 디버거를 연결할 IP 주소 VS Code를 알려 줍니다 `launch.json` .

5. 디버거를 연결 하 고 스크립트를 통해 대화형으로 프로시저를 실행 합니다.

### <a name="configure-python-scripts"></a>Python 스크립트 구성

디버깅을 사용 하도록 설정 하려면 ML 파이프라인의 단계에서 사용 하는 Python 스크립트를 다음과 같이 변경 합니다.

1. 다음 import 문을 추가 합니다.

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. 다음 인수를 추가 합니다. 이러한 인수를 사용 하면 필요에 따라 디버거를 사용 하도록 설정 하 고 디버거 연결에 대 한 제한 시간을 설정할 수 있습니다.

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. 다음 문을 추가 합니다. 이러한 문은 현재 실행 컨텍스트를 로드 하 여 코드가 실행 되 고 있는 노드의 IP 주소를 기록할 수 있도록 합니다.

    ```python
    global run
    run = Run.get_context()
    ```

1. Debugpy를 `if` 시작 하 고 디버거가 연결 될 때까지 대기 하는 문을 추가 합니다. 제한 시간 이전에 디버거가 연결 되지 않은 경우 스크립트는 정상적으로 계속 됩니다. `HOST`및 값은 사용자 `PORT` 고유의 함수 인지 확인 합니다 `listen` .

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

다음 Python 예제에서는 `train.py` 디버깅을 사용 하도록 설정 하는 간단한 파일을 보여 줍니다.

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
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
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>ML 파이프라인 구성

Debugpy을 시작 하 고 실행 컨텍스트를 가져오는 데 필요한 Python 패키지를 제공 하려면 환경을 만들고를 설정 `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` 합니다. SDK 버전을 사용 하는 것과 일치 하도록 변경 합니다. 다음 코드 조각에서는 환경을 만드는 방법을 보여 줍니다.

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
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

[Python 스크립트 구성](#configure-python-scripts) 섹션에서 ML 파이프라인 단계에서 사용 하는 스크립트에 새 인수를 추가 했습니다. 다음 코드 조각에서는 이러한 인수를 사용 하 여 구성 요소에 대 한 디버깅을 사용 하도록 설정 하 고 시간 제한을 설정 하는 방법을 보여 줍니다. 또한 다음을 설정 하 여 이전에 만든 환경을 사용 하는 방법을 보여 줍니다 `runconfig=run_config` .

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

파이프라인이 실행 되 면 각 단계에서 자식 실행을 만듭니다. 디버깅을 사용 하는 경우 수정 된 스크립트는 `70_driver_log.txt` 자식 실행에 대 한의 다음 텍스트와 유사한 정보를 기록 합니다.

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

값을 저장 `ip_address` 합니다. 해당 정보는 다음 섹션에서 사용됩니다.

> [!TIP]
> 이 파이프라인에 대 한 자식 실행에 대 한 로그 실행 단계에서 IP 주소를 찾을 수도 있습니다. 이 정보를 보는 방법에 대 한 자세한 내용은 [AZURE ML 실험 실행 및 메트릭 모니터링](how-to-track-experiments.md)을 참조 하세요.

### <a name="configure-development-environment"></a>개발 환경 구성

1. VS Code 개발 환경에 debugpy을 설치 하려면 다음 명령을 사용 합니다.

    ```bash
    python -m pip install --upgrade debugpy
    ```

    VS Code와 함께 debugpy를 사용 하는 방법에 대 한 자세한 내용은 [원격 디버깅](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)을 참조 하세요.

1. 디버거를 실행 하는 Azure Machine Learning 계산과 통신 하도록 VS Code를 구성 하려면 새 디버그 구성을 만듭니다.

    1. VS Code에서 __디버그__ 메뉴를 선택한 다음, __구성 열기__를 선택합니다. __launch.json__이라는 파일이 열립니다.

    1. 파일의 __launch.js__ 에서가 포함 된 줄을 찾은 `"configurations": [` 후 다음 텍스트를 삽입 합니다. 항목을 `"host": "<IP-ADDRESS>"` 이전 섹션의 로그에서 반환 된 IP 주소로 변경 합니다. `"localRoot": "${workspaceFolder}/code/step"`디버깅 중인 스크립트의 복사본을 포함 하는 로컬 디렉터리로 항목을 변경 합니다.

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
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
        > 이미 다른 항목이 구성 섹션에 있으면 삽입한 코드 뒤에 쉼표(,)를 추가합니다.

        > [!TIP]
        > 특히 파이프라인에 대 한 리소스를 별도의 디렉터리에 보관 하는 것이 가장 좋은 방법입니다. 코드는 각 단계에만 해당 됩니다. 이 예제에서는 `localRoot` 예제 값을 참조 `/code/step1` 합니다.
        >
        > 여러 스크립트를 디버그 하는 경우 다른 디렉터리에서 각 스크립트에 대 한 별도의 구성 섹션을 만듭니다.

    1. __launch.json__ 파일을 저장합니다.

### <a name="connect-the-debugger"></a>디버거 연결

1. VS Code를 열고 스크립트의 로컬 복사본을 엽니다.
2. 연결 된 후 스크립트를 중지 하려는 중단점을 설정 합니다.
3. 자식 프로세스가 스크립트를 실행 하 고가 `Timeout for debug connection` 로그에 표시 되는 동안 F5 키를 사용 하거나 __디버그__를 선택 합니다. 메시지가 표시 되 면 __Azure Machine Learning Compute: 원격 디버그__ 구성을 선택 합니다. 디버그 드롭다운 메뉴의 오른쪽 __Azure Machine Learning: 원격 디버그__ 항목에서 디버그 아이콘을 선택 하 고 녹색 화살표를 사용 하 여 디버거를 연결할 수도 있습니다.

    이 시점에서 VS Code 계산 노드의 debugpy에 연결 하 고 이전에 설정한 중단점에서 중지 합니다. 이제 실행되는 코드를 단계별로 실행하고 변수를 보는 등의 작업을 수행할 수 있습니다.

    > [!NOTE]
    > 로그에에 대 한 항목이 표시 `Debugger attached = False` 되 면 제한 시간이 만료 되 고 스크립트가 디버거 없이 계속 됩니다. 파이프라인을 다시 제출 하 고 `Timeout for debug connection` 메시지 뒤, 그리고 시간 제한이 만료 되기 전에 디버거를 연결 합니다.

## <a name="debug-and-troubleshoot-deployments"></a>배포 디버그 및 문제 해결

경우에 따라 모델 배포에 포함된 Python 코드를 대화형으로 디버그해야 할 수도 있습니다. 예를 들어 항목 스크립트가 실패하고 추가 로깅으로 이유를 확인할 수 없는 경우입니다. VS Code 및 debugpy를 사용 하 여 Docker 컨테이너 내에서 실행 되는 코드에 연결할 수 있습니다.

> [!IMPORTANT]
> `Model.deploy()` 및 `LocalWebservice.deploy_configuration`을 사용하여 모델을 로컬로 배포하는 경우 이 디버깅 방법이 작동하지 않습니다. 대신 [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-) 메서드를 사용하여 이미지를 만들어야 합니다.

로컬 웹 서비스를 배포하려면 로컬 시스템에서 작동하는 Docker를 설치해야 합니다. Docker를 사용하는 방법에 대한 자세한 내용은 [Docker 설명서](https://docs.docker.com/)를 참조하세요. 계산 인스턴스를 사용할 때는 Docker가 이미 설치 되어 있습니다.

### <a name="configure-development-environment"></a>개발 환경 구성

1. 로컬 VS Code 개발 환경에 debugpy을 설치 하려면 다음 명령을 사용 합니다.

    ```bash
    python -m pip install --upgrade debugpy
    ```

    VS Code와 함께 debugpy를 사용 하는 방법에 대 한 자세한 내용은 [원격 디버깅](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)을 참조 하세요.

1. Docker 이미지와 통신하도록 VS Code를 구성하려면 새 디버그 구성을 만듭니다.

    1. VS Code에서 __디버그__ 메뉴를 선택한 다음, __구성 열기__를 선택합니다. __launch.json__이라는 파일이 열립니다.

    1. __launch.json__ 파일에서 `"configurations": [`가 포함된 줄을 찾고, 다음 텍스트를 이 줄 뒤에 삽입합니다.

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 이미 다른 항목이 구성 섹션에 있으면 삽입한 코드 뒤에 쉼표(,)를 추가합니다.

        이 섹션에서는 5678 포트를 사용하여 Docker 컨테이너에 연결됩니다.

    1. __launch.json__ 파일을 저장합니다.

### <a name="create-an-image-that-includes-debugpy"></a>Debugpy를 포함 하는 이미지 만들기

1. Debugpy을 포함 하도록 배포에 대 한 conda 환경을 수정 합니다. 다음 예제에서는 `pip_packages` 매개 변수를 사용하여 추가하는 방법을 보여 줍니다.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Debugpy를 시작 하 고 서비스가 시작 될 때 연결을 대기 하려면 파일의 맨 위에 다음을 추가 합니다 `score.py` .

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. 환경 정의를 기반으로 하는 이미지를 만들고, 이 이미지를 로컬 레지스트리로 끌어옵니다. 

    > [!NOTE]
    > 다음 예제에서는 `ws`에서 Azure Machine Learning 작업 영역을 가리키고 `model`이 배포할 모델이라고 가정합니다. `myenv.yml` 파일에는 1단계에서 만든 conda 종속성이 포함되어 있습니다.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    이미지가 만들어지고 다운로드되면 이미지 경로(리포지토리, 이름 및 태그 포함, 이 경우 digest이기도 함)가 다음과 비슷한 메시지에 표시됩니다.

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. 이미지 작업을 더 쉽게 수행하려면 다음 명령을 사용하여 태그를 추가합니다. `myimagepath`를 이전 단계의 위치 값으로 바꿉니다.

    ```bash
    docker tag myimagepath debug:1
    ```

    나머지 단계에서는 전체 이미지 경로 값 대신 로컬 이미지를 `debug:1`로 참조할 수 있습니다.

### <a name="debug-the-service"></a>서비스 디버깅

> [!TIP]
> 파일에서 debugpy 연결에 대 한 제한 시간을 설정 하는 경우 `score.py` 제한 시간이 만료 되기 전에 VS Code을 디버그 세션에 연결 해야 합니다. VS Code를 시작하고, `score.py`의 로컬 복사본을 열고, 중단점을 설정하고, 이 섹션의 단계를 사용하기 전에 이동할 수 있도록 준비합니다.
>
> 디버깅 및 중단점 설정에 대한 자세한 내용은 [디버깅](https://code.visualstudio.com/Docs/editor/debugging)을 참조하세요.

1. 이미지를 사용하여 Docker 컨테이너를 시작하려면 다음 명령을 사용합니다.

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_path_to_score.py>:/var/azureml-apps/score.py debug:1 /bin/bash
    ```

    이 `score.py` 는 컨테이너의에 로컬로 연결 합니다. 따라서 편집기에서 변경한 내용은 컨테이너에 자동으로 반영 됩니다.

1. 컨테이너 내에서 셸에서 다음 명령을 실행 합니다.

    ```bash
    runsvdir /var/runit
    ```

1. 컨테이너 내부에서 debugpy에 VS Code을 연결 하려면 VS Code를 열고 F5 키를 사용 하거나 __디버그__를 선택 합니다. 메시지가 표시 되 면 __Azure Machine Learning 배포: Docker 디버그__ 구성을 선택 합니다. 디버그 드롭다운 메뉴의 __Azure Machine Learning 배포: Docker 디버그__ 항목에서 디버그 아이콘을 선택 하 고 녹색 화살표를 사용 하 여 디버거를 연결할 수도 있습니다.

    ![디버그 아이콘, 디버깅 시작 단추 및 구성 선택기](./media/how-to-troubleshoot-deployment/start-debugging.png)

이 시점에서 VS Code는 Docker 컨테이너 내부의 debugpy에 연결 하 고 이전에 설정한 중단점에서 중지 합니다. 이제 실행되는 코드를 단계별로 실행하고 변수를 보는 등의 작업을 수행할 수 있습니다.

VS Code를 사용하여 Python을 디버그하는 방법에 대한 자세한 내용은 [Python 코드 디버그](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)를 참조하세요.

### <a name="stop-the-container"></a>컨테이너 중지

컨테이너를 중지하려면 다음 명령을 사용합니다.

```bash
docker stop debug
```

## <a name="next-steps"></a>다음 단계

이제 원격 Visual Studio Code 설정 했으므로 계산 인스턴스를 Visual Studio Code의 원격 계산으로 사용 하 여 코드를 대화형으로 디버그할 수 있습니다. 

[자습서: 첫 번째 ML 모델 학습](tutorial-1st-experiment-sdk-train.md)에서는 통합 Notebook으로 컴퓨팅 인스턴스를 사용하는 방법을 보여 줍니다.

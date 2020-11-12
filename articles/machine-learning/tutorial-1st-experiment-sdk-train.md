---
title: '자습서: 첫 번째 기계 학습 모델 학습 - Python'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 시작 시리즈의 3부에서는 기계 학습 모델을 학습시키는 방법을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: f3ba5751e7a0c2369d505535896bbb4ff7523c02
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314566"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>자습서: 첫 번째 기계 학습 모델 학습(3/4부)

이 자습서에서는 Azure Machine Learning에서 기계 학습 모델을 학습시키는 방법을 보여 줍니다.

이 자습서는 Azure Machine Learning의 기본 사항을 알아보고 Azure에서 작업 기반 기계 학습 작업을 완료하는 *4부로 구성된 자습서 시리즈 중 3부* 입니다. 이 자습서는 시리즈 중 [1부: 설정](tutorial-1st-experiment-sdk-setup-local.md) 및 [2부: “Hello world!” 실행](tutorial-1st-experiment-hello-world.md)에서 구현된 작업을 기반으로 작성되었습니다.

이 자습서에서는 기계 학습 모델을 학습시키는 스크립트를 제출하여 다음 단계를 수행합니다. 이 예제는 Azure Machine Learning에서 로컬 디버깅과 원격 실행 간의 일관된 동작을 간소화하는 방법을 이해하는 데 도움이 됩니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 학습 스크립트를 만듭니다.
> * Conda를 사용하여 Azure Machine Learning 환경 정의
> * 제어 스크립트 만들기
> * Azure Machine Learning 클래스(`Environment`, `Run`, `Metrics`) 이해
> * 학습 스크립트 제출 및 실행
> * 클라우드에서 코드 출력 보기
> * Azure Machine Learning에 메트릭 기록
> * 클라우드에서 메트릭 보기

## <a name="prerequisites"></a>사전 요구 사항

* 시리즈 중 [2부](tutorial-1st-experiment-hello-world.md) 완료.
* Python 언어 및 기계 학습 워크플로에 대한 입문 지식
* Visual Studio Code, Jupyter 및 PyCharm과 같은 로컬 개발 환경
* Python(버전 3.5 ~ 3.7).

## <a name="create-training-scripts"></a>학습 스크립트 만들기

먼저 `model.py` 파일에서 신경망 아키텍처를 정의합니다. `model.py`를 포함하여 모든 학습 코드는 `src` 하위 디렉터리로 이동합니다.

아래 코드는 PyTorch의 [이 소개 예제](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html)에서 가져온 것입니다. Azure Machine Learning 개념은 PyTorch뿐만 아니라 모든 기계 학습 코드에도 적용됩니다.

```python
# tutorial/src/model.py
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
```

다음으로, 학습 스크립트를 정의합니다. 이 스크립트는 PyTorch `torchvision.dataset` API를 사용하여 CIFAR10 데이터 세트를 다운로드하고, `model.py`에 정의된 네트워크를 설정하며, 표준 SGD 및 교차 엔트로피 손실을 사용하여 두 Epoch 동안 학습시킵니다.

`train.py` 스크립트를 `src` 하위 디렉터리에 만듭니다.

```python
# tutorial/src/train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                running_loss = 0.0

    print("Finished Training")

```

이제 아래와 같이 요약된 디렉터리 구조가 있습니다.

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

## <a name="create-a-python-environment"></a>Python 환경 만들기

데모용으로 Conda 환경을 사용할 예정입니다. (pip 가상 환경에 대한 단계는 거의 동일합니다.)

`pytorch-env.yml`이라는 파일을 숨겨진 `.azureml` 디렉터리에 만듭니다.

```yml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
```

이 환경에는 모델 및 학습 스크립트에 필요한 모든 종속성이 있습니다. Python용 Azure Machine Learning SDK에 대한 종속성은 없습니다.

## <a name="test-locally"></a>로컬에서 테스트

다음 코드를 사용하여 이 환경에서 로컬로 실행되는 스크립트를 테스트합니다.

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env                      # activate conda environment
python src/train.py                             # train model
```

이 스크립트가 실행되면 `tutorial/data`라는 디렉터리에 다운로드된 데이터가 표시됩니다.

## <a name="create-the-control-script"></a>제어 스크립트 만들기

다음 제어 스크립트와 “Hello world!”를 제출하는 데 사용하는 스크립트의 차이점은 환경을 설정하기 위해 몇 줄의 줄을 추가한다는 점입니다.

`04-run-pytorch.py`라는 새 Python 파일을 `tutorial` 디렉터리에 만듭니다.

```python
# tutorial/04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='src', script='train.py', compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env', file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>코드 변경 내용 이해

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning은 실험을 실행하기 위해 재현 가능한 버전의 Python 환경을 나타내는 [환경](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) 개념을 제공합니다. 환경은 로컬 Conda 또는 pip 환경에서 쉽게 만들 수 있습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)에 환경을 추가합니다.
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a>Azure Machine Learning에 실행을 제출

로컬 환경을 전환한 경우 Python용 Azure Machine Learning SDK가 설치된 환경으로 다시 전환해야 합니다. 

다음을 실행합니다.

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> 이 스크립트를 처음 실행하면 Azure Machine Learning이 PyTorch 환경에서 새 Docker 이미지를 빌드합니다. 전체 실행을 완료하는 데 5~10분이 걸릴 수 있습니다. 
>
> Docker 빌드 로그는 Azure Machine Learning 스튜디오에서 확인할 수 있습니다. Studio에 대한 링크에서 **출력 + 로그** 탭을 선택한 다음, `20_image_build_log.txt`를 선택합니다.
>
> 이 이미지는 이후 실행에서 다시 사용되므로 훨씬 더 빨리 실행됩니다.

이미지가 빌드되면 `70_driver_log.txt`를 선택하여 학습 스크립트의 출력을 확인합니다.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> `Your total snapshot size exceeds the limit`라는 오류가 표시되면 `data` 디렉터리가 `ScriptRunConfig`에서 사용되는 `source_directory` 값에 있음을 나타냅니다.
>
> `data`를 `src` 외부로 이동합니다.

환경을 `env.register(ws)`를 사용하여 작업 영역에 등록할 수 있습니다. 그러면 손쉽게 공유, 다시 사용, 버전 관리를 수행할 수 있습니다. 환경을 사용하면 이전 결과를 쉽게 재현하고 팀과 협업할 수 있습니다.

또한 Azure Machine Learning은 큐레이팅된 환경의 컬렉션을 유지 관리합니다. 이러한 환경은 일반적인 기계 학습 시나리오를 처리하며, 캐시된 Docker 이미지를 통해 지원됩니다. 캐시된 Docker 이미지는 첫 번째 원격 실행을 더 빠르게 만듭니다.

간단히 말해, 등록된 환경을 사용하면 시간을 절약할 수 있습니다! 자세한 내용은 [환경을 사용하는 방법](./how-to-use-environments.md)을 확인하세요.

## <a name="log-training-metrics"></a>학습 메트릭 기록

이제 Azure Machine Learning에서 학습된 모델이 있으므로 몇 가지 성능 메트릭의 추적을 시작합니다.

현재 학습 스크립트는 메트릭을 터미널에 출력합니다. Azure Machine Learning은 더 많은 기능을 통해 메트릭을 기록하는 메커니즘을 제공합니다. 몇 줄의 코드를 추가하면 Studio에서 메트릭을 시각화하고 여러 실행 간에 메트릭을 비교할 수 있습니다.

### <a name="modify-trainpy-to-include-logging"></a>로깅을 포함하도록 `train.py` 수정

`train.py` 스크립트를 수정하여 코드의 두 줄을 더 포함시킵니다.

```python
# train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run


# ADDITIONAL CODE: get Azure Machine Learning run from the current context
run = Run.get_context()

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # ADDITIONAL CODE: log loss metric to Azure Machine Learning
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

#### <a name="understand-the-additional-two-lines-of-code"></a>두 줄의 추가 코드 이해

`train.py`에서 `Run.get_context()` 메서드를 사용하여 학습 스크립트 자체 _내에서_ 실행 개체에 액세스하고, 이 개체를 사용하여 메트릭을 기록합니다.

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Azure Machine Learning의 메트릭은 다음과 같습니다.

- 실험 및 실행별로 구성되므로 메트릭을 쉽게 추적하고 비교할 수 있습니다.
- Studio에서 UI를 사용하여 학습 성과를 시각화할 수 있습니다.
- 크기를 조정할 수 있도록 설계되었으므로 수백 개의 실험을 실행하는 경우에도 이러한 이점을 유지할 수 있습니다.

### <a name="update-the-conda-environment-file"></a>Conda 환경 파일 업데이트

`train.py` 스크립트는 `azureml.core`에 대한 새로운 종속성을 가져왔습니다. 이 변경 내용을 반영하도록 `pytorch-env.yml`을 업데이트합니다.

```yaml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
    - pip
    - pip:
        - azureml-sdk
```

### <a name="submit-the-run-to-azure-machine-learning"></a>Azure Machine Learning에 실행을 제출
다음 스크립트를 한 번 더 제출합니다.

```bash
python 04-run-pytorch.py
```

이번에는 Studio를 방문할 때 모델 학습 손실에 대한 라이브 업데이트를 볼 수 있는 **메트릭** 탭으로 이동합니다.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="메트릭 탭의 학습 손실 그래프":::

## <a name="next-steps"></a>다음 단계

이 세션에서는 기본 "Hello World!" 스크립트에서 특정 Python 환경을 실행해야 하는 더 현실적인 학습 스크립트로 업그레이드했습니다. Azure Machine Learning 환경을 통해 로컬 Conda 환경을 클라우드로 전환하는 방법을 살펴보았습니다. 마지막으로 몇 줄의 코드에서 메트릭을 Azure Machine Learning에 기록할 수 있는 방법을 살펴보았습니다.

[pip requirements.txt](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-pip-requirements-name--file-path-) 또는 [기존 로컬 Conda 환경](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)을 포함하여 Azure Machine Learning 환경을 만드는 다른 방법이 있습니다.

다음 세션에서는 CIFAR10 데이터 세트를 Azure에 업로드하여 Azure Machine Learning에서 데이터를 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: 사용자 고유의 데이터 가져오기](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> 여기서 자습서 시리즈를 완료하고 다음 단계로 진행하지 않으려면 [리소스를 정리](tutorial-1st-experiment-bring-data.md#clean-up-resources)하세요.
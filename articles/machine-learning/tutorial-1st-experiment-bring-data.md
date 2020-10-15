---
title: '자습서: 사용자 고유의 데이터 사용'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 시작 시리즈의 4부에서는 원격 학습 실행에서 사용자 고유의 데이터를 사용하는 방법을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: tracking-python
ms.openlocfilehash: 13d43eb788c750a2f24033a6138ebf00ac57fffe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372575"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>자습서: 사용자 고유의 데이터 사용(4/4부)

이 자습서에서는 사용자 고유의 데이터를 업로드하고 사용하여 Azure Machine Learning에서 기계 학습 모델을 학습시키는 방법을 보여 줍니다.

이 자습서는 Azure Machine Learning의 기본 사항을 알아보고 Azure에서 작업 기반 기계 학습 작업을 완료하는 *4부로 구성된 자습서 시리즈 중 4부*입니다. 이 자습서는 [1부: 설정](tutorial-1st-experiment-sdk-setup-local.md), [2부: “Hello World” 실행](tutorial-1st-experiment-hello-world.md) 및 [3부: 모델 학습](tutorial-1st-experiment-sdk-train.md)에서 완료한 작업을 기반으로 합니다.

[3부: 모델 학습](tutorial-1st-experiment-sdk-train.md)에서는 데이터가 PyTorch API에서 기본 제공 `torchvision.datasets.CIFAR10` 메서드를 사용하여 다운로드되었습니다. 그러나 대부분의 경우 원격 학습 실행에서 사용자 고유의 데이터를 사용하려고 합니다. 이 문서에서는 Azure Machine Learning에서 사용자 고유의 데이터로 작업하는 데 사용할 수 있는 워크플로를 보여 줍니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 로컬 디렉터리의 데이터를 사용하도록 학습 스크립트 구성
> * 로컬로 학습 스크립트 테스트
> * Azure에 데이터 업로드
> * 제어 스크립트 만들기
> * 새 Azure Machine Learning 개념(매개 변수, 데이터 세트, 데이터 저장소 전달) 이해
> * 학습 스크립트 제출 및 실행
> * 클라우드에서 코드 출력 보기

## <a name="prerequisites"></a>사전 요구 사항

* 시리즈의 [3부](tutorial-1st-experiment-sdk-train.md) 완료
* Python 언어 및 기계 학습 워크플로에 대한 입문 지식
* Visual Studio Code, Jupyter 및 PyCharm과 같은 로컬 개발 환경
* Python(버전 3.5 ~ 3.7).

## <a name="adjust-the-training-script"></a>학습 스크립트 조정
이제 Azure Machine Learning에서 실행되는 학습 스크립트(tutorial/src/train.py)가 있으며 모델 성능을 모니터링할 수 있습니다. 인수를 도입하여 학습 스크립트를 매개 변수화하겠습니다. 인수를 사용하면 다른 하이퍼 매개 변수를 쉽게 비교할 수 있습니다.

현재 학습 스크립트는 각 실행에서 CIFAR10 데이터 세트를 다운로드하도록 설정되어 있습니다. 아래의 Python 코드는 디렉터리에서 데이터를 읽도록 조정되었습니다.

>[!NOTE] 
> `argparse`를 사용하여 스크립트를 매개 변수화합니다.

```python
# tutorial/src/train.py
import os
import argparse
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run

run = Run.get_context()

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument('--data_path', type=str, help='Path to the training data')
    parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
    parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
    args = parser.parse_args()
    
    print("===== DATA =====")
    print("DATA PATH: " + args.data_path)
    print("LIST FILES IN DATA PATH...")
    print(os.listdir(args.data_path))
    print("================")
    
    # prepare DataLoader for CIFAR10 data
    transform = transforms.Compose([transforms.ToTensor(), transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])
    trainset = torchvision.datasets.CIFAR10(
        root=args.data_path,
        train=True,
        download=False,
        transform=transform,
    )
    trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(
        net.parameters(),
        lr=args.learning_rate,
        momentum=args.momentum,
    )

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
                run.log('loss', loss) # log loss metric to AML
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

### <a name="understanding-the-code-changes"></a>코드 변경 내용 이해

`train.py`의 코드는 `argparse` 라이브러리를 사용하여 `data_path`, `learning_rate` 및 `momentum`을 설정했습니다.

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

또한 사용자 정의 매개 변수를 사용하도록 최적화 프로그램을 업데이트하기 위해 `train.py` 스크립트가 조정되었습니다.

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

## <a name="test-the-script-locally"></a>스크립트를 로컬로 테스트

이제 스크립트에서 _데이터 경로_를 인수로 허용합니다. 먼저 로컬로 테스트합니다. `data`라는 폴더를 자습서 디렉터리 구조에 추가합니다. 디렉터리 구조는 다음과 같습니다.

```txt
tutorial
└──.azureml
|  └──config.json
|  └──pytorch-env.yml
└──data
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
└──04-run-pytorch.py
```

이전 자습서에서 `train.py`를 로컬로 실행하지 않은 경우 `data/` 디렉터리가 없습니다. 이 경우 `train.py` 스크립트에서 `download=True`를 사용하여 `torchvision.datasets.CIFAR10` 메서드를 로컬로 실행합니다.

수정된 학습 스크립트를 로컬로 실행하려면 다음을 호출합니다.

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

데이터에 대한 로컬 경로를 전달하여 CIFAR10 데이터 세트를 다운로드할 필요가 없습니다. 또한 학습 스크립트에서 하드 코딩하지 않고도 다른 값을 _학습 속도_ 및 _모멘텀_ 하이퍼 매개 변수에 사용하여 실험할 수도 있습니다.

## <a name="upload-the-data-to-azure"></a>Azure에 데이터 업로드

Azure Machine Learning에서 이 스크립트를 실행하려면 Azure에서 학습 데이터를 사용할 수 있도록 해야 합니다. Azure Machine Learning 작업 영역에는 _기본_ 데이터 저장소가 장착되어 제공됩니다. 이는 학습 데이터를 저장할 수 있는 Azure Blob Storage 계정입니다.

>[!NOTE] 
> Azure Machine Learning을 사용하면 데이터를 저장하는 다른 클라우드 기반 데이터 저장소를 연결할 수 있습니다. 자세한 내용은 [데이터 저장소 설명서](./concept-data.md)를 참조하세요.  

`05-upload-data.py`라는 새 Python 제어 스크립트를 `tutorial` 디렉터리에 만듭니다.

```python
# tutorial/05-upload-data.py
from azureml.core import Workspace
ws = Workspace.from_config()
datastore = ws.get_default_datastore()
datastore.upload(src_dir='./data', target_path='datasets/cifar10', overwrite=True)
```

`target_path` 값은 CIFAR10 데이터가 업로드되는 데이터 저장소의 경로를 지정합니다.

>[!TIP] 
> Azure Machine Learning을 사용하여 데이터를 업로드하는 동안 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 임시 파일을 업로드할 수 있습니다. ETL 도구가 필요한 경우 [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)를 사용하여 데이터를 Azure에 수집할 수 있습니다.

Python 파일을 실행하여 데이터를 업로드합니다. (업로드 속도는 60초 미만이어야 합니다.)

```bash
python 05-upload-data.py
```
다음과 같은 표준 출력이 표시됩니다.
```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```


## <a name="create-a-control-script"></a>제어 스크립트 만들기

이전과 같이 `06-run-pytorch-data.py`라는 새 Python 제어 스크립트를 만듭니다.

```python
# tutorial/06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
        )
    
    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env',file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to an Azure Machine Learning compute cluster. Click on the link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>코드 변경 내용 이해

제어 스크립트는 [이 시리즈의 3부](tutorial-1st-experiment-sdk-train.md)에 있는 것과 비슷하며 다음과 같은 새 줄이 있습니다.

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      [데이터 세트](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true)는 Azure Blob Storage에 업로드한 데이터를 참조하는 데 사용됩니다. 데이터 세트는 데이터를 기반으로 하는 있는 추상화 계층이며, 안정성과 신뢰성을 향상시키도록 설계되었습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true)는 `train.py`에 전달되는 인수 목록을 포함하도록 수정되었습니다. `dataset.as_named_input('input').as_mount()` 인수는 지정된 디렉터리가 컴퓨팅 대상에 _탑재됨_을 의미합니다.
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a>Azure Machine Learning에 실행을 제출

이제 새 구성을 사용하기 위해 실행을 다시 제출합니다.

```bash
python 06-run-pytorch-data.py
```

그러면 Azure Machine Learning 스튜디오에서 실험에 대한 URL이 출력됩니다. 해당 링크로 이동하면 코드가 실행되는 것을 확인할 수 있습니다.

### <a name="inspect-the-log-file"></a>로그 파일 검사

스튜디오에서 이전 URL 출력을 선택하여 실험 실행으로 이동한 다음, **출력 + 로그**로 이동합니다. `70_driver_log.txt` 파일을 선택합니다. 다음 출력이 표시됩니다.

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

참고:

- Azure Machine Learning에서 자동으로 Blob Storage를 컴퓨팅 클러스터에 탑재했습니다.
- 제어 스크립트에 사용된 ``dataset.as_named_input('input').as_mount()``가 탑재 지점으로 확인됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

또한 리소스 그룹을 유지하면서 단일 작업 영역을 삭제할 수도 있습니다. 작업 영역 속성을 표시하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 `Datastore`를 사용하여 데이터를 Azure에 업로드하는 방법을 살펴보았습니다. 데이터 저장소는 작업 영역에 대한 클라우드 스토리지로 제공되므로 데이터를 유지할 수 있는 지속적이고 유연한 장소를 제공했습니다.

명령줄을 통해 데이터 경로를 허용하도록 학습 스크립트를 수정하는 방법을 살펴보았습니다. `Dataset`를 사용하여 디렉터리를 원격 실행에 탑재할 수 있었습니다. 

이제 모델이 있으므로 다음을 알아봅니다.

* [Azure Machine Learning을 사용하여 모델을 배포](how-to-deploy-and-where.md)하는 방법

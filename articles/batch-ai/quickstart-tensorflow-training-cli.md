---
title: Azure 빠른 시작 - Deep Learning 학습 - Azure CLI | Microsoft Docs
description: 빠른 시작 - Azure CLI를 사용하여 Batch AI의 단일 GPU로 TensorFlow Deep Learning 신경망을 학습하는 방법을 간단히 알아봅니다.
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ms.openlocfilehash: 99d864a5d519ce56a559bea4db7fe89a113e47b9
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157925"
---
# <a name="quickstart-train-a-deep-learning-model-with-batch-ai"></a>빠른 시작: Batch AI를 사용하여 Deep Learning 모델 학습

이 빠른 시작에서는 Batch AI로 관리하는 GPU 사용 가상 머신에서 샘플 Deep Learning 모델을 학습하는 방법을 보여 줍니다. Batch AI는 데이터 과학자 및 AI 연구원이 Azure Virtual Machines 클러스터에서 AI 및 기타 Machine Learning 모델을 대규모로 학습하기 위한 관리 서비스입니다. 

이 예제에서는 Azure CLI에서 [MNIST 데이터베이스](http://yann.lecun.com/exdb/mnist/)의 예제 [TensorFlow](https://www.tensorflow.org/) 신경망을 학습하도록 Batch AI를 설정합니다. 이 빠른 시작을 완료하면 Batch AI를 사용하여 AI 또는 Machine Learning 모델을 학습하는 주요 개념을 이해하고 더 큰 규모의 다양한 모델을 학습할 준비를 갖출 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서는 Azure CLI 버전 2.0.38 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

이 빠른 시작에서는 Cloud Shell 또는 로컬 컴퓨터의 Bash 셸에서 명령을 실행한다고 가정합니다. [Azure CLI를 사용하여 Batch AI 클러스터를 만들기](quickstart-create-cluster-cli.md) 위한 빠른 시작을 이미 완료한 경우 리소스 그룹 및 Batch AI 클러스터를 만드는 처음 두 단계를 건너뜁니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

`az group create` 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *eastus2* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. 미국 동부 2 위치 또는 Batch AI 서비스를 사용할 수 있는 다른 위치를 선택합니다. 

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Batch AI 클러스터 만들기

먼저 `az batchai workspace create` 명령을 사용하여 Batch AI *작업 영역*을 만듭니다. Batch AI 클러스터 및 기타 리소스를 구성하기 위한 작업 영역이 필요합니다.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Batch AI 클러스터를 만들려면 `az batchai cluster create` 명령을 사용합니다. 다음 예제에서는 다음과 같은 속성을 사용하여 1노드 클러스터를 만듭니다.

* NVIDIA Tesla K80 GPU가 1개 있는 NC6 VM 크기를 사용합니다. Azure는 다양한 NVIDIA GPU를 갖는 여러 VM 크기를 제공합니다.
* 컨테이너 기반 응용 프로그램을 호스트하도록 디자인된 기본 Ubuntu Server 이미지를 실행합니다. 이 배포의 학습 워크로드 대부분을 실행할 수 있습니다. 
* *myusername*이라는 사용자 계정을 추가하고, SSH 키가 로컬 환경의 기본 키 위치(*~/.ssh*)에 아직 없는 경우 생성합니다.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

명령 출력은 클러스터 속성을 나타냅니다. 노드를 만들고 시작하는 데 몇 분 정도 걸립니다. 클러스터 상태를 확인하려면 `az batchai cluster show` 명령을 실행합니다.

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

클러스터 생성 초기 단계에 출력은 다음과 비슷하여 `resizing`의 클러스터를 표시합니다.

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```

다음 단계를 계속 진행하여 학습 스크립트를 업로드하고 클러스터 상태가 변경되는 동안 학습 작업을 만듭니다. 클러스터는 상태가 `steady`이고 단일 노드가 `Idle`일 때 학습 작업을 실행할 준비가 된 것입니다.

## <a name="upload-training-script"></a>학습 스크립트 업로드

`az storage account create` 명령을 사용하여 학습 스크립트 및 학습 출력을 저장하기 위한 저장소 계정을 만듭니다.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

`az storage share create` 명령을 사용하여 계정에 `myshare`라는 Azure 파일 공유를 만듭니다.

```azurecli-interactive
az storage share create \
    --name myshare \
    --account-name mystorageaccount
```

`az storage directory create` 명령을 사용하여 Azure 파일 공유에 디렉터리를 만듭니다. 학습 스크립트의 `scripts` 디렉터리를 만들고 학습 출력을 위한 `logs`를 만듭니다.

```azurecli-interactive
# Create /scripts directory in file share
az storage directory create \
    --name scripts \
    --share-name myshare \
    --account-name mystorageaccount

# Create /logs directory in file share 
az storage directory create \
    --name logs \
    --share-name myshare \
    --account-name mystorageaccount
```

Bash 셸에서 로컬 작업 디렉터리를 만들고 TensorFlow [convolutional.py](https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py) 샘플을 다운로드합니다. Python 스크립트는 0부터 9까지 손으로 쓴 60,000개의 숫자로 이루어진 MNIST 이미지 집합을 콘볼루션 신경망에 학습시킵니다. 그런 다음, 테스트 예제 집합을 대상으로 모델을 테스트합니다.

```bash
wget https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py
```

`az storage file upload` 명령을 사용하여 공유의 `scripts` 디렉터리에 스크립트를 업로드합니다.

```azurecli-interactive
az storage file upload \
    --share-name myshare \
    --path scripts \
    --source convolutional.py \
    --account-name mystorageaccount
```

## <a name="submit-training-job"></a>교육 작업 제출

먼저, `az batchai experiment create` 명령을 사용하여 작업 영역에 Batch AI *실험*을 만듭니다. 실험은 관련 Batch AI 작업에 대한 논리적 컨테이너입니다.

```azurecli-interactive
az batchai experiment create \
    --name myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

작업 디렉터리에서 다음 내용의 학습 작업 구성 파일 `job.json`을 만듭니다. 학습 작업을 제출할 때 이 구성 파일을 전달합니다.

이 `job.json` 파일에는 Python 스크립트 파일을 찾아 GPU 노드의 TensorFlow 컨테이너에서 실행하기 위한 설정이 포함되어 있습니다. 또한 Azure Storage에 작업의 출력 파일을 저장할 위치도 지정합니다. `<AZURE_BATCHAI_STORAGE_ACCOUNT>`는 작업 제출 동안 저장소 계정 이름이 지정될 것임을 나타냅니다.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 1,
        "tensorFlowSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/scripts/convolutional.py"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/logs",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

`az batchai job create` 명령을 사용하여 노드에 작업을 제출하고, `job.json` 구성 파일 및 저장소 계정 이름을 전달합니다.

```azurecli-interactive
az batchai job create \
    --name myjob \
    --cluster mycluster \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --config-file job.json \
    --storage-account-name mystorageaccount
```

이 명령은 작업 속성을 반환한 후 몇 분 후에 완료됩니다. 이 작업의 진행률을 모니터링하려면 `az batchai job file stream` 명령을 사용하여 노드의 표준 출력 디렉터리에서 `stdout-wk-0.txt` 파일을 스트리밍합니다. 학습 스크립트는 작업 실행이 시작된 후 이 파일을 생성합니다.  

```azurecli-interactive
az batchai job file stream \
    --job myjob \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --file-name stdout-wk-0.txt
```

예제 출력:

```
File found with URL "https://mystorageaccount.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/myResourceGroup/workspaces/myworkspace/experiments/myexperiment/jobs/myjob/<JOB_ID>/stdouterr/stdout-wk-0.txt?sv=2016-05-31&sr=f&sig=Kih9baozMao8Ugos%2FVG%2BcsVsSeY1O%2FTocCNvLQhwtx4%3D&se=2018-06-20T22%3A07%3A30Z&sp=rl". Start streaming
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting data/train-images-idx3-ubyte.gz
Extracting data/train-labels-idx1-ubyte.gz
Extracting data/t10k-images-idx3-ubyte.gz
Extracting data/t10k-labels-idx1-ubyte.gz
Initialized!
Step 0 (epoch 0.00), 14.9 ms
Minibatch loss: 8.334, learning rate: 0.010000
Minibatch error: 85.9%
Validation error: 84.6%
Step 100 (epoch 0.12), 9.7 ms
Minibatch loss: 3.240, learning rate: 0.010000
Minibatch error: 6.2%
Validation error: 7.7%
Step 200 (epoch 0.23), 8.3 ms
Minibatch loss: 3.335, learning rate: 0.010000
Minibatch error: 7.8%
Validation error: 4.5%
Step 300 (epoch 0.35), 8.3 ms
Minibatch loss: 3.157, learning rate: 0.010000
Minibatch error: 3.1%
...
Step 8500 (epoch 9.89), 8.3 ms
Minibatch loss: 1.605, learning rate: 0.006302
Minibatch error: 0.0%
Validation error: 0.9%
Test error: 0.8%
```

작업이 완료되면 스트리밍이 중지됩니다. 이 샘플 스크립트는 10개가 넘는 *Epoch*를 학습하거나 학습 데이터 집합을 통과합니다. 이 예제에서는 10개 Epoch 후에 학습된 모델이 0.8%의 테스트 오류만 나타내며 수행됩니다.

## <a name="get-job-output"></a>작업 출력 가져오기

Batch AI는 저장소 계정에 각 작업 출력용으로 고유한 폴더 구조를 만듭니다. 이 경로를 사용하여 JOB_OUTPUT_PATH 환경 변수를 설정합니다. 그런 다음, `az storage file list` 명령을 사용하여 저장소의 출력 파일을 나열합니다.

```azurecli-interactive
export JOB_OUTPUT_PATH=$(az batchai job show --name myjob --experiment myexperiment --workspace myworkspace --resource-group myResourceGroup --query jobOutputDirectoryPathSegment --output tsv)

az storage file list \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr \
    --output table
```

출력은 다음과 비슷합니다.

```
Name               Content Length  Type    Last Modified
---------------  ----------------  ------  ---------------
execution.log               14866  file
stderr-wk-0.txt              1527  file
stdout-wk-0.txt             11027  file
```

`az storage file download` 명령을 사용하여 하나 이상의 파일을 로컬 작업 디렉터리에 다운로드합니다. 예: 

```azurecli-interactive
az storage file download \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr/stdout-wk-0.txt
```

## <a name="clean-up-resources"></a>리소스 정리

Batch AI 자습서 및 샘플을 계속 사용하려면 Batch AI 작업 영역, 클러스터 및 이 빠른 시작에서 만든 저장소 계정을 사용합니다. 

노드가 실행되는 동안 Batch AI 클러스터 요금이 청구됩니다. 실행할 작업이 없는 경우 클러스터 구성을 유지하려면 클러스터 크기를 0개 노드로 조정합니다. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

나중에 작업을 실행하기 위해 하나 이상의 노드로 크기를 조정합니다. 클러스터가 더 이상 필요하지 않으면 `az batchai cluster delete` 명령을 사용하여 삭제합니다.

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

더 이상 필요하지 않은 경우 `az group delete` 명령을 사용하여 Batch AI 및 저장소 리소스의 리소스 그룹을 제거할 수 있습니다. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Batch AI를 사용하여 Azure CLI에서 단일 GPU VM에서 예제 TensorFlow Deep Learning 모델을 학습했습니다. 더 큰 GPU 클러스터에서 모델 학습을 분산하는 방법을 자세히 알아보려면 Batch AI 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [분산 학습 자습서](./tutorial-horovod-tensorflow.md)
---
title: Azure 빠른 시작 - Batch AI 클러스터 만들기 - Azure CLI | Microsoft Docs
description: 빠른 시작 - Machine Learning 및 AI 모델 학습을 위한 Batch AI 클러스터 만들기 - Azure CLI
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
ms.openlocfilehash: 0d4ba7edfb22a6710222c854ceb2bf86284d2d77
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057801"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Batch AI 학습 작업에 대한 클러스터 만들기

이 빠른 시작에서는 Azure CLI를 사용하여 AI 및 Machine Learning 모델을 학습하는 데 사용할 수 있는 Batch AI 클러스터를 만드는 방법을 보여 줍니다. Batch AI는 데이터 과학자 및 AI 연구원이 Azure Virtual Machines 클러스터에서 AI 및 기타 Machine Learning 모델을 대규모로 학습하기 위한 관리 서비스입니다.

처음에는 클러스터에 단일 노드 GPU가 있습니다. 이 빠른 시작을 완료하면 규모를 확대한 후 모델을 학습하는 데 사용할 수 있는 클러스터가 형성됩니다. Batch AI, [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) 도구 또는 [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai)를 사용하여 클러스터에 학습 작업을 제출합니다.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서는 Azure CLI 버전 2.0.38 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

이 빠른 시작에서는 Cloud Shell 또는 로컬 컴퓨터의 Bash 셸에서 명령을 실행한다고 가정합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

`az group create` 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *eastus2* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. Batch AI 서비스를 사용할 수 있는 미국 동부 2와 같은 위치를 선택해야 합니다.

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

Batch AI 클러스터를 만들려면 `az batchai cluster create` 명령을 사용합니다. 다음 예제에서는 다음과 같은 속성을 사용하여 클러스터를 만듭니다.

* NVIDIA Tesla K80 GPU가 1개 있는 NC6 VM 크기의 단일 노드를 포함합니다. 
* 호스트 컨테이너 기반 응용 프로그램으로 디자인된 기본 Ubuntu Server 이미지를 실행합니다. 이 이미지는 대부분의 학습 워크로드에 사용할 수 있습니다. 
* *myusername*이라는 사용자 계정을 추가하고 , SSH 키가 로컬 환경의 기본 키 위치(*~/.ssh*)에 아직 없는 경우 생성합니다. 

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

명령 출력은 클러스터 속성을 나타냅니다. 노드를 만들고 시작하는 데 몇 분 정도 걸립니다. 클러스터의 상태를 보려면 `az batchai cluster show` 명령을 실행합니다. 

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

클러스터 생성 초기 단계에 출력은 다음과 비슷하여 `resizing` 상태의 클러스터를 표시합니다.

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```
클러스터는 상태가 `steady`이고 단일 노드가 `Idle`일 때 사용할 준비가 된 것입니다.

## <a name="list-cluster-nodes"></a>클러스터 노드 나열 

응용 프로그램을 설치하거나 유지 관리를 수행하기 위해 클러스터 노드(이 경우 단일 노드)에 연결해야 할 경우 `az batchai cluster node list` 명령을 실행하여 포털에서 연결 정보를 가져옵니다.


```azurecli-interactive
az batchai cluster node list \
    --cluster mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup 
 ```

JSON 출력은 다음과 유사합니다.

```JSON
[
  {
    "ipAddress": "40.68.254.143",
    "nodeId": "tvm-1816144089_1-20180626t233430z",
    "port": 50000.0
  }
]
```
이 정보를 사용하여 노드로의 SSH 연결을 수행합니다. 예를 들어, 다음 명령에서 노드의 올바른 IP 주소를 대체합니다.

```bash
ssh myusername@40.68.254.143 -p 50000
``` 
계속하려면 SSH 세션을 종료합니다.

## <a name="resize-the-cluster"></a>클러스터 크기 조정

클러스터를 사용하여 학습 작업을 실행하는 경우 추가 계산 리소스가 필요할 수 있습니다. 예를 들어 분산 학습 작업을 위해 크기를 2개 노드로 늘리려면 [batch ai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) 명령을 실행합니다.

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 2
```

클러스터 크기가 조정될 때까지 몇 분 정도 걸립니다.

## <a name="clean-up-resources"></a>리소스 정리

Batch AI 자습서 및 샘플을 계속 사용하려면 이 빠른 시작에서 만든 Batch AI 작업 영역을 사용합니다. 

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
    --resource-group myResourceGroup \
```

더 이상 필요하지 않은 경우 `az group delete` 명령을 사용하여 Batch AI 리소스의 리소스 그룹을 제거할 수 있습니다. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 Batch AI 클러스터를 만드는 방법을 배웠습니다. Batch AI 클러스터를 사용하여 모델을 학습하는 방법을 자세히 알아보려면 Deep Learning 모델 학습을 위한 빠른 시작을 계속 진행하세요.

> [!div class="nextstepaction"]
> [Deep Learning 모델 학습](./quickstart-tensorflow-training-cli.md)

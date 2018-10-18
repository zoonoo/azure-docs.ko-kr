---
title: Azure Batch AI 클러스터 작업 | Microsoft Docs
description: Batch AI 클러스터 구성을 선택하는 방법과 클러스터 AI 만들기 및 관리
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 61294d8b6b84b03b1e0c8d79b4d2855452c7f0e6
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44056968"
---
# <a name="work-with-batch-ai-clusters"></a>Batch AI 클러스터 작업 

이 문서에서는 Azure Batch AI에서 클러스터를 사용하는 방법을 설명합니다. 여기에서 클러스터의 개념, 가능한 구성 형식 및 예제를 소개합니다. 이 문서에서 클러스터를 만들고 관리하는 작업에 대한 예제는 대부분 Azure CLI를 사용합니다. 그러나 Azure Portal 및 Azure Batch AI SDK를 비롯한 다른 도구를 사용해도 클러스터 작업을 수행할 수 있습니다.

Batch AI 클러스터는 서비스의 여러 리소스 중 하나입니다. 서비스의 클러스터 범위를 알아보려면 [Batch AI 리소스의 개요](resource-concepts.md)를 참조하세요.

## <a name="introduction-to-clusters"></a>클러스터 소개

Batch AI의 클러스터에는 Machine Learning 및 AI 교육 작업을 실행하기 위한 계산 리소스가 포함되어 있습니다. 클러스터의 모든 노드에는 동일한 VM 크기 및 OS 이미지가 있습니다. Batch AI는 다양한 요구에 맞게 사용자 지정된 클러스터를 만들 수 있도록 여러 옵션을 제공합니다. 일반적으로 프로젝트를 완료하는 데 필요한 처리 성능의 범주마다 다른 클러스터를 설정합니다. 수요 및 예산을 기반으로 클러스터의 노드 수 규모를 확대 및 축소할 수 있습니다. 클러스터는 프로비전하고 팀 간에 공유할 수 있으며 개인은 자신의 고유한 클러스터를 프로비전할 수 있습니다. 

각 클러스터는 *작업 영역*이라는 Batch AI 리소스에 존재합니다. 클러스터를 프로비전하기 전에 Batch AI 작업 영역이 설정되어 있어야 합니다. 예를 들어 Azure CLI를 사용하는 경우 [az batchai workspace create](/cli/azure/batchai/workspace?view=azure-cli-latest#az-batchai-workspace-create) 명령을 사용하여 작업 영역을 설정합니다. 

클러스터를 만든 후에는 한 번에 하나의 작업을 큐에 제출할 수 있습니다. 그런 다음, Batch AI는 클러스터에서 작업을 실행하기위한 리소스 할당 프로세스를 처리합니다. 

## <a name="cluster-configuration-options"></a>클러스터 구성 옵션

클러스터를 계획하는 경우 먼저 계산 요구 사항을 결정합니다. Batch AI는 유연한 구성 옵션을 제공하므로 필요에 따라 클러스터를 조정할 수 있습니다. 다음은 클러스터를 프로비전할 때 고려할 수 있는 주요 옵션입니다.

* **VM 크기** - 클러스터 노드에 대해 [지원되는 영역](https://azure.microsoft.com/global-infrastructure/services/)에서 사용 가능한 [VM 크기](../virtual-machines/linux/sizes.md)를 선택합니다. 각 클러스터에는 하나의 VM 크기만 포함될 수 있으므로 작업에 여러 형식의 VM이 필요한 경우 각 형식의 계산 요구 사항에 대해 별도의 클러스터를 프로비전해야 합니다. GPU를 활용하는 프레임워크로 개발된 Machine Learning 또는 AI 모델을 학습하려면 Azure에서 [GPU 최적화된 VM 크기](../virtual-machines/linux/sizes-gpu.md)를 참조하세요.
  
* **VM 우선 순위** - Batch AI는 클러스터에 대해 전용 또는 낮은 우선 순위의 VM을 제공합니다. 전용 VM은 클러스터에서 사용하도록 예약되어 있습니다. 낮은 우선 순위 옵션은 Azure가 VM을 회수할 경우 작업을 선점할 가능성을 고려하여 사용되지 않은 Azure VM 용량을 상당한 비용 절감으로 할당합니다. 낮은 우선 순위의 VM에서 24시간 이상 실행되는 작업도 자동으로 선점됩니다. 예산을 고려해야 하는 경우 짧은 실험 작업에 낮은 우선 순위의 VM을 사용해 보세요. 그런 다음, 더 긴 작업을 실행해야 할 때 전용 VM으로 전환하세요.

* **노드 수** - Batch AI는 클러스터의 노드 수에 대한 수동 및 자동 크기 조정 옵션을 제공합니다. 수동 옵션을 사용하면 클러스터 규모를 확장 및 축소할 때를 제어하여 사용자 자신의 계산 비용을 관리할 수 있습니다. 자동 크기 조정 옵션을 사용하면 클러스터를 사용하지 않는 경우 항상 클러스터 규모가 축소되므로 계산 비용이 최소화됩니다. 

  클러스터를 수동으로 확장하도록 선택하면 클러스터를 생성하는 동안 초기 대상을 정의합니다. 대상은 Batch AI가 처음에 할당하는 노드 수입니다. 나중에 노드 수의 크기를 수동으로 조정할 수 있습니다.  

  자동 크기 조정 기능 옵션을 선택하는 경우 클러스터 생성 중에 최대 및 최소 대상 노드 수를 정의합니다. 대상의 범위는 0부터 [Batch AI 코어 할당량](quota-limits.md)에서 지원하는 최대 지원 노드 수까지입니다. 대상을 0으로 지정하면 계산 비용이 청구되지 않고도 클러스터 구성을 유지 관리할 수 있습니다. 할당량 제한보다 높은 수의 대상을 요청하면 프로비전이 실패합니다. 

* **VM 이미지** - 기본적으로 Batch AI는 컨테이너 워크로드를 지원하는 기본 Ubuntu Server 이미지로 클러스터 VM을 프로비전합니다. [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md)와 같이 Azure Marketplace에서 미리 구성된 다른 Linux 이미지를 선택할 수 있습니다. 

* **Storage** - Batch AI는 Azure CLI를 사용하여 클러스터를 만들 때 선택할 수 있는 자동 저장 옵션을 제공합니다. 이 옵션은 Azure 파일 공유 및 Blob 컨테이너를 새 저장소 계정 아래에 자동으로 만듭니다. 이러한 저장소 리소스는 실행 시간 동안 클러스터의 각 노드에 탑재되어 로컬 경로에서 파일에 액세스할 수 있습니다. 저장소 계정 이름, 파일 공유 이름, Blob 컨테이너 이름 및 탑재 경로는 모두 기본값을 가지며 클러스터 생성 중에 추가 매개 변수를 사용하여 사용자 지정할 수도 있습니다. 

  저장소 옵션이 정의되지 않은 경우 저장소 리소스를 별도로 만들고 작업을 제출할 때 이를 정의해야 합니다. 이 옵션은 클러스터가 조직 수준에서 관리되지만 저장소는 사용자 수준에서 관리되는 경우에 유용합니다. Azure Storage에 파일을 업로드하고 실행 시간 동안 액세스하는 방법에 대한 자세한 내용은 [Azure Storage를 사용하여 Store Batch AI 작업 입력 및 출력](use-azure-storage.md)을 참조하세요.

* **사용자 액세스** - Batch AI를 사용하면 클러스터를 만들 때 공개 및 개인 SSH 키 파일을 생성하거나, 사용자 고유의 SSH 키를 제공하여 개별 노드로 SSH를 실행할 수 있습니다. 사용자 이름(기본적으로 현재 사용자로 설정됨) 및 암호를 정의할 수도 있습니다. 이러한 자격 증명은 다양한 메트릭을 보거나 작업에 대한 추가 정보를 얻기 위해 실행 중에 노드에 액세스하는 데 사용할 수 있습니다.

* **설치 작업** - Batch AI를 사용하면 할당 시 각 노드에서 실행될 명령줄 인수를 정의할 수 있습니다. 출력 파일을 로깅할 경로를 정의할 수도 있습니다. 기본 이미지 이외의 추가 프로비전 단계가 있는 경우 이 옵션을 사용합니다.

* **추가 구성** - 더욱 특수한 구성이 필요할 수 있는 흔하지 않은 몇 가지 시나리오가 있습니다. 이 경우 [클러스터 구성 파일](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#using-cluster-configuration-file)을 Azure CLI 명령에 연결하여 클러스터를 만들 수 있습니다. 

## <a name="create-the-cluster"></a>클러스터 만들기

클러스터 구성 옵션을 결정했으면 Azure CLI, Azure Portal 또는 Batch AI API를 사용하여 클러스터를 만듭니다. 예를 들어 Azure CLI를 사용하여 클러스터를 만들려면 [az batchai 클러스터 만들기](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-create) 설명서를 참조하여 필요한 구성을 제공하는 정확한 명령을 만들 수 있습니다. 

가장 기본적인 구성에서 다음 명령은 하나의 노드와 SSH 액세스 권한이 있는 Standard_NC6 클러스터를 프로비전합니다. 기본적으로 이 클러스터에는 최신 기본 Ubuntu Server 이미지를 실행하는 전용 VM이 포함되어 있습니다.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --target 1 \
    --generate-ssh-keys
```

다음 예제에서는 0개의 노드에서 4개의 노드로 자동 확장되고 낮은 우선 순위의 노드와 자동 저장 계정을 사용하는 Standard_NC6 클러스터를 프로비전합니다. 이 설치는 비용이 적고 관리하기 쉬운 클러스터가 필요한 경우에 좋은 구성입니다. 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --vm-priority lowpriority \
    --max 4 \
    --min 0 \
    --use-auto-storage 
```

다음 예제에서는 Data Science Virtual Machine 이미지, 사용자 지정 저장소 및 탑재 옵션, 사용자 지정 SSH 자격 증명, *압축 해제* 패키지를 설치하는 설치 작업 및 추가 설치를 위한 클러스터 구성 파일을 포함하는 Standard_NC6 클러스터를 프로비전합니다. 이 구성은 사용자의 필요에 따라 추가 사용자 지정된 클러스터의 예제입니다.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --image UbuntuDSVM \ 
    --config-file cluster.json \
    --setup-task 'apt install unzip -y'
    --storage-account-name <STORAGE ACCOUNT NAME> \
    --nfs-name nfsmount \
    --afs-name afsmount \
    --bfs-name bfsmount \
    --user-name adminuser \
    --ssh-key id_rsa.pub \
    --password secretpassword 
```

## <a name="monitor-the-cluster"></a>클러스터 모니터링

[az batchai cluster list](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-list), [az batchai cluster show](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show) 및 [az batchai cluster node list](/cli/azure/batchai/cluster/node?view=azure-cli-latest#az-batchai-cluster-node-list) 명령은 각 클러스터의 다양한 정보를 모니터링하는 데 사용할 수 있습니다.

### <a name="list-all-clusters"></a>모든 클러스터 나열

다음 명령은 작업 영역에 있는 모든 클러스터를 나열합니다.

```azurecli-interactive
az batchai cluster list \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
```

### <a name="show-information-about-a-cluster"></a>클러스터에 대한 정보 표시

다음 명령은 테이블 형식의 특정 클러스터에 대한 전체 정보를 표시합니다.

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --output table
```

클러스터가 자동 저장 옵션을 사용하여 프로비전된 경우 스크립트 및 교육 작업을 업로드할 때 사용할 저장소 계정 이름을 검색하고자 할 수 있습니다. 다음 명령을 사용합니다.

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --query "nodeSetup.mountVolumes.azureFileShares[0].{storageAccountName:accountName}"
```

출력은 다음과 유사해야 합니다.

```JSON
{
  "storageAccountName": "baixxxxxxxxx"
}
```

### <a name="list-cluster-nodes"></a>클러스터 노드 나열

클러스터 노드에 연결해야 하는 경우 다음 명령은 노드 목록 및 연결 정보를 검색합니다.  

```azurecli-interactive
az batchai cluster node list \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
 ```

각 노드의 출력은 다음과 유사합니다.

```JSON
[
  {
    "ipAddress": "40.68.xxx.xxx",
    "nodeId": "tvm-xxxxxxxxxx-xxxxxxxx",
    "port": 50000.0
  }
]
```
이 정보를 사용하여 다음과 유사한 명령을 사용하는 노드에 SSH를 연결할 수 있습니다.

```bash
ssh myusername@40.68.xxx.xxx -p 50000
``` 

## <a name="submit-jobs-to-the-cluster"></a>클러스터에 작업 제출

클러스터를 프로비전한 후에는 노드에서 실행할 작업을 제출할 수 있습니다. Azure CLI를 사용하여 작업을 준비, 제출 및 모니터링할 수 있는 다양한 방식의 [az batchai job](/cli/azure/batchai/job?view=azure-cli-latest) 명령을 참조하세요. 

## <a name="downscale-cluster-for-later-use"></a>나중에 사용하기 위해 클러스터 규모 축소

작업 실행을 완료하면 클러스터 규모를 축소하고자 할 수 있습니다. 계산 비용을 절약하기 위해 클러스터를 사용하지 않을 때에는 항상 클러스터의 규모를 축소하는 것이 좋습니다. 0개의 노드로 클러스터의 규모를 축소하면 나중에 규모를 확장해야 할 때 클러스터를 다시 프로비전하지 않고도 비용 청구를 중지할 수 있습니다. 클러스터 생성에서 자동 크기 조정이 선택된 경우 클러스터는 최소 대상으로 규모가 자동 축소됩니다. 수동 크기 조정이 선택된 경우 다음 명령을 사용하여 클러스터 규모를 축소합니다.

```azurecli-interactive
az batchai cluster resize \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE> \
    --target 0
```

## <a name="delete-a-cluster"></a>클러스터 삭제

클러스터 사용을 마치면 [az batchai cluster delete](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-delete) 명령을 사용하여 클러스터를 삭제할 수 있습니다.

```azurecli-interactive
az batchai cluster delete \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE>
```

또한 리소스 그룹을 삭제하면 해당 리소스 그룹에서 프로비전된 모든 클러스터도 자동으로 삭제됩니다.

```azurecli-interactive
az group delete --name <RESOURCE GROUP>
```

## <a name="next-steps"></a>다음 단계

Batch AI 클러스터 만들기에 대한 더 많은 예제를 알아보려면 [Portal](quickstart-create-cluster-portal.md) 또는 [Azure CLI](quickstart-create-cluster-cli.md) 빠른 시작을 참조하거나 GitHub에서 [Batch AI 방법](https://github.com/Azure/BatchAI/tree/master/recipes)을 참조하세요.

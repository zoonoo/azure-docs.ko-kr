---
title: Azure 빠른 시작 - Batch AI로 CNTK 교육 - Azure CLI | Microsoft Docs
description: Azure CLI를 사용하여 Batch AI로 CNTK 교육 작업을 실행하는 방법을 신속하게 알아봅니다
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 06/14/2018
ms.author: Alexander.Yukhanov
ms.openlocfilehash: eb00c1d4ec74b5268a1497b11087030ab6a86e5a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294076"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Azure CLI를 사용하여 CNTK 교육 작업 실행

Azure CLI 2.0을 사용하여 Batch AI 리소스를 만들고 관리할 수 있습니다(Batch AI 파일 서버 및 클러스터 만들기/삭제 및 교육 작업 제출/종료/삭제/모니터링).

이 빠른 시작에서는 Microsoft CNTK(Cognitive Toolkit)를 사용하여 GPU 클러스터를 만들고 교육 작업을 실행하는 방법을 보여줍니다.

교육 스크립트 [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py)는 Batch AI GitHub 페이지에 있습니다. 이 스크립트는 MNIST 데이터베이스의 콘볼루션 신경망에 손으로 쓴 숫자를 학습시킵니다.

공식 CNTK 예제는 명령줄 인수를 통해 교육 데이터 집합의 위치와 출력 디렉터리 위치를 적용하도록 수정되었습니다.

## <a name="quickstart-overview"></a>빠른 시작 개요

* 이름이 `nc6`이고 VM 크기가 `Standard_NC6`인 단일 노드 GPU 클러스터를 만듭니다.
* 작업 입력 및 출력을 저장할 저장소 계정을 만듭니다.
* 작업 출력 및 교육 스크립트를 저장할 `logs` 및 `scripts`라는 두 개의 폴더로 Azure 파일 공유를 만듭니다.
* 교육 데이터를 저장할 Azure Blob 컨테이너 `data`를 만듭니다.
* 생성된 파일 공유 및 컨테이너에 교육 스크립트 및 교육 데이터를 배포합니다.
* Azure 파일 공유 및 Azure Blob 컨테이너를 클러스터 노드에 탑재하고 `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`, `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` 및 `$AZ_BATCHAI_JOB_MOUNT_ROOT/data`에서 일반 파일 시스템으로 사용할 수 있도록 작업을 구성합니다.
`AZ_BATCHAI_JOB_MOUNT_ROOT`는 Batch AI에서 작업에 설정한 환경 변수입니다.
* 표준 출력을 스트리밍하여 작업 실행을 모니터링합니다.
* 작업 완료 후 해당 출력 및 생성된 모델을 검사합니다.
* 마지막으로, 할당된 모든 리소스를 삭제합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 - Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* batchai 모듈 버전 0.3 이상으로 Azure CLI 2.0에 액세스합니다. [Azure Cloud Shell](../cloud-shell/overview.md)에서 제공되는 Azure CLI 2.0을 사용하거나 [이 지침](/cli/azure/install-azure-cli?view=azure-cli-latest)에 따라 로컬에 설치할 수 있습니다.

  Cloud Shell을 사용하는 경우 홈 디렉터리에 빈 공간이 없으므로 작업 디렉터리를 `/usr/$USER/clouddrive`로 변경합니다.

  ```azurecli
  cd /usr/$USER/clouddrive
  ```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스를 배포하고 관리하기 위한 논리적 컨테이너입니다. 다음 명령은 미국 동부 위치에 새 리소스 그룹 `batchai.quickstart`를 만듭니다.

```azurecli
az group create -n batchai.quickstart -l eastus
```
## <a name="create-batch-ai-workspace"></a>Batch AI 작업 영역 만들기

다음 명령은 리소스 그룹에 Batch AI 작업 영역을 만듭니다. Batch AI 작업 영역은 모든 Batch AI 리소스 유형 중 최상위 컬렉션입니다.

```azurecli
az batchai workspace create -g batchai.quickstart -n quickstart
```

## <a name="create-gpu-cluster"></a>GPU 클러스터 만들기

다음 명령은 운영 체제 이미지로 Ubuntu DSVM(Data Science Virtual Machine)을 사용하여 작업 영역에 VM 크기가 Standard_NC6인 단일 노드 GPU 클러스터를 만듭니다.

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -w quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

Ubuntu DSVM을 사용하면 Docker 컨테이너에서 모든 교육 작업을 실행하고, 가장 널리 사용되는 심층 학습 프레임워크를 VM에서 직접 실행할 수 있습니다.

`--generate-ssh-keys` 옵션은 개인 및 공용 ssh 키가 아직 없는 경우 이를 생성하도록 Azure CLI에 지시합니다. 현재 사용자 이름과 생성된 ssh 키를 사용하여 클러스터 노드에 액세스할 수 있습니다.

Cloud Shell을 사용하는 경우 ~/.ssh 폴더를 일부 영구 저장소에 백업하는 것이 좋습니다.

예제 출력:
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "myuser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기

다음 명령은 Batch AI 클러스터를 만드는 데 사용된 것과 동일한 리소스 그룹에 저장소 계정을 만듭니다. 작업 입력 및 출력을 저장할 저장소 계정을 만듭니다. 명령을 고유한 저장소 계정 이름으로 업데이트합니다.

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```


## <a name="deploy-data"></a>데이터 배포

### <a name="download-the-training-script-and-training-data"></a>교육 스크립트 및 교육 데이터 다운로드

* [이 위치](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D)에서 사전 처리된 MNIST 데이터베이스를 현재 폴더로 다운로드하여 추출합니다.

GNU/Linux 또는 Cloud Shell의 경우:

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

GNU/Linux 배포에 없는 경우 `unzip`을 설치해야 할 수도 있습니다.

* [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) 예제 스크립트를 현재 폴더에 다운로드합니다.

GNU/Linux 또는 Cloud Shell의 경우:

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

### <a name="create-azure-file-share-and-deploy-the-training-script"></a>Azure 파일 공유를 만들고 교육 스크립트 배포

다음 명령은 Azure 파일 공유 `scripts` 및 `logs`를 만들고 교육 스크립트를 `scripts` 공유의 `cntk` 폴더에 복사합니다.

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

### <a name="create-a-blob-container-and-deploy-training-data"></a>Blob 컨테이너를 만들고 교육 데이터 배포

다음 명령은 Azure Blob 컨테이너 `data`를 만들고 교육 데이터를 `mnist_cntk` 폴더에 복사합니다.

```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

## <a name="submit-training-job"></a>교육 작업 제출

### <a name="create-a-batch-ai-experiment"></a>Batch AI 실험 만들기

실험은 관련 Batch AI 작업에 대한 논리적 컨테이너입니다. 다음 명령을 사용하여 작업 영역에서 실험을 만듭니다.

```azurecli
az batchai experiment create -g batchai.quickstart -w quickstart -n quickstart
```

### <a name="prepare-job-configuration-file"></a>작업 구성 파일 준비

다음 콘텐츠가 포함된 교육 작업 구성 파일 `job.json`을 만듭니다. 저장소 계정 이름으로 업데이트합니다.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<YOUR_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<YOUR_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<YOUR_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

이 구성 파일은 다음을 지정합니다.

* `nodeCount` - 작업에 필요한 노드 수(이 빠른 시작의 경우 1)
* `cntkSettings` - 교육 스크립트 및 명령줄 인수의 경로를 지정합니다. 명령줄 인수에는 교육 데이터에 대한 경로 및 생성된 모델을 저장하기 위한 대상 경로가 포함됩니다. `AZ_BATCHAI_OUTPUT_MODEL`은 출력 디렉터리 구성을 기반으로 Batch AI에서 설정하는 환경 변수(아래 참조)
* `stdOutErrPathPrefix` - Batch AI가 작업 출력 및 로그를 포함하는 디렉터리를 만드는 경로
* `outputDirectories` - Batch AI에서 생성할 출력 디렉터리 컬렉션입니다. Batch AI는 각 디렉터리에 대해 이름이 `AZ_BATCHAI_OUTPUT_<id>`인 환경 변수를 만듭니다. 여기서 `<id>`는 디렉터리 식별자입니다.
* `mountVolumes` - 작업 실행 중에 탑재할 파일 시스템의 목록입니다. 파일 시스템은 `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`에 탑재됩니다. `AZ_BATCHAI_JOB_MOUNT_ROOT`는 Batch AI에서 설정한 환경 변수
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` - 컴퓨터의 `--storage-account-name parameter` 또는 `AZURE_BATCHAI_STORAGE_ACCOUNT` 환경 변수를 통해 작업을 제출하는 동안 지정되는 저장소 계정 이름.

### <a name="submit-the-job"></a>작업 제출

```azurecli
az batchai job create -n cntk_python_1 -c nc6 -g batchai.quickstart -w quickstart -e quickstart  -f job.json --storage-account-name <storage account name>
```

예제 출력:
```
{
  "caffe2Settings": null,
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-06-14T22:22:57.543000+00:00",
  "customMpiSettings": null,
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "startTime": "2018-06-14T22:22:59.838000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2018-06-14T22:22:59.838000+00:00",
  "horovodSettings": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1/f2d6ff09-7549-4e1a-8cd8-ec839f042a61",
  "jobPreparation": null,
  "mountVolumes": {
    "azureBlobFileSystems": [
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "azureFileUrl": "https://<YOUR STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "azureFileUrl": "https://<YOUR STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null
    }
  ],
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-06-14T22:22:58.625000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "danlep0614b",
  "schedulingPriority": "normal",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/workspaces/experiments/jobs"
}

```

## <a name="monitor-job-execution"></a>작업 실행 모니터링

교육 스크립트는 표준 출력 디렉터리 내 `stderr.txt` 파일의 교육 진행률을 보고합니다. 다음 명령을 사용하여 진행률을 모니터링할 수 있습니다.

```azurecli
az batchai job file stream -j cntk_python_1 -g batchai.quickstart -w quickstart -e quickstart -f stderr.txt
```

예제 출력:
```
File found with URL "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.62% * 10000
```

작업이 완료되면(성공하든 실패하든) 스트리밍이 중지됩니다.

## <a name="inspect-generated-model-files"></a>생성된 모델 파일 검사

이 작업은 생성된 모델 파일을 `id` 속성이 `MODEL`인 출력 디렉터리에 저장합니다. 다음 명령을 사용하여 모델 파일을 나열하고 다운로드 URL을 가져올 수 있습니다.

```azurecli
az batchai job file list -j cntk_python_1 -w quickstart -e quickstart -g batchai.quickstart -d MODEL
```

예제 출력:
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

또는 Azure Portal 또는 Azure Storage 탐색기를 사용하여 생성된 파일을 검사할 수 있습니다. Batch AI는 각 작업의 결과를 다른 작업과 구별하기 위해 각 작업별로 고유한 폴더 구조를 만듭니다. 제출된 작업의 `jobOutputDirectoryPathSegment` 특성을 사용하여 출력을 포함하는 폴더의 경로를 찾을 수 있습니다.

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart -w quickstart -e quickstart --query jobOutputDirectoryPathSegment
```

예제 출력:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없으면 다음 명령을 사용하여 리소스 그룹과 할당된 모든 리소스를 삭제합니다.

```azurecli
az group delete -n batchai.quickstart -y
```

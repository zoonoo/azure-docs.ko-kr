---
title: Azure Storage를 사용하여 Batch AI 작업 입출력 저장 | Microsoft Docs
description: 입출력 파일의 쉽고 빠른 클라우드 저장소용 Batch AI에서 Azure Storage를 사용하는 방법
services: batch-ai
documentationcenter: ''
author: kevwang1
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 42697f7f4bb8c6b9ef785eef0fe2f5f33b2b38a7
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615606"
---
# <a name="store-batch-ai-job-input-and-output-with-azure-storage"></a>Azure Storage를 사용하여 Batch AI 작업 입출력 저장

이 가이드에서는 작업을 실행하는 경우 입출력 파일을 저장하기 위해 Azure Storage를 사용하는 방법을 설명합니다. Azure Storage는 Batch AI에서 지원하는 여러 저장소 옵션 중 하나입니다. Batch AI는 클라우드에 저장된 파일에 대한 원활한 액세스를 허용하면서 Batch AI 작업 또는 클러스터 파일 시스템에 Azure Storage 시스템을 탑재하여 Azure Storage와 통합합니다. 

## <a name="introduction-to-azure-storage"></a>Azure Storage 소개

Azure Storage는 Microsoft의 클라우드 저장소 솔루션입니다. Batch AI는 Azure Blob 컨테이너 및 Azure 파일 공유를 Batch AI 작업 또는 클러스터에 탑재를 지원하면서 마치 파일이 네이티브 파일 시스템에 있는 것처럼 작업에서 파일에 액세스할 수 있습니다. Batch AI는 [blobfuse](https://github.com/Azure/azure-storage-fuse)를 사용하여 Azure Blob 컨테이너를, SMB 프로토콜을 통해 Azure 파일 공유를 탑재합니다. Azure Storage에 대한 자세한 내용은 [Azure Storage 소개](../storage/common/storage-introduction.md)를 참조하세요.

## <a name="store-datasets-and-input-scripts-in-azure-storage"></a>Azure Storage에 데이터 세트 및 입력 스크립트 저장

Batch AI 환경으로 Azure Storage를 선택하는 경우 더 높은 처리량의 Blob 컨테이너에 입력 파일(예: 데이터 세트)을 저장하고, 스트리밍을 지원(작업이 동시에 실행되는 동안 출력 로그 읽기 허용)하는 파일 공유에 학습 출력을 저장하는 것이 좋습니다. 

Azure Storage를 사용하기 전에 [Azure Storage 계정을 만들](../storage/common/storage-quickstart-create-account.md)어야 합니다. Batch AI는 범용 v1(GPv1) 및 범용 v2(GPv2) Azure Storage 계정 모두에서 볼륨을 탑재하는 것을 지원합니다. Azure Storage 계정은 여러 Blob 컨테이너 또는 파일 공유 인스턴스를 보관할 수 있습니다. 만들 저장소 계정 형식을 선택할 때 비용 및 성능 요구 사항을 고려해야 합니다. 자세한 내용은 [Azure Storage 계정 개요](../storage/common/storage-account-overview.md)를 참조하세요. 

Blob 컨테이너를 만들고 Azure Blob 컨테이너에 데이터 세트를 업로드하려면 다음 방법 중 하나를 선택합니다.
- [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) - 웹 기반 GUI를 통해 업로드합니다. 적은 수의 파일을 업로드하기 위해 Azure Portal은 가장 간단한 작업을 제공합니다.
- [Azure Storage CLI](../storage/blobs/storage-quickstart-blobs-cli.md) - 명령줄을 통해 업로드(디렉터리 업로드 지원)합니다. 파일의 디렉터리를 업로드하려면 `az storage blob upload-batch`를 사용합니다.
- [기타 기술](../storage/common/storage-moving-data.md) - 응용 프로그램 SDK를 사용하는 것이 포함됩니다.

마찬가지로, Azure 파일 공유를 만들려면 다음 방법 중 하나를 선택합니다.
- [Azure Portal](../storage/files/storage-how-to-use-files-portal.md)
- [Azure Storage CLI](../storage/files/storage-how-to-use-files-cli.md)
- [기타 기술](../storage/common/storage-moving-data.md)

### <a name="auto-storage-with-batch-ai"></a>Batch AI를 사용한 자동 저장소

또는 `az batchai cluster create`를 통해 `--use-auto-storage` 매개 변수를 사용하여 Azure 파일 공유 및 Blob 컨테이너(및 이러한 볼륨을 Batch AI 클러스터에 자동으로 탑재)에서 Azure Storage 계정을 만들 수 있습니다. 자세한 내용은 [여기](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#auto-storage-account)를 참조하세요.

## <a name="mount-azure-storage"></a>Azure Storage 탑재 

### <a name="mount-volumes-to-a-job"></a>작업에 볼륨 탑재

Azure Storage 볼륨을 탑재하면 각 작업에 대해 생성된 파일 시스템을 통해 액세스할 수 있습니다. 따라서 작업에서는 마치 로컬 파일인 것처럼 파일을 읽고 클라우드 저장소에 원활하게 쓸 수 있습니다.

Azure CLI를 사용하여 만든 작업에 Azure Storage 볼륨을 탑재하려면 `az batchai job create`를 실행할 때 `job.json` 파일에서 `mountVolumes` 속성을 사용합니다. 예를 들어 [Tensorflow GPU 분산형 레시피](https://github.com/Azure/BatchAI/blob/master/recipes/TensorFlow/TensorFlow-GPU-Distributed/job.json)를 참조합니다. `mountVolumes`에 대한 스키마는 다음과 같습니다.
```json
{
    "mountVolumes": {
        "azureFileShares": [{
            "azureFileUrl": "https://<STORAGE_ACCOUNT_NAME>.file.core.windows.net/<FILE_SHARE_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }],
        "azureBlobFileSystems": [{
            "accountName": "<STORAGE_ACCOUNT_NAME>",
            "containerName": "<BLOB_CONTAINER_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }]
    }
}
```

`azureFileShares` 및 `azureBlobFileSystems` 모두는 탑재할 볼륨을 나타내는 개체의 배열입니다. 자리 표시자 설명.

- <RELATIVE_MOUNT_PATH> - 볼륨이 이 경로에 탑재됩니다. 예를 들어 `relativeMountPath`가 `jobs`이면 볼륨은 `$AZ_BATCHAI_JOB_MOUNT_ROOT/jobs`에 있습니다)
- <STORAGE_ACCOUNT_NAME> - 파일 공유 또는 Blob 컨테이너를 보유하는 Azure Storage 계정의 이름
- <FILE_SHARE_NAME> - 파일 공유의 이름
- <BLOB_CONTAINER_NAME> - Blob 컨테이너의 이름

Azure Batch AI SDK를 사용하여 Azure Storage 볼륨을 탑재하려면 `JobCreateParameters`에서 `mount_volumes`(Python) 또는 `MountVolumes`(C#, Java) 속성을 설정합니다. Azure Batch AI SDK를 사용하여 볼륨을 탑재하는 경우에는 저장소 계정 자격 증명을 제공해야 합니다. [Python](https://docs.microsoft.com/python/api/azure-mgmt-batchai/azure.mgmt.batchai.models.MountVolumes?view=azure-python), [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.batchai.models.mountvolumes?view=azure-dotnet) 및 [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.management.batchai._mount_volumes?view=azure-java-stable)에서 볼륨 탑재를 위한 스키마를 확인합니다.

### <a name="mount-volumes-to-a-cluster"></a>클러스터에 볼륨 탑재

Batch AI는 Batch AI 클러스터에 Azure Storage 볼륨을 탑재하는 것도 지원합니다. 볼륨을 클러스터에 탑재하는 경우 해당 클러스터에서 실행되는 모든 작업은 해당 클러스터에 탑재된 볼륨을 사용할 수 있습니다. 작업 수준 탑재가 최고의 유연성을 제공하는(각 작업이 다양하게 탑재된 볼륨을 보유할 수 있는) 반면 클러스터 수준 탑재는 간단한 시나리오로 충분합니다.

Azure CLI를 사용하여 만든 클러스터에 Azure Storage 볼륨을 탑재하려면 `az batchai cluster create`를 실행할 때 `cluster.json` 파일에서 `mountVolumes` 속성을 사용합니다. 클러스터를 탑재하는 경우 `mountVolumes`에 대한 스키마는 작업에 탑재하는 경우와 동일합니다. 

마찬가지로, Azure Batch AI SDK를 사용하여 탑재하는 경우 `ClusterCreateParameters`에서 `mount_volumes`(Python) 또는 `MountVolumes`(C#, Java) 속성을 사용할 수 있습니다. 

## <a name="access-mounted-filesystem-in-a-job"></a>작업에서 탑재된 파일 시스템에 액세스

### <a name="azbatchaijobmountroot-environment-variable"></a>$AZ_BATCHAI_JOB_MOUNT_ROOT 환경 변수

작업의 실행 환경 내부에서는 탑재된 저장소 시스템을 포함하는 디렉터리에 `$AZ_BATCHAI_JOB_MOUNT_ROOT` 환경 변수(작업 수준 탑재를 사용하는 경우)를 사용하여 액세스할 수 있습니다. 클러스터 수준 탑재를 사용하는 경우 이 환경 변수는 `$AZ_BATCHAI_MOUNT_ROOT`입니다. 다음 예제에서는 작업 수준 탑재를 사용하는 것으로 가정합니다.

탑재된 볼륨에서 데이터의 경로를 제공하려면 탑재된 경로와 함께 환경 변수 `$AZ_BATCHAI_JOB_MOUNT_ROOT`를 사용합니다. 예를 들어 경우 학습 스크립트 `train.py`를 상대 탑재 경로 `scripts`에서 탑재된 Azure 파일 공유에 업로드한 경우 파일은 `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py`에서 사용할 수 있습니다.

학습 스크립트가 경로를 알아야 하는 경우 해당 경로를 명령줄 인수로 전달해야 합니다. 예를 들어 `data` 경로에서 탑재된 Azure Blob 컨테이너에서 `train_data`라는 폴더에 데이터를 저장한 경우 명령줄 인수로 `--data-dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/data/train_data`를 스크립트에 전달할 수 있습니다. 따라서 명령줄 인수를 수락하려면 스크립트를 수정해야 합니다.

### <a name="abbreviate-input-paths"></a>입력 경로 단축

환경 변수로 입력 경로를 단축하려면 `job.json` 파일의 `inputDirectories` 속성(또는 Batch AI SDK를 사용한다면 `models.JobCreateParameters.input_directories`)을 사용합니다. `inputDirectories`의 스키마는 다음과 같습니다.

```json
{
    "inputDirectories": [{
        "id": "<ID>",
        "path": "<PATH>" 
    }]
}
```

지정된 각 경로를 `$AZ_BATCHAI_INPUT_<ID>`라는 환경 변수에 배치합니다. 이 방법을 사용하면 입력 파일이나 디렉터리의 경로를 단순화할 수 있습니다. 예를 들어 학습 스크립트의 경로를 단축하려면, `"id"`가 `"SCRIPT"`이고 `"path"`가 `"$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py"`인 경우 해당 경로는 작업 실행 환경의 `$AZ_BATCHAI_INPUT_SCRIPT`에서 사용할 수 있습니다.

자세한 내용은 [여기](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#input-directories)를 참조하세요.

### <a name="abbreviate-output-paths"></a>출력 경로 단축

출력 경로를 환경 변수로 단축하려면 `job.json` 파일의 `outputDirectories` 속성(또는 Batch AI SDK를 사용한다면 `models.JobCreateParameters.output_directories`)을 사용합니다. 이 방법을 사용하면 출력 파일의 경로를 단순화할 수 있습니다. `outputDirectories`의 스키마는 다음과 같습니다.

```json
{
    "outputDirectories": [{
        "id": "<ID>",
        "pathPrefix": "<PATH_PREFIX>",
        "pathSuffix": "<PATH_SUFFIX>"
    }]
}
```

지정된 각 경로를 `$AZ_BATCHAI_OUTPUT_<ID>`라는 환경 변수에 배치합니다. `pathPrefix`는 출력(예를 들어 `"$AZ_BATCHAI_JOB_MOUNT_ROOT/output"`)을 저장할 탑재된 볼륨을 결정합니다. `pathSuffix`는 출력(예를 들어 `"logs"`, `"checkpoints"`)의 폴더 이름을 결정합니다. 출력의 실제 경로는 `pathPrefix`, `jobOutputDirectoryPathSegment`(각 작업에 대해 자동 생성된) 및 `pathSuffix`의 연결입니다.

자세한 내용은 [여기](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#output-directories)를 참조하세요.

## <a name="retrieve-job-output-from-azure-storage"></a>Azure Storage에서 작업 출력 검색

### <a name="use-azure-portal"></a>Azure Portal 사용

Azure Portal은 GUI 파일 탐색기를 사용하여 작업 출력을 확인하는 편리한 방법입니다. 그러나 Stdout 및 Stderr에서 또는 `outputDirectories`의 경로에서 출력을 확인하려는 경우 파일은 Azure Storage 볼륨에서 자동 생성된 경로에 배치됩니다. 자세한 내용은 아래를 참조하세요.

### <a name="access-stdout-and-stderr-output"></a>Stdout 및 Stderr 출력에 액세스

`job.json`의 `stdOutErrPathPrefix` 속성을 사용하여 작업의 실행 로그 및 Stdout와 Stderr 출력을 배치할 위치를 작업에 알려 줍니다. 예를 들어 상대 탑재 경로 `outputs`에서 파일 공유를 탑재하고 `stdOutErrPathPrefix`가 `"$AZ_BATCHAI_JOB_MOUNT_ROOT/outputs"`가 되도록 지정한 경우, Stdout 및 Stderr 작업 출력은 탑재된 해당 볼륨의 `{subscription id}/{resource group}/workspaces/{workspace name}/experiments/{experiment name}/jobs/{job name}/{job uuid}/stdouterr`에서 사용할 수 있습니다. 이 자동 생성된 경로는 동일한 이름의 작업 간에 출력 충돌을 방지하는 데 사용됩니다.

자세한 내용은 [여기](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#standard-and-error-output)를 참조하세요.

### <a name="list-the-output-files"></a>출력 파일 나열

Azure CLI를 사용하면 `az batchai job file list` 명령으로 작업의 사용 가능한 출력 파일을 나열할 수 있습니다. 예를 들어 작업의 표준 출력 디렉터리에 파일을 나열하려면 `az batchai job file list -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME>`을 사용합니다.

자세한 내용과 예제는 [여기](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories)를 참조하세요.

### <a name="stream-output-files"></a>출력 파일 스트리밍

Azure CLI를 사용하면 `az batchai job file stream` 명령으로 파일을 스트리밍할 수 있습니다. 예를 들어 다음을 확인하려면
- Stdout: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stdout.txt`
- Stderr: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stderr.txt`

자세한 내용과 예제는 [여기](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- Azure Storage에 연결하는 CLI 명령에 대한 자세한 내용은 [Azure Batch AI CLI 설명서](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md)를 참조하세요.
- 저장소 탑재 및 출력 파일 읽기를 포함한 Batch AI 사용 예제를 더 많이 보려면 [Batch AI에 대한 Jupyter Notebook 레시피](https://github.com/Azure/BatchAI)를 참조하세요.
- [NFS 서버 탑재](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs) 및 [NFS, cifs 또는 GlusterFS 클러스터 탑재](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)를 포함하여 저장소 탑재을 위한 기타 옵션 탐색
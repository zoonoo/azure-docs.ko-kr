---
title: Azure CLI 스크립트 예제 - 구성 파일을 사용하여 Batch AI 클러스터 만들기 | Microsoft Docs
description: Azure CLI 스크립트 예제 - JSON 파일에서 구성 설정을 지정하여 Batch AI 클러스터를 만듭니다.
services: batch-ai
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.devlang: azurecli
ms.topic: sample
ms.tgt-pltfrm: multiple
ms.workload: na
ms.date: 08/16/2018
ms.author: danlep
ms.openlocfilehash: 01281c0328fab85814ff93c73f9ea25e0d4c1b08
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232803"
---
# <a name="cli-example-create-a-batch-ai-cluster-using-a-cluster-configuration-file"></a>CLI 예제: 클러스터 구성 파일을 사용하여 Batch AI 클러스터 만들기

이 스크립트는 JSON 구성 파일을 사용하여 Batch AI 클러스터에 대한 설정을 지정하는 방법을 보여 줍니다. `az batchai cluster create`에 해당하는 명령줄 매개 변수 대신 이러한 설정을 사용합니다. 구성 파일은 클러스터 노드에 여러 파일 시스템을 탑재해야 하거나 여러 클러스터에서 동일한 구성을 사용하려는 경우에 유용합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 결정한 경우 이 가이드에서는 Azure CLI 버전 2.0.38 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-config-file.sh "Create Batch AI cluster - configuration file")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 저장소 계정을 만듭니다. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | 저장소 계정에 파일 공유를 만듭니다. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Batch AI 작업 영역을 만듭니다. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Batch AI 클러스터를 만듭니다. |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Batch AI 클러스터 관련 정보를 표시합니다. |
| [az group delete](/cli/azure/group#az-group-delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

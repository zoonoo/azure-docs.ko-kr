---
title: "Azure CLI 스크립트 샘플 - Batch에서 풀 관리 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Batch에서 풀 관리"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: ae7eab97c1da1113b0248b74a9dd67de8ce49e36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-batch-pools-with-azure-cli"></a>Azure CLI를 사용하여 Azure Batch 풀 관리

다음 스크립트는 Azure Batch 서비스에서 계산 노드의 풀을 만들고 관리하기 위해 Azure CLI에서 사용 가능한 몇 가지 도구를 보여 줍니다.

> [!NOTE]
> 샘플에 있는 명령은 Azure 가상 컴퓨터를 만듭니다. VM을 실행하면 계정에 요금이 발생합니다. 이러한 요금을 최소화하려면 샘플 실행을 완료한 후 VM을 삭제합니다. [풀 정리](#clean-up-pools)를 참조하세요.

Batch 풀은 Cloud Services 구성(Windows 전용)이나 Virtual Machine 구성(Windows 및 Linux)의 두 가지 방법을 구성할 수 있습니다. 아래 샘플 스크립트는 두 가지 구성으로 풀을 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

- 아직 Azure CLI를 설치하지 않은 경우 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)에 있는 지침을 사용하여 설치합니다.
- 아직 배치 계정이 없는 경우 새로 만듭니다. 계정을 만드는 샘플 스크립트에 대한 [Azure CLI로 배치 계정 만들기](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account)를 참조하세요.
- 시작 작업에서 실행할 응용 프로그램을 아직 구성하지 않은 경우 이를 수행합니다. 응용 프로그램을 만들고 Azure에 응용 프로그램 패키지를 업로드하는 샘플 스크립트는 [Azure CLI를 사용하여 응용 프로그램을 Azure 배치에 추가](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application)를 참조하세요.

## <a name="pool-with-cloud-service-configuration-sample-script"></a>클라우드 서비스 구성이 있는 풀 샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Cloud Services Pools")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>가상 컴퓨터 구성이 있는 풀 샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Virtual Machine Pools")]

## <a name="clean-up-pools"></a>풀 정리

위의 샘플 스크립트를 실행한 후 다음 명령을 실행하여 풀을 삭제합니다.
```azurecli
az batch pool delete --pool-id mypool-windows
az batch pool delete --pool-id mypool-linux
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 Batch 풀을 만들고 조작합니다.
표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | 배치 계정에 대해 인증합니다.  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#az_batch_application_summary_list) | 배치 계정에 사용 가능한 응용 프로그램을 나열합니다.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | VM의 풀을 만듭니다.  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_set) | 풀의 속성을 업데이트합니다.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az_batch_pool_node_agent_skus_list) | 사용 가능한 노드 에이전트 SKU 및 이미지 정보를 나열합니다.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_resize) | 지정된 풀에서 실행 중인 VM 수의 크기를 조정합니다.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_show) | 풀의 속성을 표시합니다.  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_delete) | 지정된 풀을 삭제합니다.  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | 풀에서 자동 확장을 사용하고 수식을 적용합니다.  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | 풀에서 자동 확장을 사용하지 않습니다.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_list) | 지정된 풀에서 계산 노드를 모두 나열합니다.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_reboot) | 지정된 계산 노드를 다시 부팅합니다.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_delete) | 지정된 풀에서 나열된 노드를 삭제합니다.  |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Batch CLI 스크립트 샘플은 [Azure Batch CLI 설명서](../batch-cli-samples.md)에서 확인할 수 있습니다.


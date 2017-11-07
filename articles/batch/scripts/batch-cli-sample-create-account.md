---
title: "Azure CLI 스크립트 샘플 - 배치 계정 만들기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 배치 계정 만들기"
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
ms.openlocfilehash: fd2f4682a04c557b69bbfce115f41c54a96d462c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-batch-account-with-the-azure-cli"></a>Azure CLI로 배치 계정 만들기

이 스크립트는 Azure 배치 계정을 만들고 계정의 다양한 속성을 쿼리 및 업데이트하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

아직 Azure CLI를 설치하지 않은 경우 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)에 있는 지침을 사용하여 설치합니다.

## <a name="batch-account-sample-script"></a>배치 계정 샘플 스크립트

배치 계정을 만들 때 기본적으로 계산 노드가 Batch 서비스에 내부적으로 할당됩니다. 할당된 계산 노드에는 별도의 코어 할당량이 적용되며 공유 키 자격 증명 또는 Azure Active Dirctory 토큰을 통해 계정을 인증할 수 있습니다.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="batch-account-using-user-subscription-sample-script"></a>사용자 구독을 사용하는 배치 계정 샘플 스크립트

Batch에서 사용자 고유의 Azure 구독에 계산 노드를 만들도록 선택할 수도 있습니다.
계산 노드를 구독에 할당한 계정은 Azure Active Directory 토큰을 통해 인증되어야 하며 할당된 계산 노드는 구독 할당량에 포함됩니다. 이 모드에서 계정을 만들려면 계정을 만들 때 Key Vault를 지정해야 합니다.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "Create Account using User Subscription")]

## <a name="clean-up-deployment"></a>배포 정리

위의 샘플 스크립트 중 하나를 실행한 후 다음 명령을 실행하여 리소스 그룹 및 관련된 모든 리소스(배치 계정, Azure Storage 계정 및 Azure Key Vault)를 제거합니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 배치 계정 및 모든 관련된 리소스를 만듭니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az batch account create](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_create) | 배치 계정을 만듭니다.  |
| [az batch account set](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_set) | 배치 계정의 속성을 업데이트합니다.  |
| [az batch account show](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_show) | 지정된 배치 계정의 세부 정보를 검색합니다.  |
| [az batch account keys list](https://docs.microsoft.com/cli/azure/batch/account/keys#az_batch_account_keys_list) | 지정된 배치 계정의 액세스 키를 검색합니다.  |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | 추가 CLI 상호 작용을 위해 지정된 배치 계정에 대해 인증합니다.  |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | 저장소 계정을 만듭니다. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | 키 자격 증명 모음을 만듭니다. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | 지정된 주요 자격 증명 모음의 보안 정책을 업데이트합니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Batch CLI 스크립트 샘플은 [Azure Batch CLI 설명서](../batch-cli-samples.md)에서 확인할 수 있습니다.

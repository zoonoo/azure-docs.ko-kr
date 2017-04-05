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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 4d37dfc6c7110aa32788bbd3c3f81cd84a1426d2
ms.lasthandoff: 03/24/2017

---

# <a name="create-a-batch-account-with-the-azure-cli"></a>Azure CLI로 배치 계정 만들기

이 스크립트는 Azure 배치 계정을 만들고 계정의 다양한 속성을 쿼리 및 업데이트하는 방법을 보여 줍니다.

필요한 경우 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)에 있는 지침을 사용하여 Azure CLI를 설치한 다음, `az login`을 실행하여 Azure에 로그인합니다.

## <a name="batch-account-sample-script"></a>배치 계정 샘플 스크립트

배치 계정을 만들 때 기본적으로 계산 노드가 Batch 서비스에 내부적으로 할당됩니다. 할당된 계산 노드에는 별도의 코어 할당량이 적용되며 공유 키 자격 증명 또는 Azure Active Dirctory 토큰을 통해 계정을 인증할 수 있습니다.

[!code-azurecli[메인](../../../cli_scripts/batch/create-account/create-account.sh "계정 만들기")]

## <a name="batch-account-using-user-subscription-sample-script"></a>사용자 구독을 사용하는 배치 계정 샘플 스크립트

Batch에서 사용자 고유의 Azure 구독에 계산 노드를 만들도록 선택할 수도 있습니다.
계산 노드를 구독에 할당한 계정은 Azure Active Directory 토큰을 통해 인증되어야 하며 할당된 계산 노드는 구독 할당량에 포함됩니다. 이 모드에서 계정을 만들려면 계정을 만들 때 Key Vault를 지정해야 합니다.

[!code-azurecli[메인](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "사용자 구독을 사용하여 계정 만들기")]

## <a name="clean-up-deployment"></a>배포 정리

위의 샘플 스크립트 중 하나를 실행한 후 다음 명령을 실행하여 리소스 그룹 및 관련된 모든 리소스(배치 계정, Azure Storage 계정 및 Azure Key Vault)를 제거합니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 배치 계정 및 모든 관련된 리소스를 만듭니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az batch account create](https://docs.microsoft.com/cli/azure/batch/account#create) | 배치 계정을 만듭니다.  |
| [az batch account set](https://docs.microsoft.com/cli/azure/batch/account#set) | 배치 계정의 속성을 업데이트합니다.  |
| [az batch account show](https://docs.microsoft.com/cli/azure/batch/account#show) | 지정된 배치 계정의 세부 정보를 검색합니다.  |
| [az batch account keys list](https://docs.microsoft.com/cli/azure/batch/account/keys#list) | 지정된 배치 계정의 액세스 키를 검색합니다.  |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | 추가 CLI 상호 작용을 위해 지정된 배치 계정에 대해 인증합니다.  |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | 저장소 계정을 만듭니다. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#create) | 키 자격 증명 모음을 만듭니다. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#set-policy) | 지정된 주요 자격 증명 모음의 보안 정책을 업데이트합니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Batch CLI 스크립트 샘플은 [Azure Batch CLI 설명서](../batch-cli-samples.md)에서 확인할 수 있습니다.


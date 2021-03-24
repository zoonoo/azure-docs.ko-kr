---
title: 프리미엄 플랜에서 함수 앱 만들기 - Azure CLI
description: Azure CLI를 사용하여 Azure의 확장 가능한 프리미엄 플랜에서 함수 앱 만들기
ms.service: azure-functions
ms.topic: sample
ms.date: 11/23/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 714dd8d886f567bcdf07c93803662ef4b2c635f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94565165"
---
# <a name="create-a-function-app-in-a-premium-plan---azure-cli"></a>프리미엄 플랜에서 함수 앱 만들기 - Azure CLI

이 Azure Functions 샘플 스크립트는 함수의 컨테이너인 함수 앱을 만듭니다. 만든 함수 앱은 [확장 가능한 프리미엄 플랜](../functions-premium-plan.md)을 사용합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 이 자습서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="sample-script"></a>샘플 스크립트

이 스크립트는 [프리미엄 플랜](../functions-premium-plan.md)을 사용하여 함수 앱을 만듭니다.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-premium-plan/create-function-app-premium-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다. 이 스크립트는 다음 명령을 사용합니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Azure Storage 계정을 만듭니다. |
| [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | [특정 SKU](../functions-premium-plan.md#available-instance-skus)에서 프리미엄 계획을 만듭니다. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | App Service 계획의 함수 앱을 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure Functions CLI 스크립트 샘플은 [Azure Functions 설명서](../functions-cli-samples.md)에서 확인할 수 있습니다.

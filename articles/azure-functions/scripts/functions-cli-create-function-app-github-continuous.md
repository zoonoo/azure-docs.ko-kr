---
title: GitHub 배포를 사용하여 함수 앱 만들기 - Azure CLI
description: 함수 앱을 만들고 Azure Functions를 사용하여 GitHub 리포지토리에서 함수 코드를 배포합니다.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 219e993ad7132c90de6db680facc9b8f815947cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934393"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Azure에서 GitHub로부터 배포되는 함수 앱 만들기

이 Azure Functions 샘플 스크립트는 관련 리소스와 함께 [소비 계획](../consumption-plan.md)을 사용하여 함수 앱을 만듭니다. 이 스크립트는 GitHub 리포지토리에서 지속적으로 배포하기 위한 함수 코드도 구성합니다. 

이 샘플에서는 다음이 필요합니다.

* 관리 권한이 있는 함수 코드를 포함하는 GitHub 리포지토리
* GitHub 계정에 대한 [PAT(개인 액세스 토큰)](https://help.github.com/articles/creating-an-access-token-for-command-line-use)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 이 자습서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 

## <a name="sample-script"></a>샘플 스크립트

이 샘플은 Azure 함수 앱을 만들고 GitHub의 함수 코드를 배포합니다.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다. 이 스크립트는 다음 명령을 사용합니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 함수 앱에 필요한 스토리지 계정을 만듭니다. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | 서버리스 [소비 계획](../consumption-plan.md)에서 함수 앱을 만들어 Git 또는 Mercurial 리포지토리와 연결합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure Functions CLI 스크립트 샘플은 [Azure Functions 설명서](../functions-cli-samples.md)에서 확인할 수 있습니다.

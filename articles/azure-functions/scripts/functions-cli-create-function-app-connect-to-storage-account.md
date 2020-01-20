---
title: 연결된 스토리지로 함수 앱 만들기 - Azure CLI
description: Azure CLI 스크립트 샘플 - Azure Storage에 연결하는 Azure Function 만들기
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc
ms.openlocfilehash: 833b9223d473c8bfc62485e9e47ba662a4f0e154
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922679"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>명명된 스토리지 계정 연결로 함수 앱 만들기 

이 Azure Functions 샘플 스크립트는 함수 앱을 만들고 해당 함수를 Azure Storage 계정에 연결합니다. 연결을 포함하는 생성된 앱 설정을 [스토리지 트리거 또는 바인딩](../functions-bindings-storage-blob.md)에 사용할 수 있습니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

로컬로 CLI를 사용하면 Azure CLI 버전 2.0 이상을 실행하는지 확인합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

이 샘플은 Azure 함수 앱을 만들고 앱 설정에 스토리지 연결 문자열을 추가합니다.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 위치를 사용하여 리소스 그룹을 만듭니다. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 스토리지 계정을 만듭니다. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | 서버리스 [소비 계획](../functions-scale.md#consumption-plan)에서 함수 앱을 만듭니다. |
| [az storage account show-connection-string](/cli/azure/storage/account#az-storage-account-show-connection-string) | 계정에 대한 연결 문자열을 가져옵니다. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | 함수 앱에서 연결 문자열을 앱 설정으로 설정합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure Functions CLI 스크립트 샘플은 [Azure Functions 설명서](../functions-cli-samples.md)에서 확인할 수 있습니다.

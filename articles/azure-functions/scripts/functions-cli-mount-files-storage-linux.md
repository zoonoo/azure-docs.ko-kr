---
title: Python 함수 앱에 파일 공유 탑재 - Azure CLI
description: 서버리스 Python 함수 앱을 만들고 Azure CLI를 사용하여 기존 파일 공유를 탑재합니다.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d0037cea24b1989c4f7a4d2ddd6bf3f8f7e812b3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762284"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Azure CLI를 사용하여 Python 함수 앱에 파일 공유 탑재

이 Azure Functions 샘플 스크립트는 함수 앱을 만들고 Azure Files에 공유를 만듭니다. 그런 다음, 함수를 통해 데이터에 액세스할 수 있도록 공유를 탑재합니다.  

>[!NOTE]
>생성된 함수 앱은 Python 버전 3.7에서 실행됩니다. 또한 Azure Functions는 [Python 버전 3.6 및 3.8을 지원합니다](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 이 자습서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 

## <a name="sample-script"></a>샘플 스크립트

이 스크립트는 [소비 계획](../consumption-plan.md)을 사용하여 Azure Functions에서 함수 앱을 만듭니다.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create a function app on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다. 이 스크립트는 다음 명령을 사용합니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Azure Storage 계정을 만듭니다. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | 함수 앱을 만듭니다. |
| [az storage share create](/cli/azure/storage/share#az_storage_share_create) | 스토리지 계정에 Azure Files 공유를 만듭니다. | 
| [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create) | 공유에 디렉터리를 만듭니다. |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) | 함수 앱에 공유를 탑재합니다. |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_list) | 함수 앱에 탑재된 파일 공유를 표시합니다. | 

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure Functions CLI 스크립트 샘플은 [Azure Functions 설명서](../functions-cli-samples.md)에서 확인할 수 있습니다.

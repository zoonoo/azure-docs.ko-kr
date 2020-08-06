---
title: Python 함수 앱에 파일 공유 탑재 - Azure CLI
description: 서버리스 Python 함수 앱을 만들고 Azure CLI를 사용하여 기존 파일 공유를 탑재합니다.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63ab9ba3219dc600187e73bbf124d62d3f51317a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498211"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Azure CLI를 사용하여 Python 함수 앱에 파일 공유 탑재

이 Azure Functions 샘플 스크립트는 함수 앱을 만들고 Azure Files에 공유를 만듭니다. 그런 다음, 함수를 통해 데이터에 액세스할 수 있도록 공유를 탑재합니다.  

>[!NOTE]
>생성된 함수 앱은 Python 버전 3.7에서 실행됩니다. 또한 Azure Functions는 [Python 버전 3.6 및 3.8을 지원합니다](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 예제는 Bash 셸에 대해 작성되었으며, Windows 명령 프롬프트에서 실행되도록 수정되어야 합니다. 

## <a name="sample-script"></a>샘플 스크립트

이 스크립트는 [소비 계획](../functions-scale.md#consumption-plan)을 사용하는 Azure 함수 앱을 만듭니다.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다. 이 스크립트는 다음 명령을 사용합니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Azure Storage 계정을 만듭니다. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | 함수 앱을 만듭니다. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | 스토리지 계정에 Azure Files 공유를 만듭니다. | 
| [az storage directory create](/cli/azure/storage/directory#az-storage-directory-create) | 공유에 디렉터리를 만듭니다. |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | 함수 앱에 공유를 탑재합니다. |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | 함수 앱에 탑재된 파일 공유를 표시합니다. | 

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure Functions CLI 스크립트 샘플은 [Azure Functions 설명서](../functions-cli-samples.md)에서 확인할 수 있습니다.

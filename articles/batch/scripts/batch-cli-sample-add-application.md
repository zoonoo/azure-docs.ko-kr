---
title: Azure CLI 스크립트 예제 - Batch로 애플리케이션 추가
description: 이 샘플 스크립트는 Azure Batch 풀 또는 작업에 사용할 애플리케이션을 추가하는 방법을 보여줍니다.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 414a401168b1b378ed33f4904607243de0267d22
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565809"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI 예제: Azure Batch 계정에 애플리케이션 추가

이 스크립트는 Azure Batch 풀 또는 작업에 사용할 애플리케이션을 추가하는 방법을 보여줍니다. Batch 계정에 추가할 애플리케이션을 설정하려면 실행 파일을 해당 종속성과 함께 zip 파일로 패키지로 만듭니다. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 이 자습서에는 Azure CLI 버전 2.0.20 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다.
표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 스토리지 계정을 만듭니다. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Batch 계정을 만듭니다. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | 추가 CLI 상호 작용을 위해 지정된 Batch 계정에 대해 인증합니다.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | 애플리케이션을 만듭니다.  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | 지정된 애플리케이션에 애플리케이션 패키지를 추가합니다.  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | 애플리케이션의 속성을 업데이트합니다.  |
| [az group delete](/cli/azure/group#az-group-delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

---
title: "함수 앱 만들기 및 GitHub의 함수 코드 배포 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 함수 앱 만들기 및 GitHub의 함수 코드 배포"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: f87cf7d300b4c2b89ad692aadcda958e9747c7f9
ms.contentlocale: ko-kr
ms.lasthandoff: 05/15/2017

---
# <a name="create-a-function-app-and-deploy-function-code-from-github"></a>함수 앱 만들기 및 GitHub의 함수 코드 배포

이 샘플 스크립트는 관련된 리소스를 통해 [소비 계획](../functions-scale.md#consumption-plan)을 사용하여 함수 앱을 만든 다음 연속 배포를 사용하지 않고 공용 GitHub 리포지토리의 함수 코드를 배포합니다. GitHub의 지속적인 함수 코드 전송에 관해서는 [함수 앱 만들기 및 GitHub에서 지속적으로 배포](functions-cli-create-function-app-github-continuous.md)를 읽어보세요.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

이 샘플은 Azure 함수 앱을 만들고 GitHub의 함수 코드를 배포합니다.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "GitHub의 배포를 사용하여 함수 앱 만들기")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다. 이 스크립트는 다음 명령을 사용합니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service 계획을 만듭니다. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Azure 함수 앱을 만듭니다. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Git 또는 Mercurial 리포지토리를 사용하여 함수 앱에 연결합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Azure Functions CLI 스크립트 샘플은 [Azure Functions 설명서](../functions-cli-samples.md)에서 확인할 수 있습니다.


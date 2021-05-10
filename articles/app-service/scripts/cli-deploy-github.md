---
title: 'CLI: GitHub에서 앱 배포'
description: Azure CLI를 사용하여 App Service 앱의 배포 및 관리를 자동화하는 방법을 알아봅니다. 이 샘플에서는 GitHub에서 앱을 배포하는 방법을 보여줍니다.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 655c98e5249b66b114dfbe1d88cf951001d07af2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787877"
---
# <a name="create-an-app-service-app-with-deployment-from-github-using-azure-cli"></a>Azure CLI를 사용하여 GitHub에서 배포를 통해 App Service 앱 만들기

이 샘플 스크립트는 관련된 리소스를 사용하여 App Service에서 앱을 만듭니다. 그런 다음, 지속적인 배포를 사용하지 않고 공용 GitHub 리포지토리에서 앱 코드를 배포합니다. 지속적인 배포를 사용하는 GitHub 배포는 [GitHub의 지속적인 배포를 사용하여 앱 만들기](cli-continuous-deployment-github.md)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 이 자습서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Create an app with deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명 

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | App Service 계획을 만듭니다. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | App Service 앱을 만듭니다. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Git 또는 Mercurial 리포지토리를 사용하여 App Service 앱에 연결합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../samples-cli.md)에서 확인할 수 있습니다.

---
title: 'CLI: 스테이징 슬롯에 배포'
description: Azure CLI를 사용하여 App Service 앱의 배포 및 관리를 자동화하는 방법을 알아봅니다. 이 샘플에서는 스테이징 슬롯에 코드를 배포하는 방법을 보여줍니다.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 74f88d1a7960452d8bd5548a1927e7fc50fc42de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97006084"
---
# <a name="create-an-app-service-app-and-deploy-code-to-a-staging-environment-using-azure-cli"></a>Azure CLI를 사용하여 App Service 앱 만들기 및 스테이징 환경에 코드 배포

이 샘플 스크립트는 “스테이징”이라는 추가 배포 슬롯을 사용하여 App Service에서 앱을 만든 다음, “스테이징” 슬롯에 샘플 앱을 배포합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 이 자습서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Create an app and deploy code to a staging environment")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | App Service 계획을 만듭니다. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | App Service 앱을 만듭니다. |
| [`az webapp deployment slot create`](/cli/azure/webapp/deployment/slot#az-webapp-deployment-slot-create) | 배포 슬롯을 만듭니다. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config) | Git 또는 Mercurial 리포지토리를 사용하여 App Service 앱에 연결합니다. |
| [`az webapp deployment slot swap`](/cli/azure/webapp/deployment/slot#az-webapp-deployment-slot-swap) | 지정된 배포 슬롯을 프로덕션으로 교환합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../samples-cli.md)에서 확인할 수 있습니다.

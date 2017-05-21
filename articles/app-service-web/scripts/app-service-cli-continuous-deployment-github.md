---
title: "Azure CLI 스크립트 샘플 - GitHub의 연속 배포를 사용하여 웹앱 만들기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - GitHub의 연속 배포를 사용하여 웹앱 만들기"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: dfb4c7651f6cfd378f9b1706abcdd732e56d7eb1
ms.contentlocale: ko-kr
ms.lasthandoff: 05/15/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>GitHub의 연속 배포를 사용하여 웹앱 만들기

이 샘플 스크립트는 관련된 리소스를 사용하여 App Service에서 웹앱을 만든 다음 GitHub 리포지토리의 연속 배포를 설정합니다. 연속 배포를 사용하지 않는 GitHub 배포는 [웹앱 만들기 및 GitHub의 코드 배포](app-service-cli-deploy-github.md)를 참조하세요. 이 샘플에는 다음이 필요합니다.

* 관리 권한이 있는 응용 프로그램 코드를 포함하는 GitHub 리포지토리
* GitHub 계정에 대한 [PAT(개인 액세스 토큰)](https://help.github.com/articles/creating-an-access-token-for-command-line-use)

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="create-app-sample"></a>앱 샘플 만들기

[!code-azurecli-interactive[기본](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "GitHub의 연속 배포를 사용하여 웹앱 만들기")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service 계획을 만듭니다. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Azure 웹앱을 만듭니다. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Git 또는 Mercurial 리포지토리를 사용하여 Azure 웹앱에 연결합니다. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | 브라우저에서 Azure 웹앱을 엽니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../app-service-cli-samples.md)에서 확인할 수 있습니다.


---
title: "Azure CLI 스크립트 샘플 - GitHub의 배포를 사용하여 웹앱 만들기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - GitHub의 배포를 사용하여 웹앱 만들기"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: ab4be9d9a03001789bed90e58d62fe1891608823
ms.lasthandoff: 03/11/2017

---


# <a name="create-a-web-app-with-deployment-from-github"></a>GitHub의 배포를 사용하여 웹앱 만들기

이 샘플 스크립트는 Azure CLI 2.0을 사용하여 다음을 수행합니다.

* 서유럽 Azure 지역의 Azure App Service에서 웹앱을 만듭니다.
* GitHub의 웹앱 코드를 배포합니다.
* 브라우저에서 배포된 Azure 웹앱을 표시합니다.

## <a name="prerequisites"></a>필수 조건

* `az login`을 실행하여 Azure에 로그인합니다.
* GitHub 리포지토리에 사용자의 웹앱 코드를 배치합니다.

> [!NOTE]
> 사용자가 소유하지 않은 공용 GitHub 리포지토리를 사용하는 경우 App Service는 해당 GitHub 리포지토리의 코드를 배포하지만 연속 배포에 필요한 SSH 키 및 웹후크를 설정할 수 없습니다.
>
>

## <a name="create-app-sample"></a>앱 샘플 만들기

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "GitHub의 배포를 사용하여 웹앱 만들기")]

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

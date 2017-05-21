---
title: "Azure CLI 스크립트 샘플 - Azure Container Registry의 Docker 컨테이너에서 ASP.NET Core 웹앱 만들기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Azure Container Registry의 Docker 컨테이너에서 ASP.NET Core 웹앱 만들기"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: ec524ed8dd4cc58b948d3047c36a9f7913d7d041
ms.contentlocale: ko-kr
ms.lasthandoff: 05/15/2017

---

# <a name="create-an-aspnet-core-web-app-in-a-docker-container-from-azure-container-registry"></a>Azure Container Registry의 Docker 컨테이너에서 ASP.NET Core 웹앱 만들기

이 시나리오에서는 리소스 그룹, Linux App Service 계획 및 웹앱을 만들고 Docker 컨테이너를 사용하여 Azure Container Registry에서 ASP.NET Core 응용 프로그램을 배포하는 방법을 알아봅니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="create-app-sample"></a>앱 샘플 만들기

[!code-azurecli-interactive[기본](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh?highlight=6-9 "Linux Azure Container Registry")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service 계획을 만듭니다. Azure 웹앱에 대한 서버 팜과 비슷합니다. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | App Service 계획 내에서 Azure 웹앱을 만듭니다. |
| [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update) | Azure 웹앱에 대한 Docker 컨테이너를 설정합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../app-service-cli-samples.md)에서 확인할 수 있습니다.


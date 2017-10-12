---
title: "Azure CLI 스크립트 샘플 - 전세계에 고가용성 아키텍처를 가진 웹앱 확장 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 전세계에 고가용성 아키텍처를 가진 웹앱 확장"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: e4033a50-0e05-4505-8ce8-c876204b2acc
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 52679af9cd8f6b455f68b9307ed52202ff74349d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="scale-a-web-app-worldwide-with-a-high-availability-architecture"></a>전세계에 고가용성 아키텍처를 가진 웹앱 확장

이 시나리오에서는 리소스 그룹, 두 개의 App Service 계획, 두 개의 웹앱, Traffic Manager 프로필 및 두 개의 Traffic Manager 끝점을 만듭니다. 실행이 완료되면 가장 낮은 네트워크 대기 시간에 따라 웹앱의 전역적 가용성을 제공하는 가용성이 좋은 아키텍처를 사용할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 


## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh "Geographic Scale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 웹앱, Traffic Manager 프로필 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service 계획을 만듭니다. Azure 웹앱에 대한 서버 팜과 비슷합니다. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Azure 웹앱을 만듭니다. |
| [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Azure Traffic Manager 프로필을 만듭니다. |
| [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) | Azure Traffic Manager 프로필에 끝점을 추가합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../app-service-cli-samples.md)에서 확인할 수 있습니다.

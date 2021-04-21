---
title: Azure CLI 스크립트 샘플 - Application Gateway와 App Service 통합 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - Application Gateway와 App Service 통합
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: d30cc27fc3c546619e85bb9aabd0b31c10102e96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787812"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>CLI를 사용하여 Application Gateway와 App Service 통합

이 샘플 스크립트는 Azure App Service 웹앱, Azure Virtual Network 및 Application Gateway를 만듭니다. 그런 다음, 웹앱의 트래픽이 Application Gateway 서브넷에서만 시작하도록 제한합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 이 자습서에는 Azure CLI 버전 2.0.74 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, App Service 앱, Cosmos DB 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [`az network vnet create`](/cli/azure/network/vnet#az_network_vnet_create) | 가상 네트워크를 만듭니다. |
| [`az network public-ip create`](/cli/azure/network/public-ip#az_network_public_ip_create) | 공용 IP 주소를 만듭니다. |
| [`az network public-ip show`](/cli/azure/network/public-ip#az_network_public_ip_show) | 공용 IP 주소의 세부 정보를 표시합니다. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | App Service 계획을 만듭니다. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | App Service 웹앱을 만듭니다. |
| [`az webapp show`](/cli/azure/webapp#az_webapp_show) | App Service 웹앱의 세부 정보를 표시합니다. |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction#az_webapp_config_access_restriction_add) | 액세스 제한을 App Service 웹앱에 추가합니다. |
| [`az network application-gateway create`](/cli/azure/network/application-gateway#az_network_application_gateway_create) | Application Gateway를 만듭니다. |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings#az_network-application-gateway-http_settings_update) | Application Gateway HTTP 설정을 업데이트합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../samples-cli.md)에서 확인할 수 있습니다.

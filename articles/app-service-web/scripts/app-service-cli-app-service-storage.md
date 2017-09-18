---
title: "Azure CLI 스크립트 샘플 - 저장소 계정에 웹앱 연결 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 저장소 계정에 웹앱 연결"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: a07c2ae64be2081a2dba172ada810b76bfd7dfd7
ms.contentlocale: ko-kr
ms.lasthandoff: 09/09/2017

---

# <a name="connect-a-web-app-to-a-storage-account"></a>저장소 계정에 웹앱 연결

이 시나리오에서는 Azure Storage 계정 및 Azure 웹앱을 만드는 방법을 알아봅니다. 그런 다음 앱 설정을 사용하여 저장소 계정을 웹앱에 연결합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 


## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[기본](../../../cli_scripts/app-service/connect-to-storage/connect-to-storage.sh "Azure Storage")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 웹앱, 저장소 계정 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service 계획을 만듭니다. Azure 웹앱에 대한 서버 팜과 비슷합니다. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Azure 웹앱을 만듭니다. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | 저장소 계정을 만듭니다. 고정 자산이 저장될 위치입니다. |
| [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_show_connection_string) | |
| [az webapp config appsettings set](https://docs.microsoft.com/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Azure 웹앱에 대한 앱 설정을 만들거나 업데이트합니다. 앱 설정은 앱에 대한 환경 변수로 노출됩니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../app-service-cli-samples.md)에서 확인할 수 있습니다.


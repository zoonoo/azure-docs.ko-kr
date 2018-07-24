---
title: Azure CLI 스크립트 샘플 - 함수 앱에 사용자 지정 도메인 매핑 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - Azure의 함수 앱에 사용자 지정 도메인을 매핑합니다.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 07/04/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 0650fffeb54ebc4390c82fb2711d7c89e0ac4572
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989421"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>함수 앱에 사용자 지정 도메인 매핑

이 샘플 스크립트는 App Service 계획에서 함수 앱을 만든 다음, 제공하는 사용자 지정 도메인에 매핑합니다. 함수 앱이 [App Service 계획](../functions-scale.md#app-service-plan)에서 호스트된 경우 CNAME 또는 A 레코드 중 하나를 사용하여 사용자 지정 도메인을 매핑할 수 있습니다. [사용 계획](../functions-scale.md#consumption-plan)에서 함수 앱의 경우 CNAME 옵션만 지원됩니다. 이 샘플은 App Service 계획을 만들고 A 레코드에게 도메인을 매핑할 것을 요구합니다. 

이 샘플 스크립트를 실행하려면 웹앱의 기본 도메인 이름을 가리키는 사용자 지정 도메인에 이미 A 레코드를 구성해야 합니다. 자세한 내용은 [Azure App Service에 대한 사용자 지정 도메인 지침 매핑](https://aka.ms/appservicecustomdns)을 참조합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하려는 경우 Azure CLI 버전 2.0 이상을 사용해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 


## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 표의 각 명령은 명령 관련 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | 함수 앱에 필요한 저장소 계정을 만듭니다. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | 사용자 지정 도메인을 매핑하는 데 필요한 App Service 계획을 만듭니다. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | App Service 계획의 함수 앱을 만듭니다. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | 함수 앱에 사용자 지정 도메인을 매핑합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 Functions CLI 스크립트 샘플은 [Azure Functions 설명서](../functions-cli-samples.md)에서 확인할 수 있습니다.

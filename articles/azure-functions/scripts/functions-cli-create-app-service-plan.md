---
title: "Azure CLI 스크립트 샘플 - App Service 계획에서 함수 앱 만들기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - App Service 계획에서 함수 앱 만들기"
services: functions
documentationcenter: functions
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0d887999d1118e498293fb13b9612828d914b0ae
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-function-app-in-an-app-service-plan"></a>App Service 계획에서 함수 앱 만들기

이 샘플 스크립트는 함수에 대한 컨테이너인 Azure 함수 앱을 만듭니다. 함수 앱은 전용 App Service 계획을 사용하여 생성됩니다. 이는 서버 리소스가 항상 켜져 있음을 의미합니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

앱 샘플 만들기

[!code-azurecli[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "App Service 계획에서 Azure Function 만들기")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, App Service 앱 및 모든 관련된 리소스를 제거할 수 있습니다.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az appserviceplan create](https://docs.microsoft.com/cli/azure/appserviceplan#create) | App Service 계획을 만듭니다. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#delete) | Azure 함수 앱을 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Azure Functions CLI 스크립트 샘플은 [Azure Functions 설명서](../functions-cli-samples.md)에서 확인할 수 있습니다.

---
title: Azure App Service 계획에서 Linux로 실행되는 함수 앱 만들기
description: Azure CLI를 사용하여 App Service 계획에서 Linux로 실행되는 함수 앱을 만드는 방법에 알아봅니다.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: ec7b71c7da19ecefc14696c029e63a074b498ec8
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696751"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan-preview"></a>Azure App Service 계획에서 Linux로 실행되는 함수 앱 만들기(미리 보기)

Azure Functions를 사용하면 Linux에서 기본 Azure App Service 컨테이너에 함수를 호스트할 수 있습니다. 이 문서에서는 Azure CLI를 사용하여 [App Service 계획](functions-scale.md#app-service-plan)에서 실행되는 Azure로 Linux 기반 호스트 함수 앱을 만드는 방법을 보여 줍니다. [고유한 사용자 지정 컨테이너를 가져올 수도](functions-create-function-linux-custom-image.md) 있습니다. Linux 호스팅은 현재 미리 보기로 제공됩니다.

App Service 계획에서 함수 앱을 크기 조정을 할 수 있습니다. Azure Functions의 서버리스 기능을 이용하려면 [Consumption plan](functions-scale.md#consumption-plan)에서 Linux 기반 함수를 호스팅할 수 있습니다.

Mac, Windows 또는 Linux 컴퓨터를 사용하여 아래 단계를 따르면 됩니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

+ 활성 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0.21 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Linux App Service 계획 만들기

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Linux에서 함수 앱 만들기

Linux에서 함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱은 함수 코드 실행을 위한 환경을 제공합니다. 이를 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 리소스를 관리, 배포 및 공유할 수 있습니다. Linux App Service 계획과 함께 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 명령을 사용하여 함수 앱을 만듭니다.

다음 명령에서 `<app_name>` 자리 표시자 및 `<storage_name>`의 저장소 계정 이름을 고유한 함수 앱 이름으로 바꿉니다. `<app_name>`은 함수 앱의 기본 DNS 도메인으로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다. 또한 `dotnet`(C#), `node`(JavaScript) 또는 `python`에서 함수 앱에 대한 `<language>` 런타임을 설정해야 합니다.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --plan myAppServicePlan \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

함수 앱을 만들고 배포했으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

`myAppServicePlan`은 Linux 계획이기 때문에 Linux에서 함수 앱을 실행하는 컨테이너를 만드는 데 기본 제공 Docker 이미지가 사용됩니다.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources-simple.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure에서 Linux 기반에서 호스트되는 함수 앱을 만드는 방법을 보여 줍니다. 이 함수 앱에 대한 [함수 프로젝트를 배포](https://docs.microsoft.com/cli/azure/functionapp/deployment/source?view=azure-cli-latest)할 수 있습니다. Azure Functions Core Tools를 사용하여 로컬 컴퓨터에 [Functions 프로젝트 만들거나](functions-run-local.md) 새 Linux 함수 앱에 배포할 수 있습니다.  

> [!div class="nextstepaction"] 
> [Azure Functions를 로컬에서 코딩 및 테스트](functions-run-local.md)

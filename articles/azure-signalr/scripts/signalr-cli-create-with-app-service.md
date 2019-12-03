---
title: Azure CLI를 사용하여 App Service를 통해 SignalR Service 만들기
description: App CLI를 사용하여 App Service로 SignalR Service를 만듭니다. Azure SignalR Service에 대한 모든 CLI 명령에 대해 알아봅니다.
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: eb45adb699ef0f97782e6cdb8e141e254b3993f9
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158031"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>App Service로 SignalR Service 만들기

이 샘플 스크립트는 실시간 콘텐츠 업데이트를 클라이언트에 푸시하는 데 사용되는 새 Azure SignalR Service 리소스를 만듭니다. 또한 이 스크립트는 SignalR Service를 사용하는 ASP.NET Core Web App을 호스팅하기 위한 새 Web App 및 App Service 계획을 추가합니다. 웹앱은 새 SignalR Service 리소스에 연결하는 *AzureSignalRConnectionString*이라는 앱 설정으로 구성됩니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

이 스크립트는 Azure CLI용 *signalr* 확장을 사용합니다. 이 샘플 스크립트를 사용하기 전에 다음 명령을 실행하여 Azure CLI용 *signalr* 확장을 설치합니다.

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate unique names for the SignalR service, resource group, 
# app service, and app service plan
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"
myWebAppName=SignalRTestWebApp$randomNum
myAppSvcPlanName=$myAppSvcName"Plan"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Create an App Service plan.
az appservice plan create --name $myAppSvcPlanName --resource-group $myResourceGroupName --sku FREE

# Create the Web App
az webapp create --name $myWebAppName --resource-group $myResourceGroupName --plan $myAppSvcPlanName  

# Get the SignalR primary connection string
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "AzureSignalRConnectionString=$primaryConnectionString"
```

새 리소스 그룹에 대해 생성된 실제 이름을 적어 둡니다. 이 이름은 출력에 표시됩니다. 모든 그룹 리소스를 삭제하려는 경우 해당 리소스 그룹 이름을 사용합니다.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다. 이 스크립트는 다음 명령을 사용합니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Azure SignalR Service 리소스를 만듭니다. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | SignalR을 통해 실시간 콘텐츠 업데이트를 푸시할 때 애플리케이션에서 사용할 키를 나열합니다. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | 웹앱을 호스팅하기 위한 Azure App Service 계획을 만듭니다. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | App Service 호스팅 계획을 사용하는 Azure 웹앱을 만듭니다. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | 웹앱에 대한 새 앱 설정을 추가합니다. 이 앱 설정은 SignalR 연결 문자열을 저장하는 데 사용됩니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure SignalR Service CLI 스크립트 샘플은 [Azure SignalR Service 설명서](../signalr-reference-cli.md)에서 확인할 수 있습니다.

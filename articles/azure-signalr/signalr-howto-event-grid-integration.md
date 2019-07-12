---
title: Event Grid에 Azure SignalR Service 이벤트를 보내는 방법
description: SignalR 서비스에 대 한 Event Grid 이벤트를 사용 하도록 설정 하는 방법을 하는 지침을 다음 클라이언트 연결 연결/연결이 끊긴 상태로 이벤트 보내기 샘플 응용 프로그램입니다.
services: azure-signalr
author: chenyl
ms.service: azure-signalr
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: chenyl
ms.openlocfilehash: 2d782306938136ce6d21a331185f591316f58a29
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789177"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Azure SignalR Service에서 Event Grid로 이벤트를 전송 하는 방법

Azure Event Grid는 게시-구독 모델을 사용 하 여 균일 한 이벤트 소비를 제공 하는 완전히 관리 되는 이벤트 라우팅 서비스입니다. 이 가이드에서는 Azure CLI를 사용 하 여 Azure SignalR Service 만들기, 연결 이벤트를 구독 하 고 이벤트를 수신 하는 샘플 웹 응용 프로그램을 배포 합니다. 마지막으로 연결 하 고 및 분리 하 고, 샘플 응용 프로그램에서 이벤트 페이로드를 볼 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][azure-account]을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

이 문서에서 Azure CLI 명령은 **Bash** 셸에서 실행하도록 형식이 지정됩니다. PowerShell 또는 명령 프롬프트와 같은 다른 셸을 사용하는 경우 줄 연속 문자 또는 변수 할당 줄을 적절히 조정해야 합니다. 이 문서에서는 변수를 사용하여 필요한 명령 편집 작업을 최소화합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스를 배포하고 관리하는 논리 컨테이너입니다. 다음 [az 그룹 만들기][az-group-create] 명령은 이라는 리소스 그룹을 만듭니다 *myResourceGroup* 에 *eastus* 지역입니다. 리소스 그룹에 다른 이름을 사용하려면 `RESOURCE_GROUP_NAME`을 다른 값으로 설정합니다.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>SignalR Service 만들기

다음에 다음 명령 사용 하 여 리소스 그룹에 Azure Signalr Service를 배포 합니다.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

SignalR Service를 만든 후 Azure CLI 출력을 반환 합니다 다음과 비슷합니다.

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>이벤트 엔드포인트 만들기

이 섹션에서는 GitHub 리포지토리에 있는 Resource Manager 템플릿을 사용하여 Azure App Service에 미리 작성된 샘플 웹 애플리케이션을 배포합니다. 나중에 레지스트리의 Event Grid 이벤트를 구독하고 이벤트가 전송되는 엔드포인트로 이 앱을 지정합니다.

샘플 앱을 배포하려면 `SITE_NAME`을 웹앱의 고유한 이름으로 설정하고 다음 명령을 실행합니다. 사이트 이름은 웹앱의 FQDN(정규화된 도메인 이름)의 일부를 형성하기 때문에 Azure 내에서 고유해야 합니다. 이후 섹션에서 레지스트리의 이벤트를 보려면 웹 브라우저에서 앱의 FQDN으로 이동합니다.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

배포에 성공 하면 (몇 분 정도 걸릴 수 있습니다)를 확인 하기 위해 웹 앱으로 이동 하 고 브라우저를 열고 실행:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>레지스트리 이벤트 구독

Event Grid에서 *항목*을 구독하여 추적하려는 이벤트와 이벤트를 보낼 위치를 알립니다. 다음 [az eventgrid 이벤트 구독 만들기][az-eventgrid-event-subscription-create] 명령을 작성 하 고 이벤트를 보내야 하는 끝점으로 웹 앱의 URL을 지정 합니다. Azure SignalR Service를 구독 합니다. 이전 섹션에서 채워진 환경 변수가 여기에 재사용되므로 편집이 필요 없습니다.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

구독이 완료되면 다음과 비슷한 출력이 표시되어야 합니다.

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>레지스트리 이벤트 트리거

서비스 모드를 전환할 `Serverless Mode` SignalR Service 클라이언트 연결을 설정 합니다. 수행할 수 있습니다 [서버 리스 샘플](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) 참조로 합니다.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a seperate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>레지스트리 이벤트 보기

이제 클라이언트가 SignalR 서비스에 연결 했습니다. 이벤트 그리드 뷰어 웹 앱으로 이동 하 고 표시를 `ClientConnectionConnected` 이벤트입니다. 클라이언트를 종료 하는 경우 표시 됩니다는 `ClientConnectionDisconnected` 이벤트입니다.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create

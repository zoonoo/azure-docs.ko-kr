---
title: '자습서: Azure CLI를 사용하여 정책 상태 변경 이벤트를 Event Grid로 라우팅'
description: 이 자습서에서는 정책 상태 변경 이벤트를 수신 대기하고 웹후크를 호출하도록 Event Grid를 구성합니다.
ms.date: 03/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1fe87e4fd3349df7d8f5d57b2b2d95f95ed3fba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105734883"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>자습서: Azure CLI를 사용하여 정책 상태 변경 이벤트를 Event Grid로 라우팅

이 문서에서는 정책 상태 변경 이벤트를 웹 엔드포인트에 보내도록 Azure Policy 이벤트 구독을 설정하는 방법에 대해 알아봅니다. Azure Policy 사용자는 리소스에서 정책 상태가 변경될 때 내보내는 이벤트를 구독할 수 있습니다. 이러한 이벤트는 웹후크, [Azure Functions](../../../azure-functions/index.yml), [Azure Storage Queues](../../../storage/queues/index.yml) 또는 [Azure Event Grid](../../../event-grid/index.yml)에서 지원하는 다른 이벤트 처리기를 트리거할 수 있습니다. 일반적으로 이벤트 데이터를 처리하고 작업을 수행하는 엔드포인트에 이벤트를 보냅니다. 그러나 이 자습서를 간소화하기 위해 메시지를 수집하고 표시하는 웹앱에 이벤트를 보냅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

- 이 빠른 시작에서는 Azure CLI 버전 2.0.76 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

- 이전에 Azure Policy 또는 Event Grid를 사용한 경우에도 해당 리소스 공급자를 다시 등록합니다.

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Event Grid 토픽은 Azure 리소스이며 Azure 리소스 그룹에 배치해야 합니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. 

다음 예에서는 _westus_ 위치에 `<resource_group_name>`이라는 리소스 그룹을 만듭니다. `<resource_group_name>`을 리소스 그룹의 고유한 이름으로 바꿉니다.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>Event Grid 시스템 토픽 만들기

이제 리소스 그룹이 있으므로 [시스템 토픽](../../../event-grid/system-topics.md)을 만듭니다. Event Grid의 시스템 토픽은 Azure Policy 및 Azure Event Hubs와 같은 Azure 서비스에서 게시한 하나 이상의 이벤트를 나타냅니다. 이 시스템 토픽은 `Microsoft.PolicyInsights.PolicyStates` 토픽 유형을 Azure Policy 상태 변경에 사용합니다. **scope** 매개 변수의 `<SubscriptionID>`를 구독의 ID로 바꾸고, **resource-group** 매개 변수의 `<resource_group_name>`을 이전에 만든 리소스 그룹으로 바꿉니다.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>메시지 엔드포인트 만들기

토픽을 구독하기 전에 이벤트 메시지에 대한 엔드포인트를 만들어 보겠습니다. 일반적으로 엔드포인트는 이벤트 데이터를 기반으로 작업을 수행합니다. 이 빠른 시작을 간소화하기 위해 이벤트 메시지를 표시하는 [미리 작성된 웹앱](https://github.com/Azure-Samples/azure-event-grid-viewer)을 배포합니다. 배포된 솔루션은 App Service 계획, App Service 웹앱 및 GitHub의 소스 코드를 포함합니다.

`<your-site-name>`을 웹앱의 고유한 이름으로 바꿉니다. 웹앱 이름은 DNS 항목의 일부이므로 고유해야 합니다.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 성공된 후 실행하는지 확인하려면 웹앱을 봅니다. 웹 브라우저에서 `https://<your-site-name>.azurewebsites.net`으로 이동합니다.

현재 표시된 메시지가 없는 사이트가 표시되어야 합니다.

## <a name="subscribe-to-the-system-topic"></a>시스템 토픽 구독

항목을 구독하여 Event Grid에 추적하려는 이벤트와 해당 이벤트를 보낼 위치를 알립니다. 다음 예제에서는 만든 시스템 토픽을 구독하고, 웹앱의 URL을 엔드포인트로 전달하여 이벤트 알림을 받습니다. `<event_subscription_name>`을 이벤트 구독의 이름으로 바꿉니다. `<resource_group_name>` 및 `<your-site-name>`에는 앞에서 만든 값을 사용합니다.

웹앱에 대한 엔드포인트는 접미사 `/api/updates/`를 포함해야 합니다.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

웹앱을 다시 확인하고, 구독 유효성 검사 이벤트를 보냈음을 확인합니다. 눈 모양 아이콘을 선택하여 이벤트 데이터를 확장합니다. Event Grid는 유효성 검사 이벤트를 보내므로 엔드포인트는 이벤트 데이터를 수신하려는 것을 확인할 수 있습니다. 웹앱은 구독의 유효성을 검사하는 코드를 포함합니다.

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="미리 빌드된 웹앱의 Event Grid 구독 유효성 검사 이벤트에 대한 스크린샷":::

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

이 빠른 시작에서는 정책 할당을 만들고, **리소스 그룹에 태그 필요** 정의를 할당합니다. 이 정책 정의는 정책 할당 중에 구성된 태그가 없는 리소스 그룹을 식별합니다.

다음 명령을 실행하여 Event Grid 토픽을 보유하기 위해 만든 리소스 그룹으로 범위가 지정된 정책 할당을 만듭니다.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

이 명령은 다음 정보를 사용합니다.

- **이름** - 할당의 실제 이름입니다. 이 예제에서는 _requiredtags-events_ 가 사용되었습니다.
- **표시 이름** - 정책 할당에 대한 표시 이름입니다. 이 경우 _RG에 태그 필요_ 를 사용합니다.
- **범위** - 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 구독에서 리소스 그룹까지 다양한 범위가 있습니다. 리소스 그룹의 이름으로 &lt;범위&gt;를 바꿉니다. 리소스 그룹 범위의 형식은 `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>`입니다.
- **정책** – 할당을 만드는 데 기준으로 사용되는 정책 정의 ID입니다. 이 경우 _리소스 그룹에 태그 필요_ 정책 정의의 ID입니다. 정책 정의 ID를 가져오려면 이 명령을 실행합니다. `az policy definition list --query "[?displayName=='Require a tag on resource groups']"`

정책 할당이 만들어지면 **Microsoft.PolicyInsights.PolicyStateCreated** 이벤트 알림이 웹앱에 표시될 때까지 기다립니다. 만든 리소스 그룹에는 시작할 _NonCompliant_ 의 `data.complianceState` 값이 표시됩니다.

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="미리 빌드된 웹앱의 리소스 그룹에 대한 Event Grid 구독 정책 상태 생성 이벤트의 스크린샷":::

> [!NOTE]
> 리소스 그룹이 구독 또는 관리 그룹 계층 구조에서 다른 정책 할당을 상속하는 경우 각 할당에 대한 이벤트도 표시됩니다. `data.policyDefinitionId` 속성을 평가하여 이 자습서의 할당에 대한 이벤트인지 확인합니다.

## <a name="trigger-a-change-on-the-resource-group"></a>리소스 그룹에 대한 변경 트리거

리소스 그룹에서 규정을 준수하도록 하려면 이름이 **EventTest** 인 태그가 필요합니다. 다음 명령을 사용하여 태그를 리소스 그룹에 추가합니다. 여기서 `<SubscriptionID>`를 구독 ID로 바꾸고, `<ResourceGroup>`을 리소스 그룹 이름으로 바꿉니다.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

리소스 그룹에 필요한 태그가 추가되면 **Microsoft.PolicyInsights.PolicyStateChanged** 이벤트 알림이 웹앱에 표시될 때까지 기다립니다. 이벤트를 펼치면 이제 `data.complianceState` 값에 _Compliant_ 가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 웹앱 및 Azure Policy 이벤트 구독을 계속 사용하려는 경우 이 문서에서 만든 리소스를 정리하지 마세요. 계속 사용하지 않으려면 다음 명령을 사용하여 이 문서에서 만든 리소스를 삭제합니다.

`<resource_group_name>`을 위에서 만든 리소스 그룹으로 바꿉니다.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>다음 단계

이제 Azure Policy에 대한 토픽 및 이벤트 구독을 만드는 방법을 알아보았으므로 정책 상태 변경 이벤트 및 Event Grid에서 수행할 수 있는 방법에 대해 자세히 알아보세요.

- [Azure Policy 상태 변경 이벤트에 대응](../concepts/event-overview.md)
- [Event Grid에 대한 Azure Policy 스키마 세부 정보](../../../event-grid/event-schema-policy.md)
- [Event Grid 정보](../../../event-grid/overview.md)
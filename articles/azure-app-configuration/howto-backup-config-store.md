---
title: Azure App Configuration 저장소에서 키-값 자동 백업
description: App Configuration 저장소 간에 키-값 자동 백업을 설정하는 방법을 알아봅니다.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: 4b72de537f573d03f37a9b1de4341a14830479e7
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108748442"
---
# <a name="back-up-app-configuration-stores-automatically"></a>App Configuration 저장소 자동 백업

이 문서에서는 기본 Azure App Configuration 저장소에서 보조 저장소로 키-값 자동 백업을 설정하는 방법을 알아봅니다. 자동 백업에는 App Configuration과 Azure Event Grid의 통합이 사용됩니다. 

자동 백업을 설정하면 App Configuration이 구성 저장소에서 키-값에 수행된 모든 변경 사항에 대해 이벤트를 Azure Event Grid에 게시합니다. Event Grid는 키-값이 생성, 업데이트 또는 삭제될 때마다 사용자가 내보내진 이벤트를 구독할 수 있는 다양한 Azure 서비스를 지원합니다.

## <a name="overview"></a>개요

이 문서에서는 Azure Queue 스토리지를 사용하여 Event Grid에서 이벤트를 수신하고 Azure Functions의 타이머 트리거를 사용하여 배치에서 큐에 있는 이벤트를 처리합니다. 

함수가 트리거되면 이벤트를 기준으로 기본 App Configuration 저장소에서 변경된 키의 최신 값을 페치하고 그에 따라 보조 저장소를 업데이트합니다. 이 설정은 짧은 기간에 발생하는 여러 변경 사항을 하나의 백업 작업으로 조합하여 App Configuration 저장소에 요청을 과도하게 수행하는 것을 방지합니다.

![App Configuration저장소 백업의 아키텍처를 보여주는 다이어그램입니다.](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>리소스 프로비저닝

App Configuration 저장소 백업을 수행하는 근본적인 이유는 애플리케이션의 지역 간 복원력을 늘리기 위해 서로 다른 Azure 지역 간에 여러 구성 저장소를 사용하기 위한 것입니다. 이를 위해서는 기본 및 보조 저장소가 서로 다른 Azure 지역에 있어야 합니다. 이 자습서에서 만든 다른 모든 리소스는 사용자가 선택한 지역에 프로비전될 수 있습니다. 주 지역이 다운되면 주 지역에 다시 액세스할 수 있을 때까지 새로 백업할 항목이 없기 때문입니다.

이 자습서에서는 `centralus` 지역에 보조 저장소를 만들고 `westus` 지역에 다른 모든 리소스를 만듭니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)].

## <a name="prerequisites"></a>필수 구성 요소 

- Azure 개발 워크로드를 사용하는 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)

- [.NET Core SDK](https://dotnet.microsoft.com/download)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 자습서에는 Azure CLI 버전 2.3.1 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 `westus` 위치에 `<resource_group_name>`이라는 리소스 그룹을 만듭니다.  `<resource_group_name>`을 리소스 그룹의 고유한 이름으로 바꿉니다.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>App Configuration 저장소 만들기

서로 다른 지역에 기본 및 보조 App Configuration 저장소를 만듭니다.
`<primary_appconfig_name>` 및 `<secondary_appconfig_name>`을 구성 저장소의 고유 이름으로 바꿉니다. 각 저장소 이름은 DNS 이름으로 사용되기 때문에 고유해야 합니다.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-a-queue"></a>큐 만들기

Event Grid에서 게시된 이벤트를 수신하기 위해 스토리지 계정 및 큐를 만듭니다.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>App Configuration 저장소 이벤트 구독

기본 App Configuration 저장소에서 두 가지 이벤트를 구독합니다.

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

다음 명령은 큐에 전송된 두 이벤트에 대해 Event Grid 구독을 만듭니다. 엔드포인트 유형이 `storagequeue`로 설정되고, 엔드포인트는 큐 ID로 설정됩니다. `<event_subscription_name>`을 이벤트 구독에 대해 선택한 이름으로 바꿉니다.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-functions-for-handling-events-from-queue-storage"></a>큐 스토리지에서 이벤트를 처리하기 위해 함수를 만듭니다.

### <a name="set-up-with-ready-to-use-functions"></a>즉시 사용 가능한 함수 설정

이 문서에서는 다음 속성을 갖는 C# 함수를 사용합니다.
- 런타임 스택 .NET Core 3.1
- Azure Functions 런타임 버전 3.x
- 10분마다 타이머로 트리거되는 함수

데이터 백업을 더 쉽게 시작할 수 있도록 코드를 변경하지 않고도 사용할 수 있는 [함수가 테스트 및 게시](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup)되었습니다. 프로젝트 파일을 다운로드하고 [Visual Studio에서 사용자 고유의 함수 앱에 게시](../azure-functions/functions-develop-vs.md#publish-to-azure)합니다.

> [!IMPORTANT]
> 다운로드한 코드의 환경 변수는 변경하지 마세요. 다음 섹션에서 필요한 앱 설정을 만듭니다.
>

### <a name="build-your-own-function"></a>사용자 고유 함수 빌드

이전에 제공된 샘플 코드가 요구 사항을 충족하지 못할 경우 사용자 고유의 함수를 만들 수도 있습니다. 백업 완료를 위해 함수가 다음 작업을 수행할 수 있어야 합니다.
- Event Grid의 알림이 포함되었는지 확인하기 위해 큐 콘텐츠를 정기적으로 읽습니다. 구현 세부 정보는 [스토리지 큐 SDK](../storage/queues/storage-quickstart-queues-dotnet.md)를 참조하세요.
- 큐에 [Event Grid의 이벤트 알림](./concept-app-configuration-event.md#event-schema)이 포함되었으면 이벤트 메시지에서 모든 고유한 `<key, label>` 정보를 추출합니다. 키와 레이블의 조합은 기본 저장소에서 키-값 변경 사항에 대한 고유 식별자입니다.
- 기본 저장소에서 모든 설정을 읽습니다. 큐에 해당 이벤트가 포함된 보조 저장소에서 해당 설정만 업데이트합니다. 기본 저장소에는 없지만 대기열에 있었던 모든 설정을 보조 저장소에서 삭제합니다. [App Configuration SDK](https://github.com/Azure/AppConfiguration#sdks)를 사용하여 구성 저장소에 프로그래밍 방식으로 액세스할 수 있습니다.
- 처리하는 동안 예외가 발생하지 않았으면 큐에서 메시지를 삭제합니다.
- 필요에 따라 오류 처리를 구현합니다. 처리해야 할 수 있는 몇 가지 일반 예외를 보려면 앞의 샘플 코드를 참조하세요.

함수 만들기에 대해 알아보려면 [Azure에서 타이머로 트리거되는 함수 만들기](../azure-functions/functions-create-scheduled-function.md) 및 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md)을 참조하세요.


> [!IMPORTANT]
> 최선의 판단에 따라 기본 구성 저장소의 변경 빈도를 기준으로 타이머 일정을 선택합니다. 함수를 너무 자주 실행하면 결국 저장소에 대한 요청이 제한될 수 있습니다.
>


## <a name="create-function-app-settings"></a>함수 앱 설정 만들기

제공된 함수를 사용하는 경우 함수 앱에서 다음 앱 설정이 필요합니다.
- `PrimaryStoreEndpoint`: 기본 App Configuration 저장소의 엔드포인트입니다. 예제는 `https://{primary_appconfig_name}.azconfig.io`입니다.
- `SecondaryStoreEndpoint`: 보조 App Configuration 저장소의 엔드포인트입니다. 예제는 `https://{secondary_appconfig_name}.azconfig.io`입니다.
- `StorageQueueUri`: 큐 URI입니다. 예제는 `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`입니다.

다음 명령은 함수 앱에 필요한 앱 설정을 만듭니다. `<function_app_name>`은 함수 앱 이름으로 바꿉니다.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>함수 앱의 관리 ID에 대해 액세스 권한을 부여합니다.

다음 명령 또는 [Azure Portal](../app-service/overview-managed-identity.md#add-a-system-assigned-identity)을 사용하여 함수 앱의 시스템 할당 관리 ID를 추가합니다.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> 필요한 리소스 생성 및 역할 관리를 수행하려면 적절한 범위(구독 또는 리소스 그룹)에서 `Owner` 권한이 필요합니다. 역할 할당에 대한 도움이 필요하면 [Azure Portal을 사용하여 Azure 역할을 할당하는 방법](../role-based-access-control/role-assignments-portal.md)을 알아보세요.

다음 명령 또는 [Azure Portal](./howto-integrate-azure-managed-service-identity.md#grant-access-to-app-configuration)을 사용하여 함수 앱의 관리 ID에 App Configuration 저장소 액세스 권한을 부여합니다. 다음 경우에 이 역할을 사용합니다.
- 기본 App Configuration 저장소에서 `App Configuration Data Reader` 역할을 할당합니다.
- 보조 App Configuration 저장소에서 `App Configuration Data Owner` 역할을 할당합니다.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

다음 명령 또는 [Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal)을 사용하여 함수 앱의 관리 ID에 큐 액세스 권한을 부여합니다. 큐에서 `Storage Queue Data Contributor` 역할을 할당합니다.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>App Configuration 이벤트 트리거

모든 것이 작동하는지 테스트하기 위해 기본 저장소에서 키-값을 만들거나, 업데이트 또는 삭제할 수 있습니다. 타이머가 Azure Functions를 트리거하고 몇 초 후 보조 저장소에 이러한 변경 사항이 자동으로 표시됩니다.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

이벤트를 트리거했습니다. 잠시 후 Event Grid가 큐에 이벤트 알림을 전송합니다. *다음에 예약된 함수 실행 이후*, 보조 저장소에서 구성 설정을 보고 기본 저장소의 업데이트된 키-값이 포함되었는지 확인합니다.

> [!NOTE]
> 예약된 타이머 트리거를 기다리지 않고 테스트 및 문제 해결 중에 [함수를 수동으로 트리거](../azure-functions/functions-manually-run-non-http.md)할 수 있습니다.

백업 함수가 성공적으로 실행되었는지 확인한 후 보조 저장소에 이제 키가 제공된 것을 볼 수 있습니다.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>문제 해결

보조 저장소에 새 설정이 표시되지 않는 경우:

- 기본 저장소에서 설정을 만든 *후* 백업 함수가 트리거되었는지 확인합니다.
- Event Grid가 큐에 이벤트 알림을 정시에 전송하지 못할 수 있습니다. 큐에 아직 기본 저장소의 이벤트 알림이 포함되어 있는지 확인합니다. 이 경우 백업 함수를 다시 트리거합니다.
- [Azure Functions 로그](../azure-functions/functions-create-scheduled-function.md#test-the-function)에서 오류 또는 경고가 있는지 확인합니다.
- [Azure Portal](../azure-functions/functions-how-to-use-azure-function-app-settings.md#get-started-in-the-azure-portal)을 사용하여 Azure 함수 앱에 Azure Functions가 읽으려고 하는 애플리케이션 설정에 대해 올바른 값이 포함되었는지 확인합니다.
- 또한 [Azure Application Insights](../azure-functions/functions-monitoring.md?tabs=cmd)를 사용하여 Azure Functions에 대해 모니터링 및 경고를 설정할 수 있습니다. 


## <a name="clean-up-resources"></a>리소스 정리
이 App Configuration 및 이벤트 구독을 계속 작업하려면 이 문서에서 만든 리소스를 삭제하지 마세요. 계속 사용하지 않으려면 다음 명령을 사용하여 이 문서에서 만든 리소스를 삭제하세요.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

키-값의 자동 백업을 설정하는 방법을 확인했으므로, 애플리케이션의 지역 복원력을 늘리는 방법을 자세히 알아봅니다.

- [복원력 및 재해 복구](concept-disaster-recovery.md)

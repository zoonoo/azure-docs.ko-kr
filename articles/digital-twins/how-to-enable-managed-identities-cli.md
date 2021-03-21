---
title: 라우팅 이벤트에 대해 관리 id 사용 (미리 보기)-CLI
titleSuffix: Azure Digital Twins
description: Azure CLI를 사용 하 여 Azure Digital Twins에 대해 시스템에서 할당 한 id를 사용 하 고 이벤트를 전달 하는 데 사용 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c9ce87584373bd87a8f89ecb4ea692b44d3fab4d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202963"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Azure 디지털 쌍 이벤트 라우팅 (미리 보기)에 대 한 관리 id 사용: Azure CLI

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

이 문서에서는 [Azure Digital Twins 인스턴스에 대해 시스템에 할당 된 id](concepts-security.md#managed-identity-for-accessing-other-resources-preview) 를 사용 하도록 설정 하 고 (현재 미리 보기 상태) [이벤트 허브](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   대상 및 [Azure Storage 컨테이너](../storage/blobs/storage-blobs-introduction.md)와 같은 지원 되는 대상으로 이벤트를 전달할 때 id를 사용 하는 방법을 설명 합니다.

이 문서에서는 [**Azure CLI**](/cli/azure/what-is-azure-cli)사용 하는 과정을 안내 합니다.

이 문서에서 설명 하는 단계는 다음과 같습니다. 

1. 시스템이 할당 한 id를 사용 하 여 Azure 디지털 Twins 인스턴스를 만들거나 기존 Azure Digital Twins 인스턴스에서 시스템 할당 id를 사용 하도록 설정 합니다. 
1. Id에 적절 한 역할 또는 역할을 추가 합니다. 예를 들어 끝점이 이벤트 허브가 면 **Azure 이벤트 허브 데이터 발신자** 역할을 id에 할당 하 고, 끝점이 Service Bus 되는 경우 **데이터 발신자 역할을 Azure Service Bus** 합니다.
1. 인증에 시스템 할당 id를 사용할 수 있는 Azure Digital Twins에서 끝점을 만듭니다.

## <a name="enable-system-managed-identities-for-an-instance"></a>인스턴스에 대해 시스템 관리 id 사용 

Azure Digital Twins 인스턴스에서 시스템 할당 id를 사용 하도록 설정 하면 azure는 [Azure Active Directory (AZURE AD)](../active-directory/fundamentals/active-directory-whatis.md)에서 해당 id에 대 한 id를 자동으로 만듭니다. 그런 다음 해당 id를 사용 하 여 이벤트 전달을 위한 Azure Digital Twins 끝점을 인증할 수 있습니다.

**인스턴스의 초기 설정 중** 에 Azure Digital twins 인스턴스에 대해 시스템 관리 id를 사용 하도록 설정 하거나 **이미 존재 하는 인스턴스에서는 나중에 사용 하도록** 설정할 수 있습니다.

이러한 생성 방법 중 하나는 인스턴스에 대해 동일한 구성 옵션과 동일한 최종 결과를 제공 합니다. 이 섹션에서는 두 작업을 수행 하는 방법을 설명 합니다.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>인스턴스를 만드는 동안 시스템 관리 id 추가

이 섹션에서는 현재 만들어지는 Azure Digital Twins 인스턴스에서 시스템 관리 id를 사용 하도록 설정 하는 방법에 대해 알아봅니다. 

`--assign-identity`인스턴스를 만드는 데 사용 되는 명령에 매개 변수를 추가 하 여이 작업을 수행 `az dt create` 합니다. 이 명령에 대 한 자세한 내용은 해당 [참조 설명서](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) 또는 [Azure Digital twins 인스턴스를 설정 하기 위한 일반 지침](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)을 참조 하세요.

시스템 관리 id를 사용 하 여 인스턴스를 만들려면  `--assign-identity` 다음과 같이 매개 변수를 추가 합니다.

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>기존 인스턴스에 시스템 관리 id 추가

이 섹션에서는 이미 존재 하는 Azure Digital Twins 인스턴스에 시스템 관리 id를 추가 합니다.

이는 `az dt create` 명령 및 매개 변수도 사용 하 여 수행 됩니다 `--assign-identity` . 만들 인스턴스의 새 이름을 제공 하는 대신 `--assign-identity` 해당 인스턴스에 대 한 값을 업데이트 하기 위해 이미 존재 하는 인스턴스의 이름을 제공할 수 있습니다.

관리 id를 **사용 하도록 설정** 하는 명령은 시스템 관리 id를 사용 하 여 인스턴스를 만드는 명령과 동일 합니다. 이러한 모든 변경 내용은 instance name 매개 변수의 값입니다.

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

현재 사용 하도록 설정 된 인스턴스에서 관리 되는 id를 **사용 하지 않도록** 설정 하려면 다음과 유사한 명령을 사용 하 여 `--assign-identity` 를로 설정 `false` 합니다.

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Id에 Azure 역할 할당 

Azure Digital Twins 인스턴스에 대해 시스템 할당 id를 만든 후에는 지원 되는 대상에 이벤트를 전달 하기 위해 서로 다른 유형의 [끝점](concepts-route-events.md) 을 사용 하 여 인증할 수 있도록 적절 한 역할을 할당 해야 합니다. 이 섹션에서는 역할 옵션과 시스템 할당 id에 역할을 할당 하는 방법에 대해 설명 합니다.

>[!NOTE]
> 이는 중요 한 단계입니다 .이 단계를 사용 하지 않으면 id가 끝점에 액세스할 수 없고 이벤트가 전달 되지 않습니다.

### <a name="supported-destinations-and-azure-roles"></a>지원 되는 대상 및 Azure 역할 

대상 유형에 따라 id에서 끝점에 액세스 하는 데 필요한 최소 역할은 다음과 같습니다. 더 높은 사용 권한이 있는 역할 (예: 데이터 소유자 역할)도 작동 합니다.

| 대상 | Azure 역할 |
| --- | --- |
| Azure Event Hubs | Azure Event Hubs 데이터 보내는 사람 |
| Azure Service Bus | Azure Service Bus 데이터 보내는 사람 |
| Azure 스토리지 컨테이너 | Storage Blob 데이터 기여자 |

Azure Digital Twins에서 라우팅에 대해 지원 되는 끝점, 경로 및 대상 유형에 대 한 자세한 내용은 [*개념: 이벤트 경로*](concepts-route-events.md)를 참조 하세요.

### <a name="assign-the-role"></a>역할 할당

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

지정 된 역할을 `--scopes` `az dt create` 사용 하 여 하나 이상의 범위에 id를 할당 하기 위해 명령에 매개 변수를 추가할 수 있습니다. 이는 인스턴스를 처음 만들 때 또는 이미 존재 하는 인스턴스의 이름을 전달 하 여 나중에 사용할 수 있습니다.

다음은 시스템 관리 id를 사용 하 여 인스턴스를 만들고 해당 id에 이벤트 허브에서 호출 된 사용자 지정 역할을 할당 하는 예제입니다 `MyCustomRole` .

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

이 명령을 사용 하 여 역할 할당에 대 한 추가 예제는 [ **az dt create** reference 설명서](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create)를 참조 하세요.

또는 [**az role 할당**](/cli/azure/role/assignment) 명령 그룹을 사용 하 여 역할을 만들고 관리할 수도 있습니다. 이는 create 명령을 사용 하 여 역할 할당을 그룹화 하지 않으려는 추가 시나리오를 지 원하는 데 사용할 수 있습니다.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Id 기반 인증을 사용 하 여 끝점 만들기

Azure Digital Twins 인스턴스에 대해 시스템 관리 id를 설정 하 고 적절 한 역할을 할당 한 후에는 인증에 id를 사용할 수 있는 Azure Digital Twins [끝점](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) 을 만들 수 있습니다. 이 옵션은 이벤트 허브 및 Service Bus 형식 끝점에 대해서만 사용할 수 있습니다 (Event Grid에는 지원 되지 않음).

>[!NOTE]
> 키 기반 id로 이미 만들어진 끝점을 편집 하 여 id 기반 인증으로 변경할 수는 없습니다. 끝점을 처음 만들 때 인증 유형을 선택 해야 합니다.

이 작업은 `--auth-type` `az dt endpoint create` 끝점을 만드는 데 사용 되는 명령에 매개 변수를 추가 하 여 수행 됩니다. 이 명령에 대 한 자세한 내용은 해당 [참조 설명서](/cli/azure/ext/azure-iot/dt/endpoint/create) 또는 [Azure 디지털 쌍 끝점 설정에 대 한 일반 지침](how-to-manage-routes-apis-cli.md#create-the-endpoint)을 참조 하세요.

Id 기반 인증을 사용 하는 끝점을 만들려면 `IdentityBased` 매개 변수를 사용 하 여 인증 유형을 지정 합니다  `--auth-type` . 아래 예제에서는 Event Hubs 끝점에 대 한이를 보여 줍니다.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>시스템 관리 id를 사용 하지 않도록 설정 시 고려 사항

Id는이를 사용 하는 끝점과 별도로 관리 되기 때문에 id 또는 해당 역할에 대 한 변경 사항이 Azure Digital Twins 인스턴스의 끝점에 포함 될 수 있는 영향을 고려 하는 것이 중요 합니다. Id를 사용 하지 않도록 설정 하거나 끝점에 대해 필요한 역할을 제거 하는 경우 끝점에 액세스할 수 없게 되 고 이벤트 흐름이 중단 됩니다.

이제 사용 하지 않도록 설정 된 관리 id로 설정 된 끝점을 계속 사용 하려면 끝점을 삭제 하 고 다른 인증 유형을 사용 하 여 [다시 만들어야](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) 합니다. 이 변경 후에는 이벤트가 끝점에 대 한 배달을 다시 시작 하는 데 최대 1 시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD에서 관리 되는 id에 대해 자세히 알아보세요. 
* [*Azure 리소스에 대한 관리 ID*](../active-directory/managed-identities-azure-resources/overview.md)
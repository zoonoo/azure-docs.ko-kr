---
title: 사용자 지정된 컨트롤러 액세스 역할 - Avere vFXT for Azure
description: Avere vFXT 클러스터 컨트롤러용으로 사용자 지정 액세스 역할을 만드는 방법
author: ekpgh
ms.service: avere-vfxt
ms.topic: procedural
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 7ed20ccac879ec8eba1c3fbd91e38d05b08a12d2
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55303004"
---
# <a name="customized-controller-access-role"></a>사용자 지정된 컨트롤러 액세스 역할

Avere vFXT for Azure 클러스터 컨트롤러는 관리 ID 및 RBAC(역할 기반 액세스 제어)를 사용하여 클러스터를 만들고 관리할 수 있습니다. 

클러스터 컨트롤러에는 기본적으로 [기본 제공 소유자 역할](../role-based-access-control/built-in-roles.md#owner)이 할당됩니다. 또한 컨트롤러의 액세스 범위는 해당 리소스 그룹으로 지정되므로 컨트롤러는 클러스터 리소스 그룹 외부의 요소는 수정할 수 없습니다.

이 문서에서는 기본 설정을 사용하는 대신 클러스터 컨트롤러용 고유 액세스 역할을 만드는 방법을 설명합니다. 

## <a name="edit-the-role-prototype"></a>역할 프로토타입 편집

처음에는 <https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt>에서 제공되는 프로토타입 역할을 사용합니다.

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

AssignableScopes 문에 Avere vFXT Azure 배포에 해당하는 구독 ID를 추가합니다. 필요한 경우에는 이름을 사용자 지정하고 정의를 추가하거나 변경합니다. 

권한을 제한하는 경우에는 주의해야 합니다. 컨트롤러에 액세스 권한이 부족하면 클러스터 만들기가 실패할 수 있습니다. 

클러스터를 만드는 데 필요한 클러스터 컨트롤러 권한을 파악하는 과정에서 도움이 필요하면 [지원 티켓을 개설](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)합니다. 

사용자 지정 역할 정의를 .json 파일로 저장합니다. 

## <a name="define-the-role"></a>역할 정의 

다음 단계에 따라 구독에 사용자 지정 역할 정의를 추가합니다. 

1. Azure Portal에서 Azure Cloud Shell을 열거나 [https://shell.azure.com](https://shell.azure.com)으로 이동합니다.

1. Azure CLI 명령을 사용하여 vFXT 구독으로 전환합니다.

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. 역할을 만듭니다.

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   이 예제의 ```/avere-contributor-custom.json``` 대신 실제 파일 이름 및 경로를 사용하세요. 

역할 정의 명령의 출력을 저장합니다. 출력에는 클러스터 생성 템플릿에 제공해야 하는 역할 ID가 포함되어 있습니다. 

## <a name="find-the-role-id"></a>역할 ID 찾기

컨트롤러에 사용자 지정 역할을 할당하려면 Avere vFXT 배포 템플릿에 역할의 GUID(Globally Unique Identifier)를 포함해야 합니다. 

역할 GUID는 다음 형식의 32자로 된 문자열입니다. 8e3af657-a8ff-443c-a75c-2fe8c4bcb635

역할 GUID를 조회하려면 ```--name``` 매개 변수에 사용자 역할 이름을 포함하여 아래 명령을 사용합니다.

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
Avere vFXT for Azure를 배포할 때 **Avere 클러스터 역할 만들기 ID** 필드에 이 문자열을 입력합니다.

## <a name="next-steps"></a>다음 단계

[vFXT 클러스터 배포](avere-vfxt-deploy.md)에서 Avere vFXT for Azure를 배포하는 방법을 확인합니다.

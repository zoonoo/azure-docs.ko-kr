---
title: Azure Arc 활성화 된 Kubernetes에 대 한 온 보 딩 서비스 주체 만들기
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Azure Arc 활성화 된 온 보 딩 서비스 주체 만들기 '
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: bda088bdae5c866493718db94c9a2da89cada8c9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650349"
---
# <a name="create-an-onboarding-service-principal-for-azure-arc-enabled-kubernetes"></a>Azure Arc 활성화 된 Kubernetes에 대 한 온 보 딩 서비스 주체 만들기

## <a name="overview"></a>개요

제한 된 권한 역할 할당이 있는 서비스 주체를 사용 하 여 Kubernetes 클러스터를 Azure Arc에 연결할 수 있습니다. 이 기능은 Azure Pipelines 및 GitHub 작업과 같은 CI/CD (지속적인 통합 및 지속적인 배포) 파이프라인에서 유용 합니다.

Kubernetes 클러스터를 Azure Arc에 연결 하는 데 서비스 주체를 사용 하는 방법을 알아보려면 다음 단계를 수행 합니다.

## <a name="create-a-new-service-principal"></a>새 서비스 주체 만들기

Azure Active Directory 테 넌 트에 고유한 정보 이름으로 새 서비스 주체를 만듭니다.

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**출력:**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>권한 할당

새로 만든 서비스 주체에 "Kubernetes Cluster-Azure Arc 온 보 딩" 역할을 할당 합니다. 제한 된 권한을 가진이 기본 제공 Azure 역할은 보안 주체가 Azure에 클러스터를 등록 하도록 허용 합니다. 이 할당 된 역할의 보안 주체는 구독 내의 다른 클러스터 나 리소스를 업데이트, 삭제 또는 수정할 수 없습니다.

제한된 기능이 부여되어 있으므로 고객은 이 보안 주체를 쉽게 다시 사용하여 여러 클러스터를 등록할 수 있습니다.

역할을 할당할 때 적절 한 인수를 전달 하 여 권한을 추가로 제한할 수 있습니다 `--scope` . 이를 통해 관리자는 구독 또는 리소스 그룹 범위에 대 한 클러스터 등록을 제한할 수 있습니다. 다음 시나리오는 다양한 `--scope` 매개 변수에서 지원됩니다.

| 리소스  | `scope` 인수| 영향 |
| ------------- | ------------- | ------------- |
| Subscription | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | 서비스 사용자는 해당 구독의 모든 리소스 그룹에 클러스터를 등록할 수 있습니다. |
| 리소스 그룹 | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | 서비스 사용자는 리소스 그룹의 클러스터 __만__ 등록할 수 있습니다 `myGroup` . |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**출력:**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Azure CLI에서 서비스 주체 사용

다음 명령을 사용 하 여 새로 만든 서비스 주체를 참조 합니다.

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>다음 단계

* [Azure Policy를 사용하여 클러스터 구성 관리](./use-azure-policy.md)

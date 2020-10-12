---
title: Azure Arc 지원 온보딩 서비스 주체 만들기(미리 보기)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Azure Arc 지원 온보딩 서비스 주체 만들기 '
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: 02689dba32c8cc91e4a4a4de4dee98bc990b4dd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050076"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Azure Arc 지원 온보딩 서비스 주체 만들기(미리 보기)

## <a name="overview"></a>개요

Azure Arc에 Kubernetes 클러스터를 온보딩하는 데 제한된 권한으로 역할 할당을 가진 서비스 주체를 사용할 수 있습니다. 이는 Azure Pipelines 및 GitHub Actions와 같은 CI/CD(연속 통합 및 연속 배포) 파이프라인에서 유용합니다.

다음 단계는 Azure Arc에 Kubernetes 클러스터를 온보딩하는 데 서비스 주체를 사용하는 연습을 제공합니다.

## <a name="create-a-new-service-principal"></a>새 서비스 주체를 만들기

정보 이름을 사용하여 새 서비스 주체를 만듭니다. 이 이름은 Azure Active Directory 테넌트에서 고유해야 합니다.

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

새 서비스 주체를 만든 후에는 새로 만든 주 서버에 "Kubernetes Cluster-Azure Arc 온 보 딩" 역할을 할당 합니다. 이 역할은 보안 주체가 Azure에 클러스터를 등록할 수만 있게 허용하는 제한된 권한을 가진 기본 제공 Azure 역할입니다. 보안 주체는 구독 내의 다른 클러스터 또는 리소스를 업데이트, 삭제, 수정할 수 없습니다.

제한된 기능이 부여되어 있으므로 고객은 이 보안 주체를 쉽게 다시 사용하여 여러 클러스터를 등록할 수 있습니다.

역할을 할당할 때 적절한 `--scope` 인수를 전달하여 권한을 추가로 제한할 수 있습니다. 이렇게 하면 고객이 클러스터 등록을 제한할 수 있습니다. 다음 시나리오는 다양한 `--scope` 매개 변수에서 지원됩니다.

| 리소스  | `scope` 인수| 영향 |
| ------------- | ------------- | ------------- |
| Subscription | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | 서비스 주체는 지정된 구독의 기존 리소스 그룹에 있는 모든 클러스터를 등록할 수 있습니다. |
| 리소스 그룹 | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | 서비스 주체는 리소스 그룹 `myGroup`의 클러스터__만__ 등록할 수 있습니다. |

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

## <a name="use-service-principal-with-the-azure-cli"></a>Azure CLI를 통해 서비스 주체 사용

새로 만든 서비스 주체를 참조합니다.

```console
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>다음 단계

* [Azure Policy를 사용하여 클러스터 구성 관리](./use-azure-policy.md)

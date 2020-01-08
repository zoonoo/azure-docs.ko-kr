---
title: 작업 영역에서 역할 관리
titleSuffix: Azure Machine Learning
description: RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure Machine Learning 작업 영역에 액세스 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: d43e04e0ef347ec46a8aad12aac380f68106fc2f
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541609"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역에 대 한 액세스 관리
[!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning 작업 영역에 대 한 액세스를 관리 하는 방법에 대해 알아봅니다. [RBAC (역할 기반 액세스 제어)](/azure/role-based-access-control/overview) 는 Azure 리소스에 대 한 액세스를 관리 하는 데 사용 됩니다. Azure Active Directory 사용자에 게 리소스에 대 한 액세스 권한을 부여 하는 특정 역할이 할당 됩니다. Azure는 기본 제공 역할 및 사용자 지정 역할을 만드는 기능을 모두 제공 합니다.

## <a name="default-roles"></a>기본 역할

Azure Machine Learning 작업 영역은 Azure 리소스입니다. 다른 Azure 리소스와 마찬가지로 새 Azure Machine Learning 작업 영역을 만들 때 세 가지 기본 역할이 제공 됩니다. 사용자를 작업 영역에 추가 하 고 이러한 기본 제공 역할 중 하나에 할당할 수 있습니다.

| 역할 | 액세스 수준 |
| --- | --- |
| **판독기** | 작업 영역의 읽기 전용 작업입니다. 독자는 작업 영역에서 자산을 나열 하 고 볼 수 있지만 이러한 자산을 만들거나 업데이트할 수 없습니다. |
| **기여자** | 작업 영역에서 자산을 보거나, 만들거나, 편집 하거나, 삭제 합니다 (해당 하는 경우). 예를 들어 참가자는 실험을 만들고, 계산 클러스터를 만들고 연결 하 고, 실행을 제출 하 고, 웹 서비스를 배포할 수 있습니다. |
| **소유자** | 작업 영역에 대 한 모든 액세스 권한 (해당 하는 경우)을 확인, 생성, 편집 또는 삭제 (해당 하는 경우) 하는 기능을 포함 하 여 작업 영역에서. 또한 역할 할당을 변경할 수 있습니다. |

> [!IMPORTANT]
> Azure에서 역할 액세스의 범위를 여러 수준으로 지정할 수 있습니다. 예를 들어 작업 영역에 대 한 소유자 액세스 권한이 있는 사용자에 게는 작업 영역을 포함 하는 리소스 그룹에 대 한 소유자 액세스 권한이 없을 수 있습니다. 자세한 내용은 [RBAC 작동 방식](/azure/role-based-access-control/overview#how-rbac-works)을 참조 하세요.

특정 기본 제공 역할에 대 한 자세한 내용은 [Azure에 대 한 기본 제공 역할](/azure/role-based-access-control/built-in-roles)을 참조 하세요.

## <a name="manage-workspace-access"></a>작업 영역 액세스 관리

작업 영역의 소유자 인 경우 작업 영역에 대 한 역할을 추가 하 고 제거할 수 있습니다. 또한 사용자에 게 역할을 할당할 수 있습니다. 다음 링크를 사용 하 여 액세스를 관리 하는 방법을 알아봅니다.
- [Azure Portal UI](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure 리소스 관리자 템플릿](/azure/role-based-access-control/role-assignments-template)

[AZURE MACHINE LEARNING cli](reference-azure-machine-learning-cli.md)를 설치한 경우 cli 명령을 사용 하 여 사용자에 게 역할을 할당할 수도 있습니다.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` 필드는 작업 영역 부모 구독이 있는 Azure Active Directory 인스턴스에서 기존 사용자의 이메일 주소입니다. 다음은이 명령을 사용 하는 방법의 예입니다.

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>사용자 지정 역할 만들기

기본 제공 역할이 충분 하지 않은 경우 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할에는 해당 작업 영역에 대 한 읽기, 쓰기, 삭제 및 계산 리소스 권한이 있을 수 있습니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준 또는 특정 구독 수준에서 역할을 사용 하도록 설정할 수 있습니다.

> [!NOTE]
> 해당 리소스 내에서 사용자 지정 역할을 만들려면 해당 수준의 리소스 소유자 여야 합니다.

사용자 지정 역할을 만들려면 먼저 역할에 대 한 사용 권한 및 범위를 지정 하는 역할 정의 JSON 파일을 생성 합니다. 다음 예제에서는 특정 작업 영역 수준에서 범위가 "Data 과학자" 인 사용자 지정 역할을 정의 합니다.

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

`AssignableScopes` 필드를 변경 하 여 구독 수준, 리소스 그룹 수준 또는 특정 작업 영역 수준에서이 사용자 지정 역할의 범위를 설정할 수 있습니다.

이 사용자 지정 역할은 다음 작업을 제외 하 고 작업 영역에서 모든 작업을 수행할 수 있습니다.

- 계산 리소스를 만들거나 업데이트할 수 없습니다.
- 계산 리소스를 삭제할 수 없습니다.
- 역할 할당을 추가, 삭제 또는 변경할 수 없습니다.
- 작업 영역을 삭제할 수 없습니다.

이 사용자 지정 역할을 배포 하려면 다음 Azure CLI 명령을 사용 합니다.

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

배포 후에이 역할은 지정 된 작업 영역에서 사용할 수 있게 됩니다. 이제 Azure Portal에서이 역할을 추가 하 고 할당할 수 있습니다. 또는 `az ml workspace share` CLI 명령을 사용 하 여 사용자에 게이 역할을 할당할 수 있습니다.

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

사용자 지정 역할에 대 한 자세한 내용은 [Azure 리소스에 대 한 사용자 지정 역할](/azure/role-based-access-control/custom-roles)을 참조 하세요.

사용자 지정 역할에서 사용할 수 있는 작업 (작업)에 대 한 자세한 내용은 [리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [엔터프라이즈 보안 개요](concept-enterprise-security.md)
- [가상 네트워크 내에서 실험 및 유추/점수를 안전 하 게 실행](how-to-enable-virtual-network.md)
- [자습서: 모델 교육](tutorial-train-models-with-aml.md)
- [리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
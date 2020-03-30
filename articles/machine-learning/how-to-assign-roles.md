---
title: 작업 영역에서 역할 관리
titleSuffix: Azure Machine Learning
description: 역할 기반 액세스 제어(RBAC)를 사용하여 Azure 기계 학습 작업 영역에 액세스하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270096"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure 기계 학습 작업 영역에 대한 액세스 관리
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습 작업 영역에 대한 액세스를 관리하는 방법을 배웁니다. [RBAC(역할 기반 액세스 제어)는](/azure/role-based-access-control/overview) Azure 리소스에 대한 액세스를 관리하는 데 사용됩니다. Azure Active Directory의 사용자에게는 리소스에 대한 액세스 권한을 부여하는 특정 역할이 할당됩니다. Azure는 기본 제공 역할과 사용자 지정 역할을 만드는 기능을 모두 제공합니다.

## <a name="default-roles"></a>기본 역할

Azure 기계 학습 작업 영역은 Azure 리소스입니다. 다른 Azure 리소스와 마찬가지로 새 Azure 기계 학습 작업 영역이 만들어지면 세 가지 기본 역할이 제공됩니다. 사용자를 작업 영역에 추가하고 이러한 기본 제공 역할 중 하나에 할당할 수 있습니다.

| 역할 | 액세스 수준 |
| --- | --- |
| **읽기 권한자** | 작업 영역에서 읽기 전용 작업입니다. 독자는 작업 영역에서 에셋을 나열하고 볼 수 있지만 이러한 자산을 만들거나 업데이트할 수는 없습니다. |
| **참가자** | 작업 공간에서 에셋을 보고, 만들거나 편집하거나(해당하는 경우) 삭제합니다. 예를 들어 참여자는 실험을 만들고, 계산 클러스터를 만들거나 연결하고, 실행을 제출하고, 웹 서비스를 배포할 수 있습니다. |
| **소유자** | 작업 영역에서 자산을 보거나, 생성, 편집 또는 삭제하는 기능을 포함하여 작업 영역에 대한 전체 액세스 권한입니다. 또한 역할 할당을 변경할 수 있습니다. |

> [!IMPORTANT]
> 역할 액세스는 Azure의 여러 수준으로 범위를 확대할 수 있습니다. 예를 들어 작업 영역에 대한 소유자 액세스 권한이 있는 사용자가 작업 영역을 포함하는 리소스 그룹에 대한 소유자 액세스 권한이 없을 수 있습니다. 자세한 내용은 [RBAC 작동 방식을](/azure/role-based-access-control/overview#how-rbac-works)참조하십시오.

특정 기본 제공 역할에 대한 자세한 내용은 Azure 에 [대한 기본 제공 역할을](/azure/role-based-access-control/built-in-roles)참조하십시오.

## <a name="manage-workspace-access"></a>작업 영역 액세스 관리

작업 영역의 소유자인 경우 작업 영역에 대한 역할을 추가하고 제거할 수 있습니다. 사용자에게 역할을 할당할 수도 있습니다. 다음 링크를 사용하여 액세스를 관리하는 방법을 알아보십시오.
- [Azure 포털 UI](/azure/role-based-access-control/role-assignments-portal)
- [Powershell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [나머지 API](/azure/role-based-access-control/role-assignments-rest)
- [Azure 리소스 관리자 템플릿](/azure/role-based-access-control/role-assignments-template)

[Azure 기계 학습 CLI를](reference-azure-machine-learning-cli.md)설치한 경우 CLI 명령을 사용하여 사용자에게 역할을 할당할 수도 있습니다.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

필드는 `user` 작업 영역 부모 구독이 있는 Azure Active Directory의 인스턴스에서 기존 사용자의 전자 메일 주소입니다. 다음은 이 명령을 사용하는 방법의 예입니다.

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>사용자 지정 역할 만들기

기본 제공 역할이 충분하지 않은 경우 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할에는 해당 작업 영역에서 리소스 권한을 읽고, 쓰고, 삭제하고, 계산할 수 있습니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준 또는 특정 구독 수준에서 역할을 사용할 수 있도록 할 수 있습니다.

> [!NOTE]
> 해당 리소스 내에서 사용자 지정 역할을 만들려면 해당 수준에서 리소스의 소유자여야 합니다.

사용자 지정 역할을 만들려면 먼저 역할에 대한 권한과 범위를 지정하는 역할 정의 JSON 파일을 생성합니다. 다음 예제에서는 특정 작업 영역 수준에서 범위가 지정된 "데이터 과학자"라는 사용자 지정 역할을 정의합니다.

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

`AssignableScopes` 필드를 변경하여 구독 수준, 리소스 그룹 수준 또는 특정 작업 영역 수준에서 이 사용자 지정 역할의 범위를 설정할 수 있습니다.

이 사용자 지정 역할은 다음 작업을 제외한 작업 영역에서 모든 작업을 수행할 수 있습니다.

- 계산 리소스를 만들거나 업데이트할 수 없습니다.
- 계산 리소스를 삭제할 수 없습니다.
- 역할 할당을 추가, 삭제 또는 변경할 수 없습니다.
- 작업 영역을 삭제할 수 없습니다.

이 사용자 지정 역할을 배포하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

배포 후 지정된 작업 영역에서 이 역할을 사용할 수 있게 됩니다. 이제 Azure Portal에서 이 역할을 추가하고 할당할 수 있습니다. 또는 `az ml workspace share` CLI 명령을 사용하여 사용자에게 이 역할을 할당할 수 있습니다.

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

사용자 지정 역할에 대한 자세한 내용은 [Azure 리소스에 대한 사용자 지정 역할을](/azure/role-based-access-control/custom-roles)참조합니다.

사용자 지정 역할에서 사용할 수 있는 작업(작업)에 대한 자세한 내용은 [리소스 공급자 작업을](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)참조하십시오.


## <a name="frequently-asked-questions"></a>질문과 대답


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>17. Azure 기계 학습 서비스에서 다양한 작업을 수행하는 데 필요한 권한은 무엇입니까?

다음 표는 Azure 기계 학습 활동및 최소 범위에서 수행하는 데 필요한 사용 권한에 대한 요약입니다. 예를 들어 작업 영역 범위(열 4)로 활동을 수행할 수 있는 경우 해당 사용 권한이 있는 모든 상위 범위도 자동으로 작동합니다. 이 테이블의 모든 경로는 `Microsoft.MachineLearningServices/`에 대한 상대 **경로입니다.**

| 활동 | 구독 수준 범위 | 리소스 그룹 수준 범위 | 작업 영역 수준 범위 |
|---|---|---|---|
| 새 작업 영역 만들기 | 필요하지 않음 | 소유자 또는 기여자 | 해당/A(생성 후 소유자가 되거나 더 높은 범위 역할을 상속) |
| 새 계산 클러스터 만들기 | 필요하지 않음 | 필요하지 않음 | 소유자, 기여자 또는 사용자 지정 역할 허용:`workspaces/computes/write` |
| 새 노트북 VM 만들기 | 필요하지 않음 | 소유자 또는 기여자 | 가능하지 않음 |
| 새 계산 인스턴스 만들기 | 필요하지 않음 | 필요하지 않음 | 소유자, 기여자 또는 사용자 지정 역할 허용:`workspaces/computes/write` |
| 실행 제출, 데이터 액세스, 모델 배포 또는 파이프라인 게시와 같은 데이터 평면 활동 | 필요하지 않음 | 필요하지 않음 | 소유자, 기여자 또는 사용자 지정 역할 허용:`workspaces/*/write` <br/> 또한 MSI가 저장소 계정의 데이터에 액세스할 수 있도록 작업 영역에 등록된 데이터스토어가 필요합니다. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>17. 구독의 모든 사용자 지정 역할을 나열하려면 어떻게 해야 합니까?

Azure CLI에서 다음 명령을 실행합니다.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>17. 구독에서 역할에 대한 역할 정의를 찾으려면 어떻게 해야 합니까?

Azure CLI에서 다음 명령을 실행합니다. 위의 `<role-name>` 명령에서 반환되는 것과 동일한 형식이어야 합니다.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>17. 역할 정의를 업데이트하려면 어떻게 해야 합니까?

Azure CLI에서 다음 명령을 실행합니다.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

새 역할 정의의 전체 범위에 대한 사용 권한이 있어야 합니다. 예를 들어 이 새 역할에 세 개의 구독에 대한 범위가 있는 경우 세 구독 모두에 대한 사용 권한이 있어야 합니다. 

> [!NOTE]
> 역할 업데이트는 해당 범위의 모든 역할 할당에 적용하려면 15분에서 1시간 정도 걸릴 수 있습니다.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>17. 작업 영역 에디션을 업데이트하지 못하게 하는 역할을 정의할 수 있습니까? 

예. 작업 영역 에디션을 업데이트하지 않는 역할을 정의할 수 있습니다. 작업 영역 업데이트는 작업 영역 개체에 대한 PATCH 호출이므로 JSON 정의의 `"NotActions"` 배열에 다음 작업을 배치하여 이 작업을 수행합니다. 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>17. 작업 영역에서 할당량 작업을 수행하는 데 필요한 권한은 무엇입니까? 

작업 영역에서 할당량 관련 작업을 수행하려면 구독 수준 권한이 필요합니다. 즉, 관리되는 계산 리소스에 대한 구독 수준 할당량 또는 작업 영역 수준 할당량을 설정하는 것은 구독 범위에서 쓰기 권한이 있는 경우에만 발생할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

- [엔터프라이즈 보안 개요](concept-enterprise-security.md)
- [가상 네트워크 내에서 실험 및 추론/점수 를 안전하게 실행](how-to-enable-virtual-network.md)
- [자습서: 모델 교육](tutorial-train-models-with-aml.md)
- [리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)

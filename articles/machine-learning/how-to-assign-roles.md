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
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270096"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역에 대 한 액세스 관리
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning 작업 영역에 대 한 액세스를 관리 하는 방법에 대해 알아봅니다. [RBAC (역할 기반 액세스 제어)](/azure/role-based-access-control/overview) 는 Azure 리소스에 대 한 액세스를 관리 하는 데 사용 됩니다. Azure Active Directory 사용자에 게 리소스에 대 한 액세스 권한을 부여 하는 특정 역할이 할당 됩니다. Azure는 기본 제공 역할 및 사용자 지정 역할을 만드는 기능을 모두 제공 합니다.

## <a name="default-roles"></a>기본 역할

Azure Machine Learning 작업 영역은 Azure 리소스입니다. 다른 Azure 리소스와 마찬가지로 새 Azure Machine Learning 작업 영역을 만들 때 세 가지 기본 역할이 제공됩니다. 사용자를 작업 영역에 추가 하 고 이러한 기본 제공 역할 중 하나에 할당할 수 있습니다.

| 역할 | 액세스 수준 |
| --- | --- |
| **판독기** | 작업 영역의 읽기 전용 작업입니다. 읽기 권한자는 작업 영역에서 자산을 나열하고 볼 수 있지만 이러한 자산을 만들거나 업데이트할 수 없습니다. |
| **기여자** | 작업 영역에서 해당하는 자산을 보거나, 만들거나, 편집하거나, 삭제합니다. 예를 들어 기여자는 실험을 만들고, 컴퓨팅 클러스터를 만들거나 연결하고, 실행을 제출하고, 웹 서비스를 배포할 수 있습니다. |
| **소유자** | 작업 영역에 대한 모든 액세스 권한(작업 영역에서 해당하는 자산을 보거나, 만들거나, 편집하거나, 삭제하는 기능 포함)입니다. 또한 역할 할당을 변경할 수 있습니다. |

> [!IMPORTANT]
> Azure에서 역할 액세스의 범위를 여러 수준으로 지정할 수 있습니다. 예를 들어 작업 영역에 대 한 소유자 액세스 권한이 있는 사용자에 게는 작업 영역을 포함 하는 리소스 그룹에 대 한 소유자 액세스 권한이 없을 수 있습니다. 자세한 내용은 [RBAC 작동 방식](/azure/role-based-access-control/overview#how-rbac-works)을 참조 하세요.

특정 기본 제공 역할에 대 한 자세한 내용은 [Azure에 대 한 기본 제공 역할](/azure/role-based-access-control/built-in-roles)을 참조 하세요.

## <a name="manage-workspace-access"></a>작업 영역 액세스 관리

작업 영역의 소유자인 경우 작업 영역에 대한 역할을 추가하고 제거할 수 있습니다. 또한 사용자에게 역할을 할당할 수 있습니다. 다음 링크를 사용하여 액세스를 관리하는 방법을 알아봅니다.
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

기본 제공 역할이 충분하지 않은 경우 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할에는 해당 작업 영역에 대 한 읽기, 쓰기, 삭제 및 계산 리소스 권한이 있을 수 있습니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준 또는 특정 구독 수준에서 역할을 사용 하도록 설정할 수 있습니다.

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


## <a name="frequently-asked-questions"></a>질문과 대답


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>17. Azure Machine Learning 서비스에서 다양 한 작업을 수행 하는 데 필요한 권한은 무엇 인가요?

다음 표에는 Azure Machine Learning 작업에 대 한 요약과 최소 범위에서 작업을 수행 하는 데 필요한 사용 권한이 정리 되어 있습니다. 예를 들어 작업 영역 범위 (열 4)를 사용 하 여 작업을 수행할 수 있는 경우 해당 사용 권한을 가진 상위 범위가 모두 자동으로 적용 됩니다. 이 테이블의 모든 경로는 `Microsoft.MachineLearningServices/`에 대 한 **상대 경로** 입니다.

| 작업 | 구독 수준 범위 | 리소스 그룹 수준 범위 | 작업 영역 수준 범위 |
|---|---|---|---|
| 새 작업 영역 만들기 | 필요하지 않음 | 소유자 또는 참가자 | 해당 없음 (소유자가 되거나 생성 후 상위 범위 역할 상속) |
| 새 계산 클러스터 만들기 | 필요하지 않음 | 필요하지 않음 | 다음을 허용 하는 소유자, 참가자 또는 사용자 지정 역할: `workspaces/computes/write` |
| 새 노트북 VM 만들기 | 필요하지 않음 | 소유자 또는 참가자 | 가능 하지 않음 |
| 새 계산 인스턴스 만들기 | 필요하지 않음 | 필요하지 않음 | 다음을 허용 하는 소유자, 참가자 또는 사용자 지정 역할: `workspaces/computes/write` |
| 실행 제출, 데이터 액세스, 모델 배포 또는 파이프라인 게시와 같은 데이터 평면 활동 | 필요하지 않음 | 필요하지 않음 | 다음을 허용 하는 소유자, 참가자 또는 사용자 지정 역할: `workspaces/*/write` <br/> 또한 MSI에서 저장소 계정의 데이터에 액세스할 수 있도록 작업 영역에 등록 된 데이터 저장소가 필요 합니다. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>17. 내 구독의 모든 사용자 지정 역할을 나열 어떻게 할까요??

Azure CLI에서 다음 명령을 실행 합니다.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>17. 내 구독에서 역할에 대 한 역할 정의를 찾을 어떻게 할까요? 있나요?

Azure CLI에서 다음 명령을 실행 합니다. `<role-name>`은 위의 명령에서 반환 된 것과 동일한 형식 이어야 합니다.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>17. 역할 정의를 업데이트 어떻게 할까요??

Azure CLI에서 다음 명령을 실행 합니다.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

새 역할 정의의 전체 범위에 대 한 사용 권한이 있어야 합니다. 예를 들어이 새 역할에 세 개의 구독에 대 한 범위가 있는 경우 세 구독 모두에 대 한 사용 권한이 있어야 합니다. 

> [!NOTE]
> 역할 업데이트는 해당 범위의 모든 역할 할당에 적용 되는 데 15 분에서 1 시간 정도 걸릴 수 있습니다.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>17. 작업 영역 버전 업데이트를 방지 하는 역할을 정의할 수 있나요? 

예, 작업 영역 버전 업데이트를 방지 하는 역할을 정의할 수 있습니다. 작업 영역 업데이트는 작업 영역 개체에 대 한 패치 호출 이므로 JSON 정의의 `"NotActions"` 배열에 다음 작업을 추가 하 여이 작업을 수행 합니다. 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>17. 작업 영역에서 할당량 작업을 수행 하는 데 필요한 권한은 무엇 인가요? 

작업 영역에서 모든 할당량 관련 작업을 수행 하려면 구독 수준 권한이 있어야 합니다. 즉, 구독 범위에서 쓰기 권한이 있는 경우에만 관리 되는 계산 리소스에 대 한 구독 수준 할당량 또는 작업 영역 수준 할당량 설정이 발생할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

- [엔터프라이즈 보안 개요](concept-enterprise-security.md)
- [가상 네트워크 내에서 실험 및 유추/점수를 안전 하 게 실행](how-to-enable-virtual-network.md)
- [자습서: 모델 교육](tutorial-train-models-with-aml.md)
- [리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)

---
title: 작업 영역에서 역할 관리
titleSuffix: Azure Machine Learning
description: RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure Machine Learning 작업 영역에 액세스 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 07/24/2020
ms.custom: how-to, seodec18
ms.openlocfilehash: 5b454c324d475eb4f692e1715cb2ea45105f78e1
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056927"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역에 대 한 액세스 관리
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning 작업 영역에 대 한 액세스를 관리 하는 방법에 대해 알아봅니다. Azure [RBAC (역할 기반 액세스 제어)](/azure/role-based-access-control/overview) 는 azure 리소스에 대 한 액세스를 관리 하는 데 사용 됩니다. Azure Active Directory 사용자에 게 리소스에 대 한 액세스 권한을 부여 하는 특정 역할이 할당 됩니다. Azure는 기본 제공 역할 및 사용자 지정 역할을 만드는 기능을 모두 제공 합니다.

## <a name="default-roles"></a>기본 역할

Azure Machine Learning 작업 영역은 Azure 리소스입니다. 다른 Azure 리소스와 마찬가지로 새 Azure Machine Learning 작업 영역을 만들 때 세 가지 기본 역할이 제공됩니다. 사용자를 작업 영역에 추가 하 고 이러한 기본 제공 역할 중 하나에 할당할 수 있습니다.

| 역할 | 액세스 수준 |
| --- | --- |
| **판독기** | 작업 영역의 읽기 전용 작업입니다. 독자는 작업 영역에서 [데이터 저장소](how-to-access-data.md) 자격 증명을 포함 하 여 자산을 나열 하 고 볼 수 있습니다. 판독기는 이러한 자산을 만들거나 업데이트할 수 없습니다. |
| **기여자** | 작업 영역에서 자산을 보거나, 만들거나, 편집 하거나, 삭제 합니다 (해당 하는 경우). 예를 들어 기여자는 실험을 만들고, 컴퓨팅 클러스터를 만들거나 연결하고, 실행을 제출하고, 웹 서비스를 배포할 수 있습니다. |
| **소유자** | 작업 영역에 대 한 모든 액세스 권한 (해당 하는 경우)을 확인, 생성, 편집 또는 삭제 (해당 하는 경우) 하는 기능을 포함 하 여 작업 영역에서. 또한 역할 할당을 변경할 수 있습니다. |
| **사용자 지정 역할** | 작업 영역 내에서 특정 컨트롤이 나 데이터 평면 작업에 대 한 액세스를 사용자 지정할 수 있습니다. 예를 들어 실행을 제출 하 고, 계산을 생성 하 고, 모델을 배포 하거나, 데이터 집합을 등록 합니다. |

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

[AZURE MACHINE LEARNING cli](reference-azure-machine-learning-cli.md)를 설치한 경우 cli 명령을 사용 하 여 사용자에 게 역할을 할당할 수 있습니다.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user`필드는 작업 영역 부모 구독이 있는 Azure Active Directory 인스턴스에 있는 기존 사용자의 전자 메일 주소입니다. 다음은이 명령을 사용 하는 방법의 예입니다.

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> "az ml workspace share" 명령은 Azure Active Directory B2B에서 페더레이션된 계정에 대해 작동 하지 않습니다. 명령 대신 Azure UI 포털을 사용 하세요.


## <a name="azure-machine-learning-operations"></a>Azure Machine Learning 작업

많은 작업 및 작업에 대 한 기본 제공 작업을 Azure Machine Learning 합니다. 전체 목록은 [Azure 리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)을 참조 하세요.

## <a name="create-custom-role"></a>사용자 지정 역할 만들기

기본 제공 역할이 충분하지 않은 경우 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할에는 해당 작업 영역에 대 한 읽기, 쓰기, 삭제 및 계산 리소스 권한이 있을 수 있습니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준 또는 특정 구독 수준에서 역할을 사용 하도록 설정할 수 있습니다.

> [!NOTE]
> 해당 리소스 내에서 사용자 지정 역할을 만들려면 해당 수준의 리소스 소유자 여야 합니다.

사용자 지정 역할을 만들려면 먼저 역할에 대 한 사용 권한 및 범위를 지정 하는 역할 정의 JSON 파일을 생성 합니다. 다음 예제에서는 특정 작업 영역 수준에서 범위가 "Data 과학자 Custom" 인 사용자 지정 역할을 정의 합니다.

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> 필드를 변경 `AssignableScopes` 하 여 구독 수준, 리소스 그룹 수준 또는 특정 작업 영역 수준에서이 사용자 지정 역할의 범위를 설정할 수 있습니다.
> 위의 사용자 지정 역할은 예를 들어 [Azure Machine Learning 서비스에 대 한](#customroles)몇 가지 제안 된 사용자 지정 역할을 참조 하세요.

이 사용자 지정 역할은 다음 작업을 제외 하 고 작업 영역에서 모든 작업을 수행할 수 있습니다.

- 계산 리소스를 만들거나 업데이트할 수 없습니다.
- 계산 리소스를 삭제할 수 없습니다.
- 역할 할당을 추가, 삭제 또는 변경할 수 없습니다.
- 작업 영역을 삭제할 수 없습니다.

이 사용자 지정 역할을 배포 하려면 다음 Azure CLI 명령을 사용 합니다.

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

배포 후에이 역할은 지정 된 작업 영역에서 사용할 수 있게 됩니다. 이제 Azure Portal에서이 역할을 추가 하 고 할당할 수 있습니다. 또는 CLI 명령을 사용 하 여 사용자에 게이 역할을 할당할 수 있습니다 `az ml workspace share` .

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

사용자 지정 역할에 대 한 자세한 내용은 [Azure 사용자 지정 역할](/azure/role-based-access-control/custom-roles)을 참조 하세요. 사용자 지정 역할과 함께 사용할 수 있는 작업 (작업 및 작업 아님)에 대 한 자세한 내용은 [리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)을 참조 하세요.

## <a name="frequently-asked-questions"></a>질문과 대답


### <a name="q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service"></a>17. Azure Machine Learning 서비스에서 몇 가지 일반적인 시나리오를 수행 하는 데 필요한 권한은 무엇 인가요?

다음 표에는 Azure Machine Learning 작업에 대 한 요약과 최소 범위에서 작업을 수행 하는 데 필요한 사용 권한이 정리 되어 있습니다. 예를 들어 작업 영역 범위 (열 4)를 사용 하 여 작업을 수행할 수 있는 경우 해당 사용 권한을 가진 상위 범위가 모두 자동으로 적용 됩니다.

> [!IMPORTANT]
> 이 테이블에서로 시작 하는 모든 경로 `/` 는 다음에 대 한 **상대 경로** 입니다 `Microsoft.MachineLearningServices/` .

| 활동 | 구독 수준 범위 | 리소스 그룹 수준 범위 | 작업 영역 수준 범위 |
| ----- | ----- | ----- | ----- |
| 새 작업 영역 만들기 | 필요 없음 | 소유자 또는 참가자 | 해당 없음 (소유자가 되거나 생성 후 상위 범위 역할 상속) |
| 작업 영역 버전 업데이트 | 필요 없음 | 필요 없음 | 다음을 허용 하는 소유자, 참가자 또는 사용자 지정 역할:`/workspaces/write` |
| 구독 수준 Amlcompute 할당량을 요청 하거나 작업 영역 수준 할당량을 설정 합니다. | 소유자, 참가자 또는 사용자 지정 역할 </br>있어`/locations/updateQuotas/action`</br> 구독 범위 | 권한 없음 | 권한 없음 |
| 새 계산 클러스터 만들기 | 필요 없음 | 필요 없음 | 다음을 허용 하는 소유자, 참가자 또는 사용자 지정 역할:`/workspaces/computes/write` |
| 새 계산 인스턴스 만들기 | 필요 없음 | 필요 없음 | 다음을 허용 하는 소유자, 참가자 또는 사용자 지정 역할:`/workspaces/computes/write` |
| 모든 유형의 실행 제출 | 필요 없음 | 필요 없음 | 다음을 허용 하는 소유자, 참가자 또는 사용자 지정 역할:`"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| 파이프라인 끝점 게시 | 필요 없음 | 필요 없음 | 다음을 허용 하는 소유자, 참가자 또는 사용자 지정 역할:`"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| AKS/ACI 리소스에 등록 된 모델 배포 | 필요 없음 | 필요 없음 | 다음을 허용 하는 소유자, 참가자 또는 사용자 지정 역할:`"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| 배포 된 AKS 끝점에 대 한 점수 매기기 | 필요 없음 | 필요 없음 | 허용 되는 소유자, 참가자 또는 사용자 지정 역할: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (AAD 인증을 사용 하지 않는 경우) 또는 `"/workspaces/read"` (토큰 인증을 사용 하는 경우) |
| 대화형 전자 필기장을 사용 하 여 저장소 액세스 | 필요 없음 | 필요 없음 | 다음을 허용 하는 소유자, 참가자 또는 사용자 지정 역할:`"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*"` |
| 새 사용자 지정 역할 만들기 | 허용 되는 소유자, 참가자 또는 사용자 지정 역할`Microsoft.Authorization/roleDefinitions/write` | 필요 없음 | 다음을 허용 하는 소유자, 참가자 또는 사용자 지정 역할:`/workspaces/computes/write` |

> [!TIP]
> 작업 영역을 처음 만들 때 오류가 발생 하는 경우 역할에서을 허용 하는지 확인 `Microsoft.MachineLearningServices/register/action` 합니다. 이 작업을 통해 Azure 구독에 Azure Machine Learning 리소스 공급자를 등록할 수 있습니다.

### <a name="q-are-we-publishing-azure-built-in-roles-for-the-machine-learning-service"></a>17. Machine Learning 서비스에 대 한 Azure 기본 제공 역할을 게시 하 고 있나요?

현재 Machine Learning 서비스에 대 한 [Azure 기본 제공 역할](/azure/role-based-access-control/built-in-roles) 을 게시 하지 않습니다. 게시 한 후 기본 제공 역할을 업데이트할 수 없으며, 고객 시나리오 및 피드백에 따라 역할 정의를 아직 firming 하 고 있습니다. 

<a id="customroles"></a>

### <a name="q-are-there-some-custom-role-templates-for-the-most-common-scenarios-in-machine-learning-service"></a>17. Machine Learning 서비스에서 가장 일반적인 시나리오에 대 한 사용자 지정 역할 템플릿이 있나요?

예 여기에는 사용자 지정 역할을 정의 하는 기본으로 사용할 수 있는 사용자 지정 제안 역할 정의가 포함 된 몇 가지 일반적인 시나리오가 나와 있습니다.

* __Data 과학자 Custom__: 데이터 과학자가 다음을 **제외 하 고**작업 영역 내의 모든 작업을 수행할 수 있습니다.

    * 계산 만들기
    * 프로덕션 AKS 클러스터에 모델 배포
    * 프로덕션 환경에 파이프라인 끝점 배포

    `data_scientist_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.MachineLearningServices/workspaces/*/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __데이터 과학자 제한 된 사용자 지정__: 허용 된 작업에 와일드 카드가 없는 더 제한적인 역할 정의입니다. 다음을 **제외 하 고**작업 영역 내에서 모든 작업을 수행할 수 있습니다.

    * 계산 만들기
    * 프로덕션 AKS 클러스터에 모델 배포
    * 프로덕션 환경에 파이프라인 끝점 배포

    `data_scientist_restricted_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Restricted Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/computes/start/action",
            "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
            "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
            "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
            "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
            "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/modules/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/write",
            "Microsoft.MachineLearningServices/workspaces/datastores/delete"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Mlops 사용자 지정__: 서비스 주체에 역할을 할당 하 고이를 사용 하 여 mlops 파이프라인을 자동화할 수 있습니다. 예를 들어 이미 게시 된 파이프라인에 대해 실행을 제출 하려면 다음을 수행 합니다.

    `mlops_custom_role.json` :
    ```json
    {
        "Name": "MLOps Custom",
        "IsCustom": true,
        "Description": "Can run pipelines against a published pipeline endpoint",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
            "Microsoft.MachineLearningServices/workspaces/environments/read",    
            "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
            "Microsoft.MachineLearningServices/workspaces/modules/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/read",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __작업 영역 관리자__: 다음을 **제외**하 고 작업 영역 범위 내에서 모든 작업을 수행할 수 있습니다.

    * 새 작업 영역 만들기
    * 구독 또는 작업 영역 수준 할당량 할당
    * 작업 영역 버전 업그레이드

    작업 영역 관리자도 새 역할을 만들 수 없습니다. 작업 영역의 범위 내에서 기존 기본 제공 또는 사용자 지정 역할만 할당할 수 있습니다.

    `workspace_admin_custom_role.json` :
    ```json
    {
        "Name": "Workspace Admin Custom",
        "IsCustom": true,
        "Description": "Can perform all operations except quota management and upgrades",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/write",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.Authorization/roleAssignments/*"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

<a name="labeler"></a>
* __Labeler Custom__: 레이블 데이터에만 한정 되는 역할을 정의할 수 있습니다.

    `labeler_custom_role.json` :
    ```json
    {
        "Name": "Labeler Custom",
        "IsCustom": true,
        "Description": "Can label data for Labeling",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>17. 내 구독의 모든 사용자 지정 역할을 나열 어떻게 할까요??

Azure CLI에서 다음 명령을 실행 합니다.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-operations-supported-by-the-machine-learning-service"></a>17. Machine Learning 서비스에서 지 원하는 작업을 찾을 어떻게 할까요? 있나요?

Azure CLI에서 다음 명령을 실행 합니다.

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

[리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)목록 에서도 찾을 수 있습니다.


### <a name="q-what-are-some-common-gotchas-when-using-azure-rbac"></a>17. Azure RBAC를 사용 하는 경우 몇 가지 일반적인 알려진 문제?

Azure RBAC (역할 기반 액세스 제어)를 사용 하는 동안 알아야 할 몇 가지 사항은 다음과 같습니다.

- 작업 영역 이라고 하는 Azure에서 리소스를 만들 때 작업 영역의 소유자가 아닙니다. 사용자의 역할은 해당 구독에 대해 권한이 부여 된 가장 높은 범위 역할에서 상속 됩니다. 예를 들어 네트워크 관리자이 고 Machine Learning 작업 영역을 만들 수 있는 권한이 있는 경우 소유자 역할이 아니라 해당 작업 영역에 대 한 네트워크 관리자 역할이 할당 됩니다.
- 작업/NotActions의 충돌 하는 섹션을 사용 하는 동일한 AAD 사용자에 두 개의 역할 할당이 있는 경우 한 역할의 NotActions에 나열 된 작업은 다른 역할의 작업으로도 나열 될 경우 적용 되지 않을 수 있습니다. Azure에서 역할 할당을 구문 분석 하는 방법에 대 한 자세한 내용은 [AZURE RBAC에서 사용자에 게 리소스 액세스 권한이 있는지 확인 하는 방법](/azure/role-based-access-control/overview#how-azure-rbac-determines-if-a-user-has-access-to-a-resource) 을 참조 하세요.
- VNet 내에서 계산 리소스를 배포 하려면 해당 VNet 리소스에서 "Microsoft. Network/virtualNetworks/join/action"에 대 한 권한을 명시적으로 부여 해야 합니다.
- 경우에 따라 새 역할 할당이 스택에 캐시 된 사용 권한에 적용 되는 데 1 시간 정도 걸릴 수 있습니다.


### <a name="q-what-permissions-do-i-need-to-use-a-user-assigned-managed-identity-with-my-amlcompute-clusters"></a>17. 사용자 할당 관리 id를 내 Amlcompute 클러스터와 함께 사용 하는 데 필요한 권한은 무엇 인가요?

Amlcompute 클러스터에 사용자 할당 id를 할당 하려면 계산을 만들기 위한 쓰기 권한과 [관리 Id 운영자 역할이](/azure/role-based-access-control/built-in-roles#managed-identity-operator)있어야 합니다. 관리 Id를 사용 하는 RBAC에 대 한 자세한 내용은 [사용자 할당 id를 관리 하는 방법](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) 을 참조 하세요.


### <a name="q-do-we-support-role-based-access-control-on-the-studio-portal"></a>17. 스튜디오 포털에서 역할 기반 액세스 제어를 지원 하나요?

Azure Machine Learning Studio는 azure 역할 기반 액세스 제어 (Azure RBAC)를 지원 합니다. 

> [!IMPORTANT]
> 작업 영역의 데이터 과학자에 대 한 특정 사용 권한이 있는 사용자 지정 역할을 할당 한 후에는 해당 작업 (예: 계산 단추 추가)이 자동으로 사용자에 게 숨겨집니다. 이러한 항목을 숨기면 혼동을 통해 서비스에서 무단 액세스 알림을 반환 하는 컨트롤을 사용할 때 볼 수 없습니다.

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>17. 내 구독에서 역할에 대 한 역할 정의를 찾을 어떻게 할까요? 있나요?

Azure CLI에서 다음 명령을 실행 합니다. 는 `<role-name>` 위의 명령에서 반환 된 것과 동일한 형식 이어야 합니다.

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

예, 작업 영역 버전 업데이트를 방지 하는 역할을 정의할 수 있습니다. 작업 영역 업데이트는 작업 영역 개체에 대 한 패치 호출 이므로 `"NotActions"` JSON 정의의 배열에 다음 작업을 추가 하 여이 작업을 수행 합니다. 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>17. 작업 영역에서 할당량 작업을 수행 하는 데 필요한 권한은 무엇 인가요? 

작업 영역에서 할당량 관련 작업을 수행 하려면 구독 수준 권한이 있어야 합니다. 즉, 구독 범위에서 쓰기 권한이 있는 경우에만 관리 되는 계산 리소스에 대 한 구독 수준 할당량 또는 작업 영역 수준 할당량 설정이 발생할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

- [엔터프라이즈 보안 개요](concept-enterprise-security.md)
- [가상 네트워크 내에서 실험 및 유추/점수를 안전 하 게 실행](how-to-enable-virtual-network.md)
- [자습서: 모델 교육](tutorial-train-models-with-aml.md)
- [리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)

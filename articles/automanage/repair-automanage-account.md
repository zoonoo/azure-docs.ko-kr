---
title: 손상 된 Azure Automanage 계정 복구
description: Automanage 계정을 포함 하는 구독을 새 테 넌 트로 최근에 이동한 경우 다시 구성 해야 합니다. 이 문서에서는 방법에 대해 알아봅니다.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: 226a23bfdacb0f7423c7dafb8cae36af7333699d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681842"
---
# <a name="repair-an-automanage-account"></a>Automanage 계정 복구
[Azure Automanage 계정은](./automanage-virtual-machines.md#automanage-account) 자동화 된 작업이 수행 되는 보안 컨텍스트 또는 id입니다. 자동 관리 계정을 포함 하는 구독을 새 테 넌 트로 최근에 이동한 경우 계정을 다시 구성 해야 합니다. 다시 구성 하려면 id 유형을 다시 설정 하 고 계정에 적절 한 역할을 할당 해야 합니다.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>1 단계: Automanage 계정 id 유형 다시 설정
다음 Azure Resource Manager (ARM) 템플릿을 사용 하 여 자동 관리 계정 id 유형을 다시 설정 합니다. 파일을 armdeploy.js또는 유사한 이름으로 로컬에 저장 합니다. 계정 이름 및 위치는 ARM 템플릿에서 필수 매개 변수 이므로 Automanage 계정 이름 및 위치를 확인 합니다.

1. 다음 템플릿을 사용 하 여 리소스 관리자 배포를 만듭니다. `identityType = None`을 사용하세요.
    * 을 사용 하 여 Azure CLI에서 배포를 만들 수 있습니다 `az deployment sub create` . 자세한 내용은 [az deployment sub](https://docs.microsoft.com/cli/azure/deployment/sub)를 참조 하세요.
    * 모듈을 사용 하 여 PowerShell에서 배포를 만들 수 있습니다 `New-AzDeployment` . 자세한 내용은 [AzDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azdeployment)를 참조 하세요.

1. 를 사용 하 여 동일한 ARM 템플릿을 다시 실행 `identityType = SystemAssigned` 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>2 단계: Automanage 계정에 적절 한 역할 할당
Automanage 계정에는 Automanage에서 관리 하는 Vm을 포함 하는 구독에 대 한 참가자 및 리소스 정책 참여자 역할이 필요 합니다. Azure Portal, ARM 템플릿 또는 Azure CLI를 사용 하 여 이러한 역할을 할당할 수 있습니다.

ARM 템플릿 또는 Azure CLI를 사용 하는 경우 Automanage 계정의 보안 주체 ID (개체 ID 라고도 함)가 필요 합니다. Azure Portal를 사용 하는 경우 ID가 필요 하지 않습니다. 이러한 방법을 사용 하 여이 ID를 찾을 수 있습니다.

- [Azure CLI](https://docs.microsoft.com/cli/azure/ad/sp): 명령을 사용 `az ad sp list --display-name <name of your Automanage Account>` 합니다.

- Azure Portal: **Azure Active Directory** 로 이동 하 여 이름으로 Automanage 계정을 검색 합니다. **엔터프라이즈 응용 프로그램** 에서 표시 되는 경우 계정 이름 자동 관리를 선택 합니다.

### <a name="azure-portal"></a>Azure portal
1. **구독** 에서 Automanaged vm이 포함 된 구독으로 이동 합니다.
1. **액세스 제어 (IAM)** 로 이동 합니다.
1. **역할 할당 추가** 를 선택 합니다.
1. **참가자** 역할을 선택 하 고 Automanage 계정의 이름을 입력 합니다.
1. **저장** 을 선택합니다.
1. 3 ~ 5 단계를 반복 합니다. 이번에는 **리소스 정책 참가자** 역할을 사용 합니다.

### <a name="arm-template"></a>ARM 템플릿
다음 ARM 템플릿을 실행 합니다. Automanage 계정의 주 ID가 필요 합니다. 이를 가져오는 단계는이 섹션의 시작 부분에 나와 있습니다. 메시지가 표시 되 면 ID를 입력 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
다음 명령을 실행하세요.

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>다음 단계
[Azure Automanage에 대해 자세히 알아보기](./automanage-virtual-machines.md)

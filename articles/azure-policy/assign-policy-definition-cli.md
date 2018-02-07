---
title: "Azure CLI를 사용하여 Azure 환경 내에서 규정 비준수 리소스를 식별하는 정책 할당 만들기 | Microsoft Docs"
description: "PowerShell을 사용하여 규정 비준수 리소스를 식별하는 Azure Policy 할당을 만듭니다."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/17/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 76725f3ebeaf5af4f2ab8aadb303d862fa111ecb
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Azure CLI를 사용하여 Azure 환경 내에서 규정 비준수 리소스를 식별하는 정책 할당 만들기

Azure의 규정 준수를 이해하는 첫 번째 단계는 리소스 상태를 식별하는 것입니다. 이 빠른 시작은 Managed Disks를 사용하지 않는 가상 머신을 식별하는 정책 할당 만들기 과정을 단계별로 안내합니다.

이 프로세스가 끝나면 관리 디스크를 사용하지 않는 가상 머신이 식별됩니다. 이 가상 머신은 정책 할당의 *비규격*입니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

이 빠른 시작을 수행하려면 Azure CLI 버전 2.0.4 이상을 실행하여 로컬로 CLI를 설치하고 사용해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

이 빠른 시작에서 정책 할당을 만들고 Managed Disks가 없는 Virtual Machines 감사 정의를 할당합니다. 이 정책 정의는 정책 정의에 설정한 조건을 준수하지 않는 리소스를 식별합니다.

다음 단계에 따라 새 정책 할당을 만듭니다.

1. 구독이 리소스 공급자와 함께 작동하도록 Policy Insights 리소스 공급자를 등록합니다. 리소스 공급자를 등록하려면 리소스 공급자에 대해 작업 등록을 수행할 권한이 있어야 합니다. 이 작업은 참가자 및 소유자 역할에 포함되어 있습니다.

    다음 명령을 실행하여 리소스 공급자를 등록합니다.

    ```azurecli
    az provider register --namespace Microsoft.PolicyInsights
    ```

    이 명령은 등록이 진행 중이라는 내용의 메시지를 반환합니다.

    구독에 리소스 공급자의 리소스 종류가 아직 포함되어 있으면 리소스 공급자를 등록 취소할 수 없습니다. 리소스 공급자를 등록하고 살펴보는 방법에 대한 내용은 [리소스 공급자 및 종류](../azure-resource-manager/resource-manager-supported-services.md)를 참조하세요.

2. 모든 정책 정의를 보고 *Managed Disks가 없는 Virtual Machines 감사* 정책 정의를 찾습니다.

    ```azurecli
az policy definition list
```

    Azure Policy는 사용 가능한 기본 정책 정의를 제공합니다. 기본 제공되는 정책 정의는 다음과 같습니다.

    - 태그 및 해당 값 강제 적용
    - 태그 및 해당 값 적용
    - SQL Server 버전 12.0 필요

3. 다음으로, 다음 정보를 제공하고 다음 명령을 실행하여 정책 정의를 할당합니다.

    - 정책 할당에 대한 표시 **이름** - 이 경우 *Managed Disks가 없는 Virtual Machine 감사*를 사용합니다.
    - **정책** – 할당을 만드는 데 사용되는 정책 정의입니다. 이 경우 정책 정의는 *Managed Disks가 없는 Virtual Machines 감사*입니다.
    - **범위** - 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 구독에서 리소스 그룹까지 다양한 범위가 있습니다.

    이전에 등록한 구독(또는 리소스 그룹)을 사용합니다. 이 예제에서는 구독 ID로 **bc75htn-a0fhsi-349b-56gh-4fghti-f84852**, 리소스 그룹 이름으로 **FabrikamOMS**를 사용합니다. 이러한 구독 ID와 리소스 그룹 이름을 사용하려는 구독 ID와 리소스 그룹 이름으로 변경해야 합니다.

    이 명령은 다음과 비슷해야 합니다.

    ```azurecli
az policy assignment create --name Audit Virtual Machines without Managed Disks Assignment --policy Audit Virtual Machines without Managed Disks --scope /subscriptions/
bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

정책 할당은 일정 범위 내에서 이루어지도록 할당된 정책입니다. 이 범위 역시 관리 그룹부터 리소스 그룹까지 아우를 수 있습니다.

## <a name="identify-non-compliant-resources"></a>규정 비준수 리소스 식별

새로운 이 할당에서 규정 비준수 리소스를 보려면:

1. Azure Policy 페이지로 돌아갑니다.
2. 왼쪽 창에서 **준수**를 선택하고 앞에서 만든 **정책 할당**을 검색합니다.

   ![정책 준수](media/assign-policy-definition/policy-compliance.png)

   새 할당을 준수하지 않는 기존 리소스는 **비준수 리소스** 탭에 표시됩니다. 이전 이미지는 비준수 리소스의 예를 보여줍니다.

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 가이드는 이 빠른 시작에 기반하여 작성되었습니다. 후속 자습서를 계속 사용하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려면 다음 명령을 실행하여 만든 할당을 삭제합니다.

```azurecli
az policy assignment delete –name  Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure 환경에서 규정 비준수 리소스를 식별하는 정책 정의를 할당했습니다.

**앞으로** 만들 리소스가 규정을 준수하도록 정책 할당에 대해 자세히 알아보려면 다음 주제에 대한 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](./create-manage-policy.md)

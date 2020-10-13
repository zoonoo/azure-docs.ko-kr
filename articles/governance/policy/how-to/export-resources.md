---
title: Azure Policy 리소스 내보내기
description: 정책 정의 및 정책 할당과 같은 Azure Policy 리소스를 GitHub로 내보내는 방법을 알아봅니다.
ms.date: 09/30/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 691e0a026c5f4f1a0a68c744ee81b1da8da9e70b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777091"
---
# <a name="export-azure-policy-resources"></a>Azure Policy 리소스 내보내기

이 문서에서는 기존 Azure Policy 리소스를 내보내는 방법에 대 한 정보를 제공 합니다. 리소스 내보내기는 유용 하 고 백업에 권장 되지만 클라우드 거 버 넌 스를 사용 하 고 [정책을 코드로](../concepts/policy-as-code.md)처리 하는 중요 한 단계 이기도 합니다. Azure Policy 리소스는 [Azure Portal](#export-with-azure-portal), [Azure CLI](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell)및 지원 되는 각 sdk를 통해 내보낼 수 있습니다.

## <a name="export-with-azure-portal"></a>Azure Portal로 내보내기

Azure Portal에서 정책 정의를 내보내려면 다음 단계를 수행 합니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. Azure Policy 페이지의 왼쪽에서 **정의** 를 선택 합니다.

1. **정의 내보내기** 단추를 사용 하거나 정책 정의의 행에서 줄임표를 선택한 다음 **정의 내보내기**를 선택 합니다.

1. **GitHub를 사용 하 여 로그인** 단추를 선택 합니다. 아직 GitHub를 사용 하 여 리소스를 내보내도록 Azure Policy 권한을 부여 하지 않은 경우 열리는 새 창에서 [Github 작업](https://github.com/features/actions) 요구 사항에 액세스를 검토 하 고 **AzureGitHubActions 권한 부여** 를 선택 하 여 내보내기 프로세스를 계속 합니다. 완료 되 면 새 창이 자동으로 닫힙니다.

1. **기본 사항** 탭에서 다음 옵션을 설정한 후 페이지 맨 아래에 있는 **정책** 탭 또는 **다음: 정책** 단추를 선택 합니다.

   - **리포지토리 필터**: _내 리포지토리로_ 설정 하 여 사용자가 소유한 리포지토리만 보거나 _모든 리포지토리_ 를 확인 하 여 GitHub 작업에 액세스 하도록 허용한 모든 리포지토리를 확인 합니다.
   - **리포지토리**: Azure Policy 리소스를 내보낼 리포지토리로 설정 합니다.
   - **분기**: 리포지토리에서 분기를 설정 합니다. 기본이 아닌 분기를 사용 하면 소스 코드에 추가 하기 전에 업데이트의 유효성을 검사 하는 좋은 방법입니다.
   - **Directory**: Azure Policy 리소스를 내보낼 _루트 수준 폴더_ 입니다. 이 디렉터리 아래의 하위 폴더는 내보내는 리소스에 따라 만들어집니다.

1. **정책** 탭에서 줄임표를 선택 하 고 관리 그룹, 구독 또는 리소스 그룹의 조합을 선택 하 여 검색 범위를 설정 합니다.
   
1. **정책 정의 추가** 단추를 사용 하 여 내보낼 개체 범위를 검색 합니다. 열리는 측 창에서 내보낼 각 개체를 선택 합니다. 검색 상자 또는 유형을 선택 하 여 선택 항목을 필터링 합니다. 내보낼 모든 개체를 선택 했으면 페이지 아래쪽의 **추가** 단추를 사용 합니다.

1. 선택한 각 개체에 대해 정책 정의에 대 한 정의, _정의 및 할당_ _만_ 을 비롯 하 여 원하는 내보내기 옵션을 선택 합니다. 그런 다음 페이지 아래쪽의 검토 **+ 내보내기** 탭 또는 **다음: 검토 + 내보내기** 단추를 선택 합니다.

   > [!NOTE]
   > 옵션 _정의와 할당_ 을 선택한 경우 정책 정의를 추가할 때 필터에 의해 설정 된 범위 내의 정책 할당만 내보내집니다.

1. **검토 + 내보내기** 탭에서 세부 정보 일치를 확인 한 다음 페이지 맨 아래에 있는 **내보내기** 단추를 사용 합니다.

1. GitHub 리포지토리, 분기 및 _루트 수준 폴더_ 를 확인 하 여 선택한 리소스를 이제 원본 제어로 내보냈습니다.

Azure Policy 리소스를 선택한 GitHub 리포지토리 및 _루트 수준 폴더_내의 다음 구조로 내보냅니다.

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Azure CLI로 내보내기

Azure Policy 정의, 이니셔티브 및 할당을 [Azure CLI](/cli/azure/install-azure-cli)를 사용 하 여 JSON으로 내보낼 수 있습니다. 이러한 각 명령은 **name** 매개 변수를 사용 하 여 JSON을 가져올 개체를 지정 합니다. **Name** 속성은 대개 _GUID_ 이며 개체의 **displayName** 이 아닙니다.

- 정의- [az policy definition show](/cli/azure/policy/definition#az-policy-definition-show)
- 이니셔티브- [az policy set-definition show](/cli/azure/policy/set-definition#az-policy-set-definition-show)
- 할당- [az policy 할당 show](/cli/azure/policy/assignment#az-policy-assignment-show)

다음은 **이름이** _VirtualMachineStorage_인 정책 정의에 대 한 JSON을 가져오는 예제입니다.

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Azure PowerShell로 내보내기

Azure Policy 정의, 이니셔티브 및 할당을 [Azure PowerShell](/powershell/azure/)를 사용 하 여 JSON으로 내보낼 수 있습니다. 이러한 각 cmdlet은 **Name** 매개 변수를 사용 하 여 JSON을 가져올 개체를 지정 합니다. **Name** 속성은 대개 _GUID_ 이며 개체의 **displayName** 이 아닙니다.

- [AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- 이니셔티브- [AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- 할당- [AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

다음은 **이름이** _VirtualMachineStorage_인 정책 정의에 대 한 JSON을 가져오는 예제입니다.

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage'
```

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](programmatically-create.md)하는 방법을 이해합니다.
- [규정 비준수 리소스를 수정](remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.

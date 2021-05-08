---
title: 관리 ID 관련 FAQ 및 알려진 문제 - Azure AD
description: Azure 리소스에 대한 관리 ID 관련 알려진 문제
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/04/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3f1be2e64435cb0bcdb369a398a9a65fc3714fb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100008539"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID 관련 FAQ 및 알려진 문제

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>FAQ(질문과 대답)

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>관리 ID가 있는 리소스는 어떻게 찾을 수 있나요?

다음 Azure CLI 명령을 사용하여 시스템 할당 관리 ID가 있는 리소스 목록을 찾을 수 있습니다. 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="do-managed-identities-have-a-backing-app-object"></a>관리 ID에 지원 앱 개체가 있나요?

아니요. 디렉터리 상 관리 ID와 Azure AD App Registrations는 동일하지 않습니다. 

앱 등록은 애플리케이션 개체와 서비스 주체의 두 가지 구성 요소로 이루어집니다. Azure 리소스용 관리 ID의 구성 요소는 Service Principal Object뿐입니다. 

관리 ID는 디렉터리에 일반적으로 MS 그래프의 앱 사용 권한 부여에 사용되는 애플리케이션 개체를 가지고 있지 않습니다. 대신 관리 ID의 MS 그래프 권한을 직접 서비스 주체에게 부여해야 합니다.  

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>동일한 관리 ID를 여러 하위 지역에서 사용할 수 있나요?

간단히 말하자면 그렇습니다. 둘 이상의 Azure 지역에서 사용자 할당 관리 ID를 사용할 수 있습니다. 자세히 말하면, 사용자 할당 관리 ID는 하위 지역 리소스로 만들지만 Azure AD에서 만든 연결된 [서비스 주체](../develop/app-objects-and-service-principals.md#service-principal-object)(SPN)는 전 세계에서 사용 가능합니다. 모든 Azure지역에서 서비스 주체를 쓸 수 있으며 서비스 주체의 가용성은 Azure AD의 가용성에 기인합니다. 예를 들어, 중남부 지역에서 사용자 할당 관리 ID를 만들었는데 해당 하위 지역을 사용할 수 없게 되면, 관련 문제는 관리 ID 자체의 [컨트롤 플레인](../../azure-resource-manager/management/control-plane-and-data-plane.md) 작업에만 영향을 줍니다.  관리 ID를 사용하도록 이미 구성된 리소스에서 수행하는 작업은 영향을 받지 않습니다.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Azure 리소스에 대한 관리 ID가 Azure Cloud Services에서 작동하나요?

아니요. Azure 리소스에 대한 관리 ID는 Azure Cloud Services에서 지원될 예정이 없습니다.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>관리 ID와 연결된 자격 증명은 무엇인가요? 유효 기간은 얼마나 되며 얼마나 자주 회전하나요?

> [!NOTE]
> 관리 ID 인증 방법은 예고 없이 변경될 수 있는 내부 구현 세부 정보입니다.

관리 ID는 인증서 기반 인증을 사용합니다. 각 관리 ID 자격 증명은 90일 후에 만료되며 45일 후에 롤업됩니다.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID의 보안 경계란 무엇인가요?

ID의 보안 경계는 연결되는 리소스입니다. 예를 들어, Azure 리소스에 대한 관리 ID를 갖는 가상 머신의 보안 경계는 바로 가상 머신입니다. 해당 VM에서 실행되는 모든 코드는 Azure 리소스에 대한 관리 ID 엔드포인트 및 요청 토큰을 호출할 수 있습니다. 이것은 Azure 리소스에 대한 관리 ID를 지원하는 다른 리소스와 비슷한 환경입니다.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>요청에 ID를 지정하지 않을 경우 IMDS 기본값은 무엇인가요?

- 시스템에서 할당된 관리 ID가 활성화되지 않은 경우 요청에 ID를 지정하지 않으면 IMDS 기본값은 시스템에서 할당된 관리 ID가 됩니다.
- 시스템에서 할당된 관리 ID가 활성화되지 않은 경우 사용자가 할당한 관리 ID 하나만 있으면 IMDS 기본값은 사용자가 할당한 관리 ID가 됩니다. 
- 시스템에서 할당된 관리 ID가 활성화되지 않은 경우 사용자가 할당한 관리 ID가 여러 개 존재하면 요청에 관리 ID를 지정해야 합니다.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>구독을 다른 디렉터리로 이동하면 관리 ID가 자동으로 다시 생성되나요?

아니요. 구독을 다른 디렉터리로 이동하면 관리 ID를 수동으로 다시 만들고 다시 Azure 역할 할당을 해야 합니다.
- 시스템에서 할당된 관리 ID: 비활성화하거나 재활성화합니다. 
- 사용자 할당 관리 ID: 삭제한 후 다시 만들어서 필요한 리소스(예: 가상 머신)에 다시 연결합니다.

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>관리 ID를 사용하여 다른 디렉터리/테넌트의 리소스에 액세스할 수 있나요?

아니요. 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>리소스에서 관리 ID에 필요한 Azure RBAC 권한은 무엇인가요? 

- 시스템이 할당한 관리형 ID: 리소스에 대한 쓰기 권한이 필요합니다. 예를 들어 가상 머신의 경우 Microsoft.Compute/virtualMachines/write가 필요합니다. 이 작업은 [Virtual Machine 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)와 같은 리소스별 기본 제공 역할에 포함됩니다.
- 사용자가 할당한 관리형 ID: 리소스에 대한 쓰기 권한이 필요합니다. 예를 들어 가상 머신의 경우 Microsoft.Compute/virtualMachines/write가 필요합니다. 관리 ID에 대한 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 역할 할당도 필요합니다.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>사용자 할당 관리 ID를 만들지 못하게 하려면 어떻게 해야 하나요?

[Azure Policy](../../governance/policy/overview.md)를 사용하여 사용자들이 사용자 할당 관리 ID를 만들지 못하게 할 수 있습니다.

- [Azure Portal](https://portal.azure.com)로 탐색하여 **정책** 으로 이동합니다.
- **정의** 를 선택합니다.
- **+ 정책 정의** 를 선택하고 필요한 정보를 입력합니다.
- 정책 규칙 섹션에 붙여넣습니다.

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {}
}

```

정책을 만든 후 사용하려는 리소스 그룹에 할당합니다.

- 리소스 그룹으로 이동합니다.
- 테스트에 사용할 리소스 그룹을 찾습니다.
- 왼쪽 메뉴의 **정책** 을 선택합니다.
- **정책 할당** 을 선택합니다.
- **기본** 섹션에서 다음을 제공합니다.
    - **범위** 테스트에 사용할 리소스 그룹
    - **정책 정의**: 이전에 만든 정책입니다.
- 다른 모든 설정은 기본값으로 유지하고 **검토 + 만들기** 를 선택합니다.

이 시점에서는 리소스 그룹에 사용자 할당 관리 ID를 만들 수 없습니다.

  ![정책 위반](./media/known-issues/policy-violation.png)

## <a name="known-issues"></a>알려진 문제

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Azure 리소스에 대한 관리 ID 확장에 대해 스키마 내보내기를 시도할 때 "자동화 스크립트"가 실패함

VM에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정하는 경우 VM 또는 해당 리소스 그룹에 "자동화 스크립트" 기능을 사용하려고 할 때 다음과 같은 오류가 표시됩니다.

![Azure 리소스에 대한 관리 ID 자동화 스크립트 내보내기 오류](./media/msi-known-issues/automation-script-export-error.png)

현재 Azure 리소스에 대한 관리 ID VM 확장(2019년 1월에 사용 중단 예정)은 리소스 그룹 템플릿으로 해당 스키마를 내보내는 기능을 지원하지 않습니다. 결과적으로 생성된 템플릿은 리소스에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정하는 구성 매개 변수를 표시하지 않습니다. [템플릿을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-template-windows-vm.md)의 예제를 수행하여 이러한 섹션을 수동으로 추가할 수 있습니다.

스키마 내보내기 기능을 Azure 리소스에 대한 관리 ID VM 확장(2019년 1월에 사용 중단 예정)에 사용할 수 있는 경우 [VM 확장을 포함하는 리소스 그룹 내보내기](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)에 나열됩니다.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM이 리소스 그룹 또는 구독에서 이동한 후 시작되지 않음

실행 중인 상태의 VM을 이동하면 이동 중에도 VM은 계속 실행됩니다. 그러나 이동 후 중지했다가 다시 시작하면 VM이 시작되지 않습니다. 이 문제가 발생하는 이유는 VM이 Azure 리소스에 대한 관리 ID에 대한 참조를 업데이트하지 않고 이전 리소스 그룹의 ID를 계속 가리키기 때문입니다.

**해결 방법** 
 
Azure 리소스에 대한 관리 ID의 올바른 값을 가져올 수 있도록 VM에서 업데이트를 트리거합니다. VM 속성 변경을 수행하여 Azure 리소스에 대한 관리 ID에 대한 참조를 업데이트할 수 있습니다. 예를 들어 다음 명령을 사용하여 VM에 새 태그 값을 설정할 수 있습니다.

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
이 명령은 VM에 값이 1인 새 태그 "fixVM"을 설정합니다. 
 
이 속성을 설정하면 VM은 올바른 Azure 리소스에 대한 관리 ID 리소스 URI로 업데이트됩니다. 그리고 나면 VM을 시작할 수 있습니다. 
 
VM이 시작되면 다음 명령을 사용하여 태그를 제거할 수 있습니다.

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Azure AD 디렉터리 간에 구독 전송

구독이 다른 디렉터리로 이동/전송되면 관리 ID는 업데이트되지 않습니다. 결과적으로, 존재하는 시스템에서 할당하거나 사용자가 할당한 관리 ID가 모두 손상됩니다. 

다른 디렉터리로 이동한 구독의 관리 ID에 대한 해결 방법:

 - 시스템에서 할당된 관리 ID: 비활성화하거나 재활성화합니다. 
 - 사용자 할당 관리 ID: 삭제한 후 다시 만들어서 필요한 리소스(예: 가상 머신)에 다시 연결합니다.

자세한 내용은 [다른 Azure AD 디렉터리로 Azure 구독 양도](../../role-based-access-control/transfer-subscription.md)를 참조하세요.

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>사용자가 할당한 관리 ID를 다른 리소스 그룹/구독으로 이동

사용자가 할당한 관리 ID를 다른 리소스 그룹으로 이동하는 것은 지원되지 않습니다.

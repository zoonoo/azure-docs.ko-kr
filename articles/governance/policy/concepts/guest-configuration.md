---
title: 가상 머신 내에서 감사를 수행하는 방법 이해
description: Azure Policy가 게스트 구성을 사용하여 Azure 가상 머신 내에서 설정을 감사하는 방법에 대해 알아봅니다.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 0a571084819c5dfed3f8d6891b59032ef2eecdd6
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856403"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure Policy 게스트 구성 이해

Azure Policy는 Azure 리소스를 감사 및 [수정](../how-to/remediate-resources.md)할 수 있을 뿐 아니라 가상 머신 내의 설정도 감사할 수 있습니다. 게스트 구성 확장 및 클라이언트가 유효성 검사를 수행합니다. 이 확장은 클라이언트를 통해 운영 체제 구성, 애플리케이션 구성/유무, 환경 설정 등의 설정 유효성을 검사합니다.

> [!IMPORTANT]
> 현재는 **기본 제공** 정책만 게스트 구성에서 지원됩니다.

## <a name="extension-and-client"></a>확장 및 클라이언트

가상 머신 내에서 설정을 감사할 수 있도록 [가상 머신 확장](../../../virtual-machines/extensions/overview.md)이 사용하도록 설정됩니다. 이 확장은 적용 가능한 정책 할당 및 해당 구성 정의를 다운로드합니다.

### <a name="register-guest-configuration-resource-provider"></a>게스트 구성 리소스 공급자 등록

게스트 구성을 사용하려면 먼저 리소스 공급자를 등록해야 합니다. 포털 또는 PowerShell을 통해 등록할 수 있습니다.

#### <a name="registration---portal"></a>등록 - 포털

Azure Portal을 통해 게스트 구성용 리소스 공급자를 등록하려면 다음 단계를 수행합니다.

1. Azure Portal을 시작하고 **모든 서비스**를 클릭합니다. **구독**을 검색하여 선택합니다.

1. 게스트 구성을 사용하도록 설정할 구독을 찾아서 클릭합니다.

1. **구독** 페이지의 왼쪽 메뉴에서 **리소스 공급자**를 클릭합니다.

1. **Microsoft.GuestConfiguration**이 표시될 때까지 필터링하거나 스크롤한 다음 같은 행에서 **등록**을 클릭합니다.

#### <a name="registration---powershell"></a>등록 - PowerShell

PowerShell을 통해 게스트 구성용 리소스 공급자를 등록하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>유효성 검사 도구

가상 머신 내에서 게스트 구성 클라이언트는 로컬 도구를 사용하여 감사를 실행합니다.

다음 표에는 지원되는 각 운영 체제에서 사용되는 로컬 도구 목록이 나와 있습니다.

|운영 체제|유효성 검사 도구|메모|
|-|-|-|
| Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 게스트 구성 확장을 통해 Ruby 및 Python이 설치됩니다. |

### <a name="supported-client-types"></a>지원되는 클라이언트 유형

다음 표에는 Azure 이미지에서 지원되는 운영 체제 목록이 나와 있습니다.

|게시자|이름|버전|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> 사용자 지정 가상 머신 이미지에서는 현재 게스트 구성이 지원되지 않습니다.

### <a name="unsupported-client-types"></a>지원되지 않는 클라이언트 유형

다음 표에는 지원되지 않는 운영 체제가 나열되어 있습니다.

|운영 체제|메모|
|-|-|
|Windows 클라이언트 | 클라이언트 운영 체제(예: Windows 7 및 Windows 10)는 지원되지 않습니다.
|Windows Server 2016 Nano Server | 지원되지 않습니다.|

## <a name="guest-configuration-definition-requirements"></a>게스트 구성 정의 요구 사항

게스트 구성에서 실행하는 각 감사에는 두 가지 정책 정의(**DeployIfNotExists** 및 **AuditIfNotExists**)가 필요합니다. **DeployIfNotExists**는 게스트 구성 에이전트 및 [유효성 검사 도구](#validation-tools) 지원을 위한 기타 구성 요소가 포함된 가상 머신을 준비하는 데 사용됩니다.

**DeployIfNotExists** 정책 정의는 다음 항목의 유효성을 검사하고 수정합니다.

- 가상 머신에 평가할 구성이 할당되었는지 확인합니다. 현재 할당된 구성이 없으면 다음 작업수을 행하여 할당을 가져오고 가상 머신을 준비합니다.
  - [관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 가상 머신에 인증
  - **Microsoft.GuestConfiguration** 확장의 최신 버전 설치
  - [유효성 검사 도구](#validation-tools) 및 종속성(필요한 경우) 설치

**DeployIfNotExists**가 규정을 준수하는 것으로 확인되면 **AuditIfNotExists** 정책 정의는 로컬 유효성 검사 도구를 사용하여 할당된 구성의 규정 준수 여부를 확인합니다. 유효성 검사 도구는 게스트 구성 클라이언트에 결과를 제공합니다. 클라이언트는 게스트 확장에 결과를 전달하므로 게스트 구성 리소스 공급자를 통해 사용할 수 있습니다.

Azure Policy는 게스트 구성 리소스 공급자 **complianceStatus** 속성을 사용하여 **규정 준수** 노드에서 규정 준수를 보고합니다. 자세한 내용은 [규정 준수 데이터 가져오기](../how-to/getting-compliance-data.md)를 참조하세요.

> [!NOTE]
> 각 게스트 구성 정의에는 **DeployIfNotExists** 및 **AuditIfNotExists** 정책 정의가 모두 포함되어 있어야 합니다.

할당에 사용할 정의를 그룹화할 수 있도록, 게스트 구성을 위한 모든 기본 제공 정책은 이니셔티브에 포함됩니다. *[미리 보기]: Linux 및 Windows 가상 머신 내부의 암호 보안 설정 감사*라는 기본 제공 이니셔티브에는 정책 18개가 포함되어 있습니다. 그리고 Window용 **DeployIfNotExists** 및 **AuditIfNotExists** 쌍 6개와 Linux용 쌍 3개가 있습니다. 각 경우에서 정의 내의 논리는 [정책 규칙](definition-structure.md#policy-rule) 정의를 기준으로 하여 대상 운영 체제만 평가되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Policy 정의 구조](definition-structure.md)를 검토합니다.
- [정책 효과 이해](effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](../how-to/programmatically-create.md)하는 방법을 이해합니다.
- [규정 준수 데이터를 가져오는 방법](../how-to/getting-compliance-data.md)을 알아봅니다.
- [비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/index.md)을 포함하는 관리 그룹을 검토합니다.
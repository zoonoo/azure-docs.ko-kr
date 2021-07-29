---
title: 리소스 계층 구조를 보호하는 방법 - Azure 거버넌스
description: 기본 관리 그룹 설정을 포함하는 계층 설정으로 리소스 계층 구조를 보호하는 방법에 대해 알아봅니다.
ms.date: 04/09/2021
ms.topic: conceptual
ms.openlocfilehash: c87603510d036efded1331a5c08a7aae17326d09
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108765164"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>리소스 계층 구조를 보호하는 방법

리소스, 리소스 그룹, 구독, 관리 그룹 및 테넌트가 리소스 계층 구조를 집합적으로 구성합니다. Azure 사용자 지정 역할 또는 Azure Policy 정책 할당과 같은 루트 관리 그룹의 설정은 리소스 계층 구조의 모든 리소스에 영향을 줄 수 있습니다. 모든 리소스에 부정적인 영향을 줄 수 있는 변경으로부터 리소스 계층 구조를 보호하는 것이 중요합니다.

이제 관리 그룹에 테넌트 관리자가 이러한 동작을 제어할 수 있도록 하는 계층 설정이 있습니다. 이 문서에서는 사용 가능한 각 계층 설정과 이러한 계층 설정을 지정하는 방법을 설명합니다.

## <a name="azure-rbac-permissions-for-hierarchy-settings"></a>계층 설정에 대한 Azure RBAC 권한

계층 설정을 구성하려면 루트 관리 그룹에 대해 다음과 같은 두 가지 리소스 공급자 작업이 필요합니다.

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

이러한 작업은 사용자가 계층 설정을 읽고 업데이트만 할 수 있도록 허용합니다. 작업은 관리 그룹 계층 구조 또는 계층 구조의 리소스에 대한 다른 액세스 권한을 제공하지 않습니다. 이러한 작업은 모두 Azure 기본 제공 역할 **계층 설정 관리자** 에서 사용할 수 있습니다.

## <a name="setting---default-management-group"></a>설정 - 기본 관리 그룹

기본적으로 테넌트 내에 추가된 새 구독은 루트 관리 그룹의 멤버로 추가됩니다. 정책 할당, Azure RBAC(Azure 역할 기반 액세스 제어) 및 기타 거버넌스 구문이 루트 관리 그룹에 할당되면 이러한 새 구독에 즉시 적용됩니다. 이러한 이유로 대부분의 조직에서는 루트 관리 그룹이 이러한 구성을 할당하기에 적합한 그룹이더라도 루트 관리 그룹에 적용하지 않습니다. 다른 경우에는 보다 제한적인 컨트롤 집합이 새 구독에 대해 필요하지만 모든 구독에 할당되어서는 안 됩니다. 이 설정은 두 사용 사례 모두 지원합니다.

새 구독에 대한 기본 관리 그룹을 정의하는 것을 허용하여 조직 차원의 거버넌스 구문을 루트 관리 그룹에 적용할 수 있으며, 새 구독에 더 적합한 정책 할당 또는 Azure 역할 할당을 가진 별도의 관리 그룹을 정의할 수 있습니다.

### <a name="set-default-management-group-in-portal"></a>포털에서 기본 관리 그룹 설정

Azure Portal에서 이 설정을 구성하려면 다음 단계를 따릅니다.

1. 검색 창을 사용하여 '관리 그룹'을 검색하고 선택합니다.

1. 루트 관리 그룹에서 관리 그룹 이름 옆에 있는 **세부 정보** 를 선택 합니다.

1. **설정** 아래에서 **계층 구조 설정** 을 선택합니다.

1. **기본 관리 그룹 변경** 단추를 선택합니다.

   > [!NOTE]
   > **기본 관리 그룹 변경** 단추를 사용할 수 없는 경우 보고 있는 관리 그룹이 루트 관리 그룹이 아니거나 보안 주체에 계층 설정을 변경하는 데 필요한 권한이 없는 것입니다.

1. 계층 구조에서 관리 그룹을 선택하고 **선택** 단추를 사용합니다.

### <a name="set-default-management-group-with-rest-api"></a>REST API를 사용하여 기본 관리 그룹 설정

REST API를 사용하여 이 설정을 구성하기 위해 [계층 구조 설정](/rest/api/managementgroups/hierarchysettings) 엔드포인트가 호출됩니다. 이렇게 하려면 다음 REST API URI 및 본문 형식을 사용합니다. `{rootMgID}`를 루트 관리 그룹의 ID로 바꾸고 `{defaultGroupID}`를 기본 관리 그룹이 될 관리 그룹의 ID로 바꿉니다.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- 요청 본문

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

기본 관리 그룹을 루트 관리 그룹으로 다시 설정하려면 동일한 엔드포인트를 사용하고 **defaultManagementGroup** 을 `/providers/Microsoft.Management/managementGroups/{rootMgID}` 값으로 설정합니다.

## <a name="setting---require-authorization"></a>설정 - 권한 부여 필요

기본적으로 모든 사용자는 테넌트 내에 새 관리 그룹을 만들 수 있습니다. 테넌트의 관리자는 관리 그룹 계층 구조에서 일관성 및 준수를 유지하기 위해 특정 사용자에게만 이러한 사용 권한을 제공하려고 할 수 있습니다. 사용하도록 설정하면 사용자는 루트 관리 그룹에서 `Microsoft.Management/managementGroups/write` 작업을 수행하여 새 자식 관리 그룹을 만들어야 합니다.

### <a name="set-require-authorization-in-portal"></a>포털에서 권한 부여 필요 설정

Azure Portal에서 이 설정을 구성하려면 다음 단계를 따릅니다.

1. 검색 창을 사용하여 '관리 그룹'을 검색하고 선택합니다.

1. 루트 관리 그룹에서 관리 그룹 이름 옆에 있는 **세부 정보** 를 선택 합니다.

1. **설정** 아래에서 **계층 구조 설정** 을 선택합니다.

1. **새 관리 그룹을 만드는 데 쓰기 권한이 필요합니다.** 옵션을 설정으로 전환합니다.

   > [!NOTE]
   > **새 관리 그룹을 만드는 데 쓰기 권한이 필요합니다.** 옵션을 사용할 수 없는 경우 보고 있는 관리 그룹이 루트 관리 그룹이 아니거나 보안 주체에 계층 설정을 변경하는 데 필요한 권한이 없는 것입니다.

### <a name="set-require-authorization-with-rest-api"></a>REST API로 권한 부여 필요 설정

REST API를 사용하여 이 설정을 구성하기 위해 [계층 구조 설정](/rest/api/managementgroups/hierarchysettings) 엔드포인트가 호출됩니다. 이렇게 하려면 다음 REST API URI 및 본문 형식을 사용합니다. 이 값은 _부울_ 이므로 값에 **true** 또는 **false** 를 제공합니다. **true** 값은 이 방법을 통해 관리 그룹 계층 구조를 보호합니다.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- 요청 본문

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

설정을 다시 끄려면 동일한 엔드포인트를 사용하고 **requireAuthorizationForGroupCreation** 을 **false** 값으로 설정합니다.

## <a name="powershell-sample"></a>PowerShell 샘플

PowerShell에는 기본 관리 그룹을 설정하거나 권한 부여가 필요한 집합을 설정하는 'Az' 명령이 없지만 해결 방법으로 아래 PowerShell 샘플과 함께 REST API를 사용할 수 있습니다.

```powershell
$root_management_group_id = "Enter the ID of root management group"
$default_management_group_id = "Enter the ID of default management group (or use the same ID of the root management group)"

$body = '{
     "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/' + $default_management_group_id + '",
          "requireAuthorizationForGroupCreation": true
     }
}'

$token = (Get-AzAccessToken).Token
$headers = @{"Authorization"= "Bearer $token"; "Content-Type"= "application/json"}
$uri = "https://management.azure.com/providers/Microsoft.Management/managementGroups/$root_management_group_id/settings/default?api-version=2020-02-01"

Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body
```

## <a name="next-steps"></a>다음 단계

관리 그룹에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [관리 그룹을 만들어 Azure 리소스 구성](../create-management-group-portal.md)
- [관리 그룹을 변경, 삭제 또는 관리하는 방법](../manage.md)

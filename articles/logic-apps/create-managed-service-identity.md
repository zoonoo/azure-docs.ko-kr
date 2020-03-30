---
title: 관리되는 ID를 사용하여 인증
description: 관리되는 ID를 사용하여 자격 증명 또는 암호로 로그인하지 않고 다른 Azure Active Directory 테넌의 리소스에 액세스
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117516"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Azure 논리 앱에서 관리되는 ID를 사용하여 Azure 리소스에 대한 액세스를 인증합니다.

다른 Azure Active Directory(Azure AD) 테넌트에서 리소스에 액세스하고 로그인하지 않고 ID를 인증하려면 논리 앱에서 자격 증명이나 비밀이 아닌 [관리되는 ID(이전의](../active-directory/managed-identities-azure-resources/overview.md) 관리되는 서비스 ID 또는 MSI)를 사용할 수 있습니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다.

Azure Logic Apps는 시스템 할당 및 [*사용자*](../active-directory/managed-identities-azure-resources/overview.md) [*할당관리*](../active-directory/managed-identities-azure-resources/overview.md) ID를 모두 지원합니다. 논리 앱은 시스템 할당 ID 또는 *단일* 사용자 할당 ID를 사용할 수 있으며, 이 ID는 논리 앱 그룹 간에 공유할 수 있지만 둘 다 공유할 수는 없습니다. 현재는 [특정 기본 제공 트리거 및 작업만](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) 관리되는 커넥터 나 연결이 아닌 관리되는 ID를 지원합니다.

* HTTP
* Azure Functions
* Azure API Management
* Azure App Services

이 문서에서는 논리 앱에 대해 두 종류의 관리되는 ID를 모두 설정하는 방법을 보여 줍니다. 자세한 내용은 다음 항목을 참조하세요.

* [관리되는 ID를 지원하는 트리거 및 작업](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [아웃바운드 호출에서 지원되는 인증 유형](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [논리 앱에 대한 관리되는 ID 제한](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [관리되는 ID를 통해 Azure AD 인증을 지원하는 Azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다. 액세스가 필요한 관리되는 ID와 액세스가 필요한 대상 Azure 리소스는 모두 동일한 Azure 구독을 사용해야 합니다.

* Azure 리소스에 대한 관리되는 ID 액세스를 제공하려면 해당 ID에 대한 대상 리소스에 역할을 추가해야 합니다. 역할을 추가하려면 해당 Azure AD 테넌트의 ID에 역할을 할당할 수 있는 [Azure AD 관리자 권한이](../active-directory/users-groups-roles/directory-assign-admin-roles.md) 필요합니다.

* 액세스하려는 대상 Azure 리소스입니다. 이 리소스에서는 논리 앱이 대상 리소스에 대한 액세스를 인증하는 데 도움이 되는 관리되는 ID에 대한 역할을 추가합니다.

* [관리되는 ID를 지원하는 트리거 또는 작업을](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) 사용하려는 논리 앱

## <a name="enable-managed-identity"></a>관리 ID 사용

사용하려는 관리되는 ID를 설정하려면 해당 ID에 대한 링크를 따르십시오.

* [시스템 할당 ID](#system-assigned)
* [사용자 할당 ID](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>시스템 할당 된 ID 를 사용

사용자가 할당한 ID와 달리 시스템 할당 ID를 수동으로 만들 필요가 없습니다. 논리 앱에 대해 시스템 할당ID를 설정하려면 다음과 같은 옵션을 사용할 수 있습니다.

* [Azure 포털](#azure-portal-system-logic-app)
* [Azure 리소스 관리자 템플릿](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Azure 포털에서 시스템 할당 ID 사용

1. [Azure Portal](https://portal.azure.com)의 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정**에서 **ID**를 선택합니다. 저장**에** >  **할당된** > 시스템을**선택합니다.** Azure에서 확인하라는 메시지가 표시되면 **예**.

   ![시스템 할당 된 ID 를 사용](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > 관리되는 단일 ID만 가질 수 있는 오류가 발생하면 논리 앱이 이미 사용자 할당ID와 연결되어 있습니다. 시스템 할당 된 ID를 추가 하려면 먼저 논리 응용 프로그램에서 사용자 할당 된 ID를 *제거* 해야 합니다.

   이제 논리 앱은 Azure Active Directory에 등록되고 개체 ID로 표시되는 시스템 할당 ID를 사용할 수 있습니다.

   ![시스템 할당 ID에 대한 개체 ID](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | 속성 | 값 | 설명 |
   |----------|-------|-------------|
   | **개체 ID** | <*ID-리소스 ID*> | Azure AD 테넌트에서 논리 앱에 대해 시스템 할당된 ID를 나타내는 전역 고유 식별자(GUID) |
   ||||

1. 이제 이 항목의 후반부에서 [해당 ID 액세스 권한을 부여하는 단계를 따릅니다.](#access-other-resources)

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿에서 시스템 할당 ID 사용

논리 앱과 같은 Azure 리소스 를 만들고 배포하는 자동화하려면 [Azure 리소스 관리자 템플릿을](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)사용할 수 있습니다. 템플릿에서 논리 앱에 대해 시스템 할당된 관리ID를 `identity` 사용하려면 `type` 템플릿의 논리 앱의 리소스 정의에 개체 및 자식 속성을 추가합니다.

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Azure에서 논리 앱 리소스 정의를 `identity` 만들면 개체는 다음과 같은 추가 속성을 가져옵니다.

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Property(JSON) | 값 | 설명 |
|-----------------|-------|-------------|
| `principalId` | <*보안 주체 ID*> | Azure AD 테넌트에서 논리 앱을 나타내는 관리되는 ID에 대한 서비스 주체 개체의 전역 고유 식별자(GUID)입니다. 이 GUID는 때때로 "개체 ID" 또는 `objectID`로 나타납니다. |
| `tenantId` | <*Azure-AD-테넌트-ID*> | 논리 앱이 이제 멤버인 Azure AD 테넌트를 나타내는 전역 고유 식별자(GUID)입니다. Azure AD 테넌트 내부에서 서비스 주체는 논리 앱 인스턴스와 이름이 같습니다. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>사용자 할당된 ID 사용

논리 앱에 대해 사용자 지정 관리 ID를 설정하려면 먼저 해당 ID를 별도의 독립 실행형 Azure 리소스로 만들어야 합니다. 사용할 수 있는 옵션은 다음과 같습니다.

* [Azure 포털](#azure-portal-user-identity)
* [Azure 리소스 관리자 템플릿](#template-user-identity)
* Azure PowerShell
  * [사용자 할당된 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [역할 할당 추가](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [사용자 할당된 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [역할 할당 추가](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [사용자 할당된 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [역할 할당 추가](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Azure 포털에서 사용자 할당된 ID 만들기

1. Azure [포털에서](https://portal.azure.com)모든 페이지의 검색 상자에 `managed identities` **관리되는 ID를**입력하고 선택합니다.

   !["관리되는 ID 찾기 및 선택"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. **관리되는 ID에서** **추가를**선택합니다.

   ![새 관리되는 ID 추가](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. 관리되는 ID에 대한 정보를 제공한 다음 에서 **"를**선택합니다.

   ![사용자 할당된 관리되는 ID 만들기](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **리소스 이름** | yes | <*사용자 할당 된 ID 이름*> | 사용자에게 할당된 ID를 지정하는 이름입니다. 이 예제에서는 "Fabrikam-user-할당된 ID"를 사용합니다. |
   | **구독** | yes | <*Azure 구독 이름*> | 사용할 Azure 구독의 이름입니다. |
   | **리소스 그룹** | yes | <*Azure-리소스 그룹 이름*> | 사용할 리소스 그룹의 이름입니다. 새 그룹을 만들거나 기존 그룹을 선택합니다. 이 예제는 "fabrikam 관리-ID-RG"라는 새 그룹을 만듭니다. |
   | **위치** | yes | <*Azure 지역*> | 리소스에 대한 정보를 저장할 Azure 지역입니다. 이 예제에서는 “미국 서부”를 사용합니다. |
   |||||

   이제 논리 앱에 사용자 할당된 ID를 추가할 수 있습니다. 논리 앱에 두 개 이상의 사용자 할당 ID를 추가할 수 없습니다.

1. Azure Portal의 Logic App Designer에서 논리 앱을 찾아서 엽니다.

1. 논리 앱 메뉴에서 **설정**에서 **ID를**선택한 다음 **할당된 사용자 할당된** > **을 선택합니다.**

   ![사용자 할당된 관리되는 ID 추가](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. **구독** 목록에서 관리되는 ID 창에 **할당된 사용자 추가에서** 아직 선택하지 않은 경우 Azure 구독을 선택합니다. 해당 구독의 *모든* 관리되는 ID를 표시하는 목록에서 원하는 사용자 할당ID를 찾아 선택합니다. 목록을 필터링하려면 User **할당된 관리되는 ID** 검색 상자에 ID 또는 리소스 그룹의 이름을 입력합니다. 작업이 완료되면 **추가 를**선택합니다.

   ![사용할 사용자 할당 ID 선택](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > 관리되는 단일 ID만 가질 수 있는 오류가 발생하면 논리 앱이 이미 시스템 할당 ID와 연결되어 있습니다. 사용자 할당된 ID를 추가하려면 먼저 논리 앱에서 시스템 할당 ID를 사용하지 않도록 설정해야 합니다.

   이제 논리 앱이 사용자 할당된 관리 ID와 연결됩니다.

   ![사용자 할당된 ID와의 연결](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. 이제 이 항목의 후반부에서 [해당 ID 액세스 권한을 부여하는 단계를 따릅니다.](#access-other-resources)

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿에서 사용자 할당된 ID 만들기

논리 앱과 같은 Azure 리소스를 만들고 배포하는 자동화하려면 [인증에 대해 사용자 할당된 ID를](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)지원하는 [Azure Resource Manager 템플릿을](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)사용할 수 있습니다. 템플릿 `resources` 섹션에서 논리 앱의 리소스 정의에는 다음 항목이 필요합니다.

* 속성이 `identity` `type` 설정된 개체`UserAssigned`

* id의 `userAssignedIdentities` 리소스 ID를 지정하는 자식 개체( 및 `clientId` 속성이 있는 다른 자식 개체) `principalId`

이 예제에서는 HTTP PUT 요청에 대한 논리 앱 리소스 정의를 `identity` 보여 주며 매개 변수가 없는 개체를 포함합니다. PUT 요청 및 후속 GET 작업에 대한 `identity` 응답에도 이 개체가 있습니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

| Property(JSON) | 값 | 설명 |
|-----------------|-------|-------------|
| `principalId` | <*보안 주체 ID*> | Azure AD 테넌트에서 사용자가 할당한 관리되는 ID에 대한 전역 고유 식별자(GUID) |
| `clientId` | <*클라이언트 ID*> | 런타임 동안 호출에 사용되는 로직 앱의 새 ID에 대한 전역 고유 식별자(GUID) |
||||

템플릿에 관리되는 ID의 리소스 정의도 포함된 경우 `identity` 개체를 매개변수화할 수 있습니다. 이 예제에서는 자식 `userAssignedIdentities` 개체가 템플릿 `userAssignedIdentity` `variables` 섹션에서 정의한 변수를 참조하는 방법을 보여 줍니다. 이 변수는 사용자가 할당한 ID에 대한 리소스 ID를 참조합니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| Property(JSON) | 값 | 설명 |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-테넌트-ID*> | 이제 사용자가 할당한 ID가 구성원인 Azure AD 테넌트를 나타내는 전역 고유 식별자(GUID)입니다. Azure AD 테넌트 내에서 서비스 주체는 사용자가 할당한 ID 이름과 이름이 같습니다. |
| `principalId` | <*보안 주체 ID*> | Azure AD 테넌트에서 사용자가 할당한 관리되는 ID에 대한 전역 고유 식별자(GUID) |
| `clientId` | <*클라이언트 ID*> | 런타임 동안 호출에 사용되는 로직 앱의 새 ID에 대한 전역 고유 식별자(GUID) |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>리소스에 대한 ID 액세스 권한 부여

로직 앱의 관리되는 ID를 인증에 사용하기 전에 ID를 사용할 Azure 리소스에서 해당 ID에 대한 액세스를 설정합니다. 이 작업을 완료하려면 대상 Azure 리소스에서 해당 ID에 적절한 역할을 할당합니다. 사용할 수 있는 옵션은 다음과 같습니다.

* [Azure 포털](#azure-portal-assign-access)
* [Azure 리소스 관리자 템플릿](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell[(New-AzRoleAssignment)](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)- 자세한 내용은 [Azure RBAC 및 Azure PowerShell을 사용하여 역할 할당 추가를](../role-based-access-control/role-assignments-powershell.md)참조하십시오.
* Azure CLI(az[역할 할당 만들기)](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)- 자세한 내용은 [Azure RBAC 및 Azure CLI를 사용하여 역할 할당 추가를](../role-based-access-control/role-assignments-cli.md)참조하세요.
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Azure 포털에서 액세스 할당

1. Azure [포털에서](https://portal.azure.com)관리되는 ID에 액세스할 Azure 리소스로 이동합니다.

1. 리소스 메뉴에서 해당 리소스에 대한 현재 역할 할당을 검토할 수 있는 **IAM(액세스 제어)** > **역할 할당을** 선택합니다. 도구 모음에서 역할 할당 **추가를** > **Add role assignment**선택합니다.

   !["역할 할당 추가" > "추가" 선택](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > 역할 **할당 추가** 옵션을 사용하지 않도록 설정하면 사용 권한이 없을 가능성이 큽니다. 리소스에 대한 역할을 관리할 수 있는 사용 권한에 대한 자세한 내용은 [Azure Active Directory의 관리자 역할 사용 권한을 참조합니다.](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

1. **역할 할당 추가에서**ID에 대상 리소스에 필요한 액세스 권한을 부여하는 **역할을** 선택합니다.

   이 항목의 예제에서는 ID에 [Azure Storage 컨테이너의 Blob에 액세스할 수 있는 역할이](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)필요합니다.

   !["스토리지 Blob 데이터 기여자" 역할 선택](./media/create-managed-service-identity/select-role-for-identity.png)

1. 관리되는 ID에 대한 다음 단계를 따르십시오.

   * **시스템 할당 ID**

     1. 상자에 **대한 액세스 할당에서** **논리 앱**을 선택합니다. **구독** 속성이 나타나면 ID와 연결된 Azure 구독을 선택합니다.

        ![시스템 할당 된 ID에 대 한 액세스 선택](./media/create-managed-service-identity/assign-access-system.png)

     1. **선택** 상자에서 목록에서 논리 앱을 선택합니다. 목록이 너무 길면 **선택** 상자를 사용하여 목록을 필터링합니다.

        ![시스템 할당 된 ID에 대 한 논리 응용 프로그램 선택](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **사용자 할당 ID**

     1. 상자에 **대한 액세스 할당에서** **사용자 할당된 관리 ID를 선택합니다.** **구독** 속성이 나타나면 ID와 연결된 Azure 구독을 선택합니다.

        ![사용자 할당된 ID에 대한 액세스 선택](./media/create-managed-service-identity/assign-access-user.png)

     1. **선택** 상자에서 목록에서 ID를 선택합니다. 목록이 너무 길면 **선택** 상자를 사용하여 목록을 필터링합니다.

        ![사용자 할당 된 ID 선택](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. 완료되면 **저장**을 선택합니다.

   대상 리소스의 역할 할당 목록에 선택한 관리되는 ID 및 역할이 표시됩니다. 이 예제에서는 하나의 논리 앱에 대해 시스템 할당 ID를 사용하고 다른 논리 앱 그룹에 대해 사용자 할당ID를 사용하는 방법을 보여 주며, 이 예제에서는

   ![대상 리소스에 관리되는 ID 및 역할 추가](./media/create-managed-service-identity/added-roles-for-identities.png)

   자세한 내용은 [Azure 포털 을 사용하여 리소스에 관리되는 ID 액세스를 할당합니다.](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)

1. 이제 관리되는 ID를 지원하는 트리거 또는 작업의 [ID로 액세스를 인증하는 단계를](#authenticate-access-with-identity) 따릅니다.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>관리되는 ID로 액세스 인증

논리 [앱에 대해 관리되는 ID를 사용하도록 설정하고](#azure-portal-system-logic-app) [대상 리소스 또는 엔터티에 해당 ID 에 대한 액세스 권한을 부여한](#access-other-resources)후 [관리되는 ID를 지원하는 트리거 및 작업에](logic-apps-securing-a-logic-app.md#managed-identity-authentication)해당 ID를 사용할 수 있습니다.

> [!IMPORTANT]
> 시스템 할당 ID를 사용하려는 Azure 함수가 있는 경우 먼저 [Azure 함수에 대한 인증을 사용하도록 설정합니다.](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)

다음 단계에서는 Azure Portal을 통해 트리거 또는 작업과 함께 관리되는 ID를 사용하는 방법을 보여 주며, 이 단계를 보여 주며, 이 단계는 관리되는 ID를 사용하는 방법을 보여 준다. 트리거 또는 작업의 기본 JSON 정의에서 관리되는 ID를 지정하려면 [관리되는 ID 인증을](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)참조하십시오.

1. Azure [포털에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 엽니다.

1. 아직 수행하지 않은 경우 [관리되는 ID를 지원하는 트리거 또는 작업을](logic-apps-securing-a-logic-app.md#managed-identity-authentication)추가합니다.

   예를 들어 HTTP 트리거 또는 작업은 논리 앱에 대해 사용하도록 설정한 시스템 할당 ID를 사용할 수 있습니다. 일반적으로 HTTP 트리거 또는 작업은 이러한 속성을 사용하여 액세스하려는 리소스 또는 엔터티를 지정합니다.

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **메서드** | yes | 실행하려는 작업에서 사용되는 HTTP 메서드 |
   | **URI** | yes | 대상 Azure 리소스 또는 엔터티에 액세스하기 위한 끝점 URL입니다. URI 구문에는 일반적으로 Azure 리소스 또는 서비스에 대한 [리소스 ID가](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) 포함됩니다. |
   | **헤더** | 예 | 콘텐츠 유형과 같이 나가는 요청에 필요하거나 포함하려는 헤더 값 |
   | **쿼리** | 예 | 특정 작업에 대한 매개 변수 또는 실행하려는 작업에 대한 API 버전과 같이 요청에 필요하거나 포함하려는 모든 쿼리 매개 변수 |
   | **인증** | yes | 대상 리소스 또는 엔터티에 대한 액세스를 인증하는 데 사용할 인증 유형 |
   ||||

   특정 예에서 이전에 ID에 대한 액세스를 설정한 Azure Storage 계정의 [Blob에서 스냅숏 Blob 작업을](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) 실행한다고 가정합니다. 그러나 [Azure Blob 저장소 커넥터는](https://docs.microsoft.com/connectors/azureblob/) 현재 이 작업을 제공하지 않습니다. 대신 [HTTP 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) 또는 다른 [Blob 서비스 REST API 작업을](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)사용 하 여이 작업을 실행할 수 있습니다.

   > [!IMPORTANT]
   > HTTP 요청 및 관리되는 ID를 사용하여 방화벽 뒤의 Azure 저장소 계정에 액세스하려면 [신뢰할 수 있는 Microsoft 서비스에서 액세스할 수 있는 경우를 제외하고](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)저장소 계정도 설정해야 합니다.

   [스냅숏 Blob 작업을](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)실행하려면 HTTP 작업은 다음 속성을 지정합니다.

   | 속성 | 필수 | 예제 값 | 설명 |
   |----------|----------|---------------|-------------|
   | **메서드** | yes | `PUT`| 스냅숏 Blob 작업에서 사용하는 HTTP 메서드 |
   | **URI** | yes | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | 이 구문을 사용하는 Azure 전역(공용) 환경에서 Azure Blob Storage 파일의 리소스 ID |
   | **헤더** | 예, Azure 저장소의 경우 | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Azure `x-ms-blob-type` `x-ms-version` 저장소 작업에 필요한 및 헤더 값입니다. <p><p>**중요**: Azure Storage에 대한 나가는 HTTP 트리거 및 `x-ms-version` 작업 요청에서 헤더에는 실행하려는 작업에 대한 속성 및 API 버전이 필요합니다. <p>자세한 내용은 다음 항목을 참조하세요. <p><p>- [헤더 요청 - 스냅샷 Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Azure 저장소 서비스에 대한 버전 관리](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **쿼리** | 예, 이 작업에 대해 | `comp` = `snapshot` | 스냅숏 Blob 작업에 대한 쿼리 매개 변수 이름 및 값입니다. |
   |||||

   다음은 이러한 모든 속성 값을 보여 주는 예제 HTTP 작업입니다.

   ![Azure 리소스에 액세스하기 위한 HTTP 작업 추가](./media/create-managed-service-identity/http-action-example.png)

1. 이제 HTTP 작업에 **인증** 속성을 추가합니다. 새 **매개 변수 추가** 목록에서 **인증을**선택합니다.

   ![HTTP 작업에 "인증" 속성 추가](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > 모든 트리거 및 작업이 인증 유형을 추가할 수 있도록 지원하지는 않습니다. 자세한 내용은 [아웃바운드 호출에 인증 추가를](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)참조하십시오.

1. 인증 **유형** 목록에서 **관리되는 ID를**선택합니다.

   !["인증"의 경우 "관리되는 ID"를 선택합니다.](./media/create-managed-service-identity/select-managed-identity.png)

1. 관리되는 ID 목록에서 시나리오에 따라 사용 가능한 옵션을 선택합니다.

   * 시스템 할당된 ID를 설정한 경우 아직 선택하지 않은 경우 **시스템 할당된 관리되는 ID를** 선택합니다.

     !["시스템 할당된 관리되는 ID" 선택](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * 사용자 할당된 ID를 설정한 경우 아직 선택하지 않은 경우 해당 ID를 선택합니다.

     ![사용자 할당된 ID 선택](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   이 예제는 **시스템 할당된 관리되는 ID를**계속 합니다.

1. 일부 트리거 및 동작에서 대상 리소스 ID를 설정하기 위해 **Audience** 속성도 나타납니다. 대상 리소스 또는 서비스에 대한 리소스 [ID로](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) **Audience** 속성을 설정합니다. 그렇지 않으면 기본적으로 **Audience** 속성은 `https://management.azure.com/` Azure 리소스 관리자의 리소스 ID인 리소스 ID를 사용합니다.

   > [!IMPORTANT]
   > 대상 리소스 ID가 필요한 후행 슬래시를 포함하여 AD(AD)가 예상하는 값과 *정확히 일치하는지* 확인합니다. 예를 들어 모든 Azure Blob Storage 계정의 리소스 ID에는 후행 슬래시가 필요합니다. 그러나 특정 저장소 계정에 대 한 리소스 ID 후행 슬래시를 필요로 하지 않습니다. Azure [AD를 지원하는 Azure 서비스에 대한 리소스 아이디를](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)확인합니다.

   이 예제는 **인증에** `https://storage.azure.com/` 사용되는 액세스 토큰이 모든 저장소 계정에 대해 유효하도록 Audience 속성을 설정합니다. 그러나 특정 저장소 계정에 대한 루트 `https://fabrikamstorageaccount.blob.core.windows.net`서비스 URL(을)을 지정할 수도 있습니다.

   !["잠재고객" 속성을 대상으로 리소스 ID로 설정](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Azure 저장소에 대한 Azure AD로 액세스 권한을 부여하는 자세한 내용은 다음 항목을 참조하십시오.

   * [Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 권한 부여](../storage/common/storage-auth-aad.md)
   * [Azure Active 디렉터리로 Azure 저장소에 대한 액세스 권한 부여](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 원하는 방식으로 논리 앱을 계속 빌드합니다.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>관리되는 ID 사용 안 함

논리 앱에 대해 관리되는 ID 사용을 중지하려면 다음 옵션이 있습니다.

* [Azure 포털](#azure-portal-disable)
* [Azure 리소스 관리자 템플릿](#template-disable)
* Azure PowerShell
  * [역할 할당 제거](../role-based-access-control/role-assignments-powershell.md)
  * [사용자 할당 된 ID 삭제](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [역할 할당 제거](../role-based-access-control/role-assignments-cli.md)
  * [사용자 할당 된 ID 삭제](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [역할 할당 제거](../role-based-access-control/role-assignments-rest.md)
  * [사용자 할당 된 ID 삭제](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

논리 앱을 삭제하면 Azure는 Azure AD에서 관리되는 ID를 자동으로 제거합니다.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Azure 포털에서 관리되는 ID를 사용하지 않도록 설정

Azure 포털에서 먼저 대상 리소스에 대한 ID의 액세스를 [제거합니다.](#disable-identity-target-resource) 다음으로 시스템 할당 ID를 끄거나 논리 앱에서 사용자 할당된 [ID를 제거합니다.](#disable-identity-logic-app)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>리소스에서 ID 액세스 제거

1. Azure [포털에서](https://portal.azure.com)관리되는 ID에 대한 액세스를 제거하려는 대상 Azure 리소스로 이동합니다.

1. 대상 리소스의 메뉴에서 **IAM(액세스 제어)을**선택합니다. 도구 모음에서 **역할 할당을 선택합니다.**

1. 역할 목록에서 제거할 관리되는 ID를 선택합니다. 도구 모음에서 **제거를**선택합니다.

   > [!TIP]
   > **제거** 옵션을 사용하지 않도록 설정한 경우 사용 권한이 없을 가능성이 큽니다. 리소스에 대한 역할을 관리할 수 있는 사용 권한에 대한 자세한 내용은 [Azure Active Directory의 관리자 역할 사용 권한을 참조합니다.](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

이제 관리되는 ID가 제거되고 더 이상 대상 리소스에 액세스할 수 없습니다.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>논리 앱에서 관리되는 ID 사용 안 함

1. [Azure Portal](https://portal.azure.com)의 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 논리 앱 메뉴에서 **설정**에서 **ID를**선택한 다음 ID에 대한 단계를 따릅니다.

   * 저장**에** >  **할당된** > 시스템을**선택합니다.** Azure에서 확인하라는 메시지가 표시되면 **예**.

     ![시스템 할당 된 ID를 사용 하지 않도록 설정](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * **할당된 사용자** 및 관리되는 ID를 선택한 다음 **제거를**선택합니다. Azure에서 확인하라는 메시지가 표시되면 **예**.

     ![사용자 할당된 ID 제거](./media/create-managed-service-identity/remove-user-assigned-identity.png)

이제 논리 앱에서 관리되는 ID가 비활성화됩니다.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿에서 관리되는 ID 를 사용하지 않도록 설정

Azure Resource Manager 템플릿을 사용하여 논리 앱의 관리되는 ID를 만든 `type` 경우 `None` `identity` 개체의 자식 속성을 로 설정합니다. 시스템 관리 ID의 경우 이 작업은 Azure AD에서 주체 ID도 삭제합니다.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>다음 단계

* [Azure 논리 앱의 보안 액세스 및 데이터](../logic-apps/logic-apps-securing-a-logic-app.md)
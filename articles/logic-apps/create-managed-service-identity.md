---
title: 관리 ID를 사용하여 인증 - Azure Logic Apps | Microsoft Docs
description: '로그인하지 않고 인증을 하려는 경우 논리 앱이 자격 증명이나 비밀 없이도 다른 Azure AD(Active Directory) 테넌트의 리소스에 액세스할 수 있도록 관리 ID(이전 명칭: MSI(관리 서비스 ID))를 만들면 됩니다.'
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 046aed64d3551d5c0b6ddae44b925452c01c297a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337598"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Azure Logic Apps에서 관리 ID로 리소스에 인증 및 액세스

논리 앱에서 로그인하지 않고도 다른 Azure AD(Active Directory) 테넌트의 리소스에 액세스하고 ID를 인증하려는 경우에는 자격 증명 또는 비밀이 아닌 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)(이전 명칭: MSI(관리 서비스 ID))를 사용할 수 있습니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다. 이 문서에서는 논리 앱용 시스템 할당 관리 ID를 설정하고 사용할 수 있는 방법을 설명합니다. 관리 ID에 대한 자세한 내용은 [Azure 리소스용 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

> [!NOTE]
> 현재는 각 Azure 구독에 시스템 할당 관리 ID를 사용하는 논리 앱 워크플로를 10개까지 포함할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독, 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험판 Azure 계정에 등록하세요</a>.

* 시스템 할당 관리 ID를 사용하려는 논리 앱. 논리 앱이 없는 경우 [첫 번째 논리 앱 워크플로 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>관리 ID 사용

시스템 할당 관리 ID의 경우 ID를 수동으로 만들 필요가 없습니다. 논리 앱에 대한 시스템 할당 관리 ID를 만들려면 다음과 같은 방법을 사용합니다. 

* [Azure Portal](#azure-portal) 
* [Azure 리소스 관리자 템플릿](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Azure portal

Azure Portal을 통해 논리 앱에 시스템 할당 관리 ID를 사용하려면 논리 앱의 ID 설정에서 **시스템 할당** 설정을 켭니다.

1. [Azure Portal](https://portal.azure.com)의 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정**에서 **ID**를 선택합니다. 

1. **시스템 할당** > **상태** 아래에서 **켜기**를 선택합니다. 그런 다음, **저장** > **예**를 선택합니다.

   ![관리 ID 설정 켜기](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   이제 논리 앱의 시스템 할당 관리 ID가 Azure Active Directory에 등록되었습니다.

   ![개체 ID에 대한 GUID](./media/create-managed-service-identity/object-id.png)

   | 자산 | 값 | 설명 | 
   |----------|-------|-------------| 
   | **개체 ID** | <*identity-resource-ID*> | Azure AD 테넌트의 논리 앱에 대한 시스템 할당 관리 ID를 나타내는 GUID(Globally Unique Identifier) | 
   ||| 

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

논리 앱과 같은 Azure 리소스의 생성 및 배포를 자동화하려는 경우 [Azure Resource Manager 템플릿](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)을 사용할 수 있습니다. 템플릿을 통해 논리 앱용 시스템 할당 관리 ID를 생성하려면 배포 템플릿의 논리 앱 워크플로 정의에 `"identity"` 요소 및 `"type"` 속성을 추가합니다. 

```json
"identity": {
   "type": "SystemAssigned"
}
```

예: 

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

Azure에서 논리 앱이 생성될 때 이 논리 앱의 워크플로 정의에는 다음과 같은 추가 속성이 포함됩니다.

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| 자산 | 값 | 설명 | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Azure AD 테넌트의 논리 앱을 나타내는 GUID(Globally Unique Identifier)이며 가끔 "개체 ID" 또는 `objectID`로 표시됨 | 
| **tenantId** | <*Azure-AD-tenant-ID*> | 논리 앱이 멤버로 속해 있는 Azure AD 테넌트를 나타내는 GUID(Globally Unique Identifier). Azure AD 테넌트 내부에서 서비스 주체는 논리 앱 인스턴스와 이름이 같습니다. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>관리 ID를 사용하여 리소스에 액세스

논리 앱용 시스템 할당 관리 ID를 만든 후에는 [다른 Azure 리소스 액세스 권한을 해당 ID에 제공](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)할 수 있습니다. 그런 후에 다른 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md)와 마찬가지로 인증에 해당 ID를 사용할 수 있습니다. 

> [!NOTE]
> 시스템 할당 관리 ID와 액세스 권한을 할당하려는 리소스의 Azure 구독은 동일해야 합니다.

### <a name="assign-access-to-managed-identity"></a>관리 ID에 액세스 권한 부여

논리 앱의 시스템 할당 관리 ID에 다른 Azure 리소스 액세스 권한을 제공하려면 다음 단계를 수행합니다.

1. Azure Portal에서 관리 ID에 액세스 권한을 할당하려는 Azure 리소스로 이동합니다. 

1. 리소스의 메뉴에서 **액세스 제어(IAM)**, **역할 할당 추가**를 차례로 선택합니다. 

   ![역할 할당 추가](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. **역할 할당 추가**에서 ID에 지정할 **역할**을 선택합니다. 

1. **액세스 권한 할당** 속성에서 아직 선택되어 있지 않으면 **Azure AD 사용자, 그룹 또는 서비스 주체**를 선택합니다.

1. **선택** 상자에서 논리 앱의 이름을 첫 글자부터 입력합니다. 논리 앱이 나타나면 선택합니다.

   ![관리 ID가 지정된 논리 앱 선택](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. 완료하면 **저장**을 선택합니다.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>논리 앱에서 관리 ID를 사용하여 인증

시스템 할당 관리 ID로 논리 앱을 설정하고 해당 ID에 원하는 리소스 액세스 권한을 할당한 후에는 인증에 해당 ID를 사용할 수 있습니다. 예를 들어 논리 앱이 HTTP 요청 또는 해당 리소스에 대한 호출을 전송할 수 있도록 HTTP 작업을 사용할 수 있습니다. 

1. 논리 앱에서 **HTTP** 작업을 추가합니다. 

1. 요청 **메서드** 및 호출할 리소스의 **URI** 위치와 같이 해당 작업에 필요한 세부 정보를 제공합니다.

   예를 들어 [Azure AD를 지원하는 이러한 Azure 서비스 중 하나](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 통해 Azure AD(Azure Active Directory) 인증을 사용한다고 가정해 봅시다. 
   **URI** 상자에 해당 Azure 서비스의 엔드포인트 URL을 입력합니다. 
   Azure Resource Manager를 사용하는 경우 **URI** 속성에 이 값을 입력합니다.

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version-2016-06-01`

1. HTTP 작업에서 **고급 옵션 표시**를 선택합니다. 

1. **인증** 목록에서 **관리 ID**를 선택합니다. 이 인증을 선택한 후에는 기본 리소스 ID 값을 사용하는 **대상** 속성이 표시됩니다.

   ![관리 ID 선택](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > **대상** 속성에서, 리소스 ID 값은 필수 후행 슬래시를 포함하여 Azure AD의 예상과 정확히 일치해야 합니다. 
   > 이러한 리소스 ID 값은 [Azure AD를 지원하는 Azure 서비스에 대해 설명하는 표](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)에서 찾을 수 있습니다. 
   > 예를 들어 Azure Resoruce Manager 리소스 ID를 사용하는 경우 URI에 후행 슬래시가 있어야 합니다.

1. 논리 앱을 원하는 방식으로 계속 빌드합니다.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>관리 ID 제거

논리 앱에서 시스템 할당 관리 ID를 사용하지 않도록 설정하려면 Azure Portal, Azure Resource Manager 배포 템플릿 또는 Azure PowerShell을 통해 ID를 설정하는 방법과 유사한 단계를 수행합니다. 

논리 앱을 삭제하면 Azure AD에서 논리 앱의 시스템 할당 ID가 자동으로 제거됩니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal을 통해 논리 앱에 대한 시스템 할당 관리 ID를 제거하려면 논리 앱의 ID 설정에서 **시스템 할당** 설정을 끕니다.

1. [Azure Portal](https://portal.azure.com)의 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정**에서 **ID**를 선택합니다. 

1. **시스템 할당** > **상태** 아래에서 **끄기**를 선택합니다. 그런 다음, **저장** > **예**를 선택합니다.

   ![관리 ID 설정 끄기](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>배포 템플릿

Azure Resource Manager 배포 템플릿으로 논리 앱의 시스템 할당 관리 ID를 만든 경우 `"identity"` 요소의 `"type"` 속성을 `"None"`으로 설정합니다. 이 작업은 Azure AD에서 보안 주체 ID도 삭제합니다. 

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.
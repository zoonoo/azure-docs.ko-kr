---
title: 로그인하지 않고 액세스 및 인증 - Azure Logic Apps | Microsoft Docs
description: 논리 앱이 자격 증명 없이 다른 Azure AD(Azure Active Directory) 테넌트의 리소스를 인증하고 액세스할 수 있도록 관리 ID를 만듭니다.
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: fb1c31e6e7c075e20191a4e51d7b1a9323f3b979
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973969"
---
# <a name="access-resources-and-authenticate-as-managed-identities-in-azure-logic-apps"></a>Azure Logic Apps에서 관리 ID로 리소스에 액세스 및 인증

로그인하지 않고 다른 Azure AD(Azure Active Directory) 테넌트의 리소스에 액세스하고 ID를 인증하려면, 논리 앱에서 자격 증명 대신 사용할 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 만들면 됩니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 대문에 자격 증명을 보호하는 데 도움이 됩니다. 이 문서에서는 논리 앱용 관리 ID를 만들고 사용하는 방법을 설명합니다. 자세한 내용은 [Azure 리소스의 ID 관리](../app-service/app-service-managed-service-identity.md)를 참조하세요.

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 대체 이름입니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독, 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험판 Azure 계정에 등록하세요</a>.

* 관리 ID를 사용하려는 논리 앱. 논리 앱이 없는 경우 [첫 번째 논리 앱 워크플로 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>관리 ID 만들기

Azure Portal, Azure Resource Manager 템플릿 또는 Azure PowerShell을 통해 논리 앱에 대한 관리 ID를 만들거나 사용하도록 설정할 수 있습니다. 

### <a name="azure-portal"></a>Azure portal

Azure Portal을 통해 논리 앱에 대한 관리 ID를 만들려면 논리 앱의 워크플로 설정에서 **Azure Active Directory에 등록** 설정을 켭니다.

1. [Azure Portal](https://portal.azure.com)의 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 다음 단계를 수행하세요. 

   1. 논리 앱 메뉴의 **설정**에서 **워크플로 설정**을 선택합니다. 

   1. **관리 서비스 ID** > 
   **Azure Active Directory에 등록**에서 **켜짐**을 선택합니다.

   1. 완료되면 도구 모음에서 **저장**을 선택합니다.

      ![관리 ID 설정 켜기](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      Azure에 논리 앱의 관리 ID에 대한 다음 속성과 값이 표시됩니다.

      ![보안 주체 ID 및 테넌트 ID에 대한 GUID](./media/create-managed-service-identity/principal-tenant-id.png)

      | 자산 | 값 | 설명 | 
      |----------|-------|-------------| 
      | **보안 주체 ID** | <*principal-ID-GUID*> | Azure AD 테넌트의 논리 앱을 나타내는 GUID(Globally Unique Identifier) | 
      | **테넌트 ID** | <*Azure-AD-tenant--ID-GUID*> | 논리 앱이 멤버로 속해 있는 Azure AD 테넌트를 나타내는 GUID(Globally Unique Identifier). Azure AD 테넌트 내부에서 서비스 주체는 논리 앱 인스턴스와 이름이 같습니다. | 
      ||| 

### <a name="deployment-template"></a>배포 템플릿

논리 앱과 같은 Azure 리소스의 생성 및 배치를 자동화하려면 Azure Resource Manager 템플릿을 설정하면 됩니다. 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 논리 앱 만들기 및 배포](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)를 참조하세요. 

템플릿을 통해 논리 앱의 관리 ID를 생성하려면 배포 템플릿의 논리 앱 워크플로 정의에 **identity** 요소 및 **type** 속성을 추가합니다. 이 설정은 Azure가 논리 앱에 대해 이 ID를 생성하고 관리한다는 것을 나타냅니다.

```json
"identity": {
    "type": "SystemAssigned"
}
```

예를 들어, 논리 앱은 다음 버전과 같을 수 있습니다.

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
    "principalId": "<principal-ID-GUID>",
    "tenantId": "<Azure-AD-tenant-ID>-GUID"
}
```

| 자산 | 값 | 설명 | 
|----------|-------|-------------|
| **principalId** | <*principal-ID-GUID*> | Azure AD 테넌트의 논리 앱을 나타내는 GUID(Globally Unique Identifier) | 
| **tenantId** | <*Azure-AD-tenant--ID-GUID*> | 논리 앱이 멤버로 속해 있는 Azure AD 테넌트를 나타내는 GUID(Globally Unique Identifier). Azure AD 테넌트 내부에서 서비스 주체는 논리 앱 인스턴스와 이름이 같습니다. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>관리 ID를 사용하여 리소스에 액세스

논리 앱에 대한 관리 ID를 만든 후에는 [다른 리소스에 해당 ID 액세스를 제공](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)할 수 있습니다. 그런 다음, 다른 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md)처럼, 인증에 관리 ID를 사용할 수 있습니다. 

예를 들어 다른 리소스에 대한 액세스 권한이 있는 관리 ID로 논리 앱을 이미 설정했다고 가정합니다. 이제 논리 앱이 HTTP 요청이나 호출을 해당 리소스에 보낼 수 있도록 HTTP 작업을 추가할 수 있습니다. 

1. 논리 앱에서 **HTTP** 작업을 추가합니다. 

1. 요청 **메서드** 및 호출할 리소스의 **URI** 위치와 같이 해당 작업에 필요한 세부 정보를 제공합니다.

1. HTTP 작업에서 **고급 옵션 표시**를 선택합니다. 

1. **인증** 목록에서 **관리 서비스 ID**를 선택하면 설정할 **대상 그룹** 속성이 표시됩니다.

   ![“관리 서비스 ID” 선택](./media/create-managed-service-identity/select-managed-service-identity.png)

1. 논리 앱을 원하는 방식으로 계속 빌드합니다.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>관리 ID 제거

논리 앱에서 관리 ID를 사용하지 않도록 설정하려면 Azure Portal, Azure Resource Manager 배포 템플릿 또는 Azure PowerShell을 통해 ID를 만든 방법과 유사한 단계를 수행합니다. 

논리 앱을 삭제하면 Azure AD에서 논리 앱의 시스템 할당 ID가 자동으로 제거됩니다.

### <a name="azure-portal"></a>Azure portal

1. Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 다음 단계를 수행하세요. 

   1. 논리 앱 메뉴의 **설정**에서 **워크플로 설정**을 선택합니다. 
   
   1. **관리 서비스 ID**에서 **Azure Active Directory에 등록** 속성에 대해 **꺼짐**을 선택합니다.

   1. 완료되면 도구 모음에서 **저장**을 선택합니다.

      ![관리 ID 설정 끄기](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>배포 템플릿

Azure Resource Manager 배포 템플릿으로 논리 앱의 관리 ID를 만든 경우 `"identity"` 요소의 `"type"` 속성을 `"None"`으로 설정합니다. 이 작업은 Azure AD에서 보안 주체 ID도 삭제합니다. 

```json
"identity": {
    "type": "None"
}
```

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)를 방문하세요.

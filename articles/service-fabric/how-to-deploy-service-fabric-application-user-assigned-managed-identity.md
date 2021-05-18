---
title: 사용자 할당 관리 ID를 사용하여 앱 배포
description: 이 문서에서는 사용자 할당 관리 ID를 사용하여 Service Fabric 애플리케이션을 배포하는 방법을 보여 줍니다.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 79d8654733b580be96d59e78f31105077929ac78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260084"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID를 사용하여 Service Fabric 애플리케이션 배포

관리 ID를 사용하여 Service Fabric 애플리케이션을 배포하려면 일반적으로 Azure Resource Manager 템플릿을 사용하여 Azure Resource Manager를 통해 애플리케이션을 배포해야 합니다. Azure Resource Manager를 통해 Service Fabric 애플리케이션을 배포하는 방법에 대한 자세한 내용은 [Azure Resource Manager 리소스로 애플리케이션 및 서비스 관리](service-fabric-application-arm-resource.md)를 참조하세요.

> [!NOTE] 
> 
> Azure 리소스로 배포되지 않은 애플리케이션은 관리 ID를 가질 수 **없습니다**. 
>
> 관리 ID를 사용한 Service Fabric 애플리케이션 배포는 API 버전 `"2019-06-01-preview"`에서 지원됩니다. 애플리케이션 유형, 애플리케이션 유형 버전 및 서비스 리소스에 대해 동일한 API 버전을 사용할 수도 있습니다.
>

## <a name="user-assigned-identity"></a>사용자가 할당한 ID

사용자 할당 ID로 애플리케이션을 사용하도록 설정하려면 먼저 **userAssigned** 형식과 참조된 사용자 할당 ID를 사용하여 애플리케이션 리소스에 **identity** 속성을 추가합니다. 그런 다음 **application** 리소스의 **properties** 섹션 내에 **managedIdentities** 섹션을 추가합니다. 여기에는 사용자가 할당한 각 ID의 principalID 매핑에 대한 식별 이름 목록이 포함됩니다. 사용자 할당 ID에 대한 자세한 내용은 [사용자 할당 관리 ID 만들기, 나열 또는 삭제](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)를 참조하세요.

### <a name="application-template"></a>애플리케이션 템플릿

사용자 할당 ID를 사용하여 애플리케이션을 사용하도록 설정하려면 먼저 애플리케이션 리소스에 **userAssigned** 형식과 참조된 사용자 할당 ID를 사용하여 **identity** 속성을 추가하고 **properties** 섹션 내에 **managedIdentities** 개체를 추가합니다. 여기에는 사용자가 할당한 각 ID의 principalId 매핑에 대한 식별 이름 목록이 포함되어 있습니다.

```json
{
  "apiVersion": "2019-06-01-preview",
  "type": "Microsoft.ServiceFabric/clusters/applications",
  "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
  ],
  "identity": {
    "type" : "userAssigned",
    "userAssignedIdentities": {
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
    }
  },
  "properties": {
    "typeName": "[parameters('applicationTypeName')]",
    "typeVersion": "[parameters('applicationTypeVersion')]",
    "parameters": {
    },
    "managedIdentities": [
      {
        "name" : "[parameters('userAssignedIdentityName')]",
        "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
      }
    ]
  }
}
```

위의 예에서 사용자 할당 ID의 리소스 이름은 애플리케이션의 관리 ID의 식별 이름으로 사용됩니다. 다음 예에서는 실제 식별 이름이 "AdminUser"라고 가정합니다.

### <a name="application-package"></a>애플리케이션 패키지

1. Azure Resource Manager 템플릿의 `managedIdentities` 섹션에 정의된 각 ID에 대해 **Principals** 섹션 아래의 애플리케이션 매니페스트에 `<ManagedIdentity>` 태그를 추가합니다. `Name` 특성은 `managedIdentities` 섹션에 정의된 `name` 속성과 일치해야 합니다.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. **ServiceManifestImport** 섹션에서 관리 ID를 사용하는 서비스에 대한 **IdentityBindingPolicy** 를 추가합니다. 이 정책은 `AdminUser` ID를 나중에 서비스 매니페스트에 추가해야 하는 서비스별 ID 이름에 매핑합니다.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. 서비스 매니페스트를 업데이트하여 애플리케이션 매니페스트의 `IdentityBindingPolicy`에서 `ServiceIdentityRef`와 일치하는 이름으로 **Resources** 섹션 내에 **ManagedIdentity** 를 추가합니다.

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>다음 단계

* [Service Fabric 애플리케이션 코드에서 관리 ID를 사용하는 방법](how-to-managed-identity-service-fabric-app-code.md)
* [다른 Azure 리소스에 대한 Service Fabric 애플리케이션 액세스 권한을 부여하는 방법](how-to-grant-access-other-resources.md)

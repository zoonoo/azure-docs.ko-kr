---
title: 사용자 할당된 관리 ID로 앱 배포
description: 이 문서에서는 사용자 지정 관리 되는 ID를 사용 하 고 Service Fabric 응용 프로그램을 배포 하는 방법을 보여 주며
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 9aef81db7a455b72c83cf96898a0c228f1c382fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415639"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>사용자 할당된 관리ID로 서비스 패브릭 응용 프로그램 배포

관리되는 ID를 사용하여 Service Fabric 응용 프로그램을 배포하려면 일반적으로 Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램을 Azure 리소스 관리자를 통해 배포해야 합니다. Azure 리소스 관리자를 통해 서비스 패브릭 응용 프로그램을 배포하는 방법에 대한 자세한 내용은 [응용 프로그램 및 서비스 관리를 Azure 리소스 관리자 리소스로 참조하세요.](service-fabric-application-arm-resource.md)

> [!NOTE] 
> 
> Azure 리소스로 배포되지 않은 응용 프로그램에는 관리되는 ID가 **있을 수 없습니다.** 
>
> 관리 되는 ID를 사용 하 고 `"2019-06-01-preview"`서비스 패브릭 응용 프로그램 배포 API 버전으로 지원 됩니다. 응용 프로그램 유형, 응용 프로그램 유형 버전 및 서비스 리소스에 동일한 API 버전을 사용할 수도 있습니다.
>

## <a name="user-assigned-identity"></a>사용자 할당 된 ID

User-할당된 ID를 사용하여 응용 프로그램을 사용하려면 먼저 **userAssigned** 형식과 참조된 사용자 할당ID를 사용하여 응용 프로그램 리소스에 **ID** 속성을 추가합니다. 그런 다음 사용자 **지정ID각각에** 대한 principalId 매핑에 친숙한 이름 목록을 포함하는 응용 **프로그램** 리소스의 **속성** 섹션 내에 managedIdentities 섹션을 추가합니다. 사용자 할당된 ID에 대한 자세한 내용은 [사용자 할당된 관리 ID 만들기, 목록 또는 삭제를](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell)참조하십시오.

### <a name="application-template"></a>애플리케이션 템플릿

사용자 할당 ID를 사용하여 응용 프로그램을 사용하려면 먼저 **userAssigned** 형식과 참조된 사용자 할당ID를 사용하여 응용 프로그램 리소스에 **ID** 속성을 추가한 다음 속성 **섹션** 내에 친숙한 이름 목록이 포함된 **managedIdentity** 개체를 principalId 매핑에 각각의 사용자 할당ID 매핑에 추가합니다.

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

위의 예제에서는 할당된 사용자의 리소스 이름이 응용 프로그램에 대해 관리되는 ID의 친숙한 이름으로 사용되고 있습니다. 다음 예제는 실제 친숙한 이름이 "AdminUser"라고 가정합니다.

### <a name="application-package"></a>애플리케이션 패키지

1. Azure Resource Manager `managedIdentities` 템플릿의 섹션에 정의된 각 `<ManagedIdentity>` ID에 대해 보안 주체 섹션 아래에 응용 프로그램 매니페스트에 태그를 **추가합니다.** 특성은 `Name` 섹션에 `name` 정의된 속성과 `managedIdentities` 일치해야 합니다.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. **ServiceManifestImport** 섹션에서 관리되는 ID를 사용하는 서비스에 대한 **ID 바인딩 정책을** 추가합니다. 이 정책은 `AdminUser` 나중에 서비스 매니페스트에 추가해야 하는 서비스 별 ID 이름에 ID를 매핑합니다.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. 서비스 매니페스트를 업데이트하여 **리소스** 섹션 내에 응용 프로그램 `ServiceIdentityRef` `IdentityBindingPolicy` 매니페스트의 이름과 일치하는 **이름의 ManagedIdentity를** 추가합니다.

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

* [서비스 패브릭 응용 프로그램 코드에서 관리되는 ID를 사용하는 방법](how-to-managed-identity-service-fabric-app-code.md)
* [다른 Azure 리소스에 대한 서비스 패브릭 응용 프로그램 액세스 권한 부여 방법](how-to-grant-access-other-resources.md)

---
title: Azure Service Fabric-사용자 할당 관리 id를 사용 하 여 응용 프로그램 배포 | Microsoft Docs
description: 이 문서에서는 사용자 할당 관리 id를 사용 하 여 Service Fabric 응용 프로그램을 배포 하는 방법을 보여 줍니다.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 08/09/2019
ms.author: atsenthi
ms.openlocfilehash: 0cc1e51a4d5f9ad54866066a4247e1588da381a6
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037499"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>사용자 할당 관리 Id를 사용 하 여 Service Fabric 응용 프로그램 배포 (미리 보기)

관리 id를 사용 하 여 Service Fabric 응용 프로그램을 배포 하려면 응용 프로그램을 일반적으로 Azure Resource Manager 템플릿이 있는 Azure Resource Manager를 통해 배포 해야 합니다. Azure Resource Manager를 통해 Service Fabric 응용 프로그램을 배포 하는 방법에 대 한 자세한 내용은 [Azure Resource Manager 리소스로 응용 프로그램 및 서비스 관리](service-fabric-application-arm-resource.md)를 참조 하세요.

> [!NOTE] 
> 
> Azure 리소스로 배포 되지 않은 응용 프로그램에는 관리 Id가 있을 **수 없습니다** . 
>
> 관리 Id를 사용 하는 Service Fabric 응용 프로그램 배포는 `"2019-06-01-preview"`API 버전에서 지원 됩니다. 응용 프로그램 유형, 응용 프로그램 유형 버전 및 서비스 리소스에 대해 동일한 API 버전을 사용할 수도 있습니다.
>

## <a name="user-assigned-identity"></a>사용자 할당 Id

사용자 할당 id를 사용 하 여 응용 프로그램을 사용 하도록 설정 하려면 먼저 **id** 속성을 **userassigned** 유형 및 참조 된 사용자 할당 id를 사용 하 여 응용 프로그램 리소스에 추가 합니다. 그런 다음, 각 사용자 할당 id의 principalId 매핑에 대 한 이름 목록을 포함 하는 **응용 프로그램** 리소스의 **속성** 섹션 내에 **managedIdentities** 섹션을 추가 합니다. 사용자 할당 Id에 대 한 자세한 내용은 [사용자 할당 관리 Id 만들기, 나열 또는 삭제를](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell)참조 하세요.

### <a name="application-template"></a>애플리케이션 템플릿

사용자 할당 id를 사용 하 여 응용 프로그램을 사용 하도록 설정 하려면 먼저 **userassigned** 형식의 응용 프로그램 리소스에 **identity** 속성을 추가 하 고 참조 된 사용자에 게 할당 된 Id를 추가한 다음 **managedIdentities** 개체 **를 속성** 섹션-사용자 할당 id에 대 한 principalId 매핑의 이름 목록을 포함 합니다.

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

위의 예제에서 사용자 할당 id의 리소스 이름은 응용 프로그램의 관리 되는 id 이름으로 사용 됩니다. 다음 예에서는 실제 이름이 "AdminUser" 라고 가정 합니다.

### <a name="application-package"></a>애플리케이션 패키지

1. Azure Resource Manager 템플릿의 `managedIdentities` 섹션에 정의 된 각 id에 대해 **보안 주체** 섹션의 `<ManagedIdentity>` 응용 프로그램 매니페스트에 태그를 추가 합니다. 특성 `Name` 은 섹션에`managedIdentities` 정의 된 `name` 속성과 일치 해야 합니다.

    **ApplicationManifest .xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. **ServiceManifestImport** 섹션에서 관리 id를 사용 하는 서비스에 대 한 **IdentityBindingPolicy** 를 추가 합니다. 이 정책은 나중에 `AdminUser` 서비스 매니페스트에 추가 해야 하는 서비스 관련 id 이름에 id를 매핑합니다.

    **ApplicationManifest .xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. 응용 프로그램 매니페스트의에서 `ServiceIdentityRef` 와 일치 `IdentityBindingPolicy` 하는 이름으로 **리소스** 섹션 내부에 **microsoft.managedidentity** 를 추가 하려면 서비스 매니페스트를 업데이트 합니다.

    **Servicemanifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>다음 단계

* [Service Fabric 응용 프로그램 코드에서 관리 Id를 사용 하는 방법](how-to-managed-identity-service-fabric-app-code.md)
* [Service Fabric 응용 프로그램에 다른 Azure 리소스에 대 한 액세스 권한을 부여 하는 방법](how-to-grant-access-other-resources.md)

---
title: 시스템 할당 MI를 사용 하 여 Service Fabric 앱 배포
description: 이 문서에서는 Azure Service Fabric 응용 프로그램에 시스템 할당 관리 id를 할당 하는 방법을 보여 줍니다.
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415648"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>시스템 할당 관리 id를 사용 하 여 Service Fabric 응용 프로그램 배포

Azure Service Fabric 응용 프로그램의 관리 되는 id 기능에 액세스 하려면 먼저 클러스터에서 관리 되는 Id 토큰 서비스를 사용 하도록 설정 해야 합니다. 이 서비스는 관리 되는 id를 사용 하 여 Service Fabric 응용 프로그램을 인증 하 고 사용자 대신 액세스 토큰을 가져오는 일을 담당 합니다. 서비스를 사용 하도록 설정 하면 왼쪽 창의 **시스템** 섹션 아래에 있는 Service Fabric Explorer에서 다른 시스템 서비스 옆의 **Fabric:/System/ManagedIdentityTokenService** 이름으로 실행 되는 것을 볼 수 있습니다.

> [!NOTE] 
> 관리 id를 사용 하 여 Service Fabric 응용 프로그램을 배포 하는 `"2019-06-01-preview"`것은 API 버전부터 지원 됩니다. 응용 프로그램 유형, 응용 프로그램 유형 버전 및 서비스 리소스에 대해 동일한 API 버전을 사용할 수도 있습니다. 지원 되는 최소 Service Fabric 런타임은 6.5 CU2입니다. Additoin에서 빌드/패키지 환경의 CU2 이상에는 SF .Net SDK가 있어야 합니다.

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

### <a name="application-template"></a>애플리케이션 템플릿

시스템 할당 관리 id를 사용 하 여 응용 프로그램을 사용 하도록 설정 하려면 아래 예제와 같이 **systemassigned** 형식이 **지정** 된 identity 속성을 응용 프로그램 리소스에 추가 합니다.

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
이 속성은 (Azure Resource Manager 및 관리 되는 Id와 Service Fabric 리소스 공급자를 각각 선언 하며,이 리소스는 암시적 (`system assigned`) 관리 id를 포함 해야 합니다.

### <a name="application-and-service-package"></a>응용 프로그램 및 서비스 패키지

1. 응용 프로그램 매니페스트를 업데이트 하 여 아래와 같이 단일 항목을 포함 하는 **보안 주체** 섹션에서 **microsoft.managedidentity** 요소를 추가 합니다.

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    응용 프로그램을 구성 하는 서비스에 대 한 추가 할당을 위해 응용 프로그램에 할당 된 id를 리소스로 리소스로 매핑합니다. 

2. 관리 id가 할당 되는 서비스에 해당 하는 **ServiceManifestImport** 섹션에서 다음과 같이 **IdentityBindingPolicy** 요소를 추가 합니다.

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    이 요소는 응용 프로그램의 id를 서비스에 할당 합니다. 이 할당을 사용 하지 않으면 서비스에서 응용 프로그램의 id에 액세스할 수 없게 됩니다. 위의 코드 조각에서 `SystemAssigned` id (예약 된 키워드)는 이름을 `WebAdmin`가진 서비스의 정의에 매핑됩니다.

3. 응용 프로그램 매니페스트의 `ServiceIdentityRef` `IdentityBindingPolicy` 정의에서 설정의 값과 일치 하는 이름을 사용 하 여 **리소스** 섹션 내에 **microsoft.managedidentity** 요소를 추가 하도록 서비스 매니페스트를 업데이트 합니다.

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    이는 위에 설명 된 대로 서비스에 대 한 id와 동등한 매핑 이며 서비스 정의의 관점에서 설명 합니다. Id는 응용 프로그램 매니페스트에 선언 된 대로 이름 (`WebAdmin`)으로 참조 됩니다.

## <a name="next-steps"></a>다음 단계
* Azure Service Fabric에서 [관리 되는 id 지원](./concepts-managed-identity.md) 검토
* [새 배포](./configure-new-azure-service-fabric-enable-managed-identity.md) 관리 되는 id를 지 원하는 Azure Service Fabric 클러스터 
* 기존 Azure Service Fabric 클러스터에서 [관리 되는 Id 사용](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [소스 코드에서](./how-to-managed-identity-service-fabric-app-code.md) Service Fabric 응용 프로그램의 관리 되는 id 활용
* [사용자 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure Service Fabric 응용 프로그램에 다른 Azure 리소스에 대 한 액세스 권한 부여](./how-to-grant-access-other-resources.md)

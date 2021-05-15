---
title: 시스템 할당 MI를 사용하여 Service Fabric 앱 배포
description: 이 문서에서는 Azure Service Fabric 애플리케이션에 시스템 할당 관리 ID를 할당하는 방법을 보여 줍니다.
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "81415648"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>시스템 할당 관리 ID를 사용하여 Service Fabric 애플리케이션 배포

Azure Service Fabric 애플리케이션의 관리 ID 기능에 액세스하려면 먼저 클러스터에서 관리 ID 토큰 서비스를 사용하도록 설정해야 합니다. 이 서비스는 관리 ID를 사용하여 Service Fabric 애플리케이션을 인증하고 사용자 대신 액세스 토큰을 가져오는 작업을 담당합니다. 서비스를 사용하도록 설정하면 왼쪽 창의 **시스템** 섹션 아래에 있는 Service Fabric Explorer에서 다른 시스템 서비스 옆에 있는 **fabric:/System/ManagedIdentityTokenService** 이름 아래에서 실행되는 것을 볼 수 있습니다.

> [!NOTE] 
> 관리 ID를 사용하여 Service Fabric 애플리케이션을 배포하는 것은 API 버전 `"2019-06-01-preview"` 이상에서 지원됩니다. 애플리케이션 유형, 애플리케이션 유형 버전, 서비스 리소스에 대해 동일한 API 버전을 사용할 수도 있습니다. 지원되는 최소 Service Fabric 런타임은 6.5 CU2입니다. 또한 빌드/패키지 환경에서는 CU2 이상의 SF .Net SDK가 있어야 합니다.

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

### <a name="application-template"></a>애플리케이션 템플릿

시스템 할당 관리 ID를 사용하여 애플리케이션을 사용하도록 설정하려면 아래 예제와 같이 **systemAssigned** 형식이 있는 **ID** 속성을 애플리케이션 리소스에 추가합니다.

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
이 속성은 해당 리소스가 암시적(`system assigned`) 관리 ID를 포함해야 한다고 Azure Resource Manager 및 관리 ID와 Service Fabric 리소스 공급자에 각각 선언합니다.

### <a name="application-and-service-package"></a>애플리케이션 및 서비스 패키지

1. 애플리케이션 매니페스트를 업데이트하여 **ManagedIdentity** 요소를 아래와 같이 단일 항목을 포함하는 **보안 주체** 섹션에 추가합니다.

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    애플리케이션을 구성하는 서비스에 대한 추가 할당을 위해 애플리케이션에 리소스로 할당된 ID를 식별 이름으로 매핑합니다. 

2. 관리 ID가 할당되는 서비스에 해당하는 **ServiceManifestImport** 섹션에서 다음과 같이 **IdentityBindingPolicy** 요소를 추가합니다.

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    해당 요소는 애플리케이션의 ID를 서비스에 할당합니다. 이렇게 할당하지 않으면 서비스에서 애플리케이션의 ID에 액세스할 수 없습니다. 위의 코드 조각에서 `SystemAssigned` ID(예약된 키워드)는 식별 이름 `WebAdmin` 아래의 서비스 정의에 매핑됩니다.

3. 서비스 매니페스트를 업데이트하여 애플리케이션 매니페스트의 `IdentityBindingPolicy` 정의의 `ServiceIdentityRef` 설정 값과 일치하는 이름이 포함된 **리소스** 섹션 내에 **ManagedIdentity** 요소를 추가합니다.

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    위에 설명된 대로 서비스에 대한 ID의 동등한 매핑이지만 서비스 정의의 관점을 보여 줍니다. 여기서 ID는 애플리케이션 매니페스트에 선언된 대로 식별 이름(`WebAdmin`)으로 참조됩니다.

## <a name="next-steps"></a>다음 단계
* Azure Service Fabric에서 [관리 ID 지원](./concepts-managed-identity.md) 검토
* 관리 ID 지원을 사용하여 [새 Azure Service Fabric 클러스터 배포](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* 기존 Azure Service Fabric 클러스터에서 [관리 ID 사용](./configure-existing-cluster-enable-managed-identity-token-service.md)
* Service Fabric 애플리케이션에서 [소스 코드의 관리 ID](./how-to-managed-identity-service-fabric-app-code.md) 활용
* [사용자 할당 관리 ID를 사용하여 Azure Service Fabric 애플리케이션 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure Service Fabric 애플리케이션에 다른 Azure 리소스에 대한 액세스 권한 부여](./how-to-grant-access-other-resources.md)

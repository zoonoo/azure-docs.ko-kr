---
title: 시스템 할당 MI를 사용 하 여 서비스 패브릭 응용 프로그램 배포
description: 이 문서에서는 Azure Service Fabric 응용 프로그램에 시스템 할당된 관리되는 ID를 할당하는 방법을 보여 주며
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415648"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>시스템 할당된 관리 ID로 서비스 패브릭 응용 프로그램 배포

Azure Service Fabric 응용 프로그램에 대한 관리되는 ID 기능에 액세스하려면 먼저 클러스터에서 관리되는 ID 토큰 서비스를 사용하도록 설정해야 합니다. 이 서비스는 관리되는 ID를 사용하여 Service Fabric 응용 프로그램의 인증을 담당하며 해당 응용 프로그램을 대신하여 액세스 토큰을 얻는 작업을 담당합니다. 서비스가 활성화되면 왼쪽 창의 **시스템** 섹션 아래의 서비스 패브릭 탐색기에서 다른 시스템 서비스 옆에 있는 **패브릭:/System/ManagedIdentityTokenService라는** 이름으로 실행되는 것을 볼 수 있습니다.

> [!NOTE] 
> 관리 ID가 있는 서비스 패브릭 응용 프로그램의 `"2019-06-01-preview"`배포는 API 버전부터 지원됩니다. 응용 프로그램 유형, 응용 프로그램 유형 버전 및 서비스 리소스에 동일한 API 버전을 사용할 수도 있습니다. 지원되는 최소 서비스 패브릭 런타임은 6.5 CU2입니다. 아디토인에서 빌드 /패키지 환경은 CU2 이상에서 SF .Net SDK가 있어야 합니다.

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

### <a name="application-template"></a>애플리케이션 템플릿

시스템 할당된 관리 ID를 사용하여 응용 프로그램을 활성화하려면 아래 예제와 같이 형식 **systemAssigned를** 사용하여 **ID** 속성을 응용 프로그램 리소스에 추가합니다.

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
이 속성은 (Azure Resource Manager 및 관리되는 ID 및 서비스 패브릭 리소스 공급자에 각각`system assigned`이 리소스에 암시적() 관리되는 ID가 있음을 선언합니다.

### <a name="application-and-service-package"></a>애플리케이션 및 서비스 패키지

1. 응용 프로그램 매니페스트를 업데이트하여 다음과 같이 단일 항목을 포함하는 보안 주체 섹션에 **ManagedIdentity** 요소를 **추가합니다.**

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    이렇게 하면 응용 프로그램에 할당된 ID가 친숙한 이름에 리소스로 매핑되어 응용 프로그램을 포함하는 서비스에 추가 할당됩니다. 

2. 관리 되는 ID를 할당 하는 서비스에 해당 하는 **ServiceManifestImport** 섹션에서 **IdBindingPolicy** 요소를 추가 합니다.

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    이 요소는 서비스에 응용 프로그램의 ID를 할당합니다. 이 할당이 없으면 서비스는 응용 프로그램의 ID에 액세스할 수 없습니다. 위의 코드 조각에서 `SystemAssigned` ID(예약된 키워드)는 친숙한 이름으로 `WebAdmin`서비스의 정의에 매핑됩니다.

3. 서비스 매니페스트를 업데이트하여 **리소스** 섹션 내에 Application 매니페스트의 `ServiceIdentityRef` `IdentityBindingPolicy` 정의에서 설정 값과 일치하는 이름으로 **ManagedIdentity** 요소를 추가합니다.

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    이는 위에서 설명한 대로 ID를 서비스에 매핑하는 것과 동일하지만 서비스 정의의 관점에서 볼 때와 동일합니다. ID는 응용 프로그램 매니페스트에`WebAdmin`선언된 대로 친숙한 이름() 으로 참조됩니다.

## <a name="next-steps"></a>다음 단계
* Azure 서비스 패브릭에서 [관리되는 ID 지원](./concepts-managed-identity.md) 검토
* [새 배포](./configure-new-azure-service-fabric-enable-managed-identity.md) 관리되는 ID 지원을 갖춘 Azure 서비스 패브릭 클러스터 
* 기존 Azure 서비스 패브릭 클러스터에서 [관리되는 ID 를 사용하도록 설정합니다.](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [소스 코드에서](./how-to-managed-identity-service-fabric-app-code.md) 서비스 패브릭 응용 프로그램의 관리ID 활용
* [사용자 할당된 관리되는 ID를 사용 하 고 Azure 서비스 패브릭 응용 프로그램 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure 서비스 패브릭 응용 프로그램 액세스 권한을 다른 Azure 리소스에 부여합니다.](./how-to-grant-access-other-resources.md)

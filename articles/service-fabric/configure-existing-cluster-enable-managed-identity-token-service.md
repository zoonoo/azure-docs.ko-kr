---
title: 기존 Service Fabric 클러스터에서 관리 ID 지원 구성
description: 기존 Azure Service Fabric 클러스터에서 관리 ID 지원을 사용 하도록 설정하는 방법은 다음과 같습니다
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: dc341688cae6d98f53be10351e4e4572a3539e4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790042"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>기존 Service Fabric 클러스터에서 관리 ID 지원 구성

Service Fabric 애플리케이션에서 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하려면 먼저 클러스터에서 *관리 ID 토큰 서비스* 를 사용하도록 설정합니다. 이 서비스는 관리 ID를 사용하여 Service Fabric 애플리케이션을 인증하고 사용자 대신 액세스 토큰을 가져오는 작업을 담당합니다. 서비스를 사용하면, 왼쪽 창에 있는 **시스템** 섹션에서 Service Fabric Explorer에서 해당 서비스를 볼 수 있으며, **fabric:/System/ManagedIdentityTokenService** 라는 이름으로 실행됩니다.

> [!NOTE]
> **관리 ID 토큰 서비스** 를 사용하려면 Service Fabric 런타임 버전 6.5.658.9590 이상이 필요합니다.  
>
> Azure Portal에서 클러스터 리소스를 열고 **Essentials** 섹션에서 **Service Fabric 버전** 속성을 확인하여 클러스터의 Service Fabric 버전을 찾을 수 있습니다.
>
> 클러스터가 **수동** 업그레이드 모드에 있는 경우, 먼저 6.5.658.9590 이상으로 업그레이드해야 합니다.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>기존 클러스터에서 *관리 ID 토큰 서비스* 사용

기존 클러스터에서 관리 ID 토큰 서비스를 사용하려면, 다음의 두 가지 변경 사항을 지정하여 클러스터 업그레이드를 시작해야 합니다. (1) 관리 ID 토큰 서비스 사용, (2) 각 노드를 다시 시작 하도록 요청. 먼저 클러스터 Azure Resource Manager 템플릿에 다음 코드 조각을 추가합니다.

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

변경 내용의 효력이 발생하려면 업그레이드가 클러스터를 진행하면서 각 노드에서 Service Fabric 런타임의 강제 다시 시작을 지정하도록 업그레이드 정책을 변경해야 합니다. 이렇게 다시 시작하면 새로 활성화된 시스템 서비스가 각 노드에서 시작되고 실행됩니다. 아래 코드 조각에서는 `forceRestart`이 다시 시작을 사용을 위한 필수 설정입니다. 나머지 매개 변수의 경우 아래에서 설명하는 값을 사용하거나 클러스터 리소스에 이미 지정된 기존 사용자 지정 값을 사용 합니다. 패브릭 업그레이드 정책(‘upgradeDescription’)에 대 한 사용자 지정 설정은 Service Fabric 리소스 또는 resources.azure.com에서 '패브릭 업그레이드' 옵션을 선택하여 Azure Portal에서 볼 수 있습니다. 업그레이드 정책에 대한 기본 옵션('upgradeDescription')은 powershell 또는 resources.azure.com에서 볼 수 없습니다. 자세한 내용은 [ClusterUpgradePolicy](/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy)를 참조하세요.  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> 업그레이드가 성공적으로 완료되면 이후 업그레이드의 영향을 최소화하기 위해 `forceRestart` 설정을 롤백하는 것을 잊지 마세요. 

## <a name="errors-and-troubleshooting"></a>오류 및 문제 해결

다음 메시지와 함께 배포가 실패 하는 경우 클러스터가 충분한 Service Fabric 버전에서 실행되지 않고 있다는 것을 의미합니다.

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>다음 단계
* [시스템 할당 관리 ID를 사용하여 Azure Service Fabric 애플리케이션 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [사용자 할당 관리 ID를 사용하여 Azure Service Fabric 애플리케이션 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [서비스 코드에서 Service Fabric 애플리케이션의 관리 ID 활용](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric 애플리케이션에 다른 Azure 리소스에 대한 액세스 권한 부여](./how-to-grant-access-other-resources.md)

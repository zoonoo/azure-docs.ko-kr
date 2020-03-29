---
title: 기존 서비스 패브릭 클러스터에서 관리되는 ID 지원 구성
description: 기존 Azure 서비스 패브릭 클러스터에서 관리되는 ID 지원을 활성화하는 방법은 다음과 같습니다.
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: cb6e4ab00afd80cba41881e46296f7046a905919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934956"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>기존 서비스 패브릭 클러스터에서 관리되는 ID 지원 구성(미리 보기)

Service Fabric 응용 프로그램에서 [Azure 리소스에 대해 관리되는 ID를](../active-directory/managed-identities-azure-resources/overview.md) 사용하려면 먼저 클러스터에서 *관리되는 ID 토큰 서비스를* 사용하도록 설정합니다. 이 서비스는 관리되는 ID를 사용하여 Service Fabric 응용 프로그램의 인증을 담당하며 해당 응용 프로그램을 대신하여 액세스 토큰을 얻는 작업을 담당합니다. 서비스가 활성화되면 왼쪽 창의 **시스템** 섹션 아래의 서비스 패브릭 탐색기에서 이름 **패브릭:/System/ManagedIdentityTokenService**에서 실행되는 것을 볼 수 있습니다.

> [!NOTE]
> 서비스 패브릭 런타임 버전 6.5.658.9590 이상은 **관리되는 ID 토큰 서비스를**활성화하는 데 필요합니다.  
>
> 클러스터 리소스를 열고 Essentials 섹션에서 Service Fabric 버전 속성을 확인하여 Azure 포털에서 **클러스터의 서비스 패브릭 버전을** 찾을 수 **있습니다.**
>
> 클러스터가 **수동** 업그레이드 모드에 있는 경우 먼저 클러스터를 6.5.658.9590 이상으로 업그레이드해야 합니다.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>기존 클러스터에서 *관리되는 ID 토큰 서비스* 사용

기존 클러스터에서 관리되는 ID 토큰 서비스를 활성화하려면 (1) 관리되는 ID 토큰 서비스 사용 및 (2) 각 노드의 재시작을 요청하는 두 가지 변경 사항을 지정하는 클러스터 업그레이드를 시작해야 합니다. 먼저 클러스터 Azure 리소스 관리자 템플릿을 다음 코드 조각을 추가합니다.

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

변경 내용이 적용되도록 업그레이드 정책을 변경하여 업그레이드가 클러스터를 통해 진행됨에 따라 각 노드에서 Service Fabric 런타임을 강제로 다시 시작하도록 지정해야 합니다. 이렇게 다시 시작하면 각 노드에서 새로 활성화된 시스템 서비스가 시작되고 실행됩니다. 아래 스니펫에서 `forceRestart` 필수 설정입니다. 설정의 나머지 부분에 대한 기존 값을 사용합니다.  

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
> 업그레이드가 성공적으로 완료되면 `forceRestart` 설정을 롤백하여 후속 업그레이드의 영향을 최소화하는 것을 잊지 마십시오. 

## <a name="errors-and-troubleshooting"></a>오류 및 문제 해결

다음 메시지로 배포에 실패하면 클러스터가 충분히 높은 Service Fabric 버전에서 실행되고 있지 않음을 의미합니다.

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>다음 단계
* [시스템 할당된 관리되는 ID를 사용 하 고 Azure 서비스 패브릭 응용 프로그램 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [사용자 할당된 관리되는 ID를 사용 하 고 Azure 서비스 패브릭 응용 프로그램 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [서비스 코드에서 서비스 패브릭 응용 프로그램의 관리되는 ID 활용](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure 서비스 패브릭 응용 프로그램 액세스 권한을 다른 Azure 리소스에 부여합니다.](./how-to-grant-access-other-resources.md)
---
title: Azure Service Fabric-관리 되는 id 지원을 사용 하도록 기존 Azure Service Fabric 클러스터 구성 | Microsoft Docs
description: 이 문서에서는 관리 되는 id에 대 한 지원을 사용 하도록 기존 Azure Service Fabric 클러스터를 구성 하는 방법을 보여 줍니다.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: dc9d7ba9499f2c98370b14fd88d38dffc65480e5
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68964024"
---
# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support"></a>관리 되는 Id 지원을 사용 하도록 기존 Azure Service Fabric 클러스터 구성
Azure Service Fabric 응용 프로그램의 관리 되는 id 기능에 액세스 하려면 먼저 클러스터에서 **관리 되는 Id 토큰 서비스** 를 사용 하도록 설정 해야 합니다. 이 서비스는 관리 되는 id를 사용 하 여 Service Fabric 응용 프로그램을 인증 하 고 사용자 대신 액세스 토큰을 가져오는 일을 담당 합니다. 서비스를 사용 하도록 설정 하면 왼쪽 창에 있는 **시스템** 섹션의 이름 **Fabric:/System/ManagedIdentityTokenService**에서 실행 되는 Service Fabric Explorer에서 해당 서비스를 볼 수 있습니다.

> [!NOTE]
> **관리 되는 Id 토큰 서비스**를 사용 하려면 Service Fabric runtime version 6.5.658.9590 이상이 필요 합니다.  
> 
> 클러스터 리소스를 열고 **Essentials** 섹션에서 **Service Fabric 버전** 속성을 확인 하 여 Azure Portal에서 클러스터의 Service Fabric 버전을 찾을 수 있습니다.
> 
> 클러스터가 **수동** 업그레이드 모드에 있는 경우 먼저 6.5.658.9590 이상으로 업그레이드 해야 합니다.


## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>기존 클러스터에서 관리 되는 Id 토큰 서비스를 사용 하도록 설정
기존 클러스터에서 관리 되는 Id 토큰 서비스를 사용 하려면 관리 되는 Id 토큰 서비스를 사용 하도록 설정 하 고 각 노드의 다시 시작을 요청 하는 두 가지 변경 사항을 지정 하 여 클러스터 업그레이드를 시작 해야 합니다. 이렇게 하려면 Azure Resource Manager 템플릿에 다음 두 코드 조각을 추가 합니다.

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

변경 내용을 적용 하려면 업그레이드가 클러스터를 진행 하면서 각 노드에서 Service Fabric 런타임의 강제 다시 시작을 지정 하도록 업그레이드 정책을 변경 해야 합니다. 이렇게 다시 시작 하면 새로 활성화 된 시스템 서비스가 각 노드에서 시작 되 고 실행 됩니다. 아래 코드 조각에서 `forceRestart` 는 필수 설정입니다. 나머지 설정에 대해 기존 값을 사용 합니다.  

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
> 업그레이드가 성공적으로 완료 되 면 이후 업그레이드의 영향을 최소화 하기 위해 `forceRestart` 설정을 롤백하는 것을 잊지 마세요. 

## <a name="errors-and-troubleshooting"></a>오류 및 문제 해결

다음 메시지와 함께 배포가 실패 하는 경우 클러스터가 충분 한 Service Fabric 버전에서 실행 되 고 있지 않음을 의미 합니다.

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>다음 단계
* [시스템 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [사용자 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [서비스 코드에서 Service Fabric 응용 프로그램의 관리 되는 id 활용](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric 응용 프로그램에 다른 Azure 리소스에 대 한 액세스 권한 부여](./how-to-grant-access-other-resources.md)

## <a name="related-articles"></a>관련 문서
* Azure Service Fabric에서 [관리 되는 id 지원](./concepts-managed-identity.md) 검토

* [기존 Azure Service Fabric 클러스터에서 관리 되는 id 지원 사용](./configure-existing-cluster-enable-managed-identity-token-service.md)

---
title: 새 Service Fabric 클러스터에 대한 관리 ID 지원 구성
description: 새 Azure Service Fabric 클러스터에서 관리 ID 지원을 사용하도록 설정하는 방법은 다음과 같습니다.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: ae4fec452a2342a68843d874ba955b594014c46d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574669"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>새 Service Fabric 클러스터에 대한 관리 ID 지원 구성

Service Fabric 애플리케이션에서 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하려면 먼저 클러스터에서 *관리 ID 토큰 서비스* 를 사용하도록 설정합니다. 이 서비스는 관리 ID를 사용하여 Service Fabric 애플리케이션을 인증하고 사용자 대신 액세스 토큰을 가져오는 작업을 담당합니다. 서비스를 사용하도록 설정하면 왼쪽 창의 **시스템** 섹션 아래에 있는 Service Fabric Explorer에서 다른 시스템 서비스 옆에 있는 **fabric:/System/ManagedIdentityTokenService** 라는 이름으로 실행되는 것을 볼 수 있습니다.

> [!NOTE]
> **관리 ID 토큰 서비스** 를 사용하려면 Service Fabric 런타임 버전 6.5.658.9590 이상이 필요합니다.  

## <a name="enable-the-managed-identity-token-service"></a>관리 ID 토큰 서비스 사용

클러스터 생성 시 관리 ID 토큰 서비스를 사용하도록 설정하려면 클러스터 Azure Resource Manager 템플릿에 다음 코드 조각을 추가합니다.

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

## <a name="errors"></a>오류

이 메시지와 함께 배포에 실패하면 클러스터가 필요한 Service Fabric 버전에 있지 않음을 의미 합니다(지원되는 최소 런타임은 6.5 CU2).


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>관련 문서

* Azure Service Fabric에서 [관리 ID 지원](./concepts-managed-identity.md) 검토

* [기존 Azure Service Fabric 클러스터에서 관리 ID 지원 사용](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>다음 단계

* [시스템 할당 관리 ID를 사용하여 Azure Service Fabric 애플리케이션 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [사용자 할당 관리 ID를 사용하여 Azure Service Fabric 애플리케이션 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [서비스 코드에서 Service Fabric 애플리케이션의 관리 ID 활용](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric 애플리케이션에 다른 Azure 리소스에 대한 액세스 권한 부여](./how-to-grant-access-other-resources.md)
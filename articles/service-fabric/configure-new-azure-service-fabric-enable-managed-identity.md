---
title: 새 서비스 패브릭 클러스터에 대한 관리되는 ID 지원 구성
description: 새 Azure 서비스 패브릭 클러스터에서 관리되는 ID 지원을 활성화하는 방법은 다음과 같습니다.
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 0e35d2192fdcdb294b349105f3f0158564cec86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76930471"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster-preview"></a>새 서비스 패브릭 클러스터에 대한 관리되는 ID 지원 구성(미리 보기)

Service Fabric 응용 프로그램에서 [Azure 리소스에 대해 관리되는 ID를](../active-directory/managed-identities-azure-resources/overview.md) 사용하려면 먼저 클러스터에서 *관리되는 ID 토큰 서비스를* 사용하도록 설정합니다. 이 서비스는 관리되는 ID를 사용하여 Service Fabric 응용 프로그램의 인증을 담당하며 해당 응용 프로그램을 대신하여 액세스 토큰을 얻는 작업을 담당합니다. 서비스가 활성화되면 왼쪽 창의 **시스템** 섹션 아래의 서비스 패브릭 탐색기에서 다른 시스템 서비스 옆에 있는 **패브릭:/System/ManagedIdentityTokenService라는** 이름으로 실행되는 것을 볼 수 있습니다.

> [!NOTE]
> 서비스 패브릭 런타임 버전 6.5.658.9590 이상은 **관리되는 ID 토큰 서비스를**활성화하는 데 필요합니다.  

## <a name="enable-the-managed-identity-token-service"></a>관리되는 ID 토큰 서비스 사용

클러스터 생성 시 관리되는 ID 토큰 서비스를 활성화하려면 클러스터 Azure 리소스 관리자 템플릿에 다음 코드 조각을 추가합니다.

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

이 메시지로 배포에 실패하면 클러스터가 필요한 Service Fabric 버전에 없음을 의미합니다(지원되는 최소 런타임은 6.5 CU2입니다).


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>관련 문서

* Azure 서비스 패브릭에서 [관리되는 ID 지원](./concepts-managed-identity.md) 검토

* [기존 Azure 서비스 패브릭 클러스터에서 관리되는 ID 지원 활성화](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>다음 단계

* [시스템 할당된 관리되는 ID를 사용 하 고 Azure 서비스 패브릭 응용 프로그램 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [사용자 할당된 관리되는 ID를 사용 하 고 Azure 서비스 패브릭 응용 프로그램 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [서비스 코드에서 서비스 패브릭 응용 프로그램의 관리되는 ID 활용](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure 서비스 패브릭 응용 프로그램 액세스 권한을 다른 Azure 리소스에 부여합니다.](./how-to-grant-access-other-resources.md)
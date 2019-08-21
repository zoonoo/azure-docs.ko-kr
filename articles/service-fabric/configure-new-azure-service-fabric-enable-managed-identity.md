---
title: Azure Service Fabric-관리 되는 Id를 지 원하는 새 Azure Service Fabric 클러스터 배포 | Microsoft Docs
description: 이 문서에서는 관리 되는 Id를 사용 하 여 새 Service Fabric 클러스터를 만드는 방법을 보여 줍니다.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: cb32251c2507f3def7f5e830af15828ff2f04c29
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640715"
---
# <a name="create-a-new-azure-service-fabric-cluster-with-managed-identity-support-preview"></a>관리 Id 지원 (미리 보기)을 사용 하 여 새 Azure Service Fabric 클러스터 만들기

Azure Service Fabric 응용 프로그램의 관리 되는 id 기능에 액세스 하려면 먼저 클러스터에서 관리 되는 Id 토큰 서비스를 사용 하도록 설정 해야 합니다. 이 서비스는 관리 되는 id를 사용 하 여 Service Fabric 응용 프로그램을 인증 하 고 사용자 대신 액세스 토큰을 가져오는 일을 담당 합니다. 서비스를 사용 하도록 설정 하면 왼쪽 창의 **시스템** 섹션 아래에 있는 Service Fabric Explorer에서 다른 시스템 서비스 옆의 **Fabric:/System/ManagedIdentityTokenService** 이름으로 실행 되는 것을 볼 수 있습니다.

> [!NOTE]
> **관리 되는 Id 토큰 서비스**를 사용 하려면 Service Fabric runtime version 6.5.658.9590 이상이 필요 합니다.  

## <a name="enable-the-managed-identity-token-service"></a>관리 되는 Id 토큰 서비스 사용 
클러스터를 만들 때 관리 되는 Id 토큰 서비스를 사용 하도록 설정 하려면 Azure Resource Manager 템플릿에서 다음 코드 조각을 사용할 수 있습니다.

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

이 메시지와 함께 배포가 실패 하면 클러스터가 필요한 Service Fabric 버전에 있지 않음을 의미 합니다 (지원 되는 최소 런타임은 6.5 CU2).



```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```


## <a name="related-articles"></a>관련 문서
* Azure Service Fabric에서 [관리 되는 id 지원](./concepts-managed-identity.md) 검토

* [기존 Azure Service Fabric 클러스터에서 관리 되는 id 지원 사용](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>다음 단계
* [시스템 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [사용자 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [서비스 코드에서 Service Fabric 응용 프로그램의 관리 되는 id 활용](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric 응용 프로그램에 다른 Azure 리소스에 대 한 액세스 권한 부여](./how-to-grant-access-other-resources.md)
---
title: Azure 가상 네트워크 서비스 엔드포인트 정책 | Microsoft Docs
description: 서비스 엔드포인트 정책을 사용하여 Virtual Network 트래픽을 Azure 서비스 리소스로 필터링하는 방법 알아보기
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 633d0b9e454fe2906d98624ac2cdbcc9a8b6c7fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022265"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Azure Storage에 대한 가상 네트워크 서비스 엔드포인트 정책

가상 네트워크(VNet) 서비스 엔드포인트 정책을 사용하면 Azure Storage 계정에 대한 송신 가상 네트워크 트래픽을 필터링하고 특정 Azure Storage 계정에만 데이터 반출을 허용할 수 있습니다. 엔드포인트 정책은 서비스 엔드포인트를 통해 연결할 때 Azure Storage의 가상 네트워크 트래픽에 대한 세부적인 액세스 제어를 제공합니다.

![Azure Storage 계정에 대한 가상 네트워크 아웃바운드 트래픽 보안](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

이 기능은 일반적으로 __모든 글로벌 Azure 지역__ 의 __Azure Storage__ 에 사용할 수 있습니다.

## <a name="key-benefits"></a>주요 이점

가상 네트워크 서비스 엔드포인트 정책은 다음과 같은 이점을 제공합니다.

- __Azure Storage에 대한 Virtual Network 트래픽의 보안 향상__

  [네트워크 보안 그룹에 대한 Azure 서비스 태그](./network-security-groups-overview.md)를 사용하면 가상 네트워크 아웃바운드 트래픽을 특정Azure Storage 지역으로 제한할 수 있습니다. 그러나 이렇게 하면 선택한 Azure Storage 지역 내의 모든 계정에 대한 트래픽이 허용됩니다.
  
  엔드포인트 정책을 사용하면 가상 네트워크 아웃바운드 액세스가 허용된 Azure Storage 계정을 지정하고 다른 모든 스토리지 계정에 대한 액세스를 제한할 수 있습니다. 이렇게 하면 가상 네트워크에서 데이터 반출을 보호하기 위해 더 세부적인 보안 제어가 가능합니다.

- __Azure 서비스 트래픽을 필터링하기 위한 확장성 및 고가용성 정책__

   엔드포인트 정책은 서비스 엔드포인트를 통한 가상 네트워크로부터의 Azure 서비스 트래픽을 필터링하기 위해 수평적으로 확장 가능하며 가용성이 높은 솔루션을 제공합니다. 추가 오버헤드 없이 가상 네트워크에서 이 트래픽에 대한 중앙 네트워크 어플라이언스를 유지 관리할 수 있습니다.

## <a name="json-object-for-service-endpoint-policies"></a>서비스 엔드포인트 정책에 대한 JSON 개체
서비스 엔드포인트 정책 개체를 간략히 살펴보겠습니다.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>구성

-   가상 네트워크 트래픽을 특정 Azure Storage 계정으로 제한하는 엔드포인트 정책을 구성할 수 있습니다.
-   엔드포인트 정책은 가상 네트워크의 서브넷에 구성됩니다. 정책을 적용하려면 서브넷에서 Azure Storage의 서비스 엔드포인트를 사용하도록 설정해야 합니다.
-   엔드포인트 정책을 사용하면 resourceID 형식을 사용하여 허용 목록에 특정 Azure Storage 계정을 추가할 수 있습니다. 다음 항목에 대한 액세스를 제한할 수 있습니다.
    - 구독 중인 모든 스토리지 계정<br>
      `E.g. /subscriptions/subscriptionId`

    - 리소스 그룹의 모든 스토리지 계정<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - 해당 Azure Resource Manager resourceId를 나열하여 개별 스토리지 계정. Blob, 테이블, 큐, 파일 및 Azure Data Lake Storage Gen2 트래픽에 적용됩니다. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   기본적으로 엔드포인트가 있는 서브넷에 정책이 연결되지 않은 경우 서비스의 모든 스토리지 계정에 액세스할 수 있습니다. 정책을 해당 서브넷에 구성한 후에는 정책에서 지정한 리소스만 해당 서브넷의 컴퓨팅 인스턴스에서 액세스할 수 있습니다. 다른 모든 스토리지 계정에 대한 액세스는 거부됩니다.
-   서브넷에서 서비스 엔드포인트 정책을 적용할 때 Azure Storage *서비스 엔드포인트 범위* 가 지역에서 **글로벌** 로 업그레이드됩니다. 즉, Azure Storage에 대한 모든 트래픽은 이후에 서비스 엔드포인트를 통해 보호됩니다. 서비스 엔드포인트 정책은 전역적으로 적용될 수도 있으므로 명시적으로 허용되지 않는 모든 스토리지 계정은 액세스 거부됩니다. 
-   서브넷에 여러 정책을 적용할 수 있습니다. 여러 정책을 서브넷에 연결할 때는 이 정책 간에 지정한 리소스로의 가상 네트워크 트래픽이 허용됩니다. 정책에서 지정하지 않은 모든 다른 서비스 리소스에 대한 액세스는 거부됩니다.

    > [!NOTE]  
    > 서비스 엔드포인트 정책은 **허용 정책** 이므로 지정된 리소스 외의 다른 모든 리소스는 제한됩니다. 애플리케이션에 대한 모든 서비스 리소스 종속성이 정책에서 식별 및 나열되었는지 확인합니다.

- Azure Resource Model을 사용하는 스토리지 계정만 엔드포인트 정책에서 지정할 수 있습니다. 클래식 Azure Storage 계정은 Azure 서비스 엔드포인트 정책을 지원하지 않습니다.
- 기본 계정이 나열되면 RA-GRS 보조 액세스가 자동으로 허용됩니다.
- 스토리지 계정은 가상 네트워크와 동일하거나 다른 구독 또는 Azure Active Directory 테넌트에 있을 수 있습니다.

## <a name="scenarios"></a>시나리오

- **피어링, 연결 또는 다중 가상 네트워크**: 피어링된 가상 네트워크에서 트래픽을 필터링하려면 엔드포인트 정책을 개별적으로 해당 가상 네트워크에 적용해야 합니다.
- **네트워크 어플라이언스 또는 Azure Firewall을 사용하여 인터넷 트래픽 필터링**: 서비스 엔드포인트에서 정책을 통해 Azure 서비스 트래픽을 필터링하고, 나머지 인터넷 또는 Azure 트래픽을 어플라이언스나 Azure Firewall을 통해 필터링합니다.
- **가상 네트워크에 배포된 Azure 서비스에서 트래픽 필터링**: 현재 가상 네트워크에 배포된 관리 Azure 서비스에 대해 Azure 서비스 엔드포인트 정책이 지원되지 않습니다. 
- **온-프레미스에서 Azure 서비스로 트래픽 필터링**: 서비스 엔드포인트 정책은 정책과 연결된 서브넷으로부터의 트래픽에만 적용됩니다. 온-프레미스로부터의 특정 Azure 서비스 액세스를 허용하려면 트래픽을 네트워크 가상 어플라이언스나 방화벽을 통해 필터링해야 합니다.

## <a name="logging-and-troubleshooting"></a>로깅 및 문제 해결
서비스 엔드포인트 정책에 중앙 집중식 로깅을 사용할 수 없습니다. 서비스 리소스 로그는 [서비스 엔드포인트 로깅](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting)을 참조하세요.

### <a name="troubleshooting-scenarios"></a>문제 해결 시나리오
- 미리 보기 상태로 작동하는 스토리지 계정에 대해 액세스 거부(지리적으로 페어링된 지역이 아님)
  - Azure Storage를 업그레이드하여 전역 서비스 태그를 사용하는 경우 서비스 엔드포인트의 범위와 서비스 엔드포인트 정책이 이제 전역으로 적용됩니다. 따라서 Azure Storage에 대한 트래픽은 서비스 엔드포인트를 통해 암호화되며 정책에 명시적으로 나열된 스토리지 계정만 액세스할 수 있습니다.
  - 모든 필수 스토리지 계정을 명시적으로 나열할 수 있도록 하여 액세스를 복원합니다.  
  - Azure 지원에 문의하세요.
- 엔드포인트 정책에 나열된 계정에 대한 액세스 거부
  - 네트워크 보안 그룹 또는 방화벽 필터링이 액세스를 차단할 수 있음
  - 정책 제거/다시 적용 후 연결이 끊어짐
    - Azure 서비스가 가상 네트워크로부터 또는 엔드포인트를 경유한 액세스를 구성하도록 허용되었거나 리소스 기본 정책이 *모두 허용* 으로 구성되었는지 유효성을 검사합니다.
    - 서비스 진단에서 엔드포인트를 통한 트래픽이 표시되는지 확인합니다.
    - 네트워크 보안 그룹 흐름 로그에 액세스가 표시되며 스토리지 로그에 서비스 엔드포인트를 통해 예상대로 액세스가 표시되는지 확인합니다.
    - Azure 지원에 문의하세요.
- 서비스 엔드포인트 정책에 나열되지 않은 계정에 대한 액세스 거부
  - Azure Storage가 가상 네트워크로부터 또는 엔드포인트를 경유한 액세스를 구성하도록 허용되었거나 리소스 기본 정책이 *모두 허용* 으로 구성되었는지 유효성을 검사합니다.
  - 계정이 서브넷에서 서비스 엔드포인트 정책을 사용하는 **클래식 스토리지 계정** 이 아닌지 확인합니다.
- 서브넷을 통해 서비스 엔드포인트 정책을 적용한 후 관리되는 Azure 서비스의 작동이 중지됨
  - 지금은 서비스 엔드포인트 정책에서 관리 서비스를 지원하지 않습니다. *이 공간의 업데이트를 보세요*.
- 서브넷을 통해 서비스 엔드포인트 정책을 적용한 후 관리되는 스토리지 계정에 대한 액세스가 작동 중지됨
  - 관리되는 스토리지 계정은 서비스 엔드포인트 정책에서 지원되지 않습니다. 구성된 경우 정책에서는 기본적으로 모든 관리 스토리지 계정에 대한 액세스를 거부합니다. 애플리케이션이 관리형 스토리지 계정에 액세스해야 하는 경우 해당 트래픽에 엔드포인트 정책을 사용해서는 안 됩니다.

## <a name="provisioning"></a>프로비전

가상 네트워크에 대한 쓰기 액세스 권한이 있는 사용자는 서브넷에서 서비스 엔드포인트 정책을 구성할 수 있습니다. Azure [기본 제공 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 특정 권한 할당에 대해 자세히 알아보세요.

가상 네트워크 및 Azure Storage 계정이 동일한 구독이나 다른 구독 또는 Azure Active Directory 테넌트에 있을 수 있습니다.

## <a name="limitations"></a>제한 사항

- Azure Resource Manager 배포 모델을 통해 배포된 가상 네트워크에만 서비스 엔드포인트 정책을 배포할 수 있습니다.
- 가상 네트워크는 서비스 엔드포인트 정책은 동일한 지역에 있어야 합니다.
- 서비스 엔드포인트가 정책에 나열된 Azure 서비스에 대해 구성된 경우에만 서브넷에 서비스 엔드포인트 정책을 적용할 수 있습니다.
- 온-프레미스 네트워크에서 Azure 서비스로 가는 트래픽에는 서비스 엔드포인트 정책을 사용할 수 없습니다.
- Azure 관리 서비스는 현재 엔드포인트 정책을 지원하지 않습니다. 여기에는 공유 서브넷에 배포된 관리되는 서비스(예: *Azure Batch, AZURE ADDS, Azure Application Gateway, Azure VPN Gateway, Azure Firewall*) 또는 전용 서브넷(예: *Azure App Service Environment, Azure Redis Cache, Azure API MANAGEMENT, Azure SQL MI, 클래식 관리 서비스*)이 포함됩니다.

 > [!WARNING]
 > 인프라 요구 사항에 따라 가상 네트워크(예: Azure HDInsight), Azure 서비스(예: Azure Storage)에 배포된 Azure 서비스. 엔드포인트 정책을 특정 리소스로 제한하면 가상 네트워크에 배포된 Azure 서비스에 대해 해당 인프라 리소스 액세스가 끊길 수 있습니다.

- 클래식 스토리지 계정은 엔드포인트 정책에서 지원되지 않습니다. 정책은 기본적으로 모든 클래식 스토리지 계정에 대한 액세스를 거부합니다. 애플리케이션이 Azure Resource Manager 및 클래식 스토리지 계정에 액세스해야 하는 경우 해당 트래픽에 엔드포인트 정책을 사용해서는 안 됩니다.

## <a name="pricing-and-limits"></a>가격 책정 및 제한

서비스 엔드포인트 정책 사용에는 추가 비용이 없습니다. Azure 서비스(예: Azure Storage)의 현재 가격 책정 모델은 서비스 엔드포인트에서 현재 상태로 적용됩니다.

서비스 엔드포인트 정책에는 다음 제한이 적용됩니다. 

 |리소스 | 기본 제한 |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpointPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>다음 단계

- [가상 네트워크 서비스 엔드포인트정책을 구성](virtual-network-service-endpoint-policies-portal.md)하는 방법에 대한 자세한 내용
- [가상 네트워크 서비스 엔드포인트](virtual-network-service-endpoints-overview.md)에 대한 자세한 내용

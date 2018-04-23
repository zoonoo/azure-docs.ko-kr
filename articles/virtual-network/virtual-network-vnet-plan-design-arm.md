---
title: Azure Virtual Network(VNet) 계획 및 디자인 가이드 | Microsoft Docs
description: 격리, 연결 및 위치 요구 사항을 기반으로 Azure에서 가상 네트워크를 계획 및 디자인하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2016
ms.author: jdial
ms.openlocfilehash: 6e41dae2f4e93fe2e3cef689596612a6a192c844
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="plan-and-design-azure-virtual-networks"></a>Azure Virtual Network 계획 및 디자인
실험할 VNet을 만드는 것이 매우 쉽지만 조직의 프로덕션 요구를 지원하도록 시간이 지남에 따라 여러 VNet을 배포할 가능성이 높습니다. 몇 가지 계획 및 디자인을 통해 VNet을 배포하고 필요한 리소스를 보다 효과적으로 배포할 수 있습니다. VNet에 대해 잘 모르는 경우 진행하기 전에 [VNet에 대한 정보](virtual-networks-overview.md) 및 [배포 방법](quick-create-portal.md)을 알아보는 것이 좋습니다.

## <a name="plan"></a>계획
성공을 위해서는 Azure 구독, 하위 지역 및 네트워크 리소스를 충분히 이해하는 것이 중요합니다. 아래 고려 사항 목록을 시작 지점으로 사용할 수 있습니다. 이러한 고려 사항을 이해했으면 네트워크 디자인에 대한 요구 사항을 정의할 수 있습니다.

### <a name="considerations"></a>고려 사항
아래 계획 질문에 응답하기 전에 다음을 고려합니다.

* Azure에서 만드는 모든 항목은 하나 이상의 리소스로 구성됩니다. 가상 컴퓨터(VM)는 리소스이며 VM에 사용되는 NIC(네트워크 어댑터 인터페이스)는 리소스이며 NIC에 사용된 공용 IP 주소는 리소스이며 NIC가 연결되는 VNet은 리소스입니다.
* [Azure 지역](https://azure.microsoft.com/regions/#services) 및 구독 내에서 리소스를 만듭니다. 리소스는 리소스가 있는 동일한 하위 지역 및 구독에 있는 가상 네트워크에만 연결할 수 있습니다.
* 다음을 사용하여 가상 네트워크를 서로 연결할 수 있습니다.
    * **[가상 네트워크 피어링](virtual-network-peering-overview.md)**: 피어링된 가상 네트워크는 동일한 Azure 지역에 있어야 합니다. 리소스가 같은 가상 네트워크에 연결된 것처럼 피어링된 가상 네트워크의 리소스 간 대역폭은 동일합니다.
    * **Azure [VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)**: 가상 네트워크는 같은 Azure 지역에 있을 수도 있고 다른 Azure 지역에 있을 수도 있습니다. VPN Gateway를 통해 연결된 가상 네트워크의 리소스 간 대역폭은 VPN Gateway의 대역폭에 의해 제한됩니다.
* Azure에서 제공하는 [연결 옵션](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) 중 하나를 사용하여 VNet을 온-프레미스 네트워크에 연결할 수 있습니다.
* [리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups)에서 다양한 리소스를 함께 그룹화하여 리소스를 단위로 보다 쉽게 관리할 수 있습니다. 리소스 그룹은 해당 리소스가 동일한 구독에 속하기만 하면 여러 하위 지역의 리소스를 포함할 수 있습니다.

### <a name="define-requirements"></a>요구 사항 정의
Azure 네트워크 디자인을 위한 시작 지점으로 아래 질문을 사용합니다.    

1. VNet을 호스트하는 데 어떤 Azure 위치를 사용하시겠습니까?
2. 이러한 Azure 위치 간의 통신을 제공해야 합니까?
3. Azure VNet 및 온-프레미스 데이터 센터 간의 통신을 제공해야 합니까?
4. 솔루션에 얼마나 많은 IaaS(Infrastructure as a Service) VM, 클라우드 서비스 역할 및 웹앱이 필요합니까?
5. VM 그룹을 기반으로 트래픽을 격리해야 합니까(예: 프런트 엔드 웹 서버 및 백 엔드 데이터베이스 서버)?
6. 가상 어플라이언스를 사용하여 트래픽 흐름을 제어해야 합니까?
7. 사용자에게 Azure 리소스마다 다른 권한 집합이 필요합니까?

### <a name="understand-vnet-and-subnet-properties"></a>VNet 및 서브넷 속성 이해
VNet과 서브넷 리소스를 사용하여 Azure에서 실행 중인 워크로드에 대한 보안 경계를 정의할 수 있습니다. VNet은 주소 공간의 모임(CIDR 블록으로 정의됨)으로 특징지을 수 있습니다.

> [!NOTE]
> 네트워크 관리자는 CIDR 표기법에 익숙합니다. CIDR을 잘 모르는 경우 [자세히 알아보세요](http://whatismyipaddress.com/cidr).
>
>

VNet에는 다음 속성이 포함될 수 있습니다.

| 자산 | 설명 | 제약 조건 |
| --- | --- | --- |
| **name** |VNet 이름 |최대 80자의 문자열입니다. 문자, 숫자, 밑줄, 마침표 또는 하이픈을 포함할 수 있습니다. 문자 또는 숫자로 시작해야 합니다. 문자, 숫자 또는 밑줄로 끝나야 합니다. 대문자 또는 소문자를 포함할 수 있습니다. |
| **위치** |Azure 위치(하위 지역이라고도 함). |올바른 Azure 위치 중 하나여야 합니다. |
| **addressSpace** |CIDR 표기법에서 VNet을 구성하는 주소 접두사 컬렉션 |공용 IP 주소 범위를 포함하여 올바른 CIDR 주소 블록의 배열이어야 합니다. |
| **서브넷** |VNet을 구성하는 서브넷 컬렉션 |아래 서브넷 속성 표를 참조하세요. |
| **dhcpOptions** |**dnsServers**로 명명된 단일 필수 속성을 포함하는 개체입니다. | |
| **dnsServers** |VNet에서 사용하는 DNS 서버의 배열입니다. 서버를 지정하지 않은 경우 Azure 내부 이름 확인이 사용됩니다. |IP 주소를 통해 최대 10개의 DNS 서버 배열이어야 합니다. |

서브넷은 VNet의 자식 리소스이며, IP 주소 접두사를 사용하여 CIDR 블록 내의 주소 공간 세그먼트를 정의하는 데 도움이 됩니다. NIC는 서브넷에 추가하고 VM에 연결하여 다양한 워크로드에 대한 연결을 제공할 수 있습니다.

서브넷에는 다음 속성이 포함될 수 있습니다.

| 자산 | 설명 | 제약 조건 |
| --- | --- | --- |
| **name** |서브넷 이름 |최대 80자의 문자열입니다. 문자, 숫자, 밑줄, 마침표 또는 하이픈을 포함할 수 있습니다. 문자 또는 숫자로 시작해야 합니다. 문자, 숫자 또는 밑줄로 끝나야 합니다. 대문자 또는 소문자를 포함할 수 있습니다. |
| **위치** |Azure 위치(하위 지역이라고도 함). |올바른 Azure 위치 중 하나여야 합니다. |
| **addressPrefix** |CIDR 표기법에서 서브넷을 구성하는 단일 주소 접두사 |VNet의 주소 공간 중 하나에 포함된 단일 CIDR 블록이어야 합니다. |
| **networkSecurityGroup** |서브넷에 적용된 NSG | |
| **routeTable** |서브넷에 적용된 경로 테이블 | |
| **ipConfigurations** |서브넷에 연결된 NIC에 사용된 IP 구성 개체 컬렉션 | |

### <a name="name-resolution"></a>이름 확인
기본적으로 VNet은 VNet 내부, 공용 인터넷에서 이름을 확인하는 데 [Azure에서 제공하는 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md)을 사용합니다. 그러나 온-프레미스 데이터 센터에 VNet을 연결하는 경우 네트워크 간에 이름을 확인하기 위해 [자체 DNS 서버](virtual-networks-name-resolution-for-vms-and-role-instances.md) 를 제공해야 합니다.  

### <a name="limits"></a>제한
[Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서에서 네트워킹 제한을 검토하여 디자인이 제한과 충돌하지 않는지 확인합니다. 일부 제한은 지원 티켓을 열어 늘릴 수 있습니다.

### <a name="role-based-access-control-rbac"></a>역할 기반 Access Control(RBAC)
[Azure RBAC](../role-based-access-control/built-in-roles.md) 를 사용하여 Azure의 다른 리소스에 대해 다양한 사용자가 보유할 수 있는 액세스 수준을 제어할 수 있습니다. 그러면 사용자 요구에 따라 팀에서 수행한 작업을 분리할 수 있습니다.

가상 네트워크 관점에서 **네트워크 참여자** 역할의 사용자는 Azure 리소스 관리자 가상 네트워크 리소스에 대한 모든 권한을 가집니다. 마찬가지로, **클래식 네트워크 참여자** 역할의 사용자는 클래식 가상 네트워크 리소스에 대해 모든 권한을 가집니다.

> [!NOTE]
> [자신의 역할을 만들어](../role-based-access-control/role-assignments-portal.md) 관리 요구를 분리할 수도 있습니다.
>
>

## <a name="design"></a>디자인
[계획](#Plan) 섹션의 질문에 대한 답변을 파악하면 VNet를 정의하기 전에 다음을 검토합니다.

### <a name="number-of-subscriptions-and-vnets"></a>구독 및 VNet의 수
다음과 같은 시나리오에서는 여러 VNet을 만드는 것이 좋습니다.

* **서로 다른 Azure 위치에 배치되어야 하는 VM**. Azure에서 VNet은 지역적입니다. 여러 위치에 걸쳐 있지 않습니다. 따라서 안에서 VM을 호스트할 각 Azure 위치에 대해 하나 이상의 VNet이 필요합니다.
* **서로 완전히 격리해야 하는 워크로드**. 동일한 IP 주소 공간을 사용하더라도 서로 다른 워크로드를 격리하기 위해 별도의 VNet을 만들 수 있습니다.

위에 나와 있는 제한은 하나의 하위 지역, 1개 구독을 기준으로 한다는 점에 유의합니다. 즉, 여러 구독을 사용하여 Azure에서 유지 관리할 수 있는 리소스의 제한을 늘릴 수 있습니다. 사이트 간 VPN 또는 ExpressRoute 회로를 사용하여 다양한 구독에서 VNet에 연결할 수 있습니다.

### <a name="subscription-and-vnet-design-patterns"></a>구독 및 VNet 디자인 패턴
아래 표에서는 구독 및 VNet을 사용하기 위한 몇 가지 일반적인 디자인 패턴을 보여 줍니다.

| 시나리오 | 다이어그램 | 장점 | 단점 |
| --- | --- | --- | --- |
| 단일 구독, 앱당 두 VNet |![단일 구독](./media/virtual-network-vnet-plan-design-arm/figure1.png) |관리할 구독이 하나뿐입니다. |Azure 지역당 최대 Vnet 수입니다. 이후에 구독이 더 필요합니다. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요. |
| 앱당 1개 구독, 앱당 두 VNet |![단일 구독](./media/virtual-network-vnet-plan-design-arm/figure2.png) |구독당 두 개의 VNet만 사용합니다. |앱이 너무 많은 경우 관리하기 어렵습니다. |
| 사업부당 1개 구독, 앱당 두 VNet |![단일 구독](./media/virtual-network-vnet-plan-design-arm/figure3.png) |구독 및 VNet 수 간에 균형을 유지합니다. |사업부(구독)당 최대 VNet 수입니다. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요. |
| 사업부당 1개 구독, 앱 그룹당 두 VNet |![단일 구독](./media/virtual-network-vnet-plan-design-arm/figure4.png) |구독 및 VNet 수 간에 균형을 유지합니다. |서브넷과 NSG를 사용하여 앱을 격리해야 합니다. |

### <a name="number-of-subnets"></a>서브넷 수
다음과 같은 시나리오에서는 VNet에 여러 서브넷이 있는 것이 좋습니다.

* **서브넷에 있는 모든 NIC에 대해 개인 IP 주소가 부족**. 서브넷 주소 공간에 서브넷의 NIC 수에 맞는 충분한 IP 주소가 없는 경우 여러 서브넷을 만들어야 합니다. Azure에서는 각 서브넷에서 사용할 수 없는 5개의 개인 IP 주소를 예약해 둡니다. 주소 공간의 처음 및 마지막 주소(서브넷 주소 및 멀티캐스트)와 내부적으로 사용되는 3개 주소(DHCP 및 DNS 용도)입니다.
* **보안**. 서브넷을 사용하여 다중 계층 구조의 워크로드에 대해 VM 그룹을 서로 구분하고 해당 서브넷에 대해 서로 다른 [NSG(네트워크 보안 그룹)](virtual-networks-nsg.md#subnets) 를 적용할 수 있습니다.
* **하이브리드 연결**. VPN 게이트웨이 및 ExpressRoute 회로를 사용하여 VNet을 서로 [연결](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) 하고 온-프레미스 데이터 센터에 연결할 수 있습니다. VPN 게이트웨이 및 ExpressRoute 회로에는 만들려는 자체의 서브넷이 필요합니다.
* **가상 어플라이언스**. Azure VNet에서 방화벽, WAN 가속기 또는 VPN 게이트웨이 같은 가상 어플라이언스를 사용할 수 있습니다. 이렇게 하려면 해당 어플라이언스로 [트래픽을 라우팅](virtual-networks-udr-overview.md) 하고 자체의 서브넷에서 이를 격리해야 합니다.

### <a name="subnet-and-nsg-design-patterns"></a>서브넷 및 NSG 디자인 패턴
아래 표에서는 서브넷을 사용하기 위한 몇 가지 일반적인 디자인 패턴을 보여 줍니다.

| 시나리오 | 다이어그램 | 장점 | 단점 |
| --- | --- | --- | --- |
| 단일 서브넷, 앱당 응용 프로그램 계층당 여러 NSG |![단일 서브넷](./media/virtual-network-vnet-plan-design-arm/figure5.png) |관리할 서브넷이 하나뿐입니다. |각 응용 프로그램을 격리하는 데 여러 NSG가 필요합니다. |
| 앱당 1개 서브넷, 응용 프로그램 계층당 여러 NSG |![앱당 서브넷](./media/virtual-network-vnet-plan-design-arm/figure6.png) |관리할 NSG가 소수입니다. |관리할 서브넷이 여러 개입니다. |
| 응용 프로그램 계층당 1개 서브넷, 앱당 여러 NSG |![계층당 서브넷](./media/virtual-network-vnet-plan-design-arm/figure7.png) |서브넷 및 NSG 수 간에 균형을 유지합니다. |구독당 최대 NSG 수입니다. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요. |
| 앱당, 응용 프로그램 계층당 1개 서브넷, 서브넷당 여러 NSG |![앱당 계층당 서브넷](./media/virtual-network-vnet-plan-design-arm/figure8.png) |가능하면 NSG 수가 더 적습니다. |관리할 서브넷이 여러 개입니다. |

## <a name="sample-design"></a>샘플 디자인
이 문서의 정보가 어떻게 적용되는지를 설명하기 위해 다음 시나리오를 고려합니다.

여러분은 데이터 센터가 북아메리카에 2개, 유럽에 2개 있는 회사에서 일합니다. 파일럿으로 Azure에 마이그레이션할 서로 다른 2개의 사업부에서 유지 관리하는 응용 프로그램을 연결하는 6개의 서로 다른 고객을 식별했습니다. 응용 프로그램에 대한 기본 아키텍처는 다음과 같습니다.

* App1, App2, App3 및 App4는 Ubuntu를 실행하는 Linux 서버에서 호스트되는 웹 응용 프로그램입니다. 각 응용 프로그램은 Linux 서버에서 RESTful 서비스를 호스트하는 별도의 응용 프로그램 서버에 연결됩니다. RESTful 서비스는 백 엔드 MySQL 데이터베이스에 연결됩니다.
* App5 및 App6은 Windows Server 2012 R2를 실행하는 Windows 서버에서 호스트되는 웹 응용 프로그램입니다. 각 응용 프로그램은 백 엔드 SQL Server 데이터베이스에 연결됩니다.
* 모든 앱은 현재 북아메리카에 있는 회사의 데이터 센터 중 하나에서 호스트됩니다.
* 온-프레미스 데이터 센터는 10.0.0.0/8 주소 공간을 사용합니다.

다음 요구 사항을 충족하는 가상 네트워크 솔루션을 디자인해야 합니다.

* 각 사업부는 다른 사업부의 리소스 사용량에 영향을 받아서는 안 됩니다.
* 관리가 더 쉽도록 VNet 및 서브넷의 양을 최소화해야 합니다.
* 각 사업부에는 모든 응용 프로그램에 사용되는 단일 테스트/개발 VNet이 있어야 합니다.
* 각 응용 프로그램은 대륙당(북아메리카 및 유럽) 서로 다른 2개의 Azure 데이터 센터에서 호스트됩니다.
* 각 응용 프로그램은 서로 완전히 격리됩니다.
* 고객은 HTTP를 사용하여 인터넷을 통해 각 응용 프로그램에 액세스할 수 있습니다.
* 온-프레미스 데이터 센터에 연결된 사용자는 암호화된 터널을 사용하여 각 응용 프로그램에 액세스할 수 있습니다.
* 온-프레미스 데이터 센터로의 연결은 기존 VPN 장치를 사용해야 합니다.
* 회사의 네트워킹 그룹은 VNet 구성에 대한 모든 권한을 가집니다.
* 각 사업부의 개발자는 VM을 기존의 서브넷에만 배포할 수 있습니다.
* 모든 응용 프로그램은 Azure로 그대로 마이그레이션됩니다(전환).
* 각 위치에 있는 데이터베이스는 하루에 한 번의 다른 Azure 위치에 복제됩니다.
* 각 응용 프로그램은 5개의 프런트 엔드 웹 서버, 2개의 응용 프로그램 서버(필요한 경우) 및 2개의 데이터베이스 서버를 사용합니다.

### <a name="plan"></a>계획
아래처럼 [요구 사항 정의](#Define-requirements) 섹션에 있는 질문에 응답하여 디자인 계획을 시작합니다.

1. VNet을 호스트하는 데 어떤 Azure 위치를 사용하시겠습니까?

    북아메리카에 2곳, 유럽에 2곳입니다. 기존 온-프레미스 데이터 센터의 물리적 위치에 기반하여 선택해야 합니다. 이렇게 하면 물리적 위치에서 Azure로의 연결에 대한 대기 시간이 향상됩니다.
2. 이러한 Azure 위치 간의 통신을 제공해야 합니까?

    예. 데이터베이스를 모든 위치에 복제해야 하기 때문입니다.
3. Azure VNet 및 온-프레미스 데이터 센터 간의 통신을 제공해야 합니까?

    예. 온-프레미스 데이터 센터에 연결된 사용자는 암호화된 터널을 통해 응용 프로그램에 액세스할 수 있어야 합니다.
4. 솔루션에 IaaS VM이 얼마나 필요합니까?

    200개의 IaaS VM입니다. App1, App2, App3 및 App4에는 각각 5개의 웹 서버, 각각 2개의 응용 프로그램 서버, 각각 2개의 데이터베이스 서버가 필요합니다. 응용 프로그램당 총 9개의 IaaS VM 또는 36개의 IaaS VM입니다. App5 및 App6에는 5개의 웹 서버와 각각 2개의 데이터베이스 서버가 필요합니다. 응용 프로그램당 총 7개의 IaaS VM 또는 14개의 IaaS VM입니다. 따라서 각 Azure 지역에 있는 모든 응용 프로그램에 대해 50개의 IaaS VM이 필요합니다. 4개의 하위 지역이 필요하므로 200개의 IaaS VM이 됩니다.

    각 VNet 또는 온-프레미스 데이터 센터에는 Azure IaaS VM 및 온-프레미스 네트워크 간의 이름을 확인하기 위해 DNS 서버를 제공해야 합니다.
5. VM 그룹을 기반으로 트래픽을 격리해야 합니까(예: 프런트 엔드 웹 서버 및 백 엔드 데이터베이스 서버)?

    예. 각 응용 프로그램은 서로 완전히 격리되어야 하고 각 응용 프로그램 계층도 격리되어야 합니다.
6. 가상 어플라이언스를 사용하여 트래픽 흐름을 제어해야 합니까?

    번호 보다 자세한 데이터 평면 로깅을 비롯하여 트래픽 흐름에 대한 세밀한 제어를 제공하기 위해 가상 어플라이언스를 사용할 수 있습니다.
7. 사용자에게 Azure 리소스마다 다른 권한 집합이 필요합니까?

    예. 네트워킹 팀은 가상 네트워킹 설정에 대한 모든 권한이 필요한 반면 개발자는 VM을 기존의 서브넷에 배포할 수만 있어야 합니다.

### <a name="design"></a>디자인
구독, VNet, 서브넷 및 NSG를 지정하는 디자인을 따라야 합니다. 여기서는 NSG를 설명하지만 디자인을 완료하기 전에 [NSG](virtual-networks-nsg.md) 에 대해 자세히 알아보아야 합니다.

**구독 및 VNet의 수**

다음 요구 사항은 구독 및 VNet에 대한 것입니다.

* 각 사업부는 다른 사업부의 리소스 사용량에 영향을 받아서는 안 됩니다.
* VNet 및 서브넷의 양을 최소화해야 합니다.
* 각 사업부에는 모든 응용 프로그램에 사용되는 단일 테스트/개발 VNet이 있어야 합니다.
* 각 응용 프로그램은 대륙당(북아메리카 및 유럽) 서로 다른 2개의 Azure 데이터 센터에서 호스트됩니다.

이러한 요구에 따라 각 사업부에 대한 구독이 필요합니다. 이런 방식으로 사업부의 리소스 사용량은 다른 사업부에 대한 제한에 포함되지 않습니다. VNet 수를 최소화하려고 하므로 아래처럼 **사업부당 1개 구독, 앱 그룹당 2개 VNet** 패턴의 사용을 고려합니다.

![단일 구독](./media/virtual-network-vnet-plan-design-arm/figure9.png)

또한 각 VNet에 대한 주소 공간도 지정해야 합니다. 온-프레미스 데이터 센터 및 Azure 지역 간의 연결이 필요하므로 Azure VNet에 사용되는 주소 공간은 온-프레미스 네트워크와 충돌할 수 없으며 각 VNet에 사용되는 주소 공간은 기존의 다른 VNet과 충돌하지 않아야 합니다. 이러한 요구 사항을 충족하기 위해 아래 표의 주소 공간을 사용할 수 있습니다.  

| **구독** | **VNet** | **Azure 지역** | **주소 공간** |
| --- | --- | --- | --- |
| BU1 |ProdBU1US1 |미국 서부 |172.16.0.0/16 |
| BU1 |ProdBU1US2 |미국 동부 |172.17.0.0/16 |
| BU1 |ProdBU1EU1 |북유럽 |172.18.0.0/16 |
| BU1 |ProdBU1EU2 |서유럽 |172.19.0.0/16 |
| BU1 |TestDevBU1 |미국 서부 |172.20.0.0/16 |
| BU2 |TestDevBU2 |미국 서부 |172.21.0.0/16 |
| BU2 |ProdBU2US1 |미국 서부 |172.22.0.0/16 |
| BU2 |ProdBU2US2 |미국 동부 |172.23.0.0/16 |
| BU2 |ProdBU2EU1 |북유럽 |172.24.0.0/16 |
| BU2 |ProdBU2EU2 |서유럽 |172.25.0.0/16 |

**서브넷 및 NSG 수**

다음 요구 사항은 서브넷 및 NSG에 대한 것입니다.

* VNet 및 서브넷의 양을 최소화해야 합니다.
* 각 응용 프로그램은 서로 완전히 격리됩니다.
* 고객은 HTTP를 사용하여 인터넷을 통해 각 응용 프로그램에 액세스할 수 있습니다.
* 온-프레미스 데이터 센터에 연결된 사용자는 암호화된 터널을 사용하여 각 응용 프로그램에 액세스할 수 있습니다.
* 온-프레미스 데이터 센터로의 연결은 기존 VPN 장치를 사용해야 합니다.
* 각 위치에 있는 데이터베이스는 하루에 한 번의 다른 Azure 위치에 복제됩니다.

이러한 요구 사항에 따라, 응용 프로그램 계층당 1개의 서브넷을 사용할 수 있으며 응용 프로그램당 트래픽을 필터링하기 위해 여러 NSG를 사용할 수 있습니다. 이런 방식으로 각 VNet에 3개의 서브넷(프런트 엔드, 응용 프로그램 계층 및 데이터 센터) 및 서브넷당 응용 프로그램당 1개 NSG만 포함합니다. 이 경우 **응용 프로그램 계층당 하나의 서브넷, 앱당 여러 NSG** 디자인 패턴의 사용을 고려해야 합니다. 아래 그림은 **ProdBU1US1** VNet을 나타내는 디자인 패턴의 사용을 보여 줍니다.

![계층당 1개 서브넷, 계층당 응용 프로그램당 1개 NSG](./media/virtual-network-vnet-plan-design-arm/figure11.png)

그러나 VNet 간, 온-프레미스 데이터 센터 간의 VPN 연결을 위한 별도의 서브넷을 만들어야 합니다. 또한 각 서브넷에 대한 주소 공간도 지정해야 합니다. 아래 그림은 **ProdBU1US1** VNet에 대한 샘플 솔루션을 보여 줍니다. 각 VNet에 대해 이 시나리오를 복제합니다. 각 색상은 서로 다른 응용 프로그램을 나타냅니다.

![샘플 VNet](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Access Control**

다음 요구 사항은 액세스 제어에 대한 것입니다.

* 회사의 네트워킹 그룹은 VNet 구성에 대한 모든 권한을 가집니다.
* 각 사업부의 개발자는 VM을 기존의 서브넷에만 배포할 수 있습니다.

이러한 요구 사항에 따라 네트워킹 팀에서 사용자를 각 구독의 기본 제공 **네트워크 참여자** 역할에 추가할 수 있으며 각 구독의 응용 프로그램 개발자에 대한 사용자 지정 역할을 만들어 기존 서브넷에 VM을 추가할 권한을 부여할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [가상 네트워크를 배포합니다](quick-create-portal.md).
* IaaS VM [부하를 분산](../load-balancer/load-balancer-overview.md)시키고 [여러 Azure 지역을 통한 라우팅 관리](../traffic-manager/traffic-manager-overview.md) 방법을 이해합니다.
* [NSG(네트워크 보안 그룹)](security-overview.md) 솔루션에 대해 알아봅니다.
* [크로스-프레미스 및 VNet 연결 옵션](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)에 대해 알아봅니다.

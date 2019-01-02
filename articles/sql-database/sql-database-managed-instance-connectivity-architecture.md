---
title: Azure SQL Database Managed Instance 연결 아키텍처 | Microsoft Docs
description: 이 문서에서는 Azure SQL Database Managed Instance 통신 개요를 제공하고, 다양한 구성 요소가 트래픽을 Managed Instance로 전송하기 위해 작동하는 방식과 연결 아키텍처에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: 964f91f412645e141ca003d511480f6f6eb438a3
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343305"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Azure SQL Database Managed Instance 연결 아키텍처

이 문서에서는 Azure SQL Database Managed Instance 통신 개요를 제공하고, 다양한 구성 요소가 트래픽을 Managed Instance로 전송하기 위해 작동하는 방식과 연결 아키텍처에 대해 설명합니다.  

Azure SQL Database Managed Instance는 Azure VNet 및 Managed Instance 전용 서브넷 내부에 있습니다. 이 배포를 사용하면 다음과 같은 시나리오를 수행할 수 있습니다. 
- 보안 개인 IP 주소.
- 온-프레미스 네트워크에서 직접 Managed Instance에 연결.
- 연결된 서버 또는 다른 온-프레미스 데이터 저장소에 Managed Instance 연결.
- Azure 리소스에 Managed Instance 연결.

## <a name="communication-overview"></a>통신 개요

다음 다이어그램은 Managed Instance가 제대로 작동하기 위해 연결되어야 하는 리소스 뿐만 아니라 Managed Instance에 연결되는 엔터티를 보여 줍니다.

![연결 아키텍처 엔터티](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

다이어그램 맨 아래에 표시되는 통신은 데이터 소스로서 Managed Instance에 연결되는 고객 애플리케이션 및 도구를 나타냅니다.  

Managed Instance는 PaaS(Platform-As-A-Services)로 제공되므로 Microsoft는 원격 분석 데이트 스트림을 기준으로 자동화 에이전트(관리, 배포 및 유지 관리)를 사용하여 이 서비스를 관리합니다. Managed Instance 관리는 전적으로 Microsoft의 책임이므로 고객은 RDP를 통해 관리되는 인스턴스 가상 클러스터 컴퓨터에 액세스할 수 없습니다.

최종 사용자 또는 애플리케이션이 시작하는 일부 SQL Server 작업을 위해서는 Managed Instance가 플랫폼과 상호 작용해야 할 수 있습니다. 하나는 포털, PowerShell 및 Azure CLI를 통해 노출되는 리소스인 Managed Instance 데이터베이스를 만드는 경우입니다.

Managed Instance는 적절한 작동을 위해 다른 Azure 서비스에 의존하며(예: 백업을 위해 Azure Storage, 원격 분석을 위해 Azure Service Bus, 인증을 위해 Azure AD, TDE를 위해 Azure Key Vault 등) 그에 따라 해당 서비스에 대한 연결을 시작합니다.

위에 언급된 모든 통신은 암호화되고 인증서를 사용하여 서명됩니다. 통신 당사자가 신뢰될 수 있도록 하기 위해 Managed Instance는 인증 기관에 연락하여 이러한 인증서를 지속적으로 확인합니다. 인증서가 해지되거나 Managed Instance에서 해당 인증서를 확인할 수 없으면 데이터 보호를 위해 연결을 닫습니다.

## <a name="high-level-connectivity-architecture"></a>고급 연결 아키텍처

높은 수준에서 보면 Managed Instance는 고객 가상 네트워크 서브넷 내에서 실행되고 가상 클러스터를 형성하는 격리된 전용 가상 머신 집합에서 호스트되는 서비스 구성 요소 집합입니다.

여러 Managed Instance가 단일 가상 클러스터에 호스트될 수 있습니다. 클러스터는 고객이 서브넷에서 프로비전된 인스턴스 수를 변경하면 필요에 따라 자동으로 확장 또는 축소됩니다.

고객 애플리케이션은 개인 IP 주소를 갖는 엔드포인트를 사용하여 가상 네트워크 또는 피어링된 가상 네트워크나 VPN/Express Route 연결 네트워크 내에서 실행되는 경우에만 Managed Instance에 연결하고 데이터베이스를 쿼리 및 업데이트할 수 있습니다.  

![연결 아키텍처 다이어그램](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft 관리 및 배포 서비스는 Managed Instance과 Microsoft 서비스 간의 연결이 공용 IP 주소를 갖는 엔드포인트를 통과하도록 가상 네트워크 외부에서 실행됩니다. Managed Instance가 아웃바운드 연결을 만들 때, NAT(Network Address Translation)로 인해 수신 끝에서는 연결이 이 공용 IP에서 시작되는 것처럼 보입니다.

관리 트래픽은 고객 가상 네트워크를 통해 흐릅니다. 즉, 가상 네트워크 인프라의 요소가 영향을 미치며, 관리 트래픽을 손상시켜 인스턴스를 오류 상태로 만들고 사용할 수 없게 할 수 있습니다.

> [!IMPORTANT]
> Microsoft는 고객 환경 및 서비스 가용성을 향상시키기 위해 Managed Instance 작동에 영향을 줄 수 있는 Azure Virtual Network 인프라 요소에 네트워크 의도 정책을 적용합니다. 이것은 네트워크 구성 요소를 방지하고 정상적인 Managed Instance 작동을 보장하는 것을 주 목적으로 하는 플랫폼 메커니즘으로, 네트워크 요구 사항을 최종 사용자에게 투명하게 전달합니다. Managed Instance 삭제 시, 네트워크 의도 정책도 제거됩니다.

## <a name="virtual-cluster-connectivity-architecture"></a>가상 클러스터 연결 아키텍처

Managed Instance 연결 아키텍처에 대해 좀 더 자세히 살펴보겠습니다. 다음 다이어그램은 가상 클러스터의 개념적 레이아웃을 보여 줍니다.

![연결 아키텍처 다이어그램 가상 클러스터](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

클라이언트는 `<mi_name>.<dns_zone>.database.windows.net` 형식을 갖는 호스트 이름을 사용하여 Managed Instance에 연결합니다. 이 호스트 이름은 공용 DNS 영역에 등록되고 공개적으로 확인 가능하더라도 개인 IP 주소로 확인됩니다. `zone-id`는 클러스터를 만들 때 자동으로 생성됩니다. 새로 만든 클러스터가 보조 관리형 인스턴스를 호스트하는 경우 해당 영역 ID를 기본 클러스터와 공유합니다. 자세한 내용은 [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)을 참조하세요.

이 개인 IP 주소는 트래픽을 Managed Instance GW(게이트웨이)로 전송하는 Managed Instance ILB(내부 Load Balancer)에 속합니다. 여러 Managed Instance가 동일한 클러스터 내에서 실행될 수 있으므로 GW는 Managed Instance 호스트 이름을 사용하여 트래픽을 올바른 SQL 엔진 서비스로 리디렉션합니다.

관리 및 배포 서비스는 외부 부하 분산 장치에 매핑되는 [관리 엔드포인트](#management-endpoint)를 사용하여 Managed Instance에 연결됩니다. 트래픽은 Managed Instance 관리 구성 요소가 배타적으로 사용하는 미리 정의된 포트 세트에서 수신될 때만 노드로 라우팅됩니다. 노드의 기본 제공 방화벽은 Microsoft 특정 IP 범위에서만 트래픽을 허용하도록 구성됩니다. 관리 구성 요소와 관리 평면 간의 모든 통신은 인증서로 상호 인증됩니다.

## <a name="management-endpoint"></a>관리 엔드포인트

Azure SQL Database Managed Instance 가상 클러스터에는 Microsoft에서 Managed Instance를 관리하는 데 사용하는 관리 엔드포인트가 포함됩니다. 관리 엔드포인트는 네트워크 수준의 기본 제공 방화벽과 애플리케이션 수준의 상호 인증서 확인으로 보호됩니다. [관리 엔드포인트 IP 주소를 찾을](sql-database-managed-instance-find-management-endpoint-ip-address.md) 수 있습니다.

연결이 Managed Instance(백업, 감사 로그) 내부에서 시작되면 트래픽이 관리 엔드포인트 공용 IP 주소에서 시작되는 것처럼 보입니다. Managed Instance IP 주소만 허용하도록 방화벽 규칙을 설정하여 Managed Instance에서 공용 서비스에 대한 액세스를 제한할 수 있습니다. [Managed Instance 기본 제공 방화벽을 확인](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)할 수 있는 메서드에 대한 추가 정보를 찾으세요.

> [!NOTE]
> Azure 플랫폼에는 동시에 배치되는 서비스 간에 이동하는 트래픽에 대한 최적화가 있으므로 Managed Instance와 동일한 지역에 있는 Azure 서비스에 대한 방화벽 규칙 설정에는 이 내용이 적용되지 않습니다.

## <a name="network-requirements"></a>네트워크 요구 사항

다음 요구 사항을 준수하는 가상 네트워크 내의 전용 서브넷(Managed Instance 서브넷)에 Managed Instance를 배포할 수 있습니다.
- **전용 서브넷**: Managed Instance 서브넷은 연결된 다른 클라우드 서비스를 포함하지 않아야 하고, 게이트웨이 서브넷이 아니어야 합니다. Managed Instance는 Managed Instance 이외의 리소스가 포함된 서브넷에 만들 수 없으며, 나중에 다른 리소스를 이 서브넷에 추가할 수 없습니다.
- **호환 가능한 NSG(네트워크 보안 그룹)**: Managed Instance 서브넷과 연결되는 NSG에는 다음 표에 나와 있는 규칙(필수 인바운드 및 아웃바운드 보안 규칙)이 다른 규칙 앞에 있어야 합니다. NSG를 사용하여 1433 포트에서 트래픽을 필터링함으로써 Managed Instance 데이터 엔드포인트에 대한 액세스를 완전히 제어할 수 있습니다. 
- **호환 가능한 UDR(사용자 정의 경로) 테이블**: Managed Instance 서브넷에는 **0.0.0.0/0 다음 홉 인터넷**이 필수 UDR로 할당된 사용자 경로 테이블이 있어야 합니다. 또한 온-프레미스 사설 IP 범위가 있는 트래픽을 가상 네트워크 게이트웨이 또는 NVA(가상 네트워크 어플라이언스)를 통해 대상으로 라우팅하는 UDR을 추가할 수 있습니다. 
- **선택적 사용자 지정 DNS**: 사용자 지정 DNS가 가상 네트워크에 지정되면 Azure의 재귀 확인자 IP 주소(예: 168.63.129.16)를 목록에 추가해야 합니다. 자세한 내용은 참조 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요. 사용자 지정 DNS 서버에서 다음 도메인 및 해당 하위 도메인의 호스트 이름을 확인할 수 있어야 합니다. *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com* 및 *microsoftonline-p.com* 
- **서비스 엔드포인트 없음**: Managed Instance 서브넷에는 연결된 서비스 엔드포인트가 없어야 합니다. 가상 네트워크를 만들 때 서비스 엔드포인트 옵션을 사용하지 않도록 설정해야 합니다.
- **충분한 IP 주소**: Managed Instance 서브넷에는 16개 이상의 IP 주소가 있어야 합니다(권장되는 최소값은 32개 IP 주소). 자세한 내용은 [Managed Instance의 서브넷 크기 결정](sql-database-managed-instance-determine-size-vnet-subnet.md)을 참조하세요. [Managed Instance 네트워킹 요구 사항](#network-requirements)을 충족하도록 구성하거나 [새 네트워크 및 서브넷](sql-database-managed-instance-create-vnet-subnet.md)을 만든 후 [기존 네트워크](sql-database-managed-instance-configure-vnet-subnet.md)에 Managed Instance를 배포할 수 있습니다.

> [!IMPORTANT]
> 대상 서브넷이 이러한 모든 요구 사항과 호환되지 않는 경우 새 Managed Instance를 배포할 수 없습니다. Managed Instance가 만들어지면 네트워크 구성에 대한 호환되지 않는 변경을 방지하기 위해 *네트워크 의도 정책*이 서브넷에 적용됩니다. 마지막 인스턴스가 서브넷에서 제거되면 *네트워크 의도 정책*도 제거됩니다

### <a name="mandatory-inbound-security-rules"></a>필수 인바운드 보안 규칙 

| 이름       |포트                        |프로토콜|원본           |대상|조치|
|------------|----------------------------|--------|-----------------|-----------|------|
|관리  |9000, 9003, 1438, 1440, 1452|TCP     |모두              |모두        |허용 |
|mi_subnet   |모두                         |모두     |MI SUBNET        |모두        |허용 |
|health_probe|모두                         |모두     |AzureLoadBalancer|모두        |허용 |

### <a name="mandatory-outbound-security-rules"></a>필수 아웃바운드 보안 규칙 

| 이름       |포트          |프로토콜|원본           |대상|조치|
|------------|--------------|--------|-----------------|-----------|------|
|관리  |80, 443, 12000|TCP     |모두              |모두        |허용 |
|mi_subnet   |모두           |모두     |모두              |MI SUBNET  |허용 |

  > [!Note]
  > 필수 인바운드 보안 규칙은 포트 9000, 9003, 1438, 1440, 1452의 ‘모든’ 원본의 트래픽을 허용하지만 이러한 포트는 기본 제공 방화벽으로 보호됩니다. 이 [문서](sql-database-managed-instance-find-management-endpoint-ip-address.md)에서는 관리 엔드포인트 IP 주소를 검색하고 방화벽 규칙을 확인하는 방법을 보여 줍니다. 

## <a name="next-steps"></a>다음 단계

- 개요는  [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- [새 VNet을 구성](sql-database-managed-instance-create-vnet-subnet.md)하거나 Managed Instance를 배포할 수 있는 [기존 VNet을 구성](sql-database-managed-instance-configure-vnet-subnet.md)하는 방법을 알아봅니다.
- Managed Instance를 배포할 [서브넷의 크기를 계산](sql-database-managed-instance-determine-size-vnet-subnet.md)합니다. 
- 빠른 시작에 대해서는 Managed Instance 생성 방법을 참조하세요.
  - [Azure Portal](sql-database-managed-instance-get-started.md)에서
  - [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) 사용
  - [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/) 사용
  - [Azure Resource Manager 템플릿(SSMS가 포함된 점프박스)](https://portal.azure.com/)

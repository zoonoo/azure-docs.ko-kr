---
title: Azure SQL Database에서 관리 되는 인스턴스에 대 한 연결 아키텍처 | Microsoft Docs
description: 구성 요소를 관리 되는 인스턴스로 트래픽을 방법과 Azure SQL Database 관리 되는 인스턴스 통신 및 연결 아키텍처에도에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: fa19ea0c7ebeea0170822db0dae298f84e958983
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006134"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Azure SQL Database에서 관리 되는 인스턴스에 대 한 연결 아키텍처

이 문서는 Azure SQL Database 관리 되는 인스턴스에 대 한 통신을 설명합니다. 또한 연결 아키텍처 및 구성 요소를 관리 되는 인스턴스로 트래픽을 하는 방법을 설명 합니다.  

SQL Database 관리 되는 인스턴스는 Azure 가상 네트워크 및 관리 되는 인스턴스에 전용 서브넷 내에 배치 됩니다. 이 배포는 다음을 제공합니다.

- 보안 개인 IP 주소입니다.
- 관리 되는 인스턴스는 온-프레미스 네트워크를 연결할 수 있습니다.
- 연결된 된 서버 또는 다른 관리 되는 인스턴스를 연결 하는 기능 온-프레미스 데이터 저장소.
- 관리 되는 인스턴스를 Azure 리소스에 연결할 수 있습니다.

## <a name="communication-overview"></a>통신 개요

다음 다이어그램은 관리 되는 인스턴스에 연결 하는 엔터티를 보여줍니다. 또한 관리 되는 인스턴스와 통신 해야 하는 리소스를 보여 줍니다. 다이어그램의 맨 아래에 있는 통신 프로세스는 고객 응용 프로그램 및 데이터 원본으로 관리 되는 인스턴스에 연결 하는 도구를 나타냅니다.  

![연결 아키텍처의 엔터티](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

관리 되는 인스턴스는 서비스 (PaaS) 방식의 플랫폼. Microsoft (관리, 배포 및 유지 관리) 원격 분석 데이터 스트림을 기준으로이 서비스를 관리 하는 자동화 된 에이전트를 사용 합니다. Microsoft 관리 담당 하기 때문에 고객에 게 액세스할 수 없습니다 관리 되는 인스턴스 클러스터 가상 컴퓨터를 통해 원격 데스크톱 프로토콜 (RDP).

플랫폼과 상호 작용 하는 인스턴스 관리 하는 일부 SQL Server 최종 사용자 또는 응용 프로그램에서 시작 하는 작업이 필요할 수 있습니다. 한 가지 경우는 관리 되는 인스턴스에 데이터베이스를 생성 합니다. 이 리소스는 Azure portal, PowerShell, Azure CLI 및 REST API를 통해 노출 됩니다.

관리 되는 인스턴스를 제공 하는 Azure 플랫폼 서비스의 백업에 대 한 Azure Storage, 원격 분석을 위한 Azure Event Hubs, Azure Active Directory 인증을 위해 Azure Key Vault에 대 한 투명 한 데이터 암호화 (TDE)와 같은 Azure 서비스 및 종속 보안 및 지원 가능성 기능입니다. 관리 되는 인스턴스는 이러한 서비스에 연결 합니다.

모든 통신은 암호화 및 인증서를 사용 하 여 서명 합니다. 통신 당사자를 관리 되는 신뢰할 수 있는지를 확인 하려면 인스턴스 지속적으로 인증서 해지 목록을 통해 이러한 인증서를 확인 합니다. 인증서 해지 되 면 관리 되는 인스턴스 데이터를 보호에 대 한 연결을 닫습니다.

## <a name="high-level-connectivity-architecture"></a>고급 연결 아키텍처

높은 수준에서 관리 되는 인스턴스는 서비스 구성 요소 집합입니다. 이러한 구성 요소는 고객의 가상 네트워크 서브넷 내에서 실행 되는 격리 된 가상 머신의 전용된 집합에서 호스트 됩니다. 이러한 컴퓨터를 가상 클러스터를 형성합니다.

가상 클러스터를 여러 관리 되는 인스턴스를 호스팅할 수 있습니다. 필요한 경우 클러스터 자동으로 확장 하거나이 고객 서브넷의 프로 비전 된 인스턴스 수가 변경 될 때 축소 합니다.

고객 응용 프로그램 관리 되는 인스턴스에 연결할 수 있습니다 및 쿼리 수 및 가상 네트워크 피어 링 된 가상 네트워크 내에서 데이터베이스를 업데이트 또는 네트워크 VPN 또는 Azure ExpressRoute 연결. 이 네트워크는 개인 IP 주소 및 끝점에 사용 해야 합니다.  

![연결 아키텍처 다이어그램](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft 관리 및 배포 서비스는 가상 네트워크 외부에서 실행 합니다. 관리 되는 인스턴스 및 Microsoft 서비스 공용 IP 주소를 가진 끝점을 통해 연결 합니다. 관리 되는 인스턴스를 만들 때 아웃 바운드 연결을 수신 쪽에 처럼 연결 확인이 공용 IP 주소에서 제공 되는 네트워크 주소 변환 (NAT)는입니다.

고객의 가상 네트워크를 통해 관리 트래픽이 흐릅니다. 즉, 가상 네트워크의 인프라의 요소를 실패 하 고 사용할 수 없게 되는 인스턴스를 만들어 관리 트래픽을 손상 시킬 수는 있습니다.

> [!IMPORTANT]
> Microsoft는 고객 환경을 개선 하며 서비스 가용성, Azure 가상 네트워크 인프라 요소에 네트워크 의도 정책을 적용 합니다. 정책 관리 되는 인스턴스 작동 원리를 발생할 수 있습니다. 이 플랫폼 메커니즘은 네트워킹 요구 사항을 사용자에 게 투명 하 게 통신합니다. 정책의 주요 목표는 네트워크 구성 오류를 방지 하기 위해 관리 되는 인스턴스 일반 작업을 확인 합니다. 관리 되는 인스턴스를 삭제 하면 네트워크 의도 정책 에서도 제거 됩니다.

## <a name="virtual-cluster-connectivity-architecture"></a>가상 클러스터 연결 아키텍처

관리 되는 인스턴스 연결 아키텍처에 자세히를 살펴보겠습니다. 다음 다이어그램은 가상 클러스터의 개념적 레이아웃을 보여 줍니다.

![가상 클러스터의 연결 아키텍처](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

클라이언트 형식은 수 있는 호스트 이름을 사용 하 여 관리 되는 인스턴스에 연결할 `<mi_name>.<dns_zone>.database.windows.net`합니다. 이 호스트 이름은 공용 도메인 이름 시스템 (DNS) 영역에 등록 하 고 공개적으로 확인할 수 있지만 개인 IP 주소로 확인 합니다. `zone-id` 클러스터를 만들 때 자동으로 생성 됩니다. 관리 되는 보조 인스턴스를 호스팅하는 새로 만든된 클러스터인 경우 기본 클러스터와 해당 영역 ID를 공유 합니다. 자세한 내용은 [자동 장애 조치 그룹을 사용 하 여 여러 데이터베이스의 투명 하 고 조정 된 장애 조치를 사용 하도록 설정 하려면](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)합니다.

이 개인 IP 주소는 관리 되는 인스턴스의 내부 부하 분산 장치에 속합니다. 부하 분산 장치에는 관리 되는 인스턴스의 게이트웨이 트래픽을 전달합니다. 여러 관리 되는 인스턴스를 동일한 클러스터 내에서 실행 하기 때문에 게이트웨이 관리 되는 인스턴스의 호스트 이름을 사용 하 여 올바른 SQL 엔진 서비스에 트래픽을 리디렉션할 수입니다.

사용 하 여 관리 되는 인스턴스 연결 관리 및 배포 서비스를 [관리 끝점](#management-endpoint) 매핑되는 외부 부하 분산 장치는 합니다. 미리 정의 된 집합이 관리 되는 인스턴스의 관리 구성 요소만 사용 하는 포트에서 수신 된 경우에 트래픽은 노드로 라우팅됩니다. 노드의 기본 제공 방화벽 Microsoft IP 범위 에서만에서 트래픽을 허용 하도록 설정 됩니다. 인증서 관리 구성 요소와 관리 평면 간의 모든 통신을 상호 인증합니다.

## <a name="management-endpoint"></a>관리 엔드포인트

Microsoft에서 관리 끝점을 사용 하 여 관리 되는 인스턴스를 관리 합니다. 이 끝점은 가상 클러스터 인스턴스의 내부 있습니다. 관리 끝점은 네트워크 수준에서 기본 제공 방화벽에 의해 보호 됩니다. 응용 프로그램 수준에서 상호 인증서 인증으로 보호 됩니다. 참조 끝점의 IP 주소를 찾으려면 [관리 끝점의 IP 주소를 확인할](sql-database-managed-instance-find-management-endpoint-ip-address.md)합니다.

경우 (마찬가지로 백업 및 감사 로그) 관리 되는 인스턴스 내에서 시작 하는 연결, 관리 끝점의 공용 IP 주소에서 시작 하려면 트래픽이 표시 됩니다. 관리 되는 인스턴스의 IP 주소만 허용 하도록 방화벽 규칙을 설정 하 여 관리 되는 인스턴스에서 공용 서비스에 대 한 액세스를 제한할 수 있습니다. 자세한 내용은 [관리 되는 인스턴스의 기본 제공 방화벽 확인](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)합니다.

> [!NOTE]
> 관리 되는 인스턴스의 지역 내에 있는 Azure 서비스를 이동 하는 트래픽 라우팅 최적화 되 고 하는 이유로 든 관리 인스턴스 관리 끝점 공용 IP 주소로 nat 된 없습니다. 이런 이유로 가장 일반적으로 저장소에 대 한 IP 기반 방화벽 규칙을 사용 해야 하는 경우 서비스는 관리 되는 인스턴스에서 다른 지역에 포함 되도록 해야 합니다.

## <a name="network-requirements"></a>네트워크 요구 사항

가상 네트워크 내에서 전용된 서브넷에 관리 되는 인스턴스를 배포 합니다. 서브넷에는 이러한 특징이 있어야 합니다.

- **전용된 서브넷:** 관리 되는 인스턴스의 서브넷, 연결 된 다른 클라우드 서비스를 포함할 수 없습니다 및 게이트웨이 서브넷을 될 수 없습니다. 서브넷 리소스 하지만 관리 되는 인스턴스를 포함할 수 없습니다 및 서브넷의 리소스를 나중에 추가할 수 없습니다.
- **NSG(네트워크 보안 그룹):** 가상 네트워크와 연결 된 NSG를 정의 해야 합니다 [인바운드 보안 규칙](#mandatory-inbound-security-rules) 하 고 [아웃 바운드 보안 규칙](#mandatory-outbound-security-rules) 다른 규칙 보다 먼저 합니다. 포트 1433에서 트래픽을 필터링 하 여 관리 되는 인스턴스 데이터 끝점에 대 한 액세스를 제어 하는 NSG를 사용할 수 있습니다 및 11000-11999 위해 관리 되는 인스턴스를 구성할 때 포트 연결을 리디렉션합니다.
- **사용자 정의 경로 (UDR) 테이블:** 가상 네트워크와 연결 된 UDR 테이블 특정 있어야 [항목이](#user-defined-routes)합니다.
- **서비스 끝점이 없습니다.** 서비스 끝점이 관리 되는 인스턴스의 서브넷과 연결 해야 합니다. 가상 네트워크를 만들 때 서비스 끝점 옵션은 사용 되지 않음을 확인 합니다.
- **충분 한 IP 주소:** 관리 되는 인스턴스 서브넷에는 최소 16 개의 IP 주소가 있어야 합니다. 권장 되는 최소는 32 IP 주소. 자세한 내용은 [관리 되는 인스턴스의 서브넷 크기 결정](sql-database-managed-instance-determine-size-vnet-subnet.md)합니다. 관리 되는 인스턴스를 배포할 수 있습니다 [기존 네트워크](sql-database-managed-instance-configure-vnet-subnet.md) 만족 하도록 구성 하 고 나면 [관리 되는 인스턴스에 대 한 네트워킹 요구 사항을](#network-requirements)합니다. 그렇지 않은 경우 만듭니다는 [새 네트워크 및 서브넷](sql-database-managed-instance-create-vnet-subnet.md)합니다.

> [!IMPORTANT]
> 대상 서브넷에 이러한 특성이 없는 경우에 관리 되는 새 인스턴스를 배포할 수 없습니다. 관리 되는 인스턴스를 만들면 네트워크 의도 정책 네트워킹 설정 정책을 준수 하지 않는 변경을 방지 하기 위해 서브넷에 적용 됩니다. 서브넷에서 마지막 인스턴스를 제거한 후 네트워크 의도 정책 에서도 제거 됩니다.

### <a name="mandatory-inbound-security-rules"></a>필수 인바운드 보안 규칙

| Name       |포트                        |프로토콜|원본           |대상|액션(Action)|
|------------|----------------------------|--------|-----------------|-----------|------|
|관리  |9000, 9003, 1438, 1440, 1452|TCP     |모두              |MI SUBNET  |허용 |
|mi_subnet   |모두                         |모두     |MI SUBNET        |MI SUBNET  |허용 |
|health_probe|모두                         |모두     |AzureLoadBalancer|MI SUBNET  |허용 |

### <a name="mandatory-outbound-security-rules"></a>필수 아웃바운드 보안 규칙

| Name       |포트          |프로토콜|원본           |대상|액션(Action)|
|------------|--------------|--------|-----------------|-----------|------|
|관리  |80, 443, 12000|TCP     |MI SUBNET        |AzureCloud |허용 |
|mi_subnet   |모두           |모두     |MI SUBNET        |MI SUBNET  |허용 |

> [!IMPORTANT]
> 포트 9000에 대 한 하나의 인바운드 규칙만 9003, 한지 확인 합니다. 포트 80, 443, 12000에 대 한 아웃 바운드 규칙 1438, 1440, 1452 및 하나입니다. 인스턴스에 Azure Resource Manager를 통해 인바운드 및 아웃 바운드 규칙이 각 포트에 대해 별도로 구성 된 경우 배포 실패를 프로 비전을 관리 합니다. 배포 오류 코드로 실패 이러한 포트 별도 규칙에 있는 경우 `VnetSubnetConflictWithIntendedPolicy`

\* MI 서브넷 폼 10.x.x.x/y의 서브넷에 IP 주소 범위를 가리킵니다. 서브넷 속성에서 Azure portal에서이 정보를 찾을 수 있습니다.

> [!IMPORTANT]
> 필요한 인바운드 보안 규칙에서 트래픽을 허용 하지만 _모든_ 포트 9000 원본이, 9003, 1438, 1440 및 1452, 이러한 포트는 기본 제공 방화벽으로 보호 됩니다. 자세한 내용은 [관리 끝점 주소를 확인할](sql-database-managed-instance-find-management-endpoint-ip-address.md)합니다.
> [!NOTE]
> 트랜잭션 복제를 사용 하 여 관리 되는 경우 및 게시자 또는 배포자 인스턴스에서 데이터베이스를 사용 하는 경우 서브넷의 보안 규칙에서 445 포트 (TCP 아웃 바운드)를 엽니다. 이 포트는 Azure 파일 공유에 대 한 액세스를 허용 됩니다.

### <a name="user-defined-routes"></a>사용자 정의 경로

|Name|주소 접두사|다음 홉|
|----|--------------|-------|
|subnet_to_vnetlocal|MI SUBNET|가상 네트워크|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|인터넷|
|mi-13-96-13-nexthop-internet|13.96.0.0/13|인터넷|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|인터넷|
|mi-20-8-nexthop-internet|20.0.0.0/8|인터넷|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|인터넷|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|인터넷|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|인터넷|
|mi-51-8-nexthop-internet|51.0.0.0/8|인터넷|
|mi-52-8-nexthop-internet|52.0.0.0/8|인터넷|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|인터넷|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|인터넷|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|인터넷|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|인터넷|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|인터넷|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|인터넷|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|인터넷|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|인터넷|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|인터넷|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|인터넷|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|인터넷|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|인터넷|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|인터넷|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|인터넷|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|인터넷|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|인터넷|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|인터넷|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|인터넷|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|인터넷|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|인터넷|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|인터넷|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|인터넷|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|인터넷|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|인터넷|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|인터넷|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|인터넷|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|인터넷|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|인터넷|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|인터넷|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|인터넷|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|인터넷|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|인터넷|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|인터넷|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|인터넷|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|인터넷|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|인터넷|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|인터넷|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|인터넷|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|인터넷|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|인터넷|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|인터넷|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|인터넷|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|인터넷|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|인터넷|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|인터넷|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|인터넷|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|인터넷|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|인터넷|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|인터넷|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|인터넷|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|인터넷|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|인터넷|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|인터넷|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|인터넷|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|인터넷|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|인터넷|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|인터넷|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|인터넷|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|인터넷|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|인터넷|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|인터넷|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|인터넷|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|인터넷|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|인터넷|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|인터넷|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|인터넷|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|인터넷|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|인터넷|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|인터넷|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|인터넷|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|인터넷|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|인터넷|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|인터넷|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|인터넷|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|인터넷|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|인터넷|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|인터넷|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|인터넷|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|인터넷|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|인터넷|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|인터넷|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|인터넷|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|인터넷|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|인터넷|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|인터넷|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|인터넷|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|인터넷|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|인터넷|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|인터넷|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|인터넷|
||||

또한 가상 네트워크 게이트웨이 또는 가상 네트워크 어플라이언스 (NVA)를 통해 대상으로 온-프레미스 개인 IP 범위에 있는 트래픽을 라우팅하는 경로 테이블에 항목을 추가할 수 있습니다.

사용자 지정 DNS 서버에서 호스트 이름을 확인할 수 있어야 가상 네트워크에서 사용자 지정 DNS에 포함 된 경우 \*. core.windows.net 영역입니다. 추가 기능을 사용 하 여 같은 Azure AD 인증 추가 Fqdn을 해결 해야 할 수 있습니다. 자세한 내용은 [사용자 지정 DNS 설정](sql-database-managed-instance-custom-dns.md)합니다.

## <a name="next-steps"></a>다음 단계

- 개요를 보려면 [SQL Database 데이터 보안 고급](sql-database-managed-instance.md)합니다.
- 에 대해 알아봅니다 하는 방법 [새로운 Azure virtual network 설정](sql-database-managed-instance-create-vnet-subnet.md) 요소나 [기존 Azure virtual network](sql-database-managed-instance-configure-vnet-subnet.md) 관리 되는 인스턴스를 배포할 수 있습니다.
- [서브넷의 크기를 계산](sql-database-managed-instance-determine-size-vnet-subnet.md) 관리 되는 인스턴스를 배포 하려는 합니다.
- 관리 되는 인스턴스를 만드는 방법에 알아봅니다.
  - [Azure Portal](sql-database-managed-instance-get-started.md)
  - 사용 하 여 [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)합니다.
  - 사용 하 여 [는 Azure Resource Manager 템플릿을](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)합니다.
  - 사용 하 여 [(포함 하는 SSMS를 사용 하 여 JumpBox 사용)는 Azure Resource Manager 템플릿을](https://portal.azure.com/)합니다. 

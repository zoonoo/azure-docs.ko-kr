---
title: SQL 관리 인스턴스 마이그레이션을 위한 네트워크 토폴로지
titleSuffix: Azure Database Migration Service
description: Azure 데이터베이스 마이그레이션 서비스를 사용하여 Azure SQL Database 관리 인스턴스 마이그레이션의 원본 및 대상 구성을 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 48485b7ba0f846afa737454b092a6c1ee986b737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254952"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Azure 데이터베이스 마이그레이션 서비스를 사용하는 Azure SQL DB 관리 인스턴스 마이그레이션에 대한 네트워크 토폴로지

이 문서에서는 온-프레미스 SQL 서버에서 Azure SQL Database 관리 인스턴스로의 포괄적인 마이그레이션 환경을 제공하기 위해 Azure Database 마이그레이션 서비스가 사용할 수 있는 다양한 네트워크 topologies에 대해 설명합니다.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>하이브리드 워크로드에 대해 구성된 Azure SQL Database Managed Instance 

Azure SQL Database Managed Instance가 온-프레미스 네트워크에 연결되어 있는 경우 이 토폴로지를 사용합니다. 이 방법은 가장 간소화된 네트워크 라우팅을 제공하고 마이그레이션 동안 최대 데이터 처리량을 제공합니다.

![하이브리드 워크로드에 대한 네트워크 토폴로지](media/resource-network-topologies/hybrid-workloads.png)

**요구 사항**

- 이 시나리오에서는 Azure SQL Database 관리 인스턴스 및 Azure 데이터베이스 마이그레이션 서비스 인스턴스는 동일한 Microsoft Azure 가상 네트워크에서 만들어지지만 다른 서브넷을 사용합니다.  
- 이 시나리오에서 사용되는 가상 네트워크는 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [VPN을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)사용하여 온-프레미스 네트워크에도 연결됩니다.

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>온-프레미스 네트워크에서 격리된 Azure SQL Database Managed Instance

작업 환경에 다음과 같은 시나리오 중 하나 이상이 필요한 경우 이 네트워크 토폴로지를 사용합니다.

- Azure SQL Database 관리형 인스턴스는 온-프레미스 연결에서 격리되지만 Azure 데이터베이스 마이그레이션 서비스 인스턴스는 온-프레미스 네트워크에 연결됩니다.
- 역할 기반 액세스 제어(RBAC) 정책이 있는 경우 사용자가 Azure SQL Database 관리 인스턴스를 호스팅하는 동일한 구독에 액세스하도록 제한해야 합니다.
- Azure SQL 데이터베이스 관리 인스턴스 및 Azure 데이터베이스 마이그레이션 서비스에 사용되는 가상 네트워크는 서로 다른 구독에 있습니다.

![온-프레미스 네트워크에서 격리된 Managed Instance의 네트워크 토폴로지](media/resource-network-topologies/mi-isolated-workload.png)

**요구 사항**

- 이 시나리오에 Azure Database 마이그레이션 서비스가 사용하는 가상 네트워크도 (또는https://docs.microsoft.com/azure/expressroute/expressroute-introduction) [VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)중 하나를 사용하여 온-프레미스 네트워크에 연결되어야 합니다.
- Azure SQL Database 관리 인스턴스와 Azure 데이터베이스 마이그레이션 서비스에 사용되는 가상 네트워크 간에 [VNet 네트워크 피어링을](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 설정합니다.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>클라우드-클라우드 마이그레이션: 공유 가상 네트워크

원본 SQL Server가 Azure VM에서 호스팅되고 Azure SQL Database 관리 인스턴스 및 Azure 데이터베이스 마이그레이션 서비스와 동일한 가상 네트워크를 공유하는 경우 이 토폴로지에서 사용합니다.

![공유 VNet을 사용하여 클라우드-클라우드 마이그레이션을 위한 네트워크 토폴로지](media/resource-network-topologies/cloud-to-cloud.png)

**요구 사항**

- 추가 요구 사항 없음

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>클라우드에서 클라우드로 마이그레이션: 격리된 가상 네트워크

작업 환경에 다음과 같은 시나리오 중 하나 이상이 필요한 경우 이 네트워크 토폴로지를 사용합니다.

- Azure SQL Database 관리 인스턴스는 격리된 가상 네트워크에 프로비전됩니다.
- 역할 기반 액세스 제어(RBAC) 정책이 있는 경우 사용자가 Azure SQL Database 관리 인스턴스를 호스팅하는 동일한 구독에 액세스하도록 제한해야 합니다.
- Azure SQL 데이터베이스 관리 인스턴스 및 Azure 데이터베이스 마이그레이션 서비스에 사용되는 가상 네트워크는 서로 다른 구독에 있습니다.

![격리된 VNet을 사용하여 클라우드-클라우드 마이그레이션을 위한 네트워크 토폴로지](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**요구 사항**

- Azure SQL Database 관리 인스턴스와 Azure 데이터베이스 마이그레이션 서비스에 사용되는 가상 네트워크 간에 [VNet 네트워크 피어링을](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 설정합니다.

## <a name="inbound-security-rules"></a>인바운드 보안 규칙

| **이름**   | **포트** | **프로토콜** | **소스** | **대상** | **작업** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | 모두      | 모두          | DMS SUBNET | 모두             | Allow      |

## <a name="outbound-security-rules"></a>아웃바운드 보안 규칙

| **이름**                  | **포트**                                              | **프로토콜** | **소스** | **대상**           | **작업** | **규칙이 필요한 이유**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 관리                | 443,9354                                              | TCP          | 모두        | 모두                       | Allow      | 서비스 버스 및 Azure Blob 저장소를 통한 관리 평면 통신입니다. <br/>(Microsoft 피어링을 사용하도록 설정한 경우 이 규칙이 필요하지 않을 수 있습니다.)                                                             |
| 진단               | 12000                                                 | TCP          | 모두        | 모두                       | Allow      | DMS는 이 규칙을 사용하여 문제 해결을 위한 진단 정보를 수집합니다.                                                                                                                      |
| SQL 원본 서버         | 1433(또는 SQL Server가 수신 대기 중인 TCP IP 포트) | TCP          | 모두        | 온-프레미스 주소 공간 | Allow      | DMS의 SQL Server 원본 연결 <br/>(사이트 간 연결이 있는 경우 이 규칙이 필요하지 않을 수 있습니다.)                                                                                       |
| SQL Server 명명된 인스턴스 | 1434                                                  | UDP          | 모두        | 온-프레미스 주소 공간 | Allow      | DMS의 SQL Server 명명된 인스턴스 원본 연결 <br/>(사이트 간 연결이 있는 경우 이 규칙이 필요하지 않을 수 있습니다.)                                                                        |
| SMB 공유                 | 445                                                   | TCP          | 모두        | 온-프레미스 주소 공간 | Allow      | Azure SQL Database MI 및 Azure VM의 SQL Server로 마이그레이션할 데이터베이스 백업 파일을 저장하는 DMS용 SMB 네트워크 공유 <br/>(사이트 간 연결이 있는 경우 이 규칙이 필요하지 않을 수 있습니다.) |
| DMS_subnet                | 모두                                                   | 모두          | 모두        | DMS_Subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>참조

- [Azure SQL Database Managed Instance로 SQL Server 마이그레이션](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Azure 데이터베이스 마이그레이션 서비스 사용에 대한 필수 구성 조건 개요](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Azure 포털을 사용하여 가상 네트워크 만들기](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>다음 단계

- Azure 데이터베이스 마이그레이션 서비스에 대한 개요는 [Azure 데이터베이스 마이그레이션 서비스란](dms-overview.md)무엇입니까?
- Azure 데이터베이스 마이그레이션 서비스의 지역 가용성에 대한 최신 정보는 [지역별 제품](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) 페이지를 참조하십시오.

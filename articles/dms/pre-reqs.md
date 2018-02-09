---
title: "Azure Database Migration Service 사용을 위한 필수 구성 요소 개요 | Microsoft Docs"
description: "Azure Database Migration Service를 사용하여 데이터베이스 마이그레이션을 수행하기 위한 필수 구성 요소의 개요를 알아봅니다."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: a103bb4802bc4a20b6d82f0c6bb427133d9e5643
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2018
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Azure Database Migration Service 사용을 위한 필수 구성 요소 개요
데이터베이스 마이그레이션을 수행할 때 Azure Database Migration Service가 원활히 실행되도록 하기 위해 필요한 필수 구성 요소가 몇 가지 있습니다. 일부 필수 구성 요소는 서비스가 지원하는 모든 시나리오(원본-대상 쌍)에 적용되는 반면에 특정 시나리오에만 적용되는 필수 구성 요소도 있습니다.

Azure Database Migration Service 사용과 관련된 필구 구성 요소는 다음 섹션에 나열되어 있습니다.

## <a name="prerequisites-common-across-migration-scenarios"></a>마이그레이션 시나리오의 공통적인 필수 구성 요소
지원되는 모든 마이그레이션 시나리오에 공통적인 Azure Database Migration Service 필구 구성 요소는 다음을 수행해야 합니다.
- Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 VNET을 만듭니다. 이를 통해 [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) 또는 [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용하여 온-프레미스 원본 서버에서 사이트 간 연결을 제공합니다.
- Azure VNET(Virtual Network) 네트워크 보안 그룹 규칙이 443, 53, 9354, 445, 12000과 같은 통신 포트를 차단하지 않는지 확인합니다. Azure VNET NSG 트래픽 필터링에 대한 자세한 정보는 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg) 문서를 참조하세요.
- 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있도록 방화벽 규칙을 추가해야 합니다.

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>SQL Server를 Azure SQL Database로 마이그레이션하기 위한 필수 구성 요소 
모든 마이그레이션 시나리오에 공통적인 Azure Database Migration Service 필수 구성 요소 외에도 한 시나리오나 다른 시나리오에만 적용되는 필수 구성 요소가 있습니다.

Azure Database Migration Service를 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션을 수행하는 경우, 모든 마이그레이션 시나리오에 공통적인 필수 구성 요소 외에도 다음과 같은 필구 구성 요소를 충족해야 합니다.
- [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
- 문서 [서버 네트워크 프로토콜 사용 또는 사용 안 함](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)의 지침을 수행하여 SQL Server Express를 설치하는 동안 기본적으로 사용 안 함으로 설정되어 있는 TCP/IP 프로토콜을 사용하도록 설정합니다.
- [Azure Portal에서 Azure SQL Database 만들기](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-portal) 문서의 세부 지침을 수행하여 Azure SQL Database 인스턴스를 만듭니다.
- [Data Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595) v3.3 이상을 다운로드 및 설치합니다.
- Azure Database Migration Service가 원본 데이터베이스에 액세스할 수 있도록 Windows 방화벽을 엽니다.
- 대상 데이터베이스에 대한 Azure Database Migration Service 액세스를 허용하도록 Azure SQL Database 서버에 서버 수준 [방화벽 규칙](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure)을 만듭니다. Azure Database Migration Service에 사용되는 VNET의 서브넷 범위를 제공합니다.
- 원본 SQL Server 인스턴스에 연결하는 데 사용되는 자격 증명에는 [CONTROL SERVER](https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-server-permissions-transact-sql) 권한이 있어야 합니다.
- 대상 Azure SQL Database 인스턴스에 연결하는 데 사용되는 자격 증명에는 대상 Azure SQL DB에 대한 CONTROL DATABASE 권한이 있어야 합니다.

   > [!NOTE]
   > Azure Database Migration Service를 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션을 수행하기 위해 필요한 전체 필수 구성 요소 목록은 [SQL Server를 Azure SQL Database로 마이그레이션](https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-to-azure-sql) 자습서를 참조하세요.
   > 

## <a name="next-steps"></a>다음 단계
공개 미리 보기 중 Azure Database Migration Service 및 국가별 가용성에 대한 개요는 [Azure Database Migration Service 미리 보기란 무엇인가요?](dms-overview.md) 문서를 참조하세요. 
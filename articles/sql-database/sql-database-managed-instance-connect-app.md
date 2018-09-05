---
title: Azure SQL Database Managed Instance 연결 응용 프로그램 | Microsoft Docs
description: 이 아티클에서는 Azure SQL Database Managed Instance에 응용 프로그램을 연결하는 방법을 설명합니다.
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 82e8836892b033ccbb3c3ad9806257348afe3702
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818405"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>응용 프로그램을 Azure SQL Database Managed Instance에 연결

현재 응용 프로그램을 호스트하는 방법과 위치를 결정하는 여러 가지 옵션이 있습니다. 
 
Azure App Service 또는 Azure VNet(가상 네트워크) 통합 옵션 중 일부(예: Azure App Service 환경, Virtual Machine, Virtual Machine Scale Set)를 사용하여 클라우드에서 응용 프로그램을 호스팅할 수 있습니다. 하이브리드 클라우드 접근 방법을 사용하고 응용 프로그램을 온-프레미스에 유지할 수도 있습니다. 
 
어떤 방법을 선택하든지 Managed Instance(미리 보기)에 연결할 수 있습니다.  

![고가용성](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>동일한 VNet 내에서 응용 프로그램 연결 

이 시나리오가 가장 간단합니다. VNet 내의 가상 머신은 다른 서브넷 내에 있더라도 서로 직접 연결할 수 있습니다. 즉, Azure 응용 프로그램 환경 또는 Virtual Machine 내 응용 프로그램을 연결하려면 연결 문자열을 적절하게 설정하기만 하면 됩니다.  
 
연결을 설정할 수 없는 경우에 네트워크 보안 그룹을 응용 프로그램 서브넷에 설정했는지 확인합니다. 이 경우에 리디렉션할 11000~12000 포트 범위뿐만 아니라 SQL 포트 1433에 대산 아웃바운드 연결을 열어야 합니다. 

## <a name="connect-an-application-inside-a-different-vnet"></a>다른 VNet 내에서 응용 프로그램 연결 

이 시나리오에서는 Managed Instance가 고유한 VNet의 개인 IP 주소를 포함하기 때문에 조금 복잡합니다. 연결하려면 Managed Instance를 배포할 때 응용 프로그램이 VNet에 액세스해야 합니다. 따라서 먼저 응용 프로그램과 Managed Instance VNet이 연결되어야 합니다. 이 시나리오가 작동하기 위해 VNet이 동일한 구독에 있을 필요는 없습니다. 
 
VNet을 연결하는 두 가지 옵션이 있습니다. 
- [Azure Virtual Network 피어링](../virtual-network/virtual-network-peering-overview.md) 
- VNet 간 VPN 게이트웨이([Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
피어링이 Microsoft 백본 네트워크를 사용하기 때문에 피어링 옵션은 적합한 기능입니다. 따라서 연결성 관점에서 피어링된 VNet 및 동일한 VNet에 있는 가상 머신 간에 대기 시간이 눈에 띄는 차이점은 없습니다. VNet 피어링은 동일한 지역의 네트워크로 제한됩니다.  
 
> [!IMPORTANT]
> Managed Instance에 대한 VNet 피어링 시나리오는 [전역 가상 네트워크 피어 링 제약 조건](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)으로 인해 동일한 지역의 네트워크로 제한됩니다. 

## <a name="connect-an-on-premises-application"></a>온-프레미스 응용 프로그램 연결 

Managed Instance는 개인 IP 주소를 통해서만 액세스할 수 있습니다. 온-프레미스에서 액세스하기 위해 응용 프로그램과 Managed Instance VNet 간에 사이트 간 연결을 만들어야 합니다. 
 
온-프레미스를 Azure VNet에 연결하는 두 개의 옵션이 있습니다. 
- 사이트 간 VPN 연결([Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [ExpressRoute](../expressroute/expressroute-introduction.md) 연결  
 
온-프레미스와 Azure 연결을 성공적으로 설정하고 Managed Instance에 연결을 설정할 수 없는 경우 리디렉션을 위해 방화벽에서 11000~12000 포트 범위뿐만 아니라 SQL 포트 1433의 아웃바운드 연결이 열려 있는지 확인합니다. 

## <a name="connect-an-azure-app-service-hosted-application"></a>Azure App Service 호스트 응용 프로그램 연결 

Managed Instance는 개인 IP 주소를 통해서만 액세스할 수 있습니다. 따라서 Azure App Service에서 액세스하려면 먼저 응용 프로그램과 Managed Instance VNet 간에 연결을 만들어야 합니다. [Azure Virtual Network에 앱 통합](../app-service/web-sites-integrate-with-vnet.md)을 참조하세요.  
 
문제 해결은 [VNet 및 응용 프로그램 문제 해결](../app-service/web-sites-integrate-with-vnet.md#troubleshooting)을 참조하세요. 연결을 설정할 수 없는 경우 [네트워킹 구성 동기화](sql-database-managed-instance-sync-network-configuration.md)를 시도하세요. 
 
특수한 경우에 Azure App Service를 Managed Instance에 연결합니다. 즉, Managed Instance VNet에 피어링된 네트워크에 Azure App Service를 통합하는 경우입니다. 해당 경우에는 다음 구성을 설정해야 합니다. 

- Managed Instance VNet에는 게이트웨이가 없어야 합니다.  
- Managed Instance VNet에는 원격 게이트웨이 사용 옵션을 설정해야 합니다. 
- 피어링된 VNet에는 게이트웨이 전송 허용 옵션을 설정해야 합니다. 
 
이 시나리오는 다음 다이어그램에서 설명합니다.

![통합 앱 피어링](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>개발자 상자에서 응용 프로그램 연결 

Managed Instance는 개인 IP 주소를 통해서만 액세스할 수 있습니다. 따라서 개발자 상자에서 액세스하려면 먼저 개발자 상자와 Managed Instance VNet 간에 연결을 만들어야 합니다.  
 
네이티브 Azure 인증서 인증을 사용하여 VNet에 지점 및 사이트 간 연결 구성 아티클([Azure Portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md))에서는 수행 방법을 자세히 설명합니다. 

## <a name="required-versions-of-drivers-and-tools"></a>드라이버 및 도구의 필요한 버전

Managed Instance에 연결하려면 다음과 같은 버전 이상의 도구와 드라이버를 사용하는 것이 좋습니다.

| 드라이버/도구 | 버전 |
| --- | --- |
|.NET Framework | 4.6.1(또는 .NET Core) | 
|ODBC 드라이버    | v17 |
|PHP 드라이버 | 5.2.0 |
|JDBC 드라이버    | 6.4.0 |
|Node.js 드라이버 | 2.1.1 |
|OLEDB 드라이버   | 18.0.2.0 |
|SSMS   | 17.8.1 [이상](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) |

## <a name="next-steps"></a>다음 단계

- Managed Instance에 대한 자세한 내용은 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- Managed Instance를 새로 만드는 방법을 보여 주는 자습서에 대해서는 [Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.

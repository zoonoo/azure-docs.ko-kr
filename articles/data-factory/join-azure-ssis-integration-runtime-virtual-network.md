---
title: Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인
description: Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: e94eef3072b9636c8022a5949b05519c1554cb9e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585783"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인

Azure 데이터 팩터리에서 SQL Server 통합 서비스(SSIS)를 사용하는 경우 다음 시나리오에서 Azure-SSIS 통합 런타임(IR)을 Azure 가상 네트워크에 조인해야 합니다.

- 자체 호스팅 IR을 프록시로 구성하거나 관리하지 않고 Azure-SSIS IR에서 실행되는 SSIS 패키지의 온-프레미스 데이터 저장소에 연결하려고 합니다. 

- IP 방화벽 규칙/가상 네트워크 서비스 끝점 또는 개인 끝점이 있는 관리되는 인스턴스가 있는 Azure SQL 데이터베이스에서 SSIS 카탈로그 데이터베이스(SSISDB)를 호스트하려고 합니다. 

- Azure-SSIS IR에서 실행되는 SSIS 패키지의 가상 네트워크 서비스 끝점으로 구성된 Azure 리소스에 연결하려고 합니다.

- Azure-SSIS IR에서 실행되는 SSIS 패키지의 IP 방화벽 규칙으로 구성된 데이터 저장소/리소스에 연결하려고 합니다.

데이터 팩터리에서는 Azure-SSIS IR을 클래식 배포 모델 또는 Azure 리소스 관리자 배포 모델을 통해 생성된 가상 네트워크에 조인할 수 있습니다.

> [!IMPORTANT]
> 클래식 가상 네트워크가 더 이상 사용되지 않으므로 대신 Azure Resource Manager 가상 네트워크를 사용하십시오.  이미 클래식 가상 네트워크를 사용하는 경우 가능한 한 빨리 Azure Resource Manager 가상 네트워크로 전환합니다.

[가상 네트워크 자습서에 가입하기 위해 Azure-SQL 서버 통합 서비스(SSIS) 통합 런타임(IR)을 구성하는](tutorial-deploy-ssis-virtual-network.md) 것은 Azure 포털을 통해 최소 단계를 보여 주며, 가상 네트워크 자습서를 구성합니다. 이 문서에서는 자습서를 확장하고 모든 선택적 작업에 대해 설명합니다.

- 가상 네트워크(클래식)를 사용하는 경우.
- Azure-SSIS IR에 대한 공용 IP 주소를 가져오는 경우
- 자신의 DNS(도메인 이름 시스템) 서버를 사용하는 경우.
- 서브넷에서 NSG(네트워크 보안 그룹)를 사용하는 경우
- Azure ExpressRoute 또는 사용자 정의 경로(UDR)를 사용하는 경우
- 사용자 지정된 Azure-SSIS IR을 사용하는 경우
- Azure Powershell 프로비전을 사용하는 경우.

## <a name="access-to-on-premises-data-stores"></a>온-프레미스 데이터 저장소 액세스

SSIS 패키지가 온-프레미스 데이터 저장소에 액세스하는 경우 Azure-SSIS IR을 온-프레미스 네트워크에 연결된 가상 네트워크에 조인할 수 있습니다. 또는 Azure-SSIS IR에 대한 프록시로 자체 호스팅 IR을 구성하고 관리할 수 있습니다. 자세한 내용은 [Azure-SSIS IR의 프록시로 자체 호스팅IR 구성을](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)참조하십시오. 

Azure-SSIS IR을 가상 네트워크에 조인할 때 다음 중요한 사항을 기억하십시오. 

- 온-프레미스 네트워크에 연결된 가상 네트워크가 없는 경우 먼저 Azure-SSIS IR에 가입할 [Azure Resource Manager 가상 네트워크를](../virtual-network/quick-create-portal.md#create-a-virtual-network) 만듭니다. 그런 다음 해당 가상 네트워크에서 온-프레미스 네트워크로 사이트 간 [VPN 게이트웨이 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) 또는 [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 연결을 구성합니다. 

- Azure Resource Manager 가상 네트워크가 Azure-SSIS IR과 동일한 위치에 있는 온-프레미스 네트워크에 이미 연결되어 있는 경우 해당 가상 네트워크에 IR을 가입할 수 있습니다. 

- 클래식 가상 네트워크가 Azure-SSIS IR과 다른 위치에 있는 온-프레미스 네트워크에 이미 연결되어 있는 경우 Azure-SSIS IR에 가입할 [Azure Resource Manager 가상 네트워크를](../virtual-network/quick-create-portal.md#create-a-virtual-network) 만들 수 있습니다. 그런 다음 [클래식-Azure 리소스 관리자 가상 네트워크 연결을 구성합니다.](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 
 
- Azure Resource Manager 가상 네트워크가 Azure-SSIS IR과 다른 위치에 있는 온-프레미스 네트워크에 이미 연결되어 있는 경우 먼저 Azure-SSIS IR에 가입할 [Azure Resource Manager 가상 네트워크를](../virtual-network/quick-create-portal.md#create-a-virtual-network) 만들 수 있습니다. 그런 다음 Azure 리소스 관리자-Azure 리소스 관리자 가상 네트워크 연결을 구성합니다. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>SQL 데이터베이스에서 SSIS 카탈로그 호스팅

가상 네트워크 서비스 끝점이 있는 Azure SQL Database에서 SSIS 카탈로그를 호스팅하는 경우 Azure-SSIS IR을 동일한 가상 네트워크 및 서브넷에 조인해야 합니다.

개인 끝점이 있는 관리되는 인스턴스에서 SSIS 카탈로그를 호스팅하는 경우 Azure-SSIS IR을 동일한 가상 네트워크에 가입하지만 관리되는 인스턴스와 다른 서브넷에 조인해야 합니다. Azure-SSIS IR을 관리되는 인스턴스가 아닌 다른 가상 네트워크에 연결하려면 가상 네트워크 피어링(동일한 지역으로 제한됨) 또는 가상 네트워크에서 가상 네트워크로의 연결을 권장합니다. 자세한 내용은 [응용 프로그램을 Azure SQL Database 관리 인스턴스에 연결](../sql-database/sql-database-managed-instance-connect-app.md)을 참조하십시오.

## <a name="access-to-azure-services"></a>Azure 서비스에 대한 액세스

SSIS 패키지가 [가상 네트워크 서비스 끝점을](../virtual-network/virtual-network-service-endpoints-overview.md) 지원하는 Azure 리소스에 액세스하고 Azure-SSIS IR에서 해당 리소스에 대한 액세스를 보호하려는 경우 Azure-SSIS IR을 가상 네트워크 서비스 끝점에 대해 구성된 가상 네트워크 서브넷에 가입한 다음 관련 Azure 리소스에 가상 네트워크 규칙을 추가하여 동일한 서브넷에서 액세스를 허용할 수 있습니다.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>IP 방화벽 규칙에 의해 보호되는 데이터 원본에 대한 액세스

SSIS 패키지가 특정 정적 공용 IP 주소만 허용하는 데이터 저장소/리소스에 액세스하고 Azure-SSIS IR에서 해당 리소스에 대한 액세스를 보호하려는 경우 가상 네트워크에 가입하는 동안 Azure-SSIS IR에 대한 자체 [공용 IP 주소를](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) 가져온 다음 해당 리소스에 IP 방화벽 규칙을 추가하여 해당 IP 주소에서 액세스할 수 있도록 할 수 있습니다.

모든 경우에 가상 네트워크는 Azure 리소스 관리자 배포 모델을 통해서만 배포할 수 있습니다.

다음 섹션에 자세한 내용이 제공됩니다. 

## <a name="virtual-network-configuration"></a>가상 네트워크 구성

다음 요구 사항을 충족하도록 가상 네트워크를 설정합니다. 

- Azure-SSIS IR을 호스팅하는 가상 네트워크 서브넷의 구독하에 등록된 공급자인지 확인합니다. `Microsoft.Batch` 클래식 가상 네트워크를 사용하는 경우 `MicrosoftAzureBatch` 해당 가상 네트워크의 클래식 가상 시스템 기여자 역할에도 참여합니다. 

- 필요한 권한이 있는지 확인하세요. 자세한 내용은 [권한 설정](#perms)을 참조하십시오.

- Azure-SSIS IR을 호스팅할 적절한 서브넷을 선택합니다. 자세한 내용은 [서브넷 선택을](#subnet)참조하십시오. 

- Azure-SSIS IR에 대한 공용 IP 주소를 가져오는 경우 [정적 공용 IP 주소 선택을 참조하세요.](#publicIP)

- 가상 네트워크에서 자체 DNS(도메인 이름 시스템) 서버를 사용하는 경우 [DNS 서버 설정을](#dns_server)참조하십시오. 

- 서브넷에서 NSG(네트워크 보안 그룹)를 사용하는 경우 [NSG 설정을](#nsg)참조하십시오. 

- Azure ExpressRoute 또는 사용자 정의 경로(UDR)를 사용하는 경우 [Azure ExpressRoute 또는 UDR 사용을](#route)참조하십시오. 

- 가상 네트워크의 리소스 그룹(또는 공용 IP 주소를 가져오는 경우 공용 IP 주소의 리소스 그룹)이 특정 Azure 네트워크 리소스를 만들고 삭제할 수 있는지 확인합니다. 자세한 내용은 [리소스 그룹 설정을](#resource-group)참조하십시오. 

- Azure-SSIS IR에 [대한 사용자 지정 설정에](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)설명된 대로 Azure-SSIS IR을 사용자 지정하면 Azure-SSIS IR 노드는 미리 정의된 범위인 172.16.0~172.31.255.255에서 개인 IP 주소를 가져옵니다. 따라서 가상 또는 온-프레미스 네트워크의 개인 IP 주소 범위가 이 범위와 충돌하지 않도록 해야 합니다.

이 다이어그램은 Azure-SSIS IR에 필요한 연결을 보여 줍니다.

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a>권한 설정

Azure-SSIS IR을 만드는 사용자에게는 다음 권한이 있어야 합니다.

- SSIS IR를 Azure Resource Manager 가상 네트워크에 연결하는 경우 다음 두 가지 옵션이 있습니다.

  - 기본 제공 ‘네트워크 참가자’ 역할을 사용합니다. 이 역할에는 범위가 필요 이상으로 큰 _Microsoft.Network/\*_ 권한이 제공됩니다.

  - 필요한 _Microsoft.Network/virtualNetworks/\*/join/action_ 권한만 포함하는 사용자 지정 역할을 만듭니다. Azure Resource Manager 가상 네트워크에 가입하는 동안 Azure-SSIS IR에 대한 공용 IP 주소를 가져오려면 역할에 _Microsoft.Network/publicIP주소/*/조인/작업_ 권한도 포함하십시오.

- SSIS IR을 클래식 가상 네트워크에 연결하는 경우에는 기본 제공 ‘클래식 가상 머신 참가자’ 역할을 사용하는 것이 좋습니다. 그렇지 않은 경우 가상 네트워크 연결 권한이 포함된 사용자 지정 역할을 정의해야 합니다.

### <a name="select-the-subnet"></a><a name="subnet"></a> 서브넷 선택

서브넷을 선택할 때: 

- Azure-SSIS IR을 배포할 게이트웨이서브넷을 선택하지 마십시오. 가상 네트워크 게이트웨이 전용입니다. 

- 선택한 서브넷에 Azure-SSIS IR을 사용할 수 있는 충분한 주소 공간이 있는지 확인합니다. IR 노드 번호의 두 배 이상사용 가능한 IP 주소를 남겨 둡니다. Azure는 각 서브넷 내의 일부 IP 주소를 예약합니다. 이러한 주소는 사용할 수 없습니다. 서브넷의 첫 번째 및 마지막 IP 주소는 프로토콜 준수를 위해 예약되어 있으며 Azure 서비스에 세 개의 주소가 더 사용됩니다. 자세한 내용은 [이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- 다른 Azure 서비스(예: SQL Database 관리 인스턴스, 앱 서비스 등)에서 만 사용하는 서브넷을 사용하지 마십시오. 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>정적 공용 IP 주소 선택

가상 네트워크에 가입하는 동안 Azure-SSIS IR에 대한 정적 공용 IP 주소를 가져오려면 다음 요구 사항을 충족하는지 확인하십시오.

- 다른 Azure 리소스와 아직 연결되지 않은 사용하지 않은 두 가지를 제공해야 합니다. Azure-SSIS IR을 주기적으로 업그레이드할 때 추가 가 사용됩니다. 하나의 공용 IP 주소는 활성 Azure-SSIS IRs 간에 공유할 수 없습니다.

- 둘 다 표준 형식의 정적 이어야 합니다. 자세한 내용은 [공용 IP 주소의 SCO를](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) 참조하십시오.

- 둘 다 DNS 이름이 있어야 합니다. DNS 이름을 만들 때 DNS 이름을 제공하지 않은 경우 Azure Portal에서 DNS 이름을 지정할 수 있습니다.

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- 가상 네트워크는 동일한 구독 하에 있어야 하며 동일한 리전에 있어야 합니다.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a>DNS 서버 설정 
Azure-SSIS IR에 의해 결합된 가상 네트워크에서 자체 DNS 서버를 사용하여 개인 호스트 이름을 확인해야 하는 경우 전역 Azure 호스트 이름(예: `<your storage account>.blob.core.windows.net`Azure Storage Blob)도 확인할 수 있습니다. 

권장되는 방법 중 하나는 다음과 같습니다. 

-   Azure DNS에 요청을 전달하도록 사용자 지정 DNS를 구성합니다. 해결되지 않은 DNS 레코드를 자체 DNS 서버에서 Azure 재귀 해결사(168.63.129.16)의 IP 주소로 전달할 수 있습니다. 

자세한 내용은 [사용자 고유의 DNS 서버를 사용하는 이름 확인을](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)참조하십시오. 

> [!NOTE]
> Azure-SSIS IR이 사용자 고유의 DNS 접미사를 자동으로 부호 `<your_private_server>.contoso.com` 지정하지 않기 때문에 개인 호스트 이름(예: 대신 `<your_private_server>`사용)에 정규화된 도메인 이름(FQDN)을 사용하십시오.

### <a name="set-up-an-nsg"></a><a name="nsg"></a>NSG 설정
Azure-SSIS IR에서 사용하는 서브넷에 대한 NSG를 구현해야 하는 경우 다음 포트를 통해 인바운드 및 아웃바운드 트래픽을 허용합니다. 

-   **Azure-SSIS IR의 인바운드 요구 사항**

| Direction | 전송 프로토콜 | 원본 | 소스 포트 범위 | 대상 | 대상 포트 범위 | 주석 |
|---|---|---|---|---|---|---|
| 인바운드 | TCP | 배치 노드 관리 | * | VirtualNetwork | 29876, 29877(리소스 관리자 가상 네트워크에 IR에 가입하는 경우) <br/><br/>10100, 20100, 30100(IR을 클래식 가상 네트워크에 조인하는 경우)| Data Factory 서비스는 이러한 포트를 사용하여 가상 네트워크에서 Azure-SSIS IR의 노드와 통신합니다. <br/><br/> 서브넷 수준 NSG를 만들지 여부에 관계없이 Data Factory는 항상 Azure-SSIS IR을 호스팅하는 가상 시스템에 연결된 네트워크 인터페이스 카드(NIC) 수준에서 NSG를 구성합니다. 지정된 포트에서 Data Factory IP 주소의 인바운드 트래픽만 해당 NIC 수준 NSG에서 허용됩니다. 서브넷 수준에서 인터넷 트래픽에 이러한 포트를 열더라도 데이터 팩터리 IP 주소가 아닌 IP 주소의 트래픽은 NIC 수준에서 차단됩니다. |
| 인바운드 | TCP | 코프넷소 | * | VirtualNetwork | 3389 | (선택 사항) 이 규칙은 Microsoft 서포터가 고객에게 고급 문제 해결을 위해 열어 달라고 요청하는 경우에만 필요하며 문제 해결 직후에 닫을 수 있습니다. **CorpNetSaw** 서비스 태그는 원격 데스크톱을 사용하는 Microsoft 회사 네트워크의 보안 액세스 워크스테이션만 허용합니다. 또한 이 서비스 태그는 포털에서 선택할 수 없으며 Azure PowerShell 또는 Azure CLI를 통해서만 사용할 수 있습니다. <br/><br/> NIC 수준 NSG에서 포트 3389는 기본적으로 열려 있으며 서브넷 수준 NSG에서 포트 3389를 제어할 수 있으며 Azure-SSIS IR은 보호를 위해 각 IR 노드의 Windows 방화벽 규칙에서 기본적으로 포트 3389아웃바운드를 허용하지 않습니다. |
||||||||

-   **Azure-SSIS IR의 아웃바운드 요구 사항**

| Direction | 전송 프로토콜 | 원본 | 소스 포트 범위 | 대상 | 대상 포트 범위 | 주석 |
|---|---|---|---|---|---|---|
| 아웃바운드 | TCP | VirtualNetwork | * | AzureCloud | 443 | 가상 네트워크의 Azure-SSIS IR 노드는 이 포트를 사용하여 Azure 저장소 및 Azure 이벤트 허브와 같은 Azure 서비스에 액세스합니다. |
| 아웃바운드 | TCP | VirtualNetwork | * | 인터넷 | 80 | (선택 사항) 가상 네트워크의 Azure-SSIS IR 노드는 이 포트를 사용하여 인터넷에서 인증서 해지 목록을 다운로드합니다. 이 트래픽을 차단하면 IR을 시작할 때 성능이 저하되고 인증서 사용에 대한 인증서 해지 목록을 확인하는 기능이 손실될 수 있습니다. 특정 FQDN으로 대상범위를 더 좁히려면 **Azure ExpressRoute 또는 UDR 섹션을** 참조하십시오.|
| 아웃바운드 | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | (선택 사항) 이 규칙은 가상 네트워크의 Azure-SSIS IR 노드가 SQL Database 서버에서 호스팅하는 SSISDB에 액세스하는 경우에만 필요합니다. SQL Database 서버 연결 정책이 **리디렉션**대신 **프록시로** 설정된 경우 포트 1433만 필요합니다. <br/><br/> 이 아웃바운드 보안 규칙은 가상 네트워크 또는 개인 끝점으로 구성된 Azure Database 서버에서 관리되는 인스턴스에서 호스팅되는 SSISDB에는 적용되지 않습니다. |
| 아웃바운드 | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (선택 사항) 이 규칙은 가상 네트워크의 Azure-SSIS IR 노드가 개인 엔드포인트로 구성된 가상 네트워크 또는 Azure Database 서버에서 관리되는 인스턴스에서 호스팅되는 SSISDB에 액세스하는 경우에만 필요합니다. SQL Database 서버 연결 정책이 **리디렉션**대신 **프록시로** 설정된 경우 포트 1433만 필요합니다. |
| 아웃바운드 | TCP | VirtualNetwork | * | 스토리지 | 445 | (선택 사항) 이 규칙은 Azure 파일에 저장된 SSIS 패키지를 실행하려는 경우에만 필요합니다. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a>Azure 익스프레스루트 또는 UDR 사용
Azure-SSIS IR에서 아웃바운드 트래픽을 검사하려는 경우 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 힘 터널링(BGP 경로, 0.0.0.0/0, 가상 네트워크 광고)을 통해 Azure-SSIS IR에서 시작된 트래픽을 온-프레미스 방화벽 어플라이언스로 라우팅하거나 NVA(네트워크 가상 어플라이언스)를 [DR을](../virtual-network/virtual-networks-udr-overview.md)통해 방화벽 또는 [Azure 방화벽으로](https://docs.microsoft.com/azure/firewall/) 라우팅할 수 있습니다. 

![Azure-SSIS IR에 대한 NVA 시나리오](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

전체 시나리오가 작동하려면 아래 작업을 수행해야 합니다.
   -   Azure 일괄 처리 관리 서비스와 Azure-SSIS IR 간의 인바운드 트래픽은 방화벽 어플라이언스를 통해 라우팅할 수 없습니다.
   -   방화벽 어플라이언스는 Azure-SSIS IR에 필요한 아웃바운드 트래픽을 허용해야 합니다.

Azure Batch 관리 서비스와 Azure-SSIS IR 간의 인바운드 트래픽은 방화벽 어플라이언스로 라우팅할 수 없습니다. 트래픽이 들어온 것과 동일한 방식으로 회신할 수 있도록 인바운드 트래픽에 대해 경로를 정의해야 합니다. Azure Batch 관리 서비스와 다음 홉 유형을 **인터넷으로**사용하여 Azure-SSIS IR 간의 트래픽을 라우팅하기 위해 특정 DR을 정의할 수 있습니다.

예를 들어 Azure-SSIS IR이 Azure `UK South` 방화벽을 통해 아웃바운드 트래픽을 검사하려는 경우 먼저 서비스 태그 IP `BatchNodeManagement.UKSouth` 범위 [다운로드 링크](https://www.microsoft.com/download/details.aspx?id=56519) 또는 서비스 태그 [검색 API를](https://aka.ms/discoveryapi)통해 서비스 태그의 IP 범위 목록을 가져옵니다. 그런 다음 다음 홉 유형과 **함께** 다음 홉 유형의 관련 IP 범위 경로에 대해 다음 홉 유형과 0.0.0.0/0 경로와 다음 홉 유형을 **가상 어플라이언스로**적용합니다.

![Azure 일괄 처리 UDR 설정](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> 이 방법은 추가 유지 관리 비용이 발생합니다. Azure-SSIS IR을 위반하지 않도록 정기적으로 IP 범위를 확인하고 UDR에 새 IP 범위를 추가합니다. 서비스 태그에 새 IP가 나타나면 IP가 적용되므로 IP 범위를 매월 확인하는 것이 좋습니다. 

UDR 규칙을 더 쉽게 설정하려면 Powershell 스크립트를 실행하여 Azure 일괄 처리 관리 서비스에 대한 UDR 규칙을 추가할 수 있습니다.
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

아웃바운드 트래픽을 허용하는 방화벽 어플라이언스의 경우 NSG 아웃바운드 규칙의 요구 사항과 동일한 포트 아래로 아웃바운드를 허용해야 합니다.
-   Azure 클라우드 서비스로 대상을 가진 포트 443.

    Azure 방화벽을 사용하는 경우 AzureCloud 서비스 태그를 사용하여 네트워크 규칙을 지정할 수 있습니다. 다른 유형의 방화벽의 경우 포트 443에 대한 대상을 모두 허용하거나 Azure 환경의 유형에 따라 FQDNs 미만을 허용할 수 있습니다.

    | Azure 환경 | 엔드포인트                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure 공용      | <ul><li><b>Azure 데이터 팩터리(관리)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Azure 저장소(관리)</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Azure 컨테이너 레지스트리(사용자 지정 설정)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>이벤트 허브(로깅)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>마이크로소프트 로깅 서비스(내부 사용)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure 데이터 팩터리(관리)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Azure 저장소(관리)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Azure 컨테이너 레지스트리(사용자 지정 설정)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>이벤트 허브(로깅)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>마이크로소프트 로깅 서비스(내부 사용)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure 데이터 팩터리(관리)</b><ul><li>\*frontend.datamovement.azure.cn</li></ul></li><li><b>Azure 저장소(관리)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Azure 컨테이너 레지스트리(사용자 지정 설정)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>이벤트 허브(로깅)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>마이크로소프트 로깅 서비스(내부 사용)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Azure 저장소, Azure 컨테이너 레지스트리 및 이벤트 허브의 FQDN에 관해서는 이러한 끝점에 대한 네트워크 트래픽이 방화벽 어플라이언스로 라우팅되는 대신 Azure 백본 네트워크를 통과하도록 가상 네트워크에 대해 다음 서비스 끝점을 사용하도록 선택할 수도 있습니다.
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   포트 80 CRL 다운로드 사이트로 대상.

    Azure-SSIS IR 관리 목적을 위해 CRL(인증서 해지 목록) 인증서 사이트 다운로드 사이트로 사용되는 FQDN 을 아래에서 허용해야 합니다.
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    CRL이 다른 인증서를 사용하는 경우 인증서도 포함하는 것이 좋습니다. [인증서 해지 목록에서](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)자세한 내용을 이해하려면 이 내용을 읽을 수 있습니다.

    이 트래픽을 허용하지 않는 경우 Azure-SSIS IR을 시작할 때 성능 이 저하되고 보안 관점에서 권장되지 않는 인증서 사용에 대한 인증서 해지 목록을 확인하는 기능이 손실될 수 있습니다.

-   Azure SQL로 대상을 사용하는 포트 1433, 11000-11999(가상 네트워크의 Azure-SSIS IR 노드가 SQL Database 서버에서 호스팅하는 SSISDB에 액세스하는 경우에만 해당).

    Azure 방화벽을 사용하는 경우 Azure SQL Service Tag를 사용하여 네트워크 규칙을 지정할 수 있으며, 그렇지 않으면 대상을 방화벽 어플라이언스의 특정 Azure SQL URL로 허용할 수 있습니다.

-   Azure 저장소로 대상을 가진 포트 445(Azure 파일에 저장된 SSIS 패키지를 실행하는 경우에만 필요).

    Azure 방화벽을 사용하는 경우 저장소 서비스 태그를 사용하여 네트워크 규칙을 지정할 수 있으며, 그렇지 않으면 대상을 방화벽 어플라이언스의 특정 Azure 파일 저장소 URL로 허용할 수 있습니다.

> [!NOTE]
> Azure SQL 및 Storage의 경우 서브넷에서 가상 네트워크 서비스 끝점을 구성하는 경우 동일한 리전 또는 페어링된 지역의 Azure Storage에서 Azure-SSIS IR 및 Azure SQL 간의 트래픽이 방화벽 어플라이언스 대신 Microsoft Azure 백본 네트워크로 직접 라우팅됩니다.

Azure-SSIS IR의 아웃바운드 트래픽을 검사할 기능이 필요하지 않은 경우 경로를 적용하여 모든 트래픽을 다음 홉 유형 **인터넷으로**강제 할 수 있습니다.

-   Azure ExpressRoute 시나리오에서 Azure-SSIS IR을 호스팅하는 서브넷에서 다음 홉 유형을 **인터넷으로** 0.0.0.0/0 경로를 적용할 수 있습니다. 
-   NVA 시나리오에서는 Azure-SSIS IR을 호스팅하는 서브넷에 적용된 기존 0.0.0.0.0/0 경로를 다음 홉 유형에서 **인터넷에** **가상 어플라이언스로** 수정할 수 있습니다.

![경로 추가](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> 다음 홉 유형 **인터넷을** 사용하여 경로를 지정해도 모든 트래픽이 인터넷을 통해 이동하는 것은 아닙니다. 대상 주소가 Azure 서비스 중 하나인 한 Azure는 트래픽을 인터넷으로 라우팅하는 대신 Azure의 백본 네트워크를 통해 서비스로 직접 트래픽을 라우팅합니다.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a>리소스 그룹 설정

Azure-SSIS IR은 가상 네트워크와 동일한 리소스 그룹에 특정 네트워크 리소스를 만들어야 합니다. 이러한 리소스에는 다음이 포함됩니다.
- * \<Guid> azurebatch-cloudserviceloadbalancer라는*이름의 Azure 로드 밸런서.
- * \<Guid>-azurebatch-cloudservicepublicip라는*이름의 Azure 공용 IP 주소입니다.
- * \<Guid>-azurebatch-cloudservicenetworksecuritygroup이라는*이름의 네트워크 작업 보안 그룹입니다. 

> [!NOTE]
> 이제 Azure-SSIS IR에 대해 고유한 정적 공용 IP 주소를 가져올 수 있습니다. 이 시나리오에서는 Azure 로드 밸런서 및 네트워크 보안 그룹 가상 네트워크 대신 정적 공용 IP 주소와 동일한 리소스 그룹 에서만 만듭니다.

이러한 리소스는 Azure-SSIS IR이 시작될 때 만들어집니다. Azure-SSIS IR이 중지되면 삭제됩니다. Azure-SSIS IR에 대해 자체 정적 공용 IP 주소를 가져오는 경우 Azure-SSIS IR이 중지되면 자체 정적 공용 IP 주소가 삭제되지 않습니다. Azure-SSIS IR이 중지되지 않도록 하려면 다른 리소스에서 이러한 네트워크 리소스를 다시 사용하지 마십시오.

가상 네트워크/정적 공용 IP 주소가 속한 리소스 그룹/구독에 리소스 잠금이 없는지 확인합니다. 읽기 전용/삭제 잠금을 구성하면 Azure-SSIS IR을 시작하고 중지하면 실패하거나 응답이 중지됩니다.

가상 네트워크/정적 공용 IP 주소가 속한 리소스 그룹/구독에서 다음 리소스가 생성되지 않도록 하는 Azure 정책이 없는지 확인합니다. 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

구독의 리소스 할당량이 위의 세 네트워크 리소스에 충분한지 확인합니다. 특히 가상 네트워크에서 생성된 각 Azure-SSIS IR에 대해 위의 세 네트워크 리소스 각각에 대해 두 개의 무료 할당량을 예약해야 합니다. Azure-SSIS IR을 주기적으로 업그레이드할 때 추가 할당량이 사용됩니다.

### <a name="faq"></a><a name="faq"></a> FAQ

- 인바운드 연결을 위해 Azure-SSIS IR에 노출된 공용 IP 주소를 보호하되 어떻게 보호할 수 있습니까? 공용 IP 주소를 제거할 수 있습니까?
 
  Azure-SSIS IR이 가상 네트워크에 가입할 때 공용 IP 주소가 자동으로 생성됩니다. Azure 일괄 처리 관리 서비스만 Azure-SSIS IR에 인바운드 연결할 수 있도록 하는 NIC 수준 NSG가 있습니다. 인바운드 보호를 위해 서브넷 수준 NSG를 지정할 수도 있습니다.

  공용 IP 주소가 노출되지 않도록 하려면 Azure-SSIS IR을 가상 네트워크에 조인하는 대신 [자체 호스팅 IR을 Azure-SSIS IR의 프록시로 구성하는](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) 것이 좋습니다.
 
- Azure-SSIS IR의 공용 IP 주소를 내 데이터 원본에 대한 방화벽의 허용 목록에 추가할 수 있습니까?

  이제 Azure-SSIS IR에 대해 고유한 정적 공용 IP 주소를 가져올 수 있습니다. 이 경우 데이터 원본에 대한 방화벽의 허용 목록에 IP 주소를 추가할 수 있습니다. 시나리오에 따라 Azure-SSIS IR에서 데이터 액세스를 보호하기 위해 아래의 다른 옵션을 고려할 수도 있습니다.

  - 데이터 원본이 온-프레미스 네트워크에 가상 네트워크를 연결하고 Azure-SSIS IR을 가상 네트워크 서브넷에 연결한 후 해당 서브넷의 개인 IP 주소 범위를 방화벽의 데이터 원본 허용 목록에 추가할 수 있습니다.
  - 데이터 원본이 가상 네트워크 서비스 끝점을 지원하는 Azure 서비스인 경우 가상 네트워크 서브넷에서 가상 네트워크 서비스 엔드포인트를 구성하고 Azure-SSIS IR을 해당 서브넷에 조인할 수 있습니다. 그런 다음 해당 서브넷이 있는 가상 네트워크 규칙을 데이터 원본의 방화벽에 추가할 수 있습니다.
  - 데이터 원본이 Azure 가 아닌 클라우드 서비스인 경우 UDR을 사용하여 정적 공용 IP 주소를 통해 Azure-SSIS IR에서 NVA/Azure 방화벽으로 아웃바운드 트래픽을 라우팅할 수 있습니다. 그런 다음 NVA/Azure 방화벽의 정적 공용 IP 주소를 데이터 원본에 대한 방화벽의 허용 목록에 추가할 수 있습니다.
  - 위의 옵션 중 어느 것도 요구 사항을 충족하지 않는 경우 [Azure-SSIS IR에 대한 프록시로 자체 호스팅](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)IR을 구성하는 것이 좋습니다. 그런 다음 자체 호스팅 IR을 호스팅하는 컴퓨터의 정적 공용 IP 주소를 데이터 원본에 대한 방화벽의 허용 목록에 추가할 수 있습니다.

- Azure-SSIS IR에 대해 내 주소를 가져오려면 두 개의 정적 공용 주소를 제공해야 하는 이유는 무엇입니까?

  Azure-SSIS IR은 정기적으로 자동으로 업데이트됩니다. 업그레이드 하는 동안 새 노드가 만들어지고 이전 노드가 삭제됩니다. 그러나 가동 중지 시간을 방지하기 위해 새 노드가 준비될 때까지 이전 노드는 삭제되지 않습니다. 따라서 이전 노드에서 사용하는 첫 번째 정적 공용 IP 주소는 즉시 해제할 수 없으며 새 노드를 만들려면 두 번째 정적 공용 IP 주소가 필요합니다.

- Azure-SSIS IR에 대해 정적 공용 IP 주소를 가져왔지만 여전히 데이터 원본에 액세스할 수 없는 이유는 무엇입니까?

  - 두 정적 공용 IP 주소가 모두 데이터 원본에 대한 방화벽의 허용 목록에 추가되어 있는지 확인합니다. Azure-SSIS IR을 업그레이드할 때마다 정적 공용 IP 주소가 가져온 두 주소 간에 전환됩니다. 허용 목록에 하나만 추가하면 업그레이드 후 Azure-SSIS IR에 대한 데이터 액세스가 끊어집니다.
  - 데이터 원본이 Azure 서비스인 경우 가상 네트워크 서비스 끝점으로 구성했는지 확인하십시오. 이 경우 Azure-SSIS IR에서 데이터 원본으로의 트래픽은 Azure 서비스에서 관리하는 개인 IP 주소를 사용하도록 전환되고 데이터 원본에 대한 방화벽의 허용 목록에 정적 공용 IP 주소를 추가해도 적용되지 않습니다.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal(데이터 팩터리 UI)

이 섹션에서는 Azure 포털 및 데이터 팩터리 UI를 사용하여 기존 Azure-SSIS IR을 가상 네트워크(클래식 또는 Azure 리소스 관리자)에 조인하는 방법을 보여 주며, 

Azure-SSIS IR을 가상 네트워크에 연결하기 전에 가상 네트워크를 올바르게 구성해야 합니다. 가상 네트워크 유형(클래식 또는 Azure 리소스 관리자)에 적용되는 섹션의 단계를 따릅니다. 그런 다음 세 번째 섹션의 단계를 수행하여 Azure-SSIS IR을 가상 네트워크에 조인합니다. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure 리소스 관리자 가상 네트워크 구성

포털을 사용하여 Azure-SSIS IR에 가입하기 전에 Azure 리소스 관리자 가상 네트워크를 구성합니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 데이터 팩터리 UI를 지원합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. **더 많은 서비스를 선택합니다.** **가상 네트워크**를 필터링하여 선택합니다. 

1. 목록에서 가상 네트워크를 필터링하고 선택합니다. 

1. **가상 네트워크** 페이지에서 **속성**을 선택합니다. 

1. **리소스 ID**에 대한 복사 단추를 선택하여 가상 네트워크에 대한 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다. 

1. 왼쪽 메뉴에서 **서브넷을 선택합니다.** 사용 가능한 주소 수가 Azure-SSIS IR의 노드보다 큰지 확인합니다. 

1. 가상 네트워크가 있는 Azure 구독에 Azure Batch 공급자가 등록되었는지 확인합니다. 또는 Azure 일괄 처리 공급자를 등록합니다. 구독에 Azure Batch 계정이 이미 있는 경우 구독이 Azure Batch에 등록됩니다. (Data Factory 포털에 Azure-SSIS IR을 만들면 Azure Batch 공급자가 자동으로 등록됩니다.) 

   1. Azure 포털에서 왼쪽 메뉴에서 **구독을 선택합니다.** 

   1. 구독을 선택합니다. 

   1. 왼쪽에서 리소스 **공급자를**선택하고 **Microsoft.Batch가** 등록된 공급자임을 확인합니다. 

   !["등록됨" 상태 확인](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   목록에서 **Microsoft.Batch**가 보이지 않으면 지금 등록할 수 있도록 구독에서 [빈 Azure Batch 계정을 만듭니다](../batch/batch-account-create-portal.md). 나중에 삭제할 수 있습니다. 

### <a name="configure-a-classic-virtual-network"></a>클래식 가상 네트워크 구성

Azure-SSIS IR에 가입하기 전에 포털을 사용하여 클래식 가상 네트워크를 구성합니다. 

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 데이터 팩터리 UI를 지원합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. **더 많은 서비스를 선택합니다.** **가상 네트워크(클래식)** 를 필터링하여 선택합니다. 

1. 목록에서 가상 네트워크를 필터링하고 선택합니다. 

1. **가상 네트워크(클래식)** 페이지에서 **속성**을 선택합니다. 

   ![클래식 가상 네트워크 리소스 ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. **RESOURCE ID**에 대한 복사 단추를 선택하여 클래식 네트워크의 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다. 

1. 왼쪽 메뉴에서 **서브넷을 선택합니다.** 사용 가능한 주소 수가 Azure-SSIS IR의 노드보다 큰지 확인합니다. 

   ![가상 네트워크에서 사용 가능한 주소 수](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. **MicrosoftAzureBatch**를 가상 네트워크의 **클래식 가상 머신 참가자** 역할에 조인합니다. 

   1. 왼쪽 메뉴에서 **IAM(액세스 제어)을**선택하고 **역할 할당 탭을 선택합니다.** 

       !["액세스 제어" 및 "추가" 단추](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. **역할 할당 추가**를 선택합니다.

   1. 역할 **할당 추가** 페이지에서 **역할,** **클래식 가상 시스템 기여자**선택. **선택** 상자에서 **붙여넣기 ddbf3205-c6bd-46ae-8127-60eb93363864**, 다음 검색 결과 목록에서 **Microsoft Azure 배치를** 선택합니다. 

       !["역할 할당 추가" 페이지에서 검색 결과](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. 설정을 저장하고 페이지를 닫기 위해 **저장을** 선택합니다. 

       ![액세스 설정 저장](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. 참가자 목록에 **Microsoft Azure Batch**가 보이는지 확인합니다. 

       ![Azure Batch 액세스 확인](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. 가상 네트워크가 있는 Azure 구독에 Azure Batch 공급자가 등록되었는지 확인합니다. 또는 Azure 일괄 처리 공급자를 등록합니다. 구독에 Azure Batch 계정이 이미 있는 경우 구독이 Azure Batch에 등록됩니다. (Data Factory 포털에 Azure-SSIS IR을 만들면 Azure Batch 공급자가 자동으로 등록됩니다.) 

   1. Azure 포털에서 왼쪽 메뉴에서 **구독을 선택합니다.** 

   1. 구독을 선택합니다. 

   1. 왼쪽에서 리소스 **공급자를**선택하고 **Microsoft.Batch가** 등록된 공급자임을 확인합니다. 

   !["등록됨" 상태 확인](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   목록에서 **Microsoft.Batch**가 보이지 않으면 지금 등록할 수 있도록 구독에서 [빈 Azure Batch 계정을 만듭니다](../batch/batch-account-create-portal.md). 나중에 삭제할 수 있습니다. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR을 가상 네트워크에 조인

Azure Resource Manager 가상 네트워크 또는 클래식 가상 네트워크를 구성한 후 Azure-SSIS IR을 가상 네트워크에 조인할 수 있습니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 데이터 팩터리 UI를 지원합니다. 

1. Azure [포털에서](https://portal.azure.com)왼쪽 메뉴에서 **데이터 팩터리를**선택합니다. 메뉴에 **데이터 팩터가** 표시되지 않으면 **더 많은 서비스를**선택한 다음 인텔리전스 + **분석** 섹션에서 **데이터 팩터리를**선택합니다. 

   ![데이터 팩터리 목록](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 목록에서 Azure-SSIS IR을 사용하여 데이터 팩터리를 선택합니다. 데이터 팩터리의 홈 페이지가 표시됩니다. 모니터 **작성** 자 & 선택합니다. 별도의 탭에 Data Factory UI가 표시됩니다. 

   ![데이터 팩터리 홈페이지](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 데이터 팩터리 UI에서 **편집** 탭으로 전환하고 **연결**을 선택한 다음, **통합 런타임** 탭으로 전환합니다. 

   !["통합 런타임" 탭](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Azure-SSIS IR이 실행 중인 경우 **통합 런타임** 목록에서 **작업** 열에서 Azure-SSIS IR에 대한 **중지** 단추를 선택합니다. Azure-SSIS IR을 중지할 때까지 편집할 수 없습니다. 

   ![IR 중지](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 통합 **런타임** 목록에서 **작업** 열에서 Azure-SSIS IR에 대한 **편집** 단추를 선택합니다. 

   ![통합 런타임 편집](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 통합 런타임 설정 패널에서 **다음** 단추를 선택하여 **일반 설정** 및 **SQL 설정** 섹션을 진행합니다. 

1. 고급 **설정** 섹션에서: 

   1. **Azure-SSIS 통합 런타임에 대한 VNet 선택을 선택하고, ADF가 특정 네트워크 리소스를 만들 수 있도록 허용하고, 선택적으로 정적 공용 IP 주소 확인란을 가져올 수 있습니다.** 

   1. **구독**에 대해서는 가상 네트워크가 있는 Azure 구독을 선택합니다.

   1. **위치**에는 통합 런타임의 동일한 위치가 선택됩니다.

   1. **유형의**경우 가상 네트워크 유형(클래식 또는 Azure 리소스 관리자)을 선택합니다. 클래식 가상 네트워크가 곧 더 이상 사용되지 않으므로 Azure 리소스 관리자 가상 네트워크를 선택하는 것이 좋습니다.

   1. **VNet 이름**에서 가상 네트워크의 이름을 선택합니다. SSISDB를 호스트하기 위해 가상 네트워크 서비스 끝점 또는 개인 끝점이 있는 관리되는 인스턴스가 있는 Azure SQL Database 서버에 사용되는 것과 동일해야 합니다. 또는 온-프레미스 네트워크에 연결된 것과 같아야 합니다. 그렇지 않으면 Azure-SSIS IR에 대한 정적 공용 IP 주소를 가져오는 가상 네트워크가 될 수 있습니다.

   1. **서브넷 이름**에서 가상 네트워크의 서브넷 이름을 선택합니다. SSISDB를 호스트하기 위해 가상 네트워크 서비스 끝점이 있는 Azure SQL Database 서버에 사용되는 것과 동일해야 합니다. 또는 SSISDB를 호스트하기 위해 개인 끝점을 사용하여 관리되는 인스턴스에 사용되는 서브넷과 다른 서브넷이어야 합니다. 그렇지 않으면 Azure-SSIS IR에 대한 정적 공용 IP 주소를 가져오는 모든 서브넷이 될 수 있습니다.

   1. **Azure-SSIS 통합 런타임에 정적 공용 IP 주소 가져오기** 확인란을 선택하여 Azure-SSIS IR에 대해 정적 공용 IP 주소를 가져올지 여부를 선택하여 데이터 원본의 방화벽에서 허용할 수 있습니다.

      확인란을 선택하면 다음 단계를 완료합니다.

      1. **첫 번째 정적 공용 IP 주소의**경우 Azure-SSIS IR에 대한 요구 사항을 [충족하는](#publicIP) 첫 번째 정적 공용 IP 주소를 선택합니다. 없는 경우 새 링크 **만들기를** 클릭하여 Azure Portal에서 정적 공용 IP 주소를 만든 다음 여기에서 새로 고침 단추를 클릭하여 선택할 수 있습니다.
      
      1. **두 번째 정적 공용 IP 주소의**경우 Azure-SSIS IR에 대한 요구 사항을 [충족하는](#publicIP) 두 번째 정적 공용 IP 주소를 선택합니다. 없는 경우 새 링크 **만들기를** 클릭하여 Azure Portal에서 정적 공용 IP 주소를 만든 다음 여기에서 새로 고침 단추를 클릭하여 선택할 수 있습니다.

   1. **VNet 유효성 검사**를 선택합니다. 유효성 검사가 성공하면 **계속을**선택합니다. 

   ![가상 네트워크 관련 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. **요약** 섹션에서 Azure-SSIS IR에 대한 모든 설정을 검토합니다. 그런 다음 **업데이트를**선택합니다.

1. Azure-SSIS IR에 대한 **작업** 열에서 **시작** 단추를 선택하여 Azure-SSIS IR을 시작합니다. 가상 네트워크에 연결되는 Azure-SSIS IR을 시작하는 데 약 20~30분이 걸립니다. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>변수를 정의합니다.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>가상 네트워크 구성

Azure-SSIS IR을 가상 네트워크에 가입하려면 가상 네트워크를 구성해야 합니다. Azure-SSIS IR이 가상 네트워크에 가입하도록 가상 네트워크 권한 및 설정을 자동으로 구성하려면 다음 스크립트를 추가합니다.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Azure-SSIS IR을 만들어 가상 네트워크에 조인

Azure-SSIS IR을 만드는 동시에 가상 네트워크에 조인할 수 있습니다. 전체 스크립트 및 지침은 [Azure-SSIS IR 만들기를](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)참조하십시오.

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>기존 Azure-SSIS IR을 가상 네트워크에 조인

[Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md) 문서에서는 Azure-SSIS IR을 만들고 동일한 스크립트의 가상 네트워크에 조인하는 방법을 보여 주며 있습니다. Azure-SSIS IR이 이미 있는 경우 다음 단계를 수행하여 가상 네트워크에 조인합니다. 
1. Azure-SSIS IR을 중지합니다. 
1. 가상 네트워크에 조인하도록 Azure-SSIS IR을 구성합니다. 
1. Azure-SSIS IR을 시작합니다. 

### <a name="stop-the-azure-ssis-ir"></a>Azure-SSIS IR 중지

가상 네트워크에 가입하려면 Azure-SSIS IR을 중지해야 합니다. 이 명령은 모든 노드를 해제하고 청구를 중지합니다.

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Azure-SSIS IR이 조인하기 위한 가상 네트워크 설정을 구성합니다.

Azure-SSIS가 참여할 가상 네트워크에 대한 설정을 구성하려면 다음 스크립트를 사용합니다. 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Azure-SSIS IR 구성

Azure-SSIS IR을 가상 네트워크에 연결하려면 `Set-AzDataFactoryV2IntegrationRuntime` 명령을 실행합니다. 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>Azure-SSIS IR 시작

Azure-SSIS IR을 시작하려면 다음 명령을 실행합니다. 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

이 명령을 완료하는 데 20~30분이 걸립니다.

## <a name="next-steps"></a>다음 단계

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하십시오. 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR을 비롯한 IR에 대한 일반적인 개념 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지를 배포합니다.](tutorial-create-azure-ssis-runtime-portal.md) 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트합니다. 
- [Azure-SSIS IR을 만듭니다.](create-azure-ssis-integration-runtime.md) 이 문서는 자습서에서 확장합니다. SSIS 카탈로그를 호스트하기 위해 가상 네트워크 서비스 끝점 또는 가상 네트워크에서 관리되는 인스턴스가 있는 Azure SQL Database를 사용하는 방법에 대한 지침을 제공합니다. Azure-SSIS IR을 가상 네트워크에 조인하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR에 대한 정보를 얻는 방법을 보여 줍니다. 반환된 정보에 대한 상태 설명을 제공합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지, 시작 또는 삭제하는 방법을 보여 줍니다. 또한 노드를 추가하여 Azure-SSIS IR을 규모 확장하는 방법을 보여줍니다.
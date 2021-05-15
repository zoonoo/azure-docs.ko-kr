---
title: Azure Migrate Server Migration을 사용하여 ExpressRoute를 통해 데이터 복제
description: Azure Migrate Server Migration을 사용하여 복제에 Azure ExpressRoute를 사용하는 방법
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: 9aa9a42422f3c114490d1dbb28a146b6e76ca8cd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558621"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Azure Migrate을 사용하여 ExpressRoute를 통해 데이터 복제: Server Migration

이 문서에서는 서버를 Azure로 마이그레이션하는 동안 ExpressRoute 회로를 통해 데이터를 복제하도록 [Azure Migrate: 서버 마이그레이션](./migrate-services-overview.md#azure-migrate-server-migration-tool)을 구성하는 방법에 대해 알아봅니다.

## <a name="understand-azure-expressroute-circuits"></a>중복 Azure ExpressRoute 회로
ExpressRoute 회로는 연결 공급자를 통해 온-프레미스 인프라를 Microsoft에 연결합니다. ExpressRoute 회로는 개인 피어링, Microsoft 피어링 또는 둘 다를 사용하도록 구성할 수 있습니다. [ExpressRoute 회로 및 피어링](../expressroute/expressroute-circuit-peerings.md#peeringcompare)의 문서를 검토하여 ExpressRoute에서 사용할 수 있는 다양한 피어링 옵션에 대해 자세히 알아보세요.

Azure Migrate의 서버 마이그레이션 도구를 사용하면 다른 클라우드에서 온-프레미스 서버 및 서버를 Azure virtual machines로 마이그레이션할 수 있습니다. 이 도구는 Azure 구독의 관리 디스크로 마이그레이션할 서버에서 데이터를 복제하기 위해 진행 중인 복제 스트림을 설정하는 방식으로 작동합니다. 서버를 마이그레이션할 준비가 되면 Azure에서 복제된 데이터가 서버를 마이그레이션하는 데 사용됩니다.

온-프레미스 서버에서 복제된 데이터는 인터넷을 통해(보안 암호화된 연결 사용) 또는 ExpressRoute 연결을 통해 Azure 구독에 전송되도록 구성할 수 있습니다. 마이그레이션할 서버가 많은 경우, 복제를 위해 ExpressRoute를 사용하면 Express 경로 회로에서 사용할 수 있는 프로비전된 대역폭을 사용하여 서버를 보다 효율적으로 마이그레이션하는 데 도움이 됩니다.

이 문서에서는 다음에 대해 알아봅니다
> [!div class="checklist"]
>
> * 개인 피어링과 함께 ExpressRoute 회로를 사용하여 데이터 복제하는 방법.
> * Microsoft 피어링과 함께 ExpressRoute 회로를 사용하여 데이터 복제하는 방법.

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>개인 피어링과 함께 ExpressRoute 회로를 사용하여 데이터 복제

> [!NOTE]
> 개인 피어링 회로를 통한 복제는 현재 [Azure에 대한 VMware 가상 머신의 에이전트 없는 마이그레이션](./tutorial-migrate-vmware.md)에만 지원됩니다. 다른 [복제 방법](./migrate-services-overview.md#azure-migrate-server-migration-tool)에 대한 프라이빗 엔드포인트 지원은 곧 제공될 예정입니다.

VMware 가상 머신을 Azure로 마이그레이션하는 에이전트 없는 방법에서 Azure Migrate 어플라이언스는 먼저 구독의 스토리지 계정(캐시 스토리지 계정)에 복제 데이터를 업로드합니다. 그러면 캐시 스토리지 계정에서 복제된 데이터가 Azure Migrate 서비스에서 구독의 복제본 관리 디스크로 이동합니다. 복제에 개인 피어링 회로를 사용하려면 프라이빗 엔드포인트를 만들어 캐시 스토리지 계정에 연결합니다. 프라이빗 엔드포인트는 VNet(가상 네트워크)에서 하나 이상의 개인 IP 주소를 사용하여 스토리지 계정을 Azure VNet에 효과적으로 제공합니다. 프라이빗 엔드포인트를 사용하면 Azure Migrate 어플라이언스에서 ExpressRoute 개인 피어링을 사용하여 캐시 스토리지 계정에 연결하고 개인 IP 주소에서 직접 데이터를 전송할 수 있습니다. <br/>  

![복제 프로세스](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - Azure Migrate 어플라이언스는 복제 데이터 외에도 오케스트레이션 복제를 포함하여 제어 평면 작업에 대한 Azure Migrate 서비스와 통신합니다. Azure Migrate 어플라이언스와 Azure Migrate 서비스 간의 제어 평면 통신은 Azure Migrate 서비스의 공용 엔드포인트에서 인터넷을 통해 계속해서 발생합니다.
> - 스토리지 계정의 프라이빗 엔드포인트는 Azure Migrate 어플라이언스가 배포된 네트워크에서 액세스할 수 있어야 합니다.
> - 캐시 스토리지 계정의 BLOB 서비스 엔드포인트에 대한 Azure Migrate 어플라이언스의 DNS 쿼리를 캐시 스토리지 계정에 연결된 프라이빗 엔드포인트의 개인 IP 주소로 확인하도록 DNS를 구성해야 합니다.
> - 캐시 스토리지 계정은 공용 엔드포인트에서 액세스할 수 있어야 합니다. (Azure Migrate 서비스는 캐시 스토리지 계정의 공용 엔드포인트를 사용하여 스토리지 계정에서 복제본 관리 디스크로 데이터를 이동합니다) 


### <a name="1-pre-requisites"></a>1.필수 구성 요소

프라이빗 엔드포인트를 만드는 Azure 사용자에게는 프라이빗 엔드포인트가 생성될 리소스 그룹 및 가상 네트워크에 대한 다음 권한이 있어야 합니다.

**사용 사례** | **권한** 
--- | --- 
 프라이빗 엔드포인트를 만들고 관리합니다. | Microsoft. Network/privateEndpoint/write/action<br/>Microsoft.Network/privateEndpoint/read/action  
|프라이빗 엔드포인트를 VNet/서브넷에 연결합니다.<br/>이는 프라이빗 엔드포인트가 생성될 가상 네트워크에 필요합니다.| Microsoft. networks/virtualNetworks/subnet/join/action 
Microsoft. Network/virtualNetworks/join/action
|스토리지 계정에 프라이빗 엔드포인트를 연결합니다. <br/>| Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnectionApproval/action <br/> Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnections/read
|네트워크 인터페이스를 만들고 네트워크 보안 그룹에 참가시킵니다. | Microsoft.Network/networkInterfaces/read <br/> Microsoft.Network/networkInterfaces/subnets/write <br/> Microsoft.Network/networkInterfaces/subnets/read<br/> Microsoft.Network/networkSecurityGroups/join/action(선택 사항)
프라이빗 DNS 영역을 만들고 관리합니다.| 프라이빗 DNS 영역 contributor 역할 <br/> _Or_ <br/> Microsoft.Network/privateDnsZones/A/* <br/>  Microsoft.Network/privateDnsZones/write 
Microsoft.Network/privateDnsZones/read <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/write <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/read <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/write <br/>  Microsoft.Network/privateDnsZones/virtualNetworkLinks/read <br/> Microsoft.Network/virtualNetworks/join/action 

### <a name="2-identify-the-cache-storage-account"></a>2. 캐시 스토리지 계정 식별 
 
Azure Migrate는 Azure Migrate 프로젝트에서 가상 머신에 대한 복제(Azure Portal 환경 사용)를 처음으로 구성할 때 캐시 스토리지 계정을 자동으로 만듭니다. 스토리지 계정은 Azure Migrate 프로젝트를 만든 것과 동일한 구독 및 리소스 그룹에 만들어집니다.

스토리지 계정을 만들고 찾으려면 다음을 수행합니다.

1. Azure Migrate에 대한 Azure Portal 환경을 사용하여 프로젝트에서 하나 이상의 가상 머신을 복제합니다.
2. Azure Migrate 프로젝트의 리소스 그룹으로 이동합니다.
3. 스토리지 계정 이름에서 **"lsa"** 접두사를 식별하여 캐시 스토리지 계정을 찾습니다.

![리소스 그룹 보기](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
>  
> 리소스 그룹에 **"lsa"** 접두사가 포함된 둘 이상의 스토리지 계정이 있는 경우 프로젝트의 복제 VM에 대한 복제 설정 및 대상 구성 메뉴로 이동하여 스토리지 계정을 확인할 수 있습니다. <br/> 
> ![복제 설정 개요](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. 캐시 스토리지 계정을 범용 v2로 업그레이드 

범용 v2(GPv2) 스토리지 계정에서만 프라이빗 엔드포인트를 만들 수 있습니다. 캐시 스토리지 계정이 GPv2 스토리지 계정이 아닌 경우 다음 단계를 사용하여 GPv2로 업그레이드합니다.

1. 스토리지 계정으로 이동합니다.
2. **Configuration(구성)** 을 선택합니다.
3. **계정 종류** 에서 **업그레이드** 를 클릭합니다.
4. **업그레이드 확인** 에서 계정 이름을 입력합니다.
5. 페이지의 가장 아래에서 **업그레이드** 를 선택합니다.

![스토리지 계정 업그레이드](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. 스토리지 계정의 프라이빗 엔드포인트 만들기

1. 스토리지 계정으로 이동하여 왼쪽 메뉴에서 **네트워킹** 을 선택하고 **프라이빗 엔드포인트 연결** 탭을 선택합니다.  
2. **+ 프라이빗 엔드포인트** 를 선택합니다.

    a. **프라이빗 엔드포인트 만들기** 창에서 **구독** 및 **리소스 그룹** 을 선택합니다. 프라이빗 엔드포인트의 이름을 입력하고 스토리지 계정 지역을 선택합니다.  
    ![PE 구성 창](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. **리소스** 탭에서 스토리지 계정이 있는 **구독 이름** 을 제공합니다. **리소스 종류** 로 **Microsoft. Storage/storageaccounts** 를 선택합니다. **리소스** 에서 GPv2 형식의 복제 스토리지 계정 이름을 제공합니다. **대상 하위 리소스** 로 **BLOB** 을 선택합니다.  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    다. **구성** 탭에서 **가상 네트워크** 를 선택하고 스토리지 계정의 프라이빗 엔드포인트를 만들어야 하는 **서브넷** 을 선택합니다.  

    > [!Note]
    > 가상 네트워크는 ExpressRoute 게이트웨이 엔드포인트를 포함하거나 ExpressRoute 게이트웨이를 사용하여 가상 네트워크에 연결되어야 합니다. 

    **사설 DNS 통합** 섹션에서 **예** 를 선택하고 프라이빗 DNS 영역과 통합합니다. **예** 를 선택하면 자동으로 DNS 영역을 선택된 가상 네트워크에 연결하고, 프라이빗 엔드포인트에 대해 만든 새 IP 및 정규화된 도메인 이름의 DNS 확인에 필요한 DNS 레코드를 추가합니다. [프라이빗 DNS 영역](../dns/private-dns-overview.md)에 대해 자세히 알아보기.

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. 프라이빗 엔드포인트에 대한 **태그** 를 추가할 수도 있습니다.  

    e. 세부 정보 입력을 완료한 후 **검토 + 만들기** 를 계속합니다. 유효성 검사가 완료되면 **만들기** 를 선택하여 프라이빗 엔드포인트를 만듭니다.

    > [!Note]
    > 프라이빗 엔드포인트를 만드는 사용자가 스토리지 계정의 소유자이기도 한 경우 프라이빗 엔드포인트는 자동으로 승인됩니다. 그렇지 않으면 소유자는 사용을 위해 프라이빗 엔드포인트를 승인해야 합니다. 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>프라이빗 DNS 영역을 만들고 DNS 레코드를 수동으로 추가(선택 사항)

프라이빗 엔드포인트를 만들 때 프라이빗 DNS 영역과 통합하는 옵션을 선택하지 않은 경우 이 섹션의 단계에 따라 수동으로 프라이빗 DNS 영역을 만듭니다. 

> [!Note]
> **예** 를 선택하여 프라이빗 DNS 영역과 통합하는 경우 이 섹션을 건너뛸 수 있습니다. 

1. 프라이빗 DNS 영역 만들기. 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a. **프라이빗 DNS 영역** 페이지에서 **+추가** 단추를 선택하여 새 영역 만들기를 시작합니다.  
    b. **프라이빗 DNS 영역 만들기** 페이지에서 필요한 세부 정보를 입력합니다. 프라이빗 DNS 영역의 이름을 _privatelink_. blob.core.windows.net로 입력합니다.  
    다. **검토 + 만들기** 탭으로 이동하여 DNS 영역을 검토하고 만듭니다.   

2. 가상 네트워크에 프라이빗 DNS 영역을 연결합니다.  

    위에서 만든 프라이빗 DNS 영역은 프라이빗 엔드포인트가 연결된 가상 네트워크에 연결되어 있어야 합니다.

    a. 이전 단계에서 만든 프라이빗 DNS 영역으로 이동한 후 페이지 왼쪽에 있는 가상 네트워크 링크로 이동합니다. **+추가** 단추를 선택합니다.   
    b. 필수 세부 정보를 입력합니다. **구독** 및 **가상 네트워크** 필드는 프라이빗 엔드포인트가 연결된 가상 네트워크의 해당 세부 정보로 채워야 합니다. 다른 필드는 모두 그대로 둘 수 있습니다.

3. 다음 단계는 DNS 레코드를 DNS 영역에 추가하는 것입니다. 스토리지 계정의 정규화된 도메인 이름에 대한 항목을 개인 DNS 영역에 추가합니다.

    a. 프라이빗 DNS 영역으로 이동한 후 페이지 왼쪽에 있는 **개요** 섹션으로 이동합니다. **+레코드 집합** 을 선택하여 레코드 추가를 시작합니다.  

    b. **레코드 집합 추가** 페이지에서 정규화된 도메인 이름 및 개인 IP 항목을 A 형식 레코드로 추가합니다.

> [!Important]
> 원본 환경에서 스토리지 계정의 프라이빗 엔드포인트에 대한 개인 IP 주소를 확인하려면 추가 DNS 설정이 필요할 수 있습니다. 필요한 DNS 구성을 이해하려면 [이 문서를 검토](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)하세요.

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>Microsoft 피어링을 사용하여 ExpressRoute 회로를 사용하여 데이터 복제

Microsoft 피어링 또는 기존 공용 피어링 도메인(새 ExpressRoute 연결에 사용되지 않음)을 사용하여 아래 다이어그램에 표시된 것처럼 ExpressRoute 회로를 통해 복제 트래픽을 라우팅할 수 있습니다.
![replicationwithmicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Microsoft 피어 링 회로를 통해 복제 데이터를 사용하는 경우에도 Azure Migrate 서비스를 사용하여 다른 통신(제어 평면)을 위해 온-프레미스 사이트에서 인터넷에 연결해야 합니다. ExpressRoute를 통해 연결할 수 없는 몇 가지 추가 URL이 있으며 복제 어플라이언스/Hyper-v 호스트에서 복제 프로세스를 오케스트레이션하는 데 액세스해야 합니다. 마이그레이션 시나리오, [VMware 에이전트](./migrate-appliance.md#public-cloud-urls) 없는 마이그레이션 또는 [에이전트 기반 마이그레이션](./migrate-replication-appliance.md)에 따라 URL 요구 사항을 검토할 수 있습니다.  

온-프레미스 사이트에서 프록시를 사용하고 복제 트래픽에 대해 ExpressRoute를 사용하려는 경우 온-프레미스 어플라이언스의 관련 URL에 대해 프록시 바이패스를 구성해야 합니다. 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Azure Migrate 어플라이언스에서 프록시 바이패스 규칙 구성(VMware 에이전트 없는 마이그레이션의 경우)

1. Azure Migrate 어플라이언스에 로그인(원격 데스크톱)합니다.   
2. 메모장을 사용하여 C:/ProgramData/Microsoftazure.mobileengagement/Config/appliance.js파일을 엽니다.
3. 파일에서 "EnableProxyBypassList": "false"로 표시되는 줄을 "EnableProxyBypassList": "true"로 변경합니다. 변경 내용을 저장하고 어플라이언스를 다시 시작합니다.
4. 다시 시작한 후에는 어플라이언스 구성 관리자를 열면 웹 앱 UI에서 프록시 바이패스 옵션을 볼 수 있습니다. 프록시 바이패스 목록에 아래 URL을 추가합니다.   
    - *vault.azure.net
    - .*.servicebus.windows.net
    - .*.discoverysrv.windowsazure.com
    - .*.migration.windowsazure.com
    - .\*.hypervrecoverymanager.windowsazure.com
    - .*.blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>복제 어플라이언스에 대한 프록시 바이패스 규칙 구성(에이전트 기반 마이그레이션의 경우)

구성 서버 및 프로세스 서버에서 프록시 바이패스 목록을 구성하려면 다음 단계를 수행합니다.

1. [PsExec 도구를 다운로드](/sysinternals/downloads/psexec)하여 시스템 사용자 컨텍스트에 액세스합니다.
2. 명령줄 psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"를 실행하여 시스템 사용자 콘텐츠에서 Internet Explorer를 엽니다
3. IE에서 프록시 설정을 추가합니다.
4. 바이패스 목록에서 Azure storage URL.*.blob.core.windows.net을 추가합니다.  

위의 무시 규칙은 관리 통신이 인터넷에 대한 프록시를 통과할 수 있는 동안 ExpressRoute를 통해 복제 트래픽이 이동할 수 있도록 합니다.  

또한 Azure Migrate 복제 트래픽이 인터넷 대신 ExpressRoute 회로를 통과하도록 하려면 다음 BGP 커뮤니티에 대한 경로 필터에서 경로를 보급해야 합니다.  

- 원본 Azure 지역에 대한 지역 BGP 커뮤니티(Azure Migrate 프로젝트 영역)
- 대상 Azure 지역에 대한 지역 BGP 커뮤니티(마이그레이션 영역)
- Azure Active Directory에 대한 BGP 커뮤니티(12076:5060)

[경로 필터](../expressroute/how-to-routefilter-portal.md) 및 [ExpressRoute에 대한 BGP 커뮤니티](../expressroute/expressroute-routing.md#bgp)목록에 대해 자세히 알아보세요. 

## <a name="next-steps"></a>다음 단계

- [ExpressRoute 회로](../expressroute/expressroute-circuit-peerings.md)에 대해 자세히 알아봅니다.
- [ExpressRoute 라우팅 도메인](../expressroute/expressroute-circuit-peerings.md#peeringcompare)에 대해 자세히 알아봅니다.
- [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)에 대해 자세히 알아보세요.
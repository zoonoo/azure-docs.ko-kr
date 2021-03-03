---
title: Azure Migrate Server Migration을 사용 하 여 Express 경로를 통해 데이터 복제
description: Azure Migrate Server Migration을 사용 하 여 복제에 Azure Express 경로를 사용 하는 방법
author: ms-deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: fe44ff423240cbe24d91cef0c0f4bb803ad8df98
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745564"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Azure Migrate로 Express 경로를 통해 데이터 복제: 서버 마이그레이션

이 문서에서는 서버를 Azure로 마이그레이션하는 동안 Express 경로 회로를 통해 데이터를 복제 하도록 [서버 마이그레이션을 Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) 구성 하는 방법에 대해 알아봅니다.

## <a name="understand-azure-expressroute-circuits"></a>Azure Express 경로 회로 이해
Express 경로 (ER) 회로는 연결 공급자를 통해 온-프레미스 인프라를 Microsoft에 연결 합니다. Express 경로 회로는 개인 피어 링, Microsoft 피어 링 또는 둘 다를 사용 하도록 구성할 수 있습니다. Express 경로 [회로 및 피어 링](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare) 의 문서를 검토 하 여 express 경로에서 사용할 수 있는 다양 한 피어 링 옵션에 대해 자세히 알아보세요.

Azure Migrate의 서버 마이그레이션 도구를 사용 하면 다른 클라우드에서 온-프레미스 서버 및 서버를 Azure virtual machines로 마이그레이션할 수 있습니다. 이 도구는 Azure 구독의 관리 디스크로 마이그레이션할 서버에서 데이터를 복제 하기 위해 진행 중인 복제 스트림을 설정 하는 방식으로 작동 합니다. 서버를 마이그레이션할 준비가 되 면 Azure에서 복제 된 데이터가 서버를 마이그레이션하는 데 사용 됩니다.

온-프레미스 서버에서 복제 된 데이터는 인터넷을 통해 (보안 암호화 된 연결 사용) 또는 Express 경로 연결을 통해 Azure 구독에 전송 되도록 구성할 수 있습니다. 마이그레이션할 서버가 많은 경우, 복제를 위해 Express 경로를 사용 하면 Express 경로 회로에서 사용할 수 있는 프로 비전 된 대역폭을 사용 하 여 서버를 보다 효율적으로 마이그레이션하는 데 도움이 됩니다.

이 문서에서는 다음에 대해 알아봅니다.
> [!div class="checklist"]
>
> * 개인 피어 링을 사용 하는 Express 경로 회로를 사용 하 여 데이터를 복제 하는 방법
> * Microsoft 피어 링을 통해 Express 경로 회로를 사용 하 여 데이터를 복제 하는 방법입니다.

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>개인 피어 링을 사용 하는 Express 경로 회로를 사용 하 여 데이터 복제

> [!NOTE]
> 개인 피어 링 회로를 통한 복제는 현재 [Azure에 대 한 VMware 가상 컴퓨터의 에이전트 없는 마이그레이션](./tutorial-migrate-vmware.md)에만 지원 됩니다. 다른 [복제 방법](./migrate-services-overview.md#azure-migrate-server-migration-tool) 에 대 한 개인 끝점 지원은 곧 제공 될 예정입니다.

VMware 가상 머신을 Azure로 마이그레이션하는 에이전트 없는 방법에서 Azure Migrate 어플라이언스는 먼저 구독의 저장소 계정 (캐시 저장소 계정)에 복제 데이터를 업로드 합니다. 그러면 캐시 저장소 계정에서 복제 된 데이터가 Azure Migrate 서비스에서 구독의 복제본 관리 디스크로 이동 합니다. 복제에 개인 피어 링 회로를 사용 하려면 개인 끝점을 만들어 캐시 저장소 계정에 연결 합니다. 개인 끝점은 VNet (가상 네트워크)에서 하나 이상의 개인 IP 주소를 사용 하 여 저장소 계정을 Azure VNet에 효과적으로 제공 합니다. 개인 끝점을 사용 하면 Azure Migrate 어플라이언스에서 Express 경로 개인 피어 링을 사용 하 여 캐시 저장소 계정에 연결 하 고 개인 IP 주소에서 직접 데이터를 전송할 수 있습니다. <br/>  

![복제 프로세스](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - Azure Migrate 어플라이언스는 복제 데이터 외에도 오케스트레이션 복제를 포함 하 여 제어 평면 작업에 대 한 Azure Migrate 서비스와 통신 합니다. Azure Migrate 어플라이언스와 Azure Migrate 서비스 간의 제어 평면 통신은 Azure Migrate 서비스의 공용 끝점에서 인터넷을 통해 계속 해 서 발생 합니다.
> - 저장소 계정의 개인 끝점은 Azure Migrate 어플라이언스가 배포 된 네트워크에서 액세스할 수 있어야 합니다.
> - 캐시 저장소 계정의 blob service 끝점에 대 한 Azure Migrate 어플라이언스의 DNS 쿼리를 캐시 저장소 계정에 연결 된 개인 끝점의 개인 IP 주소로 확인 하도록 DNS를 구성 해야 합니다.
> - 캐시 저장소 계정은 공용 끝점에서 액세스할 수 있어야 합니다. Azure Migrate 서비스는 캐시 저장소 계정의 공용 끝점을 사용 하 여 저장소 계정에서 복제본 관리 디스크로 데이터를 이동 합니다. 


### <a name="1-pre-requisites"></a>1. 필수 구성 요소

개인 끝점을 만드는 Azure 사용자에 게는 개인 끝점이 생성 될 리소스 그룹 및 가상 네트워크에 대 한 다음 권한이 있어야 합니다.

**사용 사례** | **권한** 
--- | --- 
 개인 끝점을 만들고 관리 합니다. | Microsoft. Network/privateEndpoint/write/action<br/>Microsoft. Network/privateEndpoint/read/action  
|개인 끝점을 VNet/서브넷에 연결 합니다.<br/>이는 개인 끝점이 생성 될 가상 네트워크에 필요 합니다.| Microsoft. networks/virtualNetworks/subnet/join/action Microsoft. Network/virtualNetworks/join/action
|저장소 계정에 개인 끝점을 연결 합니다. <br/>| Microsoft. Storage/storageAccounts/privateEndpointConnectionApproval/action <br/> Microsoft. Storage/storageAccounts/privateEndpointConnections/read
|네트워크 인터페이스를 만들고 네트워크 보안 그룹에 조인 합니다. | Microsoft.Network/networkInterfaces/read <br/> Microsoft. 네트워크/networkInterfaces/서브넷/쓰기 <br/> Microsoft. 네트워크/networkInterfaces/서브넷/읽기<br/> Microsoft. Network/networkSecurityGroups/join/action (선택 사항)
개인 DNS 영역을 만들고 관리 합니다.| 사설 DNS 영역 기여자 역할 <br/> _Or_ <br/> Microsoft. Network/privateDnsZones/A/* <br/>  Microsoft. Network/privateDnsZones/write privateDnsZones/read <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/write <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/read <br/> Microsoft. Network/privateDnsZones/virtualNetworkLinks/write <br/>  Microsoft. Network/privateDnsZones/virtualNetworkLinks/read <br/> Microsoft. Network/virtualNetworks/join/action 

### <a name="2-identify-the-cache-storage-account"></a>2. 캐시 저장소 계정 식별 
 
Azure Migrate는 Azure Migrate 프로젝트에서 가상 머신에 대 한 복제 (Azure Portal 환경 사용)를 처음으로 구성할 때 캐시 저장소 계정을 자동으로 만듭니다. 저장소 계정은 Azure Migrate 프로젝트를 만든 것과 동일한 구독 및 리소스 그룹에 만들어집니다.

저장소 계정을 만들고 찾으려면 다음을 수행 합니다.

1. Azure Migrate에 대 한 Azure Portal 환경을 사용 하 여 프로젝트에서 하나 이상의 가상 컴퓨터를 복제 합니다.
2. Azure Migrate 프로젝트의 리소스 그룹으로 이동 합니다.
3. 저장소 계정 이름에서 **"lsa"** 접두사를 식별 하 여 캐시 저장소 계정을 찾습니다.

![리소스 그룹 보기](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> 리소스 그룹에 **"lsa"** 접두사가 포함 된 둘 이상의 저장소 계정이 있는 경우 프로젝트의 복제 vm에 대 한 복제 설정 및 대상 구성 메뉴로 이동 하 여 저장소 계정을 확인할 수 있습니다. <br/> 
> ![복제 설정 개요](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. 캐시 저장소 계정을 범용 v2로 업그레이드 

범용 v2 (GPv2) 저장소 계정 에서만 개인 끝점을 만들 수 있습니다. 캐시 저장소 계정이 GPv2 저장소 계정이 아닌 경우 다음 단계를 사용 하 여 GPv2로 업그레이드 합니다.

1. 스토리지 계정으로 이동합니다.
2. **Configuration(구성)** 을 선택합니다.
3. **계정 종류** 아래에서 **업그레이드** 를 선택 합니다.
4. **업그레이드 확인** 에서 계정 이름을 입력 합니다.
5. 페이지 맨 아래에서 **업그레이드** 를 선택 합니다.

![저장소 계정 업그레이드](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. 저장소 계정에 대 한 개인 끝점을 만듭니다.

1. 저장소 계정으로 이동 하 여 왼쪽 메뉴에서 **네트워킹** 을 선택 하 고 **개인 끝점 연결** 탭을 선택 합니다.  
2. **+ 프라이빗 엔드포인트** 를 선택합니다.

    a. **개인 끝점 만들기** 창에서 **구독** 및 **리소스 그룹** 을 선택 합니다. 개인 끝점의 이름을 입력 하 고 저장소 계정 지역을 선택 합니다.  
    ![PE 구성 창](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. **리소스** 탭에서 저장소 계정이 있는 **구독 이름을** 제공 합니다. **리소스 유형** 으로 **Microsoft. Storage/storageaccounts** 를 선택 합니다. **리소스** 에서 GPv2 유형의 복제 저장소 계정 이름을 제공 합니다. **대상 하위 리소스로** **Blob** 을 선택 합니다.  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    다. **구성** 탭에서 저장소 계정의 개인 끝점에 대 한 **가상 네트워크** 및 **서브넷** 을 선택 합니다.  

    > [!Note]
    > 가상 네트워크는 Express 경로 게이트웨이 끝점을 포함 하거나 Express 경로 게이트웨이를 사용 하 여 가상 네트워크에 연결 되어야 합니다. 

    **사설 DNS 통합** 섹션에서 **예** 를 선택 하 고 개인 DNS 영역과 통합 합니다. **예** 를 선택 하면 선택한 가상 네트워크에 dns 영역이 자동으로 연결 되 고, 새 IP의 dns 확인에 필요한 dns 레코드와 개인 끝점에 대해 생성 된 정규화 된 도메인 이름이 추가 됩니다. [개인 DNS 영역](https://docs.microsoft.com/azure/dns/private-dns-overview) 에 대해 자세히 알아보세요.

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. 개인 끝점에 대 한 **태그** 를 추가할 수도 있습니다.  

    e. 자세히 입력을 완료 한 후 계속 **검토 + 만들기** 를 계속 합니다. 유효성 검사가 완료 되 면 **만들기** 를 선택 하 여 개인 끝점을 만듭니다.

    > [!Note]
    > 개인 끝점을 만드는 사용자가 저장소 계정의 소유자 이기도 한 경우 개인 끝점은 자동으로 승인 됩니다. 그렇지 않으면 소유자는 사용을 위해 개인 끝점을 승인 해야 합니다. 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>개인 DNS 영역을 만들고 DNS 레코드를 수동으로 추가 (선택 사항)

개인 끝점을 만들 때 개인 DNS 영역과 통합 하는 옵션을 선택 하지 않은 경우이 섹션의 단계에 따라 수동으로 개인 DNS 영역을 만듭니다. 

> [!Note]
> **예** 를 선택 하 여 개인 DNS 영역과 통합 하는 경우이 섹션을 건너뛸 수 있습니다. 

1. 개인 DNS 영역을 만듭니다. 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a.  **사설 DNS 영역** 페이지에서 **+ 추가** 단추를 선택 하 여 새 영역 만들기를 시작 합니다.  
    b.  **개인 DNS 영역 만들기** 페이지에서 필요한 세부 정보를 입력 합니다. 개인 DNS 영역의 이름을 _privatelink_. blob.core.windows.net로 입력 합니다. 다. **검토 + 만들기** 탭으로 이동 하 여 DNS 영역을 검토 하 고 만듭니다.

2. 가상 네트워크에 개인 DNS 영역을 연결 합니다.  

    위에서 만든 개인 DNS 영역은 개인 끝점이 연결 된 가상 네트워크에 연결 되어 있어야 합니다.

    a. 이전 단계에서 만든 개인 DNS 영역으로 이동 하 여 페이지의 왼쪽에 있는 가상 네트워크 링크로 이동 합니다. **+ 추가** 단추를 선택 합니다.   
    b. 필요한 세부 정보를 입력 합니다. **구독** 및 **가상 네트워크** 필드는 개인 끝점이 연결 된 가상 네트워크의 해당 세부 정보로 채워야 합니다. 다른 필드는 그대로 둘 수 있습니다.

3. 다음 단계는 dns 레코드를 DNS 영역에 추가 하는 것입니다. 저장소 계정의 정규화 된 도메인 이름에 대 한 항목을 개인 DNS 영역에 추가 합니다.

    a. 개인 DNS 영역으로 이동 하 여 페이지의 왼쪽에 있는 **개요** 섹션으로 이동 합니다. 레코드 추가를 시작 하려면 **+ 레코드** 집합을 선택 합니다.  

    b. **레코드 집합 추가** 페이지에서 정규화 된 도메인 이름 및 개인 IP에 대 한 항목을 유형 레코드로 추가 합니다.

> [!Important]
> 원본 환경에서 저장소 계정의 개인 끝점에 대 한 개인 IP 주소를 확인 하려면 추가 DNS 설정이 필요할 수 있습니다. 필요한 DNS 구성을 이해 하려면 [이 문서를 검토](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) 하세요.

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>Microsoft 피어 링을 사용 하 여 Express 경로 회로를 사용 하 여 데이터 복제

Microsoft 피어 링 또는 기존 공용 피어 링 도메인 (새 Express 경로 연결에 사용 되지 않음)을 사용 하 여 아래 다이어그램에 표시 된 것 처럼 Express 경로 회로를 통해 복제 트래픽을 라우팅할 수 있습니다.
![replicationwithmicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Microsoft 피어 링 회로를 통해 복제 데이터를 사용 하는 경우에도 Azure Migrate 서비스를 사용 하 여 다른 통신 (제어 평면)을 위해 온-프레미스 사이트에서 인터넷에 연결 해야 합니다. Express 경로를 통해 연결할 수 없는 몇 가지 추가 Url이 있으며 복제 어플라이언스/Hyper-v 호스트에서 복제 프로세스를 오케스트레이션 하는 데 액세스 해야 합니다. 마이그레이션 시나리오, [VMware 에이전트](https://docs.microsoft.com/azure/migrate/migrate-appliance#public-cloud-urls) 없는 마이그레이션 또는 [에이전트 기반 마이그레이션](https://docs.microsoft.com/azure/migrate/migrate-replication-appliance)에 따라 URL 요구 사항을 검토할 수 있습니다.  

온-프레미스 사이트에서 프록시를 사용 하 고 복제 트래픽에 대해 Express 경로를 사용 하려는 경우 온-프레미스 어플라이언스의 관련 Url에 대해 프록시 바이패스를 구성 해야 합니다. 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Azure Migrate 어플라이언스에서 프록시 바이패스 규칙 구성 (VMware 에이전트 없는 마이그레이션의 경우)

1. Azure Migrate 어플라이언스에 로그인 (원격 데스크톱) 합니다.   
2. 메모장을 사용 하 여 C:/ProgramData/Microsoftazure.mobileengagement/Config/appliance.js파일을 엽니다.
3. 파일에서 "EnableProxyBypassList": "false"로 표시 되는 줄을 "EnableProxyBypassList": "true"로 변경 합니다. 변경 내용을 저장 하 고 어플라이언스를 다시 시작 합니다.
4. 다시 시작한 후에는 어플라이언스 구성 관리자를 열면 웹 앱 UI에서 프록시 바이패스 옵션을 볼 수 있습니다. 프록시 바이패스 목록에 아래 Url을 추가 합니다.   
    - . *. vault.azure.net
    - . *. servicebus.windows.net
    - . *. discoverysrv.windowsazure.com
    - . *. migration.windowsazure.com
    - . *. hypervrecoverymanager.windowsazure.com
    - . *. blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>복제 어플라이언스에 대 한 프록시 바이패스 규칙 구성 (에이전트 기반 마이그레이션의 경우)

구성 서버 및 프로세스 서버에서 프록시 바이패스 목록을 구성 하려면 다음 단계를 수행 합니다.

1. [PsExec 도구를 다운로드](https://docs.microsoft.com/sysinternals/downloads/psexec) 하 여 시스템 사용자 컨텍스트에 액세스 합니다.
2. 다음 명령줄을 실행 하 여 Internet Explorer를 시스템 사용자 컨텍스트에서 엽니다. psexec-i "%Programfiles%\internet explorer\ Explorer\iexplore.exe"
3. IE에서 프록시 설정을 추가 합니다.
4. 바이패스 목록에서 Azure storage URL. *. blob. w i n.  

위의 무시 규칙은 관리 통신이 인터넷에 대 한 프록시를 통과할 수 있는 동안 Express 경로를 통해 복제 트래픽을 이동할 수 있도록 합니다.  

또한 Azure Migrate 복제 트래픽이 인터넷 대신 Express 경로 회로를 통과 하도록 하려면 다음 BGP 커뮤니티에 대 한 경로 필터에서 경로를 보급 해야 합니다.  

- 원본 Azure 지역에 대 한 지역 BGP 커뮤니티 (Azure Migrate 프로젝트 영역)
- 대상 Azure 지역에 대 한 지역 BGP 커뮤니티 (마이그레이션 지역)
- Azure Active Directory에 대 한 BGP 커뮤니티 (12076:5060)

[경로 필터](https://docs.microsoft.com/azure/expressroute/how-to-routefilter-portal) 및 [express 경로에 대 한 BGP 커뮤니티](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp)목록에 대해 자세히 알아보세요. 

## <a name="next-steps"></a>다음 단계

- [ExpressRoute 회로](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings)에 대해 자세히 알아봅니다.
- [ExpressRoute 라우팅 도메인](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare)에 대해 자세히 알아봅니다.
- [개인 끝점](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)에 대해 자세히 알아보세요.
---
title: Azure 파일 동기화 네트워킹 고려 사항 | Microsoft Docs
description: Azure File Sync를 사용 하 여 온-프레미스에서 파일을 캐시 하도록 네트워킹을 구성 하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76d97e3312c1df51193d8a881f3ee07fcd155d75
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629362"
---
# <a name="azure-file-sync-networking-considerations"></a>Azure 파일 동기화 네트워킹 고려 사항
다음 두 가지 방법으로 Azure 파일 공유에 연결할 수 있습니다.

- SMB 또는 FileREST 프로토콜을 통해 공유에 직접 액세스합니다. 이 액세스 패턴은 최대한 많은 온-프레미스 서버를 제거할 때 주로 사용됩니다.
- Azure 파일 동기화를 사용하여 온-프레미스 서버(또는 Azure VM)에서 Azure 파일 공유 캐시를 만들고, 사용 사례에 원하는 프로토콜(SMB, NFS, FTPS 등)을 사용하여 온-프레미스 서버에서 파일 공유의 데이터에 액세스합니다. 이 액세스 패턴은 온-프레미스 성능 및 클라우드 규모의 연결 가능한 서버리스 서비스(예: Azure Backup) 모두를 가장 효율적으로 결합하므로 편리합니다.

이 문서에서는 사용 사례가 SMB를 통해 Azure 파일 공유를 직접 탑재하지 않고 온-프레미스에서 파일을 캐시하는 데 Azure 파일 동기화를 사용하는 경우에 대한 네트워킹을 구성하는 방법을 중점적으로 설명합니다. Azure Files 배포에 대한 네트워킹 고려 사항에 대한 자세한 내용은 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조하세요.

Azure 파일 동기화에 대한 네트워킹 구성은 스토리지 동기화 서비스와 Azure 스토리지 계정이라는 두 가지 Azure 개체를 포괄합니다. 스토리지 계정은 여러 파일 공유뿐만 아니라 다른 스토리지 리소스(예: Blob 컨테이너 또는 큐)도 배포할 수 있는 공유 스토리지 풀을 나타내는 관리 구조입니다. 스토리지 동기화 서비스는 등록된 서버를 나타내는 관리 구문입니다. 그리고 등록된 서버는 Azure 파일 동기화와 설정된 신뢰 관계를 갖고 있는 Windows 파일 서버와 동기화 관계의 토폴로지를 정의하는 동기화 그룹으로 구성되어 있습니다. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Azure 파일 동기화를 사용하여 Windows 파일 서버를 Azure에 연결 
온-프레미스 Windows 파일 서버를 사용하여 Azure Files 및 Azure 파일 동기화를 설정 및 사용하려면 기본 인터넷 연결을 통해 Azure에 대한 특별한 네트워킹이 필요 지 않습니다. Azure 파일 동기화를 배포하려면 Azure와 동기화하려는 Windows 파일 서버에 Azure 파일 동기화 에이전트를 설치합니다. Azure 파일 동기화 에이전트는 다음 두 채널을 통해 Azure 파일 공유와 동기화합니다.

- Azure 파일 공유에 액세스하기 위한 HTTPS 기반 프로토콜인 FileREST 프로토콜입니다. FileREST 프로토콜은 데이터 전송에 표준 HTTPS를 사용하기 때문에 포트 443만 아웃 바운드에 액세스할 수 있어야 합니다. Azure 파일 동기화는 SMB 프로토콜을 사용하여 온-프레미스 Windows Server에서 Azure 파일 공유로 데이터를 전송하지 않습니다.
- 동기화 정보를 교환하기 위한 HTTPS 기반 프로토콜인 Azure 파일 동기화의 동기화 프로토콜(즉, 환경에서 엔트포인트 간의 파일 및 폴더에 대한 버전 정보)입니다. 이 프로토콜은 타임 스탬프 및 ACL(액세스 제어 목록)과 같은 사용자 환경의 파일 및 폴더에 대한 메타데이터를 교환하는 데에도 사용됩니다. 

Azure Files는 Azure 파일 공유에 대한 직접 SMB 프로토콜 액세스를 제공하기 때문에, 고객은 Azure 파일 동기화 에이전트에서 액세스할 수 있도록 SMB를 사용하여 Azure 파일 공유를 탑재하기 위해 특수 네트워킹을 구성해야 하는지 여부를 종종 궁금해합니다. 이는 반드시 필요한 것은 아니지만, Azure 파일 공유의 직접적인 변경 내용에 대한 빠른 변경 검색이 부족한 관리자 시나리오를 제외하고는 권장되지 않습니다(Azure 파일 공유의 항목 크기와 수에 따라 변경 내용이 24시간 넘게 검색되지 않을 수 있습니다). Azure 파일 공유를 직접 사용하려는 경우(즉, 온-프레미스를 캐시하는 Azure 파일 동기화를 사용하지 않는 경우) [Azure Files 네트워킹 개요](storage-files-networking-overview.md)를 통해 직접 액세스하기 위한 네트워킹 고려 사항에 대해 자세히 알아볼 수 있습니다.

Azure 파일 동기화에 특별한 네트워킹 구성이 필요하지는 않지만 일부 고객은 다음 시나리오를 사용하도록 고급 네트워킹 설정을 구성할 수 있습니다.

- 조직의 프록시 서버 구성과 상호 운용
- Azure Files 및 Azure 파일 동기화 서비스에 대한 조직의 온-프레미스 방화벽을 엽니다.
- Express 경로 또는 VPN 연결을 통해 Azure Files 및 Azure 파일 동기화를 터널링합니다.

### <a name="configuring-proxy-servers"></a>프록시 서버 구성
대부분의 조직에서는 온-프레미스 네트워크 내부의 리소스와 Azure와 같은 네트워크 외부의 리소스 사이에 프록시 서버를 중개자로 사용합니다. 프록시 서버는 네트워크 격리, 보안, 모니터링 및 로깅과 같은 많은 애플리케이션에 유용합니다. Azure 파일 동기화는 프록시 서버와 완전하게 상호 운용할 수 있지만 Azure 파일 동기화를 사용하여 환경에 대한 프록시 엔드포인트 설정을 수동으로 구성해야 합니다. Azure 파일 동기화 server cmdlet을 사용하여 PowerShell을 통해 이 작업을 수행해야 합니다. 

프록시 서버를 사용하여 Azure 파일 동기화를 구성하는 방법에 대한 자세한 내용은 [프록시 서버로 Azure 파일 동기화 구성](storage-sync-files-firewall-and-proxy.md)을 참조하세요.

### <a name="configuring-firewalls-and-service-tags"></a>방화벽 및 서비스 태그 구성
보안을 위해 대부분의 인터넷 위치에서 파일 서버를 격리할 수 있습니다. 사용자 환경에서 Azure 파일 동기화를 사용 하려면 방화벽을 조정하여 Azure 서비스를 선택해야 합니다. 이렇게 하려면 [서비스 태그](../../virtual-network/service-tags-overview.md)라는 메커니즘을 통해 필요한 서비스의 IP 주소 범위를 검색합니다.

Azure 파일 동기화에는 서비스 태그로 식별된 다음 서비스의 IP 주소 범위가 필요합니다.

| 서비스 | Description | 서비스 태그 |
|---------|-------------|-------------|
| Azure 파일 동기화 | 스토리지 동기화 서비스 개체에 표시되는 Azure 파일 동기화 서비스는 Azure 파일 공유와 Windows 파일 서버 간에 데이터를 동기화하는 핵심 활동을 담당합니다. | `StorageSyncService` |
| Azure 파일 | Azure 파일 동기화를 통해 동기화되는 모든 데이터는 Azure 파일 공유에 저장됩니다. Windows 파일 서버에서 변경된 파일은 Azure 파일 공유로 복제되고 온-프레미스 파일 서버에서 계층화된 파일은 사용자가 요청하면 원활하게 다운로드 됩니다. | `Storage` |
| Azure 리소스 관리자 | Azure Resource Manager는 Azure용 관리 인터페이스입니다. Azure 파일 동기화 서버 등록 및 지속적인 동기화 서버 작업을 비롯한 모든 관리 호출은 Azure Resource Manager를 통해 수행됩니다. | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory 또는 Azure AD에는 스토리지 동기화 서비스에 대해 서버 등록 권한을 부여하는 데 필요한 사용자 계정 및 클라우드 리소스에 대한 액세스 권한이 있는 Azure 파일 동기화에 필요한 서비스 사용자가 포함됩니다. | `AzureActiveDirectory` |

Azure 내에서 Azure 파일 동기화를 사용하는 경우 다른 지역이더라도 네트워크 보안 그룹에서 직접 서비스 태그 이름을 사용하여 해당 서비스에 대한 트래픽을 허용할 수 있습니다. NSG에 대한 자세한 내용은 [네트워크 보안 그룹](../../virtual-network/network-security-groups-overview.md)을 참조하세요. 

온-프레미스 Azure 파일 동기화를 사용하는 경우 서비스 태그 API를 사용하여 방화벽의 허용 목록에 대한 특정 IP 주소 범위를 가져올 수 있습니다. 이 정보를 가져오는 방법에는 다음 두 가지가 있습니다.

- 서비스 태그를 지원하는 모든 Azure 서비스에 대한 IP 주소 범위의 현재 목록은 Microsoft 다운로드 센터에서 JSON 문서 형식으로 매주 게시됩니다. 각 Azure 클라우드는 해당 클라우드와 관련된 IP 주소 범위를 포함하는 자체 JSON 문서를 포함합니다.
    - [Azure 공용](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure 미국 정부](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure 중국](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure 독일](https://www.microsoft.com/download/details.aspx?id=57064)
- 서비스 태그 검색 API(미리 보기)를 사용 하면 현재 서비스 태그 목록을 프로그래밍 방식으로 검색할 수 있습니다. 미리 보기에서 서비스 태그 검색 API는 Microsoft 다운로드 센터에 게시된 JSON 문서에서 반환된 정보 보다 최신 정보가 아닌 정보를 반환할 수 있습니다. 자동화 기본 설정에 따라 API surface를 사용할 수 있습니다.
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az-network-list-service-tags)

서비스 태그 API를 사용하여 서비스의 주소를 검색하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 IP 주소에 대한 허용 목록](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses)을 참조하십시오.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>가상 개인 네트워크 또는 ExpressRoute를 통해 트래픽 터널링
일부 조직에서는 Azure와의 통신을 통해 VPN(가상 사설망) 또는 ExpressRoute와 같은 네트워크 터널을 통해 이동하여 보안을 강화하거나 Azure와의 통신이 결정적 경로를 따르는지 확인해야 합니다. 

온-프레미스 네트워크와 Azure 간에 네트워크 터널을 설정하는 경우 온-프레미스 네트워크를 Azure에 있는 하나 이상의 가상 네트워크와 피어링합니다. [가상 네트워크](../../virtual-network/virtual-networks-overview.md) 또는 VNet은 온-프레미스에서 작동하는 기존 네트워크와 비슷합니다. Azure 스토리지 계정 또는 Azure VM과 마찬가지로 VNet은 리소스 그룹에 배포된 Azure 리소스입니다. 

Azure Files 및 File Sync에서 지원하는 온-프레미스 서버 및 Azure 간의 트래픽을 터널링하는 메커니즘은 다음과 같습니다.

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): VPN Gateway는 인터넷을 통해 Azure 가상 네트워크와 대체 위치(예: 온-프레미스) 간에 암호화된 트래픽을 보내는 데 사용되는 특정 유형의 가상 네트워크 게이트웨이입니다. Azure VPN Gateway는 스토리지 계정 또는 다른 Azure 리소스와 함께 리소스 그룹에 배포할 수 있는 Azure 리소스입니다. Azure 파일 동기화는 온-프레미스 Windows 파일 서버와 함께 사용되기 때문에 일반적으로 [사이트 간(S2S) VPN](../../vpn-gateway/design.md#s2smulti)을 사용할 수 있지만 기술적으로 [지점 및 사이트 간(P2S) VPN](../../vpn-gateway/point-to-site-about.md)도 사용할 수 있습니다. 

    S2S(사이트 간) VPN 연결은 Azure 가상 네트워크와 조직의 온-프레미스 네트워크를 연결합니다. S2S VPN 연결을 사용하면 VPN 연결을 Azure 파일 공유에 액세스해야 하는 모든 클라이언트 디바이스에 대해 구성하는 것이 아니라 조직의 네트워크에서 호스팅되는 VPN 서버 또는 디바이스에 대해 한 번만 구성할 수 있습니다. S2S VPN 연결의 배포를 간소화하려면 [Azure Files에서 사용하도록 S2S(사이트 간) VPN 구성](storage-files-configure-s2s-vpn.md)을 참조하세요.

- [ExpressRoute](../../expressroute/expressroute-introduction.md) - 이를 사용하면 인터넷을 트래버스하지 않는 온-프레미스 네트워크와 Azure 간에 정의된 경로를 만들 수 있습니다. ExpressRoute는 온-프레미스 데이터 센터와 Azure 간의 전용 경로를 제공하므로 네트워크 성능을 고려할 때 ExpressRoute가 유용할 수 있습니다. ExpressRoute는 조직의 정책 또는 규정 요구 사항에서 클라우드의 리소스에 대한 결정적 경로가 필요한 경우에도 유용한 옵션입니다.

### <a name="private-endpoints"></a>프라이빗 엔드포인트
Azure Files 및 File Sync는 스토리지 계정 및 스토리지 동기화 서비스를 통해 기본 공용 엔드포인트를 제공하는 것 외에 리소스당 하나 이상의 프라이빗 엔드포인트를 포함하는 옵션을 제공합니다. Azure 리소스에 대한 프라이빗 엔드포인트를 만들면 가상 네트워크의 주소 공간 내에서 개인 IP 주소를 가져옵니다. 이는 온-프레미스 Windows 파일 서버가 온-프레미스 네트워크의 전용 주소 공간 내에 있는 IP 주소를 받는 것과 비슷합니다. 

> [!Important]  
> 스토리지 동기화 서비스 리소스에서 프라이빗 엔드포인트를 사용하려면 Azure 파일 동기화 에이전트 버전 10.1 이상을 사용해야 합니다. 10.1 이전 버전의 에이전트는 스토리지 동기화 서비스에서 프라이빗 엔드포인트를 지원하지 않습니다. 모든 이전 에이전트 버전은 스토리지 계정 리소스에서 프라이빗 엔드포인트를 지원합니다.

개별 프라이빗 엔드포인트는 특정 Azure 가상 네트워크 서브넷과 연결됩니다. 스토리지 계정 및 스토리지 동기화 서비스는 둘 이상의 가상 네트워크에 프라이빗 엔드포인트를 가질 수 있습니다.

프라이빗 엔드포인트를 사용하면 다음 작업을 수행할 수 있습니다.
- 프라이빗 피어링을 통한 VPN 또는 ExpressRoute 연결을 사용하여 온-프레미스 네트워크에서 Azure 리소스에 안전하게 연결합니다.
- Azure Files 및 File Sync에 대한 공용 엔드포인트를 사용하지 않도록 설정하여 Azure 리소스를 보호합니다. 기본적으로 프라이빗 엔드포인트를 만드는 경우에도 퍼블릭 엔드포인트에 대한 연결이 차단되지 않습니다.
- 가상 네트워크(및 피어링 경계)에서 데이터 반출을 차단할 수 있도록 하여 가상 네트워크의 보안을 강화합니다.

프라이빗 엔드포인트를 만들려면 [Azure 파일 동기화에 대한 프라이빗 엔드포인트 구성](storage-sync-files-networking-endpoints.md)을 참조하세요.

### <a name="private-endpoints-and-dns"></a>프라이빗 엔드포인트 및 DNS
프라이빗 엔드포인트를 만드는 경우 기본적으로 `privatelink` 하위 도메인에 해당하는 프라이빗 DNS 영역을 만들거나 기존 영역을 업데이트합니다. 공용 클라우드 지역의 경우 이러한 DNS 영역은 Azure File용 `privatelink.file.core.windows.net`과 Azure 파일 동기화용 `privatelink.afs.azure.net`입니다.

> [!Note]  
> 이 문서에서는 `core.windows.net` Azure 퍼블릭 지역에 대한 스토리지 계정 DNS 접미사를 사용합니다. 이는 Azure US Government 클라우드 및 Azure 중국 클라우드와 같은 Azure 소버린 클라우드에도 적용되며, 사용자 환경에 적합한 접미사로 바꾸기만 하면 됩니다. 

스토리지 계정 및 스토리지 동기화 서비스에 대한 프라이빗 엔드포인트를 만들 때 해당 프라이빗 DNS 영역에 대한 A 레코드를 만듭니다. 또한 일반 정규화된 도메인 이름이 관련 privatelink 이름에 대한 CNAMEs 되도록 공용 DNS 항목을 업데이트합니다. 이렇게 하면 요청자가 가상 네트워크 내부에 있을 때 정규화된 도메인 이름이 프라이빗 엔드포인트 IP 주소를 가리킬 수 있으며, 요청자가 가상 네트워크 외부에 있을 때 공용 엔드포인트 IP 주소를 가리킬 수 있습니다. 

Azure Files의 경우 각 프라이빗 엔드포인트는 프라이빗 엔드포인트의 개인 IP 주소 하나에 매핑되는 패턴 `storageaccount.privatelink.file.core.windows.net`뒤에 단일 정규화된 도메인 이름을 가집니다. Azure 파일 동기화의 경우 각 프라이빗 엔드포인트에는 관리, 동기화(주), 동기화(보조) 및 모니터링의 네 가지 서로 다른 Azure 파일 동기화 엔드포인트에 대한 네 개의 정규화된 도메인 이름이 있습니다. 이러한 엔드포인트에 대한 정규화된 도메인 이름은 일반적으로 이름에 비 ASCII 문자가 포함된 경우를 제외하고는 저장소 동기화 서비스의 이름을 따릅니다. 예를 들어 스토리지 동기화 서비스 이름이 미국 서부 2 지역에서 `mysyncservice`인 경우 해당 엔드포인트는 `mysyncservicemanagement.westus2.afs.azure.net`, `mysyncservicesyncp.westus2.afs.azure.net`, `mysyncservicesyncs.westus2.afs.azure.net`및 `mysyncservicemonitoring.westus2.afs.azure.net`가 될 수 있습니다. 스토리지 동기화 서비스의 각 프라이빗 엔드포인트는 4 개의 고유한 IP 주소를 포함합니다. 

Azure 프라이빗 DNS 영역이 프라이빗 엔드포인트가 포함된 가상 네트워크에 연결되어 있으므로 Azure VM의 PowerShell에서 `Resolve-DnsName` cmdlet(또는 Windows 및 Linux의 `nslookup`)을 호출하여 DNS 구성을 관찰할 수 있습니다.

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

이 예제에서는 `storageaccount.file.core.windows.net` 스토리지 계정이 프라이빗 엔드포인트의 개인 IP 주소(`192.168.0.4`)로 확인됩니다.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

온-프레미스에서 동일한 명령을 실행하면 동일한 스토리지 계정 이름이 스토리지 계정의 공용 IP 주소로 확인됩니다. `storageaccount.file.core.windows.net`은 `storageaccount.privatelink.file.core.windows.net`에 대한 CNAME 레코드이며, 스토리지 계정을 호스팅하는 Azure 스토리지 클러스터에 대한 CNAME 레코드입니다.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

이는 Azure Files 및 Azure 파일 동기화가 리소스 당 하나 이상의 프라이빗 엔드포인트 및 공용 엔드포인트를 노출할 수 있다는 사실을 반영합니다. 리소스의 정규화된 도메인 이름이 프라이빗 엔드포인트 IP 주소를 확인하도록 하려면 온-프레미스 DNS 서버에서 구성을 변경해야 합니다. 이 작업은 다음과 같이 여러 가지 방법으로 수행할 수 있습니다.

- 클라이언트의 호스트 파일을 수정하여 스토리지 계정에 대한 정규화된 도메인 이름 및 스토리지 동기화 서비스를 원하는 개인 IP 주소로 확인합니다. 이는 프라이빗 엔드포인트에 액세스해야 하는 모든 클라이언트를 변경해야 하므로 프로덕션 환경에는 권장되지 않습니다. 프라이빗 엔드포인트/리소스(삭제, 수정 등)에 대한 변경 내용은 자동으로 처리되지 않습니다.
- `privatelink.file.core.windows.net`에 대한 온-프레미스 서버에서 DNS 영역을 만들고 Azure 리소스에 대한 A 레코드를 `privatelink.afs.azure.net`합니다. 이는 온-프레미스 환경의 클라이언트가 각 클라이언트를 구성하지 않고도 Azure 리소스를 자동으로 확인할 수 있다는 이점이 있지만, 이 솔루션은 변경 내용을 반영하지 않으므로 호스트 파일을 수정하는 것과 비슷하게 불안정합니다. 이 솔루션은 불안정하지만 일부 환경에는 가장 적합할 수 있습니다.
- `core.windows.net`과 `afs.azure.net` 영역을 온-프레미스 DNS 서버에서 Azure 프라이빗 DNS 영역으로 전달합니다. Azure 프라이빗 DNS 호스트는 Azure 프라이빗 DNS 영역에 연결된 가상 네트워크 내에서만 액세스할 수 있는 특수 IP 주소(`168.63.129.16`)를 통해 연결할 수 있습니다. 이 제한을 해결하려면 `core.windows.net`과 `afs.azure.net`를 Azure 프라이빗 DNS 영역으로 전달하는 추가 DNS 서버를 가상 네트워크 내에서 실행할 수 있습니다. 이 설정을 간소화하기 위해 DNS 서버를 Azure 가상 네트워크에 자동으로 배포하고 원하는 대로 구성할 수 있는 PowerShell cmdlet을 제공했습니다. DNS 전달을 설정하는 방법에 대한 자세한 내용은 [Azure Files를 사용하여 DNS 구성](storage-files-networking-dns.md)을 참조하세요.

## <a name="encryption-in-transit"></a>전송 중인 암호화
Azure 파일 동기화 에이전트에서 Azure 파일 공유 또는 스토리지 동기화 서비스로 만들어진 연결은 항상 암호화됩니다. Azure 스토리지 계정에는 Azure Files(및 스토리지 계정에서 관리되는 다른 Azure 스토리지 서비스) 전송에 암호화를 요구 하지 않는 설정이 있지만, 이 설정을 사용하지 않으면 Azure Files와 통신할 때 Azure 파일 동기화 암호화에 영향을 주지 않습니다. 기본적으로 모든 Azure 스토리지 계정은 전송 중 암호화를 사용하도록 설정되어 있습니다. 

전송 중 암호화에 대한 자세한 내용은 [Azure Storage에서 보안 전송 필요](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

## <a name="see-also"></a>참고 항목
- [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
- [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
---
title: Azure Files 네트워킹 고려 사항 | Microsoft Docs
description: Azure Files의 네트워킹 옵션에 대한 개요입니다.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126539"
---
# <a name="azure-files-networking-considerations"></a>Azure Files 네트워킹 고려 사항 
다음 두 가지 방법으로 Azure 파일 공유에 연결할 수 있습니다.

- SMB 또는 FileREST 프로토콜을 통해 공유에 직접 액세스합니다. 이 액세스 패턴은 최대한 많은 온-프레미스 서버를 제거할 때 주로 사용됩니다.
- Azure 파일 동기화를 사용하여 온-프레미스 서버에서 Azure 파일 공유 캐시를 만들고, 사용 사례에 원하는 프로토콜(SMB, NFS, FTPS 등)을 사용하여 온-프레미스 서버에서 파일 공유의 데이터에 액세스합니다. 이 액세스 패턴은 온-프레미스 성능 및 클라우드 규모의 연결 가능한 서버리스 서비스(예: Azure Backup) 모두를 가장 효율적으로 결합하므로 편리합니다.

이 문서에서는 사용 사례에서 Azure 파일 동기화를 사용하지 않고 Azure 파일 공유에 직접 액세스하도록 호출하는 경우에 네트워킹을 구성하는 방법에 대해 중점적으로 설명합니다. Azure 파일 동기화 배포의 네트워킹 고려 사항에 대한 자세한 내용은 [Azure 파일 동기화 프록시 및 방화벽 설정 구성](storage-sync-files-firewall-and-proxy.md)을 참조하세요.

## <a name="storage-account-settings"></a>스토리지 계정 설정
스토리지 계정은 여러 파일 공유뿐만 아니라 다른 스토리지 리소스(예: Blob 컨테이너 또는 큐)도 배포할 수 있는 공유 스토리지 풀을 나타내는 관리 구조입니다. Azure 스토리지 계정은 네트워크를 보호하기 위한 두 가지 기본 설정, 즉 전송 중 암호화 및 방화벽/VNet(가상 네트워크)을 공개합니다.

### <a name="encryption-in-transit"></a>전송 중 암호화
기본적으로 모든 Azure 스토리지 계정은 전송 중 암호화를 사용하도록 설정되어 있습니다. 즉, SMB를 통해 파일 공유를 탑재하거나 FileREST 프로토콜(예: Azure Portal, PowerShell/CLI 또는 Azure SDK)을 통해 액세스할 때 Azure Files는 암호화 또는 HTTPS를 사용하는 SMB 3.0 이상을 통해 만든 연결만 허용합니다. SMB 3.0을 지원하지 않는 클라이언트 또는 SMB 3.0을 지원하지만 SMB 암호화를 지원하지 않는 클라이언트는 전송 중 암호화를 사용하도록 설정된 경우 Azure 파일 공유를 탑재할 수 없습니다. 암호화를 통해 SMB 3.0을 지원하는 운영 체제에 대한 자세한 내용은 자세한 [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) 및 [Linux](storage-how-to-use-files-linux.md)용 설명서를 참조하세요. 모든 현재 버전의 PowerShell, CLI 및 SDK는 HTTPS를 지원합니다.  

Azure 스토리지 계정에 대해 전송 중 암호화를 사용하지 않도록 설정할 수 있습니다. 또한 암호화를 사용하지 않도록 설정되면 Azure Files에서 SMB 2.1, 암호화되지 않은 SMB 3.0 및 HTTP를 통한 암호화되지 않은 FileREST API 호출을 허용합니다. 전송 중 암호화를 사용하지 않도록 설정하는 주된 이유는 Windows Server 2008 R2 또는 이전 버전의 Linux 배포와 같은 이전 운영 체제에서 실행되어야 하는 레거시 애플리케이션을 지원하기 위해서입니다. Azure Files는 Azure 파일 공유와 동일한 Azure 지역 내에서만 SMB 2.1 연결을 허용합니다. Azure 파일 공유의 Azure 지역 외부(예: 온-프레미스 또는 다른 Azure 지역)에 있는 SMB 2.1 클라이언트는 파일 공유에 액세스할 수 없습니다.

전송 중 암호화에 대한 자세한 내용은 [Azure Storage에서 보안 전송 필요](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

### <a name="firewalls-and-virtual-networks"></a>방화벽 및 가상 네트워크 
방화벽은 요청에서 스토리지 계정의 Azure 파일 공유 및 다른 스토리지 리소스에 액세스할 수 있도록 허용하는 네트워크 정책입니다. 스토리지 계정이 기본 네트워킹 설정을 사용하여 만들어지면 특정 네트워크로 제한되지 않으므로 인터넷에 액세스할 수 있습니다. 이는 인터넷의 모든 사용자가 스토리지 계정에서 호스팅되는 Azure 파일 공유에 저장된 데이터에 액세스할 수 있다는 것이 아니라 스토리지 계정에서 네트워크에서 권한이 부여된 요청을 수락한다는 것을 의미합니다. 요청에는 스토리지 계정 키, SAS(공유 액세스 서명) 토큰(FileREST만 해당) 또는 Active Directory 사용자 계정을 사용하여 권한이 부여될 수 있습니다. 

스토리지 계정에 대한 방화벽 정책은 특정 IP 주소나 범위 또는 가상 네트워크에 대한 액세스를 제한하는 데 사용할 수 있습니다. 일반적으로 스토리지 계정에 대한 대부분의 방화벽 정책은 가상 네트워크에 대한 네트워킹 액세스를 제한합니다. 

[가상 네트워크](../../virtual-network/virtual-networks-overview.md) 또는 VNet은 사용자 고유의 데이터 센터에서 작동하는 기존 네트워크와 비슷합니다. Azure 파일 공유, VM, SQL Database 등과 같은 Azure 리소스에 대한 보안 통신 채널을 만들어 서로 통신할 수 있습니다. Azure 스토리지 계정 또는 Azure VM과 마찬가지로 VNet은 리소스 그룹에 배포된 Azure 리소스입니다. 추가 네트워킹 구성을 사용하면 Azure VNet을 온-프레미스 네트워크에 연결할 수도 있습니다.

Azure VM과 같은 리소스가 가상 네트워크에 추가되면 가상 머신에 연결된 NIC(가상 네트워크 인터페이스)가 구체적으로 해당 VNet으로 제한됩니다. 이는 Azure VM이 가상화된 컴퓨터이고, 여기에는 NIC가 당연히 포함되어 있기 때문에 가능합니다. 가상 머신은 Azure의 IaaS (Infrastructure-as-a-Service) 제품 목록의 일부로 제공됩니다. Azure 파일 공유는 서버리스 파일 공유이므로 VNet에 추가할 NIC가 없습니다. 즉, Azure Files가 Azure의 PaaS(Platform-as-a-Service) 제품 목록의 일부로 제공됩니다. 스토리지 계정이 VNet의 일부가 될 수 있도록 하기 위해 Azure에서 서비스 엔드포인트라고 하는 PaaS 서비스에 대한 개념을 지원합니다. 서비스 엔드포인트를 사용하면 PaaS 서비스가 가상 네트워크의 일부가 될 수 있습니다. 서비스 엔드포인트에 대한 자세한 내용은 [가상 네트워크 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요.

스토리지 계정을 하나 이상의 가상 네트워크에 추가할 수 있습니다. 스토리지 계정을 가상 네트워크에 추가하거나 다른 방화벽 설정을 구성하는 방법에 대한 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.

## <a name="azure-networking"></a>Azure 네트워킹
기본적으로 Azure Files를 포함한 Azure 서비스는 인터넷을 통해 액세스할 수 있습니다. 스토리지 계정으로의 트래픽은 기본적으로 암호화되고 SMB 2.1 탑재는 Azure 지역 외부에서 허용되지 않으므로, 인터넷을 통해 Azure 파일 공유에 액세스하는 데 있어 기본적으로 안전하지 않습니다. 조직의 정책 또는 고유한 규정 요구 사항에 따라 Azure와의 더 제한적인 통신이 필요할 수 있으므로, Azure는 Azure 외부로부터의 트래픽이 Azure Files로 이동하는 방법을 제한하는 여러 가지 방법을 제공합니다. Azure 파일 공유에 액세스할 때 다음 서비스 제품을 사용하여 네트워킹을 더 안전하게 보호할 수 있습니다.

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): VPN Gateway는 인터넷을 통해 Azure 가상 네트워크와 대체 위치(예: 온-프레미스) 간에 암호화된 트래픽을 보내는 데 사용되는 특정 유형의 가상 네트워크 게이트웨이입니다. Azure VPN Gateway는 스토리지 계정 또는 다른 Azure 리소스와 함께 리소스 그룹에 배포할 수 있는 Azure 리소스입니다. VPN 게이트웨이는 다음 두 가지 유형의 연결을 공개합니다.
    - [P2S(지점 및 사이트 간) VPN](../../vpn-gateway/point-to-site-about.md) 게이트웨이 연결 - Azure와 개별 클라이언트 간의 VPN 연결입니다. 이 솔루션은 이동 중에 집, 커피숍 또는 호텔에서 Azure 파일 공유를 탑재할 수 있도록 하려는 재택 근무자와 같이 조직의 온-프레미스 네트워크에 속하지 않은 디바이스에 주로 유용합니다. Azure Files에서 P2S VPN 연결을 사용하려면 연결하려는 각 클라이언트에 대해 P2S VPN 연결을 구성해야 합니다. 
    - [S2S(사이트 간) VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) - Azure와 조직의 네트워크 간의 VPN 연결입니다. S2S VPN 연결을 사용하면 VPN 연결을 Azure 파일 공유에 액세스해야 하는 모든 클라이언트 디바이스에 대해 구성하는 것이 아니라 조직의 네트워크에서 호스팅되는 VPN 서버 또는 디바이스에 대해 한 번만 구성할 수 있습니다.
- [ExpressRoute](../../expressroute/expressroute-introduction.md) - 이를 사용하면 인터넷을 트래버스하지 않는 온-프레미스 네트워크와 Azure 간에 정의된 경로를 만들 수 있습니다. ExpressRoute는 온-프레미스 데이터 센터와 Azure 간의 전용 경로를 제공하므로 네트워크 성능을 고려할 때 ExpressRoute가 유용할 수 있습니다. ExpressRoute는 조직의 정책 또는 규정 요구 사항에서 클라우드의 리소스에 대한 결정적 경로가 필요한 경우에도 유용한 옵션입니다.

## <a name="securing-access-from-on-premises"></a>온-프레미스로부터의 액세스 보호 
범용 파일 공유(Office 문서, PDF, CAD 문서 등)를 Azure Files로 마이그레이션하는 경우 사용자는 일반적으로 워크스테이션, 랩톱 및 태블릿과 같은 온-프레미스 디바이스에서 파일에 계속 액세스해야 합니다. 범용 파일 공유에 대한 주요 고려 사항은 온-프레미스 사용자가 인터넷 또는 WAN을 통해 파일 공유에 안전하게 액세스할 수 있는 방법입니다.

온-프레미스에서 Azure 파일 공유에 액세스하는 가장 쉬운 방법은 온-프레미스 네트워크를 SMB에서 사용하는 445 포트로 열고 Azure Portal에서 제공하는 UNC 경로를 탑재하는 것입니다. 그러면 특별한 네트워킹이 필요하지 않습니다. 많은 고객이 SMB 1.0에 대한 오래된 보안 지침으로 인해 445 포트를 여는 것을 망설이고 있습니다. Microsoft에서는 이 지침을 인터넷에서 안전한 프로토콜로 간주하지 않습니다. Azure Files는 SMB 1.0을 구현하지 않습니다. 

SMB 3.0은 인터넷에서 안전한 파일 공유 프로토콜이어야 한다는 명시적 요구 사항을 고려하여 설계되었습니다. 따라서 SMB 3.0 이상을 사용하는 경우 컴퓨터 네트워킹의 관점에서 445 포트를 여는 것은 HTTPS 연결에 사용되는 443 포트를 여는 것과 다르지 않습니다. 안전하지 않은 SMB 1.0 트래픽을 방지하기 위해 Microsoft는 445 포트를 차단하는 대신 다음 단계를 추천합니다.

> [!Important]  
> 445 포트를 아웃바운드 트래픽에 대해 폐쇄된 상태로 두는 경우에도 다음 단계에 따라 환경에서 SMB 1.0을 제거하는 것이 좋습니다.

1. 조직의 디바이스에서 SMB 1.0을 제거하거나 사용하지 않도록 설정해야 합니다. 현재 지원되는 모든 버전의 Windows 및 Windows Server는 SMB 1.0을 제거하거나 사용하지 않도록 설정하는 것을 지원하며, Windows 10 버전 1709부터는 기본적으로 SMB 1.0이 Windows에 설치되지 않습니다. SMB 1.0을 사용하지 않도록 설정하는 방법에 대한 자세한 내용은 다음 OS 관련 페이지를 참조하세요.
    - [Windows/Windows Server 보안](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Linux 보안](storage-how-to-use-files-linux.md#securing-linux)
1. 조직 내에 SMB 1.0이 필요한 제품이 없는지 확인하고 해당 제품을 제거합니다. Microsoft는 SMB 1.0이 필요한 것으로 Microsoft에 알려진 자사 및 타사 제품이 모두 포함된 [SMB1 클리어링 하우스](https://aka.ms/stillneedssmb1)를 유지 관리합니다. 
1. (선택 사항) 조직의 온-프레미스 네트워크에서 타사 방화벽을 사용하여 SMB 1.0 트래픽을 방지합니다.

조직에서 정책 또는 규정에 따라 445 포트를 차단해야 하는 경우 Azure VPN Gateway 또는 ExpressRoute를 사용하여 443 포트를 통한 트래픽을 터널링할 수 있습니다. 이러한 항목을 배포하는 특정 단계에 대한 자세한 내용은 다음과 같은 특정 웹 페이지를 참조하세요.
- [Azure Files에서 사용할 S2S(사이트 간) VPN 구성](storage-files-configure-s2s-vpn.md)
- [Azure Files에서 사용할 P2S(지점 및 사이트 간) VPN을 Windows에 구성](storage-files-configure-p2s-vpn-windows.md)
- [Azure Files에서 사용할 P2S(지점 및 사이트 간) VPN을 Linux에 구성](storage-files-configure-p2s-vpn-linux.md)

조직에는 온-프레미스 사이트로부터의 아웃바운드 트래픽이 클라우드의 리소스에 대한 결정적 경로를 따라야 한다는 추가 요구 사항이 있을 수 있습니다. 그렇다면 ExpressRoute에서 이 요구 사항을 충족할 수 있습니다.

## <a name="securing-access-from-cloud-resources"></a>클라우드 리소스로부터의 액세스 보호
일반적으로 온-프레미스 애플리케이션이 리프트되어 클라우드로 전환되면 애플리케이션과 애플리케이션의 데이터가 동시에 이동합니다. 즉, 리프트 앤 시프트 마이그레이션에 대한 주요 고려 사항은 작동하기 위해 파일 공유에 대한 액세스가 필요한 특정 가상 머신 또는 Azure 서비스에 대한 Azure 파일 공유 액세스를 잠그는 것입니다. 

VNet을 사용하여 네트워크를 연결하는 데 허용되는(SMB 탑재 또는 Azure 파일 공유에 대한 REST API 호출) VM 또는 다른 Azure 리소스를 제한할 수 있습니다. 스토리지 계정에 대한 암호화되지 않은 트래픽을 허용하는 경우 항상 Azure 파일 공유를 VNet에 배치하는 것이 좋습니다. 그렇지 않은 경우 VNet 사용 여부는 비즈니스 요구 사항 및 조직 정책에 따라 결정되어야 합니다.

Azure 파일 공유에 대한 암호화되지 않은 트래픽을 허용하는 주된 이유는 SMB 2.1(또는 일부 Linux 배포판의 경우 암호화를 사용하지 않는 SMB 3.0)을 사용하여 Azure 파일 공유에 액세스하는 Windows Server 2008 R2, Windows 7 또는 이전의 다른 OS를 지원하기 위한 것입니다. 암호화를 사용하는 SMB 3.0 이상을 지원하는 운영 체제에서는 암호화를 사용하지 않고 SMB 2.1 또는 SMB 3.0을 사용하는 것이 좋습니다.

## <a name="see-also"></a>참고 항목
- [Azure Files 개요](storage-files-introduction.md)
- [Azure 파일 배포에 대한 계획](storage-files-planning.md)
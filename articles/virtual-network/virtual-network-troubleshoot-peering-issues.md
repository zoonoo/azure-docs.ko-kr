---
title: 가상 네트워크 피어링 문제 해결
description: 대부분의 가상 네트워크 피어링 문제를 해결하는 데 도움이 되는 단계입니다.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 662619e101b45d1dd8b34ea97e31f214b254124a
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521875"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>가상 네트워크 피어링 문제 해결

이 문제 해결 가이드는 대부분의 [가상 네트워크 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 문제를 해결하는 데 도움이 되는 단계를 제공합니다.

![가상 네트워크 피어링 다이어그램](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>두 가상 네트워크 간의 가상 네트워크 피어링 구성

가상 네트워크가 동일한 구독또는 다른 구독에 있습니까?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>가상 네트워크가 동일한 구독에 있습니다.

동일한 구독에 있는 가상 네트워크에 대해 가상 네트워크 피어링을 구성하려면 다음 문서의 메서드를 사용합니다.

* 가상 네트워크가 *동일한 지역에*있는 경우 [피어링 만들기를](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)참조하십시오.
* 가상 네트워크가 *다른 지역에*있는 경우 가상 [네트워크 피어링을](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)참조하십시오. 

> [!Note]
> 다음 리소스에 대한 글로벌 가상 네트워크 피어링을 통해 연결이 작동하지 않습니다. 
>
> * 기본 내부 부하 분산(ILB) SKU 뒤에 있는 VM(가상 머신)
> * Redis 캐시(기본 ILB SKU 사용)
> * 응용 프로그램 게이트웨이(기본 ILB SKU 사용)
> * 가상 시스템 스케일 세트(기본 ILB SKU 사용)
> * Azure 서비스 패브릭 클러스터(기본 ILB SKU 사용)
> * SQL 서버 항상 켜기(기본 ILB SKU 사용)
> * PowerApps용 Azure 앱 서비스 환경(기본 ILB SKU 사용)
> * Azure API 관리(기본 ILB SKU 사용)
> * Azure Active Directory 도메인 서비스(Azure AD DS) (기본 ILB SKU 사용)

자세한 내용은 전역 피어링의 [요구 사항 및 제약 조건을](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) 참조하세요.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>가상 네트워크가 다른 구독 또는 Active Directory 테넌에 있습니다.

다른 구독 또는 Active Directory 테넌에서 가상 네트워크에 대한 가상 네트워크 피어링을 구성하려면 [Azure CLI에 대한 다른 구독에서 피어링 만들기를](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)참조하십시오.

> [!Note]
> 네트워크 피어링을 구성하려면 두 구독 모두에 **네트워크 기여자** 권한이 있어야 합니다. 자세한 내용은 [피어링 사용 권한을](virtual-network-manage-peering.md#permissions)참조하십시오.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>온-프레미스 리소스를 사용하는 허브 스포크 토폴로지로 가상 네트워크 피어링 구성

![온-프레미스 스포크가상의 가상 네트워크 피어링 다이어그램](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>사이트 간 연결 또는 익스프레스루트 연결

다음 단계: [가상 네트워크 피어링을 위한 VPN 게이트웨이 전송 구성.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)

### <a name="for-point-to-site-connections"></a>지점 간 연결용

1. 다음 단계: [가상 네트워크 피어링을 위한 VPN 게이트웨이 전송 구성.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)
2. 가상 네트워크 피어링이 설정또는 변경된 후 지점 간 클라이언트가 스포크 가상 네트워크에 대한 업데이트된 경로를 얻을 수 있도록 사이트 간 패키지를 다운로드하고 다시 설치합니다.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>허브 스포크 토폴로지 가상 네트워크로 가상 네트워크 피어링 구성

![가상 네트워크 스포크와 가상 네트워크 피어링의 다이어그램](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>가상 네트워크는 동일한 지역에 있습니다.


1. 허브 가상 네트워크에서 NVA(네트워크 가상 어플라이언스)를 구성합니다.
1. 스포크 가상 네트워크에서 다음 홉 유형 "네트워크 가상 어플라이언스"가 적용된 사용자 정의 경로가 있습니다.

자세한 내용은 [서비스 체인](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)을 참조하십시오.

> [!Note]
> NVA를 설정하는 데 도움이 필요한 경우 [NVA 공급업체에 문의하십시오.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

NVA 장치 설정 및 라우팅 문제 해결에 대한 도움말은 [Azure의 네트워크 가상 어플라이언스 문제를](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)참조하십시오.

### <a name="the-virtual-networks-are-in-different-regions"></a>가상 네트워크는 서로 다른 지역에 있습니다.

이제 글로벌 가상 네트워크 피어링을 통한 전송이 지원됩니다. 다음 리소스에 대한 전역 가상 네트워크 피어링을 통해 연결이 작동하지 않습니다.

* 기본 ILB SKU 뒤에 있는 VM
* Redis 캐시(기본 ILB SKU 사용)
* 응용 프로그램 게이트웨이(기본 ILB SKU 사용)
* 스케일 세트(기본 ILB SKU 사용)
* 서비스 패브릭 클러스터(기본 ILB SKU 사용)
* SQL 서버 항상 켜기(기본 ILB SKU 사용)
* 앱 서비스 환경(기본 ILB SKU 사용)
* API 관리(기본 ILB SKU 사용)
* Azure AD DS(기본 ILB SKU 사용)

전역 피어링 요구 사항 및 제한 사항에 대한 자세한 내용은 [가상 네트워크 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)을 참조하십시오.

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>피어있는 두 가상 네트워크 간의 연결 문제 해결

필요한 역할 및 사용 권한이 있는 계정으로 [Azure 포털에](https://portal.azure.com/) [로그인합니다.](virtual-network-manage-peering.md#permissions) 가상 네트워크를 선택하고 **피어링을**선택한 다음 **상태** 필드를 확인합니다. 상태는 무엇입니까?

### <a name="the-peering-status-is-connected"></a>피어링 상태가 "연결됨"입니다.

이 문제를 해결하려면 다음을 수행합니다.

1. 네트워크 트래픽 흐름 확인:

   [연결 문제 해결](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) 및 IP 흐름 [확인을](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) 사용하여 소스 VM에서 대상 VM으로 이동하여 트래픽 흐름에 간섭을 일으키는 NSG 또는 UDR이 있는지 확인합니다.

   방화벽 또는 NVA를 사용하는 경우: 
   1. 이 단계가 완료된 후 복원할 수 있도록 UDR 매개 변수를 문서화합니다.
   2. NVA를 다음 홉으로 가리키는 소스 VM 서브넷 또는 NIC에서 UDR을 제거합니다. 원본 VM에서 NVA를 우회하는 대상에 직접 연결을 확인합니다. 이 단계가 작동하지 않으면 [NVA 문제 해결사를](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)참조하십시오.

2. 네트워크 추적: 
   1. 대상 VM에서 네트워크 추적을 시작합니다. 윈도우의 경우, 당신은 **넷시를**사용할 수 있습니다 . 리눅스의 경우 **TCP덤프**를 사용합니다.
   2. 원본에서 대상 IP로 **TcpPing** 또는 **PsPing을** 실행합니다.

      다음은 **TcpPing** 명령의 예입니다.`tcping64.exe -t <destination VM address> 3389`

   3. **TcpPing이** 완료되면 대상에서 네트워크 추적을 중지합니다.
   4. 패킷이 원본에서 도착하면 네트워킹 문제가 없습니다. VM 방화벽과 해당 포트에서 수신 대기 하는 응용 프로그램을 모두 검사하여 구성 문제를 찾습니다.

   > [!Note]
   > 전역 가상 네트워크 피어링(다른 지역의 가상 네트워크)을 통해 다음 리소스 유형에 연결할 수 없습니다.
   >
   > * 기본 ILB SKU 뒤에 있는 VM
   > * Redis 캐시(기본 ILB SKU 사용)
   > * 응용 프로그램 게이트웨이(기본 ILB SKU 사용)
   > * 스케일 세트(기본 ILB SKU 사용)
   > * 서비스 패브릭 클러스터(기본 ILB SKU 사용)
   > * SQL 서버 항상 켜기(기본 ILB SKU 사용)
   > * 앱 서비스 환경(기본 ILB SKU 사용)
   > * API 관리(기본 ILB SKU 사용)
   > * Azure AD DS(기본 ILB SKU 사용)

자세한 내용은 전역 피어링의 [요구 사항 및 제약 조건을](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) 참조하세요.

### <a name="the-peering-status-is-disconnected"></a>피어링 상태가 "연결이 끊어졌습니다"

이 문제를 해결하려면 두 가상 네트워크에서 피어링을 삭제한 다음 다시 만듭니다.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>허브 스포크 가상 네트워크와 온-프레미스 리소스 간의 연결 문제 해결

네트워크에서 타사 NVA 또는 VPN 게이트웨이를 사용합니까?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>내 네트워크는 타사 NVA 또는 VPN 게이트웨이를 사용합니다.

타사 NVA 또는 VPN 게이트웨이에 영향을 주는 연결 문제를 해결하려면 다음 문서를 참조하세요.

* [NVA 문제 해결사](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [서비스 체이닝](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>내 네트워크에서 타사 NVA 또는 VPN 게이트웨이를 사용하지 않습니다.

허브 가상 네트워크와 스포크 가상 네트워크에 VPN 게이트웨이가 있습니까?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>허브 가상 네트워크와 스포크 가상 네트워크에는 VPN 게이트웨이가 있습니다.

원격 게이트웨이를 사용하는 것은 지원되지 않습니다.

스포크 가상 네트워크에 이미 VPN 게이트웨이가 있는 경우 **스포크** 가상 네트워크에서 원격 게이트웨이 사용 옵션이 지원되지 않습니다. 이는 가상 네트워크 피어링 제한이 있기 때문입니다.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>허브 가상 네트워크와 스포크 가상 네트워크에 는 VPN 게이트웨이가 없습니다.

사이트 간 또는 Azure ExpressRoute 연결의 경우 온-프레미스에서 원격 가상 네트워크에 대한 연결 문제의 다음과 같은 주요 원인을 확인하십시오.

* 게이트웨이가 있는 가상 네트워크에서 **전달된 트래픽 허용** 확인란이 선택되어 있는지 확인합니다.
* 게이트웨이가 없는 가상 네트워크에서 **원격 게이트웨이 사용** 확인란이 선택되어 있는지 확인합니다.
* 네트워크 관리자가 온-프레미스 장치를 확인하여 모두 원격 가상 네트워크 주소 공간이 추가되었는지 확인합니다.

지점 간 연결의 경우:

* 게이트웨이가 있는 가상 네트워크에서 **전달된 트래픽 허용** 확인란이 선택되어 있는지 확인합니다.
* 게이트웨이가 없는 가상 네트워크에서 **원격 게이트웨이 사용** 확인란이 선택되어 있는지 확인합니다.
* 사이트 간 클라이언트 패키지를 다운로드하여 다시 설치합니다. 새로 피어난 가상 네트워크 경로는 사이트 간 클라이언트에 경로를 자동으로 추가하지 않습니다.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>동일한 리전의 스포크 가상 네트워크 간에 허브 스포크 네트워크 연결 문제 해결

허브 네트워크에는 NVA가 포함되어야 합니다. NVA가 다음 홉으로 설정된 스포크에서 DDR을 구성하고 허브 가상 네트워크에서 **전달된 트래픽 허용을** 사용하도록 설정합니다.

자세한 내용은 [서비스 체인](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)을 참조하고 선택한 [NVA 공급업체와](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) 이러한 요구 사항에 대해 논의하십시오.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>다른 지역의 스포크 가상 네트워크 간에 허브 스포크 네트워크 연결 문제 해결

이제 글로벌 가상 네트워크 피어링을 통한 전송이 지원됩니다. 다음 리소스에 대한 글로벌 가상 네트워크 피어링을 통해 연결이 작동하지 않습니다.

* 기본 ILB SKU 뒤에 있는 VM
* Redis 캐시(기본 ILB SKU 사용)
* 응용 프로그램 게이트웨이(기본 ILB SKU 사용)
* 스케일 세트(기본 ILB SKU 사용)
* 서비스 패브릭 클러스터(기본 ILB SKU 사용)
* SQL 서버 항상 켜기(기본 ILB SKU 사용)
* 앱 서비스 환경(기본 ILB SKU 사용)
* API 관리(기본 ILB SKU 사용)
* Azure AD DS(기본 ILB SKU 사용)

자세한 내용은 글로벌 피어링 및 다른 VPN [Topologies의](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/) [요구 사항 및 제약 조건을](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) 참조하십시오.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>웹 앱과 스포크 가상 네트워크 간의 허브 스포크 네트워크 연결 문제 해결

이 문제를 해결하려면 다음을 수행합니다.

1. Azure Portal에 로그인합니다. 
1. 웹 앱에서 **네트워킹을**선택한 다음 **VNet 통합을**선택합니다.
1. 원격 가상 네트워크를 볼 수 있는지 확인합니다. 원격 가상 네트워크 주소**공간(네트워크 동기화** 및 **경로 추가)을**수동으로 입력합니다.

자세한 내용은 다음 문서를 참조하세요.

* [앱을 Azure 가상 네트워크와 통합](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [지점 및 사이트 간 VPN 라우팅 정보](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>가상 네트워크 피어링 구성 오류 메시지 문제 해결 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>현재 `<TENANT ID>` 테넌트가 연결된 구독에 액세스할 수 있는 권한이 없습니다.

이 문제를 해결하려면 [피어링 만들기 - Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)를 참조하십시오.

### <a name="not-connected"></a>연결되지 않음

이 문제를 해결하려면 두 가상 네트워크에서 피어링을 삭제한 다음 다시 만듭니다.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Databricks 가상 네트워크를 피어에 연결하지 못했습니다.

이 문제를 해결하려면 **Azure Databricks에서**가상 네트워크 피어링을 구성한 다음 **Resource ID를**사용하여 대상 가상 네트워크를 지정합니다. 자세한 내용은 [원격 가상 네트워크에 대한 데이터브릭 스 가상 네트워크 피어를](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)참조하십시오.

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>원격 가상 네트워크에 게이트웨이가 없습니다.

이 문제는 다른 테넌트와 가상 네트워크를 피어로 `Use Remote Gateways`피어내고 나중에 구성하려고 할 때 발생합니다. Azure 포털의 제한사항은 다른 테넌트의 가상 네트워크에서 가상 네트워크 게이트웨이의 존재를 확인할 수 없다는 것입니다.

이 문제를 해결하는 방법에는 두 가지가 있습니다.

 * 피어링을 삭제하고 새 `Use Remote Gateways` 피어링을 만들 때 옵션을 활성화합니다.
 * Azure 포털 대신 PowerShell 또는 CLI를 `Use Remote Gateways`사용하여 을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [Azure VM 간의 연결 문제 해결](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

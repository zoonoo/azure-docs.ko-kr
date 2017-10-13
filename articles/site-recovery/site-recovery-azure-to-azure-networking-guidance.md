---
title: "Azure 간 가상 컴퓨터 복제에 대한 Azure Site Recovery 네트워킹 지침 | Microsoft Docs"
description: "Azure Virtual Machines 복제에 대한 네트워킹 지침"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 99c08a1efbc610959fb4ba824dcb0601efac5877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="networking-guidance-for-replicating-azure-virtual-machines"></a>Azure Virtual Machines 복제에 대한 네트워킹 지침

>[!NOTE]
> Azure Virtual Machines에 대한 Site Recovery 복제는 현재 미리 보기로 제공됩니다.

이 문서에서는 지역 간에 Azure Virtual Machines를 복제 및 복구할 때의 Azure Site Recovery에 대한 네트워킹 지침을 자세히 설명합니다. Azure Site Recovery 요구 사항에 대한 자세한 내용은 [필수 구성 요소](site-recovery-prereq.md) 문서를 참조하세요.

## <a name="site-recovery-architecture"></a>Site Recovery 아키텍처

Site Recovery는 주 지역에서 가동이 중단된 경우 복구할 수 있도록 Azure Virtual Machines에서 실행 중인 응용 프로그램을 다른 Azure 지역에 복제할 수 있는 쉽고 간단한 방법을 제공합니다. [이 시나리오와 Site Recovery 아키텍처](site-recovery-azure-to-azure-architecture.md)에 대해 자세히 알아보세요.

## <a name="your-network-infrastructure"></a>네트워크 인프라

다음 다이어그램에서는 Azure Virtual Machines에서 실행 중인 응용 프로그램에 대한 일반적인 Azure 환경을 보여 줍니다.

![고객 환경](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

온-프레미스 네트워크와 Azure 간의 연결에 Azure ExpressRoute 또는 VPN 연결을 사용하는 경우 환경은 다음과 유사합니다.

![고객 환경](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

일반적으로 고객은 방화벽 및/또는 NSG(네트워크 보안 그룹)를 사용하여 네트워크를 보호합니다. 방화벽은 URL 기반 또는 IP 기반 허용 목록을 사용하여 네트워크 연결을 제어할 수 있습니다. NSG는 IP 범위를 사용하여 네트워크 연결을 제어할 수 있는 규칙을 허용합니다.

>[!IMPORTANT]
> 인증된 프록시를 사용하여 네트워크 연결을 제어하는 경우 이는 지원되지 않으며 Site Recovery 복제를 사용할 수 없습니다.

다음 섹션에서는 Site Recovery 복제가 작동하기 위해 Azure Virtual Machines에 필요한 네트워크 아웃바운드 연결 변경 사항을 설명합니다.

## <a name="outbound-connectivity-for-azure-site-recovery-urls"></a>Azure Site Recovery URL에 대한 아웃바운드 연결

URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 다음과 같은 필수 Azure Site Recovery 서비스 URL을 허용 목록에 포함해야 합니다.


**URL** | **목적**  
--- | ---
*.blob.core.windows.net | VM에서 원본 지역의 캐시 저장소 계정에 데이터를 쓸 수 있도록 하는 데 필요합니다.
login.microsoftonline.com | Site Recovery 서비스 URL에 대한 권한 부여 및 인증에 필요합니다.
*.hypervrecoverymanager.windowsazure.com | VM에서 Site Recovery 서비스 통신이 발생할 수 있도록 하는 데 필요합니다.
*.servicebus.windows.net | VM에서 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 하는 데 필요합니다.

## <a name="outbound-connectivity-for-azure-site-recovery-ip-ranges"></a>Azure Site Recovery IP 범위에 대한 아웃바운드 연결

>[!NOTE]
> 네트워크 보안 그룹에서 필요한 NSG 규칙을 자동으로 만들려면 [이 스크립트를 다운로드하여 사용](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)하면 됩니다.

>[!IMPORTANT]
> * 프로덕션 네트워크 보안 그룹에서 규칙을 만들기 전에 테스트 네트워크 보안 그룹에서 필요한 NSG 규칙을 만들고 문제가 없는지 확인하는 것이 좋습니다.
> * 필요한 개수의 NSG 규칙을 만들려면 구독이 허용 목록에 포함되어 있는지 확인합니다. 구독에서 NSG 규칙 제한을 늘리려면 지원 팀에 문의하세요.

IP 기반 방화벽 프록시 또는 NSG 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 가상 컴퓨터의 원본 및 대상 위치에 따라 다음 IP 범위를 허용 목록에 포함해야 합니다.

- 원본 위치에 해당하는 모든 IP 범위 ([IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)를 다운로드할 수 있음). 허용 목록은 VM에서 캐시 저장소 계정에 데이터를 쓸 수 있도록 하는 데 필요합니다.

- Office 365 [인증 및 ID IP V4 끝점](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)에 해당하는 모든 IP 범위

    >[!NOTE]
    > 나중에 Office 365 IP 범위에 새 IP가 추가된 경우 새 NSG 규칙을 만들어야 합니다.

- 대상 위치에 따라 다른 Site Recovery 서비스 끝점 IP([XML 파일로 제공](https://aka.ms/site-recovery-public-ips))

   **대상 위치** | **Site Recovery 서비스 IP** |  **Site Recovery 모니터링 IP**
   --- | --- | ---
   동아시아 | 52.175.17.132 | 13.94.47.61
   동남아시아 | 52.187.58.193 | 13.76.179.223
   인도 중부 | 52.172.187.37 | 104.211.98.185
   인도 남부 | 52.172.46.220 | 104.211.224.190
   미국 중북부 | 23.96.195.247 | 168.62.249.226
   북유럽 | 40.69.212.238 | 52.169.18.8
   서유럽 | 52.166.13.64 | 40.68.93.145
   미국 동부 | 13.82.88.226 | 104.45.147.24
   미국 서부 | 40.83.179.48 | 104.40.26.199
   미국 중남부 | 13.84.148.14 | 104.210.146.250
   미국 중부 | 40.69.144.231 | 52.165.34.144
   미국 동부 2 | 52.184.158.163 | 40.79.44.59
   일본 동부 | 52.185.150.140 | 138.91.1.105
   일본 서부 | 52.175.146.69 | 138.91.17.38
   브라질 남부 | 191.234.185.172 | 23.97.97.36
   오스트레일리아 동부 | 104.210.113.114 | 191.239.64.144
   오스트레일리아 남동부 | 13.70.159.158 | 191.239.160.45
   캐나다 중부 | 52.228.36.192 | 40.85.226.62
   캐나다 동부 | 52.229.125.98 | 40.86.225.142
   미국 중서부 | 52.161.20.168 | 13.78.149.209
   미국 서부 2 | 52.183.45.166 | 13.66.228.204
   영국 서부 | 51.141.3.203 | 51.141.14.113
   영국 남부 | 51.140.43.158 | 51.140.189.52
   영국 남부 2 | 13.87.37.4| 13.87.34.139
   영국 북부 | 51.142.209.167 | 13.87.102.68
   한국 중부 | 52.231.28.253 | 52.231.32.85
   한국 남부 | 52.231.298.185 | 52.231.200.144

## <a name="sample-nsg-configuration"></a>샘플 NSG 구성
이 섹션에서는 가상 컴퓨터에서 Site Recovery 복제가 작동할 수 있도록 NSG 규칙을 구성하는 단계에 대해 설명합니다. NSG 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 필요한 모든 IP 범위에 대해 "HTTPS 아웃바운드 허용" 규칙을 사용합니다.

>[!Note]
> 네트워크 보안 그룹에서 필요한 NSG 규칙을 자동으로 만들려면 [이 스크립트를 다운로드하여 사용](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)하면 됩니다.

예를 들어 VM의 원본 위치가 "미국 동부"이고 복제 대상 위치가 "미국 중부"인 경우 다음 두 섹션의 단계를 따릅니다.

>[!IMPORTANT]
> * 프로덕션 네트워크 보안 그룹에서 규칙을 만들기 전에 테스트 네트워크 보안 그룹에서 필요한 NSG 규칙을 만들고 문제가 없는지 확인하는 것이 좋습니다.
> * 필요한 개수의 NSG 규칙을 만들려면 구독이 허용 목록에 포함되어 있는지 확인합니다. 구독에서 NSG 규칙 제한을 늘리려면 지원 팀에 문의하세요.

### <a name="nsg-rules-on-the-east-us-network-security-group"></a>미국 동부 네트워크 보안 그룹에 대한 NSG 규칙

* [미국 동부 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)에 해당하는 규칙을 만듭니다. 이는 VM에서 캐시 저장소 계정에 데이터를 쓸 수 있도록 하는 데 필요합니다.

* Office 365 [인증 및 ID IP V4 끝점](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)에 해당하는 모든 IP 범위에 대한 규칙을 만듭니다.

* 대상 위치에 해당하는 규칙을 만듭니다.

   **위치** | **Site Recovery 서비스 IP** |  **Site Recovery 모니터링 IP**
    --- | --- | ---
   미국 중부 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules-on-the-central-us-network-security-group"></a>미국 중부 네트워크 보안 그룹에 대한 NSG 규칙

이러한 규칙은 장애 조치 후 대상 지역에서 원본 지역으로의 복제를 활성화하는 데 필요합니다.

* [미국 중부 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)에 해당하는 규칙. 이는 VM에서 캐시 저장소 계정에 데이터를 쓸 수 있도록 하는 데 필요합니다.

* Office 365 [인증 및 ID IP V4 끝점](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)에 해당하는 모든 IP 범위에 대한 규칙

* 원본 위치에 해당하는 규칙

   **위치** | **Site Recovery 서비스 IP** |  **Site Recovery 모니터링 IP**
    --- | --- | ---
   미국 동부 | 13.82.88.226 | 104.45.147.24


## <a name="guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration"></a>기존 Azure-온-프레미스 ExpressRoute/VPN 구성에 대한 지침

온-프레미스와 Azure의 원본 위치 간에 ExpressRoute 또는 VPN 연결을 사용하는 경우 이 섹션의 지침을 따릅니다.

### <a name="forced-tunneling-configuration"></a>강제 터널링 구성

고객의 일반적인 구성은 아웃바운드 인터넷 트래픽이 온-프레미스을 통과하도록 강제하는 기본 경로(0.0.0.0/0)로 정의되어 있습니다. 이는 권장되지 않습니다. 복제 트래픽 및 Site Recovery 서비스 통신은 Azure 경계를 벗어날 수 없습니다. 따라서 복제 트래픽이 온-프레미스를 이동하지 않도록 [이러한 IP 범위](#outbound-connectivity-for-azure-site-recovery-ip-ranges)에 대해 사용자 정의 경로(UDR)를 추가해야 합니다.

### <a name="connectivity-between-the-target-and-on-premises-location"></a>대상 및 온-프레미스 위치 간 연결

대상 위치와 온-프레미스 위치 간 연결의 경우 다음 지침을 따릅니다.
- 응용 프로그램을 온-프레미스 컴퓨터에 연결해야 하는 경우 또는 VPN/ExpressRoute를 통해 온-프레미스에서 응용 프로그램에 연결하는 클라이언트가 있는 경우 대상 Azure 지역과 온-프레미스 데이터 센터 간에 하나 이상의 [사이트 간 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)이 있어야 합니다.

- 대상 Azure 지역과 온-프레미스 데이터 센터 간에 많은 트래픽이 이동할 것으로 예상되는 경우에는 대상 Azure 지역과 온-프레미스 데이터 센터 간에 다른 [ExpressRoute 연결](../expressroute/expressroute-introduction.md)을 만들어야 합니다.

- 장애 조치 후 가상 컴퓨터의 IP를 유지하려면 연결이 끊어진 상태에서 대상 지역의 사이트 간/ExpressRoute 연결을 유지합니다. 이렇게 하면 원본 지역의 IP 범위와 대상 지역의 IP 범위 간에 범위 충돌이 발생하지 않습니다.

### <a name="best-practices-for-expressroute-configuration"></a>ExpressRoute 구성에 대한 모범 사례
ExpressRoute 구성의 경우 다음 모범 사례를 따릅니다.

- 원본 지역과 대상 지역 모두에 ExpressRoute 회로를 만들어야 합니다. 그런 다음 아래 간의 연결을 만들어야 합니다.
  - 원본 가상 네트워크와 ExpressRoute 회로
  - 대상 가상 네트워크와 ExpressRoute 회로

- ExpressRoute 표준의 일부로 동일한 지역에 회로를 만들 수 있습니다. 서로 다른 지역에 ExpressRoute 회로를 만들려면 증분 비용이 포함된 Azure ExpressRoute Premium이 필요합니다. ExpressRoute Premium을 이미 사용 중인 경우에는 추가 비용이 발생하지 않습니다. 자세한 내용은 [ExpressRoute 위치 문서](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) 및 [ExpressRoute 가격 책정](https://azure.microsoft.com/pricing/details/expressroute/)을 참조하세요.

- 원본 지역과 대상 지역에서 서로 다른 IP 범위를 사용하는 것이 좋습니다. ExpressRoute 회로는 IP 범위가 동일한 두 Azure 가상 네트워크와 동시에 연결할 수 없습니다.

- 두 지역 모두에서 IP 범위가 동일한 가상 네트워크를 만든 다음 두 지역 모두에서 ExpressRoute 회로를 만들 수 있습니다. 장애 조치 발생 시 원본 가상 네트워크에서 회로의 연결을 끊고 대상 가상 네트워크에서 회로를 연결합니다.

 >[!IMPORTANT]
 > 주 지역이 완전히 중단된 경우에는 연결 끊기 작업이 실패할 수 있습니다. 이는 대상 가상 네트워크에서 ExpressRoute에 연결되는 것을 방지합니다.

## <a name="next-steps"></a>다음 단계
[Azure Virtual Machines를 복제](site-recovery-azure-to-azure.md)하여 워크로드 보호를 시작합니다.

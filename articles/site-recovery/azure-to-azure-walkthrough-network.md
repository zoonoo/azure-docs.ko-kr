---
title: "Site Recovery를 사용하여 VMware에서 Azure로의 복제를 위한 네트워킹 계획 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery를 사용하여 Azure VM을 복제할 때 필요한 네트워크 계획에 대해 설명합니다."
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
ms.date: 08/01/2017
ms.author: sujayt
ms.openlocfilehash: 7b37e853f6b97ba313111f9201f877846a28fae9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-3-plan-networking-for-azure-vm-replication"></a>3단계: Azure VM 복제를 위한 네트워킹 계획

[배포 필수 구성 요소](azure-to-azure-walkthrough-prerequisites.md)를 확인한 후에 이 문서를 참조하여 Azure Site Recovery 서비스를 통해 한 Azure 지역에서 다른 Azure 지역으로 Azure VM(가상 컴퓨터)을 복제 및 복구할 때의 네트워킹 고려 사항을 이해합니다. 

- 이 문서를 완료하면 복제하려는 Azure VM에 대한 아웃바운드 액세스를 설정하는 방법과 온-프레미스 사이트에서 해당 VM으로 연결하는 방법을 명확히 이해해야 합니다.
- 이 문서의 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 질문하세요.

>[!NOTE]
> Site Recovery를 사용하는 Azure VM 복제는 현재 미리 보기로 제공됩니다.



## <a name="network-overview"></a>네트워크 개요

일반적으로 Azure VM은 Azure 가상 네트워크/서브넷에 있으며, Azure ExpressRoute 또는 VPN 연결을 사용하는 온-프레미스 사이트에서 Azure로의 연결이 있습니다.

네트워크는 일반적으로 방화벽 및/또는 NSG(네트워크 보안 그룹)를 사용하여 보호됩니다. 방화벽은 URL 기반/IP 기반 목록을 사용하여 네트워크 연결을 제어할 수 있습니다. NSG는 IP 범위 기반 규칙을 사용합니다. 


Site Recovery가 예상대로 작동하려면 복제하려는 VM에서 아웃바운드 네트워크 연결의 일부를 변경해야 합니다. Site Recovery는 인증 프록시를 사용하여 네트워크 연결을 제어하도록 지원하지 않습니다. 인증 프록시가 있는 경우 복제는 사용할 수 없습니다. 


다음 다이어그램에서는 Azure VM에서 실행되는 응용 프로그램에 대한 일반적인 Azure 환경을 보여 줍니다.

![고객 환경](./media/azure-to-azure-walkthrough-network/source-environment.png)

**Azure VM 환경**

Azure ExpressRoute 또는 VPN 연결을 사용하여 온-프레미스 사이트에서 설정된 Azure에 연결할 수도 있습니다. 

![고객 환경](./media/azure-to-azure-walkthrough-network/source-environment-expressroute.png)

**Azure에 온-프레미스 연결** 



## <a name="outbound-connectivity-for-urls"></a>URL에 대한 아웃바운드 연결

URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 Site Recovery에서 사용되는 다음 URL을 허용하는지 확인합니다

**URL** | **세부 정보**  
--- | ---
*.blob.core.windows.net | VM에서 원본 지역의 캐시 저장소 계정에 데이터를 쓸 수 있도록 합니다.
login.microsoftonline.com | Site Recovery 서비스 URL에 대한 권한 부여 및 인증을 제공합니다.
*.hypervrecoverymanager.windowsazure.com | VM의 Site Recovery 서비스와 통신할 수 있도록 합니다.
*.servicebus.windows.net | VM에서 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 하는 데 필요합니다.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 주소 범위에 대한 아웃바운드 연결

- [이 스크립트](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)를 다운로드하고 실행하여 NSG에 필요한 규칙을 자동으로 만들 수 있습니다.
- 프로덕션 NSG에서 규칙을 만들기 전에 테스트 NSG에서 필요한 NSG 규칙을 만들고 문제가 없는지 확인하는 것이 좋습니다.
- 필요한 개수의 NSG 규칙을 만들려면 구독이 허용 목록에 포함되어 있는지 확인합니다. 구독에서 NSG 규칙 제한을 늘리려면 지원 팀에 문의하세요.
IP 기반 방화벽 프록시 또는 NSG 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 가상 컴퓨터의 원본 및 대상 위치에 따라 다음 IP 범위를 허용 목록에 포함해야 합니다.

    - 원본 위치에 해당하는 모든 IP 주소 범위 - [범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)를 다운로드합니다. 허용 목록이 VM에서 캐시 저장소 계정에 데이터를 쓸 수 있도록 하는 데 필요합니다.
    - Office 365 [인증 및 ID IP V4 끝점](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)에 해당하는 모든 IP 범위 Office 365 IP 범위에 새 IP가 추가되면 새 NSG 규칙을 만들어야 합니다.
-     대상 위치에 따라 달라지는 Site Recovery 서비스 끝점 IP 주소([XML 파일로 제공](https://aka.ms/site-recovery-public-ips)) 

   **대상 위치** | **Site Recovery 서비스 IP** |  **Site Recovery 모니터링 IP**
   --- | --- | ---
   동아시아 | 52.175.17.132</br>40.83.121.61 | 13.94.47.61
   동남아시아 | 52.187.58.193</br>52.187.169.104 | 13.76.179.223
   인도 중부 | 52.172.187.37</br>52.172.157.193 | 104.211.98.185
   인도 남부 | 52.172.46.220</br>52.172.13.124 | 104.211.224.190
   미국 중북부 | 23.96.195.247</br>23.96.217.22 | 168.62.249.226
   북유럽 | 40.69.212.238</br>13.74.36.46 | 52.169.18.8
   서유럽 | 52.166.13.64</br>52.166.6.245 | 40.68.93.145
   미국 동부 | 13.82.88.226</br>40.71.38.173 | 104.45.147.24
   미국 서부 | 40.83.179.48</br>13.91.45.163 | 104.40.26.199
   미국 중남부 | 13.84.148.14</br>13.84.172.239 | 104.210.146.250
   미국 중부 | 40.69.144.231</br>40.69.167.116 | 52.165.34.144
   미국 동부 2 | 52.184.158.163</br>52.225.216.31 | 40.79.44.59
   일본 동부 | 52.185.150.140</br>13.78.87.185 | 138.91.1.105
   일본 서부 | 52.175.146.69</br>52.175.145.200 | 138.91.17.38
   브라질 남부 | 191.234.185.172</br>104.41.62.15 | 23.97.97.36
   오스트레일리아 동부 | 104.210.113.114</br>40.126.226.199 | 191.239.64.144
   오스트레일리아 남동부 | 13.70.159.158</br>13.73.114.68 | 191.239.160.45
   캐나다 중부 | 52.228.36.192</br>52.228.39.52 | 40.85.226.62
   캐나다 동부 | 52.229.125.98</br>52.229.126.170 | 40.86.225.142
   미국 중서부 | 52.161.20.168</br>13.78.230.131 | 13.78.149.209
   미국 서부 2 | 52.183.45.166</br>52.175.207.234 | 13.66.228.204
   영국 서부 | 51.141.3.203</br>51.140.226.176 | 51.141.14.113
   영국 남부 | 51.140.43.158</br>51.140.29.146 | 51.140.189.52

## <a name="example-nsg-configuration"></a>NSG 구성 예제

이 섹션에서는 VM에서 복제가 작동할 수 있도록 NSG 규칙을 구성하는 방법을 보여 줍니다. NSG 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 필요한 모든 IP 범위에 대해 "HTTPS 아웃바운드 허용" 규칙을 사용합니다.

이 예제에서 VM 원본 위치는 "미국 동부"입니다. 복제 대상 위치는 미국 중부입니다.


### <a name="example"></a>예제

#### <a name="east-us"></a>미국 동부

1. [미국 동부 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)에 해당하는 규칙을 만듭니다. 이는 VM에서 캐시 저장소 계정에 데이터를 쓸 수 있도록 하는 데 필요합니다.
2. Office 365 [인증 및 ID IP V4 끝점](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)에 해당하는 모든 IP 범위에 대한 규칙을 만듭니다.
3. 대상 위치에 해당하는 규칙을 만듭니다.

   **위치** | **Site Recovery 서비스 IP** |  **Site Recovery 모니터링 IP**
    --- | --- | ---
   미국 중부 | 40.69.144.231</br>40.69.167.116 | 52.165.34.144

#### <a name="central-us"></a>미국 중부

이러한 규칙은 장애 조치 후 대상 지역에서 원본 지역으로의 복제를 활성화하는 데 필요합니다.

1. [미국 중부 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)에 해당하는 규칙을 만듭니다.
2. Office 365 [인증 및 ID IP V4 끝점](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)에 해당하는 모든 IP 범위에 대한 규칙을 만듭니다.
3. 원본 위치에 해당하는 규칙을 만듭니다.

   **위치**: | **Site Recovery 서비스 IP** |  **Site Recovery 모니터링 IP**
    --- | --- | ---
   미국 동부 | 13.82.88.226</br>40.71.38.173 | 104.45.147.24


## <a name="existing-on-premises-connection"></a>기존 온-프레미스 연결

온-프레미스 사이트와 Azure의 원본 위치 간에 ExpressRoute 또는 VPN 연결이 있는 경우 다음 지침을 따릅니다.

**구성** | **세부 정보**
--- | ---
**강제 터널링** | 일반적으로 기본 경로(0.0.0.0/0)는 아웃바운드 인터넷 트래픽이 온-프레미스 위치를 통과하도록 합니다. 이 구성은 권장되지 않습니다. 복제 트래픽 및 Site Recovery 통신은 Azure 내에서 유지해야 합니다.<br/><br/> 해결 방법으로, [이러한 IP 범위](#outbound-connectivity-for-azure-site-recovery-ip-ranges)에 대한 UDR(사용자 정의 경로)를 추가하여 복제 트래픽이 온-프레미스로 이동하지 않도록 합니다.
**연결** | 앱에서 온-프레미스 컴퓨터에 연결해야 하거나 온-프레미스 클라이언트에서 VPN/ExpressRoute를 통해 앱에 연결하는 경우 대상 Azure 지역과 온-프레미스 사이트 간에 하나 이상의 [사이트 간 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)이 있어야 합니다.<br/><br/> 대상 Azure 지역과 온-프레미스 사이트 사이의 트래픽 양이 많으면 대상 지역과 온-프레미스 사이에 또 하나의 [ExpressRoute 연결](../expressroute/expressroute-introduction.md)을 만듭니다.<br/><br/> 장애 조치 후 VM에 대한 IP를 유지하려면 대상 지역의 사이트 간/ExpressRoute 연결을 연결이 끊어진 상태로 유지합니다. 이렇게 하면 원본 IP 주소와 대상 IP 주소 범위 사이에 범위 충돌이 발생하지 않습니다.
**Express 경로** | 원본 지역과 대상 지역에 ExpressRoute 회로를 만듭니다.<br/><br/> 원본 네트워크와 ExpressRoute 회로 간 및 대상 네트워크와 회로 간 연결을 만듭니다.<br/><br/> 원본 지역과 대상 지역에서 서로 다른 IP 범위를 사용하는 것이 좋습니다. 회로는 동일한 IP 범위로 둘 이상의 Azure 네트워크에 동시에 연결할 수 없습니다.<br/><br/> 두 지역 모두에서 IP 범위가 동일한 가상 네트워크를 만든 다음 두 지역 모두에서 ExpressRoute 회로를 만들 수 있습니다. 장애 조치의 경우 원본 가상 네트워크에서 회로의 연결을 끊고 대상 가상 네트워크에 회로를 연결합니다.<br/><br/> 주 지역이 완전히 중단된 경우에는 연결 끊기 작업이 실패할 수 있습니다. 이 경우 대상 가상 네트워크는 ExpressRoute 연결을 가져올 수 없습니다.
**ExpressRoute Premium** | 동일한 지정학적 지역에 회로를 만들 수 있습니다.<br/><br/> 다른 지정학적 지역에 회로를 만들려면 Azure ExpressRoute Premium이 필요합니다.<br/><br/> Premium을 사용하면 비용이 증분됩니다. 이미 사용 중인 경우 추가 비용은 없습니다.<br/><br/> [위치](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) 및 [가격](https://azure.microsoft.com/pricing/details/expressroute/)에 대해 자세히 알아보세요.



## <a name="next-steps"></a>다음 단계

[4단계: 자격 증명 모음 만들기](azure-to-azure-walkthrough-vault.md)로 이동합니다.


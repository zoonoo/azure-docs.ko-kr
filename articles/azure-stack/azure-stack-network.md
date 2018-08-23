---
title: 네트워크 Azure Stack 통합 시스템에 대 한 통합 고려 사항 | Microsoft Docs
description: 다중 노드 Azure Stack을 사용 하 여 데이터 센터 네트워크 통합에 대 한 계획을 수행할 수 있는 작업에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 48e2f85488adfb776da5f52c154028f8aafb167a
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617024"
---
# <a name="network-connectivity"></a>네트워크 연결
이 문서에서는 Azure Stack 기존 네트워킹 환경에 통합 하는 방법을 결정 하는 데 Azure Stack 네트워크 인프라 정보를 제공 합니다. 

> [!NOTE]
> 외부 DNS 이름 (예를 들어 www.bing.com) Azure Stack에서를 해결 하려면 DNS 요청을 전달 하도록 DNS 서버를 제공 해야 합니다. Azure Stack의 DNS 요구 사항에 대 한 자세한 내용은 참조 하세요. [Azure Stack 데이터 센터 통합-DNS](azure-stack-integrate-dns.md)합니다.

## <a name="physical-network-design"></a>실제 네트워크 디자인
Azure Stack 솔루션에는 작업 및 서비스를 지원 하기 위해 복원 력이 있고 항상 사용 가능한 물리적 인프라에 필요 합니다. 테두리 스위치에 ToR에서 업링크 SFP + SFP28 미디어 및 1GB에서 10GB 또는 25GB 속도 제한 됩니다. 가용성에 대 한 원래 장비 제조업체 (OEM) 하드웨어 공급 업체를 사용 하 여 확인 합니다. 다음 다이어그램에서는 권장 되는 디자인을 제공합니다.

![권장 되는 Azure Stack 네트워크 디자인](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>논리 네트워크
논리 네트워크는 기본 실제 네트워크 인프라의 추상화를 나타냅니다. 구성 하 고 호스트, virtual machines 및 서비스에 대 한 네트워크 할당을 간소화 하려면 사용 됩니다. 논리 네트워크 만들기의 일환으로, 네트워크 사이트 가상 로컬 영역 네트워크 (Vlan), IP 서브넷 및 각 실제 위치의 논리 네트워크와 연결 된 IP 서브넷/VLAN 쌍을 정의 하기 위해 만들어집니다.

다음 표에서 논리 네트워크와 연결 된 IPv4 서브넷 범위에 대 한 계획 해야 합니다를 보여 줍니다.

| 논리적 네트워크 | 설명 | 크기 | 
| -------- | ------------- | ------------ | 
| 공용 VIP | Azure Stack이이 네트워크에서 31 주소의 합계를 사용합니다. 소수의 Azure Stack 서비스에 대 한 8 공용 IP 주소는 사용 하 고 나머지 테 넌 트 가상 컴퓨터에서 사용 됩니다. App Service 및 SQL 리소스 공급자를 사용 하려는 경우에 7 더 많은 주소가 사용 됩니다. 나머지 15 Ip는 향후 Azure 서비스에 대 한 예약 되어 있습니다. | / 26 (62 호스트)-롤아웃의 경우/22 (1022 호스트)<br><br>권장 되 는/24 (254 명의 호스트) = | 
| 스위치 인프라 | 지점 간 라우팅 목적으로 전용된 IP 주소 관리 인터페이스 및 스위치에 할당 된 루프백 주소를 전환 합니다. | /26 | 
| 인프라 | Azure Stack 내부 구성 요소에 대 한 통신 하는 데 사용 합니다. | /24 |
| 개인 | 저장소 네트워크 및 개인 Vip에 사용 합니다. | /24 | 
| BMC | 실제 호스트의 Bmc와 통신 하는 데 사용 합니다. | /27 | 
| | | |

## <a name="network-infrastructure"></a>네트워크 인프라
Azure Stack에 대 한 네트워크 인프라 스위치에 구성 된 여러 논리 네트워크 구성 됩니다. 다음 다이어그램에서는 이러한 논리 네트워크 및 베이스 보드 관리 컨트롤러 (BMC)에-top-of-rack TOR ()를 통합 하는 방법 (고객 네트워크) 스위치 테두리를 보여 줍니다.

![논리 네트워크 연결 다이어그램 작성 및 스위치](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC 네트워크
이 네트워크는 관리 네트워크에 전용으로 모든 베이스 보드 관리 컨트롤러 (라고도 서비스 프로세서, 예를 들어 iDRAC, iLO, iBMC 등) 연결 됩니다. 있는 경우에 하드웨어 수명 주기 호스트 (HLH)이이 네트워크에 있는 및 하드웨어 유지 관리 또는 모니터링에 대 한 OEM-특정 소프트웨어를 제공할 수 있습니다. 

또한는 HLH 배포 VM (dvm이)를 호스트합니다. Dvm이 Azure Stack 배포 하는 동안 사용 되 고 배포가 완료 되 면 제거 됩니다. dvm이 테스트에 유효성을 검사 하 고 여러 구성 요소에 액세스 하는 연결 된 배포 시나리오에서 인터넷 액세스를 해야 합니다. 내부 및 외부 회사 네트워크;에서 이러한 구성 요소 수 있습니다. 예를 들어 NTP, DNS 및 Azure입니다. 연결 요구 사항에 대 한 자세한 내용은 참조는 [NAT 섹션에서는 Azure Stack 방화벽 통합에서](azure-stack-firewall.md#network-address-translation)합니다. 

### <a name="private-network"></a>개인 네트워크
이/24 (254 호스트 Ip) 네트워크 (Azure Stack 영역의 테두리 스위치 장치 보다 크게 확장 되지 않습니다)는 Azure Stack 지역 전용 이며 두 개의 서브넷으로 구분 됩니다.

- **저장소 네트워크**합니다. 사용량 (126 호스트 Ip) 네트워크 공간 다이렉트 및 서버 메시지 블록 (SMB) 저장소 트래픽 및 가상 컴퓨터 실시간 마이그레이션 사용을 지원 하는 데 사용 합니다. 
- **내부 가상 IP 네트워크**합니다. A/25 소프트웨어 부하 분산 장치에 대 한 내부 전용으로 전용된 Vip 네트워크.

### <a name="azure-stack-infrastructure-network"></a>Azure Stack 인프라 네트워크
이/24 네트워크 전용인 내부 Azure Stack 구성 요소 통신 하 고 데이터를 서로 교환할 수 있도록 합니다. 이 서브넷에 라우팅 가능 IP 주소를 하는데 비밀로 유지 되며 솔루션에 대 한 액세스 제어 목록 (Acl)를 사용 하 여입니다. / 27 같음 크기가 작은 범위를 제외 하 고 테두리 스위치 초과 라우팅할 것으로 예상 되지 네트워크 외부 리소스 및/또는 인터넷에 액세스 해야 하는 경우 이러한 서비스 중 일부에서 사용 합니다. 

### <a name="public-infrastructure-network"></a>공용 인프라 네트워크
이/27 네트워크는 앞에서 언급 한 Azure Stack 인프라 서브넷에서 작은 범위, 공용 IP 주소를 필요로 하지는 않지만 NAT 또는 투명 프록시를 통해 인터넷에 액세스할 필요가 있습니다. 이 네트워크는 응급 복구 콘솔 시스템 (ERCS)에 대 한 할당, ERCS VM 인프라 백업 및 Azure에 등록 하는 동안 인터넷 액세스가 필요 합니다. ERCS VM 문제 해결을 위해 관리 네트워크에 라우팅할 수 있어야 합니다.

### <a name="public-vip-network"></a>공용 VIP 네트워크
공용 VIP 네트워크는 Azure Stack에서 네트워크 컨트롤러에 할당 됩니다. 스위치에서 논리 네트워크는 없습니다. SLB 주소 풀을 사용 하 고 할당/32 테 넌 트 워크 로드에 대 한 네트워크입니다. 스위치 라우팅 테이블에 이러한 32 Ip로 BGP 통해 사용할 수 있는 경로 보급 됩니다. 이 네트워크 외부에서 액세스할 수 있는 또는 공용 IP 주소를 포함합니다. Azure Stack 인프라의 나머지 테 넌 트 Vm에서 사용 되는 동안이 공용 VIP 네트워크에서 처음 31 주소를 예약 합니다. 이 서브넷에 네트워크 크기/26 (64 호스트)의 최소 롤아웃의 경우/22 (1022 호스트)의 최대 범위, / 24에 대 한 계획 하는 것이 좋습니다 네트워크입니다.

### <a name="switch-infrastructure-network"></a>인프라 네트워크 스위치
이/26 네트워크 라우팅 가능한 지점간 IP/30 (2 호스트 Ip) 서브넷과만 사용 되는 루프백을 포함 하는 서브넷은 / 대역에 대 한 32 서브넷 관리 및 BGP 라우터 ID 전환 이 IP 주소 범위는 데이터 센터에 Azure Stack 솔루션의 외부에서 라우팅할 수 있어야 합니다., 사설 또는 공용 Ip 일 수 있습니다.

### <a name="switch-management-network"></a>스위치 관리 네트워크
/이 29 (6 호스트 Ip)이 네트워크 스위치 관리 포트 연결 전용입니다. 배포, 관리 및 문제 해결에 대 한 대역의 액세스할 수 있습니다. 위에서 언급 한 스위치 인프라 네트워크에서 계산 됩니다.

## <a name="publish-azure-stack-services"></a>Azure Stack 서비스를 게시 합니다.
Azure Stack 서비스 사용할 수 있도록 사용자에 게 외부 Azure Stack에서 해야 합니다. Azure Stack 인프라 역할에 대 한 다양 한 끝점을 설정합니다. 이러한 끝점의 공용 IP 주소 풀에서 Vip가 할당 됩니다. 배포 시 지정 된 외부 DNS 영역에서 각 끝점에 대 한 DNS 항목을 생성 됩니다. 예를 들어 사용자 포털에는 포털의 DNS 호스트 항목을 할당 됩니다.  *&lt;지역 >.&lt; fqdn >* 합니다.

### <a name="ports-and-urls"></a>포트 및 Url
Azure Stack 서비스 (같은 포털, Azure Resource Manager, DNS 등) 외부 네트워크에 사용할 수 있는 허용 하도록 설정 해야 이러한 끝점에 트래픽을 특정 Url, 포트 및 프로토콜입니다.
 
배포의 경우 기존 프록시 서버에 투명 프록시 업링크를 허용 해야 합니다 특정 포트 및 Url 모두에 대 한 [인바운드](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound) 하 고 [아웃 바운드](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound) 통신 합니다. 이러한 포트 및 Url id, marketplace, 패치 및 업데이트, 등록 및 사용 현황 데이터에 대 한 포함 합니다.

## <a name="next-steps"></a>다음 단계
[테두리 연결](azure-stack-border-connectivity.md)
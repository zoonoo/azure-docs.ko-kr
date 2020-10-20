---
title: Redis 용 Azure Cache 네트워크 격리 옵션
description: 이 문서에서는 사용자의 요구에 가장 적합 한 네트워크 격리 솔루션을 확인 하는 방법을 알아봅니다. Azure 개인 링크, Azure Virtual Network (VNet) 주입 및 Azure 방화벽 규칙에 대 한 기본 사항을 활용 하 여 장점과 제한 사항을 설명 합니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: b01e7ca9ff05b6eed51e1c454b8064ab28bda0d5
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222909"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Redis 용 Azure Cache 네트워크 격리 옵션 
이 문서에서는 사용자의 요구에 가장 적합 한 네트워크 격리 솔루션을 확인 하는 방법을 알아봅니다. Azure 개인 링크, Azure Virtual Network (VNet) 주입 및 Azure 방화벽 규칙에 대 한 기본 사항을 활용 하 여 장점과 제한 사항을 설명 합니다.  

## <a name="azure-private-link-public-preview"></a>Azure 개인 링크 (공개 미리 보기) 
Azure 개인 링크는 가상 네트워크에서 Azure PaaS 서비스로 개인 연결을 제공 합니다. 네트워크 아키텍처를 간소화 하 고 공용 인터넷에 대 한 데이터 노출을 제거 하 여 Azure에서 끝점 간의 연결을 보호 합니다. 

### <a name="advantages"></a>장점
* Redis 인스턴스에 대 한 Basic, Standard 및 Premium Azure Cache에서 지원 됩니다. 
* [Azure 개인 링크](/azure/private-link/private-link-overview)를 사용 하 여 가상 네트워크 내의 서브넷에서 개인 IP 주소가 할당 된 개인 끝점을 통해 가상 네트워크에서 azure Cache 인스턴스에 연결할 수 있습니다. 이를 통해 캐시 인스턴스는 VNet 내에서 공개적으로 사용할 수 있습니다.  
* 개인 끝점을 만든 후에는 플래그를 통해 공용 네트워크에 대 한 액세스를 제한할 수 있습니다 `publicNetworkAccess` . 이 플래그는 기본적으로로 설정 되어 `Enabled` 있으며, 캐시에 대 한 공용 및 개인 링크 액세스를 모두 허용 하는 옵션을 제공 합니다. 로 설정 되 면 `Disabled` 개인 링크 액세스만 허용 됩니다. PATCH 요청을 사용 하 여 값을로 설정할 수 있습니다 `Disabled` . 자세한 내용은 azure [개인 링크를 사용 하는 Redis 용 Azure Cache (미리 보기)](cache-private-link.md)를 참조 하세요. 
* 모든 외부 캐시 종속성은 VNet의 NSG 규칙에 영향을 주지 않습니다.

### <a name="limitations"></a>제한 사항 
* 개인 끝점에 대해 NSG (네트워크 보안 그룹)를 사용할 수 없습니다. 그러나 서브넷에 다른 리소스가 있는 경우 NSG 적용이 해당 리소스에 적용 됩니다.
* 지역에서 복제, 방화벽 규칙, 포털 콘솔 지원, 클러스터 된 캐시에 대 한 다중 끝점, 방화벽에 대 한 지 속성, VNet 삽입 캐시는 아직 지원 되지 않습니다. 
* 클러스터형 캐시에 연결 하려면를 `publicNetworkAccess` 로 설정 해야 `Disabled` 하며 개인 끝점 연결은 하나만 있을 수 있습니다.

> [!NOTE]
> 캐시 인스턴스에 개인 끝점을 추가 하는 경우 DNS 때문에 모든 Redis 트래픽이 개인 끝점으로 이동 됩니다.
> 이전 방화벽 규칙이 이전에 조정 되었는지 확인 합니다.  
>
>

## <a name="azure-virtual-network-injection"></a>Azure Virtual Network 주입 
VNet은 Azure에서 개인 네트워크의 기본 구성 요소입니다. VNet을 사용 하면 여러 Azure 리소스가 서로 안전 하 게 통신 하 고, 인터넷 및 온-프레미스 네트워크를 사용할 수 있습니다. VNet은 사용자의 데이터 센터에서 작동 하는 기존 네트워크와 유사 하지만, Azure 인프라, 확장, 가용성 및 격리의 이점을 제공 합니다. 

### <a name="advantages"></a>장점
* Redis 인스턴스에 대 한 Azure 캐시가 VNet을 사용 하 여 구성 된 경우 공개적으로 주소를 지정할 수 없으며 VNet 내의 응용 프로그램 및 가상 컴퓨터 에서만 액세스할 수 있습니다.  
* VNet을 제한 된 NSG 정책과 결합 하면 데이터 exfiltration의 위험을 줄일 수 있습니다. 
* VNet 배포는 서브넷, 액세스 제어 정책 및 기타 기능을 사용 하 여 액세스를 제한 하는 Redis 용 Azure 캐시에 대 한 향상 된 보안 및 격리를 제공 합니다. 
* 지역 복제가 지원 됩니다. 

### <a name="limitations"></a>제한 사항
* VNet 삽입 된 캐시는 Redis 용 프리미엄 Azure 캐시에만 사용할 수 있습니다. 
* VNet 삽입 캐시를 사용 하는 경우 VNet/PKI, AKV, Azure Storage, Azure Monitor 등의 종속성을 캐시 하도록 VNet을 열어야 합니다.  


## <a name="azure-firewall-rules"></a>Azure 방화벽 규칙
[Azure 방화벽](/azure/firewall/overview) 은 azure VNet 리소스를 보호 하는 관리 되는 클라우드 기반 네트워크 보안 서비스입니다. 기본 제공 되는 고가용성 및 무제한 클라우드 확장성을 갖춘 완벽 한 상태 저장 방화벽으로 서의 서비스입니다. 구독 및 가상 네트워크 전반에 걸쳐 애플리케이션 및 네트워크 연결 정책을 중앙에서 만들고, 적용하고 기록할 수 있습니다.  

### <a name="advantages"></a>장점
* 방화벽 규칙이 구성되면 지정된 IP 주소 범위의 클라이언트 연결만 캐시에 연결할 수 있습니다. 방화벽 규칙이 구성된 경우에도 Azure Cache for Redis 모니터링 시스템의 연결은 항상 허용됩니다. 또한 사용자가 정의 하는 NSG 규칙이 허용 됩니다.  

### <a name="limitations"></a>제한 사항
* 방화벽 규칙은 VNet 삽입 캐시와 함께 사용할 수 있지만 현재 개인 끝점은 사용할 수 없습니다. 


## <a name="next-steps"></a>다음 단계
* [Redis 인스턴스에 대 한 프리미엄 Azure 캐시에 대해 VNet 삽입 캐시](cache-how-to-premium-vnet.md)를 구성 하는 방법에 대해 알아봅니다.  
* [Redis 계층에 대 한 모든 Azure 캐시에 대 한 방화벽 규칙](cache-configure.md#firewall)을 구성 하는 방법을 알아봅니다. 
* [Redis 계층에 대 한 모든 Azure 캐시에 대 한 개인 끝점을 구성](cache-private-link.md)하는 방법을 알아봅니다. 

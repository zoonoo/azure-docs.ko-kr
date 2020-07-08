---
title: 질문과 대답-Azure Load Balancer
description: Azure Load Balancer에 대 한 자주 묻는 질문에 대 한 대답입니다.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 205a4bd119a7324c4e6524a0e29d432aa57bf315
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85848221"
---
# <a name="load-balancer-frequently-asked-questions"></a>Load Balancer 질문과 대답

## <a name="what-types-of-load-balancer-exist"></a>어떤 유형의 Load Balancer 존재 하나요?
내부 부하 분산 장치는 VNET 내의 트래픽과 인터넷 연결 끝점 간에 트래픽을 분산 하는 외부 부하 분산 장치를 분산 합니다. 자세한 내용은 [Load Balancer 형식](components.md#frontend-ip-configurations)을 참조 하세요. 

이러한 두 유형 모두 Azure는 기능, 성능, 보안 및 상태 추적 기능이 서로 다른 기본 SKU 및 표준 SKU를 제공 합니다. 이러한 차이점은 [SKU 비교](skus.md) 문서에 설명 되어 있습니다.

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>기본에서 표준 Load Balancer로 업그레이드 하려면 어떻게 해야 하나요?
자동화 된 스크립트에 대 한 [기본에서 표준으로 업그레이드](upgrade-basic-standard.md) 문서와 Load Balancer SKU 업그레이드에 대 한 지침은을 참조 하세요.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Azure의 다양 한 부하 분산 옵션은 무엇 인가요?
사용할 수 있는 부하 분산 서비스 및 권장 사용에 대 한 [부하 분산 장치 기술 가이드](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) 를 참조 하세요.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Load Balancer ARM 템플릿은 어디에서 찾을 수 있나요?
일반적인 배포의 ARM 템플릿에 대 한 [Azure Load Balancer 빠른 시작 템플릿 목록을](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) 참조 하세요.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>인바운드 NAT 규칙은 부하 분산 규칙과 어떻게 다른가?
NAT 규칙은 트래픽을 라우팅하는 백 엔드 리소스를 지정 하는 데 사용 됩니다. 예를 들어 특정 부하 분산 장치 포트를 구성 하 여 특정 VM에 RDP 트래픽을 전송 합니다. 부하 분산 규칙은 트래픽을 라우팅하는 백 엔드 리소스의 풀을 지정 하는 데 사용 되며, 각 인스턴스에 대해 부하를 분산 합니다. 예를 들어 부하 분산 장치 규칙은 웹 서버 풀에서 부하 분산 장치의 포트 80에서 TCP 패킷을 라우팅할 수 있습니다.

## <a name="what-is-ip-1686312916"></a>IP 168.63.129.16?
Azure 상태 검색이 시작 되는 Azure 인프라 Load Balancer로 태그가 지정 된 호스트의 가상 IP 주소입니다. 백 엔드 인스턴스를 구성할 때이 IP 주소의 트래픽이 상태 프로브에 성공적으로 응답 하도록 허용 해야 합니다. 이 규칙은 Load Balancer 프런트 엔드에 대 한 액세스와 상호 작용 하지 않습니다. Azure Load Balancer 사용 하지 않는 경우이 규칙을 재정의할 수 있습니다. [여기](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)에서 서비스 태그에 대해 자세히 알아볼 수 있습니다.

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>기본 Load Balancer와 함께 글로벌 VNET 피어 링을 사용할 수 있나요?
아니요. 기본 Load Balancer는 글로벌 VNET 피어 링을 지원 하지 않습니다. 대신 표준 Load Balancer를 사용할 수 있습니다. 원활한 업그레이드는 [기본에서 표준으로 업그레이드](upgrade-basic-standard.md) 문서를 참조 하세요.

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>Azure VM에서 사용 하는 공용 IP를 검색 하려면 어떻게 해야 하나요?

여러 가지 방법으로 아웃바운드 연결의 공용 원본 IP 주소를 확인할 수 있습니다. OpenDNS는 VM의 공용 IP 주소를 표시할 수 있는 서비스를 제공합니다.
nslookup 명령을 사용하여 OpenDNS 확인자에 myip.opendns.com이라는 이름에 대한 DNS 쿼리를 전송할 수 있습니다. 서비스는 쿼리를 보내는 데 사용된 원본 IP 주소를 반환합니다. VM에서 다음 쿼리를 실행하는 경우 응답은 해당 VM에 사용되는 공용 IP입니다.

 ```nslookup myip.opendns.com resolver1.opendns.com```

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>동일한 지역에서 Azure Storage에 대 한 연결은 어떻게 작동 하나요?
위의 시나리오를 통한 아웃바운드 연결은 VM과 동일한 지역에 있는 스토리지에 연결하는 데 필요하지 않습니다. 이를 원하지 않는 경우 위에서 설명한 대로 NSG(네트워크 보안 그룹)를 사용합니다. 다른 지역의 스토리지에 연결하려면 아웃바운드 연결이 필요합니다. 동일한 지역의 VM에서 스토리지에 연결하는 경우 스토리지 진단 로그의 원본 IP 주소는 VM의 공용 IP 주소가 아니라 내부 공급자 주소가 됩니다. 동일한 지역에서 하나 이상의 Virtual Network 서브넷에 있는 VM에 대한 스토리지 계정에 액세스를 제한하려면 스토리지 계정 방화벽을 구성할 때 공용 IP 주소가 아니라 [Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 사용합니다. 서비스 엔드포인트가 구성되면 내부 공급자 주소가 아닌 스토리지 진단 로그에 Virtual Network 개인 IP 주소가 표시됩니다.

## <a name="next-steps"></a>다음 단계
위에 나열 되지 않은 경우 질문에 대 한 피드백을 보내 주세요. 이렇게 하면 제품 팀에 대 한 GitHub 문제를 만들어 모든 중요 한 고객 질문이 답변 되었는지 확인할 수 있습니다.

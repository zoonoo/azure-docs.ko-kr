---
title: Azure Load Balancer란?
titleSuffix: Azure Load Balancer
description: Azure Load Balancer 기능, 아키텍처 및 구현에 대한 개요입니다. Load Balancer의 작동 방법과 클라우드에서 이를 활용하는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2019
ms.author: allensu
ms.openlocfilehash: 4f6a0a2631c3f7458548a2ff435c3e565e744f41
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660218"
---
# <a name="what-is-azure-load-balancer"></a>Azure Load Balancer란?

*부하 분산*은 백 엔드 리소스 또는 서버의 그룹에서 로드 또는 들어오는 네트워크 트래픽을 효율적으로 분산하는 것을 의미합니다. Azure는 필요에 따라 선택할 수 있는 [다양한 부하 분산 옵션](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)을 제공합니다. 이 문서에서는 Azure Load Balancer에 대해 설명합니다.

Azure Load Balancer는 OSI(Open Systems Interconnect) 모델의 계층 4에서 작동합니다. 클라이언트의 단일 연락 지점입니다. Load Balancer는 지정된 부하 분산 규칙 및 상태 프로브에 따라 Load Balancer의 프런트 엔드에 도착하는 새 인바운드 흐름을 백 엔드 풀 인스턴스에 분산시킵니다. 백 엔드 풀 인스턴스는 Azure Virtual Machines 또는 가상 머신 확장 집합의 인스턴스일 수 있습니다. 

Azure Load Balancer를 사용하면 애플리케이션 크기를 조정하고 고가용성 서비스를 만들 수 있습니다. Load Balancer는 인바운드 및 아웃바운드 시나리오를 모두 지원하고, 짧은 대기 시간과 높은 처리량을 제공하며, 모든 TCP 및 UDP 애플리케이션에 대해 수백만 개의 흐름으로 확장됩니다.

**[공용 Load Balancer](#publicloadbalancer)** 는 개인 IP 주소를 공용 IP 주소로 변환하여 가상 네트워크 내에서 VM(가상 머신)에 대한 아웃바운드 연결을 제공할 수 있습니다. 공용 Load Balancer는 인터넷 트래픽 부하를 VM에 분산하는 데 사용됩니다.

**[내부(또는 프라이빗) Load Balancer](#internalloadbalancer)** 는 프런트 엔드에 개인 IP 주소만 필요한 시나리오에 사용할 수 있습니다. 내부 Load Balancer는 트래픽 부하를 가상 네트워크 내에 분산하는 데 사용됩니다. 하이브리드 시나리오의 온-프레미스 네트워크에서 Load Balancer 프런트 엔드에 연결할 수도 있습니다.

## <a name="load-balancer-components"></a>Load Balancer 구성 요소
* **프런트 엔드 IP 구성**: Load Balancer의 IP 주소입니다. 클라이언트의 연락 지점입니다. 이러한 주소는 공용 또는 개인 IP 주소일 수 있으므로 각각 공용 또는 내부 Load Balancer를 만들 수 있습니다.
* **백 엔드 풀**: 들어오는 요청을 처리할 Virtual Machines 또는 Virtual Machine Scale Set 인스턴스의 그룹입니다. 대량의 들어오는 트래픽을 충족하기 위해 비용 효율적으로 크기를 조정하려면 일반적으로 백 엔드 풀에 더 많은 인스턴스를 추가하는 것이 좋습니다. Load Balancer는 인스턴스의 크기를 확장하거나 축소하는 경우 자동 재구성을 통해 자체적으로 즉시 재구성됩니다. 백 엔드 풀에서 VM을 추가 또는 제거하면 Load Balancer 리소스에 대한 추가 작업 없이도 Load Balancer가 다시 구성됩니다.
* **상태 프로브**: 상태 프로브는 백 엔드 풀의 인스턴스 상태를 확인하는 데 사용됩니다. 상태 프로브에 대한 비정상 임계값을 정의할 수 있습니다. 프로브가 응답하지 않으면 Load Balancer는 비정상 인스턴스에 새 연결의 전송을 중지합니다. 프로브 오류는 기존 연결에 영향을 주지 않습니다. 애플리케이션에서 흐름을 종료 하거나, 유휴 시간 제한이 발생하거나, VM이 종료될 때까지 연결이 계속됩니다. Load Balancer는 TCP, HTTP 및 HTTPS 엔드포인트에 대한 다양한 상태 프로브 유형을 제공합니다. 자세한 내용은 [프로브 유형](load-balancer-custom-probe-overview.md#types)을 참조하세요.
* **부하 분산 규칙**: 부하 분산 규칙은 Load Balancer에 필요한 작업을 언제 수행해야 하는지 알려주는 규칙입니다. 
* **인바운드 NAT 규칙**: 인바운드 NAT 규칙은 트래픽을 특정 프런트 엔드 IP 주소의 특정 포트에서 가상 네트워크 내에 있는 특정 백 엔드 인스턴스의 특정 포트로 전달합니다. 포트 전달은 부하 분산과 동일한 해시 기반 배포를 통해 수행됩니다. 이 기능에 대한 일반적인 시나리오는 Azure Virtual Network 내의 개별 VM 인스턴스에 대한 RDP(원격 데스크톱 프로토콜) 또는 SSH(보안 셸) 세션입니다. 여러 개의 내부 엔드포인트를 동일한 프런트 엔드 IP 주소의 포트에 매핑할 수 있습니다. 프런트 엔드 IP 주소를 사용하여 추가 점프 상자 없이 VM을 원격으로 관리할 수 있습니다.
* **아웃바운드 규칙**: 아웃바운드 규칙은 백 엔드 풀에서 식별된 모든 가상 머신 또는 인스턴스에 대한 아웃바운드 NAT(Network Address Translation)를 프런트 엔드로 변환하도록 구성합니다.

## <a name="load-balancer-concepts"></a>Load Balancer 개념

Load Balancer는 TCP 및 UDP 애플리케이션에 대해 다음과 같은 기본적인 기능을 제공합니다.

* **부하 분산 알고리즘**: Azure Load Balancer를 사용하면 프런트 엔드에 도착하는 트래픽을 백 엔드 풀 인스턴스로 분산시키는 부하 분산 규칙을 만들 수 있습니다. Load Balancer는 인바운드 흐름 배포에 해싱 알고리즘을 사용하고, 흐름의 헤더를 백 엔드 풀 인스턴스에 다시 작성합니다. 상태 프로브가 정상 백 엔드 엔드포인트을 나타내면 서버에서 새 흐름을 받을 수 있습니다.
기본적으로 Load Balancer는 5 튜플 해시를 사용합니다. 해시에는 흐름을 사용 가능한 서버에 매핑하기 위한 원본 IP, 원본 포트, 대상 IP 주소, 대상 포트 및 IP 프로토콜 번호가 포함되어 있습니다. 제공된 규칙에 대해 2 또는 3튜플 해시를 사용하여 원본 IP 주소에 대한 선호도를 만들 수 있습니다. 같은 패킷 흐름의 모든 패킷이 부하 분산된 프런트 엔드 뒤에 있는 동일한 인스턴스에 도착합니다. 클라이언트가 동일한 원본 IP에서 새 흐름을 시작하면 원본 포트가 변경됩니다. 결과적으로 5개 튜플 해시로 인해 트래픽이 다른 백 엔드 엔드포인트로 이동할 수 있습니다.
자세한 내용은 [Azure Load Balancer의 배포 모드 구성](load-balancer-distribution-mode.md)을 참조하세요. 다음 이미지는 해시 기반 배포를 표시합니다.

  ![해시 기반 배포](./media/load-balancer-overview/load-balancer-distribution.png)

  그림: *해시 기반 배포 

* **애플리케이션 독립성 및 투명성**: Load Balancer는 TCP 또는 UDP나 애플리케이션 계층과 직접 상호 작용하지 않습니다. 모든 TCP 또는 UDP 애플리케이션 시나리오를 지원할 수 있습니다. Load Balancer는 흐름을 종료하거나 시작하지 않으며, 흐름의 페이로드와 상호 작용하지 않고, 애플리케이션 계층 게이트웨이 함수를 제공하지 않습니다. 프로토콜 핸드셰이크는 항상 클라이언트와 백 엔드 풀 인스턴스 사이에서 직접 발생합니다. 인바운드 흐름에 대한 응답은 항상 가상 머신의 응답입니다. 흐름이 가상 머신에 도착하면 원래 원본 IP 주소도 유지됩니다.
  * 모든 엔드포인트는 VM에서만 응답합니다. 예를 들어, TCP 핸드셰이크는 항상 클라이언트와 선택한 백 엔드 VM 사이에서 발행합니다. 프런트 엔드에 요청에 대한 응답은 백 엔드 VM에서 생성한 응답입니다. 프런트 엔드에 대한 연결의 유효성을 성공적으로 확인하는 경우 최소 하나 이상의 백 엔드 가상 머신에 통합형 연결의 유효성을 검사하는 것입니다.
  * 애플리케이션 페이로드는 Load Balancer에 투명합니다. 모든 UDP 또는 TCP 애플리케이션을 지원할 수 있습니다.
  * Load Balancer는 TCP 페이로드와 상호 작용하거나 TLS 오프로드를 제공하지 않기 때문에 엔드투엔드 암호화 시나리오를 구축할 수 있습니다. Load Balancer를 사용하면 VM 자체에서 TLS 연결을 종료하여 TLS 애플리케이션에 대한 대규모 스케일 아웃이 구현됩니다. 예를 들어 TLS 세션 키 용량은 백 엔드 풀에 추가하는 VM의 수와 유형으로만 제한됩니다.

* **아웃바운드 연결(SNAT)** : 가상 네트워크 내의 개인 IP 주소에서 인터넷의 퍼블릭 IP 주소로 전달되는 모든 아웃바운드 흐름은 Load Balancer의 프런트 엔드 IP 주소로 변환될 수 있습니다. 퍼블릭 프런트 엔드가 부하 분산 규칙을 통해 백 엔드 VM에 연결되면 Azure는 아웃바운드 연결을 퍼블릭 프런트 엔드 IP 주소로 변환합니다. 이 구성의 장점은 다음과 같습니다.
  * 프런트 엔드를 서비스의 다른 인스턴스에 동적으로 매핑할 수 있으므로 서비스의 업그레이드 및 재해 복구가 용이합니다.
  * ACL(액세스 제어 목록)을 보다 쉽게 관리할 수 있습니다. 프런트 엔드 IP로 표현되는 ACL은 서비스를 확장 또는 축소하거나 다시 배포해도 변경되지 않습니다. 아웃바운드 연결을 컴퓨터보다 더 적은 수의 IP 주소로 변환하면 수신 허용 - 받는 사람 목록을 구현해야 한다느 부담을 줄일 수 있습니다.
자세한 내용은 [Azure의 아웃바운드 연결](load-balancer-outbound-connections.md)을 참조하세요.
아래에 설명된 것처럼 표준 Load Balancer에는 이러한 기본 기능 외에 추가적인 SKU 관련 기능이 있습니다.

## <a name="skus"></a> Load Balancer SKU 비교

Load Balancer는 기본 및 표준 SKU를 둘 다 지원합니다. 이러한 SKU는 시나리오 규모, 기능 및 가격 책정에서 차이가 있습니다. 기본 Load Balancer로 가능한 시나리오를 표준 Load Balancer로 만들 수 있습니다. 두 SKU에 대한 API는 유사하고 SKU를 지정해서 호출됩니다. Load Balancer 및 공용 IP에 대한 지원 SKU의 API는 `2017-08-01` API부터 사용할 수 있습니다. 두 SKU는 동일한 일반 API 및 구조를 갖습니다.

전체 시나리오 구성은 SKU에 따라 약간 다를 수 있습니다. Load Balancer 설명서는 특정 SKU에만 문서가 적용될 때 제공됩니다. 차이점을 비교하고 파악하려면 아래 표를 참조하요. 자세한 내용은 [Azure 표준 Load Balancer 개요](load-balancer-standard-overview.md)를 참조하세요.

>[!NOTE]
> Microsoft는 표준 Load Balancer를 권장합니다.
독립 실행형 VM, 가용성 집합 및 가상 머신 확장 집합은 하나의 SKU에만 연결될 수 있습니다. 퍼블릭 IP 주소와 함께 사용할 때는 Load Balancer 및 퍼블릭 IP 주소 SKU가 일치해야 합니다. Load Balancer 및 공용 IP SKU는 변경할 수 없습니다.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

자세한 내용은 [Load Balancer 제한](https://aka.ms/lblimits)을 참조하세요. 표준 Load Balancer 세부 내용은 [개요](load-balancer-standard-overview.md), [가격](https://aka.ms/lbpricing) 및 [SLA](https://aka.ms/lbsla)를 참조하세요.

## <a name="load-balancer-types"></a>Load Balancer 유형

### <a name = "publicloadbalancer"></a>공용 Load Balancer

퍼블릭 Load Balancer는 들어오는 트래픽의 퍼블릭 IP 주소와 포트를 VM의 프라이빗 IP 주소 및 포트에 매핑합니다. Load Balancer는 VM의 응답 트래픽에 대해 다른 방식으로 트래픽을 매핑합니다. 부하 분산 규칙을 적용하여 특정 유형의 트래픽을 여러 VM 또는 서비스에 분산할 수 있습니다. 예를 들어 웹 요청 트래픽의 부하를 여러 웹 서버에 분산할 수 있습니다.

>[!NOTE]
>가용성 집합당 하나의 퍼블릭 Load Balancer와 하나의 내부 Load Balancer만 구현할 수 있습니다.

다음 그림에서는 TCP 포트 80에 대해 3개의 퍼블릭 VM 간에 공유되는 웹 트래픽의 부하가 분산된 엔드포인트를 보여 줍니다. 이 세 대의 VM은 부하 분산 집합에 속합니다.

![공용 Load Balancer 예](./media/load-balancer-overview/IC727496.png)

그림: *퍼블릭 Load Balancer를 사용하여 웹 트래픽 부하 분산*

인터넷 클라이언트가 TCP 포트 80에서 웹앱의 퍼블릭 IP 주소에 웹 페이지 요청을 보냅니다. Azure Load Balancer는 부하 분산된 세트에 있는 3개 VM에 요청을 분산합니다. Load Balancer 알고리즘에 대한 자세한 내용은 [Load Balancer 개념](load-balancer-overview.md##load-balancer-concepts)을 참조하세요.

기본적으로 Azure Load Balancer는 네트워크 트래픽을 여러 VM 인스턴스에 고르게 분산시킵니다. 세션 선호도를 구성할 수도 있습니다. 자세한 내용은 [Azure Load Balancer의 배포 모드 구성](load-balancer-distribution-mode.md)을 참조하세요.

### <a name = "internalloadbalancer"></a> 내부 Load Balancer

내부 Load Balancer는 퍼블릭 Load Balancer와 달리, 가상 네트워크 내부에 있는 리소스 또는 VPN을 사용하여 Azure 인프라에 액세스하는 리소스로만 트래픽을 보냅니다. Azure 인프라는 가상 네트워크의 부하가 분산된 프런트 엔드 IP 주소에 대한 액세스를 제한합니다. 프런트 엔드 IP 주소와 가상 네트워크는 인터넷 엔드포인트에 직접 노출되지 않습니다. 내부 LOB(기간 업무) 애플리케이션은 Azure에서 실행되며 Azure 내에서 또는 온-프레미스 리소스에서 액세스할 수 있습니다.

내부 Load Balancer를 통해 다음과 같은 유형의 부하 분산을 사용할 수 있습니다.

* **가상 네트워크 내에서**: 가상 네트워크의 VM에서 동일한 가상 네트워크 내에 있는 VM 세트로 부하 분산
* **크로스-프레미스 가상 네트워크의 경우**: 온-프레미스 컴퓨터에서 동일한 가상 네트워크 내에 있는 VM 세트로 부하 분산
* **다중 계층 애플리케이션의 경우**: 백 엔드 계층이 인터넷에 연결되어 있지 않은 인터넷 연결 다중 계층 애플리케이션의 부하 분산 백 엔드 계층에는 인터넷 연결 계층의 트래픽 부하 분산이 필요합니다. 다음 그림을 참조하세요.
* **LOB(기간 업무) 애플리케이션의 경우**: 추가적인 부하 분산 장치 하드웨어 또는 소프트웨어 없이 Azure에서 호스트되는 LOB(기간 업무) 애플리케이션의 부하 분산. 이 시나리오는 트래픽 부하가 분산되는 컴퓨터 세트에 있는 온-프레미스 서버를 포함합니다.

![내부 Load Balancer 예제](./media/load-balancer-overview/IC744147.png)

그림: *퍼블릭 및 내부 Load Balancer를 둘 다 사용하여 다중 계층 애플리케이션 부하 분산*

## <a name="pricing"></a>가격

표준 Load Balancer 사용량에 해당하는 요금이 청구됩니다.

* 구성된 부하 분산 및 아웃바운드 규칙의 수. 인바운드 NAT 규칙은 총 규칙 수를 계산하지 않습니다.
* 규칙에 관계없이 인바운드 및 아웃바운드에서 처리된 데이터의 양.

표준 Load Balancer 가격 정보에 대해서는 [Load Balancer 가격](https://azure.microsoft.com/pricing/details/load-balancer/)을 참조하세요.

기본 Load Balancer는 무료로 제공됩니다.

## <a name="sla"></a>SLA

표준 Load Balancer SLA에 대한 내용은 [Load Balancer에 대한 SLA](https://aka.ms/lbsla)를 참조하세요.

## <a name="limitations"></a>제한 사항

* Load Balancer는 특정 TCP 또는 UDP 프로토콜에 대한 부하 분산 및 포트 전달을 제공합니다. 부하 분산 규칙 및 인바운드 NAT 규칙은 TCP 및 UDP를 지원하지만 ICMP를 비롯한 다른 IP 프로토콜은 지원하지 않습니다.

  Load Balancer가 종료 및 응답하지 않거나 UDP 또는 TCP 흐름의 페이로드와 상호 작용하지 않습니다. 프록시가 아닙니다. 부하 분산 또는 인바운드 NAT 규칙에서 사용되는 것과 동일한 프로토콜을 사용하여 대역 내에서 프런트 엔드 연결에 대한 유효성 검사가 성공해야 합니다. 클라이언트가 프런트 엔드의 응답을 확인하려면 하나 이상의 가상 머신이 응답을 생성해야 합니다.

  Load Balancer 프런트 엔드에서 대역 내 응답을 수신하지 못하면 어떤 가상 머신도 응답할 수 없음을 나타냅니다. 응답할 수 있는 가상 머신이 없으면 Load Balancer 프런트 엔드와 상호 작용이 불가능합니다. 이는 SNAT 모조 포트가 TCP 및 UDP에 대해서만 지원되는 경우에 아웃바운드 연결에도 적용됩니다. ICMP를 비롯한 다른 모든 IP 프로토콜은 실패합니다. 이 문제를 완화하려면 인스턴스 수준 퍼블릭 IP 주소를 할당합니다. 자세한 내용은 [SNAT 및 PAT 이해](load-balancer-outbound-connections.md#snat)를 참조하세요.

* 내부 Load Balancer는 아웃바운드에서 시작된 연결을 내부 Load Balancer의 프런트 엔드로 변환하지 않습니다. 둘 다 프라이빗 IP 주소 공간에 있기 때문입니다. 퍼블릭 Load Balancer는 가상 네트워크 내부의 프라이빗 IP 주소에서 퍼블릭 IP 주소로의 [아웃바운드 연결](load-balancer-outbound-connections.md)을 제공합니다. 내부 Load Balancer의 경우, 이렇게 하면 변환이 필요하지 않으면 고유한 내부 IP 주소 공간 내에서 SNAT 포트가 고갈될 가능성이 방지됩니다.

  부작용은 백 엔드 풀에 있는 VM에서의 아웃바운드 흐름이 해당 풀에 있는 내부 Load Balancer의 프런트 엔드로 흐름을 유도하_고_ 자체로 매핑되는 경우, 흐름의 두 레그가 일치하지 않는다는 것입니다. 두 레그가 일치하지 않기 때문에 흐름이 실패합니다. 흐름은 프런트 엔드로의 흐름을 생성한 백 엔드 풀의 동일한 VM에 다시 매핑되지 않으면 성공하게 됩니다.

  흐름이 스스로에게 다시 매핑되는 경우 아웃바운드 흐름은 VM에서 시작된 후 프런트 엔드에 나타나며 해당 인바운드 흐름은 VM에서 시작된 후 자신에게 나타납니다. 게스트 운영 체제의 관점에서 동일한 흐름의 인바운드 및 아웃바운드 부분은 가상 머신 내에서 일치하지 않습니다. TCP 스택은 동일한 흐름의 이러한 절반을 동일한 흐름의 일부로서 인식하지 않습니다. 소스와 대상은 일치하지 않습니다. 흐름이 백 엔드 풀에서 다른 모든 VM에 매핑되는 경우 흐름의 절반은 일치하며 VM은 흐름에 응답할 수 있습니다.

  이 시나리오에 대한 증상은 흐름이 흐름을 처음 시작한 동일한 백 엔드를 반환할 때 일시적인 연결 시간 제한입니다. 일반적인 해결 방법은 내부 Load Balancer 뒤에 프록시 계층을 삽입하고 DSR(Direct Server Return) 스타일 규칙을 사용하는 것입니다. 자세한 내용은 [Azure Load Balancer의 여러 프런트 엔드](load-balancer-multivip-overview.md)를 참조하세요.

  타사 프록시에 내부 Load Balancer를 결합하거나 HTTP/HTTPS가 있는 프록시 시나리오에 대해 내부 [Application Gateway](../application-gateway/application-gateway-introduction.md)를 사용할 수 있습니다. 퍼블릭 Load Balancer를 사용하여 이 문제를 완화할 수는 있지만 결과 시나리오에서는 [SNAT 고갈](load-balancer-outbound-connections.md#snat)이 발생할 가능성이 높습니다. 신중하게 관리하지 못할 경우에는 이 두 번째 방법을 사용하지 마세요.

* 일반적으로 부하 분산 규칙에서는 IP 조각 전달이 지원되지 않습니다. UDP 및 TCP 패킷의 IP 조각화는 부하 분산 규칙에서 지원되지 않습니다. 고가용성 포트 부하 분산 규칙을 사용하여 기존 IP 조각을 전달할 수 있습니다. 자세한 내용은 [고가용성 포트 개요](load-balancer-ha-ports-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Load Balancer를 사용하여 시작하려면 [공용 표준 Load Balancer 만들기](quickstart-load-balancer-standard-public-portal.md)를 참조하세요. 하나의 부하 분산 장치를 만들고, 사용자 지정 IIS 확장이 설치된 VM을 만들고, VM 간에 웹앱의 부하를 분산시킵니다.

---
title: Azure DDoS Protection 모범 사례 및 참조 아키텍처 | Microsoft Docs
description: 로깅 데이터를 사용하여 응용 프로그램에 대해 깊이 이해할 수 있는 방법에 대해 알아봅니다.
services: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2018
ms.author: barclayn
ms.openlocfilehash: fea235ed057517840515f1ad0543dba1b1c46dd0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architecture"></a>Azure DDoS Protection: 모범 사례 및 참조 아키텍처

이 문서는 IT 의사 결정권자 및 보안 담당자를 대상으로 합니다. Azure, 네트워킹 및 보안에 대한 지식이 요구됩니다.

DDoS(분산 서비스 거부) 복원력을 설계하려면 다양한 오류 모드에 대한 계획과 설계가 필요합니다. 이 문서에서는 Azure에서 DDoS 공격에 대한 복원력을 갖춘 응용 프로그램을 디자인하기 위한 모범 사례를 제공합니다.

## <a name="types-of-attacks"></a>공격 유형

DDoS는 응용 프로그램 리소스를 고갈시키기 위해 사용되는 공격 유형입니다. 공격 목표는 응용 프로그램의 가용성과 합법적인 요청을 처리하는 기능을 약화시키는 것입니다. 최근에는 공격이 점점 정교해지고 그 규모와 영향도 점점 커지고 있습니다. 인터넷을 통해 공개적으로 도달 가능한 모든 끝점은 DDoS 공격의 대상이 될 수 있습니다.

Azure는 DDoS 공격으로부터 지속적으로 보호하는 기능을 제공합니다. 이 보호 기능은 기본적으로 추가 비용 없이 Azure 플랫폼에 통합됩니다. 플랫폼에 제공되는 핵심 DDoS 보호 기능 외에도, 네트워크 공격에 대한 고급 DDoS 완화 기능을 제공하고 특정 Azure 리소스를 보호하도록 자동으로 조정되는 "[Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/)"라고 하는 새로운 서비스가 제공됩니다. 새 가상 네트워크를 만드는 동안 간단하게 보호를 사용하도록 설정할 수 있습니다. 초기 생성 후에도 설정 가능하며 응용 프로그램 또는 리소스를 변경할 필요가 없습니다.

![](media/azure-ddos-best-practices/image1.png)

DDoS 공격은 크게 3가지 범주로 나눌 수 있습니다.

### <a name="volumetric-attacks"></a>대규모 공격

대규모 공격은 가장 일반적인 유형의 DDoS 공격입니다. 대규모 공격은 네트워크 및 전송 레이어를 대상으로 하는 무차별 암호 대입 공격입니다. 네트워크 링크 같은 리소스를 고갈시키려 시도합니다. 일반적으로 이 공격은 감염된 여러 시스템을 사용하여 정상으로 보이는 대량의 트래픽으로 네트워크 레이어를 마비시키려 합니다. ICMP(Internet Control Message Protocol), UDP(User Datagram Protocol), TCP(Transmission Control Protocol) 같은 여러 네트워크 레이어 프로토콜이 이 유형의 공격에 사용됩니다.

가장 일반적으로 사용되는 네트워크 레이어 DDoS 공격은 TCP SYN 초과, ICMP 에코, UDP 초과, DNS 및 NTP 증폭 공격입니다. 이러한 유형의 공격은 서비스를 중단시킬 목적뿐 아니라 특정 네트워크에 불법적으로 침입하기 위한 위장 공격에도 사용될 수 있습니다. 최근에 있었던 대규모 공격의 예로는 GitHub에 영향을 미친 [Memcached 악용](https://www.wired.com/story/github-ddos-memcached/)이 있습니다. 이 공격은 UDP 포트 11211을 대상으로 1.35Tb/s의 공격 볼륨을 생성했습니다.

### <a name="protocol-attacks"></a>프로토콜 공격

프로토콜 공격은 응용 프로그램 프로토콜을 대상으로 합니다. 방화벽, 응용 프로그램 서버, 부하 분산 장치 등의 인프라 장치에서 사용할 수 있는 모든 리소스를 남김 없이 사용하려고 시도합니다. 프로토콜 공격은 형식이 잘못되었거나 프로토콜 이상이 있는 패킷을 사용합니다. 이 공격은 대량의 열린 요청, 서버 및 기타 통신 장치 응답을 보내고 패킷 응답을 기다립니다. 대상이 열린 요청에 응답하려고 시도하면 결국 대상 시스템이 크래시를 일으킵니다.

프로토콜 기반 DDoS 공격의 대표적인 예는 TCP SYN Flood입니다. 이 공격에서는 연속 TCP SYN 요청이 대상으로 전달되어 과부하를 일으키는 데 사용됩니다. 대상이 응답하지 않게 만드는 것이 공격 목표입니다. 응용 프로그램 계층을 공격하는 2016 Dyn 중단은 Dyn의 DNS 서버 포트 53을 대상으로 하는 TCP SYN 플러드로 구성됩니다.

### <a name="resource-attacks"></a>리소스 공격

리소스 공격은 응용 프로그램 계층을 대상으로 합니다. 대상 시스템에 과부하를 일으키기 위해 백 엔드 프로세스를 트리거합니다. 리소스 공격은 정상으로 보이지만 CPU를 많이 사용하는 쿼리를 서버로 보내 트래픽을 유발합니다. 리소스를 고갈시키는 데 필요한 트래픽 볼륨이 다른 유형의 공격에 비해 상대적으로 적습니다. 리소스 공격의 트래픽과 정당한 트래픽을 구분할 수 없기 때문에 감지하기가 어렵습니다. 리소스 공격은 대부분 HTTP/HTTPS 및 DNS 서비스에서 이루어집니다.

## <a name="shared-responsibility-in-the-cloud"></a>클라우드의 공동 책임

점점 다양해지고 정교해지는 공격을 막으려면 심층 방어 전략이 필요합니다. 보안은 고객과 Microsoft 간의 공동 책임입니다. Microsoft는 이것을 [공동 책임 모델](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/)이라 부릅니다. 아래 그림은 이 책임의 분산을 보여줍니다.

![](media/azure-ddos-best-practices/image2.png)

Azure 고객은 Microsoft의 모범 사례를 검토하고 오류 방지에 대한 설계 및 테스트를 마친 전역 배포 응용 프로그램을 빌드할 수 있습니다.

## <a name="fundamental-best-practices"></a>기본 모범 사례

DDoS 공격은 지금도 있고 점점 늘어나는 추세입니다. 다음 섹션에서는 Azure에서 DDoS 복원 서비스를 빌드하기 위한 규범적 지침을 제공합니다.

### <a name="design-for-security"></a>보안을 위해 설계

고객은 디자인 및 구현에서 배포 및 작업까지 응용 프로그램의 전체 수명 주기에서 보안을 최우선으로 고려해야 합니다. 응용 프로그램에는 과도하게 많은 리소스를 사용하여 결국 서비스 중단으로 이어지게 만드는 비교적 적은 볼륨의 작성된 요청을 허용하는 버그가 있을 수 있습니다. Microsoft Azure에서 실행되는 서비스를 보호하려면 고객은 자신의 응용 프로그램 아키텍처를 정확하게 이해하고 [소프트웨어 품질의 5 가지 핵심 요소](https://docs.microsoft.com/azure/architecture/guide/pillars)에 집중해야 합니다.
고객은 일반적인 트래픽 볼륨, 응용 프로그램과 다른 응용 프로그램 간의 연결 모델, 공용 인터넷에 노출되는 서비스 엔드포인트를 알아야 합니다.

또한 응용 프로그램 자체를 대상으로 하는 서비스 거부 공격을 처리할 수 있도록 응용 프로그램의 복원력을 높이는 것이 매우 중요합니다. [SDL(Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx)부터 보안 및 개인 정보 보호 기능이 Azure 플랫폼에 바로 탑재됩니다. SDL은 모든 개발 단계에서 보안 문제를 해결하고 Azure를 지속적으로 업데이트하여 더욱 안전하게 보호합니다.

### <a name="design-for-scalability"></a>확장성을 위한 디자인

확장성은 증가된 부하를 처리하는 시스템의 기능입니다. 고객은 증폭되는 부하, 특히 DDoS 공격 시 증폭되는 부하 수요를 충족할 수 있도록 [수평으로 확장 가능한](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) 응용 프로그램을 설계해야 합니다. 응용 프로그램이 서비스의 단일 인스턴스에 종속된 경우 단일 실패 지점이 생깁니다. 여러 인스턴스를 프로비전하면 복원력 및 확장성이 모두 개선됩니다.

[Azure App Service](../app-service/app-service-value-prop-what-is.md)의 경우 여러 인스턴스를 제공하는 [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)을 선택합니다. Azure Cloud Services의 경우 각각의 역할을 [여러 인스턴스](../cloud-services/cloud-services-choose-me.md)를 사용하도록 구성합니다. [Azure Virtual Machines(VM)](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)의 경우, VM 아키텍처가 둘 이상의 VM을 포함하는지 그리고 각각의 VM이 [가용성 집합](../virtual-machines/virtual-machines-windows-manage-availability.md)에 포함되는지 확인합니다. 자동 크기 조정 기능을 위한 [가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)을 사용하는 것이 좋습니다.

### <a name="defense-in-depth"></a>심층 방어

심층 방어의 개념은 다양한 방어 전략을 통해 위험을 관리하는 것입니다. 응용 프로그램에서 보안 방어를 계층화하면 공격이 성공할 가능성이 줄어듭니다. 고객은 Azure 플랫폼의 기본 제공 기능을 통해 안전한 응용 프로그램 디자인을 구현하는 것이 좋습니다.

예를 들어 공격 위험이 증가하면 응용 프로그램의 크기 또는 노출 영역도 커집니다. 부하 분산 장치([ALB](../load-balancer/load-balancer-get-started-internet-portal.md)/[App Gateway](../application-gateway/application-gateway-create-probe-portal.md))에 필요 없는 노출된 IP 주소 공간 및 수신 대기 포트를 종료하는 허용 목록을 통해 또는 [NSG(네트워크 보안 그룹)](../virtual-network/virtual-networks-nsg.md)를 통해 노출 영역을 줄이는 것이 좋습니다.
[서비스 태그](/virtual-network/security-overview.md) 및 [응용 프로그램 보안 그룹](/virtual-network/security-overview.md)을 사용하여 보안 규칙 만들기의 복잡성을 최소화하고 응용 프로그램 구조의 기본 확장으로 네트워크 보안을 구성할 수 있습니다.

고객은 가능하면 [가상 네트워크](../virtual-network/virtual-networks-overview.md)에 Azure 서비스를 배포해야 합니다. 이렇게 하면 서비스 리소스가 비공개 IP 주소를 통해 통신할 수 있습니다. 가상 네트워크의 Azure 서비스 트래픽은 공용 IP 주소를 원본 IP 주소로 사용합니다. [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하던 기존 방식에서 서비스 트래픽이 가상 네트워크의 Azure 서비스에 액세스할 때 가상 네트워크 비공개 주소를 원본 IP 주소로 사용하도록 전환됩니다.

종종 고객의 온-프레미스 리소스가 Azure의 리소스와 함께 공격당하는 모습을 볼 수 있습니다. 온-프레미스 환경을 Azure에 연결하는 경우 공용 인터넷에 노출되는 온-프레미스 리소스를 최소화하는 것이 좋습니다. Azure에서 잘 알려진 공용 엔터티를 배포하여 Azure의 크기 조정 및 고급 DDoS Protection 기능을 사용할 수 있습니다. 이러한 공개적으로 액세스 가능한 엔터티는 종종 DDoS 공격의 대상이 되므로 Azure에 배치하면 온-프레미스 리소스에 미치는 영향을 줄일 수 있습니다.

## <a name="azure-ddos-protection"></a>Azure DDoS 보호

Azure는 네트워크 공격(레이어 3 및 4)으로부터 보호하는 DDoS 기본 보호와 DDoS 표준 보호라는 두 가지 DDoS 서비스를 제공합니다. 

### <a name="azure-ddos-basic-protection"></a>Azure DDoS 기본 보호

기본 보호는 기본적으로 추가 비용 없이 Azure에 통합됩니다. Azure 전역 배포 네트워크의 전체 크기 및 용량은 항상 작동하는 트래픽 모니터링 및 실시간 위험 완화를 통해 일반적인 네트워크 레이어 공격을 방어합니다. DDoS Basic 보호는 사용자 구성 또는 응용 프로그램 변경이 필요 없습니다. Azure DNS 같은 PaaS 서비스를 포함한 모든 Azure 서비스는 DDoS Basic Protection을 통해 보호됩니다.

![](media/azure-ddos-best-practices/image3.png)

Azure의 기본 DDoS 보호는 소프트웨어 및 하드웨어 구성 요소로 구성됩니다. 소프트웨어 제어 평면은 공격 트래픽을 분석 및 제거하는 하드웨어 어플라이언스를 통해 언제, 어디서, 어떤 종류의 트래픽을 조정해야 하는지 결정합니다. 제어 평면은 인프라 수준의 DDoS Protection *정책*에 따라 이 결정을 내리며, 이 정책은 정적으로 설정되고 모든 Azure 고객에게 보편적으로 적용됩니다.

예를 들어 DDoS Protection 정책은 언제 보호를 *트리거*(즉, 테넌트의 트래픽을 스크러빙 어플라이언스를 통해 라우팅)해야 하는지, 그에 따라 스크러빙 어플라이언스로 어떻게 공격을 *완화*해야 하는지 지정합니다.

Azure DDoS Protection Basic 서비스는 인프라 보호와 Azure 플랫폼 보호를 목표로 합니다. 다중 테넌트 환경의 여러 고객에게 영향을 줄 수 있는 매우 중요한 속도를 초과하는 경우 트래픽을 완화합니다. 경고 또는 고객별로 사용자 지정된 정책을 제공하지 않습니다.

### <a name="azure-ddos-standard-protection"></a>Azure DDoS 표준 보호

표준 보호는 향상된 DDoS 완화 기능을 제공하며 가상 네트워크의 특정 Azure 리소스를 보호하도록 자동으로 조정됩니다. 보호는 새 가상 네트워크 또는 기존 가상 네트워크에서 간단하게 설정할 수 있으며 응용 프로그램 또는 리소스를 변경할 필요가 없습니다. 로깅, 경고 및 원격 분석을 포함하여 기본 서비스에 비해 여러 가지 장점이 있습니다. 아래에는 Azure DDoS Protection Standard 서비스의 주요 차별화 요소가 설명되어 있습니다.

#### <a name="adaptive-realtime-tuning"></a>적응형 실시간 조정

Azure DDoS Protection Basic 서비스는 고객을 보호하는 데 도움이 되지만 고객이 다른 고객에 영향을 주지 않게 막는 역할만 수행합니다. 예를 들어 인프라 수준 DDoS 보호 정책의 *트리거 비율*보다 작고 합법적인 수신 트래픽의 일반 볼륨에 대해 서비스가 프로비전되면 해당 고객의 리소스에 대한 DDoS 공격을 알아차리지 못할 수 있습니다. 뿐만 아니라 최근 들어 공격 및 응용 프로그램별 테넌트 동작이 더욱 복잡해지면서(예: 다중 벡터 DDoS) 고객별로 사용자 지정된 보호 정책의 필요성이 대두되었습니다.
이 요구 사항은 다음과 같은 두 가지 인사이트를 사용하여 해결할 수 있습니다.

1. 고객별(IP별) 레이어 3/4 트래픽 패턴 자동 학습
2. Azure의 크기 조정을 통해 트래픽의 상당 부분을 흡수할 수 있다는 전제 하에 거짓 긍정을 최소화

![](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring--alerting"></a>DDoS 보호 원격 분석, 모니터링 및 경고

DDoS Protection Standard에서는 DDoS 공격이 진행되는 동안 [Azure Monitor](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)를 통해 풍부한 원격 분석이 노출됩니다. DDoS Protection에서 노출하는 Azure Monitor 메트릭에 대한 경고를 구성할 수 있습니다. Azure Monitor 진단 인터페이스를 통해 로깅을 Splunk(Azure Event Hubs), Azure Log Analytics 및 Azure Storage와 통합하면 고급 분석을 사용할 수 있습니다.

##### <a name="ddos-mitigation-policies"></a>DDoS 완화 정책

Azure Portal에서 **모니터** \> **메트릭**을 클릭합니다. **메트릭** 화면에서 리소스 그룹, 공용 IP 주소의 리소스 유형, Azure 공용 IP 주소를 선택합니다. 사용 가능한 메트릭 창에 DDoS 메트릭이 표시됩니다.

DDoS Protection Standard는 DDoS가 설정된 VNET에서 보호되는 리소스의 각 공용 IP에 대한 **세 가지 자동 조정 완화 정책(TCP SYN, TCP 및 UDP)** 을 적용합니다. **'DDoS 완화를 트리거할 인바운드 패킷'** 메트릭을 선택하여 정책 임계값을 볼 수 있습니다.

![](media/azure-ddos-best-practices/image7.png)

정책 임계값은 기계 학습 기반의 네트워크 트래픽 프로파일링을 통해 자동으로 구성됩니다. 정책 임계값을 초과하면 공격을 받고 있는 IP 주소에 대해 DDoS 완화가 발생합니다.

##### <a name="under-ddos-attack"></a>DDoS 공격 진행 중

공용 IP 주소가 공격을 받으면 공격 트래픽에 대한 완화를 수행할 때 메트릭 'DDoS 공격 진행 여부' 값이 1로 전환됩니다.

![](media/azure-ddos-best-practices/image8.png)

공용 IP 주소에서 활성 DDoS 완화가 수행되면 알림을 제공하도록 이 메트릭에 경고를 구성하는 것이 좋습니다.

자세한 내용은 [Azure Portal을 사용하여 Azure DDoS Protection Standard 관리](../virtual-network/ddos-protection-manage-portal.md)를 참조하세요.

#### <a name="resource-attacks"></a>리소스 공격

응용 프로그램 계층에서 이루어지는 리소스 공격과 관련하여, 고객은 웹 응용 프로그램 보호를 위해 WAF(웹 응용 프로그램 방화벽)를 구성해야 합니다. WAF는 인바운드 웹 트래픽을 검사하여 SQL 주입, 사이트 간 스크립팅, DDoS 및 기타 레이어 7 공격을 차단합니다. Azure는 [WAF(웹 응용 프로그램 방화벽)](../application-gateway/application-gateway-web-application-firewall-overview.md)는 일반적인 악용 및 취약점으로부터 웹 응용 프로그램을 중앙 집중식으로 보호하는 Application Gateway 기능으로 WAF를 제공합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)에는 Azure 파트너가 제공하는 다양한 WAF가 있으며, 그 중에는 고객의 요구 사항에 보다 적합한 서비스가 있을 수도 있습니다.

웹 응용 프로그램 방화벽조차 대규모 공격 및 상태 고갈 공격에 취약합니다. 대규모 공격 및 프로토콜 공격을 방지할 수 있도록 WAF 가상 네트워크에서 DDoS Standard 보호를 켜는 것이 좋습니다. 자세한 내용은 참조 아키텍처 섹션을 참조하세요.

### <a name="protection-planning"></a>보호 계획

계획 및 준비는 DDoS 공격을 받는 동안 시스템이 어떻게 작동하는지 이해하는 데 매우 중요합니다. 계획 및 준비는 사고 관리 응답 계획을 디자인하는 데도 도움이 됩니다.

고객은 인터넷 연결 엔드포인트의 가상 네트워크에서 DDoS Protection Standard를 사용하도록 설정되었는지 확인해야 합니다. DDoS 경고를 구성하면 인프라에 대한 잠재적 공격을 지속적으로 감시하는 데 도움이 됩니다. 고객은 응용 프로그램을 독립적으로 모니터링해야 합니다. 고객은 응용 프로그램의 정상 동작을 이해해야 합니다. DDoS 공격을 받는 동안 응용 프로그램이 예상대로 작동하지 않을 경우 조치를 수행해야 합니다.

#### <a name="ddos-attacks-orchestration"></a>DDoS 공격 오케스트레이션

공격이 발생하지 않더라도 주기적으로 시뮬레이션을 수행하여 서비스가 공격에 대응하는 방식에 대한 가정을 테스트하는 것이 좋습니다. 테스트하는 동안 서비스 또는 응용 프로그램이 계속해서 예상대로 작동하는지, 최종 사용자 환경이 중단되지 않는지 검사해야 합니다. 기술과 프로세스 간의 격차를 식별하여 DDoS 대응 전략에 통합해야 합니다. 일반적으로 프로덕션 환경에 미치는 영향을 최소화하기 위해 스테이징 환경에서 또는 피크 시간대 이외의 시간에 테스트를 수행하는 것이 좋습니다.

Microsoft는 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud)와 협력하여 고객이 DDoS Protection이 활성화된 공용 엔드포인트에 대한 트래픽을 생성하여 시뮬레이션을 수행할 수 있는 인터페이스를 개발했습니다. [BreakPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) 시뮬레이션을 사용하면 다음과 같은 일을 할 수 있습니다.

- Microsoft Azure DDoS Protection이 DDoS 공격으로부터 Azure 리소스를 보호하는 방법 확인

- DDoS 공격을 받는 동안 사고 대응 프로세스 최적화

- DDoS 규정 준수 문서화

- 네트워크 보안 팀 교육

사이버 보안은 지속적인 방어 체계 혁신을 요구하는 치열한 전투입니다. Azure의 DDoS Standard 보호는 고객에게 점점 복잡해지는 DDoS 공격을 완화하는 효과적인 솔루션을 제공하는 첨단 서비스입니다.

## <a name="components-of-a-ddos-response-strategy"></a>DDoS 대응 전략의 구성 요소

DDoS 공격이 Azure 리소스를 대상으로 하는 경우 대부분은 최종 사용자의 개입이 최소한으로 요구됩니다. 그래도 여전히 DDoS 완화를 조직의 사고 대응 전략에 통합하면 비즈니스 연속성에 미치는 영향을 최소화할 수 있습니다.

### <a name="microsoft-threat-intelligence"></a>Microsoft 위협 인텔리전스

Microsoft는 Microsoft의 온라인 서비스, Microsoft 파트너 및 인터넷 보안 커뮤니티와의 관계를 지원하는 확장된 보안 커뮤니티의 집단 지식을 사용하는 방대한 위협 인텔리전스 네트워크를 보유하고 있습니다. 중요한 인프라 공급자로서 Microsoft는 위협에 대한 경고를 조기에 수신하고, 다른 Microsoft 온라인 서비스 및 Microsoft의 글로벌 고객 기반으로부터 배운 위협 인텔리전스를 사용합니다. Microsoft의 모든 위협 인텔리전스는 다시 Azure DDoS Protection 제품에 통합됩니다.

이 외에도 Microsoft의 DCU(Digital Crimes Unit)는 봇넷, 명령 및 컨트롤의 일반적인 소스 그리고 DDoS 공격에 대한 공격 전략을 수행합니다.

### <a name="perform-a-risk-evaluation-of-your-azure-resources"></a>Azure 리소스의 위험 평가 수행

반드시 DDoS 공격의 위험 범위를 지속적으로 파악해야 합니다. 고객은 공개적으로 제공되는 새 Azure 리소스 중 보호가 필요한 리소스는 무엇인지 주기적으로 확인해야 합니다. 서비스에 단일 실패 지점이 있습니까? 서비스를 격리하여 공격의 영향을 제한하면서도 유효한 고객에게 서비스를 계속 제공하려면 어떻게 해야 할까요? DDoS Protection Standard를 사용하도록 설정해야 하는데 설정하지 않은 가상 네트워크 있습니까? 내 서비스가 여러 지역에서 활성화/장애 조치(failover)와 함께 활성화됩니까?

### <a name="customer-ddos-response-team"></a>고객 DDoS 대응 팀

공격에 효과적이고 신속하게 대응하기 위한 핵심은 DDoS 대응 팀을 만드는 것입니다. 고객은 조직 내에서 계획 및 실행을 감독할 다양한 연락 담당자를 식별해야 합니다. DDoS 대응 팀은 Azure DDoS Protection Standard 서비스를 완벽하게 이해하고 있어야 하며, 필요할 때 Microsoft 지원을 비롯한 다양한 내부 및 외부 고객과 협력할 수 있도록 공격을 능숙하게 파악하고 완화할 수 있어야 합니다.

크기 조정 테스트를 포함한 DDoS 대응 팀 계획 및 시뮬레이션 연습을 서비스 가용성 및 연속성 계획의 일반 과정으로 통합하는 것이 좋습니다. 

### <a name="during-an-attack"></a>공격 진행 중

DDoS Protection Standard는 사용자 개입 없이 DDoS 공격을 식별하여 완화합니다. 보호되는 공용 IP에 대한 활성 완화가 발생할 때 알림을 받으려면 "DDoS 공격 진행 여부" 메트릭에서 [경고를 구성](../virtual-network/ddos-protection-manage-portal.md)합니다. 경고를 검토하고 다른 DDoS 메트릭에 필요한 대로 만들어서 공격의 규모, 손실되는 트래픽 등을 이해할 수 있습니다.

#### <a name="when-to-contact-microsoft-support"></a>Microsoft 지원에 문의해야 하는 경우

- DDoS 공격이 진행되는 동안 보호되는 리소스의 성능이 심각하게 저하되거나 리소스를 사용할 수 없게 되는 경우.

- DDoS 보호 서비스가 예상대로 작동하지 않는다고 판단되는 경우. DDoS Protection 서비스는 아래 기준을 충족할 때에만 시작됩니다.

    - 'DDoS 완화를 트리거하는 정책(TCP/TCP SYN/UDP)' 메트릭 값이 보호되는 공용 IP 리소스에서 받은 트래픽보다 낮은 경우.

- 네트워크 트래픽이 크게 증가하는 계획된 바이럴 이벤트가 발생할 것이라는 사실을 알고 있는 경우.

- 작업자가 리소스에 대한 DDoS 공격을 시작하겠다고 위협한 경우.

중요한 비즈니스에 영향을 주는 문제인 경우 심각도-A [지원 티켓](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 만듭니다.

### <a name="post-attack-steps"></a>공격 후 단계

공격 후에는 항상 사후 조치를 수행하고 필요에 따라 DDoS 대응 전략을 다시 조정하는 것이 좋습니다. 고려 사항은 다음과 같습니다.

- 확장 가능한 아키텍처가 부족하여 서비스 또는 최종 사용자 환경이 중단되었습니까?

- 가장 피해가 컸던 응용 프로그램 또는 서비스는 무엇입니까?

- DDoS 대응 전략이 얼마나 효과적이었으며 어떻게 개선해야 할까요?

DDoS 공격을 받고 있다고 의심 되는 경우 일반 Azure 지원 채널을 통해 에스컬레이션하세요.

## <a name="ddos-protection-reference-architectures"></a>DDoS 보호 참조 아키텍처

DDoS Protection Standard는 [가상 네트워크에 배포되는 서비스](../virtual-network/virtual-network-for-azure-services.md)를 위해 설계되었습니다. 다른 서비스에는 기본 DDoS Basic Protection이 적용됩니다. 아래에 설명된 특정 참조 아키텍처는 시나리오별로 정렬되어 있으며, 아키텍처 패턴이 함께 그룹화되어 있습니다.

### <a name="virtual-machine-windowslinux-workloads"></a>가상 머신(Windows/Linux) 워크로드

#### <a name="application-running-on-load-balanced-vms"></a>부하 분산된 VM에서 실행되는 응용 프로그램

이 참조 아키텍처에서는 확장성과 가용성을 개선하기 위해 부하 분산 장치 뒤에서 확장 집합에 포함된 복수의 Windows VM(가상 머신)을 실행하는 검증된 모범 사례를 보여줍니다. 이 아키텍처는 웹 서버 같은 상태 비저장 워크로드에 사용할 수 있습니다.

![](media/azure-ddos-best-practices/image9.png)

이 아키텍처에서 워크로드는 여러 VM 인스턴스에 배포됩니다. 공용 IP 주소는 한 개가 사용되며, 부하 분산 장치에 의해 인터넷 트래픽이 여러 VM으로 분산됩니다. DDoS Protection Standard는 공용 IP가 연결된 Azure(인터넷) 부하 분산 장치의 가상 네트워크에서 설정됩니다.

부하 분산 장치는 수신되는 인터넷 요청을 여러 VM 인스턴스로 분산합니다. VM 확장 집합을 사용하면 여러 VM을 수동 확장 또는 축소하거나 사전 정의된 규칙을 바탕으로 자동 확장 또는 축소할 수 있습니다. 이는 리소스가 DDoS 공격을 받고 있는 경우에 중요합니다. 이 참조 아키텍처에 대한 자세한 내용 이 [문서](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)를 참조하세요.

#### <a name="applications-running-on-windows-n-tier"></a>Windows N 계층에서 실행되는 응용 프로그램

N 계층 아키텍처를 구현하는 방법은 여러 가지가 있습니다. 이 다이어그램에서는 일반적인 3계층 웹 응용 프로그램을 보여줍니다. 이 아키텍처는 [부하가 분산된 VM을 실행하여 확장성 및 가용성 확보](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)를 기반으로 합니다. 웹 및 비즈니스 계층은 부하 분산된 VM을 사용합니다.

![](media/azure-ddos-best-practices/image10.png)

위의 아키텍처에서 DDoS Protection Standard는 가상 네트워크에서 설정됩니다. 가상 네트워크의 모든 공용 IP는 레이어3/레이어4 DDoS Protection의 보호를 받습니다. 레이어 7 보호를 받으려면 WAF SKU에 Application Gateway를 배포해야 합니다. 이 참조 아키텍처에 대한 자세한 내용 이 [문서](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)를 참조하세요.

#### <a name="paas-web-application"></a>PaaS 웹 응용 프로그램

이 참조 아키텍처는 단일 지역에서 Azure App Service 응용 프로그램을 실행하는 방법을 보여줍니다. 이 아키텍처는 [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) 및 [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)를 사용하는 웹 응용 프로그램에 관한 일련의 검증된 사례를 보여줍니다.
대기 지역은 장애 조치(failover) 시나리오에 대해 설정되어 있습니다.

![](media/azure-ddos-best-practices/image11.png)

Traffic Manager는 들어오는 요청을 한 지역의 Application Gateway로 라우팅합니다. 정상 작동 중에는 요청을 활성 지역의 Application Gateway로 라우팅합니다. 해당 지역을 사용할 수 없게 되면 Traffic Manager가 대기 지역의 Application Gateway로 장애 조치(failover) 됩니다.

웹 응용 프로그램을 대상으로 하는 인터넷의 모든 트래픽이 Traffic Manager를 통해 [Application Gateway 공용 IP 주소](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)로 라우팅됩니다. 이 시나리오에서 앱 서비스(웹앱) 그 자체는 외부와 직접 연결되지 않고 Application Gateway를 통해 보호됩니다. 레이어 7(HTTP/HTTPS/웹 소켓) 공격을 방지할 수 있도록 Application Gateway WAF SKU(금지 모드)를 구성하는 것이 좋습니다. 또한 웹앱은 [Application Gateway IP 주소에서 오는 트래픽만 수락](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)하도록 구성됩니다.

이 참조 아키텍처에 대한 자세한 내용 이 [문서](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)를 참조하세요.

### <a name="mitigation-for-non-web-paas-services"></a>비 웹 PaaS 서비스에 대한 완화

#### <a name="hdinsight-on-azure"></a>Azure의 HDInsight

이 참조 아키텍처는 Azure의 [HDInsight 클러스터](https://docs.microsoft.com/azure/hdinsight/)에 대해 DDoS Protection Standard를 구성하는 방법을 보여줍니다. HDInsight 클러스터가 가상 네트워크에 연결되어 있고 해당 가상 네트워크에서 DDoS Protection이 사용하도록 설정되어 있는지 확인해야 합니다.

![](media/azure-ddos-best-practices/image12.png)

![](media/azure-ddos-best-practices/image13.png)

이 아키텍처에서 인터넷의 HDInsight 클러스터를 대상으로 하는 트래픽은 HDInsight 게이트웨이 부하 분산 장치와 연결된 공용 IP 주소로 라우팅됩니다. 그러면 게이트웨이 부하 분산 장치가 트래픽을 헤드 노드 또는 작업자 노드로 직접 보냅니다. HDInsight 가상 네트워크에서 DDoS Protection Standard를 사용하도록 설정된 경우 가상 네트워크의 모든 공용 IP가 레이어3/레이어4 DDoS Protection의 보호를 받습니다. 위의 참조 아키텍처를 N 계층/다중 지역 참조 아키텍처와 결합할 수 있습니다.

이 참조 아키텍처에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 Azure HDInsight 확장](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) 설명서를 참조하세요.

### <a name="azure-api-management"></a>Azure API Management

이 참조 아키텍처는 API를 조직 외부 고객에게 게시하는 [API Management](../api-management/api-management-key-concepts.md)의 공용 엔드포인트를 보호합니다. DDoS 보호를 사용할 수 있도록 APIM을 외부 가상 네트워크에 배포해야 합니다.

![](media/azure-ddos-best-practices/image15.png)

외부 가상 네트워크를 구성하면 공용 부하 분산 장치를 통해 공용 인터넷에서 API Management 게이트웨이 및 개발자 포털에 액세스할 수 있습니다. 이 아키텍처에서 DDoS Protection Standard는 APIM 가상 네트워크 외부 가상 네트워크에 설정됩니다. 트래픽은 인터넷에서 레이어3/레이어4 네트워크 공격으로부터 보호되는 APIM의 공용 IP 주소로 라우팅됩니다. 레이어 7 HTTP/HTTPs 공격을 방어하려면 WAF 모드에서 Application Gateway를 구성하면 됩니다.

가상 네트워크에 배포되고 DDoS Protection Standard에 대해 구성할 수 있는 추가 서비스 목록은 [여기](../virtual-network/virtual-network-for-azure-services.md)서 관리됩니다. DDoS Protection Standard는 Azure Resource Manager 리소스만 지원합니다. *공용 IP를 사용하여 VNET에 주입된 ASE(응용 프로그램 서비스 환경)를 배포하는 기능은 기본적으로 지원되지 않습니다.* ASE 환경 보호에 대한 자세한 내용은 이 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure DDoS Protection 제품 페이지](https://azure.microsoft.com/services/ddos-protection/)

* [Azure DDoS Protection 블로그](http://aka.ms/ddosblog)

* [Azure DDoS Protection 설명서](../virtual-network/ddos-protection-overview.md)

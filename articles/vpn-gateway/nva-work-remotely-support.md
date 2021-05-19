---
title: '원격으로 작업: 원격 작업에 대한 NVA(네트워크 가상 어플라이언스) 고려 사항'
titleSuffix: Azure VPN Gateway
description: 코로나19 팬데믹 기간 동안 Azure에서 NVA(네트워크 가상 어플라이언스)를 사용할 때 고려해야 할 사항에 대해 알아봅니다.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: fd0c1f836abc60a53168098cc1bcce72ded2a120
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108229201"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>원격으로 작업: 원격 작업에 대한 NVA(네트워크 가상 어플라이언스) 고려 사항

>[!NOTE]
>이 문서에서는 네트워크 가상 어플라이언스, Azure, Microsoft 네트워크 및 Azure 파트너 에코시스템을 활용하여 원격으로 작업하고 코로나19 위기로 인해 직면한 네트워크 문제를 완화할 수 있는 방법을 설명합니다.
>

Azure 고객의 일부는 Azure Marketplace에서 타사 NVA(네트워크 가상 어플라이언스)를 활용하여 코로나19 유행 기간 동안 집에서 근무하는 직원에게 지점 및 사이트 간 VPN과 같은 중요한 서비스를 제공합니다. 이 문서에서는 Azure에서 NVA를 활용하여 원격 액세스 솔루션을 제공하는 경우 고려해야 할 몇 가지 개략적인 지침을 간략하게 설명합니다.

## <a name="nva-performance-considerations"></a>NVA 성능 고려 사항

Azure Marketplace의 모든 주요 NVA 공급업체에는 솔루션을 배포할 때 사용할 VM 크기 및 인스턴스 수에 대한 권장 사항이 있어야 합니다.  거의 모든 NVA 공급업체는 지정된 지역에서 사용할 수 있는 모든 크기를 선택할 수 있지만, 공급업체가 Azure에서 성능 테스트를 수행한 VM 크기를 권장하고 있으므로 Azure VM 인스턴스 크기에 대한 공급업체 권장 사항을 따르는 것이 매우 중요합니다.  

### <a name="consider-the-following"></a>다음을 고려해 보세요.

- **용량 및 동시 사용자 수** - 연결된 각 사용자가 암호화된(IPSEC 또는 SSL VPN) 터널 하나를 만들 때 지점 및 사이트 간 VPN 사용자에게 특히 중요합니다.  
- **집계 처리량** - 원격 액세스를 제공하는 데 필요한 사용자 수를 수용하는 데 필요한 집계 대역폭입니다.
- **필요한 VM 크기** - 항상 NVA 공급업체에 의해 권장되는 VM 크기를 사용해야 합니다.  지점 및 사이트 간 VPN의 경우 많은 동시 사용자 연결이 있으면 [Dv2 및 DSv2 시리즈](../virtual-machines/dv2-dsv2-series.md "Dv2 및 DSv2 시리즈") VM과 같이 큰 VM을 사용해야 합니다. 이러한 VM은 더 많은 vCPU를 보유하고 있으며 더 많은 동시 VPN 세션을 처리할 수 있습니다.  더 많은 가상 코어를 포함하는 것 외에도 Azure의 큰 VM은 작은 VM보다 더 많은 집계 대역폭 용량을 갖습니다.
    > **중요:** 각 공급업체는 리소스를 다르게 활용합니다.  예상 사용자 부하를 수용하기 위해 사용해야 하는 인스턴스 크기를 명확하게 알 수 없는 경우 소프트웨어 공급업체에 직접 문의하여 권장 사항을 확인해야 합니다.
- **인스턴스 수** - 많은 수의 사용자 및 연결이 필요한 경우 NVA 인스턴스 크기를 스케일 업하는 데에서 오는 효과는 제한적입니다.  여러 VM 인스턴스를 배포하는 것을 고려합니다.
- **IPSec VPN과 SSL VPN** -일반 IPSec VPN 구현에서는 SSL VPN 구현보다 성능이 뛰어납니다.  
- **라이선스** - NVA 솔루션에 대해 구매한 소프트웨어 라이선스가 코로나19 유행 중에 발생할 수 있는 갑작스러운 증가를 처리할 수 있는지 확인합니다.  많은 NVA 라이선스 프로그램은 솔루션이 지원하는 연결 또는 대역폭의 수를 제한합니다.
- **가속화된 네트워킹** - 가속화된 네트워킹을 지원하는 NVA 솔루션을 고려합니다.  가속화된 네트워킹을 사용하면 VM에 대한 단일 루트 I/O 가상화(SR-IOV)를 구현할 수 있어 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로는 데이터 경로에서 호스트를 우회함으로써 대기 시간, 지터 및 CPU 사용률을 줄이므로 지원되는 VM 유형에서 가장 까다로운 네트워크 워크로드에 사용합니다. 가속 네트워킹은 가장 일반적인 용도로 2개 이상의 vCPU가 포함된 계산 최적화 인스턴스 크기에서 지원됩니다.

## <a name="monitoring-resources"></a>리소스 모니터링

각 NVA 솔루션에는 NVA의 성능을 모니터링하기 위한 자체 도구와 리소스가 있습니다.  성능 제한 사항을 이해하고 NVA가 제한 용량에 근접하거나 도달했을 때 감지할 수 있는지 확인하려면 공급업체 설명서를 참조하세요.  이 외에도 Azure Monitor Network Insights를 확인하고 다음과 같은 네트워크 가상 어플라이언스에 대한 기본 성능 정보를 확인할 수 있습니다.

- CPU 사용률
- 네트워크 인
- 네트워크 아웃
- 인바운드 흐름
- 아웃바운드 흐름

## <a name="next-steps"></a>다음 단계

대부분의 주요 NVA 파트너는 코로나19 유행 기간 동안 예기치 않은 증가에 따른 확장에 대한 지침을 게시했습니다. 파트너 리소스에 대한 몇 가지 유용한 링크는 다음과 같습니다.

[Barracuda, 코로나19 상황에서 데이터를 보호하면서 재택 근무 가능](https://www.barracuda.com/covid-19/work-from-home "코로나19 상황에서 데이터를 보호하면서 재택 근무 가능")

[Check Point, 코로나 바이러스 유행 기간 동안 안전한 원격 작업 환경 구현](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "코로나 바이러스 유행 기간 동안 안전한 원격 작업 환경 구현")

[Cisco AnyConnect, 코로나19 대비를 위한 구현 및 성능/크기 조정 참조](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Cisco AnyConnect, 코로나19 대비를 위한 구현 및 성능/크기 조정 참조")

[Citrix, 코로나19 응답 지원 센터](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix 코로나19 응답 지원 센터")

[F5, 원격 작업자의 급격한 증가를 해결하기 위한 지침](https://www.f5.com/business-continuity "F5, 원격 작업자의 급격한 증가를 해결하기 위한 지침")

[Fortinet, 고객 및 파트너를 위한 코로나19 관련 사항 업데이트](https://www.fortinet.com/covid-19.html "고객 및 파트너를 위한 코로나19 관련 사항 업데이트")

[Palo Alto Networks, 코로나19 응답 센터](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks, 코로나19 응답 센터")

[Kemp, 비즈니스 연속성을 위해 원격 작업 및 Always-On 앱 환경 사용](https://kemptechnologies.com/remote-work-always-on-application-experience-business-continuity/ "Kemp, 비즈니스 연속성을 위해 원격 작업 및 Always-On 앱 환경 사용")


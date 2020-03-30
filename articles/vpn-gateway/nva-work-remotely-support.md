---
title: '원격 작업: 원격 작업에 대한 NVA(네트워크 가상 어플라이언스) 고려 사항 | Azure VPN 게이트웨이'
description: 이 문서에서는 COVID-19 전염병 동안 Azure에서 NAS(네트워크 가상 어플라이언스)로 작업하는 데 고려해야 할 사항을 이해하는 데 도움이 됩니다.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/21/2020
ms.author: scottnap
ms.openlocfilehash: 8a22e8f0bde2d13b4055566d96680fadc2db6e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337103"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>원격 작업: 원격 작업에 대한 NVA(네트워크 가상 어플라이언스) 고려 사항

>[!NOTE]
>이 문서에서는 네트워크 가상 어플라이언스, Azure, Microsoft 네트워크 및 Azure 파트너 에코시스템을 활용하여 원격으로 작업하고 COVID-19 위기로 인해 직면하고 있는 네트워크 문제를 완화하는 방법에 대해 설명합니다.
>

일부 Azure 고객은 Azure Marketplace의 타사 네트워크 가상 어플라이언스(NAS)를 사용하여 COVID-19 전염병 기간 동안 집에서 일하는 직원을 위해 Point-to-site VPN과 같은 중요한 서비스를 제공합니다. 이 문서에서는 Azure의 NAS를 활용하여 원격 액세스 솔루션을 제공할 때 고려해야 할 몇 가지 고급 지침을 간략하게 설명합니다.

## <a name="nva-performance-considerations"></a>NVA 성능 고려 사항

Azure Marketplace의 모든 주요 NVA 공급업체는 솔루션을 배포할 때 사용할 VM 크기 및 인스턴스 수에 대한 권장 사항이 있어야 합니다.  거의 모든 NVA 공급업체에서 특정 지역에서 사용할 수 있는 모든 크기를 선택할 수 있지만 이러한 권장 사항은 공급업체가 성능을 수행한 VM 크기이기 때문에 Azure VM 인스턴스 크기에 대한 공급업체 권장 사항을 따르는 것이 매우 중요합니다. Azure에서 테스트합니다.  

### <a name="consider-the-following"></a>다음을 고려하십시오.

- **동시 사용자 수** - 연결된 각 사용자가 하나의 암호화된(IPSec 또는 SSL VPN) 터널을 생성하기 때문에 이 숫자는 Point-To-Site VPN 사용자에게 특히 중요합니다.  
- **집계 처리량** - 원격 액세스를 제공하는 데 필요한 사용자 수를 수용하는 데 필요한 집계 대역폭은 무엇입니까?
- **필요한 VM 크기** - NVA 공급업체에서 권장하는 VM 크기를 항상 사용해야 합니다.  지점 간 VPN의 경우 동시 사용자 연결이 많은 경우 [Dv2 및 DSv2 시리즈 VM과](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Dv2 및 Dsv2 시리즈") 같은 더 큰 VM 크기를 사용해야 합니다. 이러한 VM은 vCPU가 더 많고 더 많은 동시 VPN 세션을 처리할 수 있습니다.  더 많은 가상 코어를 보유하는 것 외에도 Azure의 더 큰 VM 크기는 더 작은 VM 크기보다 더 많은 집계 대역폭 용량을 갖습니다.
    > **중요 사항:** 각 공급업체는 리소스를 다르게 사용합니다.  예상 사용자 부하를 수용하기 위해 사용해야 하는 인스턴스 크기가 명확하지 않은 경우 소프트웨어 공급업체에 직접 문의하여 권장 사항을 요청해야 합니다.
- **인스턴스 수** - 많은 수의 사용자와 연결이 예상되는 경우 NVA 인스턴스 크기를 확장할 수 있는 것에는 제한이 있습니다.  여러 VM 인스턴스를 배포하는 것이 좋습니다.
- **IPSec VPN 대 SSL VPN** - 일반적으로 IPSec VPN 구현은 SSL VPN 구현보다 더 나은 성능을 발휘합니다.  
- **라이선스** - NVA 솔루션용으로 구입한 소프트웨어 라이선스가 COVID-19 전염병 기간 동안 발생할 수 있는 급격한 성장을 포함해야 합니다.  많은 NVA 라이선싱 프로그램은 솔루션이 할 수 있는 연결 또는 대역폭 의 수를 제한합니다.
- **가속 네트워킹** - 가속 네트워킹을 지원하는 NVA 솔루션을 고려하십시오.  가속화된 네트워킹을 사용하면 VM에 대한 단일 루트 I/O 가상화(SR-IOV)를 구현할 수 있어 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로는 데이터 경로에서 호스트를 우회하여 지원되는 VM 유형에서 가장 까다로운 네트워크 워크로드와 함께 사용할 대기 시간, 지터 및 CPU 사용률을 줄입니다. 가속 네트워킹은 두 개 이상의 vCPU를 통해 대부분의 범용 및 컴퓨팅 에 최적화된 인스턴스 크기에서 지원됩니다.

## <a name="monitoring-resources"></a>리소스 모니터링

각 NVA 솔루션에는 NVA의 성능을 모니터링하기 위한 자체 도구와 리소스가 있습니다.  공급업체 설명서를 참조하여 성능 제한을 이해하고 NVA가 용량에 가까워지거나 도달한 시기를 감지할 수 있습니다.  이 외에도 Azure Monitor Network Insights를 살펴보고 다음과 같은 네트워크 가상 어플라이언스에 대한 기본 성능 정보를 볼 수 있습니다.

- CPU Utilization
- 네트워크 인
- 네트워크 아웃
- 인바운드 흐름
- 아웃바운드 흐름

## <a name="next-steps"></a>다음 단계

대부분의 주요 NVA 파트너는 COVID-19 기간 동안 갑작스럽고 예상치 못한 성장에 대한 확장에 대한 지침을 게시했습니다. 다음은 파트너 리소스에 대한 몇 가지 유용한 링크입니다.

[COVID-19 동안 데이터를 보호하면서 바라쿠다 에서 재택 근무 가능](https://www.barracuda.com/covid-19/work-from-home "COVID-19 동안 데이터를 보호하면서 재택 근무 활성화")

[COVID-19 준비를 위한 Cisco AnyConnect 구현 및 성능/스케일링 레퍼런스](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "COVID-19 준비를 위한 Cisco AnyConnect 구현 및 성능/스케일링 레퍼런스")

[시트릭스 COVID-19 대응 지원 센터](https://www.citrix.com/support/covid-19-coronavirus.html "시트릭스 COVID-19 대응 지원 센터")

[원격 작업자의 급격한 증가를 해결하기 위한 F5 지침](https://www.f5.com/business-continuity "원격 작업자의 급격한 증가를 해결하기 위한 F5 지침")

[고객 및 파트너를 위한 Fortinet COVID-19 업데이트](https://www.fortinet.com/covid-19.html "고객 및 파트너를 위한 COVID-19 업데이트")

[팔로 알토 네트웍스 COVID-19 대응 센터](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "팔로 알토 네트웍스 COVID-19 대응 센터")

---
title: '원격 작업: 원격 작업에 대 한 NVA (네트워크 가상 어플라이언스) 고려 사항 | Azure VPN Gateway'
description: 이 문서에서는 COVID-19 전염병 중에 Azure에서 Nva (네트워크 가상 어플라이언스)를 사용할 때 고려해 야 하는 사항을 이해 하는 데 도움을 줍니다.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 4783016e472907392f2d379efa0fed2d90ed21bc
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89595362"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>원격 작업: 원격 작업에 대 한 NVA (네트워크 가상 어플라이언스) 고려 사항

>[!NOTE]
>이 문서에서는 네트워크 가상 어플라이언스, Azure, Microsoft 네트워크 및 Azure 파트너 에코 시스템을 활용 하 여 원격으로 작업 하 고 COVID-19 위기 때문에 직면 하 고 있는 네트워크 문제를 완화 하는 방법을 설명 합니다.
>

일부 Azure 고객은 Azure Marketplace에서 타사 네트워크 가상 어플라이언스 (Nva)를 활용 하 여 COVID-19 epidemic 동안 집에서 근무 하는 직원에 대 한 지점 및 사이트 간 VPN과 같은 중요 한 서비스를 제공 합니다. 이 문서에서는 Azure에서 Nva를 활용 하 여 원격 액세스 솔루션을 제공 하는 경우 고려해 야 할 몇 가지 개략적인 지침을 간략하게 설명 합니다.

## <a name="nva-performance-considerations"></a>NVA 성능 고려 사항

Azure Marketplace의 모든 주요 NVA 공급 업체에는 솔루션을 배포할 때 사용할 VM 크기 및 인스턴스 수에 대 한 권장 사항이 있어야 합니다.  거의 모든 NVA 공급 업체는 지정 된 지역에서 사용할 수 있는 모든 크기를 선택할 수 있지만, Azure VM 인스턴스 크기에 대 한 공급 업체 권장 사항을 따르는 것이 매우 중요 합니다. 이러한 권장 사항은 공급 업체가 Azure에서 성능 테스트를 수행한 VM 크기입니다.  

### <a name="consider-the-following"></a>다음 사항을 고려 하세요.

- **동시 사용자의 용량과 수** -연결 된 각 사용자가 암호화 된 (IPSEC 또는 SSL VPN) 터널 하나를 만들 때 지점 및 사이트 간 VPN 사용자에 게 특히 중요 합니다.  
- **집계 처리량** -원격 액세스를 제공 하는 데 필요한 사용자 수를 수용 하는 데 필요한 집계 대역폭입니다.
- **필요한 vm 크기** -항상 nva 공급 업체에서 권장 하는 vm 크기를 사용 해야 합니다.  지점 및 사이트 간 VPN의 경우 많은 동시 사용자 연결을 사용 하는 경우 [Dv2 및 DSv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Dv2 및 Dsv2 시리즈") vm과 같은 더 큰 VM 크기를 사용 해야 합니다. 이러한 Vm은 더 많은 vCPUs를 보유 하 고 있으며 더 많은 동시 VPN 세션을 처리할 수 있습니다.  더 많은 가상 코어를 포함 하는 것 외에도 Azure의 큰 VM 크기는 더 작은 VM 크기 보다 더 많은 집계 대역폭 용량을 갖습니다.
    > **중요:** 각 공급 업체는 리소스를 다르게 활용 합니다.  예상 사용자 부하를 수용 하기 위해 사용 해야 하는 인스턴스 크기를 명확 하 게 알 수 없는 경우 소프트웨어 공급 업체에 직접 문의 하 여 권장 사항을 확인 해야 합니다.
- **인스턴스 수** -많은 수의 사용자 및 연결이 필요한 경우 nva 인스턴스 크기를 달성할 수 있는 크기에 제한이 있습니다.  여러 VM 인스턴스를 배포 하는 것이 좋습니다.
- **IPSEC vpn VS SSL vpn** -일반 ipsec vpn 구현에서는 SSL vpn 구현 보다 성능이 뛰어납니다.  
- **라이선스** -nva 솔루션에 대해 구매한 소프트웨어 라이선스가 covid-19 epidemic 중에 발생할 수 있는 갑작스러운 성장을 포함 하는지 확인 합니다.  많은 NVA 라이선스 프로그램은 솔루션이 지 원하는 연결 또는 대역폭의 수를 제한 합니다.
- **가속화 된 네트워킹** -가속화 된 네트워킹을 지 원하는 nva 솔루션을 고려 합니다.  가속화된 네트워킹을 사용하면 VM에 대한 단일 루트 I/O 가상화(SR-IOV)를 구현할 수 있어 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로를 사용 하면 지원 되는 VM 유형에 서 가장 까다로운 네트워크 작업에 사용 하기 위해 데이터 경로에서 호스트를 우회 하 여 대기 시간, 지터 및 CPU 사용률을 줄입니다. 가속화 된 네트워킹은 둘 이상의 vCPUs가 포함 된 대부분의 범용 및 계산에 최적화 된 인스턴스 크기에서 지원 됩니다.

## <a name="monitoring-resources"></a>리소스 모니터링

각 NVA 솔루션에는 NVA의 성능을 모니터링 하기 위한 자체 도구와 리소스가 있습니다.  성능 제한 사항을 이해 하 고 NVA가 용량에 근접 하거나 도달 했을 때 감지할 수 있는지 확인 하려면 공급 업체 설명서를 참조 하세요.  이 외에도 Azure Monitor Network Insights를 확인 하 고 다음과 같은 네트워크 가상 어플라이언스에 대 한 기본 성능 정보를 확인할 수 있습니다.

- CPU 사용률
- 네트워크 인
- 네트워크 아웃
- 인바운드 흐름
- 아웃바운드 흐름

## <a name="next-steps"></a>다음 단계

대부분의 주요 NVA 파트너는 COVID-19를 실행 하는 동안 예기치 않게 예기치 않은 증가에 대 한 확장에 대 한 지침을 게시 파트너 리소스에 대 한 몇 가지 유용한 링크는 다음과 같습니다.

[Barracuda는 COVID-19 동안 데이터를 보호 하는 동안 집에서 작업을 사용 하도록 설정 합니다.](https://www.barracuda.com/covid-19/work-from-home "COVID-19 동안 데이터를 보호 하는 동안 홈에서 작업 사용")

[Coronavirus 동안 확인 지점 보안 원격 인력](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "Coronavirus 시 원격 인력 보호")

[COVID-19 준비에 대 한 Cisco AnyConnect 구현 및 성능/크기 조정 참조](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "COVID-19 준비에 대 한 Cisco AnyConnect 구현 및 성능/크기 조정 참조")

[Citrix COVID-19 응답 지원 센터](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19 응답 지원 센터")

[원격 작업자의 급격 한 증가를 해결 하기 위한 F5 지침](https://www.f5.com/business-continuity "원격 작업자의 급격 한 증가를 해결 하기 위한 F5 지침")

[고객 및 파트너를 위한 Fortinet COVID-19 업데이트](https://www.fortinet.com/covid-19.html "고객 및 파트너를 위한 COVID-19 업데이트")

[Palo Alto Networks COVID-19 응답 센터](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks COVID-19 응답 센터")

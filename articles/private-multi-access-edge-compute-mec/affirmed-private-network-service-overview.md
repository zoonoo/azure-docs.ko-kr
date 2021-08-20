---
title: Azure의 확정된 개인 네트워크 서비스란?
description: 프라이빗 LTE/5G 네트워크를 위한 Azure의 Affirmed Private Network Service 솔루션에 대해 알아봅니다.
author: KumudD
ms.service: private-multi-access-edge-compute-mec
ms.topic: overview
ms.date: 06/16/2021
ms.author: hollycl
ms.openlocfilehash: ca2327ee8e9245698dc8d9b57b2c5bf2bf8e6881
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112458527"
---
# <a name="what-is-affirmed-private-network-service-on-azure"></a>Azure의 확정된 개인 네트워크 서비스란?

APNS(Affirmed Private Network Service)는 관리형 서비스 공급자와 모바일 네트워크 운영자가 기업에 프라이빗 LTE 및 프라이빗 5G 솔루션을 제공하기 위해 만든 관리형 네트워크 서비스 제품입니다.

Affirmed는 해당 모바일 코어 기술과 Azure 기능을 결합하여 통신 사업자와 기업이 관리형 네트워크와 모바일 에지를 활용할 수 있도록 프라이빗 LTE/5G 네트워크를 위한 완전한 턴키 솔루션을 만들었습니다. 클라우드 관리와 자동화가 결합되므로 관리형 서비스 공급자가 완전 관리형 인프라를 제공할 수 있으며, 운영자가 Azure Marketplace의 풍부한 파트너 에코시스템에서 최상의 무선 액세스 네트워크, SIM, Azure 서비스를 선택할 수 있도록 완벽한 엔드투엔드 솔루션도 제공됩니다. 솔루션은 다음 5가지 구성 요소로 이루어져 있습니다.

- **클라우드 네이티브 Mobile Core**: 이 구성 요소는 3GPP 표준 규격이며 4G와 5G의 네트워크 기능을 모두 지원하며 기본적으로 모바일 코어 내에 있는 가상 네트워크 프로브를 포함합니다. 모바일 코어는 VM, 물리적 서버 또는 운영자 클라우드에 배포할 수 있으므로 전용 하드웨어가 필요하지 않습니다.

- **Private Network Service Manager - Affirmed Networks**: Private Network Service Manager는 운영자가 Azure 플랫폼에서 개인 모바일 코어 네트워크를 배포, 모니터링, 관리하는 데 사용하는 애플리케이션입니다. 프로그래매틱 GUI 기반 포털을 통한 개인 네트워크 리소스의 간단한 자체 활성화 및 관리를 포함하여 전체 관리 기능 세트를 제공합니다.

- **Azure Network Functions Manager**: Azure NFM(Network Functions Manager)은 Azure Portal을 사용하는 일관된 하이브리드 환경을 위해 고객이 Azure Stack Edge Pro에서 GPU로 네트워크 함수를 배포하고 프로비저닝할 수 있는 완전 관리형 클라우드 네이티브 오케스트레이션 서비스입니다.

- **Azure 클라우드**: 분석, 가상 컴퓨팅, 스토리지, 네트워킹 등의 서비스에 사용할 수 있는 IaaS(서비스 제공 인프라), PaaS(Platform as a Service), SaaS(Software as a Service) 등의 솔루션을 포함하는 퍼블릭 클라우드 컴퓨팅 플랫폼입니다.

- **Azure Stack Edge**: Microsoft에서 제공하는 클라우드 관리형 서비스 제공 하드웨어 솔루션입니다. 로컬 AI 및 고급 컴퓨팅 작업을 수행해야 하는 모든 위치에 가상으로 배포할 수 있는 강력한 로컬 서버에 Azure 클라우드의 성능을 제공합니다.


:::image type="content" source="./media/affirmed-overview/affirmed-private-network-service-solution.png" alt-text="Affirmed Private Network Service 솔루션":::

## <a name="why-use-the-affirmed-private-network-solution"></a>Affirmed Private Network 솔루션을 사용해야 하는 이유
APNS는 운영자와 고객에게 다음과 같은 주요 이점을 제공합니다.

- **배포 유연성** - APNS는 컨트롤 및 사용자 플레인 분리 기술을 사용하며, 운영자가 원하는 다양한 기업용 시나리오를 해결하기 위해 세 가지 유형의 배포 모드를 지원합니다. 운영자는 Private Network Service Manager를 사용하여 다음 배포 모델을 구성할 수 있습니다.

    - 독립 실행형을 사용하면 운영자가 RAN, Azure Stack Edge의 5G 코어, 중앙 집중식 클라우드의 관리 계층을 제공하여 온-프레미스에서 완전한 독립 실행형 개인 네트워크를 제공할 수 있습니다.

    - 분산을 사용하면 컨트롤 플레인을 클라우드에 유지하면서 사용자 플레인을 Azure Stack Edge의 엔터프라이즈 에지에 더 가깝게 배포하여 데이터를 더 빠르게 처리할 수 있습니다. 해당 모델의 예로는 제조 시설이 있습니다.

    - 모두 클라우드에 유지를 사용하면 RAN을 에지에 유지하면서 전체 5G 코어를 클라우드에 배포할 수 있으므로 변화하는 워크로드 요구에 맞게 클라우드 리소스를 동적으로 할당할 수 있습니다.

- **MNO 통합** - APNS는 모바일 네트워크 운영자 통합형입니다. 즉, 분산 구독자 코어를 사용하여 개인 및 공용 운영자 네트워크에서 완전한 이동성을 제공합니다. 운영자가 개인 모바일 네트워크를 1,000개의 엔터프라이즈 에지 사이트로 스케일링할 수 있는 이점이 있습니다.

    - MNO 라이선스, 프라이빗 라이선스, CBRS, 공유, 라이선스 없음 등의 모든 옵션을 지원합니다.

    - MNO 통합형이므로 격리/독립 실행형 개인 네트워크, 다중 사이트 로밍, 매크로 로밍을 지원합니다.

    - 99.999% 서비스 가용성을 제공하고 3GPP 규격 LTE 및 5G NR 무선과 상호 작용할 수 있습니다. 기업에 이동 통신 사업자급 복원력을 제공합니다.

- **자동화 및 관리 용이성** - APNS 솔루션은 Azure 클라우드의 Service Manager를 통해 원격으로 완전히 관리할 수 있습니다. 최종 사용자는 Service Manager를 통해 맞춤형 대시보드에 액세스하고 개인 모바일 네트워크의 디바이스를 관리하고, 보고, 켜고 끌 수 있습니다. 운영자는 네트워크 상태에서 네트워크 문제와 주요 매개 변수를 모니터링하여 최적 성능을 보장할 수 있습니다.

    - Azure 전용 다중 액세스 에지 컴퓨팅에서 실행되는 안전하고 안정적이면서 대역폭이 높고 대기 시간이 짧은 개인 모바일 네트워킹 서비스를 제공합니다.

    - 트럭 롤 없이 완전한 원격 관리를 지원합니다.

    - 운영자가 기업에 관리형 서비스를 제공하거나 관리형 서비스를 제공할 수 있는 MSP와 파트너 관계를 맺을 수 있도록 클라우드 자동화를 제공합니다.

- **스마트 네트워크 및 비즈니스 인사이트** - Affirmed 모바일 코어에는 네트워크 인사이트를 제공하는 데 사용할 수 있는 포함 가상 프로브/패킷 중개 기능이 있습니다. 운영자는 인사이트를 사용하여 더 효율적인 네트워크 의사 결정을 내리고, 고객은 인사이트를 사용하여 더 스마트한 수익 창출 의사 결정을 내릴 수 있습니다.

- **데이터 개인 정보 및 보안** - APNS는 Azure를 사용하여 개인 네트워크와 엔터프라이즈 애플리케이션에서 보안과 규정 준수를 제공합니다. 운영자는 의료, 정부, 공공 안전, 국방 등 엄격한 데이터 개인 정보 보호법이 필요한 업계 사용 사례를 위한 솔루션을 자신 있게 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Affirmed Private Network Service 솔루션을 배포](deploy-affirmed-private-network-service-solution.md)하는 방법을 알아봅니다.




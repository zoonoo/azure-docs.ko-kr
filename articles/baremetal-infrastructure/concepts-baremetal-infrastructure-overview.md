---
title: Azure의 BareMetal Infrastructure 미리 보기 개요
description: Azure의 BareMetal Infrastructure 개요.
ms.custom: references_regions
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: 603aa6504531ef8a75fccbc9d9cc6de648b42373
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954635"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>Azure의 BareMetal Infrastructure 미리 보기란?

Azure BareMetal Infrastructure는 엔터프라이즈 사용자 지정 워크로드를 마이그레이션하기 위한 안전한 솔루션을 제공합니다. BareMetal 인스턴스는 사용자에게 할당된 비공유 호스트/서버 하드웨어로 인증된 하드웨어, 라이선스 및 지원계약이 필요한 특수 워크로드를 통해 온-프레미스 솔루션의 포팅 잠금을 해제합니다. 게스트 OS(운영 체제) 모니터링 및 애플리케이션 모니터링이 소유권 내에 포함되는 반면 Azure는 인프라 모니터링 및 유지 관리를 처리합니다.

BareMetal Infrastructure는 기존 투자 및 아키텍처를 유지하면서 인프라를 현대화하는 경로를 제공합니다. BareMetal Infrastructure를 사용하여 Azure에 특수 워크로드를 제공할 수 있으므로 낮은 대기 시간으로 Azure 서비스에 액세스하고 통합할 수 있습니다.

## <a name="sku-availability-in-azure-regions"></a>Azure 지역의 SKU 가용성
수정 버전 4.2(Rev 4.2) 스탬프를 기반으로 하는 4개 지역부터 특수한 범용 워크로드에 대한 BareMetal Infrastructure를 사용할 수 있습니다.
- 서유럽
- 북유럽
- 미국 동부 2
- 미국 중남부

>[!NOTE]
>**Rev 4.2** 는 기존 Rev 4 아키텍처를 사용하는 완전히 새로워진 최신 BareMetal Infrastructure입니다.  Rev 4는 Azure VM(가상 머신) 호스트에 더 가까운 근접성을 제공합니다. Rev 4 스탬프 또는 행에 배포된 Azure VM과 BareMetal 인스턴스 단위 간의 네트워크 대기 시간이 대폭 단축되었습니다.  Azure Portal을 통해 BareMetal 인스턴스에 액세스하고 이를 관리할 수 있습니다. 

## <a name="support"></a>지원
BareMetal Infrastructure는 ISO 27001, ISO 27017, SOC 1 및 SOC 2와 호환됩니다.  또한 BYOL(사용자 라이선스 필요) 모델, 즉 OS, 특수 작업 및 타사 애플리케이션을 사용합니다.  

루트 액세스 및 모든 권한을 받는 즉시 다음에 대한 책임이 있다고 가정합니다.
- 백업 및 복구 솔루션 디자인 및 구현, 고가용성, 재해 복구
- OS 및 타사 소프트웨어에 대한 라이선스, 보안 및 지원

Microsoft는 다음을 담당합니다.
- 특수 워크로드를 위한 하드웨어 제공 
- OS 프로비전

:::image type="content" source="media/baremetal-support-model.png" alt-text="BareMetal Infrastructure 지원 모델" border="false":::

## <a name="compute"></a>컴퓨팅
BareMetal Infrastructure는 특수 워크로드에 대해 여러 SKU를 제공합니다. 작은 2소켓 시스템에서 24소켓 시스템으로 사용할 수 있는 SKU를 사용할 수 있습니다. 특수 워크로드에 대한 워크로드 관련 SKU를 사용합니다.

BareMetal 인스턴스 스탬프 자체는 다음 구성 요소를 결합합니다.

- **컴퓨팅:** 필요한 컴퓨팅 기능을 제공하고 특수 워크로드에 대해 인증된 차세대 Intel Xeon 프로세서 기반의 서버입니다.

- **네트워크:** 컴퓨팅, 스토리지, LAN 구성 요소를 상호 연결하는 통합된 고속 네트워크 패브릭입니다.

- **스토리지:** 통합 네트워크 패브릭을 통해 액세스 되는 인프라입니다.

BareMetal 스탬프의 다중 테넌트 인프라 내에서 고객이 격리된 테넌트 내에서 배포됩니다. 테넌트 배포 시 Azure 등록내에서 Azure 구독 이름을 지정합니다. 이 Azure 구독은 BareMetal 인스턴스에 청구되는 구독입니다.

>[!NOTE]
>BareMetal 인스턴스에 배포된 고객은 테넌트로 격리됩니다. 테넌트는 네트워킹, 스토리지 및 컴퓨팅 계층에서 다른 테넌트로부터 격리됩니다. 따라서 서로 다른 테넌트에 할당된 스토리지 및 컴퓨팅 단위는 BareMetal 인스턴스에서 상호 보거나 통신할 수 없습니다.

## <a name="os"></a>OS
BareMetal 인스턴스를 프로비전하는 동안 컴퓨터에 설치하려는 OS를 선택할 수 있습니다. 

>[!NOTE]
>BareMetal Infrastructure는 BYOL 모델을 염두에 두어야 합니다.

사용 가능한 Linux OS 버전은 다음과 같습니다.
- RHEL(Red Hat Enterprise Linux) 7.6
- SLES(SUSE Linux Enterprise Server)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>스토리지
특정 SKU 유형을 기반으로 하는 BareMetal 인스턴스는 특정 작업 유형에 대해 미리 정의된 NFS 스토리지와 함께 제공됩니다. BareMetal을 프로비전할 때 지원 요청을 제출하여 예상 증가량에 따라 더 많은 스토리지를 프로비전할 수 있습니다. 모든 스토리지는 NFSv3 및 NFSv4를 지원하는 개정판 4.2의 모든 플래시 디스크와 함께 제공됩니다. 최신 수정 버전 4.5NVMe SSD를 사용할 수 있습니다. 스토리지 크기 조정에 대한 자세한 내용은 [BareMetal 워크로드 형식](../virtual-machines/workloads/sap/get-started.md) 섹션을 참조하세요.

>[!NOTE]
>BareMetal에 사용되는 스토리지는 [FIPS(Federal Information Processing Standard) 게시140-2](/microsoft-365/compliance/offering-fips-140-2) 요구 사항을 충족하며 기본적으로 미사용 암호화를 제공합니다. 데이터는 디스크에 안전하게 저장됩니다.

## <a name="networking"></a>네트워킹
Azure 네트워크 서비스의 아키텍처는 BareMetal 인스턴스에서 특수 워크로드를 성공적으로 배포하기 위한 핵심 구성 요소입니다. 일부 IT 시스템은 아직 Azure에 위치하지 가능성이 큽니다. Azure를 온-프레미스 소프트웨어 배포에 대한 가상 데이터 센터처럼 보이도록 하는 네트워크 기술을 제공합니다. BareMetal 인스턴스에 필요한 Azure 네트워크 기능은 다음과 같습니다.

- Azure 가상 네트워크는 온-프레미스 네트워크 자산에 연결되는 ExpressRoute 회로에 연결됩니다.
- 온-프레미스를 Azure에 연결하는 ExpressRoute 회로에는 최소한 1Gbps 이상의 대역폭이 있어야 합니다.
- Azure에서 확장된 Active 디렉터리 및 DNS 또는 Azure에서 완전히 실행됩니다.

ExpressRoute를 사용하면 연결 공급자의 도움을 받아 프라이빗 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. **ExpressRoute 프리미엄** 을 사용하도록 설정하여 지정학적 경계 간 연결을 확장하거나, 원하는 Azure 지역 근처의 위치 사이에 비용 효율적인 데이터 전송을 위해 **ExpressRoute 로컬** 을 사용할 수 있습니다.

BareMetal 인스턴스는 Azure VNET 서버 IP 주소 범위 내에서 프로비전됩니다.

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Azure BareMetal Infrastructure 다이어그램" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

표시된 아키텍처는 다음과 같은 세 가지 섹션으로 구분됩니다.
- **왼쪽:** 여러 애플리케이션을 실행하는 고객 온-프레미스 인프라를 보여 줍니다. 파트너 또는 로컬 에지 라우터를 통해 연결합니다. 자세한 내용은 [연결 공급자 및 위치: Azure ExpressRoute](../expressroute/expressroute-locations.md)를 참조하세요.
- **중앙:** Azure 구독을 사용하여 프로비전된 [ExpressRoute](../expressroute/expressroute-introduction.md)를 Azure 경계 네트워크에 연결하여 표시합니다.
- **오른쪽:** Azure IaaS를 보여 주며, 여기서는 Azure 가상 네트워크 내에서 프로비전되는 애플리케이션을 호스트하는 VM을 사용합니다.
- **아래:** 짧은 대기 시간을 제공하는 BareMetal 연결에 대해 [ExpressRoute FastPath](../expressroute/about-fastpath.md)를 사용하도록 설정된 ExpressRoute 게이트웨이 사용을 보여 줍니다.   
   >[!TIP]
   >이를 지원하기 위해 ExpressRoute 게이트웨이를 UltraPerformance여야 합니다.  자세한 내용은 [ExpressRoute 가상 네트워크 게이트웨이 정보](../expressroute/expressroute-about-virtual-network-gateways.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 단계에서는 Azure Portal을 통해 BareMetal 인스턴스 단위를 식별하고 상호 작용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Portal을 통해 BareMetal 인스턴스 관리](connect-baremetal-infrastructure.md)
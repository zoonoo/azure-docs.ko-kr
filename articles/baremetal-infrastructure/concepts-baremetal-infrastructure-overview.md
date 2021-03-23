---
title: Azure의 BareMetal Infrastructure 미리 보기 개요
description: Azure의 BareMetal 인프라 개요.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 1/4/2021
ms.openlocfilehash: 94cee52eccd8cc9e9631b47bdf84892d763d86d3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776316"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>Azure의 BareMetal Infrastructure 미리 보기 란 무엇 인가요?

Azure BareMetal 인프라는 엔터프라이즈 사용자 지정 워크 로드를 마이그레이션하기 위한 안전한 솔루션을 제공 합니다. BareMetal 인스턴스는 사용자에 게 할당 된 비공유 호스트/서버 하드웨어입니다. 인증 된 하드웨어, 라이선스 및 지원 계약이 필요한 특수 작업으로 온-프레미스 솔루션의 포팅 잠금을 해제 합니다. 게스트 OS (운영 체제) 모니터링 및 응용 프로그램 모니터링이 소유권 내에 포함 되는 동안 Azure는 인프라 모니터링 및 유지 관리를 처리 합니다.

BareMetal 인프라는 기존 투자 및 아키텍처를 유지 하면서 인프라를 현대화 하는 경로를 제공 합니다. BareMetal 인프라를 사용 하 여 Azure에 전문화 된 워크 로드를 제공할 수 있으므로 짧은 대기 시간으로 Azure 서비스에 액세스 하 고 통합할 수 있습니다.

## <a name="sku-availability-in-azure-regions"></a>Azure 지역의 SKU 가용성
개정판 4.2 (Rev 4.2) 스탬프를 기반으로 하는 4 개 지역부터 특수 한 범용 워크 로드에 대 한 BareMetal 인프라를 사용할 수 있습니다.
- 서유럽
- 북유럽
- 미국 동부 2
- 미국 중남부

>[!NOTE]
>**Rev 4.2** 은 기존 수정 버전 4 아키텍처를 사용 하는 최신 리브랜딩 BareMetal 인프라입니다.  Rev 4는 Azure VM (가상 컴퓨터) 호스트에 더 근접 하 게 됩니다. Rev 4 스탬프 또는 행에 배포 된 Azure Vm과 BareMetal instance 단위 간의 네트워크 대기 시간이 크게 향상 되었습니다.  Azure Portal를 통해 BareMetal 인스턴스에 액세스 하 고 관리할 수 있습니다. 

## <a name="support"></a>지원
BareMetal 인프라는 ISO 27001, ISO 27017, SOC 1 및 SOC 2와 호환 됩니다.  또한 BYOL (사용자 라이선스) 모델을 사용 합니다. OS, 특수 작업 및 타사 응용 프로그램을 사용 합니다.  

루트 액세스 및 모든 권한을 받는 즉시 다음에 대 한 책임이 있다고 가정 합니다.
- 백업 및 복구 솔루션 디자인 및 구현, 고가용성 및 재해 복구
- OS 및 타사 소프트웨어에 대 한 라이선스, 보안 및 지원

Microsoft는 다음을 담당 합니다.
- 특수 작업을 위한 하드웨어 제공 
- OS 프로 비전

:::image type="content" source="media/baremetal-support-model.png" alt-text="BareMetal 인프라 지원 모델" border="false":::

## <a name="compute"></a>Compute
BareMetal 인프라는 특수화 된 워크 로드에 대해 여러 Sku를 제공 합니다. 작은 2 소켓 시스템에서 24 소켓 시스템으로 사용할 수 있는 Sku를 사용할 수 있습니다. 특수 워크 로드에 대 한 워크 로드 관련 Sku를 사용 합니다.

BareMetal 인스턴스 스탬프 자체는 다음 구성 요소를 결합 합니다.

- **컴퓨팅:** 필요한 컴퓨팅 기능을 제공 하 고 특수 한 워크 로드에 대 한 인증을 받은 다른 Intel Xeon 프로세서 세대를 기반으로 하는 서버입니다.

- **네트워크:** 통합 된 고속 네트워크 패브릭은 컴퓨팅, 저장소 및 LAN 구성 요소를 상호 통합 합니다.

- **저장소:** 통합 네트워크 패브릭을 통해 액세스 되는 인프라입니다.

BareMetal 스탬프의 다중 테 넌 트 인프라 내에서 고객은 격리 된 테 넌 트에 배포 됩니다. 테 넌 트를 배포할 때 azure 등록 내에서 azure 구독 이름을로 합니다. 이 Azure 구독은 BareMetal 인스턴스에 청구 되는 구독입니다.

>[!NOTE]
>BareMetal 인스턴스에 배포 된 고객은 테 넌 트로 격리 됩니다. 테넌트는 네트워킹, 스토리지 및 컴퓨팅 계층에서 다른 테넌트로부터 격리됩니다. 다른 테 넌 트에 할당 된 저장소 및 계산 단위는 BareMetal 인스턴스에서 서로 통신할 수 없으며 서로 통신할 수 없습니다.

## <a name="os"></a>OS
BareMetal 인스턴스를 프로 비전 하는 동안 컴퓨터에 설치 하려는 OS를 선택할 수 있습니다. 

>[!NOTE]
>BareMetal 인프라는 BYOL 모델을 염두에 두어야 합니다.

사용 가능한 Linux OS 버전은 다음과 같습니다.
- Red Hat Enterprise Linux (RHEL) 7.6
- SLES(SUSE Linux Enterprise Server)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>Storage
특정 SKU 유형을 기반으로 하는 BareMetal 인스턴스는 특정 작업 유형에 대해 미리 정의 된 NFS 저장소와 함께 제공 됩니다. BareMetal를 프로 비전 할 때 지원 요청을 제출 하 여 예상 증가량에 따라 더 많은 저장소를 프로 비전 할 수 있습니다. 모든 저장소는 NFSv3 및 NFSv4를 지 원하는 개정판 4.2의 모든 플래시 디스크와 함께 제공 됩니다. 최신 수정 버전 4.5 NVMe SSD를 사용할 수 있습니다. 저장소 크기 조정에 대 한 자세한 내용은 [BareMetal 작업 형식](../virtual-machines/workloads/sap/get-started.md) 섹션을 참조 하세요.

>[!NOTE]
>BareMetal에 사용 되는 저장소는 [FIPS (FIPS(Federal Information Processing Standard)) 게시 140-2](/microsoft-365/compliance/offering-fips-140-2) 요구 사항을 충족 하며 기본적으로 미사용 암호화를 제공 합니다. 데이터는 디스크에 안전 하 게 저장 됩니다.

## <a name="networking"></a>네트워킹
Azure 네트워크 서비스의 아키텍처는 BareMetal 인스턴스에서 특수 워크 로드를 성공적으로 배포 하기 위한 핵심 구성 요소입니다. 모든 IT 시스템이 Azure에 이미 있는 것은 아닐 수 있습니다. Azure는 Azure를 온-프레미스 소프트웨어 배포에 대 한 가상 데이터 센터 처럼 보이도록 하는 네트워크 기술을 제공 합니다. BareMetal 인스턴스에 필요한 Azure 네트워크 기능은 다음과 같습니다.

- Azure 가상 네트워크는 온-프레미스 네트워크 자산에 연결 하는 Express 경로 회로에 연결 됩니다.
- 온-프레미스를 Azure에 연결 하는 Express 경로 회로는 최소 1Gbps 이상의 대역폭을 포함 해야 합니다.
- Azure에서 확장 된 Active directory 및 DNS 또는 Azure에서 완전히 실행 됩니다.

Express 경로를 사용 하면 연결 공급자의 도움으로 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. **Express 경로 프리미엄** 을 사용 하도록 설정 하 여 지정 학적 경계 간 연결을 확장 하거나, 원하는 Azure 지역 근처의 위치 사이에 비용 효율적인 데이터를 전송 하기 위해 **express 경로 로컬** 을 사용할 수 있습니다.

BareMetal 인스턴스는 Azure VNET 서버 IP 주소 범위 내에서 프로 비전 됩니다.

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Azure BareMetal 인프라 다이어그램" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

표시된 아키텍처는 다음과 같은 세 가지 섹션으로 구분됩니다.
- **왼쪽:** 여러 응용 프로그램을 실행 하는 고객 온-프레미스 인프라를 보여 줍니다. 파트너 또는 로컬에 지 라우터를 통해 연결 합니다. 자세한 내용은 [연결 공급자 및 위치: Azure express](../expressroute/expressroute-locations.md)경로를 참조 하세요.
- **센터:** azure 구독을 사용 하 여 프로 비전 된 [Express](../expressroute/expressroute-introduction.md) 경로를 azure edge 네트워크에 연결 하 여 표시 합니다.
- **Right:** azure IaaS를 표시 하며,이 경우 azure 가상 네트워크 내에서 프로 비전 되는 응용 프로그램을 호스트 하는 vm을 사용 합니다.
- **Bottom:** 짧은 대기 시간을 제공 하는 BareMetal 연결에 대해 [express 경로](../expressroute/about-fastpath.md) 를 사용 하도록 설정 된 express 경로 게이트웨이 사용을 보여 줍니다.   
   >[!TIP]
   >이를 지원 하기 위해 Express 경로 게이트웨이를 UltraPerformance 해야 합니다.  자세한 내용은 [express 경로 가상 네트워크 게이트웨이 정보](../expressroute/expressroute-about-virtual-network-gateways.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 단계는 Azure Portal 통해 BareMetal 인스턴스 단위를 식별 하 고 상호 작용 하는 방법을 배우는 것입니다.

> [!div class="nextstepaction"]
> [Azure Portal을 통해 BareMetal 인스턴스 관리](connect-baremetal-infrastructure.md)
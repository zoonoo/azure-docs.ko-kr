---
title: Azure BareMetal 인프라의 약관 파악
description: Azure BareMetal 인프라의 약관을 파악합니다.
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: b22a6cecb2647df3878cb8fd4ade93d9a7d963fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104770889"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>BareMetal 인프라의 약관 파악

이 문서에서는 몇 가지 중요한 BareMetal 약관을 다룹니다.

- **수정 버전**: 수정 버전 3(Rev 3)으로 알려진 원래 스탬프 수정 버전 및 BareMetal 인스턴스 스탬프의 두 가지 다른 스탬프 수정 버전이 있습니다. 각 스탬프는 아키텍처와 Azure 가상 머신 호스트와의 근접성이 다릅니다.
    - **수정 버전 4**(Rev 4): Azure VM(가상 머신) 호스트에 더 가까운 근접성을 제공하고 Azure VM과 BareMetal 인스턴스 단위 간의 대기 시간을 줄이는 최신 디자인입니다. 
    - **수정 버전 4.2**(Rev 4.2): 기존 Rev 4 아키텍처를 사용하는 완전히 새로워진 최신 BareMetal 인프라입니다. Rev 4는 Azure VM(가상 머신) 호스트에 더 가까운 근접성을 제공합니다. Rev 4 스탬프 또는 행에 배포된 Azure VM과 BareMetal 인스턴스 단위 간의 네트워크 대기 시간이 대폭 줄었습니다. Azure Portal을 통해 BareMetal 인스턴스에 액세스하고 이를 관리할 수 있습니다.    

- **스탬프**: BareMetal 인스턴스의 Microsoft 내부 배포 크기를 정의합니다. 인스턴스 단위를 배포하기 전에, 데이터 센터 위치에 컴퓨팅, 네트워크, 스토리지 랙으로 구성된 BareMetal 인스턴스 스탬프를 배포해야 합니다. 이러한 배포를 BareMetal 인스턴스 스탬프 또는 수정 버전 4.2의 스탬프라고 합니다.

- **테넌트:** BareMetal 인스턴스 스탬프에 배포된 고객은 *테넌트* 로 격리됩니다. 테넌트는 네트워킹, 스토리지 및 컴퓨팅 계층에서 다른 테넌트로부터 격리됩니다. 다른 테넌트에 할당된 스토리지 및 컴퓨팅 단위는 BareMetal 인스턴스 스탬프 수준에서 서로 보거나 통신할 수 없습니다. 고객은 다양한 테넌트에 배포하도록 선택할 수 있습니다. 그러한 경우에도 BareMetal 인스턴스 스탬프 수준에서 테넌트 간 통신은 없습니다.

## <a name="next-steps"></a>다음 단계
[BareMetal 인프라](concepts-baremetal-infrastructure-overview.md)에 대해 자세히 알아보거나 [BareMetal 인스턴스 단위를 식별하고 상호 작용](connect-baremetal-infrastructure.md)하는 방법을 알아보세요. 
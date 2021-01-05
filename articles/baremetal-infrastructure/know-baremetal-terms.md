---
title: Azure BareMetal 인프라의 약관 파악
description: Azure BareMetal 인프라의 약관을 파악 합니다.
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: fd7a39854c86f728ef152f8e7d858157e1ad26f4
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861912"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>BareMetal 인프라에 대 한 용어 파악

이 문서에서는 몇 가지 중요 한 BareMetal 용어를 다룹니다.

- **수정 버전**: BareMetal 인스턴스 스탬프에 대해 수정 버전 3 (Rev 3) 이라는 원래 스탬프 수정 버전 및 두 개의 다른 스탬프 수정 버전이 있습니다. 각 스탬프는 Azure 가상 컴퓨터 호스트와 아키텍처 및 근접 하 게 다릅니다.
    - **수정 버전 4** (Rev 4): azure vm (가상 머신) 호스트에 더 근접 하 게 근접 하 고 azure Vm과 BareMetal 인스턴스 단위 간의 대기 시간을 낮추는 새로운 디자인입니다. 
    - **수정 버전 4.2** (rev 4.2): 기존 수정 버전 4 아키텍처를 사용 하는 최신 리브랜딩 BareMetal 인프라입니다. Rev 4는 Azure VM (가상 컴퓨터) 호스트에 더 근접 하 게 됩니다. Rev 4 스탬프 또는 행에 배포 된 Azure Vm과 BareMetal instance 단위 간의 네트워크 대기 시간이 크게 향상 되었습니다. Azure Portal를 통해 BareMetal 인스턴스에 액세스 하 고 관리할 수 있습니다.    

- **스탬프**: BareMetal 인스턴스의 Microsoft 내부 배포 크기를 정의 합니다. 인스턴스 단위를 배포 하기 전에 계산, 네트워크 및 저장소 랙에 구성 된 BareMetal 인스턴스 스탬프를 데이터 센터 위치에 배포 해야 합니다. 이러한 배포를 BareMetal 인스턴스 스탬프 또는 수정 버전 4.2에서 호출 합니다.

- **테 넌 트**: BareMetal 인스턴스 스탬프에 배포 된 고객은 *테 넌 트로 격리 됩니다.* 테넌트는 네트워킹, 스토리지 및 컴퓨팅 계층에서 다른 테넌트로부터 격리됩니다. 다른 테 넌 트에 할당 된 저장소 및 계산 단위는 BareMetal 인스턴스 스탬프 수준에서 서로를 보거나 서로 통신할 수 없습니다. 고객은 다양한 테넌트에 배포하도록 선택할 수 있습니다. BareMetal 인스턴스 스탬프 수준에서 테 넌 트 간에 통신이 없습니다.

## <a name="next-steps"></a>다음 단계
[BareMetal 인프라](workloads/sap/baremetal-overview-architecture.md) 에 대해 자세히 알아보거나 [BareMetal 인스턴스 단위를 식별 하 고 상호 작용](workloads/sap/baremetal-infrastructure-portal.md)하는 방법에 대해 알아보세요. 
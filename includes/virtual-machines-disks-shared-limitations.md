---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008354"
---
미리 보기 중에는 공유 디스크를 사용 하도록 설정 하는 것은 디스크 형식의 하위 집합에만 사용할 수 있습니다. 현재는 ultra disks 및 premium Ssd만 공유 디스크를 사용 하도록 설정할 수 있습니다. 공유 디스크를 사용할 수 있는 관리 되는 각 디스크에는 디스크 유형별로 구성 된 다음과 같은 제한 사항이 적용 됩니다.

### <a name="ultra-disks"></a>Ultra disks

Ultra disks에는 공유 디스크와 상관 없이 별도의 제한 사항 목록이 있습니다. Ultra disk 제한 사항은 [Azure ultra Disks 사용](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)을 참조 하세요.

울트라 디스크를 공유 하는 경우 다음과 같은 추가 제한 사항이 있습니다.

- 현재 미국 서 부 에서만 지원 됩니다.
- 현재 Azure Resource Manager 또는 SDK 지원으로 제한 되어 있습니다.
- 일부 버전의 Windows Server 장애 조치 (failover) 클러스터에서는 기본 디스크만 사용할 수 있습니다. 자세한 내용은 [장애 조치 (Failover) 클러스터링 하드웨어 요구 사항 및 저장소 옵션](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)을 참조 하세요.

### <a name="premium-ssds"></a>프리미엄 SSD

- 현재 미국 서 부 지역 에서만 지원 됩니다.
- 디스크를 공유 하는 모든 가상 머신은 동일한 [근접 배치 그룹](../articles/virtual-machines/windows/proximity-placement-groups.md)에 배포 되어야 합니다.
- OS 디스크가 아닌 데이터 디스크에 대해서만 사용할 수 있습니다.
- 일부 버전의 Windows Server 장애 조치 (failover) 클러스터에서는 기본 디스크만 사용할 수 있습니다. 자세한 내용은 [장애 조치 (Failover) 클러스터링 하드웨어 요구 사항 및 저장소 옵션](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)을 참조 하세요.
- 에서 `maxShares>1`premium ssd에는 읽기 전용 호스트 캐싱을 사용할 수 없습니다.
- 가용성 집합 및 가상 머신 확장 집합은 1로 설정 된 `FaultDomainCount` 경우에만 사용할 수 있습니다.
- Azure Backup 및 Azure Site Recovery 지원 기능을 아직 사용할 수 없습니다.

공유 디스크를 사용해 보려는 경우 [미리 보기에 등록](https://aka.ms/AzureSharedDiskPreviewSignUp)하세요.

---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471704"
---
미리 보기 에서 공유 디스크를 사용하도록 설정한 관리 디스크에는 다음과 같은 제한 사항이 적용됩니다.

- 현재 프리미엄 SSD에서만 사용할 수 있습니다.
- 현재 미국 중서부 지역에서만 지원됩니다.
- 디스크를 공유하는 모든 가상 컴퓨터는 동일한 [근접 배치 그룹에](../articles/virtual-machines/windows/proximity-placement-groups.md)배포되어야 합니다.
- OS 디스크가 아닌 데이터 디스크에서만 사용할 수 있습니다.
- 일부 버전의 Windows Server 장애 조치 클러스터에서는 기본 디스크만 사용할 수 있으며 자세한 내용은 [장애 조치 클러스터링 하드웨어 요구 사항 및 저장소 옵션을](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)참조하십시오.
- ReadOnly 호스트 캐싱은 을 사용 `maxShares>1`하 여 프리미엄 SSD에 사용할 수 없습니다.
- 가용성 집합 및 가상 시스템 크기 집합은 1로 `FaultDomainCount` 설정된 경우에만 사용할 수 있습니다.
- Azure 백업 및 Azure 사이트 복구 지원을 아직 사용할 수 없습니다.

공유 디스크를 시도하고 싶다면 미리 [보기에 등록하십시오.](https://aka.ms/AzureSharedDiskPreviewSignUp)

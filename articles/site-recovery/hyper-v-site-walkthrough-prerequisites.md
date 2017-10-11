---
title: "Azure Site Recovery를 사용하여 Azure로 Hyper-V(System Center VMM 없음) 복제를 위한 필수 구성 요소 검토 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 Azure에 온-프레미스 Hyper-V VM의 복제, 장애 조치(failover) 및 복구를 설정하기 위한 필수 구성 요소를 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7ef3fb46-52f5-4c8a-b1a1-658c2305762a
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: cbb5d3598ef91512991d7d1e9f854eb12980752b
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-without-vmm-to-azure-replication"></a>2단계: Azure에 Hyper-V(VMM 없음) 복제를 위한 필수 구성 요소 검토

필수 구성 요소는 테이블에 요약되어 있습니다.


**필수 요소** | **세부 정보** 
--- | --- 
**Azure** | [Azure 요구 사항](site-recovery-prereq.md#azure-requirements)에 대해 알아봅니다.
**온-프레미스 서버** | 온-프레미스 Hyper-V 호스트에 대한 요구 사항을 [자세히 알아봅니다](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm).
**온-프레미스 Hyper-V VM** | 복제하려는 VM은 [지원되는 운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)를 실행하고 [Azure 필수 조건](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 준수해야 합니다.
**Azure URL** | Hyper-V가 다음 URL에 액세스할 수 있어야 합니다.<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인합니다.<br/></br> [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/></br> 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요(Access Control 및 ID 관리에 사용됨).



## <a name="next-steps"></a>다음 단계

- 전체 배포를 수행하는 경우 [3단계: 용량 계획](hyper-v-site-walkthrough-capacity.md)으로 이동합니다.
- 간단한 테스트 배포를 수행하는 경우 [4단계: 네트워킹 계획](hyper-v-site-walkthrough-network.md)으로 이동합니다.

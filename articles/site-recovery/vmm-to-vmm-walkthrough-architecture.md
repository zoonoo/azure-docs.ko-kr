---
title: "Azure Site Recovery를 사용한 보조 사이트로의 Hyper-V 복제 아키텍처 검토 | Microsoft 문서"
description: "이 문서에서는 Azure Site Recovery를 사용하여 온-프레미스 Hyper-V VM을 보조 System Center VMM 사이트로 복제하기 위한 아키텍처 개요를 제공합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 07099161-4cc7-4f32-8eb9-2a71bbf0750b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: b78cd0d5a5395873afaddc8856004775f447e8ea
ms.contentlocale: ko-kr
ms.lasthandoff: 08/01/2017

---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-a-secondary-site"></a>1단계: 보조 사이트로의 Hyper-V 복제를 위한 아키텍처 검토

이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 System Center VMM(Virtual Machine Manager) 클라우드의 온-프레미스 Hyper-V VM(가상 컴퓨터)을 보조 VMM 사이트로 복제할 때 사용되는 구성 요소 및 프로세스를 설명합니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.



## <a name="architectural-components"></a>아키텍처 구성 요소

Hyper-V VM을 보조 VMM 사이트에 복제하는 데 필요한 사항은 다음과 같습니다.

**구성 요소** | **위치** | **세부 정보**
--- | --- | ---
**Azure** | Azure의 구독. | VMM 위치 간에 복제를 오케스트레이션 및 관리할 수 있도록 Azure 구독에서 Recovery Services 자격 증명 모음을 만듭니다.
**VMM 서버** | VMM 기본 및 보조 위치가 필요합니다. | VMM 서버는 기본 사이트에서 1개, 보조 사이트에서 1개를 사용하는 것이 좋습니다. 
**Hyper-V 서버** |  기본 및 보조 VMM 클라우드에 있는 하나 이상의 Hyper-V 호스트 서버. | Kerberos 또는 인증서 인증을 사용하여 LAN 또는 VPN을 통해 기본 및 보조 Hyper-V 호스트 서버 간에 데이터가 복제됩니다.  
**Hyper-V VM** | Hyper-V 호스트 서버에 있습니다. | 원본 호스트 서버에는 복제하려는 VM이 하나 이상 있어야 합니다.

## <a name="replication-process"></a>복제 프로세스

1. Azure 계정을 설정하고, Recovery Services 자격 증명 모음 만들고, 무엇을 복제할 것인지 지정합니다.
2. VMM 서버에 Azure Site Recovery 공급자를 설치하고 각 Hyper-V 호스트에 Microsoft Azure Recovery Services 에이전트를 설치하는 작업을 포함하여 원본 및 대상 복제 설정을 구성합니다.
3. 원본 VMM 클라우드에 대한 복제 정책을 만듭니다. 정책은 클라우드의 호스트에 있는 모든 VM에 적용됩니다.
4. 각 VMM에 대해 복제를 사용하도록 설정하면 선택한 설정에 따라 VM의 초기 복제가 발생합니다.
5. 초기 복제 후 델타 변경 내용의 복제가 시작됩니다. .hrl 파일에는 항목에 대한 추적된 변경 내용이 유지됩니다.


![온-프레미스 간](./media/vmm-to-vmm-walkthrough-architecture/arch-onprem-onprem.png)

## <a name="failover-and-failback-process"></a>장애 조치 및 장애 복구 프로세스

1. 온-프레미스 사이트 간에 계획된 또는 계획되지 않은 [장애 조치](site-recovery-failover.md)를 실행할 수 있습니다. 계획된 장애 조치를 실행할 경우 데이터 손실을 방지하기 위해 원본 VM이 종료됩니다.
2. 단일 컴퓨터에 장애 조치를 수행하거나 [복구 계획](site-recovery-create-recovery-plans.md)을 만들어서 여러 컴퓨터의 장애 조치를 오케스트레이션할 수 있습니다.
4. 보조 사이트에 계획되지 않은 장애 조치를 수행했다면 장애 조치 후에 보조 위치에 있는 장애 조치 컴퓨터는 보호 또는 복제하도록 설정되지 않습니다. 계획된 장애 조치를 실행했으면 장애 조치 후에 보조 위치에 있는 컴퓨터가 보호됩니다.
5. 그런 다음 장애 조치를 커밋하여 복제본 VM에서 워크로드에 액세스합니다.
6. 기본 사이트를 사용할 수 있는 경우 역방향 복제를 시작하여 보조 사이트에서 기본 사이트로 복제합니다. 역방향 복제는 가상 컴퓨터를 보호된 상태로 가져오지만 보조 데이터 센터는 여전히 활성 위치입니다.
7. 기본 사이트를 활성 위치로 다시 만들려면 다른 역방향 복제 후에 보조 사이트에서 기본 사이트로 계획된 장애 조치를 시작합니다.



## <a name="next-steps"></a>다음 단계

[2단계: 필수 조건 및 제한 사항 검토](vmm-to-vmm-walkthrough-prerequisites.md)로 이동합니다.


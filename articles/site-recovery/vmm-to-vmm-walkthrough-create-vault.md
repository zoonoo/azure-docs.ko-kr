---
title: "Azure Site Recovery를 사용하여 보조 사이트로의 Hyper-V 복제용 자격 증명 모음 만들기 | Microsoft 문서"
description: "Azure Site Recovery를 사용하여 보조 System Center VMM 사이트로 Hyper-V VM을 복제할 때 자격 증명 모음을 만드는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ff65dbfb-cb26-410e-ab48-76971625db08
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 28cfcf12b2e369f96664c163c0b6f2aa8a6ddcb9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-create-a-vault-for-hyper-v-replication-to-a-secondary-site"></a>5단계: 보조 사이트로의 Hyper-V 복제용 자격 증명 모음 만들기

[Azure Site Recovery](site-recovery-overview.md)를 사용하여 보조 사이트로의 Hyper-V 복제를 위한 온-프레미스 [System Center Virtual Machine Manager(VMM) 서버 및 Hyper-V 호스트/클러스터](vmm-to-vmm-walkthrough-vmm-hyper-v.md)를 준비한 후 Recovery Services 자격 증명 모음을 만들고 복제 시나리오를 선택할 수 있습니다.

이 문서를 읽은 후에 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.


## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>보호 목표 선택

복제할 대상과 복제할 위치를 선택합니다.

1. **Site Recovery** > **1단계: 인프라 준비** > **보호 목표**를 클릭합니다.
2. **대상 복구 사이트**를 선택하고 **예, Hyper-V 사용**을 선택합니다.
3. VMM을 사용하여 Hyper-V 호스트를 관리하도록 지정하려면 **예**를 선택합니다.
4. 보조 VMM 서버가 있는 경우 **예**를 선택합니다. 단일 VMM 서버의 클라우드 간에 복제를 배포하는 경우 **아니요**를 클릭합니다. 그런 후 **OK**를 클릭합니다.

    ![목표 선택](./media/vmm-to-vmm-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>다음 단계

[6단계: 복제 원본 및 대상 설정](vmm-to-vmm-walkthrough-source-target.md)으로 이동합니다.
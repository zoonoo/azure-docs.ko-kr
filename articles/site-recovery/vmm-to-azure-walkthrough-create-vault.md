---
title: "Azure Site Recovery를 사용하여 Azure로의 Hyper-V 복제(System Center VMM 포함)를 위한 자격 증명 모음 설정 | Microsoft 문서"
description: "이 문서에서는 Azure Site Recovery를 사용하여 Azure로의 Hyper-V 복제(VMM 포함)를 위한 자격 증명 모음을 설정하려면 수행해야 하는 단계를 간략하게 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: b3cd6f03-c33c-406d-91d4-5cba69f79abd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: af453ec27ba15ad8c59cf9f544584ad18dc0f74a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-set-up-a-vault-for-hyper-v-replication"></a>7단계: Hyper-V 복제를 위한 자격 증명 모음 설정

이 문서는 자격 증명 모음을 설정하고 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 위치에서 Azure로 복제하려는 항목을 지정하는 방법을 설명합니다.


이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]



## <a name="select-a-protection-goal"></a>보호 목표 선택

복제할 대상과 복제할 위치를 선택합니다.

1. **Recovery Services 자격 증명 모음** > 자격 증명 모음을 클릭합니다.
2. 리소스 메뉴 >에서 **Site Recovery** > **인프라 준비** > **보호 목표**를 클릭합니다.
3. **보호 목표**에서 **Azure에** > **예, Hyper-V 사용**을 선택합니다. **예**를 선택하여 VMM을 사용 중임을 확인합니다. 

     ![목표 선택](./media/vmm-to-azure-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>다음 단계

[8단계: 소스 및 대상 설정](vmm-to-azure-walkthrough-source-target.md)으로 이동합니다.

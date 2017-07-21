---
title: "Azure Site Recovery를 사용하여 Azure로 Hyper-V(System Center VMM 없음) 복제를 위한 자격 증명 모음 설정 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 Hyper-V를 Azure로 복제하기 위해 자격 증명 모음을 설정하는 데 필요한 단계를 요약합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a936b3e2-0dbe-47ac-a98e-5285d96dc786
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 8212ff011633c3a89d3310e828b6d5f1cda6ce3f
ms.contentlocale: ko-kr
ms.lasthandoff: 06/23/2017

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
3. **보호 목표**에서 **Azure에** > **예, Hyper-V 사용**을 선택합니다. **아니요** 를 선택하여 VMM을 사용 중이지 않음을 확인합니다. 

    ![목표 선택](./media/hyper-v-site-walkthrough-create-vault/choose-goals2.png)



## <a name="next-steps"></a>다음 단계

[8단계: 원본 및 대상 설정](hyper-v-site-walkthrough-source-target.md)으로 이동합니다.


---
title: "Azure Site Recovery를 사용하여 Azure로 물리적 서버 복제를 위한 주요 자격 증명 모음 설정 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 물리적 서버를 Azure로 복제하기 위해 자격 증명 모음을 설정하는 데 필요한 단계를 요약합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99f2605-f417-4995-be77-5323136b814f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: deb5ad0495edc969b374795eeb2698326dd4ff4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-set-up-a-vault-for-physical-server-replication-to-azure"></a>6단계: Azure에 물리적 서버 복제를 위한 자격 증명 모음 설정


이 문서에서는 자격 증명 모음을 설정하는 방법을 설명합니다. 자격 증명 모음을 만들고 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 위치에서 Azure로 복제하려는 항목을 지정합니다.


이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.




## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>보호 목표 선택

복제할 대상과 복제할 위치를 선택합니다.

1. **Recovery Services 자격 증명 모음** > 자격 증명 모음을 클릭합니다.
2. 리소스 메뉴에서 **Site Recovery** > **인프라 준비** > **보호 목표**를 클릭합니다.
3. **보호 목표**에서 **Azure에** > **가상화되지 않음/기타**를 선택합니다.


## <a name="next-steps"></a>다음 단계

[7단계: 원본 및 대상 설정](physical-walkthrough-source-target.md)으로 이동합니다.

---
title: "Azure로 Hyper-V 호스트(System Center VMM 없음) 복제 준비 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 Azure로 Hyper-V 호스트 복제를 준비하는 방법 설명"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0f204e24-8d78-4076-95c5-8137d1be9c01
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: e61708f4af0715eaa7147e9cd17113d5979dcdf7
ms.contentlocale: ko-kr
ms.lasthandoff: 06/23/2017

---

# <a name="step-6-prepare-hyper-v-hosts-for-replication-to-azure"></a>6단계: Azure로 Hyper-V 호스트 복제 준비

Azure Site Recovery와 상호 작용하는 온-프레미스 Hyper-V 호스트를 준비하려면 이 문서의 지침을 사용합니다.

이 문서를 읽은 후에는 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 기술적인 질문을 합니다.


## <a name="prepare-hosts"></a>호스트 준비

- Hyper-V 호스트가 [필수 구성 요소](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager)를 충족하는지 확인합니다.
- 호스트가 필요한 URL에 액세스할 수 있는지 확인합니다.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인합니다.
- [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.
- 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요(Access Control 및 ID 관리에 사용됨).

Site Recovery를 배포하는 동안 복제할 VM을 포함하는 Hyper-V 호스트를 Hyper-V 사이트에 추가합니다. Site Recovery 공급자 및 Recovery Services 에이전트가 각 호스트에 설치됩니다. Hyper-V 사이트가 Recovery Services 자격 증명 모음에 등록됩니다.

## <a name="next-steps"></a>다음 단계

[7단계: 자격 증명 모음 만들기](hyper-v-site-walkthrough-create-vault.md)로 이동합니다.



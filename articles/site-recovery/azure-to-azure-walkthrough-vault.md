---
title: "Azure Site Recovery를 사용하여 지역 간 Azure VM 복제를 위한 자격 증명 모음 설정 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 Azure 지역 간 Azure 복제를 위한 자격 증명 모음을 설정하는 데 필요한 단계를 요약하고 있습니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 40472189-3d80-4963-b175-8bddcbc2f61f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: e03d17992ee0b12049636e40188950bcc4a6f31e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2017
---
# <a name="step-4-set-up-a-vault-for-azure-to-azure-replication"></a>4단계: Azure 간 복제를 위한 자격 증명 모음 설정

[네트워크 계획](azure-to-azure-walkthrough-network.md) 후에 이 문서를 사용하여 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 통해 다른 Azure 지역에 복제하는 Azure VM(가상 컴퓨터)에 대한 자격 증명 모음을 설정합니다.

- 이 문서를 완료하면 Recovery Services 자격 증명 모음을 설정해야 합니다.
- 이 문서의 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 질문하세요.



>[!NOTE]
>
> Azure VM 복제는 현재 미리 보기로 제공됩니다.




## <a name="create-a-vault"></a>자격 증명 모음 만들기

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> VM을 복제할 위치에 Recovery Services 자격 증명 모음을 만드는 것이 좋습니다. 예를 들어 대상 위치가 미국 중부인 경우 **미국 중부**에 자격 증명 모음을 만듭니다.


## <a name="next-steps"></a>다음 단계

[5단계: 복제 사용](azure-to-azure-walkthrough-enable-replication.md)으로 이동합니다.

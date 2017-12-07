---
title: "Azure 지역 간에 Azure IaaS VM 마이그레이션 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 Azure 지역 간에 Azure IaaS 가상 컴퓨터를 마이그레이션합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 805582d89ee906e21fbe588e895ce0fe3a926a66
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Azure Site Recovery를 사용하여 Azure 지역 간에 Azure IaaS 가상 컴퓨터 마이그레이션

[Site Recovery](../site-recovery-overview.md) 서비스를 사용하여 Azure 지역 간에 Azure VM(가상 머신)을 마이그레이션하려는 경우 이 문서를 사용하세요.

## <a name="prerequisites"></a>필수 조건

마이그레이션하려는 IaaS VM이 필요합니다.

## <a name="deployment-steps"></a>배포 단계

1. [자격 증명 모음을 만듭니다](azure-to-azure-tutorial-enable-replication.md#create-a-vault).
2. 마이그레이션할 VM에 대해 [복제를 사용하도록 설정](azure-to-azure-tutorial-enable-replication.md#enable-replication)하고 Azure를 원본으로 선택합니다. 현재 관리 디스크를 사용하는 Azure VM의 네이티브 복제는 지원되지 않습니다.
3. [장애 조치(Failover) 실행](../site-recovery-failover.md). 초기 복제가 완료된 후에 Azure 지역 간에 장애 조치를 실행할 수 있습니다. 선택적으로 복구 계획을 만들고 장애 조치를 실행하여 지역 간에 여러 가상 컴퓨터를 마이그레이션할 수 있습니다. [자세히 알아봅니다](../site-recovery-create-recovery-plans.md) .

## <a name="next-steps"></a>다음 단계
[Azure Site Recovery란?](../site-recovery-overview.md)에서 다른 복제 시나리오에 대해 알아보세요.

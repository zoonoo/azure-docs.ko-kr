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
ms.date: 11/27/2017
ms.author: raynew
ms.openlocfilehash: f645150aa7b93ebbd98899cc8fdd495dfeaf3076
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Azure Site Recovery를 사용하여 Azure 지역 간에 Azure IaaS 가상 컴퓨터 마이그레이션

Azure Site Recovery에 오신 것을 환영합니다! Azure 지역 간에 Azure VM을 마이그레이션할 경우 이 문서를 사용합니다.
>[!NOTE]
>
> 재해 복구 및 마이그레이션 요구에 대해 Azure VM을 다른 지역으로 복제하는 방법은 [이 문서](../site-recovery-azure-to-azure.md)를 참조합니다. Azure Virtual Machines에 대한 Site Recovery 복제는 현재 미리 보기로 제공됩니다.


## <a name="prerequisites"></a>필수 조건
이 배포에 대해 필요한 사항은 다음과 같습니다.

* **IaaS 가상 컴퓨터**: 마이그레이션하려는 VM입니다. 이러한 VM을 실제 컴퓨터로 간주하여 마이그레이션합니다.

## <a name="deployment-steps"></a>배포 단계
이 섹션에서는 새 Azure 포털의 배포 단계를 설명합니다.

1. [자격 증명 모음을 만듭니다](../site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. 마이그레이션할 VM에 대해 [복제를 사용하도록 설정].../(site-recovery-azure-to-azure.md)하고 Azure를 원본으로 선택합니다.
  >[!NOTE]
  >
  > 현재 Managed Disks를 사용하는 Azure VM의 네이티브 복제는 지원되지 않습니다. [이 문서](../site-recovery-vmware-to-azure.md)에서 "물리적 서버에서 Azure로" 옵션을 사용하여 Managed Disks에서 VM을 마이그레이션할 수 있습니다.
3. [장애 조치(Failover) 실행](../site-recovery-failover.md). 초기 복제가 완료된 후에 Azure 지역 간에 장애 조치를 실행할 수 있습니다. 선택적으로 복구 계획을 만들고 장애 조치를 실행하여 지역 간에 여러 가상 컴퓨터를 마이그레이션할 수 있습니다. [자세히 알아봅니다](../site-recovery-create-recovery-plans.md) .

## <a name="next-steps"></a>다음 단계
[Azure Site Recovery란?](../site-recovery-overview.md)

---
title: Azure 사이트 복구를 사용하여 고객 관리 사이트 간 재해 복구(VMM 사용) 간 재해 복구 사용 중단 | 마이크로 소프트 문서
description: 하이퍼-V를 사용하는 고객 소유 사이트와 SCVMM에서 Azure로 관리하는 사이트 간 및 대체 옵션 간에 DR의 예정된 사용 중단에 대한 세부 정보
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661673"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Azure 사이트 복구를 사용 하 여 고객 관리 사이트(VMM 사용) 간의 재해 복구 사용 중단

이 문서에서는 다음 시나리오에 대 한 고객에 대 한 사용할 수 있는 곧 된 사용 중단 계획, 해당 의미 및 대체 옵션에 대해 설명 합니다.

사이트 복구를 사용하여 시스템 센터 가상 시스템 관리자(SCVMM)에서 관리하는 고객 소유 사이트 간 DR

> [!IMPORTANT]
> 고객은 환경에 지장을 주지 않도록 가능한 한 빨리 수정 조치를 취하는 것이 좋습니다. 

## <a name="what-changes-should-you-expect"></a>어떤 변화를 예상해야 합니까?

- 2020년 3월부터 Hyper-V VM의 사이트 간 복제가 중단될 예정인 Azure 포털 알림 & 전자 메일 통신을 받게 됩니다. 이 사용 중단은 2023년 3월에 계획되어 있습니다.

- 기존 구성이 있는 경우 설정에 영향을 미치지 않습니다.

- 고객이 대체 방법을 따르지 않는 한 시나리오가 더 이상 사용되지 않으면 기존 복제가 중단될 수 있습니다. 고객은 Azure 포털의 Azure 사이트 복구 환경을 통해 DR 관련 작업을 보거나 관리하거나 수행할 수 없습니다.
 
## <a name="alternatives"></a>대안 

다음은 시나리오가 더 이상 사용되지 않는 DR 전략에 영향을 미치지 않도록 하기 위해 고객이 선택할 수 있는 대안입니다. 

- 옵션 1(권장): [Azure를 DR 대상으로 사용하도록](hyper-v-vmm-azure-tutorial.md)선택합니다.


- 옵션 2: 기본 [Hyper-V 복제 솔루션을](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)사용하여 사이트 간 복제를 계속하도록 선택하지만 Azure Portal에서 Azure 사이트 복구를 사용하여 DR 구성을 관리할 수 없습니다. 


## <a name="remediation-steps"></a>수정 단계

옵션 1로 이동하려는 경우 다음 단계를 실행하십시오.

1. [VM과 연결된 모든 가상 시스템의 보호를 사용하지 않도록 설정합니다.](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario) 복제 비활성화 옵션을 사용하여 **제거하거나** 언급한 스크립트를 실행하여 온-프레미스에서 복제 설정을 정리합니다. 

2. 사이트 간 복제 구성에서 [모든 VMM 서버를 등록 취소합니다.](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)

3. VM 복제를 사용하도록 설정하기 위해 [Azure 리소스를 준비합니다.](tutorial-prepare-azure-for-hyperv.md)
4. [온-프레미스 Hyper-V 서버 준비](hyper-v-prepare-on-premises-tutorial.md)
5. [VMM 클라우드에서 VM에 대한 복제 설정](hyper-v-vmm-azure-tutorial.md)
6. 선택 사항이지만 권장: [DR 드릴 실행](tutorial-dr-drill-azure.md)

하이퍼-V 복제본을 사용하는 옵션 2로 이동하도록 선택한 경우 다음 단계를 실행합니다.

1. **보호된 항목** > **복제 항목에서**컴퓨터를 마우스 오른쪽 단추로 클릭> **복제 를 사용하지 않도록 설정합니다.**
2. **복제 사용 안 함에서** **제거를**선택합니다.

    이 옵션은 Azure Site Recovery에서 복제된 항목을 제거합니다(청구 중지). 온-프레미스 가상 머신에서 복제 구성이 정리되지 **않습니다**. 

## <a name="next-steps"></a>다음 단계
사용 중단을 계획하고 인프라 및 비즈니스에 가장 적합한 대체 옵션을 선택합니다. 이와 관련하여 문의사항이 있는 경우 Microsoft 지원에 문의하십시오.


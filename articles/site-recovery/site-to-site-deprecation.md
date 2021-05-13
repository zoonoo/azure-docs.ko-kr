---
title: Azure Site Recovery를 사용하는 고객 관리형 사이트(VMM 포함) 간 재해 복구 사용 중단 | Microsoft Docs
description: Hyper-V를 사용하는 고객 소유 사이트 간 DR 및 SCVMM에서 관리하는 사이트와 Azure 간 DR에 대한 예정된 사용 중단과 대체 옵션 관련 세부 정보입니다.
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: sharrai
ms.openlocfilehash: 9ffe7a3158b1de6828350947dcf81ef41d08708d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87421844"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Azure Site Recovery를 사용하는 고객 관리형 사이트(VMM 포함) 간 재해 복구 사용 중단

이 문서에서는 다음 시나리오의 고객에 사용할 수 있는 예정된 사용 중단 플랜과 사용 중단으로 인한 영향, 대체 옵션에 대해 설명합니다.

Site Recovery를 통해 SCVMM(System Center Virtual Machine Manager)에서 관리하는 고객 소유 사이트 간 DR

> [!IMPORTANT]
> 고객은 최대한 빨리 수정 단계를 수행하여 해당 환경에서 중단을 방지하는 것이 좋습니다. 

## <a name="what-changes-should-you-expect"></a>변경되는 사항

- 2020년 3월부터 Hyper-V VM의 사이트 간 복제에 대한 예정된 사용 중단을 알리는 Azure Portal 알림 및 메일 커뮤니케이션을 받게 됩니다. 사용 중단은 2023년 3월로 예정되어 있습니다.

- 기존 구성이 있는 경우 설정에 영향을 주지 않습니다.

- 고객이 대체 방법을 따르지 않는 경우 시나리오가 사용되지 않게 되면 기존 복제가 중단될 수 있습니다. 고객이 Azure Portal에서 Azure Site Recovery 환경을 통해 DR 관련 작업을 보거나 관리하거나 수행할 수 없게 됩니다.
 
## <a name="alternatives"></a>대안 

다음은 시나리오가 사용되지 않게 되어도 DR 전략에 영향을 주지 않도록 하기 위해 고객이 선택할 수 있는 대체 방법입니다. 

- 옵션 1(권장): [DR 대상으로 Azure 사용을 시작](hyper-v-vmm-azure-tutorial.md)하도록 선택합니다.


- 옵션 2: 기본 [Hyper-V 복제본 솔루션](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)을 사용하여 사이트 간 복제를 계속하도록 선택합니다. 하지만 Azure Portal에서 Azure Site Recovery를 통해 DR 구성을 관리할 수는 없습니다. 


## <a name="remediation-steps"></a>수정 단계

옵션 1을 선택하는 경우 다음 단계를 실행하세요.

1. [VMM과 연결된 모든 가상 머신의 보호를 사용하지 않도록 설정](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario)합니다. **복제 사용 안 함 및 제거** 옵션을 사용하거나 언급된 스크립트를 실행하여 온-프레미스의 복제 설정이 정리되었는지 확인합니다. 

2. 사이트 간 복제 구성에서 [모든 VMM 서버를 등록 취소](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)합니다.

3. VM 복제를 사용하도록 설정하기 위해 [Azure 리소스를 준비](tutorial-prepare-azure-for-hyperv.md)합니다.
4. [온-프레미스 Hyper-V 서버 준비](hyper-v-prepare-on-premises-tutorial.md)
5. [VMM 클라우드에서 VM 복제를 설정](hyper-v-vmm-azure-tutorial.md)합니다.
6. 선택 사항이지만 권장됩니다. [DR 드릴을 실행](tutorial-dr-drill-azure.md)합니다.

Hyper-V 복제본을 사용하는 옵션 2를 선택하는 경우 다음 단계를 실행하세요.

1. **보호된 항목** > **복제된 항목** 에서 컴퓨터를 마우스 오른쪽 단추로 클릭한 후 **복제 사용 안 함** 을 클릭합니다.
2. **복제 사용 안 함** 에서 **제거** 를 선택합니다.

    이 옵션은 Azure Site Recovery에서 복제된 항목을 제거합니다(청구 중지). 온-프레미스 가상 머신에서 복제 구성이 정리되지 **않습니다**. 

## <a name="next-steps"></a>다음 단계
사용 중단을 계획하고 현재 인프라 및 비즈니스에 가장 적합한 대체 옵션을 선택합니다. 이 사용 중단에 대한 문의 사항이 있는 경우 Microsoft 지원에 문의하세요.


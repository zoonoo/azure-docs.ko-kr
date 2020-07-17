---
title: Azure Site Recovery를 사용 하 여 고객이 관리 하는 사이트 (VMM) 간의 재해 복구 사용 중단 Microsoft Docs
description: Hyper-v를 사용 하는 고객 소유 사이트와 SCVMM에서 관리 하는 사이트와 Azure에 대 한 대체 옵션 간 DR의 향후 사용 중단에 대 한 세부 정보
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 501cf161a22aaa1ec37abda2b750106b6eaf3dc4
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135221"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Azure Site Recovery를 사용 하 여 고객이 관리 하는 사이트 (VMM) 간의 재해 복구 사용 중단

이 문서에서는 향후 사용 중단 계획, 해당 하는 영향 및 다음 시나리오에 대 한 고객에 게 제공 되는 대체 옵션을 설명 합니다.

Site Recovery를 사용 하 여 System Center Virtual Machine Manager (SCVMM)에서 관리 하는 고객 소유 사이트 간 DR

> [!IMPORTANT]
> 고객은 해당 환경에 대 한 중단을 방지 하기 위해 가장 빨리 수정 단계를 수행 하는 것이 좋습니다. 

## <a name="what-changes-should-you-expect"></a>어떤 변경이 필요한가?

- 2020 년 3 월부터 Hyper-v Vm에 대 한 사이트 간 복제의 예정 된 기능을 사용 하 여 전자 메일 통신 & Azure Portal 알림을 받게 됩니다. 사용 중단은 3 월 2023에 예정 되어 있습니다.

- 기존 구성이 있는 경우 설정에 영향을 주지 않습니다.

- 고객이 대체 방법을 사용 하지 않는 한 시나리오를 사용 하지 않으면 기존 복제가 중단 될 수 있습니다. 고객은 Azure Portal에서 Azure Site Recovery 환경을 통해 DR 관련 작업을 보거나 관리 하거나 수행할 수 없습니다.
 
## <a name="alternatives"></a>대안 

다음은 시나리오가 더 이상 사용 되지 않는 경우 고객이 DR 전략에 영향을 주지 않도록 하기 위해 선택할 수 있는 대안입니다. 

- 옵션 1 (권장): [DR 대상으로 Azure를 사용 하 여 시작](hyper-v-vmm-azure-tutorial.md)하도록 선택 합니다.


- 옵션 2: 기본 [Hyper-v 복제본 솔루션](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)을 사용 하 여 사이트 간 복제를 계속 하도록 선택 하지만 Azure Portal에서 Azure Site Recovery를 사용 하 여 DR 구성을 관리할 수는 없습니다. 


## <a name="remediation-steps"></a>수정 단계

옵션 1로 이동 하도록 선택 하는 경우 다음 단계를 실행 하십시오.

1. [VMMs와 연결 된 모든 가상 머신의 보호를 사용 하지 않도록 설정](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario)합니다. **복제 사용 안 함 및 제거** 옵션을 사용 하거나 언급 된 스크립트를 실행 하 여 온-프레미스의 복제 설정이 정리 되었는지 확인 합니다. 

2. [모든 VMM 서버](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) 를 사이트 간 복제 구성에서 등록 취소 합니다.

3. Vm의 복제를 사용 하도록 설정 하기 위해 [Azure 리소스를 준비](tutorial-prepare-azure-for-hyperv.md) 합니다.
4. [온-프레미스 Hyper-V 서버 준비](hyper-v-prepare-on-premises-tutorial.md)
5. [VMM 클라우드에서 Vm에 대 한 복제 설정](hyper-v-vmm-azure-tutorial.md)
6. 선택 사항 이지만 권장 사항: [DR 드릴 실행](tutorial-dr-drill-azure.md)

Hyper-v 복제본 사용의 옵션 2로 이동 하도록 선택 하는 경우 다음 단계를 실행 합니다.

1. **보호 된 항목**  >  **복제 된 항목**에서 컴퓨터를 마우스 오른쪽 단추로 클릭 하 > 복제를 **사용 하지 않도록 설정**합니다.
2. **복제 사용 안 함**에서 **제거**를 선택 합니다.

    이 옵션은 Azure Site Recovery에서 복제된 항목을 제거합니다(청구 중지). 온-프레미스 가상 머신에서 복제 구성이 정리되지 **않습니다**. 

## <a name="next-steps"></a>다음 단계
사용 중단을 계획 하 고 인프라 및 비즈니스에 가장 적합 한 대체 옵션을 선택 합니다. 이에 대 한 쿼리가 있는 경우 Microsoft 지원에 도달 하세요.


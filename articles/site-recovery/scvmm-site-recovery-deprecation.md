---
title: Hyper-v를 사용 하는 고객 소유 사이트와 SCVMM에서 관리 하는 사이트 간의 DR이 Azure로 예정 된 경우 Microsoft Docs
description: Hyper-v를 사용 하는 고객 소유 사이트와 SCVMM에서 관리 하는 사이트와 Azure에 대 한 대체 옵션 간 DR의 향후 사용 중단에 대 한 세부 정보
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/13/2019
ms.author: rajanaki
ms.openlocfilehash: c126d72720c16d4ba869156e86a6e60110b2c31b
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847487"
---
# <a name="upcoming-deprecation-of-dr-between-customer-owned-sites-using-hyper-v-and-between-sites-managed-by-scvmm-to-azure"></a>Hyper-v를 사용 하는 고객 소유 사이트와 SCVMM에서 관리 하는 사이트 간 Azure 간 DR의 사용 중단 예정

이 문서에서는 예정 된 사용 중단 계획, 해당 하는 영향 및 Azure Site Recovery에서 지원 되는 다음 시나리오에 대 한 고객의 대체 옵션을 설명 합니다. 

- 고객 소유 사이트 간에 SCVMM에서 관리 하는 Hyper-v Vm 간의 DR 
- SCVMM에서 Azure로 관리 되는 Hyper-v Vm의 DR

> [!IMPORTANT]
> 이러한 시나리오는 현재 사용 중단으로 표시 되어 있으며, 고객은 해당 환경에 대 한 중단을 방지 하기 위해 가장 빨리 수정 단계를 수행 해야 합니다. 
 

## <a name="what-changes-should-you-expect"></a>어떤 변경이 필요한가?

- 11 월 2019 부터는 이러한 시나리오에 대해 새로운 사용자 온-보드를 사용할 수 없습니다. 장애 조치, 테스트 장애 조치, 모니터링 등을 비롯 한 **기존 복제 및 관리 작업** 에 **는 영향을 주지**않습니다.

- 시나리오가 더 이상 사용 되지 않으면 고객이 권장 단계를 수행 하지 않는 한 다음과 같은 의미가 있습니다.

    - 고객 소유 사이트 간에 SCVMM에서 관리 하는 Hyper-v Vm 간 DR: 복제는 계속 작동 하지만 Hyper-v 복제본의 기본 기능은 계속 작동 하지만, 고객은 DR 관련 작업을 보거나 관리 하거나 수행할 수 없게 됩니다. Azure Portal에서 Azure의 복구 경험을 통해 
    - Azure에 대해 SCVMM에서 관리 하는 Hyper-v Vm의 DR: 기존 복제는 중단 되 고 고객은 Azure Site Recovery를 통해 DR 관련 작업을 보거나 관리 하거나 수행할 수 없습니다.


## <a name="recommended-actions-to-be-taken"></a>권장 조치 수행

다음은 시나리오가 더 이상 사용 되지 않는 경우 사용자가 DR 전략에 영향을 주지 않도록 하는 옵션입니다. 

- [Hyper-v 호스트의 vm에 대 한 DR 대상으로 Azure 사용을 시작](hyper-v-azure-tutorial.md)하도록 선택 합니다.

> [!IMPORTANT]
> 온-프레미스 환경에는 여전히 SCVMM이 있지만 Hyper-v 호스트에 대 한 참조로 ASR를 구성 합니다.

- 사이트 간 복제를 계속 하 되 기본 [Hyper-v 복제본 솔루션](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)을 사용 하도록 선택 하지만 Azure Portal에서 Azure Site Recovery를 사용 하 여 DR 구성을 관리할 수 없습니다. 


## <a name="next-steps"></a>다음 단계
사용 중단을 계획 하 고 인프라 및 비즈니스에 가장 적합 한 대체 옵션을 선택 합니다. 이에 대 한 쿼리가 있는 경우에는에 문의 하세요 Microsoft 지원


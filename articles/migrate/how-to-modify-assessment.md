---
title: "Azure Migrate 평가 설정 사용자 지정 | Microsoft Docs"
description: "Azure Migration Planner를 사용하여 VMware VM을 Azure에 마이그레이션하기 위해 평가를 설정 및 실행하는 방법을 설명합니다."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: ce47790f6214864afdba33eb5cbe3a9e49b81cd5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2017
---
# <a name="customize-an-assessment"></a>평가 사용자 지정

[Azure Migrate](migrate-overview.md)는 기본 설정으로 평가를 만듭니다. 평가를 만든 후에는 이 문서의 지침을 사용하여 기본 설정을 수정할 수 있습니다.


## <a name="edit-assessment-values"></a>평가 값 편집

1. Azure Migrate 프로젝트 **평가** 페이지에서 평가를 선택하고 **속성 편집**을 클릭합니다.
2. 아래 표에 따라 설정을 수정합니다.

    **설정** | **세부 정보** | **기본값**
    --- | --- | ---
    **대상 위치** | 마이그레이션할 Azure 위치입니다. |  미국 서부 2가 기본 위치입니다.
    **저장소 이중화** | 마이그레이션 후 Azure VM이 사용하게 될 저장소 유형입니다. | 현재 [LRS(로컬 중복 저장소)](../storage/common/storage-redundancy.md#locally-redundant-storage) 복제만 지원합니다.
    **쾌적 인자** | 쾌적 인자는 평가 중 사용할 버퍼입니다. 계절 사용량, 단기 성능 이력 등, 향후 사용이 증가할 가능성이 있는 항목에 대해 사용합니다. | 기본 설정은 1.3x입니다.
    **성능 기록** | 성능 기록 계산에 사용된 시간입니다. | 기본값은 1개월입니다.
    **백분위 수 사용률** | 성능 기록에 고려할 백분위 수 값입니다. | 기본값은 95%입니다.
    **가격 책정 계층** | VM에 대해 [가격 책정 계층](https://azure.microsoft.com/blog/basic-tier-virtual-machines-2/)을 지정할 수 있습니다.  | 기본적으로 [표준](../virtual-machines/windows/sizes-general.md) 계층이 사용됩니다.
    **제안** | 적용되는 [Azure 제안](https://azure.microsoft.com/support/legal/offer-details/)입니다. | [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/)가 기본값입니다.
    **통화** | 청구 통화입니다. | 기본값은 미국 달러입니다.
    **할인(%)** | 모든 제안에 적용되는 구독 특정 할인입니다. | 기본 설정은 0%입니다.
    **AHUB(Azure Hybrid Use Benefit)** | [AHUB(Azure Hybrid Use Benefit)](https://azure.microsoft.com/pricing/hybrid-use-benefit/) 등록 여부를 표시합니다. Yes로 설정하면 Windows 외 Azure 가격이 Windows VM에 적용됩니다. | 기본값은 [예]입니다.

3. **저장**을 클릭하여 평가를 업데이트합니다.


## <a name="next-steps"></a>다음 단계

평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).

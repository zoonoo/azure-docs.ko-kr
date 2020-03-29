---
title: Azure 마이그레이션 서버 평가를 사용하여 평가 만들기 | 마이크로 소프트 문서
description: Azure 마이그레이션 서버 평가 도구를 사용하여 평가를 만드는 방법에 대해 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68229103"
---
# <a name="create-an-assessment"></a>평가 만들기

이 문서에서는 Azure 마이그레이션을 사용하여 온-프레미스 VM 또는 하이퍼 VM에 대한 평가를 만드는 방법에 대해 설명합니다.

[Azure 마이그레이션을](migrate-services-overview.md) 사용하면 Azure로 마이그레이션할 수 있습니다. Azure Migrate는 온-프레미스 인프라, 애플리케이션 및 데이터의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다. 

## <a name="before-you-start"></a>시작하기 전에

- Azure 마이그레이션 프로젝트를 [만들었는지](how-to-add-tool-first-time.md) 확인합니다.
- 프로젝트를 이미 만든 경우 Azure 마이그레이션: 서버 평가 도구를 [추가했는지](how-to-assess.md) 확인합니다.
- 평가를 만들려면 [VMware](how-to-set-up-appliance-vmware.md) 또는 [Hyper-V](how-to-set-up-appliance-hyper-v.md)에 대한 Azure 마이그레이션 어플라이언스를 설정해야 합니다. 어플라이언스는 온-프레미스 컴퓨터를 검색하고 Azure 마이그레이션: 서버 평가로 메타데이터 및 성능 데이터를 보냅니다. [자세히 알아봅니다](migrate-appliance.md).


## <a name="assessment-overview"></a>평가 개요
Azure 마이그레이션을 사용하여 만들 수 있는 평가에는 서버 평가라는 두 가지 유형이 있습니다.

**평가** | **세부 정보** | **데이터**
--- | --- | ---
**성능 기반** | 수집된 성능 데이터를 기반으로 하는 평가 | **권장 VM 크기**: CPU 및 메모리 사용률 데이터를 기반으로 합니다.<br/><br/> **권장 디스크 유형(표준 또는 프리미엄 관리 디스크)**: 온-프레미스 디스크의 IOPS 및 처리량을 기반으로 합니다.
**온-프레미스로** | 온-프레미스 크기 조정을 기반으로 하는 평가 | **권장 VM 크기**: 온-프레미스 VM 크기에 따라<br/><br> **권장 디스크 유형**: 평가에 대해 선택한 저장소 유형 설정에 따라 설정됩니다.

평가에 대해 [자세히 알아보세요.](concepts-assessment-calculation.md)

## <a name="run-an-assessment"></a>평가 실행

다음과 같이 평가를 실행합니다.

1. 평가를 만드는 방법에 대한 [모범 사례](best-practices-assessment.md)를 검토합니다.
2. **서버** 탭에서 Azure **마이그레이션: 서버 평가** 타일에서 **평가를**클릭합니다.

    ![평가](./media/how-to-create-assessment/assess.png)

2. 서버 평가에서 평가의 이름을 **지정합니다.**
3. 평가 속성을 검토하려면 **모두 보기**를 클릭합니다.

    ![평가 속성](./media/how-to-create-assessment//view-all.png)

3. **그룹 선택 또는 작성에서**새로 **만들기를**선택하고 그룹 이름을 지정합니다. 그룹은 평가를 위해 하나 이상의 VM을 수집합니다.
4. **그룹에 머신 추가**에서 그룹에 추가할 VM을 선택합니다.
5. **평가 만들기**를 클릭하여 그룹을 만들고, 평가를 실행합니다.

    ![평가 만들기](./media/how-to-create-assessment//assessment-create.png)

6. 평가를 만든 후 **Azure** > **마이그레이션 서버: 서버 평가** > 평가에서 평가를**봅니다.**
7. **평가 내보내기**를 클릭하고, Excel 파일로 다운로드합니다.



## <a name="review-an-assessment"></a>평가 검토

평가에서 설명하는 항목은 다음과 같습니다.

- **Azure 준비:** VM이 Azure로 마이그레이션하는 데 적합한지 여부입니다.
- **월별 비용 추정**: Azure에서 VM을 실행하기 위한 예상 월별 계산 및 저장소 비용입니다.
- **월별 스토리지 비용 추정**: 마이그레이션 후 디스크 저장소에 대한 예상 비용입니다.

### <a name="view-an-assessment"></a>평가 보기

1. **마이그레이션 목표** >  **서버에서**Azure **마이그레이션의 평가를** **클릭합니다.**
2. **평가**에서 해당 평가를 클릭하여 엽니다.

    ![평가 요약](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 준비 상태 검토

1. **Azure 준비 상태**에서 VM이 Azure로 마이그레이션할 준비가 되었는지 확인합니다.
2. VM 상태를 검토합니다.
    - **Azure에 대한 준비**: Azure 마이그레이션은 평가에서 VM에 대한 VM 크기 및 비용 예측을 권장합니다.
    - **조건 준비**: 문제 및 제안된 수정 을 표시합니다.
    - **Azure에 대한 준비가 되지 않음**: 문제 및 제안된 수정 을 표시합니다.
    - **준비 없음**: Azure 마이그레이션에서 데이터 가용성 문제로 인해 준비 상태를 평가할 수 없는 경우에 사용됩니다.

2. **Azure 준비 상태** 상태를 클릭합니다. VM 준비 상태 세부 정보를 살펴보고, 드릴다운하여 컴퓨팅, 스토리지 및 네트워크 설정을 포함한 VM 세부 정보를 확인할 수 있습니다.



### <a name="review-cost-details"></a>비용 세부 정보 검토

이 보기에서는 Azure에서 VM을 실행하는 데 들어가는 예상 컴퓨팅 및 스토리지 비용을 표시합니다.

1. 월간 컴퓨팅 및 스토리지 비용을 검토합니다. 비용은 평가된 그룹의 모든 VM에 대해 집계됩니다.

    - 예상 비용은 머신의 크기 추천 사항과 해당 디스크 및 속성을 기반으로 합니다.
    - 컴퓨팅 및 스토리지의 월간 예상 비용이 표시됩니다.
    - 예상 비용은 온-프레미스 VM을 IaaS VM으로 실행하는 데 들어가는 비용입니다. Azure Migrate 서버 평가에서는 PaaS 또는 SaaS 비용을 고려하지 않습니다.

2. 월간 예상 스토리지 비용을 검토할 수 있습니다. 이 보기에는 평가된 그룹에 대해 집계된 스토리지 비용이 표시되며, 해당 그룹은 여러 유형의 스토리지 디스크로 분할되어 있습니다.
3. 드릴다운하여 특정 VM에 대한 세부 정보를 확인할 수 있습니다.


### <a name="review-confidence-rating"></a>신뢰 등급 검토

성능 기반 평가를 실행하면 신뢰 등급이 평가에 할당됩니다.

![신뢰 등급](./media/how-to-create-assessment/confidence-rating.png)

- 별 1개(최저)에서 별 5개(최고)까지의 등급입니다.
- 신뢰 등급을 통해 평가에서 제공하는 크기 추천 사항의 안정성을 예측할 수 있습니다.
- 신뢰 등급은 평가를 계산하는 데 필요한 데이터 요소의 가용성을 기반으로 합니다.

평가에 대한 신뢰 등급은 다음과 같습니다.

**데이터 요소 가용성** | **신뢰 등급**
--- | ---
0%-20% | 별 1개
21%-40% | 별 2개
41%-60% | 별 3개
61%-80% | 별 4개
81%-100% | 별 5개




## <a name="next-steps"></a>다음 단계

- 신뢰도가 높은 그룹을 만드는 데 [종속성 매핑을](how-to-create-group-machine-dependencies.md) 사용하는 방법을 알아봅니다.
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).

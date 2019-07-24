---
title: Azure Migrate Server 평가를 사용 하 여 평가 만들기 | Microsoft Docs
description: Azure Migrate Server 평가 도구를 사용 하 여 평가를 만드는 방법을 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229103"
---
# <a name="create-an-assessment"></a>평가 만들기

이 문서에서는 Azure Migrate를 사용 하 여 온-프레미스 VMware Vm 또는 Hyper-v Vm에 대 한 평가를 만드는 방법을 설명 합니다. 서버 평가.

[Azure Migrate](migrate-services-overview.md) 는 Azure로 마이그레이션하는 데 도움이 됩니다. Azure Migrate는 Azure에 대 한 온-프레미스 인프라, 응용 프로그램 및 데이터의 검색, 평가 및 마이그레이션을 추적 하는 중앙 허브를 제공 합니다. 허브는 평가 및 마이그레이션과 타사 ISV (독립 소프트웨어 공급 업체) 제품에 대 한 Azure 도구를 제공 합니다. 

## <a name="before-you-start"></a>시작하기 전에

- Azure Migrate 프로젝트를 [만들었는지](how-to-add-tool-first-time.md) 확인 합니다.
- 프로젝트를 이미 만든 경우 Azure Migrate을 [추가](how-to-assess.md) 했는지 확인 합니다. 서버 평가 도구입니다.
- 평가를 만들려면 [VMware](how-to-set-up-appliance-vmware.md) 또는 [hyper-v](how-to-set-up-appliance-hyper-v.md)에 대 한 Azure Migrate 어플라이언스를 설정 해야 합니다. 어플라이언스는 온-프레미스 컴퓨터를 검색 하 고 메타 데이터 및 성능 데이터를 Azure Migrate으로 보냅니다. 서버 평가. [자세히 알아보기](migrate-appliance.md).


## <a name="assessment-overview"></a>평가 개요
Azure Migrate를 사용 하 여 만들 수 있는 평가에는 두 가지 유형이 있습니다. 서버 평가.

**평가** | **세부 정보** | **데이터**
--- | --- | ---
**성능 기반** | 수집 된 성능 데이터를 기반으로 하는 평가 | **권장 VM 크기**: CPU 및 메모리 사용률 데이터를 기반으로 합니다.<br/><br/> **권장 디스크 유형 (standard 또는 premium managed disk)** : 온-프레미스 디스크의 IOPS 및 처리량을 기준으로 합니다.
**온-프레미스로** | 온-프레미스 크기 조정을 기반으로 하는 평가 | **권장 VM 크기**: 온-프레미스 VM 크기 기반<br/><br> **권장 디스크 유형**: 평가를 위해 선택한 저장소 유형 설정에 따라 달라 집니다.

평가에 대해 자세히 [알아보세요](concepts-assessment-calculation.md) .

## <a name="run-an-assessment"></a>평가 실행

다음과 같이 평가를 실행 합니다.

1. 평가를 만드는 방법에 대 한 [모범 사례](best-practices-assessment.md) 를 검토 합니다.
2. **서버** 탭의 Azure Migrate에서 **다음을 수행 합니다. 서버 평가** 타일에서 **평가**를 클릭 합니다.

    ![평가](./media/how-to-create-assessment/assess.png)

2. **서버 평가**에서 평가의 이름을 지정 합니다.
3. 평가 속성을 검토하려면 **모두 보기**를 클릭합니다.

    ![평가 속성](./media/how-to-create-assessment//view-all.png)

3. **그룹 선택 또는 만들기**에서 **새로 만들기**를 선택 하 고 그룹 이름을 지정 합니다. 그룹은 평가를 위해 하나 이상의 Vm을 함께 수집 합니다.
4. **그룹에 컴퓨터 추가**에서 그룹에 추가할 vm을 선택 합니다.
5. **평가 만들기** 를 클릭 하 여 그룹을 만들고 평가를 실행 합니다.

    ![평가 만들기](./media/how-to-create-assessment//assessment-create.png)

6. 평가를 만든 후에 Azure Migrate **서버** > **에서 확인 합니다. 서버 평가** > **평가**.
7. **평가 내보내기**를 클릭하고, Excel 파일로 다운로드합니다.



## <a name="review-an-assessment"></a>평가 검토

평가에서는 다음을 설명 합니다.

- **Azure 준비 상태**: Vm이 Azure로 마이그레이션하기에 적합 한지 여부
- **월간 비용 예측**: Azure에서 Vm을 실행 하기 위한 예상 월별 계산 및 저장소 비용입니다.
- **월별 저장소 비용 예측**: 마이그레이션 후의 디스크 저장소에 대 한 예상 비용입니다.

### <a name="view-an-assessment"></a>평가 보기

1. **마이그레이션 목표** >  **서버에서 Azure Migrate 평가**를 클릭 합니다.   **서버 평가**.
2. 평가 에서 평가를 클릭 하 여 엽니다.

    ![평가 요약](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 준비 상태 검토

1. **Azure 준비 상태**에서 Vm이 azure로 마이그레이션할 준비가 되었는지 여부를 확인 합니다.
2. VM 상태를 검토 합니다.
    - **Azure 준비**: Azure Migrate VM 크기 및 평가에서 vm에 대 한 예상 비용을 권장 합니다.
    - **조건 준비**: 문제 및 제안 된 수정 사항을 보여 줍니다.
    - **Azure에 대해 준비 되지 않음**: 문제 및 제안 된 수정 사항을 보여 줍니다.
    - **준비 상태 알 수 없음**: 데이터 가용성 문제로 인해 Azure Migrate 준비 상태를 평가할 수 없는 경우에 사용 됩니다.

2. **Azure 준비** 상태를 클릭 합니다. VM 준비 정보를 확인 하 고 드릴 다운 하 여 계산, 저장소 및 네트워크 설정을 비롯 한 VM 세부 정보를 볼 수 있습니다.



### <a name="review-cost-details"></a>비용 정보 검토

이 보기에는 Azure에서 실행 중인 Vm의 예상 계산 및 저장소 비용이 표시 됩니다.

1. 월별 계산 및 저장소 비용을 검토 합니다. 비용은 평가 그룹의 모든 Vm에 대해 집계 됩니다.

    - 예상 비용은 컴퓨터와 해당 디스크 및 속성에 대 한 권장 크기를 기준으로 합니다.
    - 계산 및 저장소에 대해 예상 되는 월별 비용이 표시 됩니다.
    - 비용 예측은 IaaS Vm으로 온-프레미스 Vm을 실행 하는 것입니다. Azure Migrate Server 평가는 PaaS 또는 SaaS 비용을 고려 하지 않습니다.

2. 월별 저장소 비용 예측을 검토할 수 있습니다. 이 보기는 평가 그룹에 대 한 집계 된 저장소 비용을 다양 한 유형의 저장소 디스크로 분할 하 여 보여 줍니다.
3. 드릴 다운 하 여 특정 Vm에 대 한 세부 정보를 볼 수 있습니다.


### <a name="review-confidence-rating"></a>신뢰 등급 검토

성능 기반 평가를 실행할 때 신뢰성 등급이 평가에 할당 됩니다.

![신뢰 등급](./media/how-to-create-assessment/confidence-rating.png)

- 별 1 개 (가장 낮음)에서 5 별 (가장 높음) 까지의 등급을 획득 합니다.
- 신뢰 등급을 통해 평가에서 제공 되는 크기 권장 사항의 안정성을 예측할 수 있습니다.
- 신뢰 등급은 평가를 계산 하는 데 필요한 데이터 요소의 가용성을 기준으로 합니다.

평가에 대 한 신뢰 등급은 다음과 같습니다.

**데이터 요소 가용성** | **신뢰 등급**
--- | ---
0%-20% | 별 1개
21%-40% | 별 2개
41%-60% | 별 3개
61%-80% | 별 4개
81%-100% | 별 5개




## <a name="next-steps"></a>다음 단계

- [종속성 매핑을](how-to-create-group-machine-dependencies.md) 사용 하 여 높은 신뢰도 그룹을 만드는 방법에 대해 알아봅니다.
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).

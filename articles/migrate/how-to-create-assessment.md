---
title: Azure Migrate Server 평가를 사용 하 여 Azure VM 평가 만들기 | Microsoft Docs
description: Azure Migrate Server 평가 도구를 사용 하 여 Azure VM 평가를 만드는 방법을 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: ec95cde1f023b4d034c2fae9cc5a54744ccdc9a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85549816"
---
# <a name="create-an-azure-vm-assessment"></a>Azure VM 평가 만들기

이 문서에서는 Azure Migrate: 서버 평가를 사용 하 여 온-프레미스 VMware Vm 또는 Hyper-v Vm에 대 한 Azure VM 평가를 만드는 방법을 설명 합니다.

[Azure Migrate](migrate-services-overview.md) 는 Azure로 마이그레이션하는 데 도움이 됩니다. Azure Migrate는 온-프레미스 인프라, 애플리케이션 및 데이터의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다. 

## <a name="before-you-start"></a>시작하기 전에

- Azure Migrate 프로젝트를 [만들었는지](how-to-add-tool-first-time.md) 확인 합니다.
- 프로젝트를 이미 만든 경우 Azure Migrate: 서버 평가 도구를 [추가](how-to-assess.md) 했는지 확인 합니다.
- 평가를 만들려면 [VMware](how-to-set-up-appliance-vmware.md) 또는 [hyper-v](how-to-set-up-appliance-hyper-v.md)에 대 한 Azure Migrate 어플라이언스를 설정 해야 합니다. 어플라이언스는 온-프레미스 컴퓨터를 검색 하 고 메타 데이터 및 성능 데이터를 Azure Migrate: 서버 평가로 보냅니다. [자세히 알아보기](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Azure VM 평가 개요
Azure Migrate: 서버 평가를 사용 하 여 Azure VM 평가를 만드는 데 사용할 수 있는 두 가지 유형의 크기 조정 기준이 있습니다.

**평가** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 수집된 성능 데이터를 기반으로 하는 평가 | **추천 VM 크기**: CPU 및 메모리 사용률 데이터를 기반으로 합니다.<br/><br/> **추천 디스크 유형(표준 또는 프리미엄 관리 디스크)** : 온-프레미스 디스크의 IOPS 및 처리량을 기반으로 합니다.
**온-프레미스로** | 온-프레미스 크기 조정을 기반으로 하는 평가 | **추천 VM 크기**: 온-프레미스 VM 크기를 기반으로 합니다.<br/><br> **추천 디스크 유형**: 평가를 위해 선택한 스토리지 유형 설정을 기반으로 합니다.

평가에 대해 [자세히 알아보세요](concepts-assessment-calculation.md).

## <a name="run-an-assessment"></a>평가 실행

다음과 같이 평가를 실행합니다.

1. 평가를 만드는 방법에 대한 [모범 사례](best-practices-assessment.md)를 검토합니다.
2. **서버** 탭의 **Azure Migrate: 서버 평가** 타일에서 **평가**를 클릭합니다.

    ![평가](./media/how-to-create-assessment/assess.png)

3. **서버 평가**에서 평가 유형을 "Azure VM"으로 선택 하 고, 검색 원본을 선택 하 고, 평가 이름을 지정 합니다.

    ![평가 기본 사항](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. 평가 속성을 검토하려면 **모두 보기**를 클릭합니다.

    ![평가 속성](./media/how-to-create-assessment//view-all.png)

5. **다음** 을 클릭 하 여 **평가할 컴퓨터를 선택**합니다. **그룹 선택 또는 만들기**에서 **새로 만들기**를 선택하고 그룹 이름을 지정합니다. 그룹은 평가를 위해 하나 이상의 VM을 수집합니다.
6. **그룹에 머신 추가**에서 그룹에 추가할 VM을 선택합니다.
7. **다음** 을 클릭 하 여 평가 단계 **를 검토 하** 고 평가 세부 정보를 검토 합니다.
8. **평가 만들기**를 클릭하여 그룹을 만들고, 평가를 실행합니다.

    ![평가 만들기](./media/how-to-create-assessment//assessment-create.png)

9. 평가가 만들어지면 **서버** > **Azure Migrate: 서버 평가** > **평가**에서 해당 평가를 확인합니다.
10. **평가 내보내기**를 클릭하고, Excel 파일로 다운로드합니다.



## <a name="review-an-azure-vm-assessment"></a>Azure VM 평가 검토

Azure VM 평가는 다음을 설명 합니다.

- **Azure 준비 상태**: VM이 Azure로 마이그레이션하는 데 적합한지 여부입니다.
- **월간 예상 비용**: Azure에서 VM을 실행하는 데 들어가는 월간 예상 컴퓨팅 및 스토리지 비용입니다.
- **월간 예상 스토리지 비용**: 마이그레이션 후 디스크 스토리지에 대한 예상 비용입니다.

### <a name="view-an-azure-vm-assessment"></a>Azure VM 평가 보기

1. **마이그레이션 목표** >  **서버**의 **Azure Migrate: 서버 평가**에서 **평가**를 클릭합니다.
2. **평가**에서 해당 평가를 클릭하여 엽니다.

    ![평가 요약](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 준비 상태 검토

1. **Azure 준비 상태**에서 VM이 Azure로 마이그레이션할 준비가 되었는지 확인합니다.
2. VM 상태를 검토합니다.
    - **Azure 준비 완료**: Azure Migrate는 평가에서 VM의 크기 및 예상 비용을 추천합니다.
    - **조건과 함께 준비 완료**: 문제 및 제안된 수정 사항을 보여 줍니다.
    - **Azure를 사용할 준비 안 됨**: 문제 및 제안된 수정 사항을 보여 줍니다.
    - **알 수 없는 준비**: 데이터 가용성 문제로 인해 Azure Migrate에서 준비 상태를 평가할 수 없는 경우에 사용됩니다.

3. **Azure 준비 상태** 상태를 클릭합니다. VM 준비 상태 세부 정보를 살펴보고, 드릴다운하여 컴퓨팅, 스토리지 및 네트워크 설정을 포함한 VM 세부 정보를 확인할 수 있습니다.



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

- [종속성 매핑을](how-to-create-group-machine-dependencies.md) 사용 하 여 높은 신뢰도 그룹을 만드는 방법에 대해 알아봅니다.
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).

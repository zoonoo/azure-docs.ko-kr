---
title: Azure Migrate 검색 및 평가 도구를 사용 하 여 Azure VM 평가 만들기 | Microsoft Docs
description: Azure Migrate 검색 및 평가 도구를 사용 하 여 Azure VM 평가를 만드는 방법을 설명 합니다.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: f901fe23d2ff04e7ad9ba920dd90ebab8a39246c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786722"
---
# <a name="create-an-azure-vm-assessment"></a>Azure VM 평가 만들기

이 문서에서는 Azure Migrate: 검색 및 평가를 사용 하 여 VMware, Hyper-v 또는 물리적/기타 클라우드 환경에서 온-프레미스 서버에 대 한 Azure VM 평가를 만드는 방법을 설명 합니다.

[Azure Migrate](migrate-services-overview.md) 는 Azure로 마이그레이션하는 데 도움이 됩니다. Azure Migrate는 온-프레미스 인프라, 애플리케이션 및 데이터의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다. 

## <a name="before-you-start"></a>시작하기 전에

- Azure Migrate 프로젝트를 [만들었는지](./create-manage-projects.md) 확인 합니다.
- 프로젝트를 이미 만든 경우 Azure Migrate: 검색 및 평가 도구를 [추가](how-to-assess.md) 했는지 확인 합니다.
- 평가를 만들려면 [VMware](how-to-set-up-appliance-vmware.md) 또는 [hyper-v](how-to-set-up-appliance-hyper-v.md)에 대 한 Azure Migrate 어플라이언스를 설정 해야 합니다. 어플라이언스는 온-프레미스 서버를 검색 하 고 메타 데이터 및 성능 데이터를 Azure Migrate: 검색 및 평가로 보냅니다. [자세히 알아보기](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Azure VM 평가 개요
Azure Migrate: 검색 및 평가를 사용 하 여 Azure VM 평가를 만드는 데 사용할 수 있는 두 가지 유형의 크기 조정 기준이 있습니다.

**평가** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 수집된 성능 데이터를 기반으로 하는 평가 | **추천 VM 크기**: CPU 및 메모리 사용률 데이터를 기반으로 합니다.<br/><br/> **추천 디스크 유형(표준 또는 프리미엄 관리 디스크)** : 온-프레미스 디스크의 IOPS 및 처리량을 기반으로 합니다.
**온-프레미스로** | 온-프레미스 크기 조정을 기반으로 하는 평가 | **추천 VM 크기**: 온-프레미스 VM 크기를 기반으로 합니다.<br/><br> **추천 디스크 유형**: 평가를 위해 선택한 스토리지 유형 설정을 기반으로 합니다.

평가에 대해 [자세히 알아보세요](concepts-assessment-calculation.md).

## <a name="run-an-assessment"></a>평가 실행

다음과 같이 평가를 실행합니다.

1. **개요** 페이지 > **Windows, Linux 및 SQL Server** 에서 **서버 평가 및 마이그레이션** 을 클릭합니다.

   ![서버 평가 및 마이그레이션 단추의 위치](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. **Azure Migrate: 검색 및 평가** 에서 **평가** 를 클릭 하 고 **Azure VM** 을 선택 합니다.

    ![평가 단추의 위치](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. **서버** 평가  >  **평가 유형**
4. **검색 소스** 에서 다음을 수행합니다.

    - 어플라이언스를 사용 하 여 서버를 검색 한 경우 **Azure Migrate 어플라이언스에서 검색 된 서버** 를 선택 합니다.
    - 가져온 CSV 파일을 사용 하 여 서버를 검색 한 경우 **가져온 서버** 를 선택 합니다. 
    
1. **편집** 을 클릭하여 평가 속성을 검토합니다.

    ![평가 속성을 검토하는 모두 보기 단추의 위치](./media/tutorial-assess-vmware-azure-vm/assessment-name.png)

1. **평가 속성** > **대상 속성** 에서 다음을 수행합니다.
    - **대상 지역** 에서 마이그레이션할 Azure 지역을 지정합니다.
        - 크기 및 비용 권장 사항은 사용자가 지정한 위치를 기반으로 합니다. 대상 위치를 기본값에서 변경하면 **예약 인스턴스** 및 **VM 시리즈** 를 지정하라는 메시지가 표시됩니다.
        - Azure Government에서 [이러한 지역](migrate-support-matrix.md#supported-geographies-azure-government)의 평가를 대상으로 지정할 수 있습니다.
    - **스토리지 유형** 에서
        - 평가에서 성능 기반 데이터를 사용하려는 경우 Azure Migrate에 대해 **자동** 을 선택하여 디스크 IOPS 및 처리량에 따라 스토리지 유형을 권장합니다.
        - 또는 마이그레이션할 때 VM에 사용할 스토리지 유형을 선택합니다.
    - **예약 인스턴스** 에서 마이그레이션할 때 VM에 예약 인스턴스를 사용할지 여부를 지정합니다.
        - 예약 인스턴스를 사용하도록 선택하는 경우 '**할인(%)** 또는 **VM 작동 시간** 을 지정할 수 없습니다. 
        - [자세히 알아보기](https://aka.ms/azurereservedinstances).
 1. **VM 크기** 에서 다음을 수행합니다.
     - **크기 조정 기준** 에서 서버 구성 데이터/메타 데이터 또는 성능 기반 데이터에 대 한 평가의 기반으로 사용할 경우를 선택 합니다. 성능 데이터를 사용하는 경우 다음을 수행합니다.
        - **성능 기록** 에서 평가의 기준이 될 데이터 기간을 표시합니다.
        - **백분위수 활용률** 에서 성능 샘플에 사용할 백분위수 값을 지정합니다. 
    - **VM 시리즈** 에서 고려하려는 Azure VM 시리즈를 지정합니다.
        - 성능 기반 평가를 사용하는 경우 Azure Migrate는 값을 제안합니다.
        - 필요에 따라 설정을 조정합니다. 예를 들어 A 시리즈 VM이 필요한 프로덕션 환경이 Azure에 없는 경우 시리즈 목록에서 A 시리즈를 제외할 수 있습니다.
    - **쾌적 인자** 에서 평가 중에 사용할 버퍼를 표시합니다. 이는 계절별 사용량, 성능 기록 부족, 향후 사용량 증가와 같은 문제를 고려합니다. 예를 들어 2의 쾌적 인자를 사용하는 경우 다음과 같습니다.
    
        **구성 요소** | **유효 사용률** | **쾌적 인자(2.0) 추가**
        --- | --- | ---
        코어 | 2  | 4
        메모리 | 8GB | 16GB
   
1. **가격 책정** 에서 다음을 수행합니다.
    - **제품** 에서 등록하는 경우 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)을 지정합니다. 평가는 해당 제품에 대 한 비용을 예측 합니다.
    - **통화** 에서 계정의 청구 통화를 선택합니다.
    - **할인(%)** 에서 Azure 제품에 적용되는 구독별 할인을 추가합니다. 기본 설정은 0%입니다.
    - **VM 작동 시간** 에서 VM이 실행되는 기간(월당 일/일당 시간)을 지정합니다.
        - 이는 지속적으로 실행되지 않는 Azure VM에 유용합니다.
        - 예상 비용은 지정된 기간을 기준으로 합니다.
        - 기본값은 월당 31일/일당 24시간입니다.
    - **EA 구독** 에서 비용 추정을 위해 EA(기업계약) 구독 할인을 고려할지 여부를 지정합니다. 
    - **Azure 하이브리드 혜택** 에서 Windows Server 라이선스가 이미 있는지 여부를 지정합니다. 이러한 작업을 수행하고 Windows Server 구독의 활성 소프트웨어 보증이 적용되는 경우 Azure에 라이선스를 가져올 때 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-use-benefit/)을 적용할 수 있습니다.

1. 변경하는 경우 **저장** 을 클릭합니다.

    ![평가 속성](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. **서버 평가** 에서 **다음** 을 클릭합니다.

1. **평가할 서버 선택** > **평가 이름** 에서 평가 이름을 지정합니다. 

1. **그룹 선택 또는 만들기** 에서 **새로 만들기** 를 선택하고 그룹 이름을 지정합니다. 
    
     ![그룹에 VM 추가](./media/tutorial-assess-vmware-azure-vm/assess-group.png)


1. 어플라이언스를 선택하고 그룹에 추가할 VM을 선택합니다. 그런 다음 **다음** 을 클릭합니다.


1. **검토 + 평가 만들기** 에서 평가 세부 정보를 검토하고, **평가 만들기** 를 클릭하여 그룹을 만들고 평가를 실행합니다.

1. 평가를 만든 후 **서버** 에서 확인 하  >  **고 검색 및 평가** 평가를 Azure Migrate  >  합니다.

1. **평가 내보내기** 를 클릭하고, Excel 파일로 다운로드합니다.
    > [!NOTE]
    > 성능 기반 평가의 경우 평가를 만들기 전에 검색을 시작한 후 최소 하루를 기다리는 것이 좋습니다. 이는 더 높은 신뢰도로 성능 데이터를 수집할 시간을 제공합니다. 검색을 시작한 후에는 높은 신뢰도 등급을 위해 지정한 성능 기간(일/주/월) 동안 기다리는 것이 좋습니다.

## <a name="review-an-azure-vm-assessment"></a>Azure VM 평가 검토

Azure VM 평가는 다음을 설명합니다.

- **Azure 준비 상태**: 서버가 Azure로 마이그레이션하는 데 적합한지 여부입니다.
- **월간 예상 비용**: Azure에서 VM을 실행하는 데 들어가는 월간 예상 컴퓨팅 및 스토리지 비용입니다.
- **월간 예상 스토리지 비용**: 마이그레이션 후 디스크 스토리지에 대한 예상 비용입니다.

### <a name="view-an-azure-vm-assessment"></a>Azure VM 평가 보기

1. **Windows, Linux 및 SQL Server**  >  **Azure Migrate: 검색 및 평가** 에서 **Azure VM 평가** 옆에 있는 숫자를 클릭 합니다.
2. **평가** 에서 평가를 선택하여 엽니다. 예를 들면(예제에만 해당되는 추정 및 비용) 다음과 같습니다. 

    ![평가 요약](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 준비 상태 검토

1. **Azure 준비** 에서 서버를 azure로 마이그레이션할 준비가 되었는지 확인 합니다.
2. 서버 상태를 검토 합니다.
    - **Azure 준비 완료**: Azure Migrate는 평가에서 VM의 크기 및 예상 비용을 추천합니다.
    - **조건과 함께 준비 완료**: 문제 및 제안된 수정 사항을 보여 줍니다.
    - **Azure를 사용할 준비 안 됨**: 문제 및 제안된 수정 사항을 보여 줍니다.
    - **알 수 없는 준비**: 데이터 가용성 문제로 인해 Azure Migrate에서 준비 상태를 평가할 수 없는 경우에 사용됩니다.

3. **Azure 준비 상태** 상태를 클릭합니다. 서버 준비 상태 세부 정보를 살펴보고, 드릴다운하여 컴퓨팅, 스토리지 및 네트워크 설정을 포함한 서버 세부 정보를 확인할 수 있습니다.



### <a name="review-cost-details"></a>비용 세부 정보 검토

이 보기에서는 Azure에서 VM을 실행하는 데 들어가는 예상 컴퓨팅 및 스토리지 비용을 표시합니다.

1. 월간 컴퓨팅 및 스토리지 비용을 검토합니다. 비용은 평가된 그룹의 모든 서버에 대해 집계됩니다.

    - 예상 비용은 서버 및 해당 디스크와 속성에 대 한 권장 크기를 기준으로 합니다.
    - 컴퓨팅 및 스토리지의 월간 예상 비용이 표시됩니다.
    - 예상 비용은 온-프레미스 서버를 IaaS VM으로 실행하는 데 들어가는 비용입니다. Azure VM 평가는 PaaS 또는 SaaS 비용을 고려 하지 않습니다.

2. 월간 예상 스토리지 비용을 검토할 수 있습니다. 이 보기에는 평가된 그룹에 대해 집계된 스토리지 비용이 표시되며, 해당 그룹은 여러 유형의 스토리지 디스크로 분할되어 있습니다.
3. 드릴다운하여 특정 서버에 대한 세부 정보를 확인할 수 있습니다.


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
---
title: Azure Migrate Server 평가를 사용 하 여 AVS 평가 만들기 | Microsoft Docs
description: Azure Migrate Server 평가 도구를 사용 하 여 AVS 평가를 만드는 방법을 설명 합니다.
author: rashi-ms
ms.service: azure-migrate
ms.topic: article
ms.date: 06/26/2020
ms.author: rajosh
ms.openlocfilehash: 834fd32d17c4a5ff112dd11a331a59336d7cbcb7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92315251"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Azure VMware 솔루션 (AVS) 평가 만들기

이 문서에서는 Azure Migrate: 서버 평가를 사용 하 여 온-프레미스 VMware Vm에 대 한 Azure VMware 솔루션 (AVS) 평가를 만드는 방법을 설명 합니다.

[Azure Migrate](migrate-services-overview.md) 는 Azure로 마이그레이션하는 데 도움이 됩니다. Azure Migrate는 온-프레미스 인프라, 애플리케이션 및 데이터의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다.

## <a name="before-you-start"></a>시작하기 전에

- Azure Migrate 프로젝트를 [만들었는지](how-to-add-tool-first-time.md) 확인 합니다.
- 프로젝트를 이미 만든 경우 Azure Migrate: 서버 평가 도구를 [추가](how-to-assess.md) 했는지 확인 합니다.
- 평가를 만들려면 온-프레미스 컴퓨터를 검색 하 고 메타 데이터 및 성능 데이터를 Azure Migrate: 서버 평가로 전송 하는 [VMware](how-to-set-up-appliance-vmware.md)에 대 한 Azure Migrate 어플라이언스를 설정 해야 합니다. [자세히 알아보기](migrate-appliance.md).
- 쉼표로 구분 된 값 (CSV) 형식으로 [서버 메타 데이터를 가져올](./tutorial-discover-import.md) 수도 있습니다.


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Azure VMware 솔루션 (AVS) 평가 개요

Azure Migrate: 서버 평가를 사용하여 만들 수 있는 평가에는 두 가지 유형이 있습니다.

**평가 유형** | **세부 정보**
--- | --- 
**Azure VM** | 온-프레미스 서버를 Azure 가상 머신으로 마이그레이션하기 위한 평가. <br/><br/> 이 평가 유형을 사용 하 여 Azure로 마이그레이션하기 위한 온-프레미스 [VMware vm](how-to-set-up-appliance-vmware.md), [hyper-v vm](how-to-set-up-appliance-hyper-v.md)및 [물리적 서버](how-to-set-up-appliance-physical.md) 를 평가할 수 있습니다. [자세히 알아보기](concepts-assessment-calculation.md)
**AVS(Azure VMware 솔루션)** | 온-프레미스 서버를 [AVS(Azure VMware 솔루션)](../azure-vmware/introduction.md)로 마이그레이션하기 위한 평가. <br/><br/> 이 평가 유형을 사용하여 AVS(Azure VMware 솔루션)로 마이그레이션하기 위한 온-프레미스 [VMware VM](how-to-set-up-appliance-vmware.md)을 평가할 수 있습니다. [자세히 알아보기](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Azure VMware Solution (AVS) 평가는 현재 미리 보기로 제공 되며 VMware Vm에 대해서만 만들 수 있습니다.


Azure VMware 솔루션 (AVS) 평가를 만드는 데 사용할 수 있는 두 가지 유형의 크기 조정 기준이 있습니다.

**평가** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 온-프레미스 Vm의 수집 된 성능 데이터를 기반으로 하는 평가 | **권장 노드 크기**: 평가를 위해 선택 하는 노드 유형, 저장소 유형 및 ftt 설정과 함께 CPU 및 메모리 사용률 데이터를 기반으로 합니다.
**온-프레미스로** | 온-프레미스 크기 조정을 기반으로 하는 평가 | **권장 노드 크기**: 평가를 위해 선택 하는 노드 유형, 저장소 유형 및 ftt 설정과 함께 온-프레미스 VM 크기를 기반으로 합니다.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Azure VMware 솔루션 (AVS) 평가 실행

다음과 같이 Azure VMware 솔루션 (AVS) 평가를 실행 합니다.

1. 평가를 만드는 방법에 대한 [모범 사례](best-practices-assessment.md)를 검토합니다.

2. **서버** 탭의 **Azure Migrate: 서버 평가** 타일에서 **평가**를 클릭합니다.

    ![평가 도구에서 평가를 선택한 Azure Migrate 서버가 스크린샷으로 표시 됩니다.](./media/how-to-create-assessment/assess.png)

3. **서버 평가**에서 평가 유형을 "Azure VMware 솔루션 (AVS)"으로 선택 하 고, 검색 원본을 선택 하 고, 평가 이름을 지정 합니다.

    ![평가 기본 사항](./media/how-to-create-avs-assessment/assess-servers-avs.png)

4. 평가 속성을 검토하려면 **모두 보기**를 클릭합니다.

    ![AVS 평가 속성](./media/how-to-create-avs-assessment/avs-view-all.png)

5. **다음**을 클릭하여 **평가할 머신을 선택**합니다. **그룹 선택 또는 만들기**에서 **새로 만들기**를 선택하고 그룹 이름을 지정합니다. 그룹은 평가를 위해 하나 이상의 VM을 수집합니다.

6. **그룹에 머신 추가**에서 그룹에 추가할 VM을 선택합니다.

7. **다음**에서 **검토 + 평가 만들기**를 클릭하여 평가 세부 정보를 검토합니다.

8. **평가 만들기**를 클릭하여 그룹을 만들고, 평가를 실행합니다.

    ![AVS 평가 만들기](./media/how-to-create-avs-assessment/avs-assessment-create.png)

9. 평가가 만들어지면 **서버** > **Azure Migrate: 서버 평가** > **평가**에서 해당 평가를 확인합니다.

10. **평가 내보내기**를 클릭하고, Excel 파일로 다운로드합니다.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Azure VMware 솔루션 (AVS) 평가 검토

Azure VMware 솔루션 (AVS) 평가는 다음을 설명 합니다.

- **Azure Vmware 솔루션 (avs) 준비**: 온-프레미스 Vm이 Azure vmware 솔루션 (avs)로 마이그레이션하는 데 적합 한지 여부입니다.
- **Avs 노드 수**: vm을 실행 하는 데 필요한 총 avs 노드 수입니다.
- 모든 노드에서 CPU, 메모리 및 저장소 사용률을 예측 **합니다.**
- **월간 비용 예측**: 온-프레미스 vm을 실행 하는 모든 Azure VMware SOLUTION (AVS) 노드에 대 한 월별 예상 비용입니다.


### <a name="view-an-assessment"></a>평가 보기

1. **마이그레이션 목표** >  **서버**의 **Azure Migrate: 서버 평가**에서 **평가**를 클릭합니다.

2. **평가**에서 해당 평가를 클릭하여 엽니다.

    ![AVS 평가 요약](./media/how-to-create-avs-assessment/avs-assessment-summary.png)

### <a name="review-azure-vmware-solution-avs-readiness"></a>Azure VMware 솔루션 (AVS) 준비 상태 검토

1. **Azure 준비 상태**에서 VM이 AVS로 마이그레이션할 준비가 되었는지 여부를 확인 합니다.

2. VM 상태를 검토합니다.
    - **Avs 준비**: 컴퓨터를 변경 하지 않고 AZURE (avs)로 마이그레이션할 수 있습니다. 전체 AVS 지원을 통해 AVS에서 시작 됩니다.
    - **조건 준비**: vm의 모든 기능을 사용 하기 전에 VMware 도구 및 또는 기타 설정을 요구할 뿐만 아니라 현재 vsphere 버전과의 호환성 문제가 발생할 수 있습니다.
    - **Avs에 대해 준비 되지 않음**: VM이 avs에서 시작 되지 않습니다. 예를 들어 온-프레미스 VMware VM에 cd-rom과 같이 연결 된 외부 장치가 있는 경우 VMotion 작업은 실패 합니다 (VMware VMotion 사용 하는 경우).
    - **준비 상태 알 수 없음**: 온-프레미스 환경에서 수집 된 메타 데이터가 부족 하 여 컴퓨터의 준비 상태를 확인할 수 Azure Migrate.

3. 제안 된 도구를 검토 합니다.
    - **VMWARE hcx 또는 Enterprise**: vmware 컴퓨터의 경우 온-프레미스 워크 로드를 Azure vmware 솔루션 (AVS) 사설 클라우드로 마이그레이션하기 위한 제안 된 마이그레이션 도구인 Vmware 하이브리드 클라우드 확장 (hcx) 솔루션이 제안 됩니다. [자세한 정보](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **알 수 없음**: CSV 파일을 통해 가져온 컴퓨터의 경우 기본 마이그레이션 도구를 알 수 없습니다. VMware 머신의 경우 VMware HCX(하이브리드 클라우드 확장) 솔루션을 사용하는 것이 좋습니다. 

4. **AVS 준비** 상태를 클릭 합니다. VM 준비 상태 세부 정보를 살펴보고, 드릴다운하여 컴퓨팅, 스토리지 및 네트워크 설정을 포함한 VM 세부 정보를 확인할 수 있습니다.



### <a name="review-cost-details"></a>비용 세부 정보 검토

이 보기는 Azure VMware 솔루션 (AVS)에서 Vm을 실행 하는 예상 비용을 보여 줍니다.

1. 월별 총 비용을 검토합니다. 비용은 평가된 그룹의 모든 VM에 대해 집계됩니다. 

    - 예상 비용은 전체 Vm의 리소스 요구 사항을 고려 하는 데 필요한 AVS 노드 수를 기준으로 합니다.
    - Azure VMware 솔루션 (AVS)의 가격은 노드당 계산 되므로 총 비용에는 계산 비용 및 저장소 비용 분포가 없습니다.
    - 예상 비용은 AVS에서 온-프레미스 VM을 실행하는 데 드는 비용입니다. Azure Migrate 서버 평가에서는 PaaS 또는 SaaS 비용을 고려하지 않습니다.
    
2. 월간 예상 스토리지 비용을 검토할 수 있습니다. 이 보기에는 평가된 그룹에 대해 집계된 스토리지 비용이 표시되며, 해당 그룹은 여러 유형의 스토리지 디스크로 분할되어 있습니다.

3. 드릴다운하여 특정 VM에 대한 세부 정보를 확인할 수 있습니다.


### <a name="review-confidence-rating"></a>신뢰 등급 검토

성능 기반 평가를 실행하면 신뢰 등급이 평가에 할당됩니다.

![신뢰 등급](./media/how-to-create-assessment/confidence-rating.png)

- 별 1개(최저)에서 별 5개(최고)까지의 등급입니다.
- 신뢰 등급을 통해 평가에서 제공하는 크기 추천 사항의 안정성을 예측할 수 있습니다.
- 신뢰 등급은 평가를 계산하는 데 필요한 데이터 요소의 가용성을 기반으로 합니다.
- 성능 기반 크기 조정의 경우 서버 평가의 AVS 평가에는 CPU 및 VM 메모리에 대 한 사용률 데이터가 필요 합니다. 다음 성능 데이터는 수집 되지만, AVS 평가를 위한 크기 조정 권장 사항에는 사용 되지 않습니다.
  - VM에 연결 된 모든 디스크에 대 한 디스크 IOPS 및 처리량 데이터입니다.
  - VM에 연결 된 각 네트워크 어댑터에 대 한 성능 기반 크기 조정을 처리 하는 네트워크 i/o입니다.

평가에 대한 신뢰 등급은 다음과 같습니다.

**데이터 요소 가용성** | **신뢰 등급**
--- | ---
0%-20% | 별 1개
21%-40% | 별 2개
41%-60% | 별 3개
61%-80% | 별 4개
81%-100% | 별 5개

성능 데이터에 대 한 [자세한](concepts-azure-vmware-solution-assessment-calculation.md) 정보 


## <a name="next-steps"></a>다음 단계

- [종속성 매핑을](how-to-create-group-machine-dependencies.md) 사용 하 여 높은 신뢰도 그룹을 만드는 방법에 대해 알아봅니다.
- AVS 평가를 계산 하는 방법에 [대해 자세히 알아보세요](concepts-azure-vmware-solution-assessment-calculation.md) .
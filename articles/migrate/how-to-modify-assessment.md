---
title: Azure Migrate용 평가 사용자 지정 | Microsoft Docs
description: Azure Migrate를 사용하여 만든 평가를 사용자 지정하는 방법 설명
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: e2fc240e04ec375f95bef74e3fd44381ff8cceb6
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108135746"
---
# <a name="customize-an-assessment"></a>평가 사용자 지정

이 문서에서는 Azure Migrate 검색 및 평가 도구를 통해 만들어진 평가를 사용자 지정하는 방법을 설명합니다.

[Azure Migrate](migrate-services-overview.md)는 온-프레미스 앱과 워크로드 및 프라이빗/퍼블릭 클라우드 VM의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure Migrate 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다.

Azure Migrate 검색 및 평가 도구를 사용하면 Azure로의 마이그레이션에 대한 준비로 온-프레미스 VMware VM 및 Hyper-V VM용 평가를 만들 수 있습니다. 검색 및 평가 도구는 Azure IaaS 가상 머신 및 AVS(Azure VMware Solution)으로 마이그레이션하기 위해 온-프레미스 서버를 평가합니다. 

## <a name="about-assessments"></a>평가 정보

검색 및 평가 도구로 만든 평가는 데이터의 지정 시간 스냅샷입니다. Azure Migrate: 검색 및 평가를 사용하여 만들 수 있는 평가에는 두 가지 유형이 있습니다.

**평가 유형** | **세부 정보**
--- | --- 
**Azure VM** | 온-프레미스 서버를 Azure 가상 머신으로 마이그레이션하기 위한 평가. <br/><br/> 이 평가 유형을 사용하여 Azure로 마이그레이션하기 위한 온-프레미스 [VMware VM](how-to-set-up-appliance-vmware.md), [Hyper-V VM](how-to-set-up-appliance-hyper-v.md) 및 [물리적 서버](how-to-set-up-appliance-physical.md)를 평가할 수 있습니다. (concepts-assessment-calculation.md)
**AVS(Azure VMware 솔루션)** | 온-프레미스 서버를 [AVS(Azure VMware 솔루션)](../azure-vmware/introduction.md)로 마이그레이션하기 위한 평가. <br/><br/> 이 평가 유형을 사용하여 AVS(Azure VMware 솔루션)로 마이그레이션하기 위한 온-프레미스 [VMware VM](how-to-set-up-appliance-vmware.md)을 평가할 수 있습니다. [자세히 알아보기](concepts-azure-vmware-solution-assessment-calculation.md)

Azure Migrate 평가의 크기 조정 기준 옵션:

**크기 조정 기준** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 수집된 성능 데이터를 기반으로 권장 사항을 만드는 평가 | **Azure VM 평가**: VM 크기 권장 사항은 CPU 및 메모리 사용률 데이터를 기반으로 합니다.<br/><br/> 디스크 유형 권장 사항(표준 HDD/SSD 또는 프리미엄 관리 디스크)은 온-프레미스 디스크의 IOPS 및 처리량을 기반으로 합니다.<br/><br/>**Azure SQL 평가**: Azure SQL 구성은 CPU 사용률, 메모리 사용률, IOPS(데이터 및 로그 파일), IO 작업의 처리량 및 대기 시간을 포함하는 SQL 인스턴스 및 데이터베이스의 성능 데이터를 기반으로 합니다.<br/><br/>**AVS(Azure VMware 솔루션) 평가**: AVS 노드 권장 사항은 CPU 및 메모리 사용률 데이터를 기반으로 합니다.
**온-프레미스인 경우** | 권장 사항을 적용하기 위해 성능 데이터를 사용하지 않는 평가. | **Azure VM 평가**: VM 크기 권장 사항은 온-프레미스 VM 크기를 기반으로 합니다.<br/><br> 권장 디스크 유형은 평가를 위해 스토리지 유형 설정에서 선택한 항목을 기반으로 합니다.<br/><br/> **AVS(Azure VMware 솔루션) 평가**: AVS 노드 권장 사항은 온-프레미스 VM 크기를 기반으로 합니다.

## <a name="how-is-an-assessment-done"></a>평가는 어떻게 수행하나요?

Azure Migrate 검색 및 평가에서 수행하는 평가에는 3가지 단계가 있습니다. 평가는 적합성 분석으로 시작되며 크기 조정, 월별 비용 예측이 차례로 진행됩니다. 컴퓨터는 이전 단계를 통과하는 경우에만 이후 단계로 이동합니다. 예를 들어 컴퓨터가 Azure 적합성 검사에 실패하면 Azure에 적합하지 않은 것으로 표시되고 크기 조정과 비용 예측이 수행되지 않습니다. [자세한 정보](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Azure VM 평가에는 무엇이 포함되어 있나요?

**속성** | **세부 정보**
--- | ---
**대상 위치** | 마이그레이션할 Azure 위치입니다.<br/> Azure VM 평가는 현재 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 캐나다 중부, 캐나다 동부, 인도 중부, 미국 중부, 중국 동부, 중국 북부, 동아시아, 미국 동부, 독일 중부, 독일 북동부, 미국 동부 2, 일본 동부, 일본 서부, 한국 중부, 한국 남부, 미국 중북부, 북유럽, 미국 중남부, 동남 아시아, 인도 남부, 영국 남부, 영국 서부, US Gov 애리조나, US Gov 텍사스, US Gov 버지니아, 미국 중서부, 서유럽, 인도 서부, 미국 서부, 미국 서부 2의 대상 지역을 지원합니다.
**스토리지 유형** | 이 속성을 사용하여 Azure에서 이동할 디스크의 형식을 지정할 수 있습니다.<br/><br/> 온-프레미스 크기 조정의 경우 대상 스토리지 형식을 프리미엄 관리 디스크, 표준 SSD 관리 디스크 또는 표준 HDD 관리 디스크로 지정할 수 있습니다. 성능 기반 크기 조정의 경우 대상 디스크 형식을 자동 관리 디스크, 프리미엄 관리 디스크, 표준 HDD 관리 디스크 또는 표준 SSD 관리 디스크 중에서 지정할 수 있습니다.<br/><br/> 자동으로 스토리지 형식을 지정할 때 디스크 권장 사항은 디스크의 성능 데이터를 기반으로 수행됩니다(IOPS 및 처리량). 스토리지 유형을 프리미엄/표준으로 지정하면 평가는 선택한 스토리지 유형 내의 디스크 SKU를 권장합니다. 99.9%의 단일 인스턴스 VM SLA를 달성하려는 경우 스토리지 유형을 프리미엄 관리 디스크로 지정할 수 있습니다. 그러면 모든 평가 대상 디스크가 권장 프리미엄 관리 디스크로 표시됩니다. Azure
**RI(예약 인스턴스)** | 이 속성을 사용하면 Azure에 [예약 인스턴스](https://azure.microsoft.com/pricing/reserved-vm-instances/)가 있는지를 지정할 수 있습니다. 그런 다음 평가의 비용 예측이 RI 할인을 고려하여 수행됩니다. 예약 인스턴스는 현재 Azure Migrate의 종량제 서비스에만 지원됩니다.
**크기 조정 기준** | Azure에 사용할 VM의 적정 크기를 산정하는 데 사용되는 기준입니다. ‘성능 기반’ 크기 조정을 수행하거나, 성능 기록을 고려하지 않고 ‘온-프레미스로’ VM 크기를 조정할 수 있습니다. 
**성능 기록** | 머신의 성능 데이터를 평가하는 데 고려할 기간입니다. 이 속성은 크기 조정 기준이 ‘성능 기반’인 경우에만 적용됩니다.
**백분위 수 사용률** | 적정 크기를 산정하는 데 고려되는 성능 샘플 집합의 백분위수 값입니다. 이 속성은 크기 조정이 ‘성능 기반’인 경우에만 적용됩니다.
**VM 시리즈** |     크기 조정 시 고려할 VM 시리즈를 지정할 수 있습니다. 예를 들어 Azure에서 A 시리즈 VM으로 마이그레이션하지 않으려는 프로덕션 환경이 있는 경우 목록 또는 시리즈에서 A 시리즈를 제외하면 선택한 시리즈에서만 적절한 크기 조정이 수행됩니다.
**쾌적 인자** | Azure VM 평가는 평가 중에 버퍼(쾌적 인자)를 고려합니다. 이 버퍼는 VM의 컴퓨터 사용률 데이터(CPU, 메모리, 디스크 및 네트워크)를 기반으로 적용됩니다. 쾌적 인자는 계절별 사용량, 성능 기록 부족, 향후 사용량 증가 가능성 등의 문제를 고려합니다.<br/><br/> 예를 들어 사용률이 20%인 10코어 VM은 일반적으로 2코어 VM이라는 결과가 나옵니다. 그러나 쾌적 인자가 2.0x이면 결과는 4코어 VM이 됩니다.
**제안** | 등록한 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)입니다. Azure Migrate는 그에 따라 비용을 예측합니다.
**통화** | 청구 통화입니다.
**할인(%)** | Azure 제품에 적용되는 구독별 할인입니다.<br/> 기본 설정은 0%입니다.
**VM 작동 시간** | Azure에서 VM을 연중 무휴로 실행하지는 않으려는 경우 VM을 실행할 기간(월별 일 수/일별 시간 수)을 지정하면 해당 기간에 따라 예상 비용이 계산됩니다.<br/> 기본값은 월당 31일이며 일당 24시간입니다.
**Azure 하이브리드 혜택** | 소프트웨어 보증이 있고 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-use-benefit/)을 받을 수 있는지를 지정합니다. [예]로 설정하면 Windows 외 Azure 가격이 Windows VM에 적용됩니다. 기본값은 [예]입니다.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>AVS(Azure VMware Solution) 평가에는 무엇이 포함되어 있나요?

다음은 AVS 평가에 포함된 요소입니다.


| **속성** | **세부 정보** |
| - | - |
| **대상 위치** | 마이그레이션할 AVS 프라이빗 클라우드 위치를 지정합니다.<br/><br/> AVS 평가는 현재 미국 동부, 서유럽, 미국 서부 등의 대상 지역을 지원합니다. |
| **스토리지 유형** | AVS에서 사용할 스토리지 엔진을 지정합니다.<br/><br/> AVS 평가는 기본 스토리지 유형으로 vSAN만을 지원합니다. |
**RI(예약 인스턴스)** | 이 속성은 AVS에서 예약 인스턴스를 지정하는 데 도움이 됩니다. RI는 현재 AVS 노드용으로 지원되지 않습니다. |
**노드 유형** | 온-프레미스 VM을 매핑하는 데 사용되는 [AVS 노드 유형](../azure-vmware/concepts-private-clouds-clusters.md)을 지정합니다. 기본 노드 유형은 AV36입니다. <br/><br/> Azure Migrate는 VM을 AVS로 마이그레이션하는 데 필요한 노드의 수를 추천합니다. |
**FTT 설정, RAID 수준** | 적용 가능한 허용 실패 및 RAID 조합을 지정합니다. 선택한 FTT 옵션은 온-프레미스 VM 디스크 요구 사항과 결합되어 AVS에 필요한 총 vSAN 스토리지 크기를 결정합니다. |
**크기 조정 기준** | AVS에 사용할 VM의 ‘적정 크기’를 산정하는 데 사용되는 기준입니다. ‘성능 기반’ 크기 조정을 선택하거나, 성능 기록을 고려하지 않고 ‘온-프레미스로’ 크기를 조정할 수 있습니다.  |
**성능 기록** | 머신의 성능 데이터를 평가하는 데 고려할 기간을 설정합니다. 이 속성은 크기 조정 기준이 ‘성능 기반’인 경우에만 적용됩니다. |
**백분위 수 사용률** | 적정 크기를 산정하는 데 고려되는 성능 샘플 집합의 백분위수 값을 지정합니다. 이 속성은 크기 조정이 성능 기반인 경우에만 적용됩니다.|
**쾌적 인자** | Azure Migrate는 평가 중에 버퍼(쾌적 인자)를 고려합니다. 이 버퍼는 VM의 컴퓨터 사용률 데이터(CPU, 메모리, 디스크 및 네트워크)를 기반으로 적용됩니다. 쾌적 인자는 계절별 사용량, 성능 기록 부족, 향후 사용량 증가 가능성 등의 문제를 고려합니다.<br/><br/> 예를 들어 사용률이 20%인 10코어 VM은 일반적으로 2코어 VM이라는 결과가 나옵니다. 그러나 쾌적 인자가 2.0x이면 결과는 4코어 VM이 됩니다. |
**제안** | 사용자가 등록한 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)이 표시됩니다. Azure Migrate는 그에 따라 비용을 예측합니다.|
**통화** | 계정의 청구 통화를 보여 줍니다. |
**할인(%)** | Azure 제품에 적용되는 구독별 할인을 보여 줍니다. 기본 설정은 0%입니다. |
**Azure 하이브리드 혜택** | 소프트웨어 보증이 있고 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-use-benefit/)을 받을 수 있는지를 지정합니다. 이는 노드 기반 가격으로 인한 Azure VMware 솔루션 가격에는 영향을 주지 않지만, 고객은 Azure 하이브리드 혜택을 사용하여 온-프레미스 OS 라이선스(Microsoft 기반)를 AVS에 계속해서 적용할 수 있습니다. 다른 소프트웨어 OS 공급업체는 RHEL와 같은 고유한 라이선스 용어를 제공해야 합니다. |
**vCPU 초과 구독** | AVS 노드에서 하나의 물리적 코어에 연결된 가상 코어 수의 비율을 지정합니다. 계산의 기본값은 vCPU 4개: AVS의 실제 코어 1개입니다. <br/><br/> API 사용자는 이 값을 정수로 설정할 수 있습니다. 4:1보다 큰 vCPU 초과 구독인 경우 성능 저하를 유발하기 시작할 수 있지만, 웹 서버 유형 워크로드에 사용할 수 있습니다. |

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Azure SQL 평가를 만들고 사용자 지정하는 데 사용되는 속성은 무엇인가요?

다음은 Azure SQL 평가 속성에 포함된 요소입니다.

**속성** | **세부 정보**
--- | ---
**대상 위치** | 마이그레이션할 Azure 지역입니다. Azure SQL 구성 및 비용 권장 사항은 사용자가 지정한 위치를 기반으로 합니다.
**대상 배포 유형** | 평가를 실행하려는 대상 배포 유형: <br/><br/> Azure Migrate에서 Azure SQL MI 및 Azure SQL DB로 마이그레이션하기 위한 SQL 서버의 준비 상태를 평가하고 가장 적합한 대상 배포 옵션인 대상 계층, Azure SQL 구성 및 월별 추정치를 추천하려면 **권장** 을 선택합니다.<br/><br/>Azure SQL Database로만 마이그레이션하기 위한 SQL 서버를 평가하고 대상 계층, Azure SQL DB 구성, 월별 추정치를 검토하려면 **Azure SQL DB** 를 선택합니다.<br/><br/>Azure SQL Database로만 마이그레이션하기 위한 SQL 서버를 평가하고 대상 계층, Azure SQL MI 구성, 월별 추정치를 검토하려면 **Azure SQL MI** 를 선택합니다.
**예약 용량** | 평가의 비용 예측이 고려할 수 있도록 예약된 용량을 지정합니다.<br/><br/> 예약된 용량 옵션을 선택하면 "할인(%)"을 지정할 수 없습니다.
**크기 조정 기준** | 해당 속성은 Azure SQL 구성의 크기를 적절하게 조정하는 데 사용됩니다. <br/><br/> 기본적으로 **성능 기반** 으로 설정됩니다. 이는 평가가 SQL Server 인스턴스 및 데이터베이스 성능 메트릭을 수집하여 최적의 크기의 Azure SQL Managed Instance 및/또는 Azure SQL Database 계층/구성 권장 사항을 추천한다는 것을 의미합니다.
**성능 기록** | 성능 기록은 성능 데이터를 평가할 때 사용되는 기간을 지정합니다.
**백분위 수 사용률** | 백분위 수 사용률은 적합한 크기 조정에 사용되는 성능 샘플의 백분위수 값을 지정합니다.
**쾌적 인자** | 평가 중에 사용되는 버퍼입니다. 계절별 사용량, 짧은 성능 기록, 향후 사용량 증가 등의 문제를 고려합니다.<br/><br/> 예를 들어 사용률이 20%인 10코어 인스턴스는 일반적으로 2코어 인스턴스라는 결과가 나옵니다. 2\.0의 쾌적 인자를 사용하면 4코어 인스턴스라는 결과가 대신 나옵니다.
**제품/라이선싱 프로그램** | 등록한 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)입니다. 현재 종량제 및 종량제 개발/테스트에서만 선택할 수 있습니다. 종량제 제품 외에 예약된 용량과 Azure 하이브리드 혜택을 적용하여 추가 할인을 받을 수 있습니다.
**서비스 계층** | Azure SQL Database 및/또는 Azure SQL Managed Instance로 마이그레이션해야 하는 비즈니스 요구 사항에 가장 적합한 서비스 계층 옵션입니다.<br/><br/>Azure Migrate에서 서버에 가장 적합한 서비스 계층을 추천하려면 **권장** 을 선택합니다. 권장 제품은 범용 또는 중요 비즈니스용 제품입니다. <br/><br/> 예산 기반 워크로드에 맞게 Azure SQL 구성을 디자인하려면 **범용** 을 선택합니다. [자세한 내용](../azure-sql/database/service-tier-general-purpose.md) <br/><br/> 복원력이 높고 장애 조치(failover)에 대한 대기 시간이 낮은 워크로드에 맞게 디자인된 Azure SQL 구성을 원하는 경우 **중요 비즈니스용** 을 선택합니다. [자세한 내용](../azure-sql/database/service-tier-business-critical.md)
**통화** | 계정의 청구 통화입니다.
**할인(%)** | Azure 제품에 적용되는 구독별 할인입니다. 기본 설정은 0%입니다.
**Azure 하이브리드 혜택** | SQL Server 라이선스가 이미 있는지를 지정합니다. <br/><br/> 이러한 작업을 수행하고 SQL Server 구독의 활성 소프트웨어 보증이 적용되는 경우 Azure에 라이선스를 가져올 때 Azure 하이브리드 혜택을 적용할 수 있습니다.

## <a name="edit-assessment-properties"></a>평가 속성 편집

평가를 만든 후 평가 속성을 편집하려면 다음을 수행합니다.

1. Azure Migrate 프로젝트에서 **서버** 를 클릭합니다.
2. **Azure Migrate: 검색 및 평가** 에서 평가 횟수를 클릭합니다.
3. **평가** 에서 관련 평가 > **속성 편집** 을 클릭합니다.
5. 위의 표에 따라 평가 속성을 사용자 지정합니다.
6. **저장** 을 클릭하여 평가를 업데이트합니다.


평가를 만들 때 평가 속성을 편집할 수도 있습니다.


## <a name="next-steps"></a>다음 단계

- Azure VM 평가를 계산하는 방법에 대해 [자세히 알아보세요](concepts-assessment-calculation.md).
- Azure SQL 평가를 계산하는 방법에 대해 [자세히 알아보세요](concepts-azure-sql-assessment-calculation.md).
- AVS 평가를 계산하는 방법에 대해 [자세히 알아보세요](concepts-azure-vmware-solution-assessment-calculation.md).
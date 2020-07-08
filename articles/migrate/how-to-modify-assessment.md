---
title: Azure Migrate Server 평가에 대 한 평가 사용자 지정 | Microsoft Docs
description: Azure Migrate Server 평가를 사용 하 여 만든 평가를 사용자 지정 하는 방법을 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: de526da255d0ffb2d4c8f13d87d9b9e230c8bbd7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85561820"
---
# <a name="customize-an-assessment"></a>평가 사용자 지정

이 문서에서는 Azure Migrate Server 평가를 통해 생성 된 평가를 사용자 지정 하는 방법을 설명 합니다.

[Azure Migrate](migrate-services-overview.md)는 온-프레미스 앱과 워크로드 및 프라이빗/퍼블릭 클라우드 VM의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure Migrate 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다.

Azure Migrate Server 평가 도구를 사용 하 여 Azure로의 마이그레이션을 준비 하는 동안 온-프레미스 VMware Vm 및 Hyper-v Vm에 대 한 평가를 만들 수 있습니다. 서버 평가 도구는 Azure IaaS 가상 머신 및 Azure VMware 솔루션 (AVS)으로 마이그레이션하기 위해 온-프레미스 서버를 평가 합니다. 

## <a name="about-assessments"></a>평가 정보

서버 평가를 사용 하 여 만든 평가는 데이터의 특정 시점 스냅숏입니다. Azure Migrate: 서버 평가를 사용하여 만들 수 있는 평가에는 두 가지 유형이 있습니다.

**평가 유형** | **세부 정보**
--- | --- 
**Azure VM** | 온-프레미스 서버를 Azure virtual machines로 마이그레이션하기 위한 평가. <br/><br/> 이 평가 유형을 사용 하 여 Azure로 마이그레이션하기 위한 온-프레미스 [VMware vm](how-to-set-up-appliance-vmware.md), [hyper-v vm](how-to-set-up-appliance-hyper-v.md)및 [물리적 서버](how-to-set-up-appliance-physical.md) 를 평가할 수 있습니다. (concepts-assessment-calculation.md)
**AVS(Azure VMware 솔루션)** | 온-프레미스 서버를 [Azure VMware 솔루션 (AVS)](https://docs.microsoft.com/azure/azure-vmware/introduction)으로 마이그레이션하기 위한 평가. <br/><br/> 이 평가 유형을 사용 하 여 Azure VMware 솔루션 (AVS)으로 마이그레이션하기 위한 온-프레미스 [VMware vm](how-to-set-up-appliance-vmware.md) 을 평가할 수 있습니다. [자세히 알아보기](concepts-azure-vmware-solution-assessment-calculation.md)

서버 평가에서 Azure VM 평가는 다음과 같은 두 가지 크기 조정 기준 옵션을 제공 합니다.

**크기 조정 조건** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 수집 된 성능 데이터를 기반으로 권장 사항을 만드는 평가 | **AZURE vm 평가**: vm 크기 권장 사항은 CPU 및 메모리 사용률 데이터를 기반으로 합니다.<br/><br/> 디스크 유형 권장 사항 (표준 HDD/SSD 또는 프리미엄 관리 디스크)은 온-프레미스 디스크의 IOPS 및 처리량을 기반으로 합니다.<br/><br/> **Azure VMware Solution (avs) 평가**: avs 노드 권장 사항은 CPU 및 메모리 사용률 데이터를 기반으로 합니다.
**온-프레미스 인 경우** | 권장 사항을 적용 하기 위해 성능 데이터를 사용 하지 않는 평가 | **AZURE vm 평가**: vm 크기 권장 사항은 온-프레미스 vm 크기를 기반으로 합니다.<br/><br> 권장 디스크 유형은 평가의 저장소 유형 설정에서 선택한 항목을 기반으로 합니다.<br/><br/> **Azure VMware 솔루션 (avs) 평가**: avs 노드 권장 사항은 온-프레미스 VM 크기를 기반으로 합니다.


## <a name="how-is-an-assessment-done"></a>평가는 어떻게 수행 하나요?

Azure Migrate Server 평가에서 수행 되는 평가에는 3 단계가 있습니다. 평가는 적합성 분석으로 시작되며 크기 조정, 월별 비용 예측이 차례로 진행됩니다. 컴퓨터는 이전 단계를 통과하는 경우에만 이후 단계로 이동합니다. 예를 들어 컴퓨터가 Azure 적합성 검사에 실패하면 Azure에 적합하지 않은 것으로 표시되고 크기 조정과 비용 예측이 수행되지 않습니다. [자세한 정보](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-azure-vm-assessment"></a>Azure VM 평가의 기능

**속성** | **세부 정보**
--- | ---
**대상 위치** | 마이그레이션할 Azure 위치입니다.<br/> 서버 평가는 현재 다음 대상 지역을 지원 합니다. 오스트레일리아 동부, 오스트레일리아, 브라질 남부, 캐나다 중부, 캐나다 동부, 인도 중부, 미국 중부, 중국 동부, 중국 북부, 동아시아, 미국 동부, 미국 미국, 독일 중부, 독일 북동쪽, 일본 동부, 일본 서 부, 대한민국 중부, 대한민국, 미국 중 북부, 미국 중 북부, 미국 중 북부, 동남 아시아 영국 서부 영국 남부 , US Gov 텍사스, US Gov 버지니아, 미국 서 부, 유럽 서부, 인도 서 부, 미국 서 부 및 서 부 미국.
**저장소 유형** | 이 속성을 사용 하 여 Azure에서 이동 하려는 디스크의 유형을 지정할 수 있습니다.<br/><br/> 온-프레미스 크기 조정의 경우 대상 저장소 유형을 프리미엄 관리 디스크, 표준 SSD 관리 디스크 또는 표준 HDD 관리 디스크로 지정할 수 있습니다. 성능 기반 크기 조정의 경우 대상 디스크 유형을 자동, 프리미엄 관리 디스크, 표준 HDD 관리 디스크 또는 표준 SSD 관리 디스크 중에서 지정할 수 있습니다.<br/><br/> 자동으로 스토리지 형식을 지정할 때 디스크 권장 사항은 디스크의 성능 데이터를 기반으로 수행됩니다(IOPS 및 처리량). 저장소 유형을 premium/standard로 지정 하면 평가에서 선택한 저장소 유형 내에서 디스크 SKU를 권장 합니다. 단일 인스턴스 VM SLA 99.9%를 실현 하려면 저장소 유형을 프리미엄 관리 디스크로 지정 하는 것이 좋습니다. 그러면 모든 평가 대상 디스크가 권장 프리미엄 관리 디스크로 표시됩니다. Azure
**예약 인스턴스 (RI)** | 이 속성을 사용 하 여 Azure에 [예약 된 인스턴스가](https://azure.microsoft.com/pricing/reserved-vm-instances/) 있는지 여부를 지정할 수 있습니다. 그런 다음, 평가에서의 예측 비용은 RI 할인으로 수행 됩니다. 예약 인스턴스는 현재 Azure Migrate의 종량제 서비스에만 지원됩니다.
**크기 조정 기준** | Azure에 대 한 Vm을 적절 하 게 크기 조정 하는 데 사용할 조건입니다. 성능 *기반* 크기 조정을 수행 하거나 성능 기록을 고려 하지 않고 *온-프레미스로*vm의 크기를 조정할 수 있습니다.
**성능 기록** | 컴퓨터의 성능 데이터를 평가 하는 데 고려할 기간입니다. 이 속성은 크기 조정 기준이 *성능 기반*인 경우에만 적용할 수 있습니다.
**백분위 수 사용률** | 적정 크기를 산정하는 데 고려되는 성능 샘플 집합의 백분위수 값입니다. 이 속성은 크기 조정이 *성능 기반*인 경우에만 적용할 수 있습니다.
**VM 시리즈** |     크기 조정 시 고려할 VM 시리즈를 지정할 수 있습니다. 예를 들어 Azure에서 A 시리즈 VM으로 마이그레이션하지 않으려는 프로덕션 환경이 있는 경우 목록 또는 시리즈에서 A 시리즈를 제외하면 선택한 시리즈에서만 적절한 크기 조정이 수행됩니다.
**쾌적 인자** | Azure Migrate Server 평가는 평가 중에 버퍼 (편안한 요인)를 고려 합니다. 이 버퍼는 VM의 컴퓨터 사용률 데이터(CPU, 메모리, 디스크 및 네트워크)를 기반으로 적용됩니다. 쾌적 인자는 계절별 사용량, 성능 기록 부족, 향후 사용량 증가 가능성 등의 문제를 고려합니다.<br/><br/> 예를 들어 사용률이 20%인 10코어 VM은 일반적으로 2코어 VM이라는 결과가 나옵니다. 그러나 쾌적 인자가 2.0x이면 결과는 4코어 VM이 됩니다.
**제안** | 등록한 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)입니다. Azure Migrate는 그에 따라 비용을 예측합니다.
**Currency** | 청구 통화입니다.
**할인(%)** | Azure 제품에 적용되는 구독별 할인입니다.<br/> 기본 설정은 0%입니다.
**VM 작동 시간** | Azure에서 VM을 연중 무휴로 실행하지는 않으려는 경우 VM을 실행할 기간(월별 일 수/일별 시간 수)을 지정하면 해당 기간에 따라 예상 비용이 계산됩니다.<br/> 기본값은 월당 31일이며 일당 24시간입니다.
**Azure 하이브리드 혜택** | 소프트웨어 보증이 있고 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-use-benefit/)자격이 있는지 여부를 지정 합니다. [예]로 설정하면 Windows 외 Azure 가격이 Windows VM에 적용됩니다. 기본값은 [예]입니다.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Azure VMware 솔루션 (AVS) 평가의 기능

서버 평가에서 AVS 평가에 포함 된 같습니다는 다음과 같습니다.


| **속성** | **세부 정보** |
| - | - |
| **대상 위치** | 마이그레이션할 AVS 사설 클라우드 위치를 지정 합니다.<br/><br/> 서버 평가의 AVS 평가는 현재 미국 동부, 유럽 서부, 미국 서 부 등의 대상 지역을 지원 합니다. |
| **저장소 유형** | AVS에서 사용할 저장소 엔진을 지정 합니다.<br/><br/> AVS 평가는 vSAN을 기본 저장소 유형 으로만 지원 합니다. |
**예약 인스턴스 (RIs)** | 이 속성은 AVS에서 예약 인스턴스를 지정 하는 데 도움이 됩니다. RIs는 현재 AVS 노드에 대해 지원 되지 않습니다. |
**노드 유형** | 온-프레미스 Vm을 매핑하는 데 사용 되는 [AVS 노드 유형을](https://docs.microsoft.com/azure/azure-vmware/concepts-private-clouds-clusters) 지정 합니다. 기본 노드 형식은 AV36입니다. <br/><br/> Azure Migrate Vm을 AVS로 마이그레이션하기 위해 필요한 수의 노드를 권장 합니다. |
**FTT 설정, RAID 수준** | 허용 및 Raid 조합에 적용 가능한 실패를 지정 합니다. 온-프레미스 VM 디스크 요구 사항과 함께 선택한 FTT 옵션은 AVS에 필요한 총 vSAN 저장소를 결정 합니다. |
**크기 조정 기준** | AVS에 대해 vm을 적절 하 게 _크기 조정_ 하는 데 사용할 조건을 설정 합니다. 성능 _기반_ 크기 조정 또는 성능 기록을 고려 하지 않고 _온-프레미스로_ 선택할 수 있습니다. |
**성능 기록** | 컴퓨터의 성능 데이터를 평가 하는 데 고려할 기간을 설정 합니다. 이 속성은 크기 조정 기준이 _성능 기반_인 경우에만 적용할 수 있습니다. |
**백분위 수 사용률** | 오른쪽 크기 조정에 대해 고려할 성능 샘플 집합의 백분위 수 값을 지정 합니다. 이 속성은 크기 조정이 성능 기반 인 경우에만 적용할 수 있습니다.|
**쾌적 인자** | Azure Migrate Server 평가는 평가 중에 버퍼 (편안한 요인)를 고려 합니다. 이 버퍼는 VM의 컴퓨터 사용률 데이터(CPU, 메모리, 디스크 및 네트워크)를 기반으로 적용됩니다. 쾌적 인자는 계절별 사용량, 성능 기록 부족, 향후 사용량 증가 가능성 등의 문제를 고려합니다.<br/><br/> 예를 들어 사용률이 20%인 10코어 VM은 일반적으로 2코어 VM이라는 결과가 나옵니다. 그러나 쾌적 인자가 2.0x이면 결과는 4코어 VM이 됩니다. |
**제안** | 등록 된 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/) 을 표시 합니다. Azure Migrate는 그에 따라 비용을 예측합니다.|
**Currency** | 계정에 대 한 청구 통화를 표시 합니다. |
**할인(%)** | Azure 제품을 기반으로 받는 모든 구독 관련 할인을 나열 합니다. 기본 설정은 0%입니다. |
**Azure 하이브리드 혜택** | 소프트웨어 보증이 있고 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-use-benefit/)자격이 있는지 여부를 지정 합니다. 이는 노드 기반 가격으로 인 한 Azure VMware 솔루션 가격에는 영향을 주지 않지만, 고객은 Azure 하이브리드 혜택을 사용 하 여 온 프레미스 OS 라이선스 (Microsoft 기반)를 AVS에 계속 적용할 수 있습니다. 다른 소프트웨어 OS 공급 업체는 RHEL와 같은 고유한 라이선스 용어를 제공 해야 합니다. |
**vCPU 초과 구독** | AVS 노드에서 하나의 물리적 코어에 연결 된 가상 코어 수의 비율을 지정 합니다. 계산의 기본값은 4 vCPU: 1 실제 코어 (AVS의 경우)입니다. <br/><br/> API 사용자는이 값을 정수로 설정할 수 있습니다. VCPU 초과 구독 4:1 >은 성능 저하를 발생 시킬 수 있지만 웹 서버 유형 작업에 사용할 수 있습니다. |

## <a name="edit-assessment-properties"></a>평가 속성 편집

평가를 만든 후 평가 속성을 편집 하려면 다음을 수행 합니다.

1. Azure Migrate 프로젝트에서 **서버**를 클릭 합니다.
2. **Azure Migrate: 서버 평가**에서 평가 횟수를 클릭 합니다.
3. **평가**에서 관련 평가 > **편집 속성**을 클릭 합니다.
5. 위의 표에 따라 평가 속성을 사용자 지정 합니다.
6. **저장**을 클릭하여 평가를 업데이트합니다.


평가를 만들 때 평가 속성을 편집할 수도 있습니다.


## <a name="next-steps"></a>다음 단계

- Azure VM 평가를 계산 하는 방법에 [대해 자세히 알아보세요](concepts-assessment-calculation.md) .
- AVS 평가를 계산 하는 방법에 [대해 자세히 알아보세요](concepts-azure-vmware-solution-assessment-calculation.md) .
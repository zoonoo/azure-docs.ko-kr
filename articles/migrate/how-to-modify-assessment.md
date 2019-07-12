---
title: Azure Migrate Server 평가 대 한 평가 사용자 지정 | Microsoft Docs
description: Azure Migrate Server 평가 사용 하 여 만든 평가 사용자 지정 하는 방법을 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 8b200ce3d6e73a575b1b89d82a9323d58f435a48
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807918"
---
# <a name="customize-an-assessment"></a>평가 사용자 지정

이 문서에서는 Azure Migrate Server 평가 만든 평가 사용자 지정 하는 방법을 설명 합니다.

[Azure Migrate](migrate-services-overview.md) 검색, 평가 및 온-프레미스 앱 및 워크 로드 및 사설/공용 클라우드 Vm에서 Azure로 마이그레이션을 추적 하는 중앙 허브를 제공 합니다. 허브는 평가 및 마이그레이션 뿐만 아니라 타사 독립 소프트웨어 공급 업체 (ISV) 제품에 대 한 Azure Migrate 도구를 제공합니다.

Azure로 마이그레이션하기 위한 준비 과정에서 온-프레미스 VMware Vm 및 Hyper-v Vm에 대 한 평가 만들려면 Azure Migrate Server 평가 도구를 사용할 수 있습니다. 

## <a name="about-assessments"></a>평가 대 한

Azure Migrate Server 평가 사용 하 여 실행할 수 있습니다 평가 다음과 같은 두 종류가 있습니다.

**평가** | **세부 정보** | **데이터**
--- | --- | ---
**성능 기반** | 수집 된 성능 데이터를 기반으로 평가 | **권장 VM 크기**: CPU 및 메모리 사용률 데이터 기반으로 합니다.<br/><br/> **권장 되는 디스크 유형 (표준 또는 프리미엄 관리 디스크)** : 온-프레미스 디스크의 처리량 및 IOPS에 기반합니다.
**온-프레미스로** | 온-프레미스 크기 조정에 따라 평가 합니다. | **권장 VM 크기**: 온-프레미스 VM 크기에 따라<br/><br> **권장 되는 디스크 유형**: 평가 위해 선택한 저장소 형식 설정에 기반 합니다.


## <a name="how-is-an-assessment-done"></a>평가 수행 되는 방법

Azure Migrate 평가에는 세 가지 단계가 있습니다. 평가는 적합성 분석으로 시작되며 크기 조정, 월별 비용 예측이 차례로 진행됩니다. 컴퓨터는 이전 단계를 통과하는 경우에만 이후 단계로 이동합니다. 예를 들어 컴퓨터가 Azure 적합성 검사에 실패하면 Azure에 적합하지 않은 것으로 표시되고 크기 조정과 비용 예측이 수행되지 않습니다.

## <a name="whats-in-an-assessment"></a>평가의 내용

**Property** | **세부 정보**
--- | ---
**대상 위치** | 마이그레이션할 Azure 위치입니다.<br/> Azure Migrate는 현재 이러한 대상 영역을 지원합니다. 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 캐나다 중부, 캐나다 동부, 인도 중부, 미국 중부, 중국 동부, 중국 북부, 동아시아, 미국 동부, 미국 동부 2, 독일 중부, 독일 북동부, 일본 동부, 일본 서 부, 한국 중부, 한국 남부, 북부 미국 중부, 북유럽, 미국 중남부, 동남 아시아, 인도 남부, 영국 남부, 영국 서 부, 미국 정부 애리조나, 미국 정부 텍사스, 미국 버지니아 주 정부, 미국 중서부, 유럽 서 부, 인도 서 부, 미국 서 부 및 미국 서 부 2입니다.<br/> 기본적으로 대상 지역은 미국 서부 2로 설정되어 있습니다.
**저장소 유형** | 표준 HHD 디스크/표준 SSD 디스크/Premium입니다.<br/> 평가에서 자동으로 저장소 형식을 지정할 때 디스크 권장 사항 (IOPS 및 처리량) 디스크의 성능 데이터를 기반으로 합니다.<br/> 프리미엄/표준 저장소 유형을 지정 하면 저장소 형식 내에서 디스크 SKU 평가 권장 선택 합니다.<br/> 99.9% 단일 인스턴스 VM SLA를 달성 하려는 경우 프리미엄 managed disks로 저장소 형식을 설정할 수 있습니다. 다음 평가의 모든 디스크는 프리미엄 managed disks로 권장 됩니다. <br/> 마이그레이션을 평가하는 경우 Azure Migrate는 관리 디스크만 지원합니다.<br/> 
**Reserved Instances (RI)** | Azure에서 인스턴스를 예약 하는 경우이 속성을 지정 합니다. 평가에서 비용된 예측에 고려 RI 할인 걸립니다. 예약된 인스턴스는 현재 Azure Migrate의 종 량 제 제안에 대해서만 지원 합니다.
**크기 조정 기준** | 적합 한 크기로 Vm을 사용 합니다. 성능 기반 크기 조정 수 또는 **온-프레미스로**, 성능 기록을 고려 하지 않고 있습니다.
**성능 기록** | VM 성능 평가 대해 고려할 기간입니다. 이 속성은 성능 기반 크기 조정 경우에 적용 됩니다.
**백분위 수 사용률** | 적정 Vm에 사용 되는 성능 샘플의 백분위 수 값입니다. 이 속성은 성능 기반 크기 조정 경우에 적용 됩니다.
**VM 시리즈** | 크기 예측에 사용되는 VM 시리즈입니다. 예를 들어 Azure에서 A 시리즈 VM으로 마이그레이션하지 않으려는 프로덕션 환경이 있는 경우 목록 또는 시리즈에서 A 시리즈를 제외할 수 있습니다. 크기 조정은 선택한 시리즈만을 기반으로 합니다.
**쾌적 인자** | Azure Migrate Server 평가 평가 하는 동안 버퍼 (쾌적 인자)를 고려합니다. 이 버퍼는 VM의 컴퓨터 사용률 데이터(CPU, 메모리, 디스크 및 네트워크)를 기반으로 적용됩니다. 쾌적 인자는 계절별 사용량, 성능 기록 부족, 향후 사용량 증가 가능성 등의 문제를 고려합니다.<br/><br/> 예를 들어 사용률이 20%인 10코어 VM은 일반적으로 2코어 VM이라는 결과가 나옵니다. 그러나 쾌적 인자가 2.0x이면 결과는 4코어 VM이 됩니다.
**제안** | 등록한 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)입니다. Azure Migrate는 그에 따라 비용을 예측합니다.
**통화** | 청구 통화입니다. 
**할인(%)** | Azure 제품에 적용되는 구독별 할인입니다.<br/> 기본 설정은 0%입니다.
**VM 작동 시간** | Azure에서 VM을 연중 무휴로 실행하지는 않으려는 경우 VM을 실행할 기간(월별 일 수/일별 시간 수)을 지정하면 해당 기간에 따라 예상 비용이 계산됩니다.<br/> 기본값은 월당 31일이며 일당 24시간입니다.
**Azure 하이브리드 혜택** | 여부를 지정 하면 software assurance가 있는 적합 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)합니다. [예]로 설정하면 Windows 외 Azure 가격이 Windows VM에 적용됩니다. | 기본값은 Yes입니다.


## <a name="edit-assessment-properties"></a>평가 속성 편집

평가 만든 후 평가 속성을 편집 하려면 다음을 수행 합니다.

1. Azure Migrate 프로젝트에서 클릭 **서버**합니다.
2. **Azure Migrate: Server 평가**, 평가 개수를 클릭 합니다.
3. **평가**, 관련 평가 클릭 > **속성을 편집**합니다.
5. 위의 테이블에 따라 평가 속성을 사용자 지정 합니다.
6. **저장**을 클릭하여 평가를 업데이트합니다.


평가 만들 때에 평가 속성을 편집할 수 있습니다.


## <a name="next-steps"></a>다음 단계

평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).

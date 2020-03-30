---
title: Azure 마이그레이션을 사용하여 Azure로 마이그레이션할 수 있도록 많은 수의 Hyper-V VM 평가 | 마이크로 소프트 문서
description: Azure 마이그레이션 서비스를 사용하여 Azure로 마이그레이션할 수 있도록 많은 수의 Hyper-V VM을 평가하는 방법을 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70279450"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Azure로 마이그레이션할 수 있도록 많은 수의 Hyper-V VM 을 평가합니다.

이 문서에서는 Azure 마이그레이션 서버 평가 도구를 사용하여 Azure로 마이그레이션할 수 있는 많은 수의 온-프레미스 하이퍼 VVM을 평가하는 방법을 설명합니다.

[Azure Migrate](migrate-services-overview.md)는 앱, 인프라 및 워크로드를 검색, 평가 및 Microsoft Azure로 마이그레이션하는 데 도움이 되는 도구의 허브를 제공합니다. 허브에는 Azure Migrate 도구와 타사 ISV(독립 소프트웨어 공급업체) 제품이 포함되어 있습니다. 


이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * 대규모 평가 계획.
> * Azure 권한을 구성하고 평가를 위해 하이퍼-V를 준비합니다.
> * Azure 마이그레이션 프로젝트를 만들고 평가를 만듭니다.
> * 마이그레이션을 계획할 때 평가를 검토합니다.


> [!NOTE]
> 대규모로 평가하기 전에 개념 증명을 사용하여 몇 가지 VM을 평가하려면 [자습서 시리즈를](tutorial-prepare-hyper-v.md) 따르십시오.

## <a name="plan-for-assessment"></a>평가 계획

많은 수의 Hyper-VM에 대한 평가를 계획할 때 다음과 같은 몇 가지 사항을 생각해 볼 수 있습니다.

- **Azure 마이그레이션 프로젝트 계획**: Azure 마이그레이션 프로젝트를 배포하는 방법을 알아낸다. 예를 들어 데이터 센터가 다른 지역에 있거나 검색, 평가 또는 마이그레이션 관련 메타데이터를 다른 지역에 저장해야 하는 경우 여러 프로젝트가 필요할 수 있습니다.
- **계획 어플라이언스**: Azure Migrate는 하이퍼 V VM으로 배포된 온-프레미스 Azure 마이그레이션 어플라이언스를 사용하여 평가 및 마이그레이션을 위해 VM을 지속적으로 검색합니다. 어플라이언스는 VM, 디스크 또는 네트워크 어댑터 추가와 같은 환경 변경 사항을 모니터링합니다. 또한 메타데이터 및 성능 데이터를 Azure로 보냅니다. 배포할 어플라이언스 수를 파악해야 합니다.


## <a name="planning-limits"></a>계획 한도
 
계획에 는 이 표에 요약된 제한을 사용합니다.

**계획** | **제한**
--- | --- 
**Azure 마이그레이션 프로젝트** | 프로젝트에서 최대 35,000개의 VM을 평가합니다.
**Azure 마이그레이션 어플라이언스** | 어플라이언스는 최대 5,000개의 VM을 검색할 수 있습니다.<br/> 어플라이언스는 최대 300개의 Hyper-V 호스트에 연결할 수 있습니다.<br/> 어플라이언스는 단일 Azure 마이그레이션 프로젝트에만 연결할 수 있습니다.<br/> 임의의 수의 어플라이언스로 단일 Azure 마이그레이션 프로젝트와 연결할 수 있습니다. <br/><br/> 
**그룹** | 단일 그룹에 최대 35,000개의 VM을 추가할 수 있습니다.
**Azure 마이그레이션 평가** | 단일 평가에서 최대 35,000개의 VM을 평가할 수 있습니다.



## <a name="other-planning-considerations"></a>기타 계획 고려 사항

- 어플라이언스에서 검색을 시작하려면 각 Hyper-V 호스트를 선택해야 합니다. 
- 다중 테넌트 환경을 실행하는 경우 현재 특정 테넌트에 속한 VM만 검색할 수 없습니다. 

## <a name="prepare-for-assessment"></a>평가 준비

서버 평가를 위해 Azure 및 Hyper-V를 준비합니다. 

1. [하이퍼-V 지원 요구 사항 및 제한 사항을 확인합니다.](migrate-support-matrix-hyper-v.md)
2. Azure 계정마이그레이션과 상호 작용할 수 있는 권한 설정
3. 하이퍼 V 호스트 및 VM 준비

[이 자습서의](tutorial-prepare-hyper-v.md) 지침에 따라 이러한 설정을 구성합니다.

## <a name="create-a-project"></a>프로젝트 만들기

계획 요구 사항에 따라 다음을 수행합니다.

1. Azure 마이그레이션 프로젝트를 만듭니다.
2. 프로젝트에 Azure 마이그레이션 서버 평가 도구를 추가합니다.

[자세히 알아보기](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>평가 작성 및 검토

1. 하이퍼 VM에 대한 평가를 만듭니다.
1. 마이그레이션 계획을 준비하기 위해 평가를 검토합니다.

평가 작성 및 검토에 대해 [자세히 알아보세요.](tutorial-assess-hyper-v.md)
    

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 작업을 수행합니다.
 
> [!div class="checklist"] 
> * 하이퍼 VM에 대한 Azure 마이그레이션 평가 확장 계획
> * 평가를 위해 준비된 Azure 및 하이퍼-V
> * Azure 마이그레이션 프로젝트를 만들고 평가를 실행했습니다.
> * 마이그레이션 준비에 대한 검토된 평가입니다.

이제 평가를 계산하는 방법과 [평가를 수정하는](how-to-modify-assessment.md) 방법을 [알아봅니다.](concepts-assessment-calculation.md)

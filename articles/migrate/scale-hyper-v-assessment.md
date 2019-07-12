---
title: Azure Migrate로 Azure로의 마이그레이션에 대 한 많은 수의 Hyper-v Vm 평가 | Microsoft Docs
description: Azure Migrate 서비스를 사용 하 여 Azure로 마이그레이션에 대 한 많은 수의 Hyper-v Vm을 평가 하는 방법에 설명 합니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 95704f2694892b349d0967fca2160dabd990b472
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811545"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Azure로의 마이그레이션에 대 한 많은 수의 Hyper-v Vm 평가

이 문서에서는 Azure Migrate Server 평가 도구를 사용 하 여 Azure로의 마이그레이션 위한 온-프레미스 Hyper-v Vm의 많은 수 (> 1000)을 평가 하는 방법을 설명 합니다.

[Azure Migrate](migrate-services-overview.md) 는 허브를 검색, 평가 및 Microsoft Azure에 앱, 인프라 및 워크 로드를 마이그레이션할 수 있도록 도구를 제공 합니다. 허브는 Azure Migrate 도구 및 타사 독립 소프트웨어 공급 업체 (ISV) 제품에 포함 되어 있습니다. 


이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * 대규모로 평가 대 한 계획입니다.
> * Azure 사용 권한을 구성 하 고 평가 위해 Hyper-v를 준비 합니다.
> * Azure Migrate 프로젝트를 만들고 평가 합니다.
> * 마이그레이션에 대 한 계획을 수립할 때에 평가 검토 합니다.


> [!NOTE]
> -개념 증명 대규모로 평가 하기 전에 몇 개의 Vm 평가 하려면 다음과 아웃 하려는 경우이 [자습서 시리즈](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>평가 대 한 계획

많은 수의 Hyper-v Vm에 대 한 평가 계획할 때 몇 가지 고려할 사항이 가지가 있습니다.

- **Azure Migrate 프로젝트 계획**: Azure Migrate 프로젝트를 배포 하는 방법을 파악 합니다. 예를 들어, 여러 지역에는 데이터 센터 또는 다른 지역에서 검색, 평가 또는 마이그레이션 관련 메타 데이터를 저장 해야 하는 경우에 여러 프로젝트 해야 할 수 있습니다.
- **어플라이언스 계획**: Azure Migrate는 온-프레미스 Azure Migrate 어플라이언스, Hyper-v VM으로 배포를 사용 하 여 지속적으로 평가 및 마이그레이션에 대 한 Vm을 검색. 어플라이언스 Vm, 디스크 또는 네트워크 어댑터를 추가 하는 등 환경 변화를 모니터링 합니다. 또한 azure에 대 한 메타 데이터 및 성능 데이터를 보냅니다. 배포에 얼마나 많은 어플라이언스를 파악 해야 합니다.


## <a name="planning-limits"></a>제한 계획
 
계획에 대 한 다음 표에 요약 된 한도 사용 합니다.

**계획** | **제한**
--- | --- 
**Azure Migrate 프로젝트** | 프로젝트에서 최대 10,000 개의 Vm을 평가 합니다.
**Azure Migrate 어플라이언스** | 어플라이언스 최대 5000 개의 Vm을 검색할 수 있습니다.<br/> 어플라이언스 최대 300 Hyper-v 호스트에 연결할 수 있습니다.<br/> 어플라이언스를 단일 Azure Migrate 프로젝트만 연결할 수 있습니다.<br/><br/> 
**Azure Migrate 평가** | 단일 평가에서 최대 10,000 개의 Vm을 평가할 수 있습니다.



## <a name="other-planning-considerations"></a>다른 계획 고려 사항

- 어플라이언스에서 검색을 시작 하려면 각 Hyper-v 호스트를 선택 해야 합니다. 
- 다중 테 넌 트 환경에서 실행 하는 경우 현재 특정 테 넌 트에 속한 Vm만 검색할 수 없습니다. 

## <a name="prepare-for-assessment"></a>평가 준비

Azure 및 Hyper-v server 평가 대 한 준비 합니다. 

1. 확인할 [Hyper-v 지원 요구 사항 및 제한 사항](migrate-support-matrix-hyper-v.md)합니다.
2. Azure Migrate와 상호 작용 하 여 Azure 계정에 대 한 사용 권한 설정
3. Hyper-v 호스트 및 Vm 준비

지침을 따릅니다 [이 자습서](tutorial-prepare-hyper-v.md) 이러한 설정을 구성 해야 합니다.

## <a name="create-a-project"></a>프로젝트 만들기

계획 요구 사항에 따라 다음을 수행 합니다.

1. Azure Migrate 프로젝트를 만듭니다.
2. 프로젝트에 Azure Migrate Server 평가 도구를 추가 합니다.

[자세히 알아보기](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>만들기 및 검토 평가

1. Hyper-v Vm에 대 한 평가 만듭니다.
1. 마이그레이션 계획을 위한 준비 과정에서 평가 검토 합니다.

[자세한](tutorial-assess-hyper-v.md) 만들기 및 검토 평가 하는 방법에 대 한 합니다.
    

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 작업을 수행합니다.
 
> [!div class="checklist"] 
> * Azure Migrate 평가 Hyper-v Vm에 대 한 확장 계획
> * Azure 및 Hyper-v 평가 위한 준비
> * Azure Migrate 프로젝트를 만들고 평가 실행 합니다.
> * 마이그레이션 위한 준비 과정에서 평가 검토합니다.

이제 [알아봅니다 어떻게](concepts-assessment-calculation.md) 평가 계산 하는 방법과 [평가 수정 합니다.](how-to-modify-assessment.md)

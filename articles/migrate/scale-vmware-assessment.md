---
title: Azure Migrate로 Azure로의 마이그레이션에 대 한 많은 수의 VMware Vm 평가 | Microsoft Docs
description: Azure Migrate 서비스를 사용 하 여 Azure로의 마이그레이션을 위해 VMware Vm의 큰 숫자를 평가 하는 방법에 설명 합니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 6102a1c59be3627b95dc1e0cb1d1d712d5832d2f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811337"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Azure로의 마이그레이션에 대 한 많은 수의 VMware Vm 평가


이 문서에서는 Azure Migrate Server 평가 도구를 사용 하 여 Azure로의 마이그레이션 위한 온-프레미스 VMware Vm의 많은 수 (1000 35,000) 평가 하는 방법을 설명 합니다.

[Azure Migrate](migrate-services-overview.md) 는 허브를 검색, 평가 및 Microsoft Azure에 앱, 인프라 및 워크 로드를 마이그레이션할 수 있도록 도구를 제공 합니다. 허브는 Azure Migrate 도구 및 타사 독립 소프트웨어 공급 업체 (ISV) 제품에 포함 되어 있습니다. 

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * 대규모로 평가 대 한 계획입니다.
> * Azure 사용 권한을 구성 하 고 평가 위해 VMware를 준비 합니다.
> * Azure Migrate 프로젝트를 만들고 평가 합니다.
> * 마이그레이션에 대 한 계획을 수립할 때에 평가 검토 합니다.


> [!NOTE]
> -개념 증명 대규모로 평가 하기 전에 몇 개의 Vm 평가 하려면 다음과 아웃 하려는 경우이 [자습서 시리즈](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>평가 대 한 계획

많은 수의 VMware Vm에 대 한 평가 계획할 때 몇 가지 고려할 사항이 가지가 있습니다.

- **Azure Migrate 프로젝트 계획**: Azure Migrate 프로젝트를 배포 하는 방법을 파악 합니다. 예를 들어, 여러 지역에는 데이터 센터 또는 다른 지역에서 검색, 평가 또는 마이그레이션 관련 메타 데이터를 저장 해야 하는 경우에 여러 프로젝트 해야 할 수 있습니다. 
- **어플라이언스 계획**: Azure Migrate는 온-프레미스 Azure Migrate 기기를 VMware VM으로 배포를 사용 하 여 지속적으로 Vm을 검색. 어플라이언스 Vm, 디스크 또는 네트워크 어댑터를 추가 하는 등 환경 변화를 모니터링 합니다. 또한 azure에 대 한 메타 데이터 및 성능 데이터를 보냅니다. 배포 해야 하는 얼마나 많은 어플라이언스를 파악 해야 합니다.
- **검색에 대 한 계정을 계획**: Azure Migrate 어플라이언스 평가 및 마이그레이션을 위해 Vm을 검색 하기 위해 vCenter Server에 대 한 액세스를 사용 하 여 계정을 사용 합니다. 10,000 개 보다 많은 Vm을 검색 하는 경우 여러 계정을 설정 합니다.


## <a name="planning-limits"></a>제한 계획
 
계획에 대 한 다음 표에 요약 된 한도 사용 합니다.

**계획** | **제한**
--- | --- 
**Azure Migrate 프로젝트** | 프로젝트에서 최대 35,000 Vm을 평가 합니다.
**Azure Migrate 어플라이언스** | 어플라이언스 단일 vCenter Server에 연결할 수 있습니다.<br/><br/> 어플라이언스를 단일 Azure Migrate 프로젝트만 연결할 수 있습니다.<br/> 어플라이언스는 vCenter Server에서 최대 10,000 개의 Vm을 검색할 수 있습니다.
**Azure Migrate 평가** | 단일 평가에서 최대 35,000 Vm을 평가할 수 있습니다.

다음은 염두에서 이러한 한도 사용 하 여 몇 가지 예제 배포입니다.


**vCenter 서버** | **서버에서 Vm** | **권장 사항** | **동작**
---|---|---
1 | < 10,000 | 하나의 Azure Migrate 프로젝트입니다.<br/> 한 어플라이언스입니다.<br/> 검색에 대 한 하나의 vCenter 계정입니다. | 어플라이언스를 계정으로 vCenter Server에 연결 합니다.
1 | > 10,000 | 하나의 Azure Migrate 프로젝트입니다.<br/> 여러 어플라이언스입니다.<br/> 여러 vCenter 계정입니다. | 10,000 모든 Vm에 대 한 어플라이언스를 설정 합니다.<br/><br/> VCenter 계정을 설정 하 고 10,000 개 보다 작은 Vm에는 계정에 대 한 액세스를 제한 하는 인벤토리를 나눕니다.<br/> 각 어플라이언스는 계정으로 vCenter 서버에 연결 합니다.<br/> 다른 어플라이언스로 검색 되는 컴퓨터 간에 종속성을 분석할 수 있습니다.
다중 | < 10,000 |  하나의 Azure Migrate 프로젝트입니다.<br/> 여러 어플라이언스입니다.<br/> 검색에 대 한 하나의 vCenter 계정입니다. | 어플라이언스를 계정으로 vCenter Server에 연결 합니다.<br/> 다른 어플라이언스로 검색 되는 컴퓨터 간에 종속성을 분석할 수 있습니다.
다중 | > 10,000 | 하나의 Azure Migrate 프로젝트입니다.<br/> 여러 어플라이언스입니다.<br/> 여러 vCenter 계정입니다. | VCenter 서버 검색 < 10,000 Vm 설정 각 vCenter Server에 대 한 어플라이언스입니다.<br/><br/> 하는 경우 vCenter 서버 검색 > 10,000 Vm 설정 어플라이언스 10,000 모든 Vm에 대 한 합니다.<br/> VCenter 계정을 설정 하 고 10,000 개 보다 작은 Vm에는 계정에 대 한 액세스를 제한 하는 인벤토리를 나눕니다.<br/> 각 어플라이언스는 계정으로 vCenter 서버에 연결 합니다.<br/> 다른 어플라이언스로 검색 되는 컴퓨터 간에 종속성을 분석할 수 있습니다.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>다중 테 넌 트 환경에서 검색 계획

다중 테 넌 트 환경에 대 한 계획인 경우 vCenter Server에서 검색을 범위를 수 있습니다.

- VCenter Server datacenter, 클러스터 또는 클러스터에서 호스트 또는 호스트 또는 개별 Vm의 폴더의 폴더에 어플라이언스 검색 범위를 설정할 수 있습니다.
- 환경의 테 넌 트 간에 공유 되 고 개별적으로 각 테 넌 트를 검색 하지 않으려는 경우 검색에 대 한 어플라이언스는 vCenter 계정에 대 한 액세스를 범위 지정할 수 있습니다. 
    - 테 넌 트 호스트를 공유 하는 경우에 특정 테 넌 트에 속한 Vm에 대 한 자격 증명 읽기 전용 액세스를 사용 하 여 만듭니다. 
    - Azure Migrate 어플라이언스 검색에 대 한 자격이 증명을 사용 합니다.
    - Azure Migrate 평가 vCenter 계정 권한이 vCenter VM 폴더 수준에서 부여 하는 경우 Vm을 검색할 수 없습니다. 호스트 및 클러스터의 폴더 지원 됩니다. 

## <a name="prepare-for-assessment"></a>평가 준비

Azure 및 VMware server 평가 위해 준비 합니다. 

1. 확인할 [VMware 지원 요구 사항 및 제한 사항](migrate-support-matrix-vmware.md)합니다.
2. Azure Migrate와 상호 작용 하 여 Azure 계정에 대 한 권한을 설정 합니다.
3. 평가 대 한 VMware를 준비 합니다.


지침을 따릅니다 [이 자습서](tutorial-prepare-vmware.md) 이러한 설정을 구성 해야 합니다.


## <a name="create-a-project"></a>프로젝트 만들기

계획 요구 사항에 따라 다음을 수행 합니다.

1. Azure Migrate 프로젝트를 만듭니다.
2. 프로젝트에 Azure Migrate Server 평가 도구를 추가 합니다.

[자세히 알아보기](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>만들기 및 검토 평가

1. VMware Vm에 대 한 평가 만듭니다.
1. 마이그레이션 계획을 위한 준비 과정에서 평가 검토 합니다.


지침을 따릅니다 [이 자습서](tutorial-assess-vmware.md) 이러한 설정을 구성 해야 합니다.
    

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 작업을 수행합니다.
 
> [!div class="checklist"] 
> * Azure Migrate 평가 VMware Vm에 대 한 확장 계획
> * Azure 및 평가 대 한 VMware 준비
> * Azure Migrate 프로젝트를 만들고 평가 실행 합니다.
> * 마이그레이션 위한 준비 과정에서 평가 검토합니다.

이제 [알아봅니다 어떻게](concepts-assessment-calculation.md) 평가 계산 하는 방법과 [평가 수정](how-to-modify-assessment.md)합니다.

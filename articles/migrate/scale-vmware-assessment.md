---
title: Azure 마이그레이션을 사용하여 Azure로 마이그레이션할 VM의 많은 수 평가
description: Azure 마이그레이션 서비스를 사용하여 Azure로 마이그레이션할 VM을 대량으로 평가하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: d404583b1bad474a5e24e8c7cf060aeb80d610bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336861"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Azure로 마이그레이션할 VM웨어 VM의 다수 평가


이 문서에서는 Azure 마이그레이션 서버 평가 도구를 사용하여 Azure로 마이그레이션하기 위해 온-프레미스 VMware VM의 많은 수(1000-35,000)를 평가하는 방법을 설명합니다.

[Azure Migrate](migrate-services-overview.md)는 앱, 인프라 및 워크로드를 검색, 평가 및 Microsoft Azure로 마이그레이션하는 데 도움이 되는 도구의 허브를 제공합니다. 허브에는 Azure Migrate 도구와 타사 ISV(독립 소프트웨어 공급업체) 제품이 포함되어 있습니다. 

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * 대규모 평가 계획.
> * Azure 권한을 구성하고 평가를 위해 VMware를 준비합니다.
> * Azure 마이그레이션 프로젝트를 만들고 평가를 만듭니다.
> * 마이그레이션을 계획할 때 평가를 검토합니다.


> [!NOTE]
> 대규모로 평가하기 전에 개념 증명을 사용하여 몇 가지 VM을 평가하려면 [자습서 시리즈를](tutorial-prepare-vmware.md) 따르십시오.

## <a name="plan-for-assessment"></a>평가 계획

많은 수의 VM웨어 VM에 대한 평가를 계획할 때 다음과 같은 몇 가지 사항을 생각해 볼 수 있습니다.

- **Azure 마이그레이션 프로젝트 계획**: Azure 마이그레이션 프로젝트를 배포하는 방법을 알아낸다. 예를 들어 데이터 센터가 다른 지역에 있거나 검색, 평가 또는 마이그레이션 관련 메타데이터를 다른 지역에 저장해야 하는 경우 여러 프로젝트가 필요할 수 있습니다. 
- **계획 어플라이언스**: Azure Migrate는 VMware VM으로 배포된 온-프레미스 Azure 마이그레이션 어플라이언스를 사용하여 VM을 지속적으로 검색합니다. 어플라이언스는 VM, 디스크 또는 네트워크 어댑터 추가와 같은 환경 변경 사항을 모니터링합니다. 또한 메타데이터 및 성능 데이터를 Azure로 보냅니다. 배포해야 하는 어플라이언스 수를 파악해야 합니다.
- **검색을 위한 계획 계정**: Azure 마이그레이션 어플라이언스는 평가 및 마이그레이션을 위해 VM을 검색하기 위해 vCenter Server에 액세스할 수 있는 계정을 사용합니다. 10,000개 이상의 VM을 발견하는 경우 여러 계정을 설정합니다.


## <a name="planning-limits"></a>계획 한도
 
계획에 는 이 표에 요약된 제한을 사용합니다.

**계획** | **제한**
--- | --- 
**Azure 마이그레이션 프로젝트** | 프로젝트에서 최대 35,000개의 VM을 평가합니다.
**Azure 마이그레이션 어플라이언스** | 어플라이언스는 vCenter 서버에서 최대 10,000개의 VM을 검색할 수 있습니다.<br/> 어플라이언스는 단일 vCenter 서버에만 연결할 수 있습니다.<br/> 어플라이언스는 단일 Azure 마이그레이션 프로젝트에만 연결할 수 있습니다.<br/>  임의의 수의 어플라이언스로 단일 Azure 마이그레이션 프로젝트와 연결할 수 있습니다. <br/><br/> 
**그룹** | 단일 그룹에 최대 35,000개의 VM을 추가할 수 있습니다.
**Azure 마이그레이션 평가** | 단일 평가에서 최대 35,000개의 VM을 평가할 수 있습니다.

이러한 제한을 염두에 두고 몇 가지 예제 배포는 다음과 같습니다.


**vCenter Server** | **서버의 VM** | **추천** | **작업**
---|---|---
1 | < 10,000 | 하나의 Azure 마이그레이션 프로젝트입니다.<br/> 하나의 어플라이언스.<br/> 하나의 vCenter 는 검색을 차지합니다. | 어플라이언스를 설정하고 계정으로 vCenter 서버에 연결합니다.
1 | > 10,000 | 하나의 Azure 마이그레이션 프로젝트입니다.<br/> 여러 어플라이언스.<br/> 여러 vCenter 계정. | 10,000개의 VM마다 어플라이언스를 설정합니다.<br/><br/> vCenter 계정을 설정하고 인벤토리를 분할하여 계정에 대한 액세스를 10,000개 미만의 VM 미만으로 제한합니다.<br/> 계정으로 각 어플라이언스를 vCenter 서버에 연결합니다.<br/> 서로 다른 어플라이언스로 검색된 컴퓨터 간의 종속성을 분석할 수 있습니다.
여러 접두사 | < 10,000 |  하나의 Azure 마이그레이션 프로젝트입니다.<br/> 여러 어플라이언스.<br/> 하나의 vCenter 는 검색을 차지합니다. | 어플라이언스 설정, 계정으로 vCenter 서버에 연결합니다.<br/> 서로 다른 어플라이언스로 검색된 컴퓨터 간의 종속성을 분석할 수 있습니다.
여러 접두사 | > 10,000 | 하나의 Azure 마이그레이션 프로젝트입니다.<br/> 여러 어플라이언스.<br/> 여러 vCenter 계정. | vCenter Server 검색이 10,000개의 VM을 < 경우 각 vCenter 서버에 대한 어플라이언스를 설정합니다.<br/><br/> vCenter Server 검색이 10,000개의 VM에 > 경우 10,000개의 VM마다 어플라이언스를 설정합니다.<br/> vCenter 계정을 설정하고 인벤토리를 분할하여 계정에 대한 액세스를 10,000개 미만의 VM 미만으로 제한합니다.<br/> 계정으로 각 어플라이언스를 vCenter 서버에 연결합니다.<br/> 서로 다른 어플라이언스로 검색된 컴퓨터 간의 종속성을 분석할 수 있습니다.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>다중 테넌트 환경에서 검색 계획

다중 테넌트 환경을 계획하는 경우 vCenter Server에서 검색 범위를 지정할 수 있습니다.

- 어플라이언스 검색 범위를 vCenter Server 데이터 센터, 클러스터 또는 클러스터 폴더, 호스트 또는 호스트 폴더 또는 개별 VM으로 설정할 수 있습니다.
- 환경이 테넌트 간에 공유되고 각 테넌트를 별도로 검색하려는 경우 어플라이언스가 검색에 사용하는 vCenter 계정에 대한 액세스 범위를 지정할 수 있습니다. 
    - 테넌티가 호스트를 공유하는 경우 VM 폴더별로 범위를 지정할 수 있습니다. azure 마이그레이션 vCenter 계정에 vCenter VM 폴더 수준에서 액세스 권한이 부여된 경우 VM을 검색할 수 없습니다. 검색 범위를 VM 폴더로 지정하려는 경우 vCenter 계정에 VM 수준의 읽기 전용 액세스 권한이 할당되도록 하여 검색을 수행할 수 있습니다. [자세히 알아봅니다](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>평가 준비

서버 평가를 위해 Azure 및 VMware를 준비합니다. 

1. [VMware 지원 요구 사항 및 제한 사항을 확인합니다.](migrate-support-matrix-vmware.md)
2. Azure 계정마이그레이션과 상호 작용할 수 있는 권한을 설정합니다.
3. 평가를 위해 VMware를 준비합니다.

[이 자습서의](tutorial-prepare-vmware.md) 지침에 따라 이러한 설정을 구성합니다.


## <a name="create-a-project"></a>프로젝트 만들기

계획 요구 사항에 따라 다음을 수행합니다.

1. Azure 마이그레이션 프로젝트를 만듭니다.
2. 프로젝트에 Azure 마이그레이션 서버 평가 도구를 추가합니다.

[자세히 알아보기](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>평가 작성 및 검토

1. VMware VM에 대한 평가를 만듭니다.
1. 마이그레이션 계획을 준비하기 위해 평가를 검토합니다.


[이 자습서의](tutorial-assess-vmware.md) 지침에 따라 이러한 설정을 구성합니다.
    

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 작업을 수행합니다.
 
> [!div class="checklist"] 
> * VMware VM에 대한 Azure 마이그레이션 평가 확장 계획
> * 평가를 위해 준비된 Azure 및 VMware
> * Azure 마이그레이션 프로젝트를 만들고 평가를 실행했습니다.
> * 마이그레이션 준비에 대한 검토된 평가입니다.

이제 평가를 계산하는 방법과 [평가를 수정하는](how-to-modify-assessment.md)방법을 [알아봅니다.](concepts-assessment-calculation.md)

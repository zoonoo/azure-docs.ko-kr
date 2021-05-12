---
title: Azure Migrate를 사용하여 Azure로 마이그레이션할 VMware 환경에서 대량의 서버 평가
description: Azure Migrate 서비스를 사용하여 Azure로 마이그레이션할 VMware 환경에서 대량의 서버를 평가하는 방법을 설명합니다.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10b8aaeaa25e49140dbf6f31c064c7f823d23e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778256"
---
# <a name="assess-large-numbers-of-servers-in-vmware-environment-for-migration-to-azure"></a>Azure로 마이그레이션할 VMware 환경에서 대량의 서버 평가


이 문서에서는 Azure Migrate 검색 및 평가 도구를 사용하여 Azure로 마이그레이션할 VMware 환경에서 대량(1,000~35,000대)의 온-프레미스 서버를 평가하는 방법을 설명합니다.

[Azure Migrate](migrate-services-overview.md)는 앱, 인프라 및 워크로드를 검색, 평가 및 Microsoft Azure로 마이그레이션하는 데 도움이 되는 도구의 허브를 제공합니다. 허브에는 Azure Migrate 도구와 타사 ISV(독립 소프트웨어 공급업체) 제품이 포함되어 있습니다. 

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * 대규모로 평가를 계획합니다.
> * 평가를 위해 Azure 권한을 구성하고 VMware를 준비합니다.
> * Azure Migrate 프로젝트를 만들고 평가를 만듭니다.
> * 마이그레이션을 계획할 때 평가를 검토합니다.


> [!NOTE]
> 대규모로 평가하기 전에 개념 증명을 사용해 서버 몇 대를 평가해 보려는 경우 [자습서 시리즈](./tutorial-discover-vmware.md)를 따르세요.

## <a name="plan-for-assessment"></a>평가 계획

VMware 환경에서 대량의 서버에 대한 평가를 계획할 때 고려해야 하는 사항이 몇 가지 있습니다.

- **Azure Migrate 프로젝트 계획**: Azure Migrate 프로젝트 배포 방식을 파악합니다. 예를 들어 데이터 센터가 다른 지역에 있거나 검색, 평가 또는 마이그레이션 관련 메타데이터를 다른 지역에 저장해야 하는 경우 여러 프로젝트가 필요할 수 있습니다. 
- **어플라이언스 계획**: Azure Migrate는 VMware VM으로 배포된 온-프레미스 Azure Migrate 어플라이언스를 사용하여 서버를 지속적으로 검색합니다. 어플라이언스는 서버, 디스크 또는 네트워크 어댑터를 추가하는 등의 환경 변화를 모니터링합니다. 또한 이에 대한 메타데이터 및 성능 데이터를 Azure로 보냅니다. 배포해야 하는 어플라이언스 수를 파악해야 합니다.
- **검색 계정 계획**: Azure Migrate 어플라이언스는 평가 및 마이그레이션할 서버를 검색하기 위해 vCenter Server에 대한 액세스 권한이 있는 계정을 사용합니다. 10,000대보다 많은 서버를 검색하는 경우, 필요에 따라 여러 계정을 설정합니다. 프로젝트의 두 어플라이언스에서 검색된 서버 간에는 겹치는 부분이 없습니다. 

> [!NOTE]
> 여러 어플라이언스를 설정하는 경우 제공된 vCenter 계정에 있는 서버 간에 겹치는 부분이 없어야 합니다. 이러한 겹치는 검색은 지원되지 않는 시나리오입니다. 서버를 둘 이상의 어플라이언스를 통해 검색하는 경우 서버 마이그레이션에서 Azure Portal을 사용하여 서버 복제를 활성화한 동안에는 검색 및 문제에 겹치는 부분이 발생합니다.

## <a name="planning-limits"></a>제한 계획
 
계획을 위해 이 테이블에 요약되어 있는 제한을 사용합니다.

**계획** | **제한**
--- | --- 
**Azure Migrate 프로젝트** | 프로젝트에서 최대 35,000대 서버를 평가합니다.
**Azure Migrate 어플라이언스** | 어플라이언스는 vCenter Server에서 최대 10,000대의 서버를 검색할 수 있습니다.<br/> 어플라이언스는 단일 vCenter Server에만 연결할 수 있습니다.<br/> 어플라이언스는 단일 Azure Migrate 프로젝트에만 연결할 수 있습니다.<br/>  어플라이언스는 개수와 관계없이 단일 Azure Migrate 프로젝트에 연결할 수 있습니다. <br/><br/> 
**그룹** | 단일 그룹에 최대 35,000대의 서버를 추가할 수 있습니다.
**Azure Migrate 평가**: | 단일 평가에서 최대 35,000대의 서버를 평가할 수 있습니다.

다음은 해당 한도를 염두에 둔 몇 가지 배포 예제입니다.


**vCenter Server** | **처리 가능한 서버 수** | **권장** | **동작**
---|---|---|---
하나 | < 10,000 | Azure Migrate 프로젝트 하나입니다.<br/> 어플라이언스 하나입니다.<br/> 검색을 위한 vCenter 계정 하나입니다. | 어플라이언스를 설정하고 계정을 사용하여 vCenter Server에 연결합니다.
하나 | > 10,000 | Azure Migrate 프로젝트 하나입니다.<br/> 여러 어플라이언스입니다.<br/> 여러 vCenter 계정입니다. | 10,000대 서버 모두에 대한 어플라이언스를 설정합니다.<br/><br/> vCenter 계정을 설정하고, 계정에 대한 액세스를 10,000대 서버 미만으로 제한하도록 인벤토리를 나눕니다.<br/> 계정을 사용하여 각 어플라이언스를 vCenter Server에 연결합니다.<br/> 다른 어플라이언스를 통해 검색되는 서버에서 종속성을 분석할 수 있습니다. <br/> <br/> 제공된 vCenter 계정에서 서버 간에 겹치는 부분이 없어야 합니다. 이러한 겹치는 검색은 지원되지 않는 시나리오입니다. 서버를 둘 이상의 어플라이언스를 통해 검색하는 경우 서버 마이그레이션에서 Azure Portal을 사용하여 서버 복제를 활성화한 동안에는 검색 및 문제에 겹치는 부분이 발생합니다.
여러 접두사 | < 10,000 |  Azure Migrate 프로젝트 하나입니다.<br/> 여러 어플라이언스입니다.<br/> 검색을 위한 vCenter 계정 하나입니다. | 어플라이언스를 설정하고 계정을 사용하여 vCenter Server에 연결합니다.<br/> 다른 어플라이언스를 통해 검색되는 서버에서 종속성을 분석할 수 있습니다.
여러 접두사 | > 10,000 | Azure Migrate 프로젝트 하나입니다.<br/> 여러 어플라이언스입니다.<br/> 여러 vCenter 계정입니다. | vCenter Server 검색이 10,000대 서버 미만이면 vCenter Server마다 어플라이언스를 하나씩 설정합니다.<br/><br/> vCenter Server 검색이 10,000대 서버를 초과하면 10,000대 서버 모두에 대한 어플라이언스를 하나 설정합니다.<br/> vCenter 계정을 설정하고, 계정에 대한 액세스를 10,000대 서버 미만으로 제한하도록 인벤토리를 나눕니다.<br/> 계정을 사용하여 각 어플라이언스를 vCenter Server에 연결합니다.<br/> 다른 어플라이언스를 통해 검색되는 서버에서 종속성을 분석할 수 있습니다. <br/><br/> 제공된 vCenter 계정에서 서버 간에 겹치는 부분이 없어야 합니다. 이러한 겹치는 검색은 지원되지 않는 시나리오입니다. 서버를 둘 이상의 어플라이언스를 통해 검색하는 경우 서버 마이그레이션에서 Azure Portal을 사용하여 서버 복제를 활성화한 동안에는 검색 및 문제에 겹치는 부분이 발생합니다.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>다중 테넌트 환경에서 검색 계획

다중 테넌트 환경을 계획하는 경우 vCenter Server에 대한 검색 범위를 지정할 수 있습니다.

- 어플라이언스 검색 범위를 vCenter Server 데이터 센터, 클러스터 또는 클러스터의 폴더, 호스트 또는 호스트의 폴더 또는 개별 서버로 설정할 수 있습니다.
- 사용자 환경이 테넌트 간에 공유되고 각 테넌트를 별도로 검색하려는 경우 어플라이언스에서 검색에 사용하는 vCenter 계정에 대한 액세스 범위를 지정할 수 있습니다. 
    - 테넌트가 호스트를 공유하는 경우 범위를 VM 폴더로 지정할 수 있습니다. 현재 vCenter VM 폴더 수준의 액세스 권한이 vCenter 계정에 부여된 경우 Azure Migrate에서 서버를 검색할 수 없습니다. 검색 범위를 VM 폴더로 지정하려는 경우 vCenter 계정에 서버 수준의 읽기 전용 액세스 권한이 할당되도록 하여 검색을 수행할 수 있습니다. [자세히 알아보기](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>평가 준비

Azure 및 VMware에 검색 및 평가 도구 준비:

1. [VMware 지원 요구 사항 및 제한 사항](migrate-support-matrix-vmware.md)을 확인합니다.
2. Azure Migrate에서 상호 작용할 Azure 계정에 대한 권한을 설정합니다.
3. 평가할 VMware를 준비합니다.

[이 자습서](./tutorial-discover-vmware.md)의 지침에 따라 관련 설정을 구성합니다.


## <a name="create-a-project"></a>프로젝트 만들기

계획 요구 사항에 따라 다음을 수행합니다.

1. Azure Migrate 프로젝트를 만듭니다.
2. 프로젝트에 Azure Migrate 검색 및 평가 도구를 추가합니다.

[자세한 정보](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>평가를 만들고 검토합니다.

1. VMware 환경에서 서버에 대한 평가를 만듭니다.
1. 마이그레이션 계획 준비 과정에서 평가를 검토합니다.


[이 자습서](./tutorial-assess-vmware-azure-vm.md)의 지침에 따라 관련 설정을 구성합니다.
    

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 작업을 수행합니다.
 
> [!div class="checklist"] 
> * VMware 환경의 서버에 대한 Azure 마이그레이션 평가를 스케일링할 계획을 했습니다.
> * 평가를 위해 Azure와 VMware를 준비했습니다.
> * Azure Migrate 프로젝트를 만들고 평가를 실행했습니다.
> * 마이그레이션 준비 과정에서 평가를 검토했습니다.

이제 평가를 계산하는 방법 및 [평가를 수정](how-to-modify-assessment.md)하는 [방법을 알아봅니다](concepts-assessment-calculation.md).
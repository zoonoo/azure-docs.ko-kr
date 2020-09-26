---
title: 2023 년 3 월 1 일에 Azure Vm (클래식)을 사용 중지 하 고 있습니다.
description: 이 문서에서는 클래식 배포 모델을 사용 하 여 만든 Vm의 사용 중지에 대 한 개략적인 개요를 제공 합니다.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: c9277f510e71e4d6ecb6595aa2d67d16c2ac5695
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326031"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>2023 년 3 월 1 일 Azure Resource Manager IaaS 리소스 마이그레이션 

2014에서는 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)의 IaaS (infrastructure as a service)를 시작 했습니다. 이후 기능이 향상 되었습니다. Azure Resource Manager 이제 전체 IaaS 기능 및 기타 고급 기능을 제공 하기 때문에 2020 년 2 월 28 일에 [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM)를 통해 iaas vm (가상 머신)을 관리 하지 않습니다. 이 기능은 2023 년 3 월 1 일부 터 완전히 사용 중지 됩니다. 

현재 IaaS Vm의 약 90%가 Azure Resource Manager를 사용 하 고 있습니다. ASM을 통해 IaaS 리소스를 사용 하는 경우 지금 마이그레이션 계획을 시작 합니다. [Azure Resource Manager](../azure-resource-manager/management/index.yml)를 활용 하려면 2023 년 3 월 1 일부 터이를 완료 합니다.

클래식 배포 모델을 사용 하 여 만든 Vm은 [최신 수명 주기 정책](https://support.microsoft.com/help/30881/modern-lifecycle-policy) 에 따라 만료 됩니다.

## <a name="how-does-this-affect-me"></a>이 변경 사항은 어떤 영향을 미치나요? 

- 2020 년 2 월 28 일을 기준으로, 2020 년 2 월에 ASM을 통해 IaaS Vm을 사용 하지 않은 고객은 더 이상 Vm (클래식)을 만들 수 없습니다. 
- 2023 년 3 월 1 일부 터 고객은 더 이상 ASM을 사용 하 여 IaaS Vm을 시작할 수 없습니다. 아직 실행 중이거나 할당 된는 모두 중지 되 고 할당이 취소 됩니다. 
- 2023 년 3 월 1 일부 터 Azure Resource Manager로 마이그레이션되지 않은 구독은 나머지 Vm (클래식)을 삭제 하기 위한 타임 라인에 대 한 정보가 표시 됩니다.  

이 사용 중지 *는 다음과* 같은 Azure 서비스 및 기능에 영향을 주지 않습니다. 
- Azure Cloud Services 
- Vm에서 사용 *하지 않는* 저장소 계정 (클래식) 
- Vm에서 사용 *하지 않는* 가상 네트워크 (클래식) 
- 기타 클래식 리소스

## <a name="what-actions-should-i-take"></a>어떤 작업을 수행 해야 하나요? 

현재 Azure Resource Manager로의 마이그레이션 계획을 시작 합니다. 

1. 영향을 받는 모든 Vm 목록을 만듭니다. 

   - [Azure Portal의 vm 창](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) 에 있는 **가상 머신 (클래식)** 유형의 vm은 구독 내에서 영향을 받는 모든 vm입니다. 
   - [포털](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) 또는 [PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) 을 사용 하 여 Azure 리소스 그래프를 쿼리하여 선택한 구독에 대해 플래그가 지정 된 모든 vm (클래식) 및 관련 정보 목록을 볼 수도 있습니다. 
   - 2020 년 2 월 8 일, 년 9 월 2 일부 터 이러한 Vm (클래식)을 포함 하는 모든 구독 목록이 포함 된 전자 메일을 구독 소유자에 게 보냅니다. 이 목록을 작성 하려면 사용 하세요. 

1. [Linux](./linux/migration-classic-resource-manager-plan.md) 및 [Windows](./windows/migration-classic-resource-manager-plan.md) vm (클래식)을 Azure Resource Manager으로 마이그레이션하는 방법에 [대해 자세히 알아보세요](./windows/migration-classic-resource-manager-overview.md) . 자세한 내용은 [클래식에서 Azure Resource Manager로 마이그레이션에 대 한 질문과 대답](./migration-classic-resource-manager-faq.md)을 참조 하세요.

1. [플랫폼 지원 마이그레이션 도구](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) 를 사용 하 여 기존 vm을 세 단계로 마이그레이션하는 것이 좋습니다 (유효성 검사, 준비, 커밋). 이 도구는 최소 가동 중지 시간 없이 Vm을 마이그레이션하도록 설계 되었습니다. 

   1. 첫 번째 단계인 validate는 기존 배포에 영향을 주지 않으며 마이그레이션에 대해 지원 되지 않는 모든 시나리오 목록을 제공 합니다. 
   1. [해결 방법 목록을](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview#unsupported-features-and-configurations) 통해 배포를 수정 하 고 마이그레이션 준비를 할 수 있습니다. 
   1. 이상적으로 모든 유효성 검사 오류를 수정한 후 준비 및 커밋 단계 중에 문제가 발생 하지 않도록 해야 합니다. 커밋이 성공적으로 완료 되 면 배포는 Azure Resource Manager로 실시간 마이그레이션된 후 Azure Resource Manager에서 노출 하는 새 Api를 통해 관리할 수 있습니다. 

   마이그레이션 도구가 마이그레이션에 적합 하지 않은 경우 마이그레이션에 대 한 [다른 계산 제품](https://docs.microsoft.com/azure/architecture/guide/technology-choices/compute-decision-tree) 을 탐색할 수 있습니다. 많은 Azure 계산 제품이 있으므로 서로 다르기 때문에 플랫폼 지원 마이그레이션 경로를 제공할 수 없습니다.  

1. 지원 목록에 구독을 추가 하는 방법에 대 한 기술 관련 질문, 문제 및 도움말을 보려면 [지원 담당자에 게 문의 하세요](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

1. 가능한 한 빨리 마이그레이션을 완료 하 여 비즈니스 영향을 방지 하 고 Azure Resource Manager의 향상 된 성능, 보안 및 새로운 기능을 활용 하세요. 

## <a name="what-resources-are-available-for-this-migration"></a>이 마이그레이션에 사용할 수 있는 리소스는 무엇 인가요?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html): microsoft 및 커뮤니티에서 마이그레이션을 지원 합니다.

- [Azure 마이그레이션 지원](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): 마이그레이션 중에 기술 지원을 위한 전용 지원 팀입니다.

- [Microsoft Fast track](https://www.microsoft.com/fasttrack): fast track은이 마이그레이션의 & 실행 계획을 수립할 수 있는 적격 고객을 지원할 수 있습니다. [직접 추천](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0).  

- 회사/조직이 Microsoft와 제휴 하거나 Microsoft 담당자 (예: CSAs (cloud solution 설계자) 또는 TAMs (기술 계정 관리자))와 함께 작동 하는 경우 마이그레이션에 대 한 추가 리소스에 대해 작업 해 보세요. 


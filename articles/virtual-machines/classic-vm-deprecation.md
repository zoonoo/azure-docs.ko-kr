---
title: 2023년 3월 1일에 Azure 클래식 VM을 폐기합니다.
description: 아티클은 클래식 VM 사용 중지에 대한 개요를 제공합니다.
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 764567bffd2a08ebb5beb17e3063998848b3f110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127342"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>2023년 3월 1일까지 IaaS 리소스를 Azure 리소스 관리자로 마이그레이션합니다. 

2014년에는 Azure 리소스 관리자에서 IaaS를 출시했으며 그 이후로 기능을 향상시켜 왔습니다. [Azure 리소스 관리자에는](https://azure.microsoft.com/features/resource-manager/) 이제 전체 IaaS 기능 및 기타 발전 이 있으므로 2020년 2월 28일에 Azure 서비스 관리자를 통해 IaaS VM 관리를 더 이상 사용할 수 없으며 이 기능은 2023년 3월 1일에 완전히 사용 중지됩니다. 

현재 IaaS VM의 약 90%가 Azure 리소스 관리자를 사용하고 있습니다. ASM(Azure Service Manager)을 통해 IaaS 리소스를 사용하는 경우 지금 마이그레이션 계획을 시작하고 2023년 3월 1일까지 완료하여 [Azure 리소스 관리자를](https://docs.microsoft.com/azure/azure-resource-manager/management/)활용합니다.

클래식 VM은 은퇴를 위한 [최신 수명 주기 정책을](https://support.microsoft.com/help/30881/modern-lifecycle-policy) 따르게 됩니다.

## <a name="how-does-this-affect-me"></a>이 변경 사항은 어떤 영향을 미치나요? 

1) 2020년 2월 28일부터 2020년 2월에 ASM(Azure Service Manager)을 통해 IaaS VM을 사용하지 않은 고객은 더 이상 클래식 VM을 만들 수 없습니다. 
2) 2023년 3월 1일에 고객은 더 이상 Azure 서비스 관리자를 사용하여 IaaS VM을 시작할 수 없으며, 아직 실행 중이거나 할당된 모든 VM은 중지되고 할당됩니다. 
2) 2023년 3월 1일에 Azure 리소스 관리자로 마이그레이션하지 않은 구독은 나머지 클래식 VM을 삭제하는 일정에 대해 알림을 받게 됩니다.  

다음 Azure 서비스 및 기능은 이 사용 중지의 영향을 **받지 않습니다.** 
- Cloud Services 
- 클래식 VM에서 사용하지 **않는** 저장소 계정 
- 가상 네트워크(VNet)는 클래식 VM에서 사용하지 **않습니다.** 
- 기타 클래식 리소스

## <a name="what-actions-should-i-take"></a>어떤 조치를 취해야 하나요? 

- 지금 Azure 리소스 관리자로 마이그레이션계획을 세우십시오. 

- 클래식 [Linux](./linux/migration-classic-resource-manager-plan.md) 및 [Windows](./windows/migration-classic-resource-manager-plan.md) VM을 Azure 리소스 관리자로 마이그레이션하는 방법에 대해 [자세히 알아봅니다.](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)

- 자세한 내용은 [Azure 리소스 관리자 마이그레이션에 대한 자주 묻는 질문을 참조하십시오.](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- 기술적인 질문 및 문제에 대한 문의 지원 팀에 [문의하십시오.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- FAQ 및 피드백의 일부가 아닌 다른 질문은 아래의 의견에 따라 다하십시오.

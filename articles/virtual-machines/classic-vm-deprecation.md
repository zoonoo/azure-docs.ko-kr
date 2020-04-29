---
title: 2023 년 3 월 1 일에 Azure 클래식 Vm을 사용 중지 하 고 있습니다.
description: 문서는 클래식 VM 사용 중지에 대 한 개략적인 개요를 제공 합니다.
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: e56aa5ec073aadc2a16d53c266d33255a34077cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668800"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>2023 년 3 월 1 일 Azure Resource Manager IaaS 리소스 마이그레이션 

2014에서는 Azure Resource Manager에서 IaaS를 실행 했으며 이후 기능을 개선 했습니다. [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 이제 전체 iaas 기능 및 기타 고급 기능을 제공 하기 때문에 2020 년 2 월 28 일에 Azure Service Manager를 통해 iaas vm의 관리를 사용 하지 않으며,이 기능은 2023 년 3 월 1 일에 완전히 사용 중지 됩니다. 

현재 IaaS Vm의 약 90%가 Azure Resource Manager를 사용 하 고 있습니다. ASM (Azure Service Manager)을 통해 IaaS 리소스를 사용 하는 경우 지금 마이그레이션 계획을 시작 하 고 2023 년 3 월 1 일까 지 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/)를 활용 하 여 완료 합니다.

클래식 Vm은 [최신 수명 주기 정책](https://support.microsoft.com/help/30881/modern-lifecycle-policy) 에 따라 만료 됩니다.

## <a name="how-does-this-affect-me"></a>이 변경 사항은 어떤 영향을 미치나요? 

1) 2020 년 2 월 28 일부 터 Azure Service Manager (ASM 2020)를 통해 IaaS Vm을 사용 하지 않는 고객은 더 이상 클래식 Vm을 만들 수 없습니다. 
2) 2023 년 3 월 1 일부 터 고객은 더 이상 Azure Service Manager를 사용 하 여 IaaS Vm을 시작할 수 없으며 계속 실행 중이거나 할당 된이 중지 되 고 할당이 취소 됩니다. 
2) 2023 년 3 월 1 일부 터 Azure Resource Manager로 마이그레이션되지 않은 구독은 남은 클래식 Vm을 삭제 하는 일정에 대 한 알림을 받습니다.  

다음 Azure 서비스 및 기능은이 사용 중지에 영향을 **받지 않습니다** . 
- Cloud Services 
- 클래식 Vm에서 사용 **하지 않는** 저장소 계정 
- Vnet (가상 네트워크)는 클래식 Vm에서 사용 **되지 않습니다** . 
- 기타 클래식 리소스

## <a name="what-actions-should-i-take"></a>어떤 작업을 수행 해야 하나요? 

- 현재 Azure Resource Manager로의 마이그레이션 계획을 시작 합니다. 

- 클래식 [Linux](./linux/migration-classic-resource-manager-plan.md) 및 [Windows](./windows/migration-classic-resource-manager-plan.md) vm을 Azure Resource Manager로 마이그레이션하는 방법에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) .

- 자세한 내용은 [클래식에서 Azure Resource Manager로 마이그레이션에 대 한 질문과 대답](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq) 을 참조 하세요.

- 기술 관련 질문, 문제 및 구독 허용 목록 [지원 담당자에 게 문의 하세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- FAQ 및 피드백의 일부가 아닌 다른 질문은 아래에 설명 되어 있습니다.

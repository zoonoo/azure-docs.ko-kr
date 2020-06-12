---
title: Azure 기밀 컴퓨팅 FAQ
description: Azure 기밀 컴퓨팅에 대해 자주 묻는 질문에 대한 답변입니다.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: bb821d00a168e3b8f0636b93696376dc8b5d492e
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772901"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Azure 기밀 컴퓨팅 FAQ

이 문서에서는 [Azure에서 기밀 컴퓨팅 워크로드 실행](overview.md)에 대한 가장 일반적인 질문 몇 가지에 대한 답변을 제공합니다.

Azure 문제와 관련된 정보가 이 문서에 없을 경우 [MSDN 및 Stack Overflow에서 Azure 포럼](https://azure.microsoft.com/support/forums/)을 방문합니다. 이러한 포럼이나 [@AzureSupport Twitter](https://twitter.com/AzureSupport) 의 에 문제를 게시할 수 있습니다. Azure 지원 요청을 제출할 수도 있습니다. 지원 요청을 제출하려면 [Azure 지원 페이지](https://azure.microsoft.com/support/options/)에서 지원 받기를 선택합니다.

## <a name="confidential-computing-virtual-machines"></a>기밀 컴퓨팅 가상 머신<a id="vm-faq"></a>

**Azure에서 DCsv2 시리즈 VM을 배포하려면 어떻게 하나요?**

다음은 DCsv2 VM을 배포할 수 있는 몇 가지 방법입니다.
   - [Azure Resource Manager](../virtual-machines/windows/template-description.md) 템플릿 사용
   - [Azure Portal](https://portal.azure.com/#create/hub)에서
   - [Azure 기밀 컴퓨팅(가상 머신)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) 마켓플레이스 솔루션 템플릿. 마켓플레이스 솔루션 템플릿은 고객을 지원되는 시나리오(지역, 이미지, 가용성, 디스크 암호화)로 제한하는 데 도움이 됩니다. 

**모든 OS 이미지가 Azure 기밀 컴퓨팅에서 작동하나요?**

아니요. 가상 머신은 Ubuntu Server 18.04, Ubuntu Server 16.04, Windows Server 2019 Datacenter 및 Windows Server 2016 Datacenter를 사용하는 2세대 운영 머신에만 배포할 수 있습니다. [Linux](../virtual-machines/linux/generation-2.md) 및 [Windows](../virtual-machines/windows/generation-2.md)에서의 Gen 2 VM에 대해 자세히 알아보세요.

**DCsv2 가상 머신이 포털에서 회색으로 표시되고 선택할 수 없습니다.**

VM 옆의 정보 버블에 따라 다음과 같이 조치를 취해야 합니다.
   -    **UnsupportedGeneration**: 가상 머신 이미지의 세대를 “Gen2”로 변경합니다.
   -    **NotAvailableForSubscription**: 해당 지역에서 아직 구독할 수 없습니다. 사용 가능한 지역을 선택합니다.
   -    **InsufficientQuota**: [할당량을 늘리는 지원 요청을 만듭니다](../azure-portal/supportability/per-vm-quota-requests.md). 무료 평가판 구독에는 기밀 컴퓨팅 VM에 대한 할당량이 없습니다. 

**포털 크기 선택기에서 검색하면 DCsv2 가상 머신이 표시되지 않습니다.**

[사용 가능한 지역](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)을 선택했는지 확인합니다. 또한 크기 선택기에서 "모든 필터 지우기"를 선택해야 합니다. 

**Azure Resource Manager 템플릿 배포 실패 오류 "승인된 표준 DcsV2 제품군 코어 할당을 초과하므로 작업을 완료할 수 없습니다."가 발생합니다.**

[할당량을 늘리는 지원 요청을 만듭니다](../azure-portal/supportability/per-vm-quota-requests.md). 무료 평가판 구독에는 기밀 컴퓨팅 VM에 대한 할당량이 없습니다. 

**DCsv2 시리즈 VM과 DC 시리즈 VM 간의 차이점은 무엇인가요?**

DC 시리즈 VM은 Intel SGX를 사용하는 이전 6코어 Intel 프로세서에서 실행되고, 총 메모리가 더 작고 EPC(Enclave 페이지 캐시) 메모리가 더 작고, 두 지역에서만 사용할 수 있습니다(미국 동부 및 서유럽에서 Standard_DC2s 및 Standard_DC4s). 이러한 VM을 일반 공급할 계획은 없으며 프로덕션 환경에서는 사용하지 않는 것이 좋습니다. 이러한 VM을 배포하려면 [기밀 컴퓨팅 DC 시리즈 VM(미리 보기)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Marketplace 인스턴스를 사용합니다.

**DCsv2 가상 머신을 전 세계적으로 사용할 수 있나요?**

아니요. 이번에는 일부 지역에서만 이러한 가상 머신을 사용할 수 있습니다. 사용 가능한 지역에 대한 최신 정보는 [지역별 제품 페이지](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)를 확인하세요. 

**DCsv2 가상 머신에 Open Enclave SDK를 설치하려면 어떻게 해야 하나요?**
   
Azure 또는 온-프레미스 머신에 OE SDK를 설치하는 방법에 대한 지침은 [Open Enclave SDK GitHub](https://github.com/openenclave/openenclave)의 지침을 따르세요.
     
Open Enclave SDK GitHub에서 OS별 설치 지침도 자세히 알아볼 수 있습니다.
   - [Windows에 OE SDK 설치](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [Ubuntu 18.04에 OE SDK 설치](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
   - [Ubuntu 16.04에 OE SDK 설치](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)

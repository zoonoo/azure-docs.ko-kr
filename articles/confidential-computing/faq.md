---
title: Azure 기밀 컴퓨팅 FAQ
description: Azure 기밀 컴퓨팅에 대 한 자주 묻는 질문에 대 한 답변입니다.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: 2bd9430fc6f48d72faa2c1850af0bb8432a7c5f5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149503"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Azure 기밀 컴퓨팅에 대 한 질문과 대답

이 문서에서는 [Azure에서 기밀 컴퓨팅 워크 로드](overview.md)실행에 대해 가장 일반적인 질문 중 일부에 대 한 답변을 제공 합니다.

Azure 문제와 관련된 정보가 이 문서에 없을 경우 [MSDN 및 Stack Overflow에서 Azure 포럼](https://azure.microsoft.com/support/forums/)을 방문합니다. 이러한 포럼에 문제를 게시 하거나 [ @AzureSupport Twitter의](https://twitter.com/AzureSupport)에 게시할 수 있습니다. Azure 지원 요청을 제출할 수도 있습니다. 지원 요청을 제출 하려면 [Azure 지원 페이지](https://azure.microsoft.com/support/options/)에서 지원 받기를 선택 합니다.

## <a name="confidential-computing-virtual-machines"></a>기밀 컴퓨팅 Virtual Machines<a id="vm-faq"></a>

1. **DCsv2 시리즈 Vm 배포를 시작 하려면 어떻게 해야 하나요?**

   DCsv2 VM을 배포할 수 있는 몇 가지 방법은 다음과 같습니다.
   - [Azure Resource Manager 템플릿](../virtual-machines/windows/template-description.md) 사용
   - [Azure Portal](https://portal.azure.com/#create/hub) 에서
   - [Azure 기밀 계산 (가상 머신)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) marketplace 솔루션 템플릿 Marketplace 솔루션 템플릿은 고객이 지원 되는 시나리오 (지역, 이미지, 가용성, 디스크 암호화)를 제한 하는 데 도움이 됩니다. 

1. **모든 OS 이미지가 Azure 기밀 컴퓨팅에서 작동 하나요?**

   아니요. 가상 머신은 2 세대 가상 컴퓨터에만 배포할 수 있습니다. Ubuntu Server 18.04, Ubuntu Server 16.04 및 Windows Server 2016 데이터 센터에 대 한 2 세대 지원이 제공 됩니다. [Linux](../virtual-machines/linux/generation-2.md) 및 [Windows](../virtual-machines/windows/generation-2.md) 의 2 세대 vm에 대해 자세히 알아보세요.

1. **DCsv2 가상 컴퓨터가 포털에서 회색으로 표시 되 고 하나를 선택할 수 없습니다.**

   VM 옆의 정보 버블에 따라 다음과 같은 작업을 수행 해야 합니다.
    -   **Unsupportedgeneration**: 가상 머신 이미지의 생성을 "Gen2"로 변경 합니다.
    -   **Notavailable forsubscription** : 해당 지역은 아직 구독에 사용할 수 없습니다. 사용 가능한 지역을 선택합니다.
    -   **InsufficientQuota**: [할당량을 늘리기 위한 지원 요청을 만듭니다](../azure-portal/supportability/per-vm-quota-requests.md). 무료 평가판 구독에는 기밀 컴퓨팅 Vm에 대 한 할당량이 없습니다. 

1. **DCsv2 virtual machines는 포털 크기 선택기에서 검색 하려고 할 때 표시 되지 않습니다.**

   사용 가능한 지역을 선택 했는지 확인 합니다. 또한 크기 선택기에서 "모든 필터 지우기"를 선택 해야 합니다. 

1. **DCsv2 시리즈와 DC 시리즈 Vm 간의 차이점은 무엇 인가요?**

   DC 시리즈 Vm은 SGX를 사용 하는 이전 6 코어 Intel 프로세서에서 실행 됩니다. 여기에는 총 메모리, EPC (Enclave Page) 메모리 및 더 작은 영역에서 사용할 수 있는 메모리가 있습니다. 이러한 Vm은 미국 동부 에서만 사용할 수 있으며 유럽 서 부는 Standard_DC2s 및 Standard_DC4s의 두 가지 크기로 제공 됩니다. GA로 전환 되지 않으며 [기밀 계산 DC 시리즈 VM [Preview]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Marketplace 인스턴스에만 배포할 수 있습니다.

1. **DCsv2 가상 컴퓨터를 전역적으로 사용할 수 있나요?**

   아니요. 이러한 가상 머신은 select 지역 에서만 사용할 수 있습니다. 사용 가능한 최신 지역에 대해서는 [영역별 제품 페이지](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 를 확인 하세요. 

1. **Open Enclave SDK를 설치 어떻게 할까요? 시겠습니까?**
   
   Azure 또는 온-프레미스에서 컴퓨터에 OE SDK를 설치 하는 방법에 대 한 지침은 [Open ENCLAVE SDK GitHub](https://github.com/openenclave/openenclave)의 지침을 따르세요.
     
   OS 관련 설치 지침은 Open Enclave SDK GitHub로 이동할 수도 있습니다.
     - [Windows에 OE SDK 설치](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [Ubuntu 18.04에 OE SDK 설치](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [Ubuntu 16.04에 OE SDK 설치](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)

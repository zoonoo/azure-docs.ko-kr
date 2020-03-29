---
title: Azure DevTest 랩을 사용하기 위한 인기 있는 시나리오
description: 이 문서에서는 Azure DevTest Labs를 사용하기 위한 기본 시나리오와 조직에서 서비스를 사용하기 시작하는 두 가지 일반 경로를 제공합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60773811"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Azure DevTest 랩을 사용하기 위한 인기 있는 시나리오
기업의 요구에 따라 DevTest 랩은 다양한 요구 사항을 충족하도록 구성할 수 있습니다.  이 문서에서는 인기 있는 시나리오에 대해 설명합니다. 각 시나리오에서는 DevTest Labs 및 리소스를 사용하여 이러한 시나리오를 구현하는 데 사용할 수 있는 이점을 다룹니다.  

- 개발자 데스크톱
- 테스트 환경
- 교육 세션, 실습 랩 및 해커톤
- 샌드박스 조사
- 클래스룸 랩

## <a name="developer-desktops"></a>개발자 데스크톱
개발자가 각 프로젝트에 사용하는 개발 컴퓨터의 요구 사항은 서로 다른 경우가 많습니다. 개발자는 DevTest Labs를 통해 가장 일반적인 시나리오에 맞게 구성된 온디맨드 가상 시스템에 액세스할 수 있습니다. DevTest Labs에서는 다음과 같은 이점을 제공합니다.

- 조직은 팀 간에 일관성을 보장하는 공통 개발 기계를 제공할 수 있습니다.
- 개발자는 필요에 따라 개발 컴퓨터를 신속하게 프로비저닝하거나 [기존 미리 구성된 컴퓨터를 청구할](devtest-lab-add-claimable-vm.md)수 있습니다.
- 개발자는 구독 수준 사용 권한 없이 셀프 서비스 방식으로 리소스를 프로비전할 수 있습니다.
- IT 또는 관리자는 [네트워킹 토폴로지의 사전 정의를](devtest-lab-configure-vnet.md) 할 수 있으며 개발자는 특별한 액세스 없이 간단하고 직관적인 방식으로 직접 사용할 수 있습니다.
- 개발자는 필요에 따라 개발 컴퓨터를 쉽게 [사용자 지정할](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) 수 있습니다.
- 관리자는 다음을 확인하여 비용을 제어할 수 있습니다.
    - 개발자는 개발에 필요한 것보다 [더 많은 VM을 얻을 수 없습니다.](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user)
    - 사용하지 않을 때 [VM이 종료됩니다.](devtest-lab-set-lab-policy.md#set-auto-shutdown)
    - 특정 [랩에 대한 VM 인스턴스 크기의 하위 집합만 허용](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)
    - 각 랩의 [비용 목표 및 알림 관리](devtest-lab-configure-cost-management.md)

자세한 내용은 [개발자를 위한 Azure DevTest 랩 사용을](devtest-lab-developer-lab.md)참조하십시오. 

## <a name="test-environments"></a>테스트 환경
기업 전반에서 테스트 환경을 만들고 관리하려면 많은 작업을 수행해야 합니다. DevTest Labs를 사용하면 테스트 환경을 쉽게 만들거나 업데이트하거나 복제할 수 있습니다. 이를 통해 팀은 필요할 때 완전히 구성된 환경에 액세스할 수 있습니다. 이 시나리오에서 DevTest Labs는 다음과 같은 이점을 제공합니다.

- 조직은 팀 간에 일관성을 보장하는 공통 테스트 환경을 제공할 수 있습니다.
- 테스터는 재사용 가능한 템플릿을 사용하여 Windows 및 Linux 환경을 신속하게 프로비저닝하여 최신 버전의 응용 프로그램을 테스트할 수 있습니다.
- 관리자는 랩을 Azure DevOps에 연결하여 DevOps 시나리오를 활성화할 수 있습니다.
- 랩 소유자는 다음을 통해 비용을 제어할 수 있습니다.
    - 사용하지 않을 때 [환경의 VM이 종료됩니다.](devtest-lab-set-lab-policy.md#set-auto-shutdown)
    - 특정 [랩에 대한 VM 인스턴스 크기의 하위 집합만 허용](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)
    - 각 랩의 [비용 목표 및 알림 관리](devtest-lab-configure-cost-management.md)

자세한 내용은 [VM 및 PaaS 테스트 환경에 Azure DevTest 랩 사용을](devtest-lab-test-env.md)참조하십시오.

## <a name="sandboxed-investigations"></a>샌드박스 조사
개발자는 종종 다른 기술이나 인프라 디자인을 조사합니다. 기본적으로 DevTest Labs로 만든 모든 환경은 자체 리소스 그룹에서 만들어집니다. DevTest Labs 사용자는 해당 리소스에 대한 읽기 액세스 권한만 가져옵니다. 그러나 더 많은 제어가 필요한 개발자의 경우 랩 전체 설정을 업데이트하여 사용자가 만든 모든 환경에 대해 기본 DevTest Labs 사용자에게 [기여자에게 권한을](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) 부여할 수 있습니다.  DevTest Labs를 사용하면 개발자가 랩에서 만든 환경에 자동으로 기여자 권한을 부여할 수 있습니다.  이 시나리오를 통해 개발자는 개발 또는 테스트 환경에 필요한 Azure 리소스를 추가 및/또는 변경할 수 있습니다. [리소스별 비용](devtest-lab-configure-cost-management.md#view-cost-by-resource) 페이지를 사용하면 랩 소유자가 조사에 사용되는 각 환경의 비용을 추적할 수 있습니다.

자세한 내용은 [환경 리소스 그룹에 대한 액세스 권한 설정을](https://aka.ms/dtl-sandbox)참조하십시오.

## <a name="trainings-hands-on-labs-and-hackathons"></a>교육, 실습 랩 및 해커톤 
Azure DevTest Labs의 랩은 워크샵, 실습 랩, 교육 또는 해커톤과 같은 일시적인 활동을 위한 훌륭한 컨테이너 역할을 합니다.  이 서비스에서는 각 실습생이 동일한 격리형 학습용 환경을 만드는 데 사용할 수 있는 사용자 지정 템플릿을 제공할 수 있는 랩을 만들 수 있습니다. 이 시나리오에서 DevTest Labs는 다음과 같은 이점을 제공합니다.

- [정책은](devtest-lab-set-lab-policy.md) 교육생에게 필요한 가상 컴퓨터와 같은 리소스 수만 얻을 수 있도록 합니다.
- 미리 구성된 컴퓨터와 생성된 컴퓨터는 연수생의 단일 작업으로 [클레임됩니다.](devtest-lab-add-claimable-vm.md)
- 랩은 [랩의 URL에](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)액세스하여 연습생과 공유됩니다.
- 가상 시스템의 [만료 날짜는](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) 더 이상 필요하지 않은 후에 컴퓨터가 삭제되도록 합니다.
- 교육이 끝나면 랩 및 모든 [관련 리소스를](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) 쉽게 [삭제할](devtest-lab-delete-lab-vm.md#delete-a-lab) 수 있습니다.

자세한 내용은 [교육에 Azure DevTest 랩 사용을](devtest-lab-training-lab.md)참조하십시오.  

## <a name="proof-of-concept-vs-scaled-deployment"></a>개념 증명 과 확장 배포
DevTest Labs를 탐색하기로 결정한 후에는 개념 증명과 확장된 배포라는 두 가지 일반적인 경로가 있습니다.  

**크기가 조정된 배포**에서는 개발자 수가 매우 많은 기업 전체에 DevTest Labs를 배포하기 위해 몇 주/몇 달간 검토 및 계획을 진행합니다.

**개념 증명** 배포는 단일 팀의 집중적인 노력에 중점을 두고 조직 가치를 확립합니다. 크기가 조정된 배포가 더 쉬워 보일 수도 있지만 이 방식은 개념 증명 옵션보다 실패 빈도가 더 높습니다. 따라서 일단은 소규모로 배포를 시작하고 첫 번째 팀에서 필요한 정보를 파악한 다음 2~3개 팀을 추가해 동일한 방식을 반복한 후에 확인된 사실을 바탕으로 크기가 조정된 배포를 계획하는 것이 좋습니다. 개념을 적절하게 증명하려면 1~2개 팀을 선택하여 해당 시나리오(개발 환경과 테스트 환경용 시나리오)를 파악하고 현재 사용 사례를 문서로 작성한 다음 DevTest Labs를 배포하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 읽어보세요.

- [DevTest Lab 개념](devtest-lab-concepts.md)
- [DevTest Labs FAQ](devtest-lab-faq.md)


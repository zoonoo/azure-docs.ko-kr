---
title: Azure DevTest Labs 사용 시작
description: 이 문서에서는 Azure DevTest Labs를 사용하는 기본적인 시나리오와 조직에서 서비스 사용을 시작할 수 있는 두 가지 일반 경로를 제공합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 87baef8ddb5b5d8fc979ba5afb9f9b13cb4fc2ef
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876539"
---
# <a name="get-started-with-using-azure-devtest-labs"></a>Azure DevTest Labs 사용 시작
DevTest Labs 탐색을 시작할 때는 대개 개념 증명과 크기가 조정된 배포의 두 가지 일반 경로 중 하나를 선택합니다. 

**크기가 조정된 배포**에서는 개발자 수가 매우 많은 기업 전체에 DevTest Labs를 배포하기 위해 몇 주/몇 달간 검토 및 계획을 진행합니다. 

**개념 증명 배포**에서는 단일 팀이 조직 가치를 설정하는 작업을 집중적으로 수행합니다. 크기가 조정된 배포가 더 쉬워 보일 수도 있지만 이 방식은 개념 증명 옵션보다 실패 빈도가 더 높습니다. 따라서 일단은 소규모로 배포를 시작하고 첫 번째 팀에서 필요한 정보를 파악한 다음 2~3개 팀을 추가해 동일한 방식을 반복한 후에 확인된 사실을 바탕으로 크기가 조정된 배포를 계획하는 것이 좋습니다. 개념을 적절하게 증명하려면 1~2개 팀을 선택하여 해당 시나리오(개발 환경과 테스트 환경용 시나리오)를 파악하고 현재 사용 사례를 문서로 작성한 다음 DevTest Labs를 배포하는 것이 좋습니다. 

## <a name="scenarios"></a>시나리오
DevTest Labs 구현을 위한 기본 시나리오는 다음의 세 가지입니다. 

- 개발자 데스크톱
- 테스트 환경
- 랩/교육/Hackathon

## <a name="developer-desktops"></a>개발자 데스크톱
개발자가 각 프로젝트에 사용하는 개발 컴퓨터의 요구 사항은 서로 다른 경우가 많습니다. DevTest Labs 사용 시에는 개발자가 가장 흔히 수행하는 시나리오에 맞게 미리 구성된 주문형 VM에 액세스할 수 있습니다. DevTest Labs에서는 다음과 같은 이점을 제공합니다.

- 조직은 공통의 개발 및 테스트 환경을 제공해 여러 팀 간의 일관성을 유지할 수 있습니다.
- 개발자는 필요에 따라 개발 컴퓨터를 신속하게 프로비전할 수 있습니다.
- 개발자는 구독 수준 권한 없이도 셀프 서비스 방식으로 리소스를 프로비전할 수 있습니다.
- IT 담당자나 관리자는 네트워킹 토폴로지를 미리 정의할 수 있으며, 개발자는 특수한 액세스 권한 없이도 단순하고 직관적인 방식으로 이 토폴로지를 직접 활용할 수 있습니다.
- 개발자는 필요할 때마다 개발 컴퓨터를 쉽게 사용자 지정할 수 있습니다.
- 관리자는 다음을 확인하여 비용을 제어할 수 있습니다.
    - 개발자는 개발에 필요한 것보다 더 많은 VM을 가져올 수 없습니다.
    - VM은 사용되지 않을 때 종료됩니다.
    - 특정 워크로드에 VM 인스턴스 크기 중 일부분만 사용하도록 허용합니다.

## <a name="test-environments"></a>테스트 환경
기업 전반에서 테스트 환경을 만들고 관리하려면 많은 작업을 수행해야 합니다. DevTest Labs 사용 시에는 테스트 환경을 쉽게 생성, 업데이트 또는 복제할 수 있으므로 팀이 필요 시 완전하게 구성된 환경에 액세스할 수 있습니다. 이 시나리오에서 DevTest Labs는 다음과 같은 이점을 제공합니다.

- 테스터가 재사용 가능한 템플릿과 아티팩트를 사용하여 Windows와 Linux 환경을 빠르게 프로비전함으로써 최신 버전의 애플리케이션을 테스트할 수 있습니다.
- 테스터가 다수의 테스트 에이전트를 프로비전하여 부하 테스트를 강화할 수 있습니다.
- 관리자가 랩을 Azure DevOps에 연결하여 DevOps 시나리오를 사용하도록 설정할 수 있습니다.
- 관리자는 다음을 확인하여 비용을 제어할 수 있습니다.
    - 테스터는 필요한 것보다 더 많은 VM을 가져올 수 없습니다.
    - VM은 사용되지 않을 때 종료됩니다.
    - 특정 워크로드에 VM 인스턴스 크기 중 일부분만 사용하도록 허용합니다.

## <a name="labs-workshops-trainings-and-hackathons"></a>랩, 워크샵, 교육 및 Hackathon  
Azure DevTest Labs의 랩은 워크샵, 실습 랩, 교육, Hackathon 등의 일시적인 활동에 적합한 컨테이너로 사용할 수 있습니다. 이 서비스에서는 각 실습생이 동일한 격리형 학습용 환경을 만드는 데 사용할 수 있는 사용자 지정 템플릿을 제공할 수 있는 랩을 만들 수 있습니다. 각 실습생이 필요할 때에만 교육 환경을 이용할 수 있도록 하고 교육에 필요한 리소스(예: 가상 머신)를 충분히 포함하는 정책을 적용할 수 있습니다. 마지막으로 실습생과 랩을 쉽게 공유할 수 있고 실습생은 한 번의 클릭으로 액세스할 수 있습니다. 수업이 끝나면 랩과 모든 관련 리소스를 쉽게 삭제할 수 있습니다.


## <a name="next-steps"></a>다음 단계
이 시리즈의 다음 문서를 참조하세요. [DevTest Labs 배포 확대](devtest-lab-guidance-scale.md)

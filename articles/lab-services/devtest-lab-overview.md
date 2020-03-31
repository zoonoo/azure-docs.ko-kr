---
title: Azure DevTest Lab 정보 | Microsoft Docs
description: DevTest Lab에서 Azure 가상 머신을 쉽게 만들고 관리하고 모니터링할 수 있는 방법 알아보기
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561458"
---
# <a name="about-azure-devtest-labs"></a>Azure DevTest Labs 정보
Azure DevTest Labs를 사용하면 팀의 개발자가 승인을 기다리지 않고도 VM(가상 컴퓨터) 및 PaaS 리소스를 효율적으로 자체 관리할 수 있습니다.

DevTest Labs는 미리 구성된 베이스 또는 Azure 리소스 관리자 템플릿으로 구성된 랩을 만듭니다. 이러한 도구는 환경을 만드는 데 사용할 수 있는 모든 필요한 도구와 소프트웨어를 가지고 있습니다. 몇 시간 또는 며칠이 아닌 몇 분 안에 환경을 만들 수 있습니다.

DevTest Labs를 사용하여 다음 작업을 수행하여 응용 프로그램의 최신 버전을 테스트할 수 있습니다.

- 재사용 가능한 템플릿 및 아티팩트를 사용하여 Windows 및 Linux 환경을 신속하게 프로비전합니다.
- 배포 파이프라인과 DevTest Lab을 쉽게 통합하여 주문형 환경으로 프로비전할 수 있습니다.
- 여러 테스트 에이전트를 프로비전하여 부하 테스트를 확장하고 교육 및 데모를 위한 사전 프로비저닝 환경을 만듭니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>기능
DevTest Labs는 VM을 사용하여 작업하는 개발자에게 다음과 같은 기능을 제공합니다.

- 간단한 5단계 미만의 단계를 수행하여 VM을 빠르게 만듭니다.
- 팀 리더 또는 중앙 IT에서 구성, 승인 및 승인한 선별된 VM 베이스 목록에서 선택합니다.
- 모든 소프트웨어와 도구가 이미 설치된 미리 생성된 사용자 지정 이미지에서 VM을 만듭니다. 
- VM을 만들 때 설치된 소프트웨어의 최신 빌드와 결합된 사용자 지정 이미지인 수식에서 VM을 만듭니다. 
- 프로비저닝된 후 VM에 배포된 확장 아티팩트를 설치합니다.
- VM에서 자동 종료 및 자동 시작 일정을 설정합니다.
- 생성 프로세스를 거치지 않고 미리 만들어진 VM을 클레임합니다.

DevTest Labs는 PaaS 환경에서 작업하는 개발자에게 다음과 같은 기능을 제공합니다.

- 리소스 관리자를 사용하여 세 단계 미만의 간단한 단계를 수행하여 PaaS 환경을 빠르게 만들 수 있습니다.
- 팀 리더 또는 중앙 IT 에서 구성되고 승인된 리소스 관리자 템플릿의 선별된 목록에서 선택합니다.
- 리소스 관리자 템플릿을 사용하여 랩의 컨텍스트 내에서 Azure를 탐색하여 빈 리소스 그룹(샌드박스)을 스핀업합니다.

또한 DevTest Labs를 사용하면 중앙 IT 가 폐기물을 제어하고, 리소스 비용을 최적화하며, 다음 작업을 수행하여 예산 을 유지할 수 있습니다.  

- VM에서 자동 종료 및 자동 시작 일정 설정
- 사용자가 만들 수 있는 VM 수에 대한 정책을 설정합니다.
- 사용자가 선택하는 VM 크기 및 갤러리 이미지에 대한 정책을 설정합니다.
- 비용을 추적하고 실험실에서 목표를 설정합니다.
- 필요한 조치를 취할 수 있도록 랩에 대한 높은 예상 비용에 대한 알림을 받습니다.

DevTest Labs는 클라우드에서 환경을 생성, 구성 및 관리하는 데 다음과 같은 이점을 제공합니다.

## <a name="cost-control-and-governance"></a>비용 관리 및 거버넌스
DevTest Labs를 사용하면 다음 작업을 수행할 수 있으므로 비용을 보다 쉽게 제어할 수 있습니다.

- 사용자당 또는 랩당 VM 수와 같은 [랩에 정책을 설정합니다.](devtest-lab-get-started-with-lab-policies.md) 
- [VM을 자동으로 종료하고](devtest-lab-set-lab-policy.md) 시작하는 정책을 만듭니다.
- VM 및 PaaS 리소스에 대한 비용을 추적하여 랩 내부에서 분사하여 예산 을 [유지합니다.](devtest-lab-configure-cost-management.md)
- 랩 외부에서 리소스를 회전하지 않도록 랩의 컨텍스트 에 머무르십시오.

## <a name="quickly-get-to-ready-to-test"></a>빠른 테스트 준비
DevTest Labs를 사용하면 팀이 응용 프로그램을 개발하고 테스트하는 데 필요한 모든 것을 갖춘 사전 프로비저닝된 환경을 만들 수 있습니다. 응용 프로그램의 마지막 좋은 [빌드가](devtest-lab-add-claimable-vm.md) 설치된 환경을 주장하고 작업을 시작합니다. 또는 컨테이너를 사용하여 더 빠르고 간결한 환경을 만들 수 있습니다.

## <a name="create-once-use-everywhere"></a>한 번 만들어서 어디에나 사용
팀 또는 조직 내에서 PaaS [환경 템플릿](devtest-lab-create-environment-from-arm.md) 및 [아티팩트를](add-artifact-repository.md) 캡처하고 공유하여 소스 제어에서 모두 개발자 및 테스트 환경을 쉽게 만들 수 있습니다.

## <a name="worry-free-self-service"></a>걱정할 필요가 없는 셀프 서비스
DevTest Labs를 사용하면 개발자와 테스터가 미리 구성된 리소스 집합을 사용하여 [IaaS VM](devtest-lab-add-vm.md) 및 [PaaS 리소스를](devtest-lab-create-environment-from-arm.md) 빠르고 쉽게 만들 수 있습니다.

## <a name="use-iaas-and-paas-resources"></a>IaaS 및 PaaS 리소스 사용 
개발자는 리소스 관리자 템플릿을 사용하여 Azure 서비스 패브릭 클러스터, Azure 앱 서비스의 웹 앱 기능 및 SharePoint 팜과 같은 PaaS 리소스를 스핀업할 수도 있습니다. 랩에서 PaaS를 시작하려면 [공용 환경 리포지토리의](devtest-lab-configure-use-public-environments.md) 템플릿을 사용하거나 [랩을 자신의 Git 리포지토리에 연결합니다.](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories) 또한 이러한 리소스에 대한 비용을 추적하여 예산 내에서 유지할 수도 있습니다.

## <a name="integrate-with-your-existing-toolchain"></a>기존 툴체인과 통합
미리 만들어진 플러그인 또는 API를 사용하여 선호하는 [연속 통합(CI) 도구,](devtest-lab-integrate-ci-cd-vsts.md)통합 개발 환경(IDE) 또는 자동화된 릴리스 파이프라인에서 직접 개발/테스트 환경을 프로비저닝합니다. 포괄적인 명령줄 도구를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- DevTest 랩에 대한 자세한 내용은 [DevTest 랩 개념을](devtest-lab-concepts.md)참조하십시오.
- 단계별 지침이 포함된 연습은 [자습서: Azure DevTest Labs를 사용하여 랩 설정](tutorial-create-custom-lab.md)을 참조하십시오.
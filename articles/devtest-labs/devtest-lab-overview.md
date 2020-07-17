---
title: Azure DevTest Lab 정보 | Microsoft Docs
description: DevTest Lab에서 Azure 가상 머신을 쉽게 만들고 관리하고 모니터링할 수 있는 방법 알아보기
ms.topic: article
ms.date: 06/20/2020
ms.openlocfilehash: b7e6493e5fb1c99dfa7962ed464dca3ebc6b0c7e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85480204"
---
# <a name="about-azure-devtest-labs"></a>Azure DevTest Labs 정보
Azure DevTest Labs를 통해 팀의 개발자는 승인을 기다리지 않고 Vm (가상 머신) 및 PaaS 리소스를 효율적으로 자체 관리할 수 있습니다.

DevTest Labs는 미리 구성 된 기본 또는 Azure Resource Manager 템플릿으로 구성 된 랩을 만듭니다. 여기에는 환경을 만드는 데 사용할 수 있는 필요한 모든 도구와 소프트웨어가 있습니다. 몇 분 내에 시간 또는 일이 아닌 환경을 만들 수 있습니다.

DevTest Labs를 사용 하 여 다음 작업을 수행 하 여 최신 버전의 응용 프로그램을 테스트할 수 있습니다.

- 재사용 가능한 템플릿 및 아티팩트를 사용 하 여 Windows 및 Linux 환경을 신속 하 게 프로 비전 합니다.
- 배포 파이프라인과 DevTest Lab을 쉽게 통합하여 주문형 환경으로 프로비전할 수 있습니다.
- 여러 테스트 에이전트를 프로 비전 하 여 부하 테스트를 강화 하 고 교육 및 데모를 위해 미리 프로 비전 된 환경을 만듭니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>기능
DevTest Labs는 Vm으로 작업 하는 개발자에 게 다음과 같은 기능을 제공 합니다.

- 5 개 미만의 간단한 단계를 수행 하 여 Vm을 신속 하 게 만듭니다.
- 팀 리더 또는 중앙 IT에서 구성, 승인 및 권한이 부여 된 VM 기본 목록에서 큐 레이트를 선택 합니다.
- 모든 소프트웨어 및 도구가 이미 설치 된 미리 만든 사용자 지정 이미지에서 Vm을 만듭니다. 
- Vm을 만들 때 설치 된 소프트웨어의 최신 빌드와 함께 기본적으로 사용자 지정 이미지를 사용 하는 수식에서 Vm을 만듭니다. 
- 프로 비전 된 후 Vm에 배포 된 확장 아티팩트를 설치 합니다.
- Vm에서 자동 종료 및 자동 시작 일정을 설정 합니다.
- 만들기 프로세스를 거치지 않고 미리 만든 VM을 클레임 합니다.

DevTest Labs는 PaaS 환경에서 작업 하는 개발자에 게 다음과 같은 기능을 제공 합니다.

- 몇 가지 간단한 단계를 수행 하 여 PaaS 환경을 빠르게 만들려면 리소스 관리자를 사용 합니다.
- 구성 되 고 팀 리더 또는 중앙 IT에서 권한을 부여 받은 리소스 관리자 템플릿의 큐 레이트 목록에서 선택 합니다.
- 랩 컨텍스트 내에서 Azure를 탐색 하기 위해 리소스 관리자 템플릿을 사용 하 여 빈 리소스 그룹 (샌드박스)을 실행 합니다.

또한 DevTest Labs를 사용 하면 중앙 IT에서 다음 작업을 수행 하 여 낭비를 제어 하 고 리소스에 대 한 비용을 최적화 하 고 예산 내에 유지할 수 있습니다.  

- Vm에서 자동 종료 및 자동 시작 일정 설정
- 사용자가 만들 수 있는 Vm 수에 대 한 정책 설정
- 사용자가 선택 하는 Vm의 크기 및 갤러리 이미지에 대 한 정책을 설정 합니다.
- 랩에서 비용을 추적 하 고 목표를 설정 합니다.
- 필요한 작업을 수행할 수 있도록 랩에 대 한 높은 예상 비용에 대 한 알림 받기

DevTest Labs는 클라우드에서 환경을 만들고 구성 하 고 관리 하는 다음과 같은 이점을 제공 합니다.

## <a name="cost-control-and-governance"></a>비용 제어 및 거 버 넌 스
DevTest Labs를 사용 하면 다음 작업을 수행 하 여 비용을 더 쉽게 제어할 수 있습니다.

- 랩 (예: 사용자 당 Vm 수 또는 랩 별) [에 대 한 정책을 설정](devtest-lab-set-lab-policy.md)합니다. 
- Vm [을 자동으로 종료 하](devtest-lab-set-lab-policy.md) 고 시작 하는 정책을 만듭니다.
- 분리 Vm 및 PaaS 리소스에 대 한 비용을 추적 하 여 [예산](devtest-lab-configure-cost-management.md)내에서 유지 합니다.
- 외부에서 리소스를 회전 하지 않도록 랩의 컨텍스트를 그대로 유지 합니다.

## <a name="quickly-get-to-ready-to-test"></a>빠른 테스트 준비
DevTest Labs를 사용 하면 팀에서 응용 프로그램을 개발 하 고 테스트 하는 데 필요한 모든 것을 갖춘 미리 프로 비전 된 환경을 만들 수 있습니다. 응용 프로그램의 마지막 양호한 빌드가 설치 되 고 작업을 시작 하는 [환경을 요청](devtest-lab-add-claimable-vm.md) 하기만 하면 됩니다. 또는 컨테이너를 사용 하 여 더 빠르게 간결한 환경을 만들 수도 있습니다.

## <a name="create-once-use-everywhere"></a>한 번 만들어서 어디에나 사용
팀 또는 조직 내에서 PaaS [환경 템플릿](devtest-lab-create-environment-from-arm.md) 및 [아티팩트](add-artifact-repository.md) 를 캡처 및 공유 하 여 개발자 및 테스트 환경을 쉽게 만들 수 있습니다.

## <a name="worry-free-self-service"></a>걱정할 필요가 없는 셀프 서비스
DevTest Labs를 통해 개발자와 테스터는 미리 구성 된 리소스 집합을 사용 하 여 [IaaS vm](devtest-lab-add-vm.md) 및 [PaaS 리소스](devtest-lab-create-environment-from-arm.md) 를 빠르고 쉽게 만들 수 있습니다.

## <a name="use-iaas-and-paas-resources"></a>IaaS 및 PaaS 리소스 사용 
또한 개발자는 리소스 관리자 템플릿을 사용 하 여 Azure Service Fabric 클러스터, Azure App Service의 Web Apps 기능 및 SharePoint 팜을 비롯 한 PaaS 리소스를 스핀 할 수 있습니다. 랩에서 PaaS를 시작 하려면 [공용 환경 리포지토리의](devtest-lab-configure-use-public-environments.md) 템플릿을 사용 하거나 [고유한 Git 리포지토리에 랩을 연결](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)합니다. 이러한 리소스에 대 한 비용을 추적 하 여 예산 내에 유지할 수도 있습니다.

## <a name="integrate-with-your-existing-toolchain"></a>기존 도구 체인 통합
미리 만들어진 플러그 인 또는 API를 사용 하 여 원하는 [CI (연속 통합) 도구](devtest-lab-integrate-ci-cd.md), IDE (통합 개발 환경) 또는 자동화 된 릴리스 파이프라인에서 직접 개발/테스트 환경을 프로 비전 합니다. 포괄적인 명령줄 도구를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- DevTest Labs에 대해 자세히 알아보려면 [DevTest labs 개념](devtest-lab-concepts.md)을 참조 하세요.
- 단계별 지침이 포함 된 연습은 [자습서: Azure DevTest Labs를 사용 하 여 랩 설정](tutorial-create-custom-lab.md)을 참조 하세요.
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
ms.openlocfilehash: 599b4325e0eb9aa7f6ccca2626c6d4b14f38149d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339332"
---
# <a name="about-azure-devtest-labs"></a>Azure DevTest Labs 정보
Azure DevTest Labs는 개발자가 효율적으로 셀프 서비스 가상 컴퓨터 및/또는 PaaS 필요한 리소스를 개발, 테스트, 교육 및 데모 등 필요한 도구에 대 한 상수 승인 될 때까지 기다리지 않고 팀 수 있는 서비스. 

랩은 이미 미리 구성 된 자료 또는 모든 필요한 도구 및 개발자 환경을 만드는 데 사용할 수 있는 소프트웨어를 사용 하 여 Resource Manager 템플릿 구성 됩니다. 개발자는 몇 시간에서 며칠 아니라 몇 분 안에 해당 환경을 만들 수 있습니다. 

DevTest Labs를 사용 하 여 다음 작업을 수행 하 여 응용 프로그램의 최신 버전을 테스트할 수 있습니다.

- 재사용 가능 템플릿과 아티팩트를 사용 하 여 Windows 및 Linux 환경을 빠르게 프로 비전
- 주문형 환경을 프로 비전 하도록 DevTest Labs를 사용 하 여 배포 파이프라인을 쉽게 통합
- 여러 테스트 에이전트를 프로 비전 하 여 테스트 부하를 수직 확장 및 교육 및 데모용 미리 프로 비전 된 환경을 만듭니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>기능
DevTest Labs 가상 머신을 사용 하는 개발자에 게 다음 기능을 제공 합니다.

- 보다 작거나 5 개의 간단한 단계를 수행 하 여 가상 컴퓨터를 신속 하 게 만듭니다.
- 구성, 승인 및 팀 리더 또는 central에서 권한을 부여 하는 VM 기본 항목의 엄선된 된 목록에서 선택 IT.
- 모든 소프트웨어 및 도구는 이미지에 설치 되어 있는 미리 만든된 사용자 지정 이미지에서 컴퓨터를 만듭니다. 
- 기본적으로 사용자 지정 이미지와 가상 컴퓨터를 만들 때 설치 된 소프트웨어의 최신 빌드는 수식에서 컴퓨터를 만듭니다.
- 프로 비전 된 후 VM에 배포 된 확장 된 아티팩트를 설치 합니다.
- 자동 종료를 설정 하 고 자동 종료 날짜의 끝에 있고 후 시작 하는 컴퓨터에 대 한 시작 일정 다음날 아침 컴퓨터가 실행 합니다.
- 컴퓨터를 만드는 과정을 단계별로 이동 하지 않고도 미리 만든된 가상 컴퓨터를 클레임 하기만 하면 됩니다. 

DevTest Labs PaaS 환경을 사용 하는 개발자에 게 다음 기능을 제공 합니다.

- 신속 하 게 미만 세 가지 간단한 단계를 수행 하는 PaaS Azure 리소스 관리자 환경을 만듭니다.
- Resource Manager 템플릿, 구성, 승인 및 팀 리더 또는 central에서 권한을 부여 하는 조정 된 목록에서 선택 IT.
- 랩의 컨텍스트 내에서 계속 하면서 Azure의 전체 탐색을 Resource Manager 템플릿을 사용 하 여 빈 리소스 그룹 (샌드박스)을 스핀업 합니다.
- DevTest Lab은 클라우드에서 개발자 및 테스트 환경을 만들고 구성하고 관리하는 데 다음과 같은 이점을 제공합니다.

팀의 개발자를 위한 셀프 서비스 모델 외에도 서비스를 통해 중앙 IT 낭비를 제어 리소스에 대 한 비용을 최적화 하 고 다음 작업을 수행 하 여 예산 내에서 유지 합니다. 

- 설정 자동 종료 및 자동 virtual machines에서 일정을 시작합니다.
- 가상 컴퓨터 사용자의 수에 정책을 설정 만들 수 있습니다.
- 이미지 사용자가 가상 머신 크기 및 갤러리에 정책 설정에서 선택할 수 있습니다.
- 비용 추적 및 랩 목표를 설정 합니다.
- 필요한 조치를 취할 수 있도록 랩에 대 한 높은 예상된 비용에서 알림을 받기. 

DevTest Labs에는 만들기, 구성 및 클라우드에서 환경 관리에 다음과 같은 이점을 제공 합니다.

## <a name="cost-control-and-governance"></a>비용된 제어 및 거 버 넌 스
DevTest Labs 쉽게 비용을 제어 함으로써 다음 작업을 수행할 수 있습니다.

- 사용자 및 랩 당 Vm 수 (VM) 가상 머신의 수와 같은 랩-에 대 한 정책 설정 합니다. 
- 자동으로 종료 하 고 Vm을 시작 하는 정책을 만듭니다.
- 가상 머신 및 미리 정의 된 예산 내에서 유지할 수 있도록 랩 내에서 작동 하는 PaaS 리소스에 비용을 추적할 수 있습니다. 
- 개발자는 외부 기본 리소스 그룹 또는 구독에서 모든 리소스를 스핀업 남지 있도록 랩의 컨텍스트 내에서 유지할 수 있습니다.

## <a name="quickly-get-to-ready-to-test"></a>빠른 테스트 준비
DevTest Lab을 사용하면 팀에서 애플리케이션 개발 및 테스트를 시작하는 데 필요한 모든 사항이 미리 프로비전된 환경을 만들 수 있습니다. 단순히 클레임 응용 프로그램의 마지막으로 성공한 빌드를 설치한 환경 및 바로 작업 시작 합니다. 또는 더 빠르고 간결한 환경을 만들기 위해 컨테이너를 사용합니다.

## <a name="create-once-use-everywhere"></a>한 번 만들어서 어디에나 사용
캡처 및 개발자 만들기 및 테스트 환경을 쉽게 PaaS 환경 템플릿 및 팀 또는-소스 제어에 all-조직 내에서 아티팩트를 공유 합니다.

## <a name="worry-free-self-service"></a>걱정할 필요가 없는 셀프 서비스
DevTest Labs는 개발자와 테스터를 신속 하 게 사용 하도록 설정 하 고 쉽게 virtual machines (IaaS) 및 PaaS 리소스 내에 미리 구성 된 리소스 집합을 사용 하 여 몇 번의 클릭을 만듭니다.

## <a name="use-iaas-and-paas-resources"></a>IaaS 및 PaaS 리소스를 사용 합니다. 
또한 DevTest Labs는 개발자가 웹 앱, Service Fabric 클러스터, SharePoint Farms와 같은 PaaS 리소스를 스핀업 하 고 등을 수 있습니다. 랩 내에서 Resource Manager 템플릿을 사용합니다. 공용 환경 리포지토리에서에서 Resource Manager 템플릿을 사용 하거나 랩에서 PaaS에서 시작 하려면 사용자 고유의 Git 리포지토리에 랩 연결 합니다. 또한에 미리 정의 된 예산 내에서 이러한 리소스에 비용을 추적할 수 있습니다. 

## <a name="integrate-with-your-existing-toolchain"></a>기존 도구 체인과 통합
미리 만든된 플러그 인 또는 프로 비전 하 여 원하는 CI (연속 통합) 도구에서 직접 개발/테스트 환경을 통합 개발 환경 (IDE) 또는 자동화 된 릴리스 파이프라인 API를 사용 합니다. Microsoft의 포괄적인 명령줄 도구를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- DevTest Labs에 대 한 자세한 내용은를 참조 하세요. [DevTest Lab 개념](devtest-lab-concepts.md)
- 단계별 지침이 포함 된 연습을 참조 하세요. [자습서: Azure DevTest Labs를 사용 하 여 랩 설정](tutorial-create-custom-lab.md)



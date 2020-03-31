---
title: 교육용 클래스룸 랩 사용 - Azure 랩 서비스
description: 이 문서에서는 Azure DevTest 랩을 사용하여 학습 시나리오에 Azure에서 랩을 만드는 방법을 설명합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 155806222f9e11fec177487b7147d81054ac06ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717991"
---
# <a name="use-classroom-labs-for-trainings"></a>교육용 교실 랩 사용
교육을 위한 랩을 설정할 수 있습니다. Azure Lab 서비스의 클래스룸 랩을 사용하면 각 학습자가 동일하고 격리된 환경을 학습에 사용하는 교육을 위한 랩을 만들 수 있습니다. 각 실습생이 필요할 때에만 교육 환경을 이용할 수 있도록 하고 교육에 필요한 리소스(예: 가상 머신)를 충분히 포함하는 정책을 적용할 수 있습니다. 

![교실 실습실](../media/classroom-labs-scenarios/classroom.png)

클래스룸 랩은 가상 환경에서 교육을 수행하는 데 필요한 다음 요구 사항을 충족합니다. 

- 실습생은 학습 환경을 신속하게 프로비전할 수 있습니다.
- 모든 학습 컴퓨터는 동일해야 합니다.
- 실습생은 다른 실습생이 만든 VM을 볼 수 없습니다.
- 실습생이 학습에 필요한 만큼의 VM만 사용하고 사용하지 않을 때는 VM을 종료하도록 하여 비용을 제어합니다.
- 각 실습생과 학습 랩을 쉽게 공유합니다.
- 학습 랩을 반복해서 다시 사용합니다.

이 문서에서는 앞에서 설명한 교육 요구 사항 및 교육을 위한 랩을 설정하기 위해 수행할 수 있는 자세한 단계를 충족하는 데 사용할 수 있는 다양한 Azure Lab Services 기능에 대해 알아봅니다.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>랩 계정 관리자로 랩 계정 만들기
Azure Lab 서비스를 사용하는 첫 번째 단계는 Azure Portal에서 랩 계정을 만드는 것입니다. 랩 계정 관리자가 랩 계정을 만든 후 관리자는 **랩 작성자** 역할에 랩을 만들려는 사용자를 추가합니다. 트레이너는 학생들이 가르치는 과정에 대한 연습을 할 수 있도록 가상 머신이 있는 랩을 만듭니다. 자세한 내용은 [랩 계정 만들기 및 관리를](how-to-manage-lab-accounts.md)참조하십시오.

## <a name="create-and-manage-classroom-labs"></a>클래스룸 랩 만들기 및 관리
랩 계정의 랩 작성자 역할멤버인 트레이너는 랩 계정에 하나 이상의 랩을 만들 수 있습니다. 코스에서 연습을 수행하는 데 필요한 모든 소프트웨어로 템플릿 VM을 만들고 구성합니다. 사용 가능한 이미지에서 즉시 만든 이미지를 선택한 다음 랩에 필요한 소프트웨어를 설치하여 사용자 지정합니다. 자세한 내용은 [교실 랩 만들기 및 관리를](how-to-manage-classroom-labs.md)참조하십시오.

## <a name="configure-usage-settings-and-policies"></a>사용 설정 및 정책 구성
랩 작성자는 랩에 사용자를 추가 또는 제거하고, 랩 사용자에게 보낼 등록 링크를 받고, 사용자당 개별 할당량 설정과 같은 정책을 설정하고, 랩에서 사용할 수 있는 VM 수를 업데이트하는 등의 정책을 설정할 수 있습니다. 자세한 내용은 [사용 설정 및 정책 구성을](how-to-configure-student-usage.md)참조하십시오.

## <a name="create-and-manage-schedules"></a>일정 만들기 및 관리
일정을 사용하면 랩의 VM이 지정된 시간에 자동으로 시작 및 종료되도록 Classroom(강의실) 랩을 구성할 수 있습니다. 일회성 일정이나 되풀이 일정을 정의할 수 있습니다. 자세한 내용은 [강의실 랩의 일정 만들기 및 관리를](how-to-create-schedules.md)참조하십시오.

## <a name="set-up-and-publish-a-template-vm"></a>템플릿 VM 설정 및 게시
랩의 템플릿은 모든 사용자의 가상 머신이 만들어지는 기본 가상 머신 이미지입니다. 템플릿 VM을 교육 참석자에게 제공할 내용과 정확히 일치하도록 설정합니다. 랩 사용자에게 표시되는 템플릿의 이름 및 설명을 제공할 수 있습니다. 그런 후 랩 사용자가 템플릿 VM의 인스턴스를 사용할 수 있도록 템플릿을 게시합니다. 템플릿을 게시하면 Azure Lab Services가 템플릿을 사용하여 랩에 VM을 만듭니다. 이 과정에서 만들어지는 VM 수는 랩에 허용되는 최대 사용자 수와 같으며, 이 값은 랩의 사용 정책에서 설정할 수 있습니다. 모든 가상 머신은 템플릿과 구성이 동일합니다. 자세한 내용은 [템플릿 가상 컴퓨터 설정 및 게시를](how-to-create-manage-template.md)참조하십시오. 

## <a name="use-vms-in-the-classroom-lab"></a>강의실 랩에서 VM 사용
학생 또는 교육 참석자는 랩에 등록하고 VM에 연결하여 코스에 대한 연습을 수행합니다. 자세한 내용은 [강의실 랩에 액세스하는 방법을](how-to-use-classroom-lab.md)참조하십시오.

## <a name="next-steps"></a>다음 단계
클래스룸 랩에서 실습 계정 만들기를 시작하십시오. [Tutorial: Setup a lab account with Azure Lab Services](tutorial-setup-lab-account.md)
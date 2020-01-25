---
title: 학습에 교실 Labs 사용 Azure Lab Services
description: 이 문서에서는 학습 시나리오에 대 한 Azure에서 랩을 만들기 위해 Azure DevTest Labs를 사용 하는 방법을 설명 합니다.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717991"
---
# <a name="use-classroom-labs-for-trainings"></a>학습에 대 한 교실 Labs 사용
학습에 대 한 랩을 설정할 수 있습니다. Azure Lab Services의 교실 랩에서는 각 실습생 교육을 위해 동일 하 고 격리 된 환경을 사용 하는 교육을 위한 랩을 만들 수 있습니다. 각 실습생이 필요할 때에만 교육 환경을 이용할 수 있도록 하고 교육에 필요한 리소스(예: 가상 머신)를 충분히 포함하는 정책을 적용할 수 있습니다. 

![교실 랩](../media/classroom-labs-scenarios/classroom.png)

교실 랩은 모든 가상 환경에서 학습을 수행 하는 데 필요한 다음과 같은 요구 사항을 충족 합니다. 

- 실습생은 학습 환경을 신속하게 프로비전할 수 있습니다.
- 모든 학습 컴퓨터는 동일해야 합니다.
- 실습생은 다른 실습생이 만든 VM을 볼 수 없습니다.
- 실습생이 학습에 필요한 만큼의 VM만 사용하고 사용하지 않을 때는 VM을 종료하도록 하여 비용을 제어합니다.
- 각 실습생과 학습 랩을 쉽게 공유합니다.
- 학습 랩을 반복해서 다시 사용합니다.

이 문서에서는 앞에서 설명한 교육 요구 사항과 학습을 위한 랩을 설정 하기 위해 수행할 수 있는 자세한 단계를 충족 하는 데 사용할 수 있는 다양 한 Azure Lab Services 기능에 대해 알아봅니다.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>랩 계정 관리자로 랩 계정 만들기
Azure Lab Services를 사용 하는 첫 번째 단계는 Azure Portal에서 랩 계정을 만드는 것입니다. 랩 계정 관리자가 랩 계정을 만든 후 랩 **작성자** 역할에 랩을 만들려는 사용자를 추가 합니다. 강사는 학생 들이 교육 하 고 있는 과정을 위해 연습을 수행 하는 virtual machines를 사용 하 여 랩을 만듭니다. 자세한 내용은 [랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)를 참조 하세요.

## <a name="create-and-manage-classroom-labs"></a>클래스룸 랩 만들기 및 관리
랩 계정에서 랩 작성자 역할의 멤버인 강사는 랩 계정에서 랩을 하나 이상 만들 수 있습니다. 과정에서 연습을 수행 하는 데 필요한 모든 소프트웨어를 사용 하 여 템플릿 VM을 만들고 구성 합니다. 교실 랩을 만들기 위해 사용 가능한 이미지에서 미리 만든 이미지를 선택 하 고 랩에 필요한 소프트웨어를 설치 하 여 사용자 지정 합니다. 자세한 내용은 [교실 Labs 만들기 및 관리](how-to-manage-classroom-labs.md)를 참조 하세요.

## <a name="configure-usage-settings-and-policies"></a>사용 설정 및 정책 구성
랩 작성자는 랩에 사용자를 추가 또는 제거 하 고, 랩 사용자에 게 보낼 등록 링크를 가져오고, 사용자별 개별 할당량을 설정 하는 등의 정책을 설정 하 고, 랩에서 사용할 수 있는 Vm의 수를 업데이트 하는 등의 작업을 수행할 수 있습니다. 자세한 내용은 [사용 설정 및 정책 구성](how-to-configure-student-usage.md)을 참조 하세요.

## <a name="create-and-manage-schedules"></a>일정 만들기 및 관리
일정을 사용하면 랩의 VM이 지정된 시간에 자동으로 시작 및 종료되도록 Classroom(강의실) 랩을 구성할 수 있습니다. 일회성 일정이나 되풀이 일정을 정의할 수 있습니다. 자세한 내용은 [교실 labs에 대 한 일정 만들기 및 관리](how-to-create-schedules.md)를 참조 하세요.

## <a name="set-up-and-publish-a-template-vm"></a>템플릿 VM 설정 및 게시
랩의 템플릿은 모든 사용자의 가상 머신이 만들어지는 기본 가상 머신 이미지입니다. 교육 참석자에 게 제공 하려는 것과 정확히 일치 하도록 템플릿 VM을 설정 합니다. 랩 사용자에게 표시되는 템플릿의 이름 및 설명을 제공할 수 있습니다. 그런 후 랩 사용자가 템플릿 VM의 인스턴스를 사용할 수 있도록 템플릿을 게시합니다. 템플릿을 게시하면 Azure Lab Services가 템플릿을 사용하여 랩에 VM을 만듭니다. 이 과정에서 만들어지는 VM 수는 랩에 허용되는 최대 사용자 수와 같으며, 이 값은 랩의 사용 정책에서 설정할 수 있습니다. 모든 가상 머신은 템플릿과 구성이 동일합니다. 자세한 내용은 [템플릿 가상 머신 설정 및 게시](how-to-create-manage-template.md)를 참조 하세요. 

## <a name="use-vms-in-the-classroom-lab"></a>교실 랩에서 Vm 사용
학생 또는 교육 참석자는 랩에 등록 하 고이 과정에 대 한 연습을 위해 VM에 연결 합니다. 자세한 내용은 [교실 랩에 액세스 하는 방법](how-to-use-classroom-lab.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계
[자습서: Azure Lab Services을 사용 하 여 랩 계정 설정](tutorial-setup-lab-account.md)문서의 지침에 따라 교실 랩에서 랩 계정 만들기를 시작 합니다.
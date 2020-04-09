---
title: Azure 랩 서비스에 대한 가속화된 강의실 랩 설정 가이드
description: 이 가이드는 랩 제작자가 학교 내에서 사용할 수 있도록 랩 계정을 신속하게 설정할 수 있도록 도와줍니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: e00b6dd5af1cb489aee9e8b4c9f3337eb02e4b14
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878105"
---
# <a name="classroom-lab-setup-guide"></a>교실 랩 설정 가이드

랩에서 만들 가상 컴퓨터(VM)의 수에 따라 학생에게 랩을 게시하는 프로세스는 최대 몇 시간이 걸릴 수 있습니다. 랩이 제대로 작동하는지 확인하고 학생의 VM을 게시할 수 있는 충분한 시간을 허용하기 위해 적어도 하루 정도 랩을 설정하도록 허용합니다.

## <a name="understand-the-lab-requirements-of-your-class"></a>클래스의 랩 요구 사항 이해

새 랩을 설정하기 전에 다음 질문을 고려해야 합니다.

### <a name="what-software-requirements-does-the-class-have"></a>클래스에 어떤 소프트웨어 요구 사항이 있습니까?

클래스의 학습 목표에 따라 랩의 VM에 설치해야 하는 운영 체제, 응용 프로그램 및 도구를 결정합니다. 랩 VM을 설정하려면 다음 세 가지 옵션이 있습니다.

- **Azure 마켓플레이스 이미지 사용**: Azure Marketplace는 랩을 만들 때 사용할 수 있는 수백 개의 이미지를 제공합니다. 일부 클래스의 경우 이러한 이미지 중 하나에 클래스에 필요한 모든 것이 이미 포함되어 있을 수 있습니다.

- **새 사용자 지정 이미지 만들기:** Azure Marketplace 이미지를 시작 점으로 사용하고 추가 소프트웨어를 설치하고 구성을 변경하여 사용자 지정하여 사용자 지정 이미지를 만들 수 있습니다.

- **기존 사용자 지정 이미지 사용**: 이전에 만들었거나 학교의 다른 관리자 나 교수진이 만든 기존 사용자 지정 이미지를 재사용할 수 있습니다. 이를 위해서는 관리자가 사용자 지정 이미지를 저장하기 위한 리포지토리인 공유 이미지 갤러리를 구성해야 합니다.

> [!NOTE]
> 관리자는 Azure Marketplace 이미지 및 사용자 지정 이미지를 사용할 수 있도록 사용할 책임이 있습니다. IT 부서와 협력하여 필요한 이미지를 사용하도록 설정합니다. 만든 사용자 지정 이미지는 소유한 랩 내에서 사용할 수 있도록 자동으로 활성화됩니다.

### <a name="what-hardware-requirements-does-the-class-have"></a>클래스에 어떤 하드웨어 요구 사항이 있습니까?

다음과 같은 다양한 계산 크기 중에서 선택할 수 있습니다.

- 중첩된 가상화 크기이므로 학생에게 중첩된 여러 VM을 호스팅할 수 있는 VM에 대한 액세스 권한을 부여할 수 있습니다. 예를 들어 네트워킹 코스에 이 계산 크기를 사용할 수 있습니다.

- GPU 크기로 학생이 컴퓨터 집약적인 유형의 응용 프로그램을 사용할 수 있도록 합니다. 예를 들어 이 선택은 인공 지능 및 기계 학습에 적합할 수 있습니다.

사용 가능한 계산 크기의 전체 목록을 보려면 [VM 크기 조정에](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) 대한 가이드를 참조하십시오.

> [!NOTE]
> 랩의 지역에 따라 지역에 따라 다르므로 사용 가능한 계산 크기가 더 적을 수 있습니다. 일반적으로 필요에 가장 가까운 가장 작은 계산 크기를 선택해야 합니다. Azure Lab Services를 사용하면 나중에 다른 계산 용량의 새 랩을 설정할 수 있습니다(필요한 경우).

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>클래스는 외부 Azure 또는 네트워크 리소스에 어떤 종속성을 가지고 있습니까?

랩 VM이 데이터베이스, 파일 공유 또는 라이선스 서버와 같은 외부 리소스를 사용해야 하는 경우 관리자와 협력하여 랩에서 이러한 리소스에 액세스할 수 있는지 확인합니다.

가상 네트워크에서 *보호되지 않는* Azure 리소스에 액세스하려면 관리자가 추가 구성을 요청할 필요가 없습니다. 공용 인터넷을 통해 이러한 리소스에 액세스할 수 있습니다.

> [!NOTE]
> VM에서 직접 리소스를 제공하여 외부 리소스에 대한 랩의 종속성을 줄일 수 있는지 여부를 고려해야 합니다. 예를 들어 외부 데이터베이스에서 데이터를 읽을 필요가 없도록 VM에 직접 데이터베이스를 설치할 수 있습니다.  

### <a name="how-will-costs-be-controlled"></a>비용은 어떻게 관리되나요?

랩 서비스는 종량제 가격 책정 모델을 사용하므로 랩 VM이 실행되는 시간에 대해서만 비용을 지불합니다. 비용을 제어하기 위해 일반적으로 서로 함께 사용되는 세 가지 옵션이 있습니다.

- **일정**: 일정을 사용하면 랩의 VM이 시작되고 종료되는 시기를 자동으로 제어할 수 있습니다.
- **할당량**: 할당량은 학생이 예약된 시간 이외에 VM에 액세스할 수 있는 시간을 제어합니다. 학생이 할당량을 사용하는 동안 할당량에 도달하면 VM이 자동으로 종료됩니다. 할당량이 증가하지 않으면 학생은 VM을 다시 시작할 수 없습니다.
- **자동 종료**: 자동 종료 설정을 사용하면 학생이 RDP(원격 데스크톱 프로토콜) 세션에서 연결을 끊은 후 Windows VM이 일정 시간 후에 자동으로 종료됩니다. 이 설정은 기본적으로 사용하지 않도록 설정됩니다.  

    > [!NOTE]
    > 이 설정은 현재 Windows에만 존재합니다.

### <a name="how-will-students-save-their-work"></a>학생들은 자신의 작업을 어떻게 저장할 것인가?

학생에게는 각 학생에게 자신의 VM이 할당되며, 이 VM은 랩의 수명 동안 할당됩니다. 다음을 선택할 수 있습니다.

- VM에 직접 저장합니다.
- OneDrive 또는 GitHub와 같은 외부 위치에 저장합니다.

랩 VM의 학생에 대해 OneDrive를 자동으로 구성할 수 있습니다.

> [!NOTE]
> 학생이 랩 외부에서 저장된 작업에 계속 액세스할 수 있도록 하고 수업이 끝난 후에도 학생이 자신의 작업을 외부 리포지토리에 저장하는 것이 좋습니다.

### <a name="how-will-students-connect-to-their-vm"></a>학생들은 VM에 어떻게 연결합니까?

RDP에서 Windows VM으로, 우리는 학생들이 [마이크로 소프트 원격 데스크톱 클라이언트를](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)사용하는 것이 좋습니다 . 원격 데스크톱 클라이언트는 Mac, 크롬북 및 Windows를 지원합니다.

Linux VM의 경우 학생들은 SSH 또는 RDP를 사용할 수 있습니다. 학생이 RDP를 사용하여 연결하도록 하려면 필요한 RDP 및 GUI 패키지를 설치하고 구성해야 합니다.

## <a name="set-up-your-lab"></a>랩 설정

클래스의 랩에 대한 요구 사항을 이해하면 설정할 준비가 된 것입니다. 이 섹션의 링크를 따라 랩을 설정하는 방법을 확인합니다.

1. **랩을 만듭니다.** 지침은 [강의실 랩 만들기에](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) 대한 자습서를 참조하십시오.

    > [!NOTE]
    > 클래스에 중첩 가상화가 필요한 경우 [중첩 가상화를 사용하도록 설정하는](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)단계를 참조하십시오.

1. **이미지를 사용자 지정하고 랩 VM을 게시합니다.** 템플릿 VM이라는 특수 VM에 연결합니다. 다음 가이드의 단계를 참조하십시오.
    - [템플릿 VM 생성 및 관리](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [공유 이미지 갤러리 사용](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Windows를 사용하는 경우 [Windows 템플릿 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template)을 준비하는 지침도 표시됩니다. 이 지침에는 학생이 사용할 OneDrive 및 Office를 설정하는 단계가 포함됩니다.

1. **VM 풀 및 용량을 관리합니다.** 클래스에서 필요에 따라 VM 용량을 쉽게 확장하거나 축소할 수 있습니다. VM 용량을 늘리려면 새 VM을 설정하는 작업이 수반되므로 몇 시간이 걸릴 수 있습니다. [VM 풀을 설정하고 관리하는](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)단계를 참조하십시오.

1. **랩 사용자를 추가하고 관리합니다.** 랩에 사용자를 추가하려면 다음 자습서의 단계를 참조하십시오.
   - [랩에 사용자 추가](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [사용자에게 초대 보내기](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    학생이 사용할 수 있는 계정 유형에 대한 자세한 내용은 [학생 계정을](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)참조하십시오.
  
1. **비용 제어를 설정합니다.** 랩 비용을 제어하려면 일정, 할당량 및 자동 종료를 설정합니다. 다음 자습서를 참조하세요.

   - [일정 설정](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > 설치한 운영 체제 유형에 따라 VM을 시작하는 데 몇 분 정도 걸릴 수 있습니다. 랩 VM을 예약된 시간 동안 사용할 수 있도록 하려면 VM을 30분 전에 시작하는 것이 좋습니다.

   - [사용자에 대한 할당량을 설정하고](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) [특정 사용자에 대한 추가 할당량 설정](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [연결 해제 시 자동 종료 사용](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > 일정, 할당량 및 자동 종료는 템플릿 VM에 적용되지 않습니다. 따라서 템플릿VM이 사용되지 않을 때 는 종료해야 합니다. 그렇지 않으면 비용이 계속 발생합니다. 또한 기본적으로 랩을 만들 때 템플릿 VM이 자동으로 시작됩니다. 랩 설정을 즉시 완료하고 템플릿 VM을 종료해야 합니다.

1. **대시보드를 사용합니다.** 지침은 [랩의 대시보드 를 참조하십시오.](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)

    > [!NOTE]
    > 대시보드에 표시된 예상 비용은 학생이 랩을 사용할 때 기대할 수 있는 최대 비용입니다. 예를 들어 학생이 사용하지 않은 할당량 시간에 대한 요금은 *청구되지 않습니다.* 예상 비용은 템플릿 VM 또는 공유 이미지 갤러리 사용에 대한 요금을 *반영하지 않습니다.*

## <a name="next-steps"></a>다음 단계

- [클래스룸 랩의 사용량 추적](tutorial-track-usage.md)
  
- [클래스룸 랩 액세스](tutorial-connect-virtual-machine-classroom-lab.md)
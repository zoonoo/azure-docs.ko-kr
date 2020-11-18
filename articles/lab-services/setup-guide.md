---
title: Azure Lab Services에 대 한 가속화 되는 랩 설치 가이드
description: 이 가이드를 통해 랩 작성자는 학교 내에서 사용할 랩 계정을 신속하게 설정할 수 있습니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8ef168aefb69df32f57b623bb488adbb97cbd411
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659678"
---
# <a name="lab-setup-guide"></a>랩 설정 가이드

학생에 게 랩을 게시 하는 프로세스에는 몇 시간이 걸릴 수 있습니다.  시간은 랩에 생성 되는 Vm (가상 머신)의 수에 따라 달라 집니다. 랩을 설정 하는 데 적어도 하루 이상 허용 하 여 제대로 작동 하는지 확인 하 고 학생의 Vm을 게시할 수 있는 충분 한 시간을 허용 합니다.

## <a name="understand-the-lab-requirements-of-your-class"></a>클래스 랩 요구 사항 이해

새 랩을 설정하기 전에 다음 질문을 고려해야 합니다.

### <a name="what-software-requirements-does-the-class-have"></a>클래스에서 소프트웨어 요구 사항은 무엇인가요?

클래스의 학습 목표에 따라 랩의 VM에 설치해야 하는 운영 체제, 애플리케이션 및 도구를 결정합니다. 랩 VM을 설정하려면 다음 세 가지 옵션을 사용할 수 있습니다.

- **Azure Marketplace 이미지 사용**: Azure Marketplace는 랩을 만들 때 사용할 수 있는 수백 개의 이미지를 제공합니다. 일부 클래스의 경우 이러한 이미지 중 하나에 클래스에 필요한 모든 항목이 이미 포함되어 있을 수 있습니다.

- **새 사용자 지정 이미지 만들기**: Azure Marketplace 이미지를 시작점으로 사용하고 추가 소프트웨어를 설치하고 구성을 변경하여 이 이미지를 사용자 지정함으로써 고유의 사용자 지정 이미지를 만들 수 있습니다.

- **기존 사용자 지정 이미지 사용**: 이전에 만들었거나 학교의 다른 관리자나 교직원이 만든 기존 사용자 지정 이미지를 재사용할 수 있습니다. 사용자 지정 이미지를 사용 하려면 관리자가 공유 이미지 갤러리를 설정 해야 합니다.  공유 이미지 갤러리는 사용자 지정 이미지를 저장 하는 데 사용 되는 리포지토리입니다.

> [!NOTE]
> 관리자는 Azure Marketplace 이미지와 사용자 지정 이미지를 사용하도록 설정해야 합니다. 필요한 이미지를 사용할 수 있도록 IT 부서와 조정합니다. 사용자가 만든 사용자 지정 이미지는 사용자의 랩 내에서 자동으로 사용하도록 설정됩니다.

### <a name="what-hardware-requirements-does-the-class-have"></a>클래스에서 하드웨어 요구 사항은 무엇인가요?

선택할 수 있는 다양 한 계산 크기는 다음과 같습니다.

- 중첩 된 가상화 크기를 사용 하 여 여러 중첩 된 Vm을 호스트할 수 있는 VM에 학생에 대 한 액세스를 제공할 수 있습니다. 예를 들어 네트워킹 또는 윤리적 해킹 클래스에 대해이 계산 크기를 사용할 수 있습니다.

- GPU 크기. 학생이 컴퓨팅 집약적인 애플리케이션 형식을 사용할 수 있습니다. 예를 들어 이러한 선택은 인공 지능 및 기계 학습에 사용 되는 경우가 많습니다.

적절 한 VM 크기를 선택 하는 방법에 대 한 지침은 다음 문서를 참조 하세요.
- [VM 크기 조정](./administrator-guide.md#vm-sizing)
- [물리적 랩에서 Azure Lab Services로 이동](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> 랩의 지역에 따라 사용 가능한 컴퓨팅 크기가 더 작을 수 있습니다. 이는 지역에 따라 다르기 때문입니다. 일반적으로 사용자의 요구에 가장 가까운 가장 작은 컴퓨팅 크기를 선택해야 합니다. Azure Lab Services를 사용하면 필요한 경우 나중에 다른 컴퓨팅 용량으로 새로운 랩을 설정할 수 있습니다.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>클래스에서 외부 Azure 또는 네트워크 리소스에 대해 어떤 종속성이 있나요?
랩 Vm에는 데이터베이스, 파일 공유 또는 라이선스 서버 액세스와 같은 외부 리소스에 대 한 액세스 권한이 필요할 수 있습니다.  랩 Vm이 외부 리소스를 사용 하도록 허용 하려면 IT 관리자와 조정 합니다.

> [!NOTE]
> VM에서 직접 리소스를 제공하여 랩의 외부 리소스에 대한 종속성을 줄일 수 있는지 고려해야 합니다. 예를 들어 외부 데이터베이스에서 데이터를 읽을 필요가 없도록 VM에 직접 데이터베이스를 설치하면 됩니다.  

### <a name="how-will-costs-be-controlled"></a>비용은 어떻게 제어할 수 있나요?
랩 서비스는 종량제 가격 책정 모델을 사용하므로 랩 VM이 실행되는 시간에 대해서만 비용을 지불합니다. 비용을 제어 하기 위해 일반적으로 함께 사용 되는 세 가지 옵션이 있습니다.

- **일정**: 일정을 사용하면 랩의 VM이 시작되고 종료되는 시간을 자동으로 제어할 수 있습니다.
- **할당량**: 할당량은 학생이 예정된 시간 외에 VM에 액세스할 수 있는 시간을 제어합니다.  학생이 자신의 VM을 사용 하 고 해당 할당량에 도달 하면 VM이 자동으로 종료 됩니다.  할당량을 늘리지 않으면 학생은 VM을 다시 시작할 수 없습니다.
- **자동 종료**: 사용 하도록 설정 하면 학생이 RDP (원격 데스크톱 프로토콜) 세션에서 연결을 끊은 후 자동 종료 설정으로 인해 Windows vm이 자동으로 종료 됩니다. 기본적으로 이 설정은 사용하지 않도록 설정되어 있습니다.

자세한 내용은 다음 문서를 참조 하세요.
- [비용 예측](./cost-management-guide.md#estimate-the-lab-costs)
- [비용 관리](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>학생은 어떻게 작업을 저장하나요?
학생에게는 랩의 수명 동안 각각의 VM이 할당됩니다. 다음을 선택할 수 있습니다.

- VM에 직접 저장합니다.
- OneDrive 또는 GitHub와 같은 외부 위치에 저장합니다.

랩 VM에서 학생을 위해 OneDrive를 자동으로 구성할 수 있습니다.

> [!NOTE]
> 학생들이 랩 외부에 저장된 작업에 계속 액세스할 수 있도록 하고 클래스가 종료된 후에는 학생들은 외부 리포지토리에 작업을 저장하는 것이 좋습니다.

### <a name="how-will-students-connect-to-their-vm"></a>학생이 해당 VM에 어떻게 연결하나요?
Windows VM에 대한 RDP의 경우 학생이 [Microsoft 원격 데스크톱 클라이언트](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)를 사용하는 것이 좋습니다. 원격 데스크톱 클라이언트는 Mac, Chromebook 및 Windows를 지원합니다.

Linux VM의 경우 학생은 SSH 또는 RDP 중 하나를 사용할 수 있습니다. 학생이 RDP를 사용하여 연결하도록 하려면 필요한 RDP 및 GUI 패키지를 설치하고 구성해야 합니다.

### <a name="will-students-also-be-using-microsoft-teams"></a>학생 들도 Microsoft 팀을 사용 하 고 있나요?
Azure Lab Services는 교직원이 팀 내에서 랩을 만들고 관리할 수 있도록 Microsoft 팀과 통합 됩니다.  마찬가지로 학생 들은 팀 내에서 랩에 액세스할 수 있습니다.

자세한 내용은 다음 아티클을 참조하세요.
- [Microsoft 팀 내 Azure Lab Services](./lab-services-within-teams-overview.md)

## <a name="set-up-your-lab"></a>랩 설정

클래스 랩에 대한 요구 사항을 파악하면 설정할 준비가 된 것입니다. 랩 설정 방법을 보려면 이 섹션의 링크를 클릭합니다.  팀 내에서 랩을 사용 하 고 있는지 여부에 따라 여러 단계가 제공 됩니다.

1. **랩을 만듭니다.** 랩 만들기에 대 한 자습서를 참조 하세요.
    - 지침을 보려면 [교실 랩을 만드세요](./tutorial-setup-classroom-lab.md#create-a-classroom-lab) .
    - [팀에서 랩 만들기](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > 클래스에 중첩된 가상화가 필요한 경우 [중첩된 가상화 사용](./how-to-enable-nested-virtualization-template-vm.md) 단계를 참조하세요.

1. **이미지를 사용자 지정하고 랩 VM을 게시합니다.** 템플릿 VM이라는 특별 VM에 연결합니다. 다음 가이드의 단계를 참조하세요.
    - [템플릿 VM 만들기 및 관리](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [공유 이미지 갤러리 사용](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Windows를 사용하는 경우 [Windows 템플릿 VM 준비](./how-to-prepare-windows-template.md) 지침도 참조하세요. 이 지침에는 학생이 사용할 수 있도록 OneDrive 및 Office를 설정하는 단계가 포함되어 있습니다.

1. **VM 풀 및 용량을 관리합니다.** 클래스에 필요한 만큼 VM 용량을 쉽게 확장하거나 축소할 수 있습니다. 새 Vm을 설정 하는 동안 VM 용량을 늘리려면 몇 시간이 걸릴 수 있습니다. 다음 문서의 단계를 참조 하세요.
    - [VM 풀 설정 및 관리](./how-to-set-virtual-machine-passwords.md)
    - [팀의 랩 서비스에서 VM 풀 관리](./how-to-manage-vm-pool-within-teams.md)

1. **랩 사용자를 추가 및 관리합니다.** 랩에 사용자를 추가하려면 다음 자습서의 단계를 참조하세요.
   - [랩에 사용자 추가](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [사용자에게 초대 보내기](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [팀에서 Lab Services 사용자 목록 관리](./how-to-manage-user-lists-within-teams.md)

    학생이 사용할 수 있는 계정 유형에 대한 자세한 내용은 [학생 계정](./how-to-configure-student-usage.md#student-accounts)을 참조하세요.
  
1. **비용 제어를 설정합니다.** 랩의 비용을 제어 하려면 일정, 할당량 및 자동 종료를 설정 합니다. 다음 자습서를 참조하세요.

   - [일정 설정](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > 설치한 운영 체제의 유형에 따라 VM을 시작 하는 데 몇 분 정도 걸릴 수 있습니다. 예약된 시간 동안 랩 VM을 사용할 준비가 되었는지 확인하려면 30분 전에 VM을 시작하는 것이 좋습니다.

   - [사용자에 대한 할당량 설정](./how-to-configure-student-usage.md#set-quotas-for-users) 및 [특정 사용자에 대한 추가 할당량 설정](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [연결 해제 시 자동 종료 사용](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > 일정 및 할당량은 템플릿 VM에 적용되지 않지만 자동 종료 설정은 적용됩니다. 
        > 
        > 랩을 만들면 템플릿 VM이 만들어지지만 시작되지는 않습니다. 이를 시작하고, 연결하고, 랩에 대한 필수 구성 요소 소프트웨어를 설치한 다음, 게시할 수 있습니다. 템플릿 VM을 게시하는 경우 자동으로 종료됩니다(아직 수행하지 않은 경우). 
        > 
        > 템플릿 VM은 실행될 때 **비용** 이 발생하므로 템플릿 VM을 실행할 필요가 없는 경우 종료해야 합니다.

    - [팀 내에서 Lab Services 일정 만들기 및 관리](./how-to-create-schedules-within-teams.md) 

1. **대시보드를 사용합니다.** 자세한 내용은 [랩의 대시보드 사용](./use-dashboard.md)을 참조하세요.

    > [!NOTE]
    > 대시보드에 표시되는 예상 비용은 랩의 학생 사용에 대해 예상할 수 있는 최대 비용입니다. 예를 들어 학생이 사용하지 않은 할당량 시간에 대한 요금이 청구되지 *않습니다*. 예상 비용은 템플릿 VM, 공유 이미지 갤러리 또는 랩 작성자가 사용자 컴퓨터를 시작 하는 경우에 대 한 요금을 반영 *하지 않습니다* .

## <a name="next-steps"></a>다음 단계

- [클래스룸 랩의 사용량 추적](tutorial-track-usage.md)
  
- [클래스룸 랩 액세스](tutorial-connect-virtual-machine-classroom-lab.md)
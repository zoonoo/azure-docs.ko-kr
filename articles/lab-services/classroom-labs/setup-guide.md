---
title: Azure Lab Services에 대 한 가속화를 위한 교실 랩 랩 설정 가이드
description: 이 가이드를 통해 랩 작성자는 학교 내에서 사용할 랩 계정을 신속 하 게 설정할 수 있습니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878105"
---
# <a name="classroom-lab-setup-guide"></a>교실 랩 설치 가이드

학생에 게 랩을 게시 하는 프로세스는 랩에서 생성 될 Vm (가상 머신)의 수에 따라 최대 몇 시간이 걸릴 수 있습니다. 랩을 설정 하는 데 적어도 하루 이상 허용 하 여 제대로 작동 하는지 확인 하 고 학생의 Vm을 게시할 수 있는 충분 한 시간을 허용 합니다.

## <a name="understand-the-lab-requirements-of-your-class"></a>클래스의 랩 요구 사항 이해

새 랩을 설정 하기 전에 다음 질문을 고려해 야 합니다.

### <a name="what-software-requirements-does-the-class-have"></a>클래스에는 어떤 소프트웨어 요구 사항이 있나요?

클래스의 학습 목표에 따라 랩의 Vm에 설치 해야 하는 운영 체제, 응용 프로그램 및 도구를 결정 합니다. 랩 Vm을 설정 하려면 다음 세 가지 옵션을 사용할 수 있습니다.

- **Azure Marketplace 이미지 사용**: Azure Marketplace 랩을 만들 때 사용할 수 있는 수백 개의 이미지를 제공 합니다. 일부 클래스의 경우 이러한 이미지 중 하나에 클래스에 필요한 모든 항목이 이미 포함 되어 있을 수 있습니다.

- **새 사용자 지정 이미지 만들기**: Azure Marketplace 이미지를 시작 점으로 사용 하 여 사용자 지정 이미지를 만들고 추가 소프트웨어를 설치 하 고 구성을 변경 하 여 사용자 지정할 수 있습니다.

- **기존 사용자 지정 이미지 사용**: 이전에 만들었거나 학교에서 다른 관리자나 교직원이 만든 기존 사용자 지정 이미지를 다시 사용할 수 있습니다. 이렇게 하려면 관리자가 사용자 지정 이미지를 저장 하는 리포지토리로 공유 이미지 갤러리를 구성 해야 합니다.

> [!NOTE]
> 관리자는 Azure Marketplace 이미지와 사용자 지정 이미지를 사용 하도록 설정할 책임이 있습니다. 필요한 이미지를 사용할 수 있도록 IT 부서와 조정 합니다. 사용자가 만든 사용자 지정 이미지는 사용자가 소유한 랩 내에서 자동으로 사용 하도록 설정 됩니다.

### <a name="what-hardware-requirements-does-the-class-have"></a>클래스의 하드웨어 요구 사항은 무엇 인가요?

선택할 수 있는 다양 한 계산 크기는 다음과 같습니다.

- 중첩 된 가상화 크기를 사용 하 여 여러 중첩 된 Vm을 호스트할 수 있는 VM에 학생에 대 한 액세스를 제공할 수 있습니다. 예를 들어 네트워킹 과정에이 계산 크기를 사용할 수 있습니다.

- 학생 들이 컴퓨터 집약적 응용 프로그램 형식을 사용할 수 있도록 GPU 크기. 예를 들어 이러한 선택은 인공 지능 및 기계 학습에 적합 합니다.

사용 가능한 계산 크기의 전체 목록은 [VM 크기 조정](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) 가이드를 참조 하세요.

> [!NOTE]
> 랩의 지역에 따라 사용 가능한 계산 크기를 줄일 수 있습니다 .이는 지역에 따라 다릅니다. 일반적으로 사용자의 요구에 가장 가까운 가장 작은 계산 크기를 선택 해야 합니다. Azure Lab Services를 사용 하면 필요한 경우 나중에 다른 계산 용량을 사용 하 여 새 랩을 설정할 수 있습니다.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>외부 Azure 또는 네트워크 리소스에 대 한 클래스의 종속성은 무엇 인가요?

랩 Vm에서 데이터베이스, 파일 공유 또는 라이선스 서버와 같은 외부 리소스를 사용 해야 하는 경우 관리자와 협력 하 여 랩에서 이러한 리소스에 액세스할 수 있도록 해야 합니다.

가상 네트워크로 보호 *되지* 않는 Azure 리소스에 대 한 액세스의 경우 관리자가 추가 구성을 검색할 필요가 없습니다. 공용 인터넷을 통해 이러한 리소스에 액세스할 수 있습니다.

> [!NOTE]
> VM에서 직접 리소스를 제공 하 여 랩의 외부 리소스에 대 한 종속성을 줄일 수 있는지 여부를 고려해 야 합니다. 예를 들어 외부 데이터베이스에서 데이터를 읽을 필요가 없도록 하려면 VM에 직접 데이터베이스를 설치 하면 됩니다.  

### <a name="how-will-costs-be-controlled"></a>비용은 어떻게 제어할 수 있나요?

Lab Services는 종 량 제 가격 책정 모델을 사용 합니다. 즉, 랩 VM이 실행 되는 시간에 대해서만 비용을 지불 합니다. 비용을 제어 하기 위해 일반적으로 서로 함께 사용 되는 세 가지 옵션이 있습니다.

- **일정**: 일정에 따라 랩의 vm이 시작 되 고 종료 되는 시간을 자동으로 제어할 수 있습니다.
- **할당량**: 할당량은 학생이 예약 된 시간 외에 VM에 대 한 액세스 권한을 보유 하는 시간을 제어 합니다. 학생 들이 사용 하는 동안 할당량에 도달 하면 VM이 자동으로 종료 됩니다. 할당량이 증가 하지 않으면 학생은 VM을 다시 시작할 수 없습니다.
- **자동 종료**: 사용 하도록 설정 하면 학생이 RDP (원격 데스크톱 프로토콜) 세션에서 연결을 끊은 후 자동 종료 설정이 일정 기간 후에 Windows vm이 자동으로 종료 되도록 합니다. 이 설정은 기본적으로 사용하지 않도록 설정됩니다.  

    > [!NOTE]
    > 이 설정은 현재 Windows에 대해서만 존재 합니다.

### <a name="how-will-students-save-their-work"></a>학생 들은 어떻게 작업을 저장 하나요?

학생에 게는 랩의 수명 동안 할당 된 고유한 VM이 각각 할당 됩니다. 다음을 선택할 수 있습니다.

- VM에 직접 저장 합니다.
- OneDrive 또는 GitHub와 같은 외부 위치에 저장 합니다.

랩 Vm에서 학생을 위해 OneDrive를 자동으로 구성할 수 있습니다.

> [!NOTE]
> 학생 들이 랩 외부에 저장 된 작업에 계속 액세스할 수 있도록 하 고 클래스가 종료 된 후에는 학생 들이 외부 리포지토리에 작업을 저장 하는 것이 좋습니다.

### <a name="how-will-students-connect-to-their-vm"></a>학생 들이 자신의 VM에 어떻게 연결 하나요?

Windows Vm에 대 한 RDP의 경우 학생 들이 [Microsoft 원격 데스크톱 클라이언트](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)를 사용 하는 것이 좋습니다. 원격 데스크톱 클라이언트는 Mac, Chromebooks 및 Windows를 지원 합니다.

Linux Vm의 경우 학생은 SSH 또는 RDP 중 하나를 사용할 수 있습니다. 학생이 RDP를 사용 하 여 연결 하도록 하려면 필요한 RDP 및 GUI 패키지를 설치 하 고 구성 해야 합니다.

## <a name="set-up-your-lab"></a>랩 설정

클래스 랩에 대 한 요구 사항을 파악 한 후에는 설정할 준비가 된 것입니다. 랩을 설정 하는 방법을 보려면이 섹션의 링크를 따르세요.

1. **랩을 만듭니다.** 지침에 대 한 [교실 랩 만들기](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) 에 대 한 자습서를 참조 하세요.

    > [!NOTE]
    > 클래스에 중첩 된 가상화가 필요한 경우 [중첩 된 가상화 사용](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)의 단계를 참조 하세요.

1. **이미지를 사용자 지정 하 고 랩 Vm을 게시 합니다.** 템플릿 VM 이라는 특수 한 VM에 연결 합니다. 다음 가이드의 단계를 참조 하세요.
    - [템플릿 VM 만들기 및 관리](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [공유 이미지 갤러리 사용](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Windows를 사용 하는 경우 [windows 템플릿 VM 준비](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template)의 지침도 참조 해야 합니다. 이러한 지침에는 학생 들이 사용할 수 있도록 OneDrive 및 Office를 설정 하는 단계가 포함 되어 있습니다.

1. **VM 풀 및 용량을 관리 합니다.** 클래스에 필요한 만큼 VM 용량을 쉽게 확장 하거나 축소할 수 있습니다. VM 용량을 늘리려면 새 Vm을 설정 하는 작업이 포함 되므로 몇 시간이 걸릴 수 있습니다. [VM 풀 설정 및 관리](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)의 단계를 참조 하세요.

1. **랩 사용자를 추가 하 고 관리 합니다.** 랩에 사용자를 추가 하려면 다음 자습서의 단계를 참조 하세요.
   - [랩에 사용자 추가](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [사용자에 게 초대 보내기](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    학생 들이 사용할 수 있는 계정 유형에 대 한 자세한 내용은 [학생 계정](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)을 참조 하세요.
  
1. **비용 제어를 설정 합니다.** 랩의 비용을 제어 하려면 일정, 할당량 및 자동 종료를 설정 합니다. 다음 자습서를 참조하세요.

   - [일정 설정](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > 설치한 운영 체제의 유형에 따라 VM을 시작 하는 데 몇 분 정도 걸릴 수 있습니다. 예약 된 시간 동안 랩 VM을 사용할 준비가 되었는지 확인 하려면 30 분 전에 Vm을 시작 하는 것이 좋습니다.

   - [사용자에 대 한 할당량 설정](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) 및 [특정 사용자에 대 한 추가 할당량 설정](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [연결 해제 시 자동 종료 사용](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > 일정, 할당량 및 자동 종료는 템플릿 VM에 적용 되지 않습니다. 따라서 템플릿 VM을 사용 하지 않을 때는 종료 해야 합니다. 그렇지 않으면 계속 비용이 발생 합니다. 또한 기본적으로 랩을 만들 때 템플릿 VM이 자동으로 시작 됩니다. 랩 설정이 즉시 완료 되었는지 확인 하 고 템플릿 VM을 종료 합니다.

1. **대시보드를 사용 합니다.** 지침은 [랩의 대시보드 사용](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)을 참조 하세요.

    > [!NOTE]
    > 대시보드에 표시 되는 예상 비용은 랩의 학생 사용에 대해 예상할 수 있는 최대 비용입니다. 예를 들어 학생 들이 사용 하지 않은 할당량 시간에 대 한 요금이 청구 *되지* 않습니다. 예상 비용은 템플릿 VM 또는 공유 이미지 갤러리 사용에 대 한 요금을 반영 *하지 않습니다* .

## <a name="next-steps"></a>다음 단계

- [클래스룸 랩의 사용량 추적](tutorial-track-usage.md)
  
- [클래스룸 랩 액세스](tutorial-connect-virtual-machine-classroom-lab.md)
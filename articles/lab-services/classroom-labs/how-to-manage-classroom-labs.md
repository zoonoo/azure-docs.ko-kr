---
title: Azure Lab Services의 클래스룸 랩 관리 | Microsoft Docs
description: 클래스룸 랩을 만들고 구성하거나, 모든 클래스룸 랩을 보거나, 랩 사용자와 등록 링크를 공유하거나 또는 랩을 삭제하는 방법에 대해 알아봅니다.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 85a3a9f7afac8250b225d42462f6b29042e34a2a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330415"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Azure Lab Services에서 클래스룸 랩 관리 
이 문서에서는 클래스룸 랩을 만들고 삭제하는 방법을 설명합니다. 또한 랩 계정에서 모든 클래스룸 랩을 보는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>전제 조건
랩 계정에서 클래스룸 랩을 설정하려면 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 랩 소유자는 [랩 작성자 역할에 사용자 추가](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) 문서의 단계에 따라 랩 작성자 역할에 다른 사용자를 추가할 수 있습니다.

## <a name="create-a-classroom-lab"></a>클래스룸 랩 만들기

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다. Internet Explorer 11은 아직 지원되지 않습니다. 
2. **로그인**을 선택하고 자격 증명을 입력합니다. 랩 계정에서 **랩 작성자** 역할의 멤버인 **사용자 ID**를 선택하거나 입력하고 암호를 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
3. **새 랩**을 선택 합니다. 
    
    ![클래스룸 랩 만들기](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. **새 랩** 창에서 다음 작업을 수행합니다. 
    1. 랩에 대한 **이름**을 지정합니다. 
    2. 클래스에 필요한 **가상 컴퓨터의 크기** 를 선택 합니다. 사용 가능한 크기 목록은 [VM 크기](#vm-sizes) 섹션을 참조 하세요. 
    3. 클래스 룸 랩에 사용 하려는 **가상 머신 이미지** 를 선택 합니다. Linux 이미지를 선택하면, 이에 대한 원격 데스크톱 연결을 사용하도록 설정하는 옵션이 표시됩니다. 자세한 내용은 [Linux에 대한 원격 데스크톱 연결을 사용하도록 설정](how-to-enable-remote-desktop-linux.md)을 참조하세요.
    4. 페이지에 표시 된 **시간당 총 가격** 을 확인 합니다. 
    6. **저장**을 선택합니다.

        ![새 랩 창](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **가상 컴퓨터 자격 증명** 페이지에서 랩의 모든 vm에 대 한 기본 자격 증명을 지정 합니다.
    1. 랩의 모든 VM에 대한 **사용자 이름**을 지정합니다.
    2. 사용자에 대한 **암호**를 지정합니다. 

        > [!IMPORTANT]
        > 사용자 이름과 암호를 적어 둡니다. 다시 표시되지 않습니다.
    3. 학생 들이 자신의 암호를 설정 하도록 하려면 **모든 가상 머신에 동일한 암호 사용** 옵션을 사용 하지 않도록 설정 합니다. 이 단계는 **선택 사항**입니다. 

        교사는 랩의 모든 Vm에 대해 동일한 암호를 사용 하도록 선택 하거나, 학생 들이 자신의 Vm에 대 한 암호를 설정 하도록 할 수 있습니다. 기본적으로이 설정은 Ubuntu를 제외한 모든 Windows 및 Linux 이미지에 대해 사용 하도록 설정 됩니다. **Ubuntu** VM을 선택 하는 경우이 설정을 사용 하지 않도록 설정 하므로 학생에 게 처음 로그인 할 때 암호를 설정 하 라는 메시지가 표시 됩니다.  

        ![새 랩 창](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
        > [!IMPORTANT]
        > 사용자 이름과 암호를 적어 둡니다. 다시 표시되지 않습니다.    
    4. 그런 다음 **가상 머신 자격 증명** 페이지에서 **다음** 을 선택 합니다. 
5. **랩 정책** 페이지에서 각 사용자에 게 할당 된 시간 (**각 사용자에 대 한 할당량**)을 랩의 예약 된 시간 외부에 입력 한 다음 **마침**을 선택 합니다. 

    ![각 사용자에 대 한 할당량](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. 템플릿 VM 만들기의 상태를 보여 주는 다음 화면이 표시 됩니다. 랩에서 템플릿을 만드는 데는 최대 20분이 걸립니다. 

    ![템플릿 VM 만들기 상태](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. **템플릿** 페이지에서 다음 단계를 수행 합니다 .이 단계는 자습서에서 **선택 사항** 입니다.

    2. **연결**을 선택하여 템플릿 VM에 연결합니다. Linux 템플릿 VM인 경우, SSH 또는 RDP(RDP가 활성화된 경우) 중 무엇을 사용하여 연결할지 선택합니다.
    1. **암호 재설정**을 선택하여 VM에 대한 암호를 재설정합니다. 
    1. 템플릿 VM에 소프트웨어를 설치하고 구성합니다. 
    1. VM을 **중지**합니다.  
    1. 템플릿에 대한 **설명**을 입력합니다.
10. **템플릿** 페이지의 도구 모음에서 **게시** 를 선택 합니다. 

    ![게시 템플릿 단추](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 게시한 후에는 게시를 취소할 수 없습니다. 
8. **게시 템플릿** 페이지에서 랩에서 만들 가상 머신의 수를 입력 한 다음 **게시**를 선택 합니다. 

    ![게시 템플릿-Vm 수](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. 페이지에 템플릿 **게시 상태가** 표시 됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다. 

    ![템플릿 게시 - 진행률](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 왼쪽 메뉴에서 가상 컴퓨터를 선택 하거나 가상 컴퓨터 타일을 선택 하 여 **가상 컴퓨터 풀** 페이지로 전환 합니다. **할당되지 않음** 상태인 가상 머신이 보이는지 확인합니다. 이러한 VM은 아직 학생들에게 할당되지 않았습니다. **중지됨** 상태일 것입니다. 이 페이지에서 학생 VM을 시작하고, VM에 연결하고, VM을 중지하고, VM을 삭제할 수 있습니다. 이 페이지에서 VM을 시작할 수도 있고, 학생들이 VM을 시작하게 할 수도 있습니다. 

    ![중지됨 상태의 가상 머신](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    이 페이지에서 다음 작업을 수행 합니다. 자습서에서는이 단계를 수행 하지 않습니다. 이러한 단계는 정보를 위한 것입니다.): 
    
    1. 랩 용량 (랩의 Vm 수)을 변경 하려면 도구 모음에서 **랩 용량** 을 선택 합니다.
    2. 모든 Vm을 한 번에 시작 하려면 도구 모음에서 **모두 시작** 을 선택 합니다. 
    3. 특정 VM을 시작 하려면 **상태**에서 아래쪽 화살표를 선택한 다음 **시작**을 선택 합니다. 첫 번째 열에서 VM을 선택한 다음 도구 모음에서 **시작** 을 선택 하 여 vm을 시작할 수도 있습니다.                

### <a name="vm-sizes"></a>VM 크기  

| 크기 | 코어 수 | RAM | 설명 | 
| ---- | ----- | --- | ----------- | 
| 작음 | 2 | 3.5 GB | 이 크기는 명령줄, 웹 브라우저 열기, 트래픽이 적은 웹 서버, 중소 규모의 데이터베이스에 가장 적합 합니다. |
| 중간 | 4 | 7 GB | 이 크기는 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합 합니다. | 
| 중간 (중첩 된 가상화) | 4 | 16GB | 이 크기는 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합 합니다. 이 크기는 중첩 된 가상화도 지원 합니다. <p>이 크기는 각 학생이 여러 Vm이 필요한 시나리오에서 사용할 수 있습니다. 교사는 중첩 된 가상화를 사용 하 여 가상 컴퓨터 내에서 작은 크기의 중첩 된 가상 컴퓨터를 설정할 수 있습니다. </p> |
| 큼 | 8 | 32GB | 이 크기는 더 빠른 Cpu, 더 뛰어난 로컬 디스크 성능, 큰 데이터베이스, 큰 메모리 캐시를 필요로 하는 응용 프로그램에 가장 적합 합니다. 이 크기는 또한 중첩 된 가상화를 지원 합니다. |  
| 소형 GPU (시각화) | 6 | 56GB | 이 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 사용 하는 원격 시각화, 스트리밍, 게임, 인코딩에 가장 적합 합니다. | 
| 소형 GPU (계산) | 6 | 56GB | 이 크기는 인공 지능 및 심층 학습 응용 프로그램과 같은 계산 집약적 및 네트워크 집약적 응용 프로그램에 가장 적합 합니다. | 
| 중형 GPU (시각화) | 12 | 112GB | 이 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 사용 하는 원격 시각화, 스트리밍, 게임, 인코딩에 가장 적합 합니다. | 

## <a name="view-all-classroom-labs"></a>모든 클래스룸 랩 보기
1. [Azure Lab Services Portal](https://labs.azure.com)로 이동합니다.
2. **로그인**을 선택합니다. 랩 계정에서 **랩 작성자** 역할의 멤버인 **사용자 ID**를 선택하거나 입력하고 암호를 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
3. 선택한 랩 계정에 있는 모든 랩이 표시되는지 확인합니다. 랩의 타일에는 랩의 가상 컴퓨터 수와 각 사용자에 대 한 할당량 (예약 된 시간 외)이 표시 됩니다.

    ![모든 랩](../media/how-to-manage-classroom-labs/all-labs.png)
3. 상단의 드롭 다운 목록을 사용하여 다른 랩 계정을 선택합니다. 선택한 랩 계정에 랩이 표시됩니다. 

## <a name="delete-a-classroom-lab"></a>클래스룸 랩 삭제
1. 랩의 타일에서 모퉁이에 있는 점 세 개 (...)를 선택 하 고 **삭제**를 선택 합니다. 

    ![삭제 단추](../media/how-to-manage-classroom-labs/delete-button.png)
3. **랩 삭제** 대화 상자에서 **삭제** 를 선택 하 여 삭제를 계속 합니다. 

## <a name="switch-to-another-classroom-lab"></a>다른 클래스룸 랩으로 전환
현재 클래스룸 랩에서 다른 클래스룸 랩으로 전환하려면 맨 위에 있는 랩 계정에서 랩 드롭다운 목록을 선택합니다.

![맨 위에 있는 드롭다운 목록에서 랩을 선택합니다.](../media/how-to-manage-classroom-labs/switch-lab.png)

이 드롭다운 목록에서 **새 랩을** 사용 하 여 새 랩을 만들 수도 있습니다. 


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)


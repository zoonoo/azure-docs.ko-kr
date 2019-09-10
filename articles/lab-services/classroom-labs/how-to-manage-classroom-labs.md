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
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 1f9cb82abd5bc0823f5e7bc23fe437007bccc8e0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873586"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Azure Lab Services에서 클래스룸 랩 관리 
이 문서에서는 클래스룸 랩을 만들고 삭제하는 방법을 설명합니다. 또한 랩 계정에서 모든 클래스룸 랩을 보는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 구성 요소
랩 계정에서 클래스룸 랩을 설정하려면 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 랩 소유자는 다음 문서의 단계에 따라 랩 작성자 역할에 다른 사용자를 추가할 수 있습니다. [랩 작성자 역할에 사용자 추가](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)

## <a name="create-a-classroom-lab"></a>클래스룸 랩 만들기

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다. Internet Explorer 11은 아직 지원되지 않습니다. 
2. **로그인**을 선택합니다. 랩 계정에서 **랩 작성자** 역할의 멤버인 **사용자 ID**를 선택하거나 입력하고 암호를 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
3. **새 랩** 창에서 다음 작업을 수행합니다. 
    1. 랩에 대한 **이름**을 지정합니다. 
    2. 랩에서 **가상 머신의 최대 수**를 지정합니다. 나중에 랩에서 가상 머신의 수를 늘리거나 줄일 수 있습니다. 
    6. **저장**을 선택합니다.

        ![클래스룸 랩 만들기](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **가상 머신 사양 선택** 페이지에서 다음 단계를 수행합니다.
    1. 랩에서 만드는 VM(가상 머신)의 **크기**를 선택합니다. 현재, **소형**, **중형**, **중형(가상화)** , **대형** 및 **GPU** 크기를 사용할 수 있습니다. 자세한 내용은 [VM 크기](#vm-sizes) 섹션을 참조 하세요.
    1. VM을 만들려는 **지역**을 선택합니다. 
    1. 랩에서 VM을 만드는 데 사용할 **VM 이미지**를 선택합니다. Linux 이미지를 선택하면, 이에 대한 원격 데스크톱 연결을 사용하도록 설정하는 옵션이 표시됩니다. 자세한 내용은 [Linux에 대한 원격 데스크톱 연결을 사용하도록 설정](how-to-enable-remote-desktop-linux.md)을 참조하세요.
    1. **다음**을 선택합니다.

        ![VM 사양 지정](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. **자격 증명 설정** 페이지에서 랩의 모든 VM에 대한 기본 자격 증명을 지정합니다. 
    1. 랩의 모든 VM에 대한 **사용자 이름**을 지정합니다.
    2. 사용자에 대한 **암호**를 지정합니다. 

        > [!IMPORTANT]
        > 사용자 이름과 암호를 적어 둡니다. 다시 표시되지 않습니다.
    3. 학생 들이 자신의 암호를 설정 하도록 하려면 **모든 가상 머신에 동일한 암호 사용** 옵션을 사용 하지 않도록 설정 합니다. 이 단계는 **선택 사항**입니다. 

        교사는 랩의 모든 Vm에 대해 동일한 암호를 사용 하도록 선택 하거나, 학생 들이 자신의 Vm에 대 한 암호를 설정 하도록 할 수 있습니다. 기본적으로이 설정은 Ubuntu를 제외한 모든 Windows 및 Linux 이미지에 대해 사용 하도록 설정 됩니다. **Ubuntu** VM을 선택 하는 경우이 설정을 사용 하지 않도록 설정 하므로 학생에 게 처음 로그인 할 때 암호를 설정 하 라는 메시지가 표시 됩니다.
    1. **만들기**를 선택합니다. 

        ![자격 증명 설정](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. **템플릿 구성** 페이지에 랩 만들기 프로세스의 상태가 표시됩니다. 랩에서 템플릿을 만드는 데는 최대 20분이 걸립니다. 랩의 템플릿은 모든 사용자의 가상 머신이 만들어지는 기본 가상 머신 이미지입니다. 랩 사용자에게 제공하려는 구성과 정확히 일치하도록 템플릿 가상 머신을 설정합니다.  

    ![템플릿 구성](../media/tutorial-setup-classroom-lab/configure-template.png)
7. 템플릿 구성이 완료되면 다음 페이지가 표시됩니다. 

    ![완료된 템플릿 페이지 구성](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. 다음 단계는 이 자습서의 선택적 단계입니다. 
    2. **연결**을 선택하여 템플릿 VM에 연결합니다. Linux 템플릿 VM인 경우, SSH 또는 RDP(RDP가 활성화된 경우) 중 무엇을 사용하여 연결할지 선택합니다.
    1. **암호 재설정**을 선택하여 VM에 대한 암호를 재설정합니다. 
    1. 템플릿 VM에 소프트웨어를 설치하고 구성합니다. 
    1. VM을 **중지**합니다.  
    1. 템플릿에 대한 **설명**을 입력합니다.
9. 템플릿 페이지에서 **다음**을 선택합니다. 
10. **템플릿 게시** 페이지에서 다음 작업을 수행합니다. 
    1. 템플릿을 즉시 게시하려면 *게시한 후에는 템플릿을 수정할 수 없습니다. 이 프로세스는 한 번만 수행할 수 있으며 최대 1시간이 걸릴 수 있습니다.* 확인란을 선택하고, **게시**를 선택합니다.  랩 사용자가 템플릿 VM의 인스턴스를 사용할 수 있도록 템플릿을 게시합니다.

        > [!WARNING]
        > 게시한 후에는 게시를 취소할 수 없습니다. 
    2. 나중에 게시하려면 **나중을 위해 저장**을 선택합니다. 마법사가 완료되면 템플릿 VM을 게시할 수 있습니다. 마법사가 완료된 후에 구성과 게시를 수행하는 방법에 대한 자세한 내용은 [클래스룸 랩을 관리하는 방법](how-to-manage-classroom-labs.md) 문서의 템플릿 게시 섹션을 참조하세요.

        ![템플릿 게시](../media/tutorial-setup-classroom-lab/publish-template.png)
11. 템플릿 **게시 진행률**이 표시됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다. 

    ![템플릿 게시 - 진행률](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 템플릿이 성공적으로 게시되면 다음 페이지가 표시됩니다. **완료** 를 선택합니다.

    ![템플릿 게시 - 성공](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 랩의 **대시보드**가 표시됩니다. 
    
    ![클래스룸 랩 대시보드](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. **가상 머신** 페이지로 전환하고, **할당되지 않음** 상태인 가상 머신이 보이는지 확인합니다. 이러한 VM은 아직 학생들에게 할당되지 않았습니다. **중지됨** 상태일 것입니다. 이 페이지에서 학생 VM을 시작하고, VM에 연결하고, VM을 중지하고, VM을 삭제할 수 있습니다. 이 페이지에서 VM을 시작할 수도 있고, 학생들이 VM을 시작하게 할 수도 있습니다. 

    ![중지됨 상태의 가상 머신](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>VM 크기  

| Size | 코어 | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| 소형 | 2 | 3.5 GB | 이 크기는 명령줄, 웹 브라우저 열기, 트래픽이 적은 웹 서버, 중소 규모의 데이터베이스에 가장 적합 합니다. |
| 보통 | 4 | 7 GB | 이 크기는 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합 합니다. | 
| 중간 (중첩 된 가상화) | 4 | 16GB | 이 크기는 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합 합니다. 이 크기는 중첩 된 가상화도 지원 합니다. <p>이 크기는 각 학생이 여러 Vm이 필요한 시나리오에서 사용할 수 있습니다. 교사는 중첩 된 가상화를 사용 하 여 가상 컴퓨터 내에서 작은 크기의 중첩 된 가상 컴퓨터를 설정할 수 있습니다. </p> |
| 대형 | 8 | 32GB | 이 크기는 더 빠른 Cpu, 더 뛰어난 로컬 디스크 성능, 큰 데이터베이스, 큰 메모리 캐시를 필요로 하는 응용 프로그램에 가장 적합 합니다. 이 크기는 또한 중첩 된 가상화를 지원 합니다. |  
| 소형 GPU (시각화) | 6 | 56GB | 이 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 사용 하는 원격 시각화, 스트리밍, 게임, 인코딩에 가장 적합 합니다. | 
| 소형 GPU (계산) | 6 | 56GB | 이 크기는 인공 지능 및 심층 학습 응용 프로그램과 같은 계산 집약적 및 네트워크 집약적 응용 프로그램에 가장 적합 합니다. | 
| 중형 GPU (시각화) | 12 | 112GB | 이 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 사용 하는 원격 시각화, 스트리밍, 게임, 인코딩에 가장 적합 합니다. | 

## <a name="view-all-classroom-labs"></a>모든 클래스룸 랩 보기
1. [Azure Lab Services Portal](https://labs.azure.com)로 이동합니다.
2. **로그인**을 선택합니다. 랩 계정에서 **랩 작성자** 역할의 멤버인 **사용자 ID**를 선택하거나 입력하고 암호를 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
3. 선택한 랩 계정에 있는 모든 랩이 표시되는지 확인합니다. 

    ![모든 랩](../media/how-to-manage-classroom-labs/all-labs.png)
3. 상단의 드롭 다운 목록을 사용하여 다른 랩 계정을 선택합니다. 선택한 랩 계정에 랩이 표시됩니다. 

## <a name="delete-a-classroom-lab"></a>클래스룸 랩 삭제
1. 랩에 대한 타일에서 구석에 있는 3 개의 점(...)을 선택합니다. 

    ![랩 선택](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. **삭제**를 선택합니다. 

    ![삭제 단추](../media/how-to-manage-classroom-labs/delete-button.png)
3. **랩 삭제** 대화 상자에서 **삭제**를 선택합니다. 

    ![대화 상자 삭제](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>다른 클래스룸 랩으로 전환
현재 클래스룸 랩에서 다른 클래스룸 랩으로 전환하려면 맨 위에 있는 랩 계정에서 랩 드롭다운 목록을 선택합니다.

![맨 위에 있는 드롭다운 목록에서 랩을 선택합니다.](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)


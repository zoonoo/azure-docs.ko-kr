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
ms.topic: how-to
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 46c53c99c12ade986ab913bf013b652a931a4d22
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257745"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Azure Lab Services에서 클래스룸 랩 관리 
이 문서에서는 클래스룸 랩을 만들고 삭제하는 방법을 설명합니다. 또한 랩 계정에서 모든 클래스룸 랩을 보는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>사전 요구 사항
랩 계정에서 클래스룸 랩을 설정하려면 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 랩 소유자는 [랩 작성자 역할에 사용자 추가](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) 문서의 단계에 따라 랩 작성자 역할에 다른 사용자를 추가할 수 있습니다.

## <a name="create-a-classroom-lab"></a>클래스룸 랩 만들기

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다. 인터넷 익스플로러 11은 아직 지원되지 않습니다. 
2. **로그인**을 선택하고 자격 증명을 입력합니다. 랩 계정에서 **랩 작성자** 역할의 멤버인 **사용자 ID**를 선택하거나 입력하고 암호를 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
3. **새 랩**을 선택합니다. 
    
    ![클래스룸 랩 만들기](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. **새 랩** 창에서 다음 작업을 수행합니다. 
    1. 랩에 대한 **이름**을 지정합니다. 
    2. 클래스에 필요한 **가상 시스템의 크기를** 선택합니다. 사용 가능한 크기 목록은 [VM 크기](#vm-sizes) 섹션을 참조하십시오. 
    3. 교실 랩에 사용할 **가상 컴퓨터 이미지를** 선택합니다. Linux 이미지를 선택하면, 이에 대한 원격 데스크톱 연결을 사용하도록 설정하는 옵션이 표시됩니다. 자세한 내용은 [Linux에 대한 원격 데스크톱 연결을 사용하도록 설정](how-to-enable-remote-desktop-linux.md)을 참조하세요.

        랩 계정 소유자 자격 증명을 사용하여 로그인한 경우 랩에 더 많은 이미지를 사용하도록 설정하는 옵션이 표시됩니다. 자세한 내용은 [랩 생성 시 이미지 활성화를](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation)참조하십시오.
    4. 페이지에 표시된 **시간당 총 가격을** 검토합니다. 
    6. **저장**을 선택합니다.

        ![새 랩 창](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > [랩 작성자가 랩 위치](allow-lab-creator-pick-lab-location.md) 옵션을 선택할 수 있도록 랩 계정이 구성된 경우 랩의 위치를 선택하는 옵션이 표시됩니다. 
4. **가상 머신 자격 증명** 페이지에서 랩의 모든 VM에 대한 기본 자격 증명을 지정합니다.
    1. 랩의 모든 VM에 대한 **사용자 이름**을 지정합니다.
    2. 사용자에 대한 **암호**를 지정합니다. 

        > [!IMPORTANT]
        > 사용자 이름과 암호를 적어 둡니다. 다시 표시되지 않습니다.
    3. 학생이 자신의 암호를 설정하도록 하려면 **모든 가상 시스템에 대해 동일한 암호 사용을** 사용하지 않도록 설정합니다. 이 단계는 **선택 사항**입니다. 

        교사는 랩의 모든 VM에 대해 동일한 암호를 사용하거나 학생이 VM에 대한 암호를 설정할 수 있도록 허용할 수 있습니다. 기본적으로 이 설정은 우분투를 제외한 모든 Windows 및 Linux 이미지에서 사용할 수 있습니다. **우분투** VM을 선택하면 이 설정이 비활성화되므로 학생이 처음으로 로그인할 때 암호를 설정하라는 메시지가 표시됩니다.  

        ![새 랩 창](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. 그런 다음 가상 컴퓨터 자격 증명 페이지에서 **다음을** **선택합니다.** 
5. 랩 **정책** 페이지에서 다음 단계를 수행합니다.
    1. 랩의 예약된 시간 이외에 각 사용자에 대해 할당된**시간(각 사용자에 대한 할당량)을**입력합니다. 
    2. 가상 **컴퓨터자동 종료 옵션의** 경우 사용자가 연결을 끊을 때 VM을 자동으로 종료할지 여부를 지정합니다. VM이 자동으로 종료되기 전에 사용자가 다시 연결될 때까지 기다려야 하는 시간도 지정할 수 있습니다. 자세한 내용은 [연결을 끊을 때 VM의 자동 종료 를 활성화를](how-to-enable-shutdown-disconnect.md)참조하십시오.
    3. 그런 다음 **완료를 선택합니다.** 

        ![각 사용자에 대한 할당량](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. 템플릿 VM 만들기 상태를 보여 주는 다음 화면이 표시됩니다. 랩에서 템플릿을 만드는 데는 최대 20분이 걸립니다. 

    ![템플릿 VM 만들기 상태](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. **템플릿** 페이지에서 다음 단계를 수행합니다. **optional**

    2. **연결**을 선택하여 템플릿 VM에 연결합니다. Linux 템플릿 VM인 경우, SSH 또는 RDP(RDP가 활성화된 경우) 중 무엇을 사용하여 연결할지 선택합니다.
    1. **암호 재설정**을 선택하여 VM에 대한 암호를 재설정합니다. 
    1. 템플릿 VM에 소프트웨어를 설치하고 구성합니다. 
    1. VM을 **중지합니다.**  
    1. 템플릿에 대한 **설명**을 입력합니다.
10. **템플릿** 페이지의 도구 모음에서 **게시**를 선택합니다. 

    ![템플릿 게시 단추](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 게시한 후에는 게시를 취소할 수 없습니다. 
8. **템플릿 게시** 페이지에서 랩에서 만들려는 가상 머신의 수를 입력한 다음, **게시**를 선택합니다. 

    ![템플릿 게시 - VM 수](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. 템플릿 **게시 상태**가 페이지에 표시됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다. 

    ![템플릿 게시 - 진행률](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 왼쪽 메뉴에서 가상 머신을 선택하거나 가상 머신 타일을 선택하여 **가상 머신 풀** 페이지로 전환합니다. **할당되지 않음** 상태인 가상 머신이 보이는지 확인합니다. 이러한 VM은 아직 학생들에게 할당되지 않았습니다. **중지됨** 상태일 것입니다. 이 페이지에서 학생 VM을 시작하고, VM에 연결하고, VM을 중지하고, VM을 삭제할 수 있습니다. 이 페이지에서 VM을 시작할 수도 있고, 학생들이 VM을 시작하게 할 수도 있습니다. 

    ![중지됨 상태의 가상 머신](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    이 페이지에서 수행하는 작업은 다음과 같습니다. (이러한 단계는 자습서에서 정보용으로만 제공되므로 수행하지 마세요.) 
    
    1. 랩 용량(랩의 VM 수)을 변경하려면 도구 모음에서 **랩 용량**을 선택합니다.
    2. 모든 VM을 한 번에 시작하려면 도구 모음에서 **모두 시작**을 선택합니다. 
    3. 특정 VM을 시작하려면 **상태**에서 아래쪽 화살표를 선택한 다음, **시작**을 선택합니다. 또한 첫 번째 열에서 VM을 선택한 다음, 도구 모음에서 **시작**을 선택하여 VM을 시작할 수도 있습니다.                

### <a name="vm-sizes"></a>VM 크기  

| 크기 | 코어 수 | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| 작음 | 2 | 3.5 GB | 이 크기는 명령줄, 웹 브라우저 열기, 트래픽이 적은 웹 서버, 중소 규모 데이터베이스에 가장 적합합니다. |
| 중간 | 4 | 7 GB | 이 크기는 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합합니다. | 
| 중간(중첩 가상화) | 4 | 16GB | 이 크기는 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합합니다. 이 크기는 중첩된 가상화도 지원합니다. <p>이 크기는 각 학생에게 여러 VM이 필요한 시나리오에서 사용할 수 있습니다. 교사는 중첩 된 가상화를 사용하여 가상 시스템 내부에 몇 가지 작은 크기의 중첩 가상 시스템을 설정할 수 있습니다. </p> |
| 큰 | 8 | 32GB | 이 크기는 더 빠른 CPU, 더 나은 로컬 디스크 성능, 대규모 데이터베이스, 대용량 메모리 캐시가 필요한 응용 프로그램에 가장 적합합니다. 이 크기는 중첩 가상화도 지원합니다. |  
| 소형 GPU(시각화) | 6 | 56GB | 이 크기는 OpenGL 및 DirectX와 같은 프레임워크를 사용하여 원격 시각화, 스트리밍, 게임, 인코딩에 가장 적합합니다. | 
| 소형 GPU(컴퓨팅) | 6 | 56GB | 이 크기는 인공 지능 및 딥 러닝 응용 프로그램과 같은 컴퓨팅 집약적이고 네트워크 집약적인 응용 프로그램에 가장 적합합니다. | 
| 중간 GPU(시각화) | 12 | 112GB | 이 크기는 OpenGL 및 DirectX와 같은 프레임워크를 사용하여 원격 시각화, 스트리밍, 게임, 인코딩에 가장 적합합니다. | 

> [!NOTE]
> Azure Lab 서비스는 GPU 이미지가 있는 랩을 만들 때 필요한 GPU 드라이버를 자동으로 설치하고 구성합니다.  

## <a name="view-all-classroom-labs"></a>모든 클래스룸 랩 보기
1. [Azure Lab Services Portal](https://labs.azure.com)로 이동합니다.
2. **로그인**을 선택합니다. 랩 계정에서 **랩 작성자** 역할의 멤버인 **사용자 ID**를 선택하거나 입력하고 암호를 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
3. 선택한 랩 계정에 있는 모든 랩이 표시되는지 확인합니다. 랩 타일에는 랩의 가상 컴퓨터 수와 각 사용자에 대한 할당량(예약된 시간 이외)이 표시됩니다.

    ![모든 랩](../media/how-to-manage-classroom-labs/all-labs.png)
3. 상단의 드롭 다운 목록을 사용하여 다른 랩 계정을 선택합니다. 선택한 랩 계정에 랩이 표시됩니다. 

## <a name="delete-a-classroom-lab"></a>클래스룸 랩 삭제
1. 랩의 타일에서 모서리에 있는 세 개의 점(...)을 선택한 다음 **삭제를**선택합니다. 

    ![삭제 단추](../media/how-to-manage-classroom-labs/delete-button.png)
3. 랩 **삭제** 대화 상자에서 **삭제를** 선택하여 삭제를 계속합니다. 

## <a name="switch-to-another-classroom-lab"></a>다른 클래스룸 랩으로 전환
현재 클래스룸 랩에서 다른 클래스룸 랩으로 전환하려면 맨 위에 있는 랩 계정에서 랩 드롭다운 목록을 선택합니다.

![맨 위에 있는 드롭다운 목록에서 랩을 선택합니다.](../media/how-to-manage-classroom-labs/switch-lab.png)

이 드롭다운 목록에서 새 **랩을** 사용하여 새 랩을 만들 수도 있습니다. 

> [!NOTE]
> Az.LabServices PowerShell 모듈(미리 보기)을 사용하여 랩을 관리할 수도 있습니다. 자세한 내용은 [GitHub의 Az.LabServices 홈 페이지를](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)참조하십시오.

다른 랩 계정으로 전환하려면 랩 계정 옆의 드롭다운을 선택하고 다른 랩 계정을 선택합니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)


---
title: Azure Lab Services의 클래스룸 랩 관리 | Microsoft Docs
description: 클래스룸 랩을 만들고 구성하거나, 모든 클래스룸 랩을 보거나, 랩 사용자와 등록 링크를 공유하거나 또는 랩을 삭제하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: dd05b13a34e2be1a9d6bdc011a9aa24e79bc8dd1
ms.sourcegitcommit: 8ad5761333b53e85c8c4dabee40eaf497430db70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148374"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Azure Lab Services에서 클래스룸 랩 관리 
이 문서에서는 클래스룸 랩을 만들고 삭제하는 방법을 설명합니다. 또한 랩 계정에서 모든 클래스룸 랩을 보는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>사전 요구 사항
랩 계정에서 클래스룸 랩을 설정하려면 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 랩 소유자는 다음 문서의 단계에 따라 랩 작성자 역할에 다른 사용자를 추가할 수 있습니다. [랩 작성자 역할에 사용자 추가](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)

## <a name="create-a-classroom-lab"></a>클래스룸 랩 만들기

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다. Internet Explorer 11은 아직 지원되지 않습니다. 
1. **로그인** 을 선택하고 자격 증명을 입력합니다. 랩 계정에서 **랩 작성자** 역할의 멤버인 **사용자 ID** 를 선택하거나 입력하고 암호를 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
1. **새 랩** 을 선택합니다. 
    
    ![클래스룸 랩 만들기](./media/tutorial-setup-classroom-lab/new-lab-button.png)
1. **새 랩** 창에서 다음 작업을 수행합니다. 
    1. 랩에 대한 **이름** 을 지정합니다. 
    1. 클래스에 필요한 **가상 머신 크기** 를 선택합니다. 사용 가능한 크기 목록은 [VM 크기](#vm-sizes) 섹션을 참조하세요. 
    1. 클래스 룸 랩에서 사용하려는 **가상 머신 이미지** 를 선택합니다. Linux 이미지를 선택하면 **원격 데스크톱 연결 사용** 옵션이 표시됩니다. 자세한 내용은 [Linux에 대한 원격 데스크톱 연결을 사용하도록 설정](how-to-enable-remote-desktop-linux.md)을 참조하세요.

        랩 계정 소유자 자격 증명을 사용하여 로그인한 경우 랩에 더 많은 이미지를 사용하도록 설정하는 옵션이 표시됩니다. 자세한 내용은 [랩 생성 시 이미지 사용](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation)을 참조하세요.
    1. 페이지에 표시되는 **시간당 총 가격** 을 검토합니다. 
    1. **저장** 을 선택합니다.

        !["새 랩" 창을 보여 주는 스크린샷](./media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > 랩 계정에 [랩 작성자가 랩 위치를 선택하도록 허용](allow-lab-creator-pick-lab-location.md) 옵션이 구성된 경우 랩의 위치를 선택하는 옵션이 표시됩니다. 
4. **가상 머신 자격 증명** 페이지에서 랩의 모든 VM에 대한 기본 자격 증명을 지정합니다.
    1. 랩의 모든 VM에 대한 **사용자 이름** 을 지정합니다.
    2. 사용자에 대한 **암호** 를 지정합니다. 

        > [!IMPORTANT]
        > 사용자 이름과 암호를 적어 둡니다. 다시 표시되지 않습니다.
    3. 학생들이 자신의 암호를 설정하도록 하려면 **모든 가상 머신에 동일한 암호 사용** 옵션을 사용하지 않도록 설정합니다. 이 단계는 **선택 사항** 입니다. 

        교육자는 랩의 모든 VM에 동일한 암호를 사용하도록 선택하거나, 학생들이 VM의 암호를 설정하도록 할 수 있습니다. 기본적으로 이 설정은 Ubuntu를 제외한 모든 Windows 및 Linux 이미지에 사용하도록 설정됩니다. **Ubuntu** VM을 선택하는 경우 이 설정이 사용하지 않도록 설정되므로 학생들이 처음 로그인할 때 암호를 설정하라는 메시지가 표시됩니다.  

        ![새 랩 창](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. 그런 다음, **가상 머신 자격 증명** 페이지에서 **다음** 을 선택합니다. 
5. **랩 정책** 페이지에서 다음 단계를 수행합니다.
    1. 각 사용자에게 할당되는 시간( **각 사용자에 대한 할당량** )을 랩에 예약된 시간 이외의 시간으로 입력합니다. 
    2. **가상 머신 자동 종료** 옵션의 경우 사용자 연결이 끊길 때 VM이 자동으로 종료되도록 설정할지 여부를 지정합니다. VM이 자동으로 종료되기 전에 사용자가 다시 연결될 때까지 대기해야 하는 기간도 지정할 수 있습니다. 자세한 내용은 [연결 해제 시 VM 자동 종료 사용](how-to-enable-shutdown-disconnect.md)을 참조하세요.
    3. 그런 다음, **마침** 을 선택합니다. 

        ![각 사용자에 대한 할당량](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. 템플릿 VM 만들기 상태를 보여 주는 다음 화면이 표시됩니다. 랩에서 템플릿을 만드는 데는 최대 20분이 걸립니다. 

    ![템플릿 VM 만들기 상태](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. **템플릿** 페이지에서 다음 단계를 수행합니다. 이 자습서에서는 이러한 단계가 **선택 사항** 입니다.

    1. **연결** 을 선택하여 템플릿 VM에 연결합니다. Linux 템플릿 VM인 경우 SSH 또는 GUI 원격 데스크톱을 사용하여 연결할지 여부를 선택합니다.  GUI 원격 데스크톱을 사용하려면 추가 설정이 필요합니다. 자세한 내용은 [Linux 가상 머신에 그래픽 원격 데스크톱 사용](how-to-use-remote-desktop-linux-student.md)을 참조하세요.
    1. **암호 재설정** 을 선택하여 VM에 대한 암호를 재설정합니다. 
    1. 템플릿 VM에 소프트웨어를 설치하고 구성합니다. 
    1. VM을 **중지** 합니다.  
    1. 템플릿에 대한 **설명** 을 입력합니다.
9.  **템플릿** 페이지의 도구 모음에서 **게시** 를 선택합니다. 

    ![템플릿 게시 단추](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 게시한 후에는 게시를 취소할 수 없습니다. 
10. **템플릿 게시** 페이지에서 랩에서 만들려는 가상 머신의 수를 입력한 다음, **게시** 를 선택합니다. 

    ![템플릿 게시 - VM 수](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. 템플릿 **게시 상태** 가 페이지에 표시됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다. 

    ![템플릿 게시 - 진행률](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 왼쪽 메뉴에서 가상 머신을 선택하거나 가상 머신 타일을 선택하여 **가상 머신 풀** 페이지로 전환합니다. **할당되지 않음** 상태인 가상 머신이 보이는지 확인합니다. 이러한 VM은 아직 학생들에게 할당되지 않았습니다. **중지됨** 상태일 것입니다. 이 페이지에서 학생 VM을 시작하고, VM에 연결하고, VM을 중지하고, VM을 삭제할 수 있습니다. 이 페이지에서 VM을 시작할 수도 있고, 학생들이 VM을 시작하게 할 수도 있습니다. 

    ![중지됨 상태의 가상 머신](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    이 페이지에서 수행하는 작업은 다음과 같습니다. (이러한 단계는 자습서에서 정보용으로만 제공되므로 수행하지 마세요.) 
    
    1. 랩 용량(랩의 VM 수)을 변경하려면 도구 모음에서 **랩 용량** 을 선택합니다.
    2. 모든 VM을 한 번에 시작하려면 도구 모음에서 **모두 시작** 을 선택합니다. 
    3. 특정 VM을 시작하려면 **상태** 에서 아래쪽 화살표를 선택한 다음, **시작** 을 선택합니다. 또한 첫 번째 열에서 VM을 선택한 다음, 도구 모음에서 **시작** 을 선택하여 VM을 시작할 수도 있습니다.                

### <a name="vm-sizes"></a>VM 크기  

| 크기 | 코어 수 | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| 작음 | 2 | 3.5 GB | 이 크기는 명령줄, 웹 브라우저 열기, 낮은 트래픽 웹 서버, 중소규모 데이터베이스에 가장 적합합니다. |
| 중간 | 4 | 7 GB | 이 크기는 관계형 데이터베이스, 메모리 내 캐시 및 분석에 가장 적합합니다. | 
| 중간(중첩된 가상화) | 4 | 16GB | 이 크기는 관계형 데이터베이스, 메모리 내 캐시 및 분석에 가장 적합합니다. 이 크기는 중첩된 가상화도 지원합니다. <p>이 크기는 각 학생이 여러 VM이 필요한 시나리오에서 사용할 수 있습니다. 교육자는 중첩된 가상화를 사용하여 가상 머신 내에 작은 크기의 중첩된 가상 머신 몇 개를 설정할 수 있습니다. </p> |
| 소형 GPU(컴퓨팅) | 6 | 56GB | <p>이 크기는 AI와 딥 러닝 애플리케이션 같은 컴퓨팅 및 네트워크를 많이 사용하는 애플리케이션에 가장 적합합니다.</p><p>Azure Lab Services는 GPU 이미지를 사용하여 랩을 만들 때 필요한 GPU 드라이버를 자동으로 설치하고 구성합니다. </p> | 
| 소형 GPU(시각화) | 6 | 56GB | 이 크기는 OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합합니다. | 
| 큰 | 8 | 16GB | 이 크기는 더 빠른 CPU, 향상된 로컬 디스크 성능, 큰 데이터베이스, 큰 메모리 캐시가 필요한 애플리케이션에 가장 적합합니다. |
| 대형(중첩된 가상화) | 8 | 32GB | 이 크기는 더 빠른 CPU, 향상된 로컬 디스크 성능, 큰 데이터베이스, 큰 메모리 캐시가 필요한 애플리케이션에 가장 적합합니다. 이 크기는 중첩된 가상화도 지원합니다. |  
| 중간 GPU(시각화) | 12 | 112GB | 이 크기는 OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합합니다. | 

> [!NOTE]
> 클래스룸 랩을 만들 때 이러한 VM 크기 중 일부가 목록에 표시되지 않을 수 있습니다. 이 목록은 랩 위치의 현재 용량을 기준으로 채워집니다. 랩 계정 작성자가 [랩 작성자가 랩의 위치를 선택하도록 허용](allow-lab-creator-pick-lab-location.md)할 경우 랩에 다른 위치를 선택하고 VM 크기를 사용할 수 있는지 확인할 수 있습니다. 

## <a name="view-all-classroom-labs"></a>모든 클래스룸 랩 보기

1. [Azure Lab Services Portal](https://labs.azure.com)로 이동합니다.
1. **로그인** 을 선택합니다. 랩 계정에서 **랩 작성자** 역할의 멤버인 **사용자 ID** 를 선택하거나 입력하고 암호를 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 

    [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]
1. 선택한 랩 계정에 있는 모든 랩이 표시되는지 확인합니다. 랩의 타일에는 랩의 가상 머신 수와 각 사용자의 할당량(예약 된 시간 외)이 표시됩니다.

    ![모든 랩](./media/how-to-manage-classroom-labs/all-labs.png)
1. 상단의 드롭 다운 목록을 사용하여 다른 랩 계정을 선택합니다. 선택한 랩 계정에 랩이 표시됩니다. 

## <a name="delete-a-classroom-lab"></a>클래스룸 랩 삭제

1. 랩에 대한 타일에서 구석에 있는 3개의 점(...)을 선택한 다음, **삭제** 를 선택합니다. 

    ![삭제 단추](./media/how-to-manage-classroom-labs/delete-button.png)
1. **랩 삭제** 대화 상자에서 **삭제** 를 선택하여 삭제를 계속합니다. 

## <a name="switch-to-another-classroom-lab"></a>다른 클래스룸 랩으로 전환

현재 클래스룸 랩에서 다른 클래스룸 랩으로 전환하려면 맨 위에 있는 랩 계정에서 랩 드롭다운 목록을 선택합니다.

![맨 위에 있는 드롭다운 목록에서 랩을 선택합니다.](./media/how-to-manage-classroom-labs/switch-lab.png)

이 드롭다운 목록에서 **새 랩** 을 사용하여 새 랩을 만들 수도 있습니다. 

> [!NOTE]
> Az.LabServices PowerShell 모듈(미리 보기)을 사용하여 랩을 관리할 수도 있습니다. 자세한 내용은 [GitHub의 Az.LabServices 홈페이지](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)를 참조하세요.

다른 랩 계정으로 전환하려면 랩 계정 옆에 있는 드롭다운을 선택하고 다른 랩 계정을 선택합니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)


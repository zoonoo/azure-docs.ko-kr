---
title: Azure Lab Services에서 클래스룸 랩 템플릿 관리 | Microsoft Docs
description: Azure Lab Services에서 클래스룸 랩 템플릿을 만들고 관리하는 방법을 알아봅니다.
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
ms.openlocfilehash: f0fc1e143ce7d271d5faaa8dda0eb40cdfc9e006
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332789"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Azure Lab Services에서 클래스룸 템플릿 만들기 및 관리
랩의 템플릿은 모든 사용자의 가상 머신이 만들어지는 기본 가상 머신 이미지입니다. 랩 사용자에게 제공하려는 구성과 정확히 일치하도록 템플릿 가상 머신을 설정합니다. 랩 사용자에게 표시되는 템플릿의 이름 및 설명을 제공할 수 있습니다. 그런 후 랩 사용자가 템플릿 VM의 인스턴스를 사용할 수 있도록 템플릿을 게시합니다. 템플릿을 게시하면 Azure Lab Services가 템플릿을 사용하여 랩에 VM을 만듭니다. 이 과정에서 만들어지는 VM 수는 랩에 허용되는 최대 사용자 수와 같으며, 이 값은 랩의 사용 정책에서 설정할 수 있습니다. 모든 가상 머신은 템플릿과 구성이 동일합니다.

이 문서에서는 Azure Lab Services의 클래스룸 랩에서 템플릿 가상 머신을 만들고 관리하는 방법을 설명합니다. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>클래스룸 랩을 만드는 동안 템플릿 게시
교실 랩을 만드는 동안 템플릿을 게시 하는 방법을 알아보려면 클래스 룸 [랩 만들기](how-to-manage-classroom-labs.md#create-a-classroom-lab) 를 참조 하세요.
 
## <a name="set-or-update-template-title-and-description"></a>템플릿 제목 및 설명 설정 또는 업데이트
다음 단계에 따라 제목과 설명을 처음 설정한 후 나중에 업데이트합니다. 

1. **템플릿** 페이지에서 랩의 새 **제목을** 입력 합니다.  
2. 템플릿에 대 한 새 **설명을** 입력 합니다. 포커스를 텍스트 상자 밖으로 이동 하면 자동으로 저장 됩니다. 

    ![템플릿 이름 및 설명](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>템플릿 VM 업데이트
다음 단계를 사용 하 여 템플릿 VM을 업데이트 합니다.  

1. **템플릿** 페이지의 도구 모음에서 **템플릿 사용자 지정** 을 선택 합니다. 

    ![템플릿 사용자 지정 단추](../media/how-to-create-manage-template/customize-template-button.png)
2. **템플릿 사용자 지정** 대화 상자에서 **계속**을 선택 합니다. 템플릿을 시작 하 고 변경한 후에는 더 이상 사용자에 게 마지막으로 게시 한 가상 머신과 동일한 설정이 적용 되지 않습니다. 다시 게시 하기 전 까지는 사용자의 기존 가상 컴퓨터에 템플릿 변경 내용이 반영 되지 않습니다.

    ![사용자 지정 대화 상자](../media/how-to-create-manage-template/customize-template-dialog.png)
1. 도구 모음에서 **템플릿에 연결** 단추를 선택 하 여 템플릿 VM에 연결 하 고 지침을 따릅니다. Windows 컴퓨터의 경우 RDP 파일을 다운로드 하는 옵션이 표시 됩니다. 

    ![템플릿 VM에 연결](../media/how-to-create-manage-template/connect-template-vm.png)
1. 학생들이 랩을 사용하는 데 필요한 소프트웨어를 설치합니다(예: Visual Studio, Azure Storage Explorer 등). 
2. 템플릿 VM의 연결을 종료합니다(원격 데스크톱 세션 종료). 
3. **템플릿 중지**를 선택 하 여 템플릿 VM을 **중지** 합니다. 
4. 다음 섹션의 단계에 따라 업데이트 된 템플릿 VM을 **게시** 합니다. 

## <a name="publish-the-template-vm"></a>템플릿 VM 게시  
랩을 만드는 동안 템플릿을 게시하지 않으면 나중에 게시할 수 있습니다. 게시하기 전에 템플릿 VM에 연결하고 소프트웨어를 사용하여 업데이트할 수 있습니다. 템플릿을 게시하면 Azure Lab Services가 템플릿을 사용하여 랩에 VM을 만듭니다. 이 프로세스에서 만든 Vm 수는 처음으로 게시할 때 지정한 Vm 수 또는 가상 머신 풀 페이지에서 지정한 Vm 수입니다. 모든 가상 머신은 템플릿과 구성이 동일합니다. 

1. **템플릿** 페이지의 도구 모음에서 **게시** 를 선택 합니다. 
1. **템플릿 게시** 메시상지 자에서 메시지를 검토하고, **게시**를 선택합니다. 이 프로세스는 만들려는 VM 개수에 따라 다소 시간이 걸릴 수 있습니다.

    ![게시 단추](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > 템플릿이 게시되면 게시를 취소할 수 없습니다. 그렇지만 템플릿을 다시 게시할 수는 있습니다. 
4. 템플릿 페이지에서 게시 프로세스의 상태를 볼 수 있습니다. 템플릿의 상태가 **게시됨**으로 변경될 때까지 기다립니다. 

    ![게시 상태](../media/how-to-create-manage-template/publish-status.png)
1. **가상 머신** 페이지로 전환하고, **할당되지 않음** 상태인 가상 머신이 보이는지 확인합니다. 이러한 VM은 아직 학생들에게 할당되지 않았습니다. VM이 만들어질 때까지 기다립니다. **중지됨** 상태일 것입니다. 이 페이지에서 학생 VM을 시작하고, VM에 연결하고, VM을 중지하고, VM을 삭제할 수 있습니다. 이 페이지에서 VM을 시작할 수도 있고, 학생들이 VM을 시작하게 할 수도 있습니다. 

    ![중지됨 상태의 가상 머신](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)

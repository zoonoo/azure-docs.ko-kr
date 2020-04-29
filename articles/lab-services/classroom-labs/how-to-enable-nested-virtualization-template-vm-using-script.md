---
title: Azure Lab Services의 템플릿 VM에서 중첩 된 가상화 사용 (스크립트) | Microsoft Docs
description: 내에서 여러 Vm을 사용 하 여 템플릿 VM을 만드는 방법을 알아봅니다.  즉, Azure Lab Services의 템플릿 VM에서 중첩 된 가상화를 사용 하도록 설정 합니다.
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
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 56e5ad21f94521565b4df193b2450a1c994b66f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503037"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>스크립트를 사용 하 여 Azure Lab Services의 템플릿 가상 머신에서 중첩 된 가상화 사용

중첩 된 가상화를 사용 하면 랩의 템플릿 가상 머신 내에서 다중 VM 환경을 만들 수 있습니다. 템플릿을 게시 하면 랩에 있는 각 사용자에 게 가상 머신 내에서 여러 Vm을 설정 하는 가상 머신이 제공 됩니다.  중첩 된 가상화 및 Azure Lab Services에 대 한 자세한 내용은 [Azure Lab Services의 템플릿 가상 머신에서 중첩 된 가상화 사용](how-to-enable-nested-virtualization-template-vm.md)을 참조 하세요.

이 문서의 단계는 Windows Server 2016 또는 Windows Server 2019에 대해 중첩 된 가상화를 설정 하는 데 초점을 둡니다. Hyper-v를 사용 하 여 템플릿 컴퓨터를 설정 하는 스크립트를 사용 합니다.  다음 단계에서는 [Lab Services hyper-v 스크립트](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)를 사용 하는 방법을 안내 합니다.

>[!IMPORTANT]
>랩을 만들 때 가상 머신 크기에 대 한 **큰 (중첩 된 가상화)** 또는 **중간 (중첩 된 가상화)** 을 선택 합니다.  그렇지 않으면 중첩 된 가상화가 작동 하지 않습니다.  

## <a name="run-script"></a>스크립트 실행

1. Internet Explorer를 사용 하는 경우 신뢰할 수 있는 사이트 목록 `https://github.com` 에를 추가 해야 합니다.
    1. Internet Explorer를 엽니다.
    1. 기어 아이콘을 선택 하 고 **인터넷 옵션**을 선택 합니다.  
    1. **인터넷 옵션** 대화 상자가 나타나면 **보안**을 선택 하 고, **신뢰할 수 있는 사이트**를 선택 하 고, **사이트** 단추를 클릭 합니다.
    1. **신뢰할 수 있는 사이트** 대화 상자가 표시 되 `https://github.com` 면 신뢰할 수 있는 웹 사이트 목록에를 추가 하 고 **닫기**를 선택 합니다.

        ![신뢰할 수 있는 사이트](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. 다음 단계에 설명 된 대로 Git 리포지토리 파일을 다운로드 합니다.
    1. 로 [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)이동 합니다.
    1. **복제 또는 다운로드** 단추를 클릭 합니다.
    1. **ZIP 다운로드**를 클릭 합니다.
    1. ZIP 파일 추출

    >[!TIP]
    >에서 [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)Git 리포지토리를 복제할 수도 있습니다.

1. **관리자** 모드에서 **PowerShell** 을 시작 합니다.
1. PowerShell 창에서 다운로드 한 스크립트가 포함 된 폴더로 이동 합니다. 리포지토리 파일의 최상위 폴더에서 탐색 하는 경우 스크립트는에 `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`있습니다.
1. 스크립트를 성공적으로 실행 하려면 실행 정책을 변경 해야 할 수 있습니다. 다음 명령을 실행합니다.

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. 스크립트를 실행합니다.

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > 스크립트에서 컴퓨터를 다시 시작 해야 할 수 있습니다. 스크립트의 지침에 따라 스크립트를 다시 실행 합니다. 그러면 스크립트가 **완료** 될 때까지 스크립트가 출력에 표시 됩니다.
1. 실행 정책을 다시 설정 해야 합니다. 다음 명령을 실행합니다.

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>결론

이제 템플릿 컴퓨터에서 Hyper-v 가상 컴퓨터를 만들 준비가 되었습니다. Hyper-v 가상 컴퓨터를 만드는 방법에 대 한 지침은 [hyper-v에서 가상 컴퓨터 만들기](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) 를 참조 하세요. 또한 [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) 를 참조 하 여 사용 가능한 운영 체제 및 소프트웨어를 확인 하십시오.  

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정 하는 데 일반적입니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에 대 한 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
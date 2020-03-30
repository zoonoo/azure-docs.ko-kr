---
title: Azure 랩 서비스(스크립트)의 템플릿 VM에 중첩가상화 활성화 | 마이크로 소프트 문서
description: 내부에 여러 VM이 있는 템플릿 VM을 만드는 방법에 대해 알아봅니다.  즉, Azure Lab 서비스의 템플릿 VM에서 중첩된 가상화를 사용하도록 설정합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503037"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>스크립트를 사용하여 Azure Lab 서비스의 템플릿 가상 머신에 중첩가상화 사용

중첩된 가상화를 사용하면 랩의 템플릿 가상 시스템 내에서 다중 VM 환경을 만들 수 있습니다. 템플릿을 게시하면 랩의 각 사용자에게 여러 VM이 있는 가상 시스템이 설정됩니다.  중첩 된 가상화 및 Azure Lab 서비스에 대한 자세한 내용은 [Azure Lab Services의 템플릿 가상 컴퓨터에서 중첩 된 가상화 활성화를](how-to-enable-nested-virtualization-template-vm.md)참조하십시오.

이 문서의 단계는 Windows Server 2016 또는 Windows Server 2019에 중첩된 가상화를 설정하는 데 중점을 둡니다. 스크립트를 사용하여 하이퍼-V를 사용하여 템플릿 컴퓨터를 설정합니다.  다음 단계는 [랩 서비스 하이퍼-V 스크립트를](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)사용하는 방법을 안내합니다.

>[!IMPORTANT]
>랩을 만들 때 가상 컴퓨터 크기에 대해 **큰(중첩된 가상화)** 또는 **중간(중첩된 가상화)을** 선택합니다.  중첩된 가상화는 그렇지 않으면 작동하지 않습니다.  

## <a name="run-script"></a>스크립트 실행

1. Internet Explorer를 사용하는 경우 신뢰할 수 `https://github.com` 있는 사이트 목록에 추가해야 할 수 있습니다.
    1. Internet Explorer를 엽니다.
    1. 기어 아이콘을 선택하고 **인터넷 옵션을 선택합니다.**  
    1. 인터넷 **옵션** 대화 상자가 나타나면 **보안을**선택하고 **신뢰할 수 있는 사이트를**선택하며 사이트 **단추를** 클릭합니다.
    1. 신뢰할 **수 있는 사이트** 대화 `https://github.com` 상자가 나타나면 신뢰할 수 있는 웹 사이트 목록에 추가하고 **닫기를**선택합니다.

        ![신뢰할 수 있는 사이트](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. 다음 단계에 설명된 대로 Git 리포지토리 파일을 다운로드합니다.
    1. 로 [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)이동합니다.
    1. 복제 **또는 다운로드** 버튼을 클릭합니다.
    1. **ZIP 다운로드를 클릭합니다.**
    1. ZIP 파일 추출

    >[!TIP]
    >에서 [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)Git 리포지토리를 복제할 수도 있습니다.

1. **관리자** 모드에서 **PowerShell을** 시작합니다.
1. PowerShell 창에서 다운로드한 스크립트를 사용하여 폴더로 이동합니다. 리포지토리 파일의 위쪽 폴더에서 탐색하는 경우 스크립트가 에 `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`있습니다.
1. 스크립트를 성공적으로 실행하려면 실행 정책을 변경해야 할 수 있습니다. 다음 명령 실행:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. 스크립트를 실행합니다.

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > 스크립트를 다시 시작해야 할 수 있습니다. 스크립트의 지침을 따르고 **스크립트가 완료될** 때까지 스크립트를 다시 실행합니다.
1. 실행 정책을 재설정하는 것을 잊지 마십시오. 다음 명령 실행:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>결론

이제 템플릿 컴퓨터가 Hyper-V 가상 컴퓨터를 만들 준비가 되었습니다. 하이퍼-V 가상 컴퓨터를 만드는 방법에 대한 지침은 [하이퍼-V에서 가상 컴퓨터 만들기를](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) 참조하십시오. 또한 사용 가능한 운영 체제 및 소프트웨어를 확인하려면 [Microsoft 평가 센터를](https://www.microsoft.com/evalcenter/) 참조하십시오.  

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정하는 데 일반적입니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에게 이메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
---
title: Azure Lab Services의 템플릿 VM에서 중첩 된 가상화 사용 Microsoft Docs
description: Azure Lab Services의 템플릿 VM에서 중첩 된 가상화를 사용 하도록 설정 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981996"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Azure Lab Services의 템플릿 가상 머신에서 중첩 된 가상화 사용
이 문서에서는 Azure Lab Services의 템플릿 가상 머신에서 중첩 된 가상화를 설정 하는 방법을 설명 합니다. 중첩 된 가상화는 클래스의 각 학생이 여러 컴퓨터를 필요로 하는 경우 Azure Lab Services에 사용 됩니다.
 
## <a name="considerations"></a>고려 사항
중첩 된 가상화를 사용 하 여 랩을 설정 하기 전에 다음 사항을 고려해 야 합니다.

- 새 랩을 만들 때 가상 머신 크기에 대 한 **중간 (중첩 된 가상화)** 또는 **큰** 크기를 선택 합니다. 이러한 가상 머신 크기는 중첩 된 가상화를 지원 합니다. 
- 호스트와 클라이언트 가상 컴퓨터 모두에 대해 양호한 성능을 제공 하는 크기를 선택 합니다.  가상화를 사용 하는 경우 선택 하는 크기는 한 대의 컴퓨터 뿐만 아니라 동시에 실행 해야 하는 모든 클라이언트 컴퓨터에 적합 해야 합니다.
- 클라이언트 가상 컴퓨터는 azure 가상 네트워크의 DNS 서버와 같은 Azure 리소스에 액세스할 수 없습니다.
- 호스트 가상 컴퓨터를 사용 하려면 클라이언트 컴퓨터에서 인터넷 연결을 허용 하도록 설정 해야 합니다. 
- 클라이언트 가상 머신은 독립 컴퓨터로 사용이 허가 됩니다. Microsoft 운영 체제 및 제품의 라이선스에 대 한 자세한 내용은 [Microsoft 라이선스](https://www.microsoft.com/licensing/default) 를 참조 하십시오. 템플릿 컴퓨터를 설정 하기 전에 사용 중인 다른 소프트웨어에 대 한 사용권 계약을 확인 합니다.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>템플릿 VM에서 중첩 된 가상화 사용
이 섹션의 단계에서는 Windows Server 2016 또는 Windows Server 2019에 대해 중첩 된 가상화를 설정 하는 방법에 중점을 둡니다. Hyper-v를 사용 하 여 템플릿 컴퓨터를 설정 하는 스크립트를 사용 합니다. 자동화 된 솔루션은 [Lab Services Hyper-v 스크립트](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)의 스크립트를 참조 하세요. 다음 단계에서는 스크립트를 사용 하는 방법을 안내 합니다.

1. Internet Explorer를 사용 하는 경우 신뢰할 수 있는 사이트 목록에 `https://github.com`을 추가 해야 할 수 있습니다. 
    1. Internet Explorer를 엽니다.
    1. 기어 아이콘을 선택 하 고 **인터넷 옵션**을 선택 합니다.  
    1. **인터넷 옵션** 대화 상자가 나타나면 **보안**을 선택 하 고, **신뢰할 수 있는 사이트**를 선택 하 고, **사이트** 단추를 클릭 합니다.
    1. **신뢰할 수 있는 사이트** 대화 상자가 나타나면 `https://github.com`을 신뢰할 수 있는 웹 사이트 목록에 추가 하 고 **닫기**를 선택 합니다.

        ![신뢰할 수 있는 사이트](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  다음 단계에 설명 된 대로 Git 리포지토리 파일을 다운로드 합니다.  또는 [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)에서 Git 리포지토리를 복제할 수 있습니다. 
    1. [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)으로 이동합니다.
    1. ***복제 또는 다운로드** 단추를 클릭 합니다.
    1. **ZIP 다운로드**를 클릭 합니다.
    1. ZIP 파일의 압축을 풉니다.
1. **관리자** 모드에서 **PowerShell** 을 시작 합니다.
1. PowerShell 창에서 다운로드 한 스크립트가 포함 된 폴더로 이동 합니다. 리포지토리 파일의 최상위 폴더에서 탐색 하는 경우 스크립트는 `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`에 있습니다.
1. 스크립트를 성공적으로 실행 하려면 실행 정책을 변경 해야 할 수 있습니다. 다음 명령을 실행합니다.
    
    ```powershell
    Set-ExecutionPolicy bypass -force 
    ```
1. 스크립트를 실행 합니다.
    
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
다음 문서를 참조하세요.

- [Azure VM에서 중첩 된 가상화를 사용 하도록 설정 하는 방법](../../virtual-machines/windows/nested-virtualization.md) 
- [Windows Server에 Hyper-v 역할 설치](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [Lab Services Hyper-v 스크립트](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)

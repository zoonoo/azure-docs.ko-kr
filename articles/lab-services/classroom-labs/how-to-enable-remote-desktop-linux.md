---
title: Azure Lab Services에서 Linux용 원격 데스크톱 활성화 | Microsoft Docs
description: Azure Lab Services의 랩에서 Linux 가상 머신에 대해 원격 데스크톱을 활성화하는 방법에 대해 알아봅니다.
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
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a40575340fc5b1c202be6b001807085954439f03
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588159"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Azure Lab Services의 랩에서 Linux 가상 머신에 대해 원격 데스크톱 활성화
이 문서에서는 다음 작업을 수행하는 방법을 보여 줍니다.

- Linux VM에 대한 원격 데스크톱 활성화
- 교육자가 RDP(원격 데스크톱 연결)를 통해 템플릿 VM에 연결할 수 있는 방법입니다.

## <a name="enable-remote-desktop-for-linux-vm"></a>Linux VM에 대한 원격 데스크톱 활성화
랩 생성 중에 교육자는 **Linux** 이미지에 대해 **원격 데스크톱 연결**을 사용하도록 설정할 수 있습니다. 템플릿에 대해 Linux 이미지를 선택하면 **원격 데스크톱 연결 활성화** 옵션이 표시됩니다. 이 옵션을 사용하도록 설정하면 교육자는 RDP(원격 데스크톱)를 통해 템플릿 VM 및 학생 VM에 연결할 수 있습니다. 

![Linux 이미지에 대해 원격 데스크톱 연결 활성화](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

**원격 데스크톱 연결 활성화** 메시지 상자에서 **원격 데스크톱을 사용하여 계속**을 선택합니다. 

![Linux 이미지에 대해 원격 데스크톱 연결 활성화](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> **원격 데스크톱 연결**을 사용하도록 설정하면 Linux 머신에서 **RDP** 포트만 열립니다. 가상 머신 이미지에 RDP가 이미 설치되어 구성되어 있는 경우에는 추가 단계 없이 RDP를 통해 VM에 연결할 수 있습니다.
> 
> VM 이미지에 RDP가 설치 및 구성되어 있지 않은 경우에는 처음으로 SSH를 사용하여 Linux 머신에 연결하고 RDP 및 GUI 패키지를 설치하여 나중에 RDP를 사용하여 Linux 머신에 연결할 수 있도록 해야 합니다. 자세한 내용은 [Azure에서 원격 데스크톱을 설치 및 구성하여 Linux VM에 연결](../../virtual-machines/linux/use-remote-desktop.md)을 참조하세요. 그런 다음, 학생이 학생 Linux VM에 RDP할 수 있도록 이미지를 게시합니다. 

## <a name="supported-operating-systems"></a>지원되는 운영 체제
현재 원격 데스크톱 연결은 다음과 같은 운영 체제에서 지원됩니다.

- openSUSE Leap 42.3
- CentOS 기반 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>템플릿 VM에 연결 
교육자는 먼저 SSH를 사용하여 템플릿 VM에 연결하고 여기에 RDP 및 GUI 패키지를 설치해야 합니다. 그런 다음, 교육자는 RDP를 사용하여 템플릿 VM에 연결할 수 있습니다. 

1. 도구 모음에서 **템플릿 사용자 지정**이 표시되는 경우 해당 항목을 선택합니다. 그런 다음, **템플릿 사용자 지정** 대화 상자에서 **계속**을 선택합니다. 이 작업을 수행하면 템플릿 VM이 시작됩니다.  

    ![템플릿 사용자 지정](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. 템플릿 VM을 시작한 후 **템플릿 연결**을 선택한 다음, 도구 모음에서 **SSH를 통해 연결**을 선택할 수 있습니다. 

    ![랩을 만든 후 RDP를 통해 템플릿에 연결](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. 다음 **가상 머신에 연결** 대화 상자가 표시됩니다. 텍스트 상자 옆에 있는 **복사** 단추를 선택하여 클립보드에 복사합니다. SSH 연결 문자열을 저장합니다. SSH 터미널(예: [Putty](https://www.putty.org/))에서 이 연결 문자열을 사용하여 가상 머신에 연결합니다.
 
    ![SSH 연결 문자열](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. RDP 및 GUI 패키지를 설치하여 나중에 RDP를 사용하여 Linux 머신에 연결할 수 있습니다. 자세한 내용은 [Azure에서 원격 데스크톱을 설치 및 구성하여 Linux VM에 연결](../../virtual-machines/linux/use-remote-desktop.md)을 참조하세요. 그런 다음, 학생이 학생 Linux VM에 RDP할 수 있도록 이미지를 게시합니다.
5. 이러한 패키지를 설치한 후에는 도구 모음에서 **템플릿에 연결**을 사용한 다음, **RDP를 통해 연결**을 선택하여 RDP를 통해 템플릿 VM에 연결할 수 있습니다. RDP 파일을 저장하고 RDP를 통해 템플릿 VM에 연결하는 데 사용합니다. 

## <a name="next-steps"></a>다음 단계
강사가 원격 데스크톱 연결 기능을 사용하도록 설정하면 학생이 RDP/SSH를 통해 VM에 연결할 수 있습니다. 자세한 내용은 [교실 랩에서 Linux VM에 원격 데스크톱 사용](how-to-use-remote-desktop-linux-student.md)을 참조하세요. 
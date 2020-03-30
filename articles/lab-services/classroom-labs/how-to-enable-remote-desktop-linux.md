---
title: Azure 랩 서비스에서 Linux용 원격 데스크톱 사용 | 마이크로 소프트 문서
description: Azure Lab 서비스의 랩에서 Linux 가상 컴퓨터에 대한 원격 데스크톱을 사용하도록 설정하는 방법을 알아봅니다.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270863"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Azure Lab 서비스의 랩에서 Linux 가상 컴퓨터에 대한 원격 데스크톱 사용
이 문서에서는 다음 작업을 수행하는 방법을 보여 주며 다음과 같은 작업을 수행합니다.

- 리눅스 VM에 대한 원격 데스크톱 을 사용
- 교사는 원격 데스크톱 연결 (RDP)를 통해 템플릿 VM에 연결할 수있는 방법.

## <a name="enable-remote-desktop-for-linux-vm"></a>리눅스 VM에 대한 원격 데스크톱 을 사용
랩을 만드는 동안 교사는 **Linux** 이미지에 대한 **원격 데스크톱 연결을** 활성화할 수 있습니다. 템플릿에 대해 Linux 이미지를 선택하면 **원격 데스크톱 연결 사용** 옵션이 표시됩니다. 이 옵션을 사용하도록 설정하면 교사는 RDP(원격 데스크톱)를 통해 템플릿 VM 및 학생 VM에 연결할 수 있습니다. 

![Linux 이미지에 대한 원격 데스크톱 연결 사용](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

원격 **데스크톱 연결 사용** 설정 메시지 상자에서 **원격 데스크톱으로 계속을**선택합니다. 

![Linux 이미지에 대한 원격 데스크톱 연결 사용](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> 원격 **데스크톱 연결을** 사용하도록 설정하면 Linux **컴퓨터에서만 RDP** 포트가 열립니다. RDP가 이미 가상 머신 이미지(예: 우분투 데이터 과학 가상 머신 이미지)에 설치 및 구성된 경우 추가 단계를 따르지 않고 RDP를 통해 VM에 연결할 수 있습니다.
> 
> VM 이미지에 RDP가 설치되고 구성되지 않은 경우 SSH를 사용하여 리눅스 컴퓨터에 처음으로 연결하고 나중에 RDP를 사용하여 학생이 리눅스 컴퓨터에 연결할 수 있도록 RDP 및 GUI 패키지를 설치해야 합니다. 자세한 내용은 [Azure의 Linux VM에 연결하도록 원격 데스크톱 설치 및 구성을](../../virtual-machines/linux/use-remote-desktop.md)참조하세요. 그런 다음 학생이 학생 Linux VM에 RDP를 사용할 수 있도록 이미지를 게시합니다. 

## <a name="supported-operating-systems"></a>지원되는 운영 체제
현재 원격 데스크톱 연결은 다음 운영 체제에 대해 지원됩니다.

- openSUSE Leap 42.3
- 센트로스 기반 7.5
- 데비안 9 "스트레치"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>템플릿 VM에 연결 
교사는 먼저 SSH를 사용하여 템플릿 VM에 연결하고 RDP 및 GUI 패키지를 설치해야 합니다. 그런 다음 교사는 RDP를 사용하여 템플릿 VM에 연결할 수 있습니다. 

1. 도구 모음에 **템플릿 사용자 지정이** 표시되면 템플릿을 선택합니다. 그런 다음 사용자 **지정 템플릿** 대화 상자에서 **계속을** 선택합니다. 이 작업은 템플릿 VM을 시작합니다.  

    ![템플릿 사용자 지정](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. 템플릿 VM이 시작된 후 **템플릿 연결을** 선택한 다음 도구 모음에서 **SSH를 통해 연결할** 수 있습니다. 

    ![랩을 만든 후 RDP를 통해 템플릿에 연결](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. **가상 컴퓨터** 대화 상자에 다음 연결 상자가 표시됩니다. 텍스트 상자 옆에 있는 **복사** 단추를 선택하여 클립보드에 복사합니다. SSH 연결 문자열을 저장합니다. SSH 터미널(예: [Putty](https://www.putty.org/))에서 이 연결 문자열을 사용하여 가상 머신에 연결합니다.
 
    ![SSH 연결 문자열](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. 나중에 RDP를 사용하여 리눅스 컴퓨터에 연결할 수 있도록 RDP 및 GUI 패키지를 설치합니다. 자세한 내용은 [Azure의 Linux VM에 연결하도록 원격 데스크톱 설치 및 구성을](../../virtual-machines/linux/use-remote-desktop.md)참조하세요. 그런 다음 학생이 학생 Linux VM에 RDP를 사용할 수 있도록 이미지를 게시합니다.
5. 이러한 패키지를 설치한 후 도구 모음의 **템플릿에 연결 하여** **RDP를 통해 연결을** 선택하여 RDP를 통해 템플릿 VM에 연결할 수 있습니다. RDP 파일을 저장하고 RDP를 통해 템플릿 VM에 연결하는 데 사용합니다. 

## <a name="next-steps"></a>다음 단계
교수자가 원격 데스크톱 연결 기능을 사용하도록 설정한 후 학생은 RDP/SSH를 통해 VM에 연결할 수 있습니다. 자세한 내용은 [강의실 랩에서 Linux VM용 원격 데스크톱 사용을](how-to-use-remote-desktop-linux-student.md)참조하십시오. 
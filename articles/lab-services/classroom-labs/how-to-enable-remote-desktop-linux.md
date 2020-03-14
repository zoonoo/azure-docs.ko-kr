---
title: Azure Lab Services에서 Linux 용 원격 데스크톱 사용 Microsoft Docs
description: Azure Lab Services에서 랩에서 Linux 가상 머신에 대 한 원격 데스크톱을 사용 하도록 설정 하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270863"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Azure Lab Services에서 랩에서 Linux 가상 머신에 대 한 원격 데스크톱을 사용 하도록 설정
이 문서에서는 다음 작업을 수행 하는 방법을 보여 줍니다.

- Linux VM에 대 한 원격 데스크톱 사용
- 교사가 원격 데스크톱 연결 (RDP)를 통해 템플릿 VM에 연결할 수 있는 방법입니다.

## <a name="enable-remote-desktop-for-linux-vm"></a>Linux VM에 대 한 원격 데스크톱 사용
랩을 만드는 동안 교사는 **Linux** 이미지에 대 한 **원격 데스크톱 연결** 을 사용 하도록 설정할 수 있습니다. **사용 원격 데스크톱 연결** 옵션은 템플릿에 대해 Linux 이미지를 선택할 때 표시 됩니다. 이 옵션을 사용 하도록 설정 하면 교사는 RDP (원격 데스크톱)를 통해 템플릿 VM 및 학생 Vm에 연결할 수 있습니다. 

![Linux 이미지에 대 한 원격 데스크톱 연결 사용](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

원격 데스크톱 연결 메시지 **사용** 상자에서 **원격 데스크톱을 사용 하 여 계속**을 선택 합니다. 

![Linux 이미지에 대 한 원격 데스크톱 연결 사용](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> **원격 데스크톱 연결** 을 사용 하도록 설정 하면 Linux 컴퓨터 에서만 **RDP** 포트가 열립니다. 가상 컴퓨터 이미지에 RDP가 이미 설치 되어 구성 되어 있는 경우 (예: Ubuntu Data Science Virtual Machine 이미지) 사용자/학생이 추가 단계를 수행 하지 않고도 RDP를 통해 Vm에 연결할 수 있습니다.
> 
> VM 이미지가 RDP를 설치 하 고 구성 하지 않은 경우에는 처음으로 SSH를 사용 하 여 Linux 컴퓨터에 연결 하 고 RDP 및 GUI 패키지를 설치 하 여 나중에 RDP를 사용 하 여 Linux 컴퓨터에 연결할 수 있도록 해야 합니다. 자세한 내용은 [Azure에서 LINUX VM에 연결 하기 위해 원격 데스크톱 설치 및 구성](../../virtual-machines/linux/use-remote-desktop.md)을 참조 하세요. 그런 다음 학생이 학생 Linux Vm에 RDP 할 수 있도록 이미지를 게시 합니다. 

## <a name="supported-operating-systems"></a>지원되는 운영 체제
현재 원격 데스크톱 연결은 다음과 같은 운영 체제에서 지원 됩니다.

- openSUSE Leap 42.3
- CentOS 기반 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>템플릿 VM에 연결 
교사는 먼저 SSH를 사용 하 여 템플릿 VM에 연결 하 고 여기에 RDP 및 GUI 패키지를 설치 해야 합니다. 그런 다음 교사는 RDP를 사용 하 여 템플릿 VM에 연결할 수 있습니다. 

1. 도구 모음에 **템플릿 사용자 지정** 이 표시 되 면 선택 합니다. 그런 다음 **템플릿 사용자 지정** 대화 상자에서 **계속** 을 선택 합니다. 이 작업을 수행 하면 템플릿 VM이 시작 됩니다.  

    ![템플릿 사용자 지정](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. 템플릿 VM이 시작 된 후에는 도구 모음에서 **템플릿 연결** 을 선택 하 고 **SSH를 통해 연결할** 수 있습니다. 

    ![랩을 만든 후 RDP를 통해 템플릿에 연결](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. **가상 컴퓨터에 다음 연결** 대화 상자가 표시 됩니다. 텍스트 상자 옆의 **복사** 단추를 선택 하 여 클립보드에 복사 합니다. SSH 연결 문자열을 저장 합니다. SSH 터미널(예: [Putty](https://www.putty.org/))에서 이 연결 문자열을 사용하여 가상 머신에 연결합니다.
 
    ![SSH 연결 문자열](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Rdp 및 GUI 패키지를 설치 하 여 나중에 RDP를 사용 하 여 Linux 컴퓨터에 연결할 수 있습니다. 자세한 내용은 [Azure에서 LINUX VM에 연결 하기 위해 원격 데스크톱 설치 및 구성](../../virtual-machines/linux/use-remote-desktop.md)을 참조 하세요. 그런 다음 학생이 학생 Linux Vm에 RDP 할 수 있도록 이미지를 게시 합니다.
5. 이러한 패키지를 설치한 후에는 도구 모음에서 **템플릿에 연결** 을 사용 하 여 rdp를 통해 **연결** 을 선택 하 여 RDP를 통해 템플릿 VM에 연결할 수 있습니다. Rdp 파일을 저장 하 고 RDP를 통해 템플릿 VM에 연결 하는 데 사용 합니다. 

## <a name="next-steps"></a>다음 단계
강사가 원격 데스크톱 연결 기능을 사용 하도록 설정 하면 학생이 RDP/SSH를 통해 Vm에 연결할 수 있습니다. 자세한 내용은 [교실 랩에서 Linux vm에 원격 데스크톱 사용](how-to-use-remote-desktop-linux-student.md)을 참조 하세요. 
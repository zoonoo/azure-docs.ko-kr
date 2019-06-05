---
title: Azure Lab Services에서 Linux에 대 한 원격 데스크톱 사용 | Microsoft Docs
description: Azure Lab Services의 랩에 Linux 가상 머신에 대 한 원격 데스크톱을 사용 하는 방법에 알아봅니다.
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
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 389d467bd9672743d4a086e8a1c505fb0366dba7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237098"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>설정 하 고 Azure Lab Services의 랩에 Linux 가상 머신에 대 한 원격 데스크톱 사용
이 문서에서는 다음 태스크를 수행 하는 방법을 보여 줍니다.

- Linux VM에 대 한 원격 데스크톱을 사용 하도록 설정
- 어떻게 교사를 템플릿 VM 원격 데스크톱 연결 (RDP)을 통해 연결할 수 있습니다.
- 학생 학생 RDP 통해 VM에 연결 하는 방법

## <a name="enable-remote-desktop-for-linux-vm"></a>Linux VM에 대 한 원격 데스크톱을 사용 하도록 설정
랩 만들기 중 교사 설정할 수 있습니다 **원격 데스크톱 연결** 에 대 한 **Linux** 이미지입니다. 합니다 **원격 데스크톱 연결 사용** 템플릿에 대 한 Linux 이미지를 선택 하면 옵션이 표시 됩니다. 이 옵션을 사용 하는 경우 VM 및 RDP (원격 데스크톱)를 통해 학생 Vm 템플릿에 교사에 게 연결할 수 있습니다. 

![Linux 이미지에 대 한 원격 데스크톱 연결 사용](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

에 **원격 데스크톱 연결 사용** 메시지 상자에서 **원격 데스크톱을 사용 하 여 계속**합니다. 

![Linux 이미지에 대 한 원격 데스크톱 연결 사용](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> 사용 하도록 설정 **원격 데스크톱 연결** 만 열립니다 합니다 **RDP** Linux 컴퓨터에서 포트입니다. 강사로 서 하는 처음으로 SSH를 사용 하 여 Linux 컴퓨터에 연결 하 고 나중에 RDP를 사용 하 여 Linux 컴퓨터에 연결할 수 있도록 RDP 및 GUI 패키지를 설치 합니다. 그런 다음, **게시** 이미지 학생 학생 Linux Vm의 RDP 수 있도록 합니다. 

## <a name="supported-operating-systems"></a>지원되는 운영 체제
현재 원격 데스크톱 연결을 다음 운영 체제에 사용할 수 있습니다.

- openSUSE Leap 42.3
- CentOS 기반 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>템플릿 VM에 연결 하는 교사 RDP를 사용 하 여
교사 템플릿 VM에 연결 해야 먼저 SSH를 사용 하 고 RDP 및 GUI 패키지를 설치 합니다. 그런 다음 교사는 RDP를 사용 하 여 Linux Vm에 연결 하려면 다음 단계를 따르면 됩니다. 

표시 된 **원격 데스크톱** 랩을 만든 시 템플릿 VM에 연결 하는 옵션입니다. 

![생성 시 템플릿에 RDP 통해 연결](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

표시 된 **원격 데스크톱** 옵션 랩을 만든 후 랩의 홈 페이지 및 템플릿 VM 시작 합니다. 아직 시작 되지 않은 경우 템플릿 VM을 시작 합니다. 

![랩을 만든 후 RDP 통해 템플릿에 연결](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

RDP 또는 SSH를 사용 하 여 VM에 연결 하는 방법에 대 한 자세한 내용은 [RDP]((#connect-using-ssh-or-rdp) 또는 SSH를 사용 하 여 연결을 참조 하세요. 합니다. 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>학생 VM에 연결 하는 교사 RDP를 사용 하 여
교사/교수 학생 VM에 연결할 수로 전환 하 여는 **Virtual Machines** 보기 및 선택 하는 **연결** 아이콘. 그 전에 교사 해야 **게시** RDP 및 GUI 패키지 설치를 사용 하 여 템플릿 이미지입니다. 

![교사 학생 VM에 연결](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

RDP 또는 SSH를 사용 하 여 VM에 연결 하는 방법에 대 한 자세한 내용은 [RDP]((#connect-using-ssh-or-rdp) 또는 SSH를 사용 하 여 연결을 참조 하세요. 합니다. 

## <a name="students-connecting-to-the-student-vm"></a>학생 학생 VM에 연결
학생 랩 소유자 (교사/교수) 후에 Linux Vm에 RDP 수 **게시** 템플릿을 RDP 및 GUI 패키지를 사용 하 여 VM 컴퓨터에 설치 합니다. 단계는 다음과 같습니다. 

1. 학생 로그인 경우 Labs 포털로 직접 (`https://labs.azure.com`) 또는 등록 링크를 사용 하 여 (`https://labs.azure.com/register/<registrationCode>`), 타일 학생이 각 랩에 대 한 액세스에 대 한 표시 됩니다. 
2. 타일을 선택 **시작** VM이 중지 하는 경우. 
3. **연결**을 선택합니다. VM에 연결할 두 옵션이 표시 됩니다. **SSH** 하 고 **원격 데스크톱**합니다.

    ![학생 VM-연결 옵션](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>RDP 또는 SSH를 사용 하 여 연결
선택 하는 경우는 **SSH** 옵션을 다음과 같이 표시 **가상 머신에 연결할** 대화 상자:  

![SSH 연결 문자열](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

선택 된 **복사** 클립보드로 복사 하려면 텍스트 상자 옆에 있는 단추입니다. SSH 연결 문자열을 저장 합니다. SSH 터미널(예: [Putty](https://www.putty.org/))에서 이 연결 문자열을 사용하여 가상 머신에 연결합니다.

선택 하는 경우는 **RDP** 옵션, RDP 파일을 컴퓨터에 다운로드 됩니다. 저장 하 고 컴퓨터에 연결을 엽니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)


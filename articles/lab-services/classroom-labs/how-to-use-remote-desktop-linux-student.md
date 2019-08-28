---
title: Azure Lab Services에서 Linux 용 원격 데스크톱 사용 | Microsoft Docs
description: Azure Lab Services에서 랩에서 Linux 가상 머신에 대 한 원격 데스크톱을 사용 하는 방법에 대해 알아봅니다.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 33b4ed3974c3e4e88e5d74ff31a3b8008ab565e9
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657321"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Azure Lab Services의 클래스 룸 랩에서 Linux 가상 머신에 대 한 원격 데스크톱 사용
이 문서에서는 학생이 RDP/SSH를 사용 하 여 랩에서 Linux VM (가상 머신)에 연결할 수 있는 방법을 보여 줍니다. 

강사는 학생이 교실 랩의 VM에 연결 하기 전에 원격 데스크톱 연결 기능을 사용 하도록 설정 해야 합니다. 강사가 원격 데스크톱 연결 기능을 사용 하도록 설정 하는 방법에 대 한 지침은 [Linux 가상 머신에 대 한 원격 데스크톱 사용](how-to-enable-remote-desktop-linux.md)을 참조 하세요.

> [!IMPORTANT] 
> **원격 데스크톱 연결** 을 사용 하도록 설정 하면 Linux 컴퓨터 에서만 **RDP** 포트가 열립니다. 강사는 처음으로 SSH를 사용 하 여 Linux 컴퓨터에 연결 하 고 rdp 및 GUI 패키지를 설치 하 여 나중에 RDP를 사용 하 여 Linux 컴퓨터에 연결할 수 있습니다. 

## <a name="connect-to-the-student-vm"></a>학생 VM에 연결
학생은 랩 소유자 (교사/교수)가 컴퓨터에 설치 된 RDP 및 GUI 패키지를 사용 하 여 템플릿 VM을 **게시** 한 후 해당 Linux VM에 rdp 할 수 있습니다. 단계는 다음과 같습니다. 

1. 학생이 실습 포털에 직접 로그인 (`https://labs.azure.com`) 하거나 등록 링크 (`https://labs.azure.com/register/<registrationCode>`)를 사용 하 여 학생 들이 액세스할 수 있는 각 랩에 대 한 타일이 표시 됩니다. 
2. 타일에서 VM이 중지 되 면 **시작** 을 선택 합니다. 
3. **연결**을 선택합니다. VM에 연결 하는 두 가지 옵션이 표시 됩니다. **SSH** 및 **원격 데스크톱**.

    ![학생 VM-연결 옵션](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>SSH 또는 RDP를 사용 하 여 연결
**SSH** 옵션을 선택 하면 다음과 같이 **가상 컴퓨터에 연결** 대화 상자가 표시 됩니다.  

![SSH 연결 문자열](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

텍스트 상자 옆의 **복사** 단추를 선택 하 여 클립보드에 복사 합니다. SSH 연결 문자열을 저장 합니다. SSH 터미널(예: [Putty](https://www.putty.org/))에서 이 연결 문자열을 사용하여 가상 머신에 연결합니다.

**Rdp** 옵션을 선택 하면 rdp 파일이 컴퓨터에 다운로드 됩니다. 이를 저장 하 고 열어 컴퓨터에 연결 합니다. 

## <a name="next-steps"></a>다음 단계
교실 랩에서 Linux Vm에 대 한 원격 데스크톱 연결 기능을 사용 하도록 설정 하는 방법을 알아보려면 [linux 가상 머신에 대 한 원격 데스크톱 사용](how-to-enable-remote-desktop-linux.md)을 참조 하세요. 


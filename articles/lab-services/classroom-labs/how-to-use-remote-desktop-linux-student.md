---
title: Azure 랩 서비스에서 리눅스에 대 한 원격 데스크톱 사용 | 마이크로 소프트 문서
description: Azure Lab 서비스의 랩에서 Linux 가상 컴퓨터에 원격 데스크톱을 사용하는 방법을 알아봅니다.
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
ms.openlocfilehash: 0f45af2730b05998fc82212c63778c89bb16b6ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73585098"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Azure Lab 서비스의 강의실 랩에서 Linux 가상 컴퓨터에 원격 데스크톱 사용
이 문서에서는 학생들이 RDP/SSH를 사용하여 랩의 VM(Linux 가상 머신)에 연결할 수 있는 방법을 보여 주며 있습니다. 

교수자는 학생이 교실 랩의 VM에 연결하기 전에 원격 데스크톱 연결 기능을 사용하도록 설정해야 합니다. 강사가 원격 데스크톱 연결 기능을 사용하도록 설정하는 방법에 대한 지침은 [Linux 가상 컴퓨터용 원격 데스크톱 활성화를](how-to-enable-remote-desktop-linux.md)참조하십시오.

> [!IMPORTANT] 
> 원격 **데스크톱 연결을** 사용하도록 설정하면 Linux **컴퓨터에서만 RDP** 포트가 열립니다. 강사는 SSH를 사용하여 처음으로 리눅스 컴퓨터에 연결하고 나중에 RDP를 사용하여 리눅스 컴퓨터에 연결할 수 있도록 RDP 및 GUI 패키지를 설치할 수 있습니다. 

## <a name="connect-to-the-student-vm"></a>학생 VM에 연결
학생들은 랩 소유자(교사/교수)가 컴퓨터에 설치된 RDP 및 GUI 패키지가 있는 템플릿 VM을 **게시한** 후 리눅스 VM에 RDP를 사용할 수 있습니다. 다음 단계를 참조하십시오. 

1. 학생이 Labs 포털에`https://labs.azure.com`직접() 또는 등록 링크()를`https://labs.azure.com/register/<registrationCode>`사용하여 에 서명하면 학생이 액세스할 수 있는 각 랩의 타일이 표시됩니다. 
2. 타일에서 중지된 상태인 경우 단추를 전환하여 VM을 시작합니다. 
3. **연결**을 선택합니다. VM에 연결하는 두 가지 옵션이 있습니다: **SSH** 및 **원격 데스크톱**.

    ![학생 VM - 연결 옵션](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>SSH 또는 RDP를 사용하여 연결
**SSH** 옵션을 선택하면 **가상 컴퓨터** 대화 상자에 다음 연결 상자가 표시됩니다.  

![SSH 연결 문자열](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

텍스트 상자 옆에 있는 **복사** 단추를 선택하여 클립보드에 복사합니다. SSH 연결 문자열을 저장합니다. SSH 터미널(예: [Putty](https://www.putty.org/))에서 이 연결 문자열을 사용하여 가상 머신에 연결합니다.

**RDP** 옵션을 선택하면 RDP 파일이 컴퓨터에 다운로드됩니다. 저장하고 열어 기기에 연결합니다. 

## <a name="next-steps"></a>다음 단계
강의실 랩에서 Linux VM용 원격 데스크톱 연결 기능을 사용하도록 설정하는 방법을 알아보려면 [Linux 가상 컴퓨터용 원격 데스크톱 활성화를](how-to-enable-remote-desktop-linux.md)참조하십시오. 


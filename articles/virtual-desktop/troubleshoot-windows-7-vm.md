---
title: 윈도우 7 가상 머신 윈도우 가상 데스크톱 - Azure
description: Windows 가상 데스크톱 환경에서 Windows 7 가상 컴퓨터(VM)에 대한 문제를 해결하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a2ff3f6fa9896e45ecd6ab40d40d46a046edf1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127379"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Windows Virtual Desktop에서 Windows 7 가상 머신 문제 해결

이 문서를 사용하여 Windows 가상 데스크톱 세션 호스트 가상 시스템(VM)을 구성할 때 겪고 있는 문제를 해결합니다.

## <a name="known-issues"></a>알려진 문제

윈도우 7 윈도우 가상 데스크톱에서 다음과 같은 기능을 지원 하지 않습니다.

- 가상화된 애플리케이션(원격 앱)
- 표준 시간대 리디렉션
- 자동 DPI 스케일링

윈도우 가상 데스크톱은 윈도우 7에 대한 전체 데스크톱을 가상화 할 수 있습니다.

자동 DPI 크기 조정은 지원되지 않지만 원격 데스크톱 클라이언트의 아이콘을 마우스 오른쪽 단추로 클릭하고 **해결**을 선택하여 가상 컴퓨터에서 해상도를 수동으로 변경할 수 있습니다.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>오류: 원격 데스크톱 사용자 그룹에 액세스할 수 없습니다.

Windows 가상 데스크톱이 원격 데스크톱 사용자 그룹에서 사용자 또는 사용자의 자격 증명을 찾을 수 없는 경우 다음 오류 메시지 중 하나가 표시될 수 있습니다.

- "이 사용자는 원격 데스크톱 사용자 그룹의 구성원이 아닙니다."
- "원격 데스크톱 서비스를 통해 로그인할 수 있는 권한이 부여되어야 합니다."

이 오류를 해결하려면 사용자를 원격 데스크톱 사용자 그룹에 추가합니다.

1. Azure Portal을 엽니다.
2. 오류 메시지가 있는 가상 컴퓨터를 선택합니다.
3. **명령 실행을**선택합니다.
4. 추가하려는 사용자의 `<username>` 이름으로 대체된 다음 명령을 실행합니다.
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```
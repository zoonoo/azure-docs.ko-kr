---
title: Windows 7 virtual machines Windows 가상 데스크톱 (클래식)-Azure
description: Windows 가상 데스크톱 (클래식) 환경에서 Windows 7 Vm (가상 컴퓨터)에 대 한 문제를 해결 하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1d71b4472b16372927db56b20eca4fcbde0cc625
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005417"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop-classic"></a>Windows 가상 데스크톱의 Windows 7 가상 컴퓨터 문제 해결 (클래식)

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 Windows Virtual Desktop(클래식)에 적용됩니다.

이 문서를 사용 하 여 Windows 가상 데스크톱 세션 호스트 Vm (가상 컴퓨터)을 구성할 때 발생 하는 문제를 해결할 수 있습니다.

## <a name="known-issues"></a>알려진 문제

Windows 7 Windows 가상 데스크톱은 다음과 같은 기능을 지원 하지 않습니다.

- 가상화 된 응용 프로그램 (Remoteapp)
- 표준 시간대 리디렉션
- 자동 DPI 배율

Windows 가상 데스크톱은 Windows 7 용 전체 데스크톱만 가상화 할 수 있습니다.

자동 DPI 크기 조정이 지원 되지 않지만, 원격 데스크톱 클라이언트에서 아이콘을 마우스 오른쪽 단추로 클릭 하 고 **해결**을 선택 하 여 가상 머신에서 해상도를 수동으로 변경할 수 있습니다.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>오류: 원격 데스크톱 사용자 그룹에 액세스할 수 없습니다.

Windows 가상 데스크톱이 원격 데스크톱 사용자 그룹에서 사용자의 자격 증명을 찾을 수 없거나 사용자의 자격 증명을 찾을 수 없는 경우 다음 오류 메시지 중 하나가 표시 될 수 있습니다.

- "이 사용자는 원격 데스크톱 사용자 그룹의 구성원이 아닙니다."
- "원격 데스크톱 서비스를 통해 로그인 할 수 있는 권한을 부여 해야 합니다."

이 오류를 해결 하려면 원격 데스크톱 사용자 그룹에 사용자를 추가 합니다.

1. Azure Portal을 엽니다.
2. 오류 메시지가 표시 된 가상 컴퓨터를 선택 합니다.
3. **명령 실행을**선택 합니다.
4. 다음 명령을 `<username>` 추가 하려는 사용자의 이름으로 대체 하 여 실행 합니다.

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```
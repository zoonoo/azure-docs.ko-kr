---
title: Windows 7 가상 머신 Windows Virtual Desktop(클래식) - Azure
description: Windows Virtual Desktop(클래식) 환경에서 Windows 7 VM(가상 머신)의 문제를 해결하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 99c1c76092714c58fd13d9ce51ef850971d80c86
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444125"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop-classic"></a>Windows Virtual Desktop(클래식)에서 Windows 7 가상 머신 문제 해결

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 Windows Virtual Desktop(클래식)에 적용됩니다.

이 문서를 사용하여 Windows Virtual Desktop 세션 호스트 VM(가상 머신)을 구성할 때 발생하는 문제를 해결할 수 있습니다.

## <a name="known-issues"></a>알려진 문제

Windows Virtual Desktop의 Windows 7에서는 다음 기능을 지원하지 않습니다.

- 가상화된 애플리케이션(RemoteApps)
- 표준 시간대 리디렉션
- 자동 DPI 조정

Windows Virtual Desktop은 Windows 7용 전체 데스크톱만 가상화할 수 있습니다.

자동 DPI 조정은 지원되지 않지만 원격 데스크톱 클라이언트에서 아이콘을 마우스 오른쪽 단추로 클릭하고 **해상도** 를 선택하여 가상 머신의 해상도를 수동으로 변경할 수 있습니다.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>오류: 원격 데스크톱 사용자 그룹에 액세스할 수 없습니다.

Windows Virtual Desktop이 원격 데스크톱 사용자 그룹에서 사용자 또는 사용자의 자격 증명을 찾을 수 없는 경우 다음 오류 메시지 중 하나가 표시될 수 있습니다.

- "이 사용자는 원격 데스크톱 사용자 그룹의 구성원이 아닙니다."
- "원격 데스크톱 서비스를 통해 로그인할 수 있는 권한을 부여해야 합니다."

이 오류를 해결하려면 사용자를 원격 데스크톱 사용자 그룹에 추가합니다.

1. Azure Portal을 엽니다.
2. 오류 메시지가 표시된 가상 머신을 선택합니다.
3. **명령 실행** 을 선택합니다.
4. `<username>`을 추가할 사용자 이름으로 대체하여 다음 명령을 실행합니다.

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```
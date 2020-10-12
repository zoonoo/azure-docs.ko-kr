---
title: Windows 가상 데스크톱 호스트 풀 삭제-Azure
description: Windows 가상 데스크톱에서 호스트 풀을 삭제 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dfc9858bea468389d8ce90677f048e5d1fd3bb82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88007593"
---
# <a name="delete-a-host-pool"></a>호스트 풀 삭제

Windows 가상 데스크톱에서 만든 모든 호스트 풀이 세션 호스트 및 앱 그룹에 연결 됩니다. 호스트 풀을 삭제 하려면 연결 된 앱 그룹 및 세션 호스트를 삭제 해야 합니다. 앱 그룹을 삭제 하는 것은 매우 간단 하지만 세션 호스트를 삭제 하는 것은 더 복잡 합니다. 세션 호스트를 삭제 하는 경우 활성 사용자 세션이 없는지 확인 해야 합니다. 사용자가 데이터를 잃지 못하도록 세션 호스트의 모든 사용자 세션을 로그 오프 해야 합니다.

## <a name="delete-a-host-pool-with-powershell"></a>PowerShell을 사용 하 여 호스트 풀 삭제

PowerShell을 사용 하 여 호스트 풀을 삭제 하려면 먼저 호스트 풀의 모든 앱 그룹을 삭제 해야 합니다. 모든 앱 그룹을 삭제 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

그런 다음이 cmdlet을 실행 하 여 호스트 풀을 삭제 합니다.

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

이 cmdlet은 호스트 풀의 세션 호스트에서 기존 사용자 세션을 모두 제거 합니다. 또한 호스트 풀에서 세션 호스트의 등록을 취소 합니다. 모든 관련 Vm (가상 머신)은 여전히 구독 내에 존재 합니다.

## <a name="delete-a-host-pool-with-the-azure-portal"></a>Azure Portal를 사용 하 여 호스트 풀 삭제

Azure Portal에서 호스트 풀을 삭제 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **Windows Virtual Desktop**을 검색하여 선택합니다.

3. 페이지의 왼쪽에 있는 메뉴에서 **호스트 풀** 을 선택 하 고 삭제 하려는 호스트 풀의 이름을 선택 합니다.

4. 페이지의 왼쪽에 있는 메뉴에서 **응용 프로그램 그룹**을 선택 합니다.

5. 삭제할 호스트 풀의 모든 응용 프로그램 그룹을 선택 하 고 **제거**를 선택 합니다.

6. 앱 그룹을 제거 했으면 페이지의 왼쪽에 있는 메뉴로 이동 하 고 **개요**를 선택 합니다.

7. **제거**를 선택합니다.

8. 삭제 중인 호스트 풀에 세션 호스트가 있는 경우 계속 하려면 사용 권한을 요청 하는 메시지가 표시 됩니다. **예**를 선택합니다.

9. 그러면 Azure Portal에서 모든 세션 호스트를 제거 하 고 호스트 풀을 삭제 합니다. 세션 호스트와 관련 된 Vm은 삭제 되지 않으며 구독에서 유지 됩니다.

## <a name="next-steps"></a>다음 단계

호스트 풀을 만드는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure Portal로 호스트 풀 만들기](create-host-pools-azure-marketplace.md)
- [PowerShell을 사용한 호스트 풀 만들기](create-host-pools-powershell.md)

호스트 풀 설정을 구성 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [호스트 풀의 원격 데스크톱 프로토콜 속성 사용자 지정](customize-rdp-properties.md)
- [Windows Virtual Desktop 부하 분산 방법 구성](configure-host-pool-load-balancing.md)
- [개인 데스크톱 호스트 풀 할당 유형 구성](configure-host-pool-personal-desktop-assignment-type.md)
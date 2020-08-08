---
title: Windows 가상 데스크톱 부하 분산 구성-Azure
description: Windows 가상 데스크톱 환경에 대 한 부하 분산 방법을 구성 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 07eae73a36bf4051925547fa375f46963a162881
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010109"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Windows Virtual Desktop 부하 분산 방법 구성

호스트 풀에 대해 부하 분산 방법을 구성 하면 사용자의 요구에 맞게 Windows 가상 데스크톱 환경을 조정할 수 있습니다.

>[!NOTE]
> 사용자에 게는 항상 호스트 풀 내의 세션 호스트에 대 한 1:1 매핑이 있으므로 영구 데스크톱 호스트 풀에는이 내용이 적용 되지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 PowerShell 모듈을 다운로드 및 설치 하 고 Azure 계정에 로그인 하기 위해 [Windows 가상 데스크톱 powershell 모듈 설정](powershell-module.md) 의 지침을 따르고 있다고 가정 합니다.

## <a name="configure-breadth-first-load-balancing"></a>너비 우선 부하 분산 구성

너비 우선 부하 분산은 새로운 비영구 호스트 풀의 기본 구성입니다. 너비 우선 부하 분산은 호스트 풀에서 사용 가능한 모든 세션 호스트에 새 사용자 세션을 배포 합니다. 너비 우선 부하 분산을 구성할 때 호스트 풀의 세션당 최대 세션 제한 수를 설정할 수 있습니다.

최대 세션 제한을 조정 하지 않고 너비 우선 부하 분산을 수행 하도록 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

그런 다음, 너비 우선 부하 분산 방법을 설정 했는지 확인 하려면 다음 cmdlet을 실행 합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

너비 우선 부하 분산을 수행 하 고 새 최대 세션 제한을 사용 하도록 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>깊이 우선 부하 분산 구성

깊이 우선 부하 분산은 연결 수가 가장 많은 사용 가능한 세션 호스트에 새 사용자 세션을 배포 하지만 최대 세션 제한 임계값에 도달 하지 않았습니다. 깊이 우선 부하 분산을 구성 하는 경우 호스트 풀에서 세션당 최대 세션 제한을 설정 해야 합니다.

깊이 우선 부하 분산을 수행 하도록 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

설정이 업데이트 되었는지 확인 하려면 다음 cmdlet을 실행 합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Azure Portal를 사용 하 여 부하 분산 구성

또한 Azure Portal를 사용 하 여 부하 분산을 구성할 수 있습니다.

부하 분산을 구성 하려면:

1. https://portal.azure.com에서 Azure Portal에 로그인합니다.
2. 서비스에서 **Windows 가상 데스크톱** 을 검색 하 고 선택 합니다.
3. Windows 가상 데스크톱 페이지에서 **호스트 풀**을 선택 합니다.
4. 편집 하려는 호스트 풀의 이름을 선택 합니다.
5. **속성**을 선택합니다.
6. 필드에 **최대 세션 제한을** 입력 하 고 드롭다운 메뉴에서이 호스트 풀에 대해 원하는 **부하 분산 알고리즘** 을 선택 합니다.
7. **저장**을 선택합니다. 그러면 새 부하 분산 설정이 적용 됩니다.